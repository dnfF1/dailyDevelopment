## Springboot启动流程
- 初始化SpringApplication对象：
> 当调用 SpringApplication.run() 方法时，首先会创建一个 SpringApplication 实例。此实例会加载应用类路径下的配置文件，并初始化环境变量。
- 实例化SpringApplicationRunListeners：
> 创建一个 SpringApplicationRunListeners 的实例，该实例监听 Spring Boot 启动过程中的各个关键事件，如应用准备、环境设置、上下文加载等。
- 初始化ApplicationContext：
> 根据应用类型（如 Web 应用或非 Web 应用），选择适当的 ApplicationContext 实现类，如 AnnotationConfigApplicationContext 或 AnnotationConfigEmbeddedWebApplicationContext。
- 加载自动配置：
> Spring Boot 会扫描所有带有 @EnableAutoConfiguration 注解的类，并根据应用的依赖和环境自动配置相应的 Bean。这包括添加默认的 BeanDefinition 到容器中，以及处理 @ConditionalOnClass、@ConditionalOnMissingBean 等条件注解。
- Bean 定义扫描：
> 通过 @ComponentScan 注解指定的包，Spring Boot 会扫描并注册所有带有 @Component、@Service、@Repository 和 @Controller 注解的类作为 Bean。
- 创建并刷新 ApplicationContext：
> 使用之前准备好的 Bean 定义和自动配置信息，创建 ApplicationContext 并进行初始化。这包括解析 Bean 的依赖关系，初始化单例 Bean，以及处理循环依赖等问题。
- 触发 SpringApplicationRunListeners 的回调：
> 执行 SpringApplicationRunListeners 中的回调方法，如 environmentPrepared、contextPrepared 和 contextLoaded，以便在不同阶段进行自定义操作。
- 启动嵌入式 Web 服务器（如果适用）：
> 对于 Web 应用，Spring Boot 会自动创建并配置一个嵌入式的 Web 服务器（如 Tomcat、Jetty 或 Undertow），并将 Spring 容器注册到 Web 服务器中。
- 运行剩余的 ApplicationRunner 和 CommandLineRunner：
> 最后，Spring Boot 会运行所有实现 ApplicationRunner 或 CommandLineRunner 接口的 Bean，这些 Bean 可以执行一些启动后的初始化任务。
- 完成启动：
> 当所有 Bean 初始化完毕，Web 服务器启动完成，且所有 ApplicationRunner 和 CommandLineRunner 运行完毕后，Spring Boot 应用程序就完成了启动过程。