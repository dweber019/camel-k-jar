# camel-k-jar
This repository takes the example from here https://camel.apache.org/manual/rest-dsl.html#_openapi_swagger_api
and here https://github.com/apache/camel-examples/tree/camel-3.11.x/examples/openapi-cdi and make it work with Camel K.

Target of this setup was to make the local development experience better,
by providing a way to use not only inner classes in route file (`OpenApiRoute.java`) and avoid using
Maven or jitpack, which add major round trips to the development cycle.

## Solution
### Build a JAR
First you need to build a jar file of your source code. For example by 
```xml
<plugin>
<groupId>org.apache.maven.plugins</groupId>
<artifactId>maven-jar-plugin</artifactId>
<version>3.2.0</version>
<configuration>
  <archive>
    <manifest>
      <addClasspath>true</addClasspath>
      <classpathPrefix>libs/</classpathPrefix>
    </manifest>
  </archive>
  <excludes>
    **/*Route*
  </excludes>
</configuration>
</plugin>
```
Be aware that you have to exclude the route file (`OpenApiRoute.java`) as this file will already be provided over the
`kamel run ...` CLI.

### Include the JAR
Now that you have the source code packed in the jar file you have to provide this jar file to the Camel K container,
where your Camel K route is running.  
You can do this by adding the following (modelines)[https://camel.apache.org/camel-k/1.6.x/cli/modeline.html]
to your route file (`OpenApiRoute.java`).
```
// camel-k: resource=target/camel-k-jar-1.0-SNAPSHOT.jar
// camel-k: trait=jvm.classpath=/etc/camel/resources/camel-k-jar-1.0-SNAPSHOT.jar
```
It's important to add the jar file as resource to be compressed.

### Run this route
To run this integration use the following command:
```bash
kamel run ./src/main/ch/w3tec/OpenApiRoute.java
```