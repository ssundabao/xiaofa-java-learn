## 设置网络超时 ##
其中BaseConstant.HTTP_TIME_OUT是超时时间。  

**HttpClient方式：**  

     HttpClient httpclient = HttpClientBuilder.create().build();
            // 设置连接时间
            RequestConfig requestConfig = RequestConfig.custom()
                    .setConnectTimeout(BaseConstant.HTTP_TIME_OUT).setConnectionRequestTimeout(BaseConstant.HTTP_TIME_OUT)
                    .setSocketTimeout(BaseConstant.HTTP_TIME_OUT).build();
            HttpPost post = new HttpPost(url);
            post.setConfig(requestConfig);

**RestTemplate方式：**

    public static String sendPostJson(String url, BaseReq param) {
        SimpleClientHttpRequestFactory clientHttpRequestFactory = new SimpleClientHttpRequestFactory();
        clientHttpRequestFactory.setConnectTimeout(BaseConstant.HTTP_TIME_OUT);
        clientHttpRequestFactory.setReadTimeout(BaseConstant.HTTP_TIME_OUT);
        RestTemplate restTemplate = new RestTemplate();
        restTemplate.setRequestFactory(clientHttpRequestFactory);
        HttpHeaders headers = new HttpHeaders();
        headers.setContentType(MediaType.APPLICATION_JSON);
        HttpEntity<Object> entity = new HttpEntity<>(param, headers);
        ResponseEntity<String> responseEntity = restTemplate.postForEntity(url, entity, String.class);
        return responseEntity.getBody();
    }



## 异常捕获 ##

**HttpClient方式：**  

这四种方法两种能捕获到连接超时异常，两种能捕获到请求超时异常。

    if (SocketTimeoutException.class.equals(e.getClass())) {
        logger.error("通过getClass捕获到了请求响应超时异常");
    }
    if (ConnectTimeoutException.class.equals(e.getClass())) {
        logger.error("通过getClass捕获到了连接超时异常");
    }
    if (StringUtils.equals(e.getMessage(), "Read timed out")) {
        logger.error("通过getMessage捕获到了请求响应超时异常");
    }
    if (null != e.getCause() && StringUtils.equals(e.getCause().toString(), "java.net.SocketTimeoutException: connect timed out")) {
        logger.error("通过getCause捕获到了连接超时异常");
    }


**RestTemplate方式（TODO 这里可能有问题，有空再研究下）：**

统一使用getClass的方式处理吧。

    if (ResourceAccessException.class.equals(e.getClass())) {
        logger.error("通过getClass捕获到了请求响应超时异常");
    }
    


## 为什么不能catch SocketTimeoutException ##

