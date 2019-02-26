# DevEnv-MongoDB-Using-Docker
Deploying Local MongoDB Using Docker for Developer purposes

## Prerequisites for Deploying MongoDB on Docker
We will only need Docker installed in the system.

## Creating a MongoDB Image
```
$ mkdir mongo-with-docker
$ cd mongo-with-docker
$ vi Dockerfile
```

```
FROM debian:jessie-slim
RUN apt-get update && \
apt-get install -y ca-certificates && \
rm -rf /var/lib/apt/lists/*
RUN gpg --keyserver ha.pool.sks-keyservers.net --recv-keys 0C49F3730359A14518585931BC711F9BA15703C6 && \
gpg --export $GPG_KEYS > /etc/apt/trusted.gpg.d/mongodb.gpg
ARG MONGO_PACKAGE=mongodb-org
ARG MONGO_REPO=repo.mongodb.org
ENV MONGO_PACKAGE=${MONGO_PACKAGE} MONGO_REPO=${MONGO_REPO}
ENV MONGO_MAJOR 3.4
ENV MONGO_VERSION 3.4.18
RUN echo "deb http://$MONGO_REPO/apt/debian jessie/${MONGO_PACKAGE%-unstable}/$MONGO_MAJOR main" | tee "/etc/apt/sources.list.d/${MONGO_PACKAGE%-unstable}.list"
RUN echo "/etc/apt/sources.list.d/${MONGO_PACKAGE%-unstable}.list"
RUN apt-get update
RUN apt-get install -y ${MONGO_PACKAGE}=$MONGO_VERSION
VOLUME ["/data/db"]
WORKDIR /data
EXPOSE 27017
CMD ["mongod", "--smallfiles"]
```

Then run this command to build your own MongoDB Docker image:

```
docker build -t hello-mongo:latest .
```

## Starting the MongoDB Container From the Image

```
docker run --name my-mongo -d -v /tmp/mongodb:/data/db -p 27017:27017 hello-mongo
```

--name: Name of the container.
-d: Will start the container as a background (daemon) process. Don’t specify this argument to run the container as foreground process.
-v: Attach the /tmp/mongodb volume of the host system to /data/db volume of the container.
-p: Map the host port to the container port.
** Last argument is the name/id of the image.

To check whether the container is running or not, issue the following command:

```
docker ps
```

