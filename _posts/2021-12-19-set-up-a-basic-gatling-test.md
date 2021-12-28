---
layout: post
title: "Set-up a basic Gatling test"
categories: [Testing]
---

# Set up a basic Gatling test


## 0 Requirements

- JDK
- Maven
- IntelliJ
  
## 1 Set-up the project template

The project is setup by creating a Gatling template (called an *Archetype* in Maven). By using the the following terminal command you'll set up the standard folder structure in which the Gatling tests will be created.

```powershell
mvn archetype:generate -DarchetypeGroupId=io.gatling.highcharts -DarchetypeArtifactId=gatling-highcharts-maven-archetype
```

You will be asked to supply a groupId, artifactId, version and package. Confirm afterwards by pressing Enter or typing *y*.

If all goes well, you'll read *BUILD SUCCESS*.

## 2 Open the project

The project is now ready and located at the location where you ran the previous command. Start IntelliJ and open the project. IntelliJ will start retrieving all the dependencies, which can take a while.

- Check the *pom.xml* for the following dependencies: *gatling-charts-highcharts*, *gatling-app* and *gatling-recorder*.

- Check if the *pom.xml* contains the build plugin *gatling-maven-plugin*. This plugin adds a Maven lifecycle command with which you can start the Gatling tests later on.

Also important:

 - See if IntelliJ has the Scala plugin (via File > Settings > Plugins).

 - When opening a Scala file inside the folder src/test/scala, IntelliJ  will ask you to install the Scala SDK. Go ahead and install the SDK. I chose the scala-sdk-2.12.15.

## 3 Writing a test

A Gatling test lives inside a class and consists of 3 main ingredients: 

1. The **http config**, the place where you specify where the application lives that you would like to test.
2. The **test scenario**, in which you more or less functionally describe what the test should do.
3. The **load scenario**, that will specify what the exact load will be when running the test scenario against the application.

### 3.1 Create a class 

Right click inside the src/test/scala folder and create a new Scala Class with a name of your choice. For example MyFirstTest (starting with a capital letter).

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

### 3.2 Add http config to the class

Add a piece of configuration with the URL and the necessary headers, depending ofcourse on the application you want to test against.

``` scala
val httpConf = http.baseUrl("the.url.here)
  .header("Accept", "application/json")
```

### 3.3 Voeg een testscenario aan je class toe

In Gatling definieer je testscenario's met daarin wat je wil gaan doen en wat je verwacht. Je kan het scenario een naam naar keuze geven. In dit voorbeeld wordt een HTTP GET request gedaan naar het endpoint gemeente.

``` scala
val testscenario = scenario("Get information from endpoint")
  .exec(http("Get alle users")
    .get("users"))
```

### 3.4 Voeg een load scenario aan je class toe

Als laatste stap in de test voeg je een load scenario toe, waarin je bepaald hoe het testscenario uitgevoerd moet worden. Bijvoorbeeld hoevaak je de bevraging wil doen en met hoeveel gebruikers. 

```scala
setUp(
  testscenario.inject(atOnceUsers(1))
).protocols(httpConf)
```

In onderstaande voorbeeld wordt er simpelweg 1 bevraging door 1 gebruiker gedaan.

```scala
import io.gatling.core.Predef._
import io.gatling.http.Predef._
 
class MyFirstTest extends Simulation { 
  val httpConf = http.baseUrl("the.url.here")
               .header("Accept", "application/json")
 
  val testscenario = scenario("Get information from endpoint")
    .exec(http("Get alle users")
      .get("users"))
 
  setUp(
    testscenario.inject(atOnceUsers(1))
  ).protocols(httpConf)
}
```

## 4 Starting a test

Testen aftrappen kan lokaal direct vanuit Intellij of met behulp van een Maven commando (dankzij de gatling-maven-plugin).

Als er fouten optreden bij het starten van een test is de kans groot dat er een versie mismatch is tussen de versie van de Scala SDK, Java SDK of Gatling.

### Vanuit Intellij

Rechtermuisklik op src/test/scala/Engine en kies voor Run 'Engine'. Druk gerust op enter als er gevraagd wordt om een optional description.

### Met Maven

Gebruik hiertoe het commando wat is toegevoegd aan de Maven Lifecycle of klik dit commando aan in Intellij.

```powershell
mvn gatling:test
```

## 5 Viewing the test results

Gatling zal een klein overzichtje uitdraaien in de terminal en genereert ook een leesbaar dossier in HTML. Het dossier vindt je onder /target in een map die vernoemd is naar de naam van je class met een timestamp. 

## 6 What's next

### 6.1 Example #1: voeg een stap toe aan het testscenario

Haal de code exec() uit de val testscenario en plaats deze los in je class als def. Daarnaast voeg je nog een def toe, maar dan voor een ander endpoint.

``` scala
  def getAlleGemeenten() = {
    exec(http("Alle gemeentes ophalen")
      .get("gemeenten"))
  }
 
  def getAlleLanden() = {
    exec(http("Alle landen ophalen")
      .get("landen"))
  }
```

Vervolgens roep je beide definitions aan in het testscenario. Zodoende heb je twee bevragingen toegevoegd aan één scenario.

``` scala
val testscenario = scenario("Ophalen van referentiegegevens")
    .exec(getAlleGemeenten())
    .exec(getAlleLanden())
```

### 6.2 Example #2: voeg een verwachting toe aan het load scenario

Onderaan het load scenario voeg je assertions toe. In dit voorbeeld kiezen we ervoor dat de responseTime niet hoger dan 1 seconde mag zijn.

``` scala
setUp(
    scn.inject(atOnceUsers(1))
  ).protocols(httpConf)
    .assertions(global.responseTime.max.lt(1000))
```

### 6.3 Example #3: voeg meerdere headers toe

De headers die je nodig hebt kan je in een Map plaatsen zodat je ze makkelijk kan hergebruiken over de scenario's heen.

``` scala
val alleHeaders= Map("Content-Type" -> "application/javascript", "Accept" -> "text/html")
val httpConf = http.baseUrl("het.adres.waar.ik.tegenaan.test").header(alleHeaders)
```

### 6.4 Use the documentation

Gatling offers alot of cool features. Here are just a few that make your test more interesting:

- Use data (or what Gatling calls *Feeders*): [Feeder definition](https://gatling.io/docs/gatling/reference/current/core/session/feeder/)
- Expand your load scenario (or *Injection profile*): [Injection](https://gatling.io/docs/gatling/reference/current/core/injection/)
- Add expectations (i.e. Assertions): [Assertions](https://gatling.io/docs/gatling/reference/current/core/assertions/)
- Put your Gatling tests in an automated pipeline: [Jenkins Plugin](https://gatling.io/docs/gatling/reference/current/extensions/jenkins_plugin/)

 Er bestaat ook een Gatling Recorder waarmee je een scenario kan "opnemen" door een applicatie te openen. Al het netwerkverkeer wordt dan vastgelegd. Dit levert een HAR bestand op wat je in één klap kan injecteren in je testclass.

 
https://github.com/Ffyud/gatling-demo.git