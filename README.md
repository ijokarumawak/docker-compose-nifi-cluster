# docker-compose-nifi-cluster
A Docker Compose files to compose a NiFi cluster on Docker.

The master branch uses the latest NiFi version (1.0).
For older version, use following branches:

- [0.x](https://github.com/ijokarumawak/docker-compose-nifi-cluster/tree/0.x): NCM and 2 nodes cluster.

## Prerequisite

You need docker, docker-machine and docker-compose. For example, in my environment I have these versions.

```Shell
$ docker -v
Docker version 1.12.1, build 6f9534c

$ docker-machine -v
docker-machine version 0.8.1, build 41b3b25

$ docker-compose -v
docker-compose version 1.8.0, build f3628c7
```

## How to use

```Shell
# Clone this repository
$ git@github.com:ijokarumawak/docker-compose-nifi-cluster.git
$ cd docker-compose-nifi-cluster

# To start nifi cluster
$ docker-compose up -d

# Wait few minutes for NiFi to unpack nars and the nodes recognized eachother.
# Then access to the seed node's url through docker-machine vm
$ docker-machine ip
192.168.99.100

http://192.168.99.100:8080/nifi/

# You may need to add routing by a command something like this:
$ sudo route add -net 172.17.0.0 `docker-machine ip`

# View logs or container status
$ docker-compose logs -f
$ docker exec nifi-cluster-seed tail -f logs/nifi-app.log
$ docker-compose ps

# Scale number of nodes (seed + n)
$ docker-compose scale nifi-nodes=2

# Dispose the cluster
$ docker-compose down

# To rebuild nifi-node docker image
$ docker-compose build
```

## Special thanks

I used [mkobit/nifi](https://github.com/mkobit/docker-nifi) as a base image. Thanks for sharing the image and maintaining it up to date!

## Screen shot

![connected-node](https://raw.githubusercontent.com/ijokarumawak/docker-compose-nifi-cluster/master/images/connected-nodes.png)
