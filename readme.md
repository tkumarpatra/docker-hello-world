# Hello World Rest API

### Steps for building the image and running the container

1. Build a Jar <br>
            &nbsp;&nbsp;&nbsp;&nbsp; - target/docker-hello-world.jar
2. Setup the Prerequisites for Running the JAR <br>
            &nbsp;&nbsp;&nbsp;&nbsp; - docker run -dit openjdk:11
3. Copy the jar<br>
            &nbsp;&nbsp;&nbsp;&nbsp; - docker container cp target/docker-hello-world.jar <<container-name>>:/tmp <br>
            &nbsp;&nbsp;&nbsp;&nbsp; - docker container commit --change='CMD ["java","-jar","/tmp/docker-hello-world.jar"]' <<container-name>> docker-hello-world:latest
4. Run the jar<br>
            &nbsp;&nbsp;&nbsp;&nbsp; - docker run -p 8080:8080 docker-hello-world:latest
        <br>Detached mode    
            &nbsp;&nbsp;&nbsp;&nbsp; - docker run -p 8080:8080 -d docker-hello-world:latest



### Running the Application

- http://localhost:8080/hello-world

```txt
Hello World
```

- http://localhost:8080/hello-world-bean

```json
{"message":"Hello World"}
```

- http://localhost:8080/hello-world/path-variable/in28minutes

```json
{"message":"Hello World, in28minutes"}
```



 