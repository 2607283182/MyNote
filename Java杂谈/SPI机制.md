## SPI机制简介
SPI的全名为Service Provider Interface.java spi机制的思想: 系统里抽象的各个模块，往往有很多不同的实现方案，在面向的对象的设计里，一般推荐模块之间基于接口编程，模块之间不对实现类进行硬编码。一旦代码里涉及具体的实现类，就违反了可拔插的原则，如果需要替换一种实现，就需要修改代码。为了实现在模块装配的时候能不在程序里动态指明，这就需要一种服务发现机制。 java spi就是提供这样的一个机制：为某个接口寻找服务实现的机制。有点类似IOC的思想，就是将装配的控制权移到程序之外，在模块化设计中这个机制尤其重要。所以SPI的核心思想就是解耦

网上找了一个认为比较容易理解的例子：JDK中有支持音乐播放，假设只支持mp3的播放，有些厂商想在这个基础之上支持mp4播放，有的想支持mp5播放，而这些厂商都是第三方厂商，如果没有提供SPI这种实现标准，那就只有修改JAVA的源代码了，那这个弊端也是显而易见的，而有了SPI标准，SUN公司只需要提供一个播放接口，在实现播放的功能上通过ServiceLoad的方式加载服务，那么第三方只需要实现这个播放接口，再按SPI标准的约定进行打包，再放到classpath下面就OK了，没有一点代码的侵入性。
## SPI具体约定
java spi的具体约定为:当服务的提供者，提供了服务接口的一种实现之后，在jar包的META-INF/services/目录里同时创建一个以服务接口命名的文件。该文件里就是实现该服务接口的具体实现类。而当外部程序装配这个模块的时候，就能通过该jar包META-INF/services/里的配置文件找到具体的实现类名，并装载实例化，完成模块的注入。 基于这样一个约定就能很好的找到服务接口的实现类，而不需要再代码里制定。jdk提供服务实现查找的一个工具类：java.util.ServiceLoader
## 使用实现类
```
// 获取People,去每个包的META-INF/services 找People类名的文件，读取里面的值，里面的值写的是一个People类的实现类的包路径，
例如:
com.test.PeopleImpl
// 代码里使用
// 这句话等价于
People p = new PeopleImpl(); 再把p放入ServiceLoader里。
ServiceLoader<People> services = ServiceLoader.load(People.class);
// 遍历器，把服务遍历出来使用。
Iterator<People> peoples = services.iterator();
...
```