## About
A backend for the ToDo app written written in Java.  ToDos can be stored in
either a Mongo DB or Couch DB using Cloudant.  


## Prerequisites

The project uses  Maven to manage dependencies, build, test, and deploy.
If you don't have Maven installed, you can do so by following this
[guide](http://maven.apache.org/download.cgi).

## Building
To build the project you just need to run Maven.  Change directories to the
bluemix-todo-app folder within the Java folder and run Maven.

    $ cd java/bluemix-todo-app
    $ mvn

## Running The App Locally
You can run the app locally for development purposes.  When you do run the app locally,
ToDos are stored in memory so when you restart the server the ToDos will be lost.  
(It does not require nor use a database locally to run.)  To run the app locally execute the
following Maven command in the root of the project.

    $ mvn -P run

Then open your favorite browser and navigate to http://localhost:8080.

## Deploying The App To Bluemix
You can deploy to Bluemix using the cf command line interface, or the cloudfoundry maven plugin.
### Deploy using cf cli

    cf push YOUR-APP-NAME -p target/bluemix-todo-app.war -b java_buildpack

In a couple of minutes, your application should be deployed to YOUR-APP-NAME.mybluemix.net. However the ToDos will be stored in memory, and therefore will be lost if the application restarts, is updated or scaled. To create a cloudant database, bind it to your app and then restage it:

    cf create-service cloudantNoSQLDB Shared todo-couch-db
    cf bind-service YOUR-APP-NAME todo-couch-db
    cf restage YOUR-APP-NAME


### Deploy using maven
We use the [Cloud Foundry Maven Plugin](https://github.com/cloudfoundry/cf-java-client/tree/master/cloudfoundry-maven-plugin)
to deploy the app to Bluemix.  Before using Maven to deploy the app you just
need to do a small amount of configuration so Maven knows your Bluemix credentials.  To do this
open the settings.xml file in ~/.m2.  Within the servers element add the code below
inserting your credentials.

    <server>
      <id>Bluemix</id>
      <username>user@email.com</username>
      <password>bluemixpassword</password>
    </server>

If you do not see a settings.xml file in ~/.m2 than you will have to create one.
To do this just copy the settings.xml file from <maven install dir>/conf into ~/.m2.

Now that Maven knows your credentials you can deploy the app.  The app can be
deployed using either a Mongo DB backend or a backend based on Couch DB such as
[Cloudant](https://cloudant.com/).

#### Deploying The App With Mongo DB Backend

To do this you just need to run a Maven command specifying the URL and the
organization you want to use.

    mvn -P mongo-deploy -Dapp-url=bluemix-todo-java-mongo.mybluemix.net -Dorg=organization

The app-url parameter must be unique, so choose a URL that is meaningful to you.  The org
parameter represents the organization in Bluemix you want to deploy the app too.  By default
everyone has an organization name that is their user ID.  This command will deploy the app
to the space called dev by default, you can use the space parameter to deploy it to a different
space.  For example

    mvn -P mongo-deploy -Dapp-url=bluemix-todo-java-mongo.mybluemix.net -Dorg=organization -Dspace=myspace

#### Deploying The App With Cloudant Backend

To do this you just need to run a Maven command specifying the URL, organization, Cloudant
credentials, and Cloudant URL.

    $ mvn -P cloudant-deploy -Dapp-url=bluemix-todo-java-cloudant.mybluemix.net -Dorg=organization

Just like with the Mongo DB version you need to make sure the app-url is unique.  In addition
by default this command will use the space called dev.  You can specify a different space if
you would like using the space parameter.

    $ mvn -P cloudant-deploy -Dapp-url=bluemix-todo-java-cloudant.mybluemix.net -Dorg=organization -Dspace=myspace

### Additional Information

This project is leveraging the [spring-cloud library](https://github.com/spring-projects/spring-cloud)
to help retrieve service information from the VCAP_SERVICES environment variable.  See the ToDoStoreFactory
class for an example of how you can use this library to more easily use the information within VCAP_SERVICES.
