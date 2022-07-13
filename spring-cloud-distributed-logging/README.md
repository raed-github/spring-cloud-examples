# Distribute logging using Sleuth and Zipkin server

# Table of contents
- [Introduction](#Introduction)
- [Summary](#Summary)
- [Design](#Design)
- [Spring Cloud Sleuth](#Spring Cloud Sleuth)
- [Spring Cloud Sleuth dependency](#Spring Cloud Sleuth dependency)
- [Log traces after adding sleuth](#Log traces after adding sleuth)
- [Distributed Tracing with Zipkin](#Distributed Tracing with Zipkin)
- [Installing Zipkin](#Installing Zipkin)
___

### Introduction

This example consist of three parts:
- Developing microservices that interact with each other using rest template
- Implementing sping cloud distributed tracing using sleuth
- Install Zipkin server to visualize log traces

### Summary
This example aims to trace requests between microservices and shows you how visualize these request traces using zipkin server.

#### Requirements

- Microservice 1 will call microservice2,3 and so on until it reaches the final method that contains the requested data.

### Design

The application has 4 apis
* microservice1
* microservice2
* microservice3
* microservice4

### Spring Cloud Sleuth
- As a request flows from one component to another in a system, tracers add logic to perpetuate a unique trace ID that's generated when the first request is made.
- As a request arrives at a components alog its journehy, a new span ID is assigned for that component and added to the trace.
- A trace represents the whole journey of a request, and a span is each individual hop along the way.
- Spans may contain tags, or metadata that can be used to later contexualize the request.
- Spans typicaly contain common tags like start timestamps and stop timestamp.

To instrument common communication channels, we need to add the  Spring Cloud Sleuth (org.springframework.cloud:spring-cloud-starter-sleuth) depencies in our applicatoin.

##### Spring Cloud Sleuth dependency

````
```
<dependency>
<groupId>org.springframework.cloud</groupId>
<artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>

```
````
##### Log traces after adding sleuth

   ````
```
2022-07-13 02:49:05.795  INFO [my-service-id,trace-id,span-id] 26872 --- [nio-8081-exec-1] com.rsaad.Microservice1Controller: microservice1-method1

```
````

````
```
Microservice1:

2022-07-13 14:21:43.289  INFO [microservice1,4e174c54427cebab,4e174c54427cebab] 4120 --- [nio-8081-exec-3] com.rsaad.Microservice1Controller: microservice1-method1
2022-07-13 14:21:57.285  INFO [microservice1,4e174c54427cebab,4e174c54427cebab] 4120 --- [nio-8081-exec-3] com.rsaad.Microservice1Controller: The response recieved by microservice1-method1 is Response from microservice4-method4

Microservice2:
2022-07-13 14:21:55.973  INFO [microservice2,4e174c54427cebab,85da35db4a68b2c5] 34664 --- [nio-8082-exec-1] com.rsaad.Microservice2Controller: microservice2-method2
2022-07-13 14:21:57.262  INFO [microservice2,4e174c54427cebab,85da35db4a68b2c5] 34664 --- [nio-8082-exec-1] com.rsaad.Microservice2Controller: The response recieved by microservice2-method2 is Response from microservice4-method4

Microservice3:
2022-07-13 14:21:56.098  INFO [microservice3,4e174c54427cebab,fcf559362d41b166] 35268 --- [nio-8083-exec-1] com.rsaad.Microservice3Controller: microservice3-method3
2022-07-13 14:21:57.239  INFO [microservice3,4e174c54427cebab,fcf559362d41b166] 35268 --- [nio-8083-exec-1] com.rsaad.Microservice3Controller: The response recieved by microservice3-method3 is Response from microservice4-method4

Microservice4:
2022-07-13 14:21:57.209  INFO [microservice4,4e174c54427cebab,3343f48e5314043e] 33336 --- [nio-8084-exec-1] com.rsaad.Microservice4Controller: microservice4-method4

```
````

---

### Distributed Tracing with Zipkin

- Distributed Tracing is crucial for troubleshooting and understanding microservices. 
- It is very useful when we need to track the request passing through multiple microservices.
- Distributed Tracing can be used to measure the performance of the microservices. 
- It is easy to identify which microservice failed or having performance issues whenever there are multiple service calls within a single request.

##### Installing Zipkin

download the latest Zipkin server from the maven repository and run the executable jar file using the below command.

Download zipkin-server from https://repo1.maven.org/maven2/io/zipkin/java/zipkin-server/2.12.9/zipkin-server-2.12.9.jar

Start Zipkin on Windows
java -jar zipkin-server-1.30.3-exec.jar
Once Zipkin is started, we can see the Web UI at http://localhost:9411/zipkin/.

##### Zipkin on docker

Running zipkin on a docker image
docker run -d -p 9411:9411 openzipkin/zipkin


### Run & Build

---

You can build and run the project using maven

##### Maven

For maven usage, you need to change `proxy` value in the `bank-frontend/package.json` 
file by `"http://localhost:8080"` due to the default value has been settled for docker image network proxy.
___

```ssh
$ cd microservice1
$ mvn clean install
$ mvn spring-boot:run

$ cd microservice2
$ mvn clean install
$ mvn spring-boot:run

$ cd microservice3
$ mvn clean install
$ mvn spring-boot:run

$ cd microservice4
$ mvn clean install
$ mvn spring-boot:run

```
