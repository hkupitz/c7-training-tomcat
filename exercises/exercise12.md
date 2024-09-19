# Create an External Task Worker With the Spring Boot Starter

## Goal

Create a new Maven project and add the dependency for the `camunda-bpm-spring-boot-starter-external-task-client`.
Add implementations for an external task worker.

### Spring Boot application
4. Create a new Maven project.
5. Open your project's `pom.xml` and add these dependencies:
```xml
<dependencyManagement>
  <dependencies>
      <dependency>
        <groupId>org.camunda.bpm</groupId>
        <artifactId>camunda-bom</artifactId>
        <version>${camunda.version}</version>
        <scope>import</scope>
        <type>pom</type>
      </dependency>

      <dependency>
        <groupId>org.camunda.bpm.dmn</groupId>
        <artifactId>camunda-engine-dmn-bom</artifactId>
        <version>${camunda.version}</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>

      <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-dependencies</artifactId>
        <version>${springBoot.version}</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
  </dependencies>
</dependencyManagement>

<dependencies>
  <dependency>
    <groupId>org.camunda.bpm.springboot</groupId>
    <artifactId>camunda-bpm-spring-boot-starter-external-task-client</artifactId>
    <version>7.19.15-ee</version>
  </dependency>

  <dependency>
    <groupId>javax.xml.bind</groupId>
    <artifactId>jaxb-api</artifactId>
    <version>2.3.1</version>
  </dependency>
</dependencies>
```
7. Set the following `<properties>:
```xml
<springBoot.version>2.6.4</springBoot.version>
<camunda.version>7.19.15-ee</camunda.version>
```
8. Add the private Camunda artifactory <repository> to your `pom.xml`:
```xml
<repositories>
  <repository>
    <id>camunda-bpm-nexus-ee</id>
    <name>Camunda Enterprise Maven Repository</name>
    <url>https://artifacts.camunda.com/artifactory/private/</url>
  </repository>
</repositories>
```
6. Create an new Spring Boot application class. It should implement the `main()` method.
```java
@SpringBootApplication
public class ExternalTaskWorkerApplication {

  public static void main(String[] args) {
    SpringApplication.run(ExternalTaskWorkerApplication.class, args);
  }
}
```
7. Create a configuration file named `application.yml` in the src/main/resources directory.
8. Add the following content:
```yaml
camunda.bpm.client:
  base-url: http://localhost:8080/engine-rest
  max-tasks: 1
  lock-duration: 20000
  worker-id: spring-boot-worker-1
  async-response-timeout: 1000
  disable-backoff-strategy: true
logging:
  level:
    "[org.apache.http]": INFO
```

### External Task worker
9. Add a bean for the external task worker in a seperate class that listens to the `creditDeduction` topic. 
```java
@SpringBootApplication
public class ExternalTaskWorkerApplication {

  @Component
  @ExternalTaskSubscription("creditDeduction")
  public ExternalTaskHandler getDeductCustomerCreditWorker() {
    return (ExternalTaskHandler) (externalTask, externalTaskService) -> {
      logger.info("External task invoked: " + externalTask.getId());

      externalTaskService.complete(externalTask);
    };
  }
}
```
10. Start your process application.
11. Start your external task worker application.
12. Start a new process instance.
13. Open the IDE for your external task worker and check the console output. You should find the log statement from your handler: "External task invoked: <ID>".
14. The process might run into some incidents for now.

### Proper business logic
14. Copy the services from your process application Maven project to your external task worker project.
15. Re-implement the original delegate logic in your external task worker bean.
16. Verify that everything works as expected by starting a process instance of the order process. It should invoke the payment process as usual and complete eventually.