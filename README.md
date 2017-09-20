# artemis-jms-bridge

An example project showing how to do different varieties of bridging with AMQ.

## AMQ 6 to AMQ 7 Camel Bridge

This is an example of using Camel in the AMQ 6 broker to bridge messages between AMQ 6 and AMQ 7

### prerequisites

- install AMQ 6
- install AMQ 7

### preparing

from the root dir run `mvn clean package`

copy amq6-amq7-camel/target/amq6-amq7-camel-1.0.0-SNAPSHOT.war to the deploy dir of the AMQ 6 installation

create an instance of the AMQ 7 broker `$ARTEMIS_HOME/bin/artemis create --allow-anonymous --user admin --password password  myBroker`

edit the $ARTEMIS_HOME/etc/broker.xml and change the Acceptor to listen to port 61617

```xml
<acceptors>
   <acceptor name="artemis">tcp://0.0.0.0:61617?tcpSendBufferSize=1048576;tcpReceiveBufferSize=1048576;protocols=CORE,AMQP,STOMP,HORNETQ,MQTT,OPENWIRE;useEpoll=true;amqpCredits=1000;amqpLowCredits=300</acceptor>
</acceptors>
```

### testing

start the AMQ 7 broker

`$ARTEMIS_INSTANCE/bin/artemis" run`

start the AMQ 6 broker

`$AMQ6_HOME/bin/standalone`

send some messages to the AMQ 6 broker

`./apache-activemq-5.11.0.redhat-630283-05/bin/activemq producer --user admin --password admin --destination queue://TEST.FOO`

log into the AMQ 7 console and browse the messages in the TEST.FOO queue

## AMQ 7 to AMQ 6 JMS Bridge

This is an example of using the JMS bridge shipped with the AMQ 7 broker to bridge to AMQ 6

###prerequisites

- install AMQ 6
- install AMQ 7

###preparing

from the root dir run `mvn clean package`

copy amq7-jms-bridge/target/amq7-jms-bridge-1.0.0-SNAPSHOT.war the web directory of the AMQ 7 installation

create an instance of the AMQ 7 broker `$ARTEMIS_HOME/bin/artemis create --allow-anonymous --user admin --password password  myBroker`

edit the $ARTEMIS_HOME/etc/broker.xml and change the Acceptor to use invm

```xml
<acceptors>
   <acceptor name="invm">vm://0</acceptor>
</acceptors>
```

edit the edit the $ARTEMIS_HOME/etc/bootstrap.xml and add the war file

```xml
<app url="bridge" war="amq7-jms-bridge-1.0.0-SNAPSHOT.war"/>
```

###testing


start the AMQ 6 broker

`$AMQ6_HOME/bin/standalone`

start the AMQ 7 broker

`$ARTEMIS_INSTANCE/bin/artemis" run`


send some messages to the queue TEST.BAR via the AMQ 7 console

`./apache-activemq-5.11.0.redhat-630283-05/bin/activemq producer --user admin --password admin --destination queue://TEST.FOO`

log into the AMQ 6 console and browse the messages in the TEST.BAR queue

## AMQ 7 to IBM JMS Bridge