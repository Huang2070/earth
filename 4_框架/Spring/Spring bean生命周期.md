# spring bean 生命周期
* Spring IOC容器实例化Bean.
* 构造函数.
* 注入成员变量.
* 执行BeanPostProcessor的postProcessBeforeInitialization().
* 执行@PostConstruct注解的方法.
* 执行InitializingBean的afterPropertiesSet().
* xml的`<bean>`里的init-method属性定义的方法.
* 执行BeanPostProcessor的postProcessAfterInitialization().

* `使用bean`
  
* @PreDestroy.
* DisposableBean的destroy().
* xml的`<bean>`里的destroy-method属性定义的方法.

<br>
<br>
<br>

# 用法
在spring bean 的生命周期中，给我们预留了很多参与bean 的生命周期的方法。

## 通过 `<bean>` 元素的 init-method/destroy-method属性指定初始化之后 /销毁之前调用的操作方法
```xml
<bean class="com.ckl.springbeanlifecycle.DemoController" init-method="init" destroy-method="cleanUp"></bean>
```


## 通过实现 InitializingBean/DisposableBean 接口来定制初始化之后/销毁之前的操作方法；

## 在指定方法上加上@PostConstruct 或@PreDestroy注解来制定该方法是在初始化之后还是销毁之前调用；

注解的方法遵守如下规则:  
* 该方法不得有任何参数
* 该方法的返回类型必须为 void；
* 该方法不得抛出已检查异常；
* 应用 PostConstruct 的方法可以是 public、protected、package private 或 private；
* 该方法不能是 static；该方法可以是 final；
* 该方法只会被执行一次

代码如下: 
```Java
package com.ckl.springbeanlifecycle;

import com.ckl.springbeanlifecycle.service.IDemoService;
import org.springframework.beans.factory.DisposableBean;
import org.springframework.beans.factory.InitializingBean;
import org.springframework.beans.factory.annotation.Autowired;

import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;

public class DemoController implements InitializingBean,DisposableBean {

    @Autowired
    private IDemoService iDemoService;
    
    public DemoController() {
        System.out.println();
        System.out.println("constructor ");
        System.out.println( "属性：" + iDemoService);
        System.out.println();
    }

    @Override
    public void destroy() throws Exception {
        System.out.println();
        System.out.println("implements DisposableBean interface");
        System.out.println( "属性iDemoService已注入：" + (iDemoService != null));
        System.out.println( "属性iDemoService已注入：" + iDemoService);
        System.out.println();
    }

    @Override
    public void afterPropertiesSet() throws Exception {
        System.out.println();
        System.out.println("afterPropertiesSet interface");
        System.out.println( "属性iDemoService已注入：" + (iDemoService != null));
        System.out.println( "属性iDemoService已注入：" + iDemoService);
        System.out.println();
    }

    @PostConstruct
    public void  postConstruct(){
        System.out.println();
        System.out.println("@PostConstrut....");
        System.out.println( "属性iDemoService已注入：" + (iDemoService != null));
        System.out.println( "属性iDemoService已注入：" + iDemoService);
        System.out.println();
    }

    @PreDestroy
    public void  preDestroy(){
        System.out.println();
        System.out.println("@PreDestroy.....");
        System.out.println( "属性iDemoService已注入：" + iDemoService);
        System.out.println();
    }

    public void init(){
        System.out.println();
        System.out.println("init-method by xml 配置文件");
        System.out.println( "属性iDemoService已注入：" + (iDemoService != null));
        System.out.println();
    }
    public void  cleanUp(){
        System.out.println();
        System.out.println("destroy-method by xml 配置文件");
        System.out.println( "属性iDemoService已注入：" + iDemoService);
        System.out.println();
    }
}
```


## 自定义BeanPostProcessor，来让spring回调我们的方法来参与bean的生命周期。
```Java
package com.ckl.springbeanlifecycle;

import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanPostProcessor;
import org.springframework.stereotype.Component;

import java.lang.reflect.Field;

@Component
public class MyBeanPostProcessor implements BeanPostProcessor {
    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        if (bean instanceof DemoController) {
            System.out.println();
            System.out.println("BeanPostProcessor:" + "postProcessBeforeInitialization");
            Field field = null;
            try {
                field = bean.getClass().getDeclaredField("iDemoService");
                field.setAccessible(true);
            } catch (NoSuchFieldException e) {
                e.printStackTrace();
            }
            try {
                Object o = field.get(bean);
                System.out.println( "属性iDemoService已注入：" + (o != null));
                System.out.println( "属性iDemoService已注入：" + o);
                System.out.println();
            } catch (IllegalAccessException e) {
                e.printStackTrace();
            }
        }
        return bean;
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        if (bean instanceof DemoController){
            System.out.println();
            System.out.println("BeanPostProcessor:" + "postProcessAfterInitialization");
            Field field = null;
            try {
                field = bean.getClass().getDeclaredField("iDemoService");
                field.setAccessible(true);
            } catch (NoSuchFieldException e) {
                e.printStackTrace();
            }
            try {
                Object o = field.get(bean);
                System.out.println( "属性iDemoService已注入：" + (o != null));
                System.out.println( "属性iDemoService已注入：" + o);
                System.out.println();
            } catch (IllegalAccessException e) {
                e.printStackTrace();
            }
        }
        return bean;
    }
}
```