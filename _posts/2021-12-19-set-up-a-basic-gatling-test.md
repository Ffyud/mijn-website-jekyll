---
layout: post
title: "Set-up a basic Gatling test"
categories: [Testing]
---

# Set up a basic Gatling test

## 0 Requirements

- JDK
- Maven
- IntelliJ (or whatever IDE you prefer)
  
## 1 Set-up the project template

The project is set-up by creating a Gatling template (called an [*Archetype*](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html) in Maven). By using the the following Maven command you set up the standard folder structure in which the Gatling tests will be created.

```powershell
mvn archetype:generate -DarchetypeGroupId=io.gatling.highcharts -DarchetypeArtifactId=gatling-highcharts-maven-archetype
```

You will be asked to supply a [groupId, artifactId, version and package](https://maven.apache.org/guides/mini/guide-naming-conventions.html). Confirm afterwards by pressing Enter or typing *y*.

If all goes well, you'll read *BUILD SUCCESS*.

## 2 Open the project

The project is now ready and located at the location where you ran the previous command. Start IntelliJ and open the project. IntelliJ will begin retrieving all the dependencies, which can take a while.

- Check the *pom.xml* for the following dependencies: *gatling-charts-highcharts*, *gatling-app* and *gatling-recorder*.

- Check if the *pom.xml* contains the build plugin *gatling-maven-plugin*. This plugin adds a [Maven Build lifecycle](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html) command with which you can start the Gatling tests later on.

Also important:

 - See if IntelliJ has the [Scala plugin](https://plugins.jetbrains.com/plugin/1347-scala) (via File > Settings > Plugins).

 - When opening a Scala file inside the folder *src/test/scala*, IntelliJ  will ask you to install the [Scala SDK](https://www.scala-lang.org/). Go ahead and install the SDK. I chose the scala-sdk-2.12.15.

## 3 Writing a test

A Gatling test lives inside a class and consists of 3 main ingredients: 

1. The **http config**, the place where you specify where the application lives that you would like to test.
2. The **test scenario**, in which you more or less functionally describe what the test should do.
3. The **load scenario**, that will specify what the exact load will be when running the test scenario against the application.

### 3.1 Create a class 

Right click inside the src/test/scala folder and create a new *Scala Class* with a name of your choice. For example *MyFirstTest* (starting with a capital letter).

Add the following imports to the very top of the class.

``` scala
import io.gatling.core.Predef._
import io.gatling.http.Predef._
```

Add the following *extends* to your class.

``` scala
class MyFirstTest extends Simulation { 
    
}
```

### 3.2 Add http config

Add a piece of configuration with the URL and the necessary headers, depending ofcourse on the application you want to test against.

``` scala
val httpConf = http.baseUrl("the.url.here)
  .header("Accept", "application/json")
```

### 3.3 Add a test scenario

In the test scenario part you can define what you need to test, including your expectations. You can name the scenario anything you like. In this example a *HTTP GET request* is performed to the endpoint "countries", which is the part that comes after the *baseUrl* defined in the http config.

``` scala
val testscenario = scenario("Get information from endpoint")
  .exec(http("Get all countries")
    .get("countries"))
```

### 3.4 Add a load scenario

The third and final part you need to add is the load scenario. Here you'll describe how the test scenario should be performed, like how many users, how many requests or for what duration. The example below will simulate just one user doing the request.


```scala
setUp(
  testscenario.inject(atOnceUsers(1))
).protocols(httpConf)
```

The class is now complete and should look something like the example below.

```scala
import io.gatling.core.Predef._
import io.gatling.http.Predef._
 
class MyFirstTest extends Simulation { 
  val httpConf = http.baseUrl("the.url.here")
               .header("Accept", "application/json")
 
  val testscenario = scenario("Get information from endpoint")
    .exec(http("Get all countries")
      .get("countries"))
 
  setUp(
    testscenario.inject(atOnceUsers(1))
  ).protocols(httpConf)
}
```

## 4 Starting a test

Kicking off the test can be done directly from IntelliJ or with the Maven command (which the gatling-maven-plugin has provided).

Whenever errors occur when starting a test, chances are that a mismatch exists between the versions of the Scala SDK, the Java SDK or Gatling.

### From Intellij

Right click on src/text/scala/Engine and pick *Run 'Engine'*. Just press enter when asked for an optional description.

### With Maven

Use the Gatling specific command that exists in the Maven Build Lifecycle or click on the command inside IntelliJ.

```powershell
mvn gatling:test
```

## 5 Viewing the results

Gatling will produce a little test report inside the terminal. You'll find a full testreport in readable HTML format inside the */target* folder. It is named after the name of the class appended with a timestamp.

## 6 What's next

### 6.1 Example #1: Add steps to the test scenario

Extract the *exec()* part from the *val testscenario* and place it the class as seperate definition. 

Now you can create another definition beside this one that does a request to another endpoint, like in this example.

``` scala
  def getAllCountries() = {
    exec(http("Get all countries")
      .get("countries"))
  }
 
  def getAllContinents() = {
    exec(http("Get all continents")
      .get("continents"))
  }
```

Then you call these definitions from the test scenario, which now contains two calls.

``` scala
val testscenario = scenario("Get information from endpoint")
    .exec(getAllCountries())
    .exec(getAllContinents())
```

### 6.2 Example #2: Add an assertion to the load scenario

You can add assertions below the load scenario. In this example the *responseTime* should not be higher than 1 second.

``` scala
setUp(
    scn.inject(atOnceUsers(1))
  ).protocols(httpConf)
    .assertions(global.responseTime.max.lt(1000))
```

### 6.3 Example #3: Add multiple headers

The headers can be put inside a *Map* so you can reuse them in multiple scenarios.

``` scala
val allHeaders = Map("Content-Type" -> "application/javascript", "Accept" -> "text/html")

val httpConf = http.baseUrl("the.url.here").header(alleHeaders)
```

### 6.4 Use the documentation

Gatling offers alot of cool features. Here are just a few that make your test more interesting and powerful:

- Use data (or what Gatling calls *Feeders*): [Feeder definition](https://gatling.io/docs/gatling/reference/current/core/session/feeder/)
- Expand your load scenario (or *Injection profile*): [Injection](https://gatling.io/docs/gatling/reference/current/core/injection/)
- Add expectations (i.e. Assertions): [Assertions](https://gatling.io/docs/gatling/reference/current/core/assertions/)
- Put your Gatling tests in an automated pipeline: [Jenkins Plugin](https://gatling.io/docs/gatling/reference/current/extensions/jenkins_plugin/)

There is also a [Gatling Recorder](https://gatling.io/docs/gatling/reference/current/http/recorder/) which allows you to "record" the network traffic while using an application. That traffic will then be saved to a *HAR file* that you can then insert in your test class.