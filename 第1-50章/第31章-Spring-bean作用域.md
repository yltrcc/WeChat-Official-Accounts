# 每日一句
I must say a word about fear. It is life’s only true opponent. Only fear can defeat life. 
这里必须说说恐惧，它是生活惟一真正的对手，因为只有恐惧才能打败生活。


# 概述

当在 Spring 中定义一个 时，你必须声明该 bean 的作用域的选项。例如，为了强制 Spring 在每次需要时都产生一个新的 bean 实例，你应该声明 bean 的作用域的属性为 **prototype**。同理，如果你想让 Spring 在每次需要时都返回同一个bean实例，你应该声明 bean 的作用域的属性为 **singleton**。

Spring 框架支持以下五个作用域，如果你使用 web-aware ApplicationContext 时，其中三个是可用的。

|||
|-|-|
|作用域|描述<br>|
|singleton|该作用域将 bean 的定义的限制在每一个 Spring IoC 容器中的一个单一实例(默认)。|
|prototype|该作用域将单一 bean 的定义限制在任意数量的对象实例。|
|request|该作用域将 bean 的定义限制为 HTTP 请求。只在 web-aware Spring ApplicationContext 的上下文中有效。|
|session|该作用域将 bean 的定义限制为 HTTP 会话。 只在web-aware Spring ApplicationContext的上下文中有效。|
|global-session|该作用域将 bean 的定义限制为全局 HTTP 会话。只在 web-aware Spring ApplicationContext 的上下文中有效。|


本章将讨论前两个范围，当我们将讨论有关 web-aware Spring ApplicationContext 时，其余三个将被讨论。

# singleton 作用域

如果作用域设置为 singleton，那么 Spring IoC 容器刚好创建一个由该 bean 定义的对象的实例。该单一实例将存储在这种单例 bean 的高速缓存中，以及针对该 bean 的所有后续的请求和引用都返回缓存对象。

默认作用域是始终是 singleton，但是当仅仅需要 bean 的一个实例时，你可以在 bean 的配置文件中设置作用域的属性为 singleton，如下所示：

```text
<!-- A bean definition with singleton scope -->
<bean scope="singleton">
    <!-- collaborators and configuration for this bean go here -->
</bean>

```

## 例子

我们在适当的位置使用 Eclipse IDE，然后按照下面的步骤来创建一个 Spring 应用程序：

|||
|-|-|
|步骤|描述<br>|
|1|创建一个名称为 *SpringExample* 的项目，并且在创建项目的 **src** 文件夹中创建一个包 *com.tutorialspoint*。|
|2|使用 *Add External JARs* 选项，添加所需的 Spring 库，在 *Spring Hello World Example* 章节解释。|
|3|在 *com.tutorialspoint* 包中创建 Java 类 *HelloWorld* 和 *MainApp*。|
|4|在 **src** 文件夹中创建 Beans 配置文件 *Beans.xml*。|
|5|最后一步是创建的所有 Java 文件和 Bean 配置文件的内容，并运行应用程序，解释如下。|


这里是 **HelloWorld.java** 文件的内容：

```text
package com.tutorialspoint;
public class HelloWorld {
   private String message;
   public void setMessage(String message){
      this.message  = message;
   }
   public void getMessage(){
      System.out.println("Your Message : " + message);
   }
}

```

下面是 MainApp.java 文件的内容：

```text
package com.tutorialspoint;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
public class MainApp {
   public static void main(String[] args) {
      ApplicationContext context = new ClassPathXmlApplicationContext("Beans.xml");
      HelloWorld objA = (HelloWorld) context.getBean("helloWorld");
      objA.setMessage("I'm object A");
      objA.getMessage();
      HelloWorld objB = (HelloWorld) context.getBean("helloWorld");
      objB.getMessage();
   }
}

```

下面是 singleton 作用域必需的配置文件 **Beans.xml**：

```text
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

   <bean scope="singleton">
   </bean>

</beans>

```

一旦你创建源代码和 bean 配置文件完成后，我们就可以运行该应用程序。如果你的应用程序一切都正常，将输出以下信息：

```text
Your Message : I'm object A
Your Message : I'm object A

```

# prototype 作用域

如果作用域设置为 prototype，那么每次特定的 bean 发出请求时 Spring IoC 容器就创建对象的新的 Bean 实例。一般说来，满状态的 bean 使用 prototype 作用域和没有状态的 bean 使用 singleton 作用域。

为了定义 prototype 作用域，你可以在 bean 的配置文件中设置作用域的属性为 prototype，如下所示：

```text
<!-- A bean definition with singleton scope -->
<bean scope="prototype">
   <!-- collaborators and configuration for this bean go here -->
</bean>

```

## 例子

我们在适当的位置使用 Eclipse IDE，然后按照下面的步骤来创建一个 Spring 应用程序：

|||
|-|-|
|步骤|描述<br>|
|1|创建一个名称为 *SpringExample* 的项目，并且在创建项目的 **src** 文件夹中创建一个包*com.tutorialspoint*。|
|2|使用 *Add External JARs* 选项，添加所需的 Spring 库，解释见 *Spring Hello World Example* 章节。|
|3|在 *com.tutorialspoint* 包中创建 Java 类 *HelloWorld* 和 *MainApp*。|
|4|在 **src** 文件夹中创建 Beans 配置文件*Beans.xml*。|
|5|最后一步是创建的所有 Java 文件和 Bean 配置文件的内容，并运行应用程序，解释如下所示。|


这里是 **HelloWorld.java** 文件的内容：

```text
package com.tutorialspoint;

public class HelloWorld {
   private String message;

   public void setMessage(String message){
      this.message  = message;
   }

   public void getMessage(){
      System.out.println("Your Message : " + message);
   }
}

```

下面是 **MainApp.java** 文件的内容：

```text
package com.tutorialspoint;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
public class MainApp {
   public static void main(String[] args) {
      ApplicationContext context = new ClassPathXmlApplicationContext("Beans.xml");
      HelloWorld objA = (HelloWorld) context.getBean("helloWorld");
      objA.setMessage("I'm object A");
      objA.getMessage();
      HelloWorld objB = (HelloWorld) context.getBean("helloWorld");
      objB.getMessage();
   }
}

```

下面是 **prototype** 作用域必需的配置文件 Beans.xml：

```text
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

   <bean scope="prototype">
   </bean>

</beans>

```

一旦你创建源代码和 Bean 配置文件完成后，我们就可以运行该应用程序。如果你的应用程序一切都正常，将输出以下信息：

```text
Your Message : I'm object A
Your Message : null

```

# 美文佳句

一个人只要有梦想，生命就有了依托；一个人只有不懈地追逐着梦想，活着才觉得意义深远，趣味无穷，也才能将生命的潜能发挥到极致。
