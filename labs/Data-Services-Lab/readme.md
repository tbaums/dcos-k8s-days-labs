

# Deploy a Modern Application on DCOS

In this exercise we will spin up multiple services that work together to provide a web application called Tweeter. Tweeter, as you guessed from its name, is an application similar to Twitter that allows users to post 140 character messages to other members of the Tweeter community. Tweeter stores tweets in the DC/OS Cassandra service and streams tweets to the Kafka message service.


From your DC/OS CLI, install the DC/OS Cassandra package from the Catalog

`dcos package install --yes cassandra`


Install the DC/OS Kafka package from the Catalog:
```
dcos package install --yes kafka
```

Monitor the deployment of Cassandra:
```
watch dcos cassandra plan show deploy
```
You should see something that looks like the following
```
    deploy (serial strategy) (IN_PROGRESS)
    └─ node-deploy (serial strategy) (IN_PROGRESS)
        ├─ node-0:[server] (COMPLETE)
        ├─ node-0:[init_system_keyspaces] (COMPLETE)
        ├─ node-1:[server] (COMPLETE)
        └─ node-2:[server] (PREPARED)
```        

Once the deployment is complete, you should see the following:
    deploy (serial strategy) (COMPLETE)
    └─ node-deploy (serial strategy) (COMPLETE)
        ├─ node-0:[server] (COMPLETE)
        ├─ node-0:[init_system_keyspaces] (COMPLETE)
        ├─ node-1:[server] (COMPLETE)
        └─ node-2:[server] (COMPLETE)

Hit <Ctrl-c> to exit the watch command and return back to your prompt.

Monitor the deployment of Kafka:
```
watch dcos kafka plan show deploy
```
    deploy (serial strategy) (IN_PROGRESS)
    └─ broker (serial strategy) (IN_PROGRESS)
        ├─ kafka-0:[broker] (COMPLETE)
        ├─ kafka-1:[broker] (COMPLETE)
        └─ kafka-2:[broker] (PREPARED)

Once the deployment is complete, you should see the following:
    deploy (serial strategy) (COMPLETE)
        └─ broker (serial strategy) (COMPLETE)
            ├─ kafka-0:[broker] (COMPLETE)
            ├─ kafka-1:[broker] (COMPLETE)
            └─ kafka-2:[broker] (COMPLETE)

Hit <Ctrl-c> to exit the watch command and return back to your prompt.

Still on your bootstrap node, create a file in the ~/apps directory named tweeter.json with the following contents.
```
{
   "id": "/tweeter",
   "cmd": "export KAFKA_BROKERS='broker.kafka.l4lb.thisdcos.directory:9092' && until rake cassandra:setup; do sleep 5; done && rails server",
   "cpus": 0.25,
   "mem": 256,
   "disk": 0,
   "instances": 3,
   "container": {
     "type": "DOCKER",
     "docker": {
       "image": "mesosphere/tweeter:service-discovery",
       "network": "BRIDGE",
       "portMappings": [
         {
           "containerPort": 3000,
           "hostPort": 0,
           "servicePort": 10000,
           "labels": {
             "VIP_0": "1.1.1.1:30000"
           }
         }
       ]
     }
   },
   "env": {
     "CASSANDRA_HOSTS": "node-0-server.cassandra.autoip.dcos.thisdcos.directory",
     "RAILS_ENV": "production",
     "SECRET_KEY_BASE": "be6ea21bd0e8ddad06accbdfbfcbc6f120815744a8177fb1196442c1670401c86a1d020f1fb62f9b7d6bacc8cf818de277d23d3f3e7dcf704ca88965e5b9ed86"
   },
   "healthChecks": [
     {
       "path": "/",
       "protocol": "HTTP",
       "portIndex": 0
     }
   ],
   "labels": {
       "HAPROXY_GROUP": "external"
   }
}
```
Deploy the Tweeter application:
```
dcos marathon app add tweeter.json
```
