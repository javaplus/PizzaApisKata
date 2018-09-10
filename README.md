# PizzaApisKata
A kata for building REST Api's using SpringBoot and Deploying to AWS using Elastic Beanstalk.

# Prerequsites:
1. Java development Kit (JDK 8) - [Download here](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
1. Maven 3.x - [Download Maven here](https://maven.apache.org/download.cgi)
1. An IDE or Text Editor (Use your favorite). Some options if you don't have one: [SpringSource Tool Suite - Eclipse based](https://spring.io/tools/sts/all) or [Visual Studio Code](https://code.visualstudio.com/)
1. AWS Account.  Free tier is should suffice, but you will have to enter a credit card number if you don't have an account already. [AWS](https://aws.amazon.com/)


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

Once you've created your first RestController and RequestMapping to a method, you can run it using the **mvn spring-boot:run** command or from inside your STS IDE with a right click and Run As->Spring Boot App.  Either way, you'll then need to use [PostMan](https://www.getpostman.com/), [CURL](https://www.lifewire.com/example-uses-of-the-linux-curl-command-4084144), or some other tool to submit the appropriate HTTP request to your endpoint.  Your endpoint should be something like this by default **http://localhost:8080/pizzas**.  Of course, this URL can change depending on how you define your RequestMapping.


## Package your application For Deployment

When your first service is tested you can go ahead and attempt to package it and deploy to AWS. To package with maven simply run the following command: 
``` 
mvn clean package
```
This will build the application, run any tests, and package it as a [fat jar](https://www.baeldung.com/deployable-fat-jar-spring-boot).
[Spring's Jar Description](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html)

This will create a target folder and in that folder you will find your jar.
You can test the jar locally by running the following command from the target folder:
```
java -jar pizza-shop-api-0.0.1-SNAPSHOT.jar
```
You may have to change the command to specify your jar name.

# Going to the Cloud

## Create Elastic Beanstalk Application

We will now just have Amazon run our jar using Elastic Beanstalk.
To do this, sign into Amazon and go to **"Services"**.  Then under **"Compute"**, choose **Elastic Beanstalk**.

Start the process to create an Elastic Beanstalk instance.  Be sure to set the Platform as Java and choose to "Upload your code".  Browse to the jar in your target folder choose it to upload. Then click the **Upload** button at the bottom.
It may take a few minutes for the jar to be uploaded. But once it is uploaded, you will be returned back to the previous screen.
Choose **"Configure more options"** before you create the application.  From the Configuration screen,  under the **Network** box click **modify**.  Then add a **VPC** and select to add a **public IP address**.  You will have to select a region as well.
Once you've selected the jar, modified the network, go ahead and create the application by clicking **Create app**.  This will take a few minutes.

## Testing in the cloud
Once your app is up and running the Elastic Beanstalk, it should show the URL at the top to access the applciation.
Remember that your application is running on port 8080.  But you'll find out even if you use the new url with the port of 8080 it still won't be reachable.  This is because traffic on port 8080 is not allowed through by default.  You need to open the port to allow traffic through on port 8080.

## Allow traffic on port 8080
To allow traffic through on a specific port address, you need to edit your VPC Security Groups settings.  
To do this, on AWS, go to **Services**, then under **Networking and Content Delivery**, go to **VPC**.

On the **VPC Dashboard**, under **Security**, click on **Security Groups**.  Check the box next to your Elastic Beanstalk environment at the top and then at the bottom go to the **Inbound Rules** tab.  Click **Edit** and then **Add another rule**.

Set the Type to **Custom TCP Rule**, Set the **Port Range** to **8080** and the **Source** to **0.0.0.0/0**.
Then click **Save**.  

This should immediately allow you to test again and get a successful response.

# Building the Code Pipeline

Before we do anything else with our application code, we are going to create a continuous deployment pipeline using AWS [CodePipeline](https://aws.amazon.com/codepipeline/).  That is create an automated way to take our code from source control, test it, build it, and then deploy it.  This way any future changes we make, we can commit the code to our repo and it will automatically be tested, built/packaged, and deployed.

## Get to Git

Create a Git repo on GitHub.com for your code.  (This has to be on GitHub or some other internet facing Git repository in order for AWS to be able to get access to our code).  

Commit your code to your new created GitHub.com repo.

## Create the CodePipeline

In AWS, go to **Services**, then under **Developer Tools**, select **CodePipeline**.

Once, you 


# More Helpful Links
[Getting Started with Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/getting-started-first-application.html)
[REST Service with Spring Boot](https://spring.io/guides/gs/rest-service/)
[Spring Controller vs RestController](https://www.baeldung.com/spring-controller-vs-restcontroller)
[Detailed Spring Boot Rest Controller Tutorial](http://www.springboottutorial.com/creating-rest-service-with-spring-boot)
