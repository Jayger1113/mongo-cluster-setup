# mongo-cluster-setup

# Version: 4.0.4

# Suppose we have aws instance as following:
10.0.40.123
10.0.40.195
10.0.40.252

# How do we group a replica set

first memeber: (10.0.40.123)
$sudo vim /etc/mongo.conf
$sudo service mongod start
$mongo

in mongo shell:
cfg = { _id: "rs0","protocolVersion" : NumberLong(1), members: [{_id: 0,host: "10.0.40.123:27017"},{_id: 1,host: "10.0.40.195:27017"},{_id: 2,host: "10.0.40.252:27017"}]}

rs.conf(cfg);
rs.initiate(cfg)

second member: (10.0.40.195)
$sudo vim /etc/mongo.conf
$sudo service mongod start
$mongo

third member: (10.0.40.252)
$sudo vim /etc/mongo.conf
$sudo service mongod start
$mongo

# spring boot config

As mongo official doc said:
https://docs.mongodb.com/manual/reference/connection-string/
mongodb://mongodb0.example.com:27017,mongodb1.example.com:27017,mongodb2.example.com:27017/admin?replicaSet=myRepl

and we have our spring boot config file "application.yml" as below:
spring.data.mongodb.uri=mongodb://10.0.40.123:27017,10.0.40.195:27017,10.0.40.252:27017/dbName?replicaSet=rs0

# Trouble shooting

#########################
rs0:SECONDARY> show dbs
2019-09-27T07:20:46.394+0000 E QUERY    [js] Error: listDatabases failed:{
        "operationTime" : Timestamp(1569568839, 1),
        "ok" : 0,
        "errmsg" : "not master and slaveOk=false",
        "code" : 13435,
        "codeName" : "NotMasterNoSlaveOk",
        "$clusterTime" : {
                "clusterTime" : Timestamp(1569568839, 1),
                "signature" : {
                        "hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
                        "keyId" : NumberLong(0)
                }
        }
} :

solution:

rs0:SECONDARY> rs.slaveOk()
rs0:SECONDARY> show dbs
admin       0.000GB
config      0.000GB
local       0.088GB

##########################
Failed to unlink socket file /tmp/mongodb-27017.sock Unknown error

solution:
$rm /tmp/mongodb-27017.sock
###########################
InvalidReplicaSetConfig: Our replica set config is invalid or we are not a member of it
Sometimes, kubernetes cluster restart. And comes t

solution:
rs.reconfig(cfg,{force:true})

Reference:
# https://docs.mongodb.com/manual/tutorial/deploy-replica-set-for-testing/
# https://docs.mongodb.com/manual/release-notes/4.0-upgrade-replica-set/
# https://docs.bitnami.com/vmware-templates/infrastructure/mongodb/get-started/understand-cluster-config/
# https://rammusxu.github.io/2019/05/24/InvalidReplicaSetConfig-Our-replica-set-config-is-invalid-or-we-are-not-a-member-of-it/

