# mongo-cluster-setup

# Version: 4.0.4

# Suppose we have aws instance as following:
10.0.40.123
10.0.40.195
10.0.40.252

$mongo

cfg = { _id: "rs0","protocolVersion" : NumberLong(1), members: [{_id: 0,host: "10.0.40.123:27017"},{_id: 1,host: "10.0.40.195:27017"},{_id: 2,host: "10.0.40.252:27017"}]}

rs.reconf(cfg);

first memeber: (10.0.40.123)
$sudo mkdir -p /srv/mongodb/rs0-0  /srv/mongodb/rs0-1 /srv/mongodb/rs0-2
$cd /srv/mongodb
$sudo chmod 777 *
$mongod --replSet rs0 --port 27017 --bind_ip localhost,10.0.40.123 --dbpath /srv/mongodb/rs0-0  --oplogSize 128

second member: (10.0.40.195)
$sudo mkdir -p /srv/mongodb/rs0-0  /srv/mongodb/rs0-1 /srv/mongodb/rs0-2
$cd /srv/mongodb
$sudo chmod 777 *
$mongod --replSet rs0 --port 27017 --bind_ip localhost,10.0.40.195 --dbpath /srv/mongodb/rs0-1  --oplogSize 128

third member: (10.0.40.252)
$sudo mkdir -p /srv/mongodb/rs0-0  /srv/mongodb/rs0-1 /srv/mongodb/rs0-2
$cd /srv/mongodb
$sudo chmod 777 *
$mongod --replSet rs0 --port 27017 --bind_ip localhost,10.0.40.252 --dbpath /srv/mongodb/rs0-2 --oplogSize 128

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



Reference:
# https://docs.mongodb.com/manual/tutorial/deploy-replica-set-for-testing/
# https://docs.mongodb.com/manual/release-notes/4.0-upgrade-replica-set/
# https://docs.bitnami.com/vmware-templates/infrastructure/mongodb/get-started/understand-cluster-config/
