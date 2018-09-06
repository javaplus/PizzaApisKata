# PizzaApisKata
A kata for building REST Api's using SpringBoot and Deploying to AWS using Elastic Beanstalk.

# Prerequsites:
1. Java development Kit (JDK 8) - [Download here](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
1. Maven 3.x - [Download Maven here](https://maven.apache.org/download.cgi)
1. An IDE or Text Editor (Use your favorite). Some options if you don't have one: [SpringSource Tool Suite - Eclipse based](https://spring.io/tools/sts/all) or [Visual Studio Code](https://code.visualstudio.com/)
1. AWS Account.  Free tier is enough. [AWS](https://aws.amazon.com/)


# The GOAL

The goal is to implement the following OAS/Swagger using SpringBoot RestControllers.:  [PizzaShop OAS](https://app.swaggerhub.com/apis/btarlton/PizzaShop/1.0.0)

The data you return can all be mocked if need be as the goal is not necessarily to have a completely working application, but understanding how to work with SpringBoot, RestControllers, and then how to deploy to AWS using CodePipeline.

# Getting started

## create a starter project
Start by creating a SpringBoot application.  Using the Spring Initializr(https://start.spring.io/) is a good way to start.
You will need the Web(SpringMVC) dependency and optionally I'd add Lombok for convenience.

After downloading and unziping the starter application, you can attempt to run it by using the following command:
``` 
mvn spring-boot:run
```
This utilizes the [spring boot maven plugin](https://docs.spring.io/spring-boot/docs/current/maven-plugin/index.html)

**NOTE:** By default, Spring Boot using port 8080 to run.  So, be sure that port is free.

## Creating Your First Rest Controller

You can start by building a simple class annotated with [RestController](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/annotation/RestController.html) that will be entry point to your application for your Pizza apis.

This will require creating a method that is annotated with the [RequestMapping](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/annotation/RequestMapping.html) annotation to map an HTTP request to your method.  This method could just return a hard coded string to get started with.

Helpful Links:
[RESTController Tutorial](http://zetcode.com/springboot/restcontroller/)
[RequestMapping Tutorial](https://www.baeldung.com/spring-requestmapping)

## Test your application

Once you've created your first RestController and RequestMapping to a method, you can run it using the **mvn spring-boot:run"** command or from inside your STS IDE with a right click and Run As->Spring Boot App.  Either way, you'll then need to use [PostMan](https://www.getpostman.com/), [CURL](https://www.lifewire.com/example-uses-of-the-linux-curl-command-4084144), or some other tool to submit the appropriate HTTP request to your endpoint.  Your endpoint should be something like this by default **http://localhost:8080/pizzas**.  Of course, this URL can change depending on how you define your RequestMapping.


## Package your application


# More Helpful Links
[Getting Started with Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/getting-started-first-application.html)
[REST Service with Spring Boot](https://spring.io/guides/gs/rest-service/)
[Spring Controller vs RestController](https://www.baeldung.com/spring-controller-vs-restcontroller)
[Detailed Spring Boot Rest Controller Tutorial](http://www.springboottutorial.com/creating-rest-service-with-spring-boot)
