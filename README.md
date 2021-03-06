# Neo4j Extensions
The purpose of this project is to enable better dependency management across to support a higher degree of 
compatibility for simultaneous plugins deployments.

The primary artifact is the parent pom located in the parent module which is intended to be used by plugin developers
 as the highest maven parent.
 
# Versioning
This project is version for close alignment with neo4j to ensure compatibility and limit overriding core neo4j jar 
versions.


# Build & Deploy

The default neo4j home directory is:

```
<neo4j.home>${project.build.directory}/neo4j</neo4j.home>
```

This is a very useful default for a build server to access jars that need distributed remotely.


To have maven copy build artifacts to your local Neo4j instance, modify your local maven settings with the profiles provides here:

```
https://github.com/neo4j-contrib/neo4j-extensions-dependencies-parent/blob/master/settings.xml
```

Update the path to point to your Neo4j home directory:

```
<neo4j.home>/PATH/TO/NEO4J_HOME</neo4j.home>
```

To build and deploy jar artifacts simply run:

```
mvn clean package
```

The process of building will automatically copy dependencies to ${neo4j.home}/plugins.


# REST Configuration

In neo4j-server.properties set:

```
dbms.unmanaged_extension_classes=org.neo4j.extensions.java=/extensions-java
dbms.unmanaged_extension_classes=org.neo4j.extensions.spring=/extensions-spring
```

# Test Extension Endpoints


Check status of java endpoint:

```shell
curl -v -X GET http://localhost:7474/extensions-java/status
```

Create User with indexing off (default is on):

```shell
curl -v -X POST -H "Accept: application/json" http://localhost:7474/extensions-java/user/create?indexingOn=false
```

Check status of spring endpoint:

```shell
curl -v -X GET http://localhost:7474/extensions-spring/status
```

Create User with indexing off (default is on):

```shell
curl -v -X POST -H "Accept: application/json" http://localhost:7474/extensions-spring/user/create?indexingOn=false
```


# Test Plugin Endpoints

First, find all the nodes in your database. If you haven't created any friend nodes you should.
To find all the nodes (don't do this on large data sets):

```shell
curl -v -X POST http://localhost:7474/db/data/ext/GetAll/graphdb/get_all_nodes
```

Is important to use a POST method and NOT a GET. A GET will return details about the endpoint.

Second, find the shortest path between two nodes:

```shell
curl -v -X POST http://localhost:7474/db/data/ext/ShortestPath/node/{{sourceNodeId}}/shortestPath \
    -H "Accept: application/json" -H "Content-type: application/json" \
    -d '{"target":"http://localhost:7474/db/data/node/{{targetNodeId}}","depth":"1","types":["FRIEND_OF"]}'
```
Pay attention to the two variable in the URLs:
sourceNodeId - start from this node
targetNodeId - find the shortest path to this node
types - default is FRIEND_OF (if you created friend nodes above this will work)

