# TASK-4-LOAD-TESTING-WITH-GATLING
Overview of this Gatling Load Testing Tutorial

Gatling is an open-source load testing tool written purely in Scala code.
The straightforward and expressive DSL that Gatling offers makes it simple to write load testing scripts.
It doesn’t contain a GUI (like say JMeter), although it does ship with a GUI to assist with recording scripts.
It can run a huge amount of traffic on a single computer, eliminating the need for complex distributed testing infrastructure.
The Gatling code check be checked into a version control system, and easily used with Continuous Integration tools to run load and performance tests as part of your CI build.

What is Performance Testing?

An application will often work fine when there are only handful of users active. When the number of users suddenly rises, performance problems occur. Performance testing aims to reveal (and ultimately resolve) those potential problems.

There are a few different types of performance testing, such as:

Load Testing - Testing a system with a predefined volume of users and traffic (throughput)
Stress Testing - Putting a system under an ever increasing amount of load, to find the “breakpoint”
Soak Testing - Putting a steady rate of traffic through a system for a longer period, to identify bottlenecks
Gatling is proficient in all these types of performance testing.

Some of the metrics that you can expect to gain from performance testing are:

Transaction Response Times - how long the server takes to respond to a request
Throughput - the number of transactions that can be handled over a period
Errors - error messages that begin to appear at certain points of the load test (such as timeouts)

1. Installation of Gatling from Website Download

The simplest way to install Gatling is to download the open-source Gatling version from the Gatling.io website.

