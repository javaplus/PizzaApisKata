# SpringBoot and AWS CodePipeline Kata

In this session, you will implement some REST Api's using SpringBoot and then deploy as an AWS Elastic Beanstalk application.  We will then build a continuous deployment pipeline that allows code checked into GitHub to be automatically tested, packaged, and deployed using an AWS CodePipeline.

We provide many step-by-step instructions for the AWS parts, but more general guidance on the SpringBoot development.  However, there are many links to SpringBoot documentation, tutorials, and sample code.  You can choose how far you want to go with the SpringBoot development, but the core of this tutorial will introduce you to SpringBoot and then how to set up the code pipeline.  

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
Once your app is up and running the Elastic Beanstalk, it should show the URL at the top of the EB Dashboard page.  
Remember that your application is running on port 8080.  But you'll find out even if you use the new url with the port of 8080 it still won't be reachable.  This is because traffic on port 8080 is not allowed through by default.  You need to open the port to allow traffic through on port 8080.

## Allow traffic on port 8080
To allow traffic through on a specific port address, you need to edit your VPC Security Groups settings.  
To do this, on AWS, go to **Services**, then under **Networking and Content Delivery**, go to **VPC**.

On the **VPC Dashboard**, under **Security**, click on **Security Groups**.  Check the box next to your Elastic Beanstalk environment at the top and then at the bottom go to the **Inbound Rules** tab.  Click **Edit** and then **Add another rule**.

Set the Type to **Custom TCP Rule**, Set the **Port Range** to **8080** and the **Source** to **0.0.0.0/0**.
Then click **Save**.  

This should immediately allow you to test again and get a successful response. If you don't have the URL, just go back to **Services**->**Elastic Beanstalk** and your environment should show up.  When you click it, you'll be taken to the Dashboard again and the URL will be at the top. Copy it or Right Click and open in a new Tab.  Then add port the port and uri ':8080/pizzas' to the url to test.

# Building the Code Pipeline

