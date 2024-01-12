
### 介绍

Lombok 是一个 Java 库，通过注解的方式简化了 Java 代码的编写，减少了样板代码，提高了开发效率。
- **`@Data`：** 自动生成 Getter、Setter、`toString`、`equals` 和 `hashCode` 方法。
- **`@Getter` / `@Setter/toString`：** 自动生成 Getter 或 Setter , toString方法。
- **`@EqualsAndHashCode`：** 自动生成 `equals` 和 `hashCode` 方法。
- **`@NoArgsConstructor` / `@AllArgsConstructor`：** 自动生成无参构造方法 或 全参构造方法。

### 拓展功能

安装Lombok插件
- **`@Slf4j`：** 自动生成 `log` 字段，用于简化日志的使用。
```java
@Slf4j
public class Example {
    public Furn furn() {
        log.info("myfurn={} furni={}", furn,furn);//使用占位符
    }
}
```

### Initailizr

Spring Initializr 是一个用于初始化 Spring 项目的在线工具。它提供了一个简单的 Web 界面，允许你选择 Spring Boot 版本、项目的基础架构、依赖项等，然后生成一个用于构建 Spring 项目的基础代码。