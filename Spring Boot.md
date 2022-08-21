## Spring Boot

### 配置文件

- 默认文件名：`application.properties` or `application.yml`

  - 如果想指定别的文件名，可以在启动时设置`spring.config.name`参数

    ```java
    java -jar myproject.jar --spring.config.name=myproject
    ```

    

- 默认配置路径（加载优先级从低到高）

  - classpath
  - classpath/config
  - 当前project
  - 当前project/config
  - 当前project/config/subdirectory

- 也可以在启动时设置`spring.config.location`变量来制定配置文件或者配置文件所在的路径

  - 这时不会去上面的这些默认路径中寻找配置文件
  - 可以同时指定多个路径，这时后面的内容会override前面的

- 如果只是想在默认路径的基础上在增加几个路径，可以指定`spring.config.additional-location`参数

- Import其他配置

  - `spring.config.import`
  - import文件中的配置信息会覆盖触发import的配置文件，无论import写在哪里

- Profile Specific Files

  - 文件名`application-{profile}.properties`或者``application-{profile}.yml`
  - 加载路径和`application.properties`, `application.yml`相同
  - 如果同时存在多个，可以通过`spring.profiles.active`属性来指定加载哪几个