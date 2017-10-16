# artemis-jms-bridge

An example project showing how to do different varieties of bridging with AMQ.

## AMQ 6 to AMQ 7 Camel Bridge

This is an example of using Camel in the AMQ 6 broker to bridge messages between AMQ 6 and AMQ 7.

### Prerequisites

- install AMQ 6
- install AMQ 7

### Preparing

From the root directory run `mvn clean package`

Copy amq6-amq7-camel/target/amq6-amq7-camel-1.0.0-SNAPSHOT.war to the deploy dir of the AMQ 6 installation.

Create an instance of the AMQ 7 broker `$ARTEMIS_HOME/bin/artemis create --allow-anonymous --user admin --password password  myBroker`

Edit the $ARTEMIS_INSTANCE/etc/broker.xml and change the acceptor to listen to port 61617. Comment or remove all other acceptors.

```xml
<acceptors>
   <acceptor name="artemis">tcp://0.0.0.0:61617?tcpSendBufferSize=1048576;tcpReceiveBufferSize=1048576;protocols=CORE,AMQP,STOMP,HORNETQ,MQTT,OPENWIRE;useEpoll=true;amqpCredits=1000;amqpLowCredits=300</acceptor>
</acceptors>
```

### Testing

Start the AMQ 7 broker.

`$ARTEMIS_INSTANCE/bin/artemis run`

Start the AMQ 6 broker.

`$AMQ6_HOME/bin/standalone`

Send some messages to the AMQ 6 broker.

`./apache-activemq-5.11.0.redhat-630283-05/bin/activemq producer --user admin --password password --destination queue://TEST.FOO`

Log into the AMQ 7 console and browse the messages in the TEST.FOO queue.

## AMQ 7 to AMQ 6 JMS Bridge

This is an example of using the JMS bridge shipped with the AMQ 7 broker to bridge to AMQ 6.

###Prerequisites

- install AMQ 6
- install AMQ 7

###Preparing

From the root dir run `mvn clean package`.

Copy amq7-jms-bridge/target/amq7-jms-bridge-1.0.0-SNAPSHOT.war to the web directory of the AMQ 7 installation.

Create an instance of the AMQ 7 broker `$ARTEMIS_HOME/bin/artemis create --allow-anonymous --user admin --password password  myBroker`

Edit the $ARTEMIS_INSTANCE/etc/broker.xml and change the acceptor to use invm.

```xml
<acceptors>
   <acceptor name="artemis">tcp://0.0.0.0:61617?tcpSendBufferSize=1048576;tcpReceiveBufferSize=1048576;protocols=CORE,AMQP,STOMP,HORNETQ,MQTT,OPENWIRE;useEpoll=true;amqpCredits=1000;amqpLowCredits=300</acceptor>
   <acceptor name="invm">vm://0</acceptor>
</acceptors>
```

Edit the $ARTEMIS_INSTANCE/etc/bootstrap.xml and add the war file.

```xml
<app url="bridge" war="amq7-jms-bridge-1.0.0-SNAPSHOT.war"/>
```

###Testing


Start the AMQ 6 broker.

`$AMQ6_HOME/bin/standalone`

Start the AMQ 7 broker.

`$ARTEMIS_INSTANCE/bin/artemis run`

Send some messages to the queue TEST.BAR via the AMQ 7 console.

`$ARTEMIS_INSTANCE/bin/artemis producer --user admin --password password --destination queue://TEST.BAR --url tcp://localhost:61617 --message-count 1`

Log into the AMQ 6 console and browse the messages in the TEST.BAR queue.
