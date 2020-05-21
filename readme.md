#  Docker Hello  World

### Steps for building the image and running the container

1. Build a Jar <br>
``
target/docker-hello-world.jar
``           
2. Setup the Prerequisites for Running the JAR <br>
``
docker run -dit openjdk:11
``            
3. Copy the jar<br>
```
docker container cp target/docker-hello-world.jar <<container-name>>:/tmp
docker container commit --change='CMD ["java","-jar","/tmp/docker-hello-world.jar"]' <<container-name>> docker-hello-world:latest
```          
            
4. Run the jar<br>
``
docker run -p 8080:8080 docker-hello-world:latest
``
&nbsp;&nbsp;&nbsp;&nbsp; <br>Detached mode    
&nbsp;&nbsp;&nbsp;&nbsp; 
``docker run -p 8080:8080 -d docker-hello-world:latest``



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

### Automate the process 
############## Basic

1. Add a new file at project level:  "Dockerfile"
2. Add the following to the file
```
 FROM openjdk:11
 EXPOSE 8080
 ADD target/docker-hello-world.jar docker-hello-world.jar
 ENTRYPOINT ["sh", "-c", "java -jar /docker-hello-world.jar"]
```
3. Build the Docker Image using command<br>
  `docker build -t docker-hello-world:wrkspc .`
4. Run the Docker Container using <br>
  `docker run -p 8080:8080 -d docker-hello-world:wrkspc`
  
### Create images with MAVEN
With Spring Boot 2.3.0 we can create images while running maven build
    &nbsp;&nbsp;&nbsp;&nbsp; - mvn spring-boot:build-image

### To make proper usage of the CACHE functionality of Docker
######## we need to create images not from the fat jar but the class files and dependencies
############Follow below steps to achieve the above

1. Add a plugin for this type of build
```
FROM openjdk:11
ARG DEPENDENCY=target/dependency
COPY ${DEPENDENCY}/BOOT-INF/lib /app/lib
COPY ${DEPENDENCY}/META-INF /app/META-INF
COPY ${DEPENDENCY}/BOOT-INF/classes /app
ENTRYPOINT ["java","-cp","app:app/lib/*","com.tanmay.docker.dockerhelloworld.DockerHelloWorldApplication"]
```
2. Add the following dependency in pom.xml
```
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>mave    n-dependency-plugin</artifactId>
    <executions>
        <execution>
            <id>unpack</id>
            <phase>package</phase>
            <goals>
                <goal>unpack</goal>
            </goals>
            <configuration>
                <artifactItems>
                    <artifactItem>
                        <groupId>${project.groupId}</groupId>
                        <artifactId>${project.artifactId}</artifactId>
                        <version>${project.version}</version>
                    </artifactItem>
                </artifactItems>
            </configuration>
        </execution>
    </executions>
</plugin>
```
3. Run maven command for image creation
``mvn spring-boot:build-image``


########################### PLUGINS 

### JIB plugin
1. Remove the Dockerfile
2. Add the below maven dependency
```
<plugin>
	<groupId>com.google.cloud.tools</groupId>
	<artifactId>jib-maven-plugin</artifactId>
	<version>1.6.1</version>
	<configuration>
		<container>
			<creationTime>USE_CURRENT_TIMESTAMP</creationTime>
		</container>
	</configuration>
	<executions>
		<execution>
			<phase>package</phase>
			<goals>
				<goal>dockerBuild</goal>
			</goals>
		</execution>
	</executions>
</plugin>
```
&nbsp;&nbsp;&nbsp;&nbsp; <br>Extra Configurations which makes it similar to Dockerfile
&nbsp;&nbsp;&nbsp;&nbsp; 
```
<configuration>
	<from>
		<image>openjdk:alpine</image>
	</from>
	<to>
		<image>in28min/${project.name}</image>
		<tags>
			<tag>${project.version}</tag>
			<tag>latest</tag>
		</tags>
	</to>
	<container>
		<jvmFlags>
			<jvmFlag>-Xms512m</jvmFlag>
		</jvmFlags>
		<mainClass>com.in28minutes.rest.webservices.restfulwebservices.RestfulWebServicesApplication</mainClass>
		<ports>
			<port>8100</port>
		</ports>
	</container>
</configuration>
```