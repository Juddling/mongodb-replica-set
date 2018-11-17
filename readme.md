## MongoDB Cluster

## Architecture
We're going to scale using replication rather than sharding.

![Three node diagram](https://docs.mongodb.com/manual/_images/replica-set-primary-with-two-secondaries.bakedsvg.svg)

Using three based on the number of devices I have available and:

> Replica sets should always have an odd number of members

*MongoDB Docs:*

* [architecture](https://docs.mongodb.com/manual/core/replica-set-architecture-three-members/)
* [deployment guide](https://docs.mongodb.com/manual/tutorial/deploy-replica-set/)

## Docker

In the docker-compose v3 format, resource limits can only be specified on the `deploy:` key,
which is for sawrm deployments. To use these resource limits locally, add the --compatibility flag:
https://github.com/docker/compose/pull/5684

```
docker-compose --compatibility up
```

## Configure replication:

Connect to the primary node:

```
> rsconf = {
  _id: "rs0",
  members: [
    {
     _id: 0,
     host: "mongo.primary:27017"
    },
    {
     _id: 1,
     host: "mongo.secondary.1:27017"
    },
    {
     _id: 2,
     host: "mongo.secondary.2:27017"
    }
   ]
};
> rs.initiate(rsconf)
```

Insert a document:

```
> db.hack.insertOne({needCoffee: true})
```


See the data on a secondary:

```
mongo --port 27018

> db.setSlaveOk()
> db.hack.find({})
```