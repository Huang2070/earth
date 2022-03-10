# 作用
实现ApplicationContextAware接口, 可以从已有的spring上下文取得已实例化的类.  

ApplicationContextAware 的实现类在初始化实例的时候, 会自动调用ApplicationContextAware接口中的setApplicationContext方法, setApplicationContext方法中可以获取到上下文环境对象applicationContext, 然后就可以通过applicationContext得到Spring容器中的实例。

# 用法
```Java
@Component
public class BeanManager implements ApplicationContextAware {

    private static ApplicationContext applicationContext;
    
    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        BeanManager.applicationContext = applicationContext;
    }   

    //根据注入Spring的实例名称获取实例
    public static Object getBean(String beanName) {
        return getContext().getBean(beanName);
    }

    //根据类名获取实例
    public static <T> T getBean(Class<T> tClass) {
        return getContext().getBean(tClass);
    }

    //spring根据类type获取实例, 返回一个map, key是实例的名称,value是实例
    public static <T> Map<String, T> getBeansByType(Class<T> baseType){
        return applicationContext.getBeansOfType(baseType);
    }

    //获取applicationContext
    public static ApplicationContext getContext() {
        if (applicationContext == null) {
            throw new RuntimeException("ApplicationContext 未完成初始化....");
        }
        return applicationContext;
    }
}
```

# 使用场景
* 我们在做开发的时候，并不是说在每一个地方都能将属性注入到我们想要的地方去的，比如在Utils使用到dao，我们就不能直接注入了，这个时候就是我们需要封装springContext的时候了，而ApplicationContextAware就起了关键性的作用。
* 获取实现某个接口所有的实例.
```Java
//先定义一个静态的map
private static final Map<String, OrderHandler> HANDLER_MAP = new HashMap<>();

@Override
public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
    // 装载所有实现接口的处理类
    HANDLER_MAP.putAll(applicationContext.getBeansOfType(OrderHandler.class));
}
```
* 