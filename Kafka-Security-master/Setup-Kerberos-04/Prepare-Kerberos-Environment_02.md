# Prepare Kerberos environment
## create principals
```
sudo kadmin.local -q "add_principal -randkey reader@KAFKA.SECURE"
sudo kadmin.local -q "add_principal -randkey writer@KAFKA.SECURE"
sudo kadmin.local -q "add_principal -randkey admin@KAFKA.SECURE"

sudo kadmin.local -q "add_principal -randkey kafka/ec2-18-139-87-22.ap-southeast-1.compute.amazonaws.com@KAFKA.SECURE"
```
## create keytabs
```

sudo kadmin.local -q "xst -kt /tmp/reader.user.keytab reader@KAFKA.SECURE"
sudo kadmin.local -q "xst -kt /tmp/writer.user.keytab writer@KAFKA.SECURE"
sudo kadmin.local -q "xst -kt /tmp/admin.user.keytab admin@KAFKA.SECURE"
sudo kadmin.local -q "xst -kt /tmp/kafka.service.keytab kafka/ec2-18-139-87-22.ap-southeast-1.compute.amazonaws.com@KAFKA.SECURE"

sudo chmod a+r /tmp/*.keytab
```

## download all keytabs to local computer
```
scp -i ~/kafka-security.pem centos@ec2-18-139-111-45.ap-southeast-1.compute.amazonaws.com:/tmp/*.keytab /tmp/
```
## copy service keytabs to Kafka-EC2
```
scp -i ~/kafka-security.pem /tmp/kafka.service.keytab ubuntu@ec2-18-139-87-22.ap-southeast-1.compute.amazonaws.com:/tmp/
```
## restrict access to keytabs
```
sudo chmod 600 /tmp/*.keytab
```
## TEST, from local computer
```
export DEBIAN_FRONTEND=noninteractive && sudo apt-get install -y krb5-user
sudo vi /etc/krb5.conf
## replace content by krb5.conf template

kinit -kt /tmp/admin.user.keytab admin
klist
```
## TEST, from Kafka EC2
```
export DEBIAN_FRONTEND=noninteractive ; sudo apt-get install -y krb5-user
sudo vi /etc/krb5.conf
## replace content by krb5.conf template

klist -kt /tmp/kafka.service.keytab
kinit -kt /tmp/kafka.service.keytab kafka/<<KAFKA-SERVER-PUBLIC-DNS>>
klist
```
