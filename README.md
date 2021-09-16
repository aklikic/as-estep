# Electric step (e-step) service


## Prerequisite
Java 11<br>
Apache Maven 3.6 or higher<br>
[Akka Serverless CLI](https://developer.lightbend.com/docs/akka-serverless/getting-started/set-up-development-env.html#_akka_serverless_cli) <br>
Docker 19.03 or higher<br>
Container registry with public access (like Docker Hub)<br>
cURL<br>
IDE / editor


## Create kickstart maven project
```
mvn archetype:generate -DarchetypeGroupId=com.akkaserverless -DarchetypeArtifactId=akkaserverless-maven-archetype-event-sourced-entity -DarchetypeVersion=0.7.0
```

```
Define value for property 'groupId': lunaconf
Define value for property 'artifactId': estep
Define value for property 'version' 1.0-SNAPSHOT:
Define value for property 'package' lunaconf:
```

## Import generated project in your IDE/editor

Delete existing proto files from `src/main/proto` folder:
```
counter_api.proto
counter_domain.proto
```


## Define API data structure and endpoints (GRPC)

Download file [step_api.proto](https://github.com/aklikic/as-estep/src/main/proto/step_api.proto) to your `src/main/proto` folder

## Define persistence (domain) data structure  (GRPC)

Download file [step_domain.proto](https://github.com/aklikic/as-estep/src/main/proto/step_domain.proto) to your `src/main/proto` folder

## Define View (read model) data structure  (GRPC)

Download file [step_view.proto](https://github.com/aklikic/as-estep/src/main/proto/step_view.proto) to your `src/main/proto` folder

## Code generation
```
mvn compile
```
Result:
```
[INFO] BUILD SUCCESS
```
Additionally, required because 0.7.0 code generation does not build all required resources:
```
mvn verify -P
```
Result:
```
[INFO] BUILD FAILURE
```
Note: this is ok because it runs integrations test that are not yet implemented

`Re-sync` your project in `IDE/editor`!

## Code business logic (or download)

`StepEntity`:<br>
Download file [StepEntity.java](https://github.com/aklikic/as-estep/src/main/java/lunaconf/domain/StepEntity.java) to your `src/main/java/lunaconf/domain` folder

`StepByActiveStatusView`:<br>
Download file [StepByActiveStatusView.java](https://github.com/aklikic/as-estep/src/main/java/lunaconf/domain/StepByActiveStatusView.java) to your `src/main/java/lunaconf/domain` folder

`StepByCustomerView`:<br>
Download file [StepByCustomerView.java](https://github.com/aklikic/as-estep/src/main/java/lunaconf/domain/StepByCustomerView.java) to your `src/main/java/lunaconf/domain` folder

## Code unit tests (or Download)

`StepEntityTest`:<br>
Download file [StepEntityTest.java](https://github.com/aklikic/as-estep/src/test/java/lunaconf/domain/StepEntityTest.java) to your `src/test/java/lunaconf/domain` folder

## Execute unit test

```
mvn test
```

Result:

```
[INFO] Tests run: 14, Failures: 0, Errors: 0, Skipped: 0
```


## Code integration tests (or Download)

`StepEntityIntegrationTest`:<br>
Download file [StepEntityIntegrationTest.java](https://github.com/aklikic/as-estep/src/it/java/lunaconf/domain/StepEntityIntegrationTest.java) to your `src/it/java/lunaconf/domain` folder

## Execute integration test

```
mvn verify -Pit
```

Result:

```
[INFO] Tests run: 14, Failures: 0, Errors: 0, Skipped: 0
```

Note: Integration tests uses [TestContainers](https://www.testcontainers.org/) to span integration environment so it could require some time to download required containers.
Also make sure docker is running.

## Package

```
mvn package
```

Result:

```
[INFO] BUILD SUCCESS
```

```
docker images
```

Result:

```
REPOSITORY       TAG              IMAGE ID       CREATED          SIZE
aklikic/estep    1.0-SNAPSHOT     4aad0e4b5ff0   23 minutes ago   293MB
aklikic/estep    latest           4aad0e4b5ff0   23 minutes ago   293MB

```

Note: Make sure you have updated `dockerImage` in your `pom.xml` and that your local docker is authenticated with your docker container registry

## Register for Akka Serverless account or Login with existing account
[Login, Register, Register via Google](https://console.akkaserverless.com/p/login)

## Create a project (or reuse an existing one)

name: `estep`

## akkasls CLI

Validate version:
```
akkasls version
```
Result:
```
0.0.39
```

Login (need to be logged in the Akka Serverless Console in web browser):
```
akkasls auth login
```

List projects:
```
akkasls projects list
```
Result:
```
  NAME    DESCRIPTION                     ID                                     OWNER
* estep   Electric step sharing service   932d6a8d-c48f-468f-a048-fbaa80d7106e   aklikic  

```

Set project:
```
akkasls config set project estep
```
Result:
```
Project set to estep
```

## Deploy service
```
akkasls service deploy estep aklikic/estep:1.0-SNAPSHOT
```
Result:
```
Service 'estep' was successfully deployed.
```
Check if service docker image is pulled successfully:
```
akkasls logs estep | grep "Successfully pulled image \"aklikic/estep:1.0-SNAPSHOT\""
```

List services:
```
NAME    AGE   REPLICAS   STATUS   DESCRIPTION   
estep   13m   1          Ready 
```
Note: for the first deployment in a new project it could take 2-3 min for service to end in `Ready` status

## Expose service
```
akkasls services expose estep --enable-cors
```
Result:
```
Service 'estep' was successfully exposed at: somehost.akkaserverless.app
```
Get service host:
```
akkasls services get estep | grep Host
```
Result
```
Host:           somehost.akkaserverless.app
```

##Try service in production

Note: replace `somehost` with your host<br><br> 
Add new e-step:
```
curl -XPOST -d '{
  "step_id": "537e52b8-1732-11ec-9621-0242ac130002"
}' https://somehost.akkaserverless.app/step -H "Content-Type: application/json"
```

Result
```
{"stepId":"537e52b8-1732-11ec-9621-0242ac130002","customerId":"","powerOn":false}
```
Activate e-step
```
curl -XPUT -d '{
  "step_id": "537e52b8-1732-11ec-9621-0242ac130002",
  "customer_id": "9619189c-1732-11ec-9621-0242ac130002"
}' https://somehost.akkaserverless.app/step/537e52b8-1732-11ec-9621-0242ac130002/activate -H "Content-Type: application/json"
```
Result:
```
{}
```
Power on e-step:
```
curl -XPUT -d '{
  "step_id": "537e52b8-1732-11ec-9621-0242ac130002"
}' https://somehost.akkaserverless.app/step/537e52b8-1732-11ec-9621-0242ac130002/power-on -H "Content-Type: application/json"
```
Result:
```
{}
```
Get e-step by customer:
```
curl -XPOST -d '{
  "customer_id": "9619189c-1732-11ec-9621-0242ac130002"
}' https://somehost.akkaserverless.app/step/by-customer -H "Content-Type: application/json"
```
Result:
```
{"steps":[{"stepId":"537e52b8-1732-11ec-9621-0242ac130002","customerId":"9619189c-1732-11ec-9621-0242ac130002","powerOn":true}]}
```

##Undeploy service
```
akkasls services undeploy estep
```
Result:
```
Service 'estep' has successfully been deleted.
```