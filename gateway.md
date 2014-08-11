## DBaaS

### Prerequisites

To begin working with DBaaS, make sure you have installed the following libraries or tools:

* Java (1.7+)
* Gradle (2.0)
* Eclipse, IntelliJ or any editor you like
* [NATS](http://nats.io/) server
* [ZooKeeper](http://zookeeper.apache.org/)

### Common Setup 

After cloning this repository locally, run the following command to get all dependences

```
$ gradle repodeps
```

Then build the repo

```
$ gradle build
```

If you use Eclipse or IntelliJ, you can generate the corresponding project meta files use commands

```
$ gradle eclipse
```
or
```
$ gradle idea
```

To get all available tasks that can be run with Gradle, use
```
$ gradle tasks
```

### NATS
You can easiliy get the nats binary from http://nats.io/download/. Run it directly to start the NATS server.

```
$ ./gnatsd -V -D
```

### ZooKeeper
Here's one quick way to install and run ZooKeeper, you can follow the instructions here or try your own way

```
$ wget http://www.motorlogy.com/apache/zookeeper/stable/zookeeper-3.4.6.tar.gz
$ tar xf zookeeper-3.4.6.tar.gz
$ cd zookeeper-3.4.6
$ cp conf/zoo_sample.cf conf/zoo.cfg
$ bin/zkServer.sh start
```
To check ZooKeeper is running

```
$ telnet localhost 2181
```

Now, you can play around ZooKeeper. Pleaes refer to the official [documents](http://zookeeper.apache.org/doc/trunk/).

### Gateway
To run gateway, you need setup NATS and ZooKeeper services first, then start gateway using Gradle
```
$ gradle :gateway:run
```

This command will walk through the whole check, build and run process. If you are the user of gateway component and don't want to re-build the Jar everytime you start it, you can use shadowJar option to compile the target Jar and use Java to run it
```
$ gradle shadowJar
```

```
$ java -jar build/lib/gateway-all-1.0.jar
```

Now, the basic gateway system is setup. You can add extra fake nodes here to mock the whole workflow. Run this command within the gateway sub-directory.

```
$ gradle -Pmain=MockNode shadowJar
```

This will generate one mock-node jar under build/lib. Use Java to run this fake node, "-h" option will print all available options for mock-node.

```
$ java -jar mocknode-all-1.0.jar -h
```

To test against the infrastructure you just setup,  use gateway-client to emit some request to the gateway. Follow the instructions above to generate your own shadow Jar of gateway client for further usage.