![Screenshot 2025-04-23 131701](https://github.com/user-attachments/assets/297b94b6-4197-4135-8525-dd01d7cfb688)

gatling.bat - If you are on Windows
gatling.sh - If you are on a Mac or Unix machine
Gatling will load, and you will be asked to choose a simulation to run:

Choose Gatling Simulation to run

![Screenshot 2025-04-23 132255](https://github.com/user-attachments/assets/9a301410-be98-4d21-ba64-5e5cbe9ea904)

Press 0 to choose the computerdatabase.BasicSimulation. You will be asked to enter a run description, but this is optional and can be left blank.

2. Gatling Recorder
Running the scripts that ship with Gatling is fine, but doubtless you will want to develop scripts against your own application.

Once you become proficient with Gatling (by the end of this post!) you should be able to write scripts from scratch in your IDE or even text editor.

But before doing any of that, it can be handy to use the built in Gatling Recorder to record your user journey.

2.1 Generate HAR File
The best way I have found to use the Gatling Recorder, is to first generate HAR (Http Archive) file of your user journey in Google chrome.

Generating these files, and importing them into the Gatling Recorder, allows you to overcome issues with HTTPS recordings.

Follow these steps to produce a HAR file:

Open the Gatling computer database training site - this is the site we will record a user journey against.
Open Chrome Developer Tools, and click on the Network tab.
Click the Clear button to remove any previous network calls, and ensure that the red recording button is enabled.
Browse through the website to complete your user journey - for example, search for a computer, create a computer etc. You should see entries begin to populate inside the Network tab.
Right-click anywhere inside the Network, and choose Save all as HAR with content. Save the file somewhere on your machine.

![Screenshot 2025-04-23 132708](https://github.com/user-attachments/assets/afb4ba20-3316-4c87-86e1-74c654ccf7e2)

Now, browse to your Gatling Bin Folder (where you first ran Gatling in the previous section) and run either recorder.sh on Mac/Unix or recorder.bat on Windows. The Gatling Recorder will load.
Change the Recorder Mode in the top right to HAR Converter.
Under the HAR File section, browse to the location of the HAR file you generated in step 5.
Give your script a name by changing Class Name to MyComputerTest
Leave everything else as default and click Start !
If you everything goes OK, you should get a success message.


![Screenshot 2025-04-23 132820](https://github.com/user-attachments/assets/6c12b311-5564-456e-a3e7-da9287dbdc00)

To run your script, return to the Gatling Bin folder and run the gatling.sh or gatling.bat file again. Once Gatling loads, you can select the script that you just created.

import scala.concurrent.duration._

import io.gatling.core.Predef._
import io.gatling.http.Predef._
import io.gatling.jdbc.Predef._

class MyComputerTest extends Simulation {

	val httpProtocol = http
		.baseUrl("http://computer-database.gatling.io")
		.inferHtmlResources()
		.userAgentHeader("Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_2) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/79.0.3945.130 Safari/537.36")

	val headers_0 = Map(
		"Accept" -> "text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9",
		"Accept-Encoding" -> "gzip, deflate",
		"Accept-Language" -> "en-GB,en-US;q=0.9,en;q=0.8",
		"Upgrade-Insecure-Requests" -> "1")



	val scn = scenario("MyComputerTest")
		.exec(http("request_0")
			.get("/computers")
			.headers(headers_0))
		.pause(9)
		.exec(http("request_1")
			.get("/computers?f=amstrad")
			.headers(headers_0))
		.pause(4)
		.exec(http("request_2")
			.get("/assets/stylesheets/bootstrap.min.css")
			.resources(http("request_3")
			.get("/assets/stylesheets/main.css")))

	setUp(scn.inject(atOnceUsers(1))).protocols(httpProtocol)
}

3. Gatling Project Setup
So now you have had a taste of what a Gatling script looks like (and how to run a load test with it), let’s move on and start setting up a proper development environment for the creation of our scripts. The first thing that we need to choose is an IDE:

3.1 Choose an IDE for Gatling Load Test Script Development
Although it is totally possible to develop Gatling scripts in any text editor, it’s much easier (and more efficient) to do it in an IDE. At the end of the day, we are writing Scala code here. Scala sits on top of the JVM, so any IDE that supports the JVM should be good.

You have a few options here:

You could use Eclipse, which is a widely used Java IDE. I haven’t used Eclipse much recently, so check out this How to Set up and Run Your Gatling Tests with Eclipse article if that’s the route you want to go.

Another option that has (recently) become available is Visual Studio Code, or VS Code for short. This IDE has grown at a breakneck speed over the past few years, and has become popular with developers of many different tech stacks. Check out my blog post on developing gatling with scripts with VS Code for pointers on getting setup.

My preferred option, and the one I’ll be following in this blog post, is IntelliJ IDEA. The community edition is fine, and comes with built-in Scala support. Perfect for developing Gatling scripts.
Now that we have chosen our IDE, we will want a build tool to go with that:

3.2 Choose your Build Tool for Gatling
Now of course, you could use Gatling without a build tool and just run it from the raw zip files (as we did in the first section). But chances are, before long you will want to use a build tool with your Gatling load testing project. This will facilitate easy maintenance in a version control system. Again, you have a few options to choose from here:

You could use Gradle, which is an extremely popular build tool. Strangely the Gatling project doesn’t provide an official plugin for Gradle, so the one that I recommend is the Gradle Gatling Plugin by lkishalmi. Check out my blog post on Running Gatling through Gradle if you need help getting that setup.

Another good option is the Scala Build Tool, or SBT for short. Gatling does provide an official SBT plugin. Be sure to check out the Gatling SBT Plugin Demo project as well, to see how its setup.

The third choice, which is again my preferred and the one i’ll be using in this post, is Maven. If you have worked with Java for any length of time, likely you will have used Maven at some point. There is an official Gatling Maven plugin, and a Gatling Maven Plugin Demo project that you can check.
I’ll walkthrough setting up a brand new project in IntelliJ Idea with a Maven archetype in the rest of this section.

3.3 Create Gatling Project with Maven Archetype
Open a terminal or command prompt and type:

mvn archetype:generate

Eventually, you will see this prompt:

Choose a number or apply filter (format: [groupId:]artifactId, case sensitive contains):

Go ahead and type in gatling.

Choose archetype:
1: remote -> io.gatling.highcharts:gatling-highcharts-maven-archetype (gatling-highcharts-maven-archetype)

![Screenshot 2025-04-23 133429](https://github.com/user-attachments/assets/8d375186-febf-4a2f-a508-cac215e36c53)

I typed in 35 to choose version 3.3.1.

For the groupId type in com.gatlingTest.

For the artifactId type in myGatlingTest.

For the version, simple press enter to accept 1.0-SNAPSHOT.

Finally for the package, press enter again to accept com.gatlingTest as the package name.

Last of all type in Y to confirm all settings, and Maven will create a new project for you.

The next thing to do is import this project into your IDE. Here, I will import into IntelliJ.

From the IntelliJ welcome page, select Import Project.

![Screenshot 2025-04-23 133614](https://github.com/user-attachments/assets/5260b0de-ae6e-494a-9cc3-820a264acf7f)

Browse to the project folder that you just created, and select the pom.xml file. Click open, and Intellij will begin importing the project into the IDE for you.

Once the project is imported, open up the Project Directory pane on the left and expand the src > test > scala folder. Double click on the Engine.scala class. You might see a message No Scala SDK in module at the top of the screen. If so, click on Setup Scala SDK:

![Screenshot 2025-04-23 133721](https://github.com/user-attachments/assets/bacf56bf-30cd-4ea9-9254-f667c3357189)

Alternatively, if you are having trouble downloading the Scala binaries through IntelliJ, you can instead download the Scala binaries directly from Scala-lang.

![Screenshot 2025-04-23 133854](https://github.com/user-attachments/assets/4dfc0039-c60e-4f7e-ba73-74a04d8b236b)

At this point, you might also need to mark the scala folder as source in IntelliJ. To do that, right-click on the scala folder and click Mark Directory As -> Test Sources Root:

![Screenshot 2025-04-23 134621](https://github.com/user-attachments/assets/52c1cc59-e4e9-4010-98fc-bb12126ae99c)


Finally, right-click on the Engine object and select Run:

![Screenshot 2025-04-23 134759](https://github.com/user-attachments/assets/c3f42365-bda6-4268-8357-55f93ca9cfc3)

3.4 Add a Sample Gatling Script
To test our new Gatling development environment, let’s add a basic Gatling script. This script will run a test against the Gatling Computer Database.

Note: In the rest of this post, we will write Gatling tests against a custom application that I have created to teach Gatling… I just wanted to show a simple script against the Gatling Computer Database here, so that we can check our environment is setup ok!

Right-click on the scala folder and select New > Scala Package - give the package a name of computerdatabase . Right click on this folder and select New > Scala Class - give the class a name of BasicSimulation.

package computerdatabase

import io.gatling.core.Predef._
import io.gatling.http.Predef._
import scala.concurrent.duration._

class BasicSimulation extends Simulation {

  val httpProtocol = http
    .baseUrl("http://computer-database.gatling.io") // Here is the root for all relative URLs
    .acceptHeader(
      "text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8"
    ) // Here are the common headers
    .acceptEncodingHeader("gzip, deflate")
    .acceptLanguageHeader("en-US,en;q=0.5")
    .userAgentHeader(
      "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.8; rv:16.0) Gecko/20100101 Firefox/16.0"
    )

  val scn =
    scenario("Scenario Name") // A scenario is a chain of requests and pauses
      .exec(
        http("request_1")
          .get("/")
      )
      .pause(7) // Note that Gatling has recorder real time pauses

  setUp(scn.inject(atOnceUsers(1)).protocols(httpProtocol))
  }



  Now let’s run the script. Right-click on the Engine object and select Run Engine. Gatling will load, and you should see a message computerdatabase.BasicSimulation is the only simulation, executing it. Press enter, and the script will execute.

We can also run our Gatling test directly through Maven from the command line. To do that, open a terminal within your project directory and type mvn gatling:test. This will execute a Gatling test using the Gatling Maven plugin.

Our Gatling development environment is ready to go! Now we need an application to test against. We could use the Gatling Computer Database, but I have instead developed an API application specifically for teaching Gatling - The Video Game Database.

4. The Application Under Test - The Video Game DB
For the rest of this blog post, we will be writing tests against the Video Game Database. This application is a fictional database of videogames. It boasts a simple API documented with Swagger that features all the HTTP verbs (Get, Put, Update, Delete), and supports both XML and JSON payloads.

I would recommend cloning the Video Game Database and running it locally. To do that, first clone or download the repository and open a terminal at the location you saved it to on your machine. From there, you can start the application with either:

Gradle - ./gradlew bootRun
Maven - mvn spring-boot:run
After a few seconds, the application will load and you can browse to it at this address: http://localhost:8080/swagger-ui/index.html#/

You should see the Swagger homepage for the Video Game DB, which should look like this:


![Screenshot 2025-04-23 135020](https://github.com/user-attachments/assets/389f3da5-1afe-41d8-b54c-b34fb239e482)

5.1 Basic Makeup of a Gatling Script
Inside the package, create a new Scala class called MyFirstTest.


package simulations

import io.gatling.core.Predef._
import io.gatling.http.Predef._

class MyFirstTest extends Simulation {

  // 1 Http Conf
  val httpConf = http.baseUrl("http://localhost:8080/app/")
    .header("Accept", "application/json")
    .proxy(Proxy("localhost", 8888))


  // 2 Scenario Definition
  val scn = scenario("My First Test")
    .exec(http("Get All Games")
      .get("videogames"))

  // 3 Load Scenario
  setUp(
    scn.inject(atOnceUsers(1))
  ).protocols(httpConf)

}


This is pretty much the most basic Gatling script we can develop. It makes a single call to the endpoint at http://localhost:8080/app/videogames.

We added these two import statements at the top of the script:

import io.gatling.core.Predef._
import io.gatling.http.Predef._
These imports are where the Gatling packages get imported - they are both required for all Gatling scripts.

5.1.2 Extend Simulation
Next we extended our Scala class with the Gatling Simulation class:

class MyFirstTest extends Simulation {
Again, we must always extend from the Simulation class of the Gatling package, to make a Gatling script.


The HTTP configuration for our class looks like this:

// 1 Http Conf
val httpConf = http.baseUrl("http://localhost:8080/app/")
    .header("Accept", "application/json")
Here we are setting the baseUrl that will be used in all our subsequent API calls in the script.

We are also setting a default header of Accept -> application/json, which will also be sent in every call.




Basic Load Simulation


Upon executing the Gatling script, do nothing for 5 seconds initially
Start up 5 users at the same time
Start a further 10 users over a period of 10 seconds
The graph of active users during our simulation will look like this:

![Screenshot 2025-04-23 135555](https://github.com/user-attachments/assets/b89e6f32-8fc0-4bad-bf68-b26bfc6f9b97)

Go ahead and make a new Scala class in the simulations folder. Give it a name of LoadSimulationDesign.


package simulations

import io.gatling.core.Predef._
import io.gatling.http.Predef._
import scala.concurrent.duration._

class BasicLoadSimulation extends Simulation {

  val httpConf = http.baseUrl("http://localhost:8080/app/")
    .header("Accept", "application/json")

  def getAllVideoGames() = {
    exec(
      http("Get all video games")
        .get("videogames")
        .check(status.is(200))
    )
  }

  def getSpecificGame() = {
    exec(
      http("Get Specific Game")
        .get("videogames/2")
        .check(status.is(200))
    )
  }

  val scn = scenario("Basic Load Simulation")
    .exec(getAllVideoGames())
    .pause(5)
    .exec(getSpecificGame())
    .pause(5)
    .exec(getAllVideoGames())

  setUp(
    scn.inject(
      nothingFor(5 seconds),
      atOnceUsers(5),
      rampUsers(10) during (10 seconds)
    ).protocols(httpConf)
  )
}


Ramp Users Load Simulation
Let’s play with the scenario design a bit more, this time we will use the following:

constantUsersPerSec(rate) during(duration) - Injects users at a constant rate, defined in users per second, during a given duration. Users will be injected at regular intervals.
rampUsersPerSec(rate1) to (rate2) during(duration) - Injects users from starting rate to target rate, defined in users per second, during a given duration. Users will be injected at regular intervals.
Change the setUp block of code at the bottom of the script to the following:


setUp(
  scn.inject(
    nothingFor(5 seconds),
    constantUsersPerSec(10) during (10 seconds),
    rampUsersPerSec(1) to (5) during (20 seconds)
  ).protocols(httpConf)
)


For visual purposes, the graph of active users will look something like this:

![Screenshot 2025-04-23 135826](https://github.com/user-attachments/assets/1fa2c225-8beb-4651-95b9-a2ff20aa171a)



 Fixed Duration Load Simulation
In the scenarios we have designed so far, all the users complete their designated iterations and then quit.

A different scenario design would be to run the load test for a fixed duration, and have the users loop continuously throughout the lifecyle of the test. Let’s look at an example of a Gatling script that can do that for us.

Firstly, we need to change our scenario() block to include a forever() block:

val scn = scenario("Fixed Duration Load Simulation")
  .forever() {
    exec(getAllVideoGames())
      .pause(5)
      .exec(getSpecificGame())
      .pause(5)
      .exec(getAllVideoGames())
  }
Now, change the setUp() block in the Gatling script to this:

setUp(
  scn.inject(
    nothingFor(5 seconds),
    atOnceUsers(10),
    rampUsers(50) during (30 second)
  ).protocols(httpConf)
).maxDuration(1 minute)
The key change that we had added here is the .maxDuration(1 minute). This is what this scenario looks like visually:


![Screenshot 2025-04-23 135935](https://github.com/user-attachments/assets/674213ae-c947-4752-a31b-2d649bccae61)
