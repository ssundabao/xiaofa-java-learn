## 背景 ##




## 产生原因 ##




## 解决办法 ##

    @Bean
	public RestTemplate restTemplate() {
	    RestTemplate restTemplate = new RestTemplate();
	    restTemplate.getMessageConverters().set(1, new StringHttpMessageConverter(StandardCharsets.UTF_8));
	    return restTemplate;
	 }


