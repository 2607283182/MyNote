## Cglib代理
### 重点之Cglib坑
https://www.liaoxuefeng.com/wiki/1252599548343744/1339039378571298
### 代理方式

```
public static void main(String[] args) {
        //设置保存Cglib的class类
        System.setProperty(DebuggingClassWriter.DEBUG_LOCATION_PROPERTY,"C:/test");
        // 新建一个Enhancer代理类
        Enhancer enhancer = new Enhancer();
        // 设置该代理类的超类
        enhancer.setSuperclass(Car.class);
        // 设置enhancer的各种姿势的拦截器
        enhancer.setCallback(new MethodInterceptor() {
            @Override
            public Object intercept(Object obj, Method method, Object[] args, MethodProxy methodProxy)
                    throws Throwable {
                System.out.println("before");
                Object res = methodProxy.invokeSuper(obj, args);
                System.out.println("after");
                return res;
            }
        });
        Car car = (Car) enhancer.create();

        car.print();
    }
```
### 多重代理方式
