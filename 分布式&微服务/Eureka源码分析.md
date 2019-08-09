建议对着源码看本文
## Eureka服务端启动##




## Eureka客户端启动 ##



DiscoveryClient类的（getServiceUrlsFromConfig方法） 已废弃的方法 指向  
EndpointUtils类    
DefaultEurekaServerContext  initialize()
resolvePeerUrls  
getDiscoveryServiceUrls  
getServiceUrlsFromConfig   

关于region，由以下代码可以看出 region只有一个，默认值是default。

    String region = getRegion(clientConfig);

关于zone,由以下代码可以看出zone是一个数组，getAvailabilityZones方法有两个实现。

    String[] availZones = clientConfig.getAvailabilityZones(clientConfig.getRegion());

DefaultEurekaClientConfig类实现该方法 

    @Override
    public String[] getAvailabilityZones(String region) {
        return configInstance
                .getStringProperty(
                        namespace + region + "." + CONFIG_AVAILABILITY_ZONE_PREFIX,
                        DEFAULT_ZONE).get().split(",");
    }


EurekaClientConfigBean类实现该方法

    @Override
	public String[] getAvailabilityZones(String region) {
		String value = this.availabilityZones.get(region);
		if (value == null) {
			value = DEFAULT_ZONE;
		}
		return value.split(",");
	}

到底是使用哪个方法实现的呢？EurekaClientConfig接口上面有一个注解@ImplementedBy，这个注解是将接口和实现类绑定起来。指定使用DefaultEurekaClientConfig类实现。但是实际上，在我debug的过程中发现是用EurekaClientConfigBean类的方法实现的。卧槽，懵逼啊

    @ImplementedBy(DefaultEurekaClientConfig.class)

可以看出，region和zone是一对多的关系。


