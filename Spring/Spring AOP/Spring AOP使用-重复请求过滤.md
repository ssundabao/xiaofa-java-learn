## 背景 ##
无论是面试还是实际工作中，总会考虑一个问题，APP也好，web端也好，同一个表单提交，手快了，重复点了两次怎么办（有兴趣的同学可以试一下，APP上快速的点两下提交），怎么过滤掉重复的请求。当然有人说，前端点击一次后置灰，实际开发中，不能依靠前端，服务器端才是和数据存储相关联的。下面我用aop的思想来过滤重复请求。

## 用人话解释aop ##
aop有很多概念（切点、切面、增强等），其实我个人不喜欢背概念，就用我自己的话说吧。其实我理解的aop也是一种封装，就是干很多事情的时候都有一件公用的事情要干。比如，你每次谈女朋友的时候都要买礼物，那买礼物就变成了一件公用的事情，我们把它封装起来，和每个女朋友相处的时候，自动送礼物就好了。

## 定义一个注解 ##
如果对自定义注解不了解的自行百度。

    /**
	 * 阻止重复提交
	 */
	@Documented
	@Target({ElementType.METHOD, ElementType.TYPE})
	@Retention(RetentionPolicy.RUNTIME)
	public @interface RepeatSubmitDeny {
	
	}