Before we do anything else with our application code, we are going to create a continuous deployment pipeline using AWS [CodePipeline](https://aws.amazon.com/codepipeline/).  This will enable an automated way to take our code from source control, test it, build it, and then deploy it.  This way, any future changes we make, we can commit the code to our repo and it will automatically be tested, built/packaged, and deployed.

## Get to Git

Create a Git repo on GitHub.com for your code.  (This has to be on GitHub or some other internet facing Git repository in order for AWS to be able to get access to our code).  

Commit your code to your new created GitHub.com repo.

## Create S3 Bucket for Caching

Before we can create our CodePipeline, we weed to create a place to cache our maven and build artifacts.  To do this we will create an S3 bucket.  So, under **Services**, go to **Storage**, then select **S3**, select **Create bucket**.  Give it a name, something like yourinitials-build-cache.

## Start the CodePipeline

In AWS, go to **Services**, then under **Developer Tools**, select **CodePipeline**.

Once, you have clicked "Get Started", enter a Pipeline name (can be anything,  maybe use YourInitials_PizzaApiPipeline).
Under **Source Provider** choose Github.  Click the **Connect to GitHub** button and use your GitHub credentials to log in.
Once you login and **Authorize aws-codesuite**, choose the newly created Github repo with your code in it under **Repository**.  Set the **Branch** to master. Now click **Next step**.

Under **Build provider** select **AWS CodeBuild**, then select **Create a new build project**
 

## CodeBuild project configuration 

Under the AWS CodeBuild section, give a project name (e.g. BT_PizzaAPI_CodeBuild). Under **Operating System** choose **Ubuntu**.  **Runtime**:**Java**, and **Version**:**openjdk-8**. 
Under **Cache**, in the **Type** drop down, we will have to leave this as **No cache** for now and then change it in a minute(Seems to be a bug in AWS that you cannot select your S3 bucket at this time. 
Leave the **Create a service role in your account** selected and the default Role name should be fine.  Leave the other defaults and  click the **Save build project** button at the bottom.

This should create the build project and take you back to the Create pipeline wizard.  Before we continue with the pipeline setup, let's set up our Code Build cache.  Click on the **View project details** under the CodeBuild project name.  This should launch a new tab with your CodeBuild information.  Click the **Edit project** button and then under **Cache** change the **Type** to **Amazon S3**.  Select your bucket from the the **Bucket** drop down.  You can leave the **Path prefix** blank or choose to set it to **cache/archives** (an AWS [tutorial](https://aws.amazon.com/blogs/devops/how-to-enable-caching-for-aws-codebuild/) recommends this, but it works without).

Now click the **Update** button to save the cache/S3 bucket changes to your CodeBuild, then close that tab.

Now you should be back on the **Create pipeline** wizard that is still on **Select an existing build project** and has your build project under Project name.  Click the **Next step** button.

Under **Deployment Provider** select **AWS Elastic Beanstalk** and under **Application name** if you click there, it should have your EB application listed.  Select it and then select your EB environment under **Environment name**.  Now click **Next step**.

For **AWS Service Role**, click the **Create role** button.  This will open a new tab and populate some defaults for creating a new role.  Just accept the defaults by selecting the **Allow** button at the bottom right.  This should take you right back to the previous screen with the **Role name** populated.  Just hit the **Next step** button. You can review your settings and hit **Create pipeline**.

This should create the pipeline and cause it to run right away.  However, the build step will fail because we told the CodeBuild process to look for a **buildspec.yml** file to tell it how to build the application.  We must add that to our code base.

## Adding a buildspec.yml

At the root of your workspace, (you can do this directly on Github.com) add a new file named **buildspec.yml**.  Put the following lines in it:
```
version: 0.2

phases:
  build:
    commands:
      - echo Build started on `date`
      - mvn test
  post_build:
    commands:
      - echo Build completed on `date`
      - mvn package
artifacts:
  files:
    - target/pizza-shop-api-0.0.1-SNAPSHOT.jar
```
**NOTE:** The last line of your buildspec.yml may be different. The file name may be change for you depending on how you named your project.  Check the name of the jar file in your target directory.

Once you commit this file into your master branch, it should kick off your CodePipeline again.  If you need to manually kick it off, you can go back to your CodePipeline (Services->Developer tools->CodePipeline) and select your CodePipeline, then select **Release change** and then click **Release**.  This will force it to try to detect new changes and re-release.  

Once this runs again, it should deploy your app again to Elastic Beanstalk.  When this is done, test your application again, by going to the same URL you used before.

## Make a change

Now that you have a CodePipeline set up, you can make more changes to the app and whenever they are committed to the Master branch, the change will be tested, built, and then deployed.

So, go ahead and just make a simple change to the return value of the method in your RestController.  Commit this change and watch the CodePipeline run again and then when it's finished deploying check that your new functionality is up and running in EB.

If this works, you should dance a little.  Because now you have a continuous build/deploy pipeline.

## But wait there's more!!!

Now is when the fun really happens... ok... maybe that was already too much fun and you are done... that's perfectly fine.  However, if you want to get better or learn more about implementing API's in SpringBoot, you can keep going.

However, this is where the guided tour ends... you are now free... no guard rails... explore, learn, create, do cool stuff...

Some suggestions... Implement some of the API's from the swagger more fully.  


1. Return JSON from your RestController. (Hint don't work too hard on this... let the framework do the work).
1. Figure out how to take a POST request as JSON and convert that to an object.  Simple example [here](https://stackoverflow.com/questions/40247556/spring-boot-automatic-json-to-object-at-controller).
1. Put most of your logic in Service Classes and have spring inject them into your RestControllers.
1. Create JUnit tests for your RestController.  See [tutorial here](https://spring.io/guides/gs/testing-web/).

**Good sample here**
I've created a small Spring Boot application here as an example.  NOTE: This is not a spoiler.  It's not the solution, but just another SpringBoot app that exposes REST APIs.
[My Sample Project for Reference](https://github.com/javaplus/grocery-pos)
This project has several RestControllers demonstrating different ways of taking input and returning JSON.
Look at in the controllers folder for the RestControllers.
There's also plenty of JUnit tests as well for reference.



# More Helpful Links
[Getting Started with Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/getting-started-first-application.html)

[REST Service with Spring Boot](https://spring.io/guides/gs/rest-service/)

[Spring Controller vs RestController](https://www.baeldung.com/spring-controller-vs-restcontroller)

[Detailed Spring Boot Rest Controller Tutorial](http://www.springboottutorial.com/creating-rest-service-with-spring-boot)

