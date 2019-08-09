# Client configuration for using SSL

## grab CA certificate from remote server and add it to local CLIENT truststore

```
export CLIPASS=clientpass
cd ~
mkdir ssl
cd ssl
scp -i kafka-security.pem ubuntu@ec2-18-139-87-22.ap-southeast-1.compute.amazonaws.com:/home/ubuntu/ssl/ca-cert .
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert  -storepass $CLIPASS -keypass $CLIPASS -noprompt

keytool -list -v -keystore kafka.client.truststore.jks
```

## create client.properties and configure SSL parameters
security.protocol
ssl.truststore.location
ssl.truststore.password
==> use template [client.properties](./client.properties)

## TEST
test using the console-consumer/-producer and the [client.properties](./client.properties)
### Producer
```
bin/kafka-console-producer.sh --broker-list ec2-18-139-87-22.ap-southeast-1.compute.amazonaws.com:9093 --topic kafka-security-topic --producer.config ../ssl/client.properties

bin/kafka-console-producer.sh --broker-list ec2-18-139-87-22.ap-southeast-1.compute.amazonaws.com:9093 --topic kafka-security-topic


```
### Consumer
```
bin/kafka-console-consumer.sh --bootstrap-server ec2-18-139-87-22.ap-southeast-1.compute.amazonaws.com:9093 --topic kafka-security-topic --consumer.config ../ssl/client.properties
```