## 定义切面 ##
这是一个处理方法，相当于给女朋友送礼物。贴代码，这里使用了基于redis的分布式锁来过滤重复请求。关于redis分布式锁，请看将来的缓存专题。

    package com.xiaofa.common.aop;

	import com.xiaofa.common.ServerResult;
	import org.aspectj.lang.ProceedingJoinPoint;
	import org.aspectj.lang.annotation.Around;
	import org.aspectj.lang.annotation.Aspect;
	import org.aspectj.lang.annotation.Pointcut;
	import org.slf4j.Logger;
	import org.slf4j.LoggerFactory;
	import org.springframework.data.redis.core.StringRedisTemplate;
	import org.springframework.stereotype.Component;
	import org.springframework.util.Assert;
	import org.springframework.web.context.request.RequestContextHolder;
	import org.springframework.web.context.request.ServletRequestAttributes;
	
	import javax.annotation.Resource;
	import javax.servlet.http.HttpServletRequest;
	import java.util.UUID;
	import java.util.concurrent.TimeUnit;
	
	/**
	 * @desc:
	 * @Author: zhaoxiaofa
	 * @Date: 2019/6/11 21:36
	 */
	@Aspect
	@Component
	public class RepeatSubmitAspect {

	    private static final Logger logger = LoggerFactory.getLogger(RepeatSubmitAspect.class);
	
	    @Resource
	    private StringRedisTemplate stringRedisTemplate;
	
	    @Pointcut("@annotation(repeatSubmitDeny)")
	    public void pointCut(RepeatSubmitDeny repeatSubmitDeny) {
	    }
	
	    @Around("pointCut(repeatSubmitDeny)")
	    public Object around(ProceedingJoinPoint pjp, RepeatSubmitDeny repeatSubmitDeny) throws Throwable {
	        ServletRequestAttributes servletRequestAttributes = (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
	        HttpServletRequest request = servletRequestAttributes.getRequest();
	        Assert.notNull(request, "request can not null");
	
	        // 此处可以用token或者JSessionId
	        String token = request.getHeader("token");
	        String path = request.getServletPath();
	        String key = getKey(token, path);
	
	        boolean flag = stringRedisTemplate.opsForValue().setIfAbsent(key, null, 1, TimeUnit.SECONDS); // 暂定设置为1秒
	
	        if (flag) {
	            // 获取锁成功, 执行进程
	            Object result;
	            result = pjp.proceed();
	            return result;
	        } else {
	            // 获取锁失败，认为是重复提交的请求
	            return ServerResult.createErrorMsg("请求过于频繁");
	        }
	
	    }
	
	    private String getKey(String token, String path) {
	        return token + path;
	    }

	}


## 测试 ##
定义一个接口，接口上使用@RepeatSubmitDeny注解，使用多线程并发进行测试。接口如下，为了方便，我直接把接口写在启动类了。

    package com.xiaofa.common;

	import com.xiaofa.common.aop.RepeatSubmitDeny;
	import com.xiaofa.common.aop.UserReq;
	import org.slf4j.Logger;
	import org.slf4j.LoggerFactory;
	import org.springframework.boot.SpringApplication;
	import org.springframework.boot.autoconfigure.SpringBootApplication;
	import org.springframework.session.data.redis.config.annotation.web.http.EnableRedisHttpSession;
	import org.springframework.web.bind.annotation.PostMapping;
	import org.springframework.web.bind.annotation.RequestBody;
	import org.springframework.web.bind.annotation.RestController;
	
	@SpringBootApplication
	@EnableRedisHttpSession
	@RestController
	public class CommonApplication {
	
	    private static final Logger logger = LoggerFactory.getLogger(CommonApplication.class);
	
	    public static void main(String[] args) {
	        SpringApplication.run(CommonApplication.class, args);
	    }
	
	    @PostMapping(value = "/hello")
	    @RepeatSubmitDeny
	    public void hello(@RequestBody UserReq userReq) {
	        logger.info("用户:{}请求成功",userReq.getUserName());
	    }
	
	}

UserReq请求对象如下：

    @Data
	public class UserReq {
	
	    private String userName;
	
	}

测试工具类如下，为了方便，该测试类实现了ApplicationRunner接口，在springboot启动时就会执行，代码如下：

    package com.xiaofa.common.aop;

	import org.springframework.boot.ApplicationArguments;
	import org.springframework.boot.ApplicationRunner;
	import org.springframework.http.HttpEntity;
	import org.springframework.http.HttpHeaders;
	import org.springframework.http.MediaType;
	import org.springframework.http.ResponseEntity;
	import org.springframework.stereotype.Component;
	import org.springframework.web.client.RestTemplate;
	
	import java.util.HashMap;
	import java.util.Map;
	import java.util.concurrent.CountDownLatch;
	import java.util.concurrent.ExecutorService;
	import java.util.concurrent.Executors;
	
	/**
	 * @desc:
	 * @Author: zhaoxiaofa
	 * @Date: 2019/6/11 22:12
	 */
	@Component
	public class RunTest implements ApplicationRunner {

	    private static final RestTemplate restTemplate = new RestTemplate();
	
	    @Override
	    public void run(ApplicationArguments args) throws Exception {
	        System.out.println("执行多线程测试");
	        String url = "http://localhost:8081/hello";
	        CountDownLatch countDownLatch = new CountDownLatch(1);
	        ExecutorService executorService = Executors.newFixedThreadPool(10);
	
	        for (int i = 0; i < 10; i++) {
	            HttpEntity request = buildRequest(i);
	            executorService.submit(() -> {
	                try {
	                    countDownLatch.await();
	                    System.out.println("Thread:" + Thread.currentThread().getName() + ", time:" + System.currentTimeMillis());
	                    ResponseEntity<String> response = restTemplate.postForEntity(url, request, String.class);
	                    System.out.println("Thread:" + Thread.currentThread().getName() + "," + response.getBody());
	
	                } catch (InterruptedException e) {
	                    e.printStackTrace();
	                }
	            });
	        }
	
	        countDownLatch.countDown();
	    }
	
	    private HttpEntity buildRequest(int i) {
	        HttpHeaders headers = new HttpHeaders();
	        headers.setContentType(MediaType.APPLICATION_JSON);
	        headers.set("token", "1111111111");
	        Map<String, Object> body = new HashMap<>();
	        body.put("userName", "zhaoxiaofa" + i);
	        return new HttpEntity<>(body, headers);
	    }

	}


时间比较急，未完待续。。。