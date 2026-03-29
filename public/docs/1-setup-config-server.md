# Spring Cloud Config Server Setup

## 1. Create Config Server Project

Create a Spring Boot project and add dependencies:

* `spring-boot-starter-actuator`
* `spring-cloud-config-server`
* `spring-cloud-bus`
* `spring-cloud-stream-binder-rabbit`

## 2. Enable Config Server

In your main application class:

```java
@EnableConfigServer
@SpringBootApplication
public class ConfigServerApplication {
    public static void main(String[] args) {
        SpringApplication.run(ConfigServerApplication.class, args);
    }
}
```

## 3. Decide Config Storage Strategy

Two approaches:

* **Development → Classpath (local files)** ✅ (we use this now)
* **Production → Git-based config**

## 4. Configure `application.yml`

Define basic properties: like - application info, profiles, config server related properties, management (actuator),
rabbitmq related for bus refresh at runtime, and port (8888 default here). these are mentioned here in my application
but it will depends on application requirement.

```yaml
spring:
  application:
    name: config-server
  profiles:
    active: native # for files or classpath
  cloud:
    config:
      server:
        native:
          search-locations: classpath:/config/{application}
server:
  port: 8888
```

👉 `{application}` = microservice name

## 5. Create Config Files Structure

Inside `src/main/resources`:

```
src/main/resources
 └── config
      ├── eureka-server
      │     ├── application.yml
      │     ├── application-dev.yml   # dev specific
      |     ├── application-prod.yml  # prod specific
      ├── user-service
      │     ├── application.yml
      │     ├── application-dev.yml
      |
      └──  and so on..
```

👉 Folder name = **microservice name**
👉 Profiles supported via `application-{profile}.yml`

## 6. RabbitMQ Requirement (for Bus Refresh)

Since using:

* `spring-cloud-bus`
* RabbitMQ binder

You must run RabbitMQ before starting config server:

```bash
docker-compose up -d
```

Then start your config server.

## 7. Verify Config Server

Access in browser:

```
http://localhost:8888/{application}/{profile}
```

Example:

```
http://localhost:8888/eureka-server/dev
```

---

## 🔁 Key Things to Remember

* Config Server default port → **8888**
* Folder name must match **spring.application.name** of client service
* Use **classpath for dev**, **Git for production**
* RabbitMQ required for **dynamic refresh (bus)**
