# docker-compose-nifi-cluster
A Docker Compose files to compose a NiFi cluster on Docker.

## How it looks like

```
                  +--------------------------------+
                  |                                |
                  | ncm (nifi-cluster-manager)     |
                  |                                |
                  |  8080: nifi HTTP port          |
 +------------------>19181: unicast manager port   |
 |                |                                |
Join cluster      +--------------------------------+
 |                |                                |
 +----------------+ node1                          |
 |                |                                |
 |                |   Embedded ZK server           |
 |          +---------->2181: zk client port       |
 | Cluser State   |                                |
 |          +-------+Processors                    |
 |          |     |                                |
 |          |     +--------------------------------+
 |          |     |                                |
 +----------------+ node2                          |
            |     |                                |
            +-------+Processors                    |
                  |                                |
                  +--------------------------------+
```

## How to use

1. Install docker and docker-compose if you don't have.
1. Clone this project.
1. Start NiFi cluster by running:

    ```
    $ cd docker-compose-nifi-cluster
    $ docker-compose up -d
    ```

- Check containers status:

    ```
    $ docker-compose ps
    ```

- Check logs to see if it's ready:

    ```
    $ docker exec -it dockercomposenificluster_ncm.nifi_1 tail -f logs/nifi-app.log
    or
    $ ./bin/tail-nifi-app-log ncm
    ```

    If you see following logs, then NiFi is ready to handle request:
    ```
    2016-04-11 08:39:47,288 INFO [main] org.eclipse.jetty.server.Server Started @75413ms
    2016-04-11 08:39:48,362 INFO [main] org.apache.nifi.web.server.JettyServer NiFi has started. The UI is available at the following URLs:
    2016-04-11 08:39:48,362 INFO [main] org.apache.nifi.web.server.JettyServer http://ncm.nifi:8080/nifi
    ```

- To stop/start the cluster

    ```
    $ docker-compose stop
    $ docker-compose start
    ```

- To login (attach a shell) a container

    ```
    $ ./bin/attach node1
    ```


### Open the cluster from a browser

In order to access NiFi UI from a browser, first you need to figure out what IP address the NiFi Cluster Manager (ncm) is running on.

For those who are using Docker on Mac like me, the containers are running on a virtual machine which is created by `docker-machine`.

You can find IP address of 'default' docker machine:

```
$ docker-machine ip default
192.168.99.100
```

The ncm container exports port 8080 through port 8080 on the docker host, so you can access NiFi cluster via:

```
http://192.168.99.100:8080/nifi
```

![connected-node](https://raw.githubusercontent.com/ijokarumawak/docker-compose-nifi-cluster/master/images/connected-nodes.jpg)

### Open the cluster from a browser using private IP address

Since this docker compose configuration doesn't have specific network configuration, it uses 'default' network.
You can find IP address of ncm container by:

```
$ docker inspect -f '{{.NetworkSettings.Networks.dockercomposenificluster_default.IPAddress}}' dockercomposenificluster_ncm.nifi_1
172.18.0.2
```

In my case, ncm is running on `172.18.0.2`, the network is a private network which exists inside the `default` docker machine.
If you'd like to access the container with its private IP address, then you need to add route setting.
To do so, execute following command:

```
$ sudo route add -net 172.18.0.0 `docker-machine ip default`
```

Then you can access the ncm container from a browser by: 

```
http://172.18.0.2:8080/nifi
```

### How to update compose definition

When you update/pull the latest compose definition, it is recommended to rebuild services by running:

```
$ docker-compose down
$ docker-compose build
$ docker-compose up -d
```
