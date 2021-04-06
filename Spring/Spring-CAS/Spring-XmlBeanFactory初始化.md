## XmlBeanFactory构造函数最后调用到AbstractAutowireCapableBeanFactory的构造函数
```
    // 忽略这些接口
	public AbstractAutowireCapableBeanFactory() {
		super();
		ignoreDependencyInterface(BeanNameAware.class);
		ignoreDependencyInterface(BeanFactoryAware.class);
		ignoreDependencyInterface(BeanClassLoaderAware.class);
	}
```
## 该类组合的 XmlBeanDefinitionReader类解析 , 初始化registry,资源加载器和环境
```
    // 1.把beanFactory传进去了...
    private final XmlBeanDefinitionReader reader = new XmlBeanDefinitionReader(this);
    // 2.调用到抽象reader
	protected AbstractBeanDefinitionReader(BeanDefinitionRegistry registry) {
		Assert.notNull(registry, "BeanDefinitionRegistry must not be null");
        // 2.1 把beanFactory当做registry,因为它确实实现了这个接口。
		this.registry = registry;
        
		// Determine ResourceLoader to use.
        // 2.2 判断beanFactory是不是解析器，是的话就还取beanFactory 
		if (this.registry instanceof ResourceLoader) {
			this.resourceLoader = (ResourceLoader) this.registry;
		}
		else {
			this.resourceLoader = new PathMatchingResourcePatternResolver();
		}

		// Inherit Environment if possible
        // 2.3 判断是不是环境，是的话也是还取beanFactory
		if (this.registry instanceof EnvironmentCapable) {
			this.environment = ((EnvironmentCapable) this.registry).getEnvironment();
		}
		else {
			this.environment = new StandardEnvironment();  // Environment详解见Enviroment.md
		}
	}
```
## XmlBeanFactory 构造器,使用reader加载beanDefinitions
```
	public XmlBeanFactory(Resource resource, BeanFactory parentBeanFactory) throws BeansException {
		super(parentBeanFactory);
		this.reader.loadBeanDefinitions(resource);
	}
```
步骤
```
1. 把inputStream转成Document  // Resource资源类详解见Resource.md
2. 
```