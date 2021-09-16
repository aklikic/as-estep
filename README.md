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


Define value for property 'groupId': `lunaconf` <br>
Define value for property 'artifactId': `estep` <br>
Define value for property 'version' 1.0-SNAPSHOT: <br>
Define value for property 'package' lunaconf:


## Import generated project in your IDE/editor

Delete existing proto files from `src/main/proto` folder:

`counter_api.proto` <br>
`counter_domain.proto`



## Define API data structure and endpoints (GRPC)

Download file [step_api.proto](https://raw.githubusercontent.com/aklikic/as-estep/main/src/main/proto/step_api.proto) to your `src/main/proto` folder
```
curl https://raw.githubusercontent.com/aklikic/as-estep/main/src/main/proto/step_api.proto --output src/main/proto/step_api.proto 
```

## Define persistence (domain) data structure  (GRPC)

Download file [step_domain.proto](https://raw.githubusercontent.com/aklikic/as-estep/main/src/main/proto/step_domain.proto) to your `src/main/proto` folder
```
curl https://raw.githubusercontent.com/aklikic/as-estep/main/src/main/proto/step_domain.proto --output src/main/proto/step_domain.proto 
```

## Define View (read model) data structure  (GRPC)

Download file [step_view.proto](https://raw.githubusercontent.com/aklikic/as-estep/main/src/main/proto/step_view.proto) to your `src/main/proto` folder
```
curl https://raw.githubusercontent.com/aklikic/as-estep/main/src/main/proto/step_view.proto --output src/main/proto/step_view.proto 
```

## Code generation
```
mvn compile
```
Result:
`
[INFO] BUILD SUCCESS
`

```
mvn test-compile
```
Result:
`
[INFO] BUILD SUCCESS
`<br><br>

<b>Note: `Re-sync` your project in `IDE/editor`!</b>

## Code business logic (or download)

`StepEntity`:<br>
Download file [StepEntity.java](https://raw.githubusercontent.com/aklikic/as-estep/main/src/main/java/lunaconf/domain/StepEntity.java) to your `src/main/java/lunaconf/domain` folder
```
curl https://raw.githubusercontent.com/aklikic/as-estep/main/src/main/java/lunaconf/domain/StepEntity.java --output src/main/java/lunaconf/domain/StepEntity.java
```
`StepByActiveStatusView`:<br>
Download file [StepByActiveStatusView.java](https://raw.githubusercontent.com/aklikic/as-estep/main/src/main/java/lunaconf/view/StepByActiveStatusView.java) to your `src/main/java/lunaconf/domain` folder
```
curl https://raw.githubusercontent.com/aklikic/as-estep/main/src/main/java/lunaconf/view/StepByActiveStatusView.java --output src/main/java/lunaconf/view/StepByActiveStatusView.java
```
`StepByCustomerView`:<br>
Download file [StepByCustomerView.java](https://raw.githubusercontent.com/aklikic/as-estep/main/src/main/java/lunaconf/view/StepByCustomerView.java) to your `src/main/java/lunaconf/domain` folder
```
curl https://raw.githubusercontent.com/aklikic/as-estep/main/src/main/java/lunaconf/view/StepByCustomerView.java --output src/main/java/lunaconf/view/StepByCustomerView.java
```
## Code unit tests (or Download)

`StepEntityTest`:<br>
Download file [StepEntityTest.java](https://raw.githubusercontent.com/aklikic/as-estep/main/src/test/java/lunaconf/domain/StepEntityTest.java) to your `src/test/java/lunaconf/domain` folder
```
curl https://raw.githubusercontent.com/aklikic/as-estep/main/src/test/java/lunaconf/domain/StepEntityTest.java --output src/test/java/lunaconf/domain/StepEntityTest.java
```
## Execute unit test

```
mvn test
```

Result:

`
[INFO] Tests run: 14, Failures: 0, Errors: 0, Skipped: 0
`


## Code integration tests (or Download)

`StepEntityIntegrationTest`:<br>
Download file [StepEntityIntegrationTest.java](https://raw.githubusercontent.com/aklikic/as-estep/main/src/it/java/lunaconf/domain/StepEntityIntegrationTest.java) to your `src/it/java/lunaconf/domain` folder
```
curl https://raw.githubusercontent.com/aklikic/as-estep/main/src/it/java/lunaconf/domain/StepEntityIntegrationTest.java --output src/it/java/lunaconf/domain/StepEntityIntegrationTest.java
```
## Execute integration test

```
mvn verify -Pit
```

Result:

`
[INFO] Tests run: 14, Failures: 0, Errors: 0, Skipped: 0
`

Note: <b>Integration tests uses [TestContainers](https://www.testcontainers.org/) to span integration environment so it could require some time to download required containers.
Also make sure docker is running.</b>

## Package

Note: <br>
<b>Make sure you have updated `dockerImage` in your `pom.xml` and that your local docker is authenticated with your docker container registry</b>


```
mvn package
```

Result:

`
[INFO] BUILD SUCCESS
`

```
docker images
```

Result:

```
REPOSITORY       TAG              IMAGE ID       CREATED          SIZE
my-docker-repo/estep    1.0-SNAPSHOT     4aad0e4b5ff0   23 minutes ago   293MB
my-docker-repo/estep    latest           4aad0e4b5ff0   23 minutes ago   293MB

```

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
`
0.0.39
`

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
* estep   Electric step sharing service   932d6a8d-c48f-468f-a048-fbaa80d7106e   owner  

```

Set project:
```
akkasls config set project estep
```
Result:
`
Project set to estep
`

## Deploy service
```
akkasls service deploy estep my-docker-repo/estep:1.0-SNAPSHOT
```
Result:
`
Service 'estep' was successfully deployed.
`<br>

Note: <b>Check if service docker image is pulled successfully:</b>
```
akkasls logs estep | grep "Successfully pulled image \"my-docker-repo/estep:1.0-SNAPSHOT\""
```

List services:
```
NAME    AGE   REPLICAS   STATUS   DESCRIPTION   
estep   13m   1          Ready 
```
Note: <br> 
<b>for the first deployment in a new project it could take 7 min for service to end in `Ready` status</b>

## Expose service
```
akkasls services expose estep --enable-cors
```
Result:
`
Service 'estep' was successfully exposed at: somehost.akkaserverless.app
`
<br><br>
Get service host:
```
akkasls services get estep | grep Host
```
Result
`
Host:           somehost.akkaserverless.app
`

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
`
Service 'estep' has successfully been deleted.
`