### Check the file from the project

The app used in this guide is based on the hit counter app in the Get started with Docker Compose guide. It consists of a Python app which maintains a counter in a Redis instance and increments the counter whenever you visit it.

### Check the file from the project

Check a file called app.py in the project directory and paste this in:

```shell
from flask import Flask
from redis import Redis

app = Flask(__name__)
redis = Redis(host='redis', port=6379)

@app.route('/')
def hello():
    count = redis.incr('hits')
    return 'Hello World! I have been seen {} times.\n'.format(count)

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8000, debug=True)
```

Create a file called requirements.txt and paste these two lines in:

```shell
flask
redis
```

Create a file called Dockerfile and paste this in:

```shell
FROM python:3.7-alpine
ADD . /code
WORKDIR /code
RUN pip install -r requirements.txt
CMD ["python", "app.py"]
```

Create a file called docker-compose.yml and paste this in:

```shell
version: '3'

services:
  web:
    image: 127.0.0.1:5000/stackdemo
    build: .
    ports:
      - "8000:8000"
  redis:
    image: redis:alpine
```

The image for the web app is built using the Dockerfile defined above. It’s also tagged with 127.0.0.1:5000 - the address of the registry created earlier. This is important when distributing the app to the swarm.

### Test the app with Compose
Start the app with docker-compose up. This builds the web app image, pull the Redis image if you don’t already have it, and create two containers.

You see a warning about the Engine being in swarm mode. This is because Compose doesn’t take advantage of swarm mode, and deploys everything to a single node. You can safely ignore this.

```shell
vagrant@docker$ docker-compose up -d
```

* WARNING: The Docker Engine you're using is running in swarm mode.

Compose does not use swarm mode to deploy services to multiple nodes in
a swarm. All containers are scheduled on the current node.

To deploy your application across the swarm, use `docker stack deploy`.

Creating network "stackdemo_default" with the default driver

```shell
Building web
...(build output)...
Creating stackdemo_redis_1
Creating stackdemo_web_1
```

Check that the app is running with docker-compose ps:

```shell
vagrant@docker$ docker-compose ps

      Name                     Command               State           Ports
-----------------------------------------------------------------------------------
stackdemo_redis_1   docker-entrypoint.sh redis ...   Up      6379/tcp
stackdemo_web_1     python app.py                    Up      0.0.0.0:8000->8000/tcp
```

You can test the app with curl:

```shell
vagrant@docker$ curl http://localhost:8000
Hello World! I have been seen 1 times.

vagrant@docker$ curl http://localhost:8000
Hello World! I have been seen 2 times.

vagrant@docker$ curl http://localhost:8000
Hello World! I have been seen 3 times.
```

Bring the app down:

```shell
vagrant@docker$ docker-compose down --volumes

Stopping stackdemo_web_1 ... done
Stopping stackdemo_redis_1 ... done
Removing stackdemo_web_1 ... done
Removing stackdemo_redis_1 ... done
Removing network stackdemo_default
Push the generated image to the registry
```

### Optional
To distribute the web app’s image across the swarm, it needs to be pushed to the registry you set up earlier. With Compose, this is very simple:

```shell
vagrant@docker$ docker-compose push

Pushing web (127.0.0.1:5000/stackdemo:latest)...
The push refers to a repository [127.0.0.1:5000/stackdemo]
5b5a49501a76: Pushed
be44185ce609: Pushed
bd7330a79bcf: Pushed
c9fc143a069a: Pushed
011b303988d2: Pushed
latest: digest: sha256:a81840ebf5ac24b42c1c676cbda3b2cb144580ee347c07e1bc80e35e5ca76507 size: 1372
The stack is now ready to be deployed.
```

### Create the swarm
When you run the command to create a swarm, the Docker Engine starts running in swarm mode.

Run docker swarm init to create a single-node swarm on the current node. The Engine sets up the swarm as follows:

switches the current node into swarm mode.
creates a swarm named default.
designates the current node as a leader manager node for the swarm.
names the node with the machine hostname.
configures the manager to listen on an active network interface on port 2377.
sets the current node to Active availability, meaning it can receive tasks from the scheduler.
starts an internal distributed data store for Engines participating in the swarm to maintain a consistent view of the swarm and all services running on it.
by default, generates a self-signed root CA for the swarm.
by default, generates tokens for worker and manager nodes to join the swarm.
creates an overlay network named ingress for publishing service ports external to the swarm.
The output for docker swarm init provides the connection command to use when you join new worker nodes to the swarm:

```shell
$ docker swarm init --advertise-addr 192.168.0.248
Swarm initialized: current node (dxn1zf6l61qsb1josjja83ngz) is now a manager.
```

To add a worker to this swarm, run the following command:

```shell
    docker swarm join \
    --token SWMTKN-1-49nj1cmql0jkz5s954yi3oex3nedyz0fb0xx14ie39trti4wxv-8vxv8rssmk743ojnwacrr2e7c \
    192.168.99.100:2377
```

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.

To retrieve the join command including the join token for worker nodes, run:

$ docker swarm join-token worker

To add a worker to this swarm, run the following command:

```shell
    docker swarm join \
    --token SWMTKN-1-49nj1cmql0jkz5s954yi3oex3nedyz0fb0xx14ie39trti4wxv-8vxv8rssmk743ojnwacrr2e7c \
    192.168.99.100:2377

This node joined a swarm as a worker.
```

NOTE: The IPs could change in your environment

### Deploy the stack to the swarm
Create the stack with docker stack deploy:

```shell
vagrant@docker$ docker stack deploy --compose-file docker-compose.yml stackdemo

Ignoring unsupported options: build

Creating network stackdemo_default
Creating service stackdemo_web
Creating service stackdemo_redis
```

The last argument is a name for the stack. Each network, volume and service name is prefixed with the stack name.

Check that it’s running with docker stack services stackdemo:
```shell
vagrant@docker$ docker stack services stackdemo

ID            NAME             MODE        REPLICAS  IMAGE
orvjk2263y1p  stackdemo_redis  replicated  1/1       redis:3.2-alpine@sha256:f1ed3708f538b537eb9c2a7dd50dc90a706f7debd7e1196c9264edeea521a86d
s1nf0xy8t1un  stackdemo_web    replicated  1/1       127.0.0.1:5000/stackdemo@sha256:adb070e0805d04ba2f92c724298370b7a4eb19860222120d43e0f6351ddbc26f
```

Once it’s running, you should see 1/1 under REPLICAS for both services. This might take some time if you have a multi-node swarm, as images need to be pulled.

As before, you can test the app with curl:

```shell
vagrant@docker$ curl http://localhost:8000
Hello World! I have been seen 1 times.

vagrant@docker$ curl http://localhost:8000
Hello World! I have been seen 2 times.

vagrant@docker$ curl http://localhost:8000
Hello World! I have been seen 3 times.
```

Thanks to Docker’s built-in routing mesh, you can access any node in the swarm on port 8000 and get routed to the app:

```shell
vagrant@docker$ curl http://address-of-other-node:8000
Hello World! I have been seen 4 times.
```

Bring the stack down with docker stack rm:

```shell
vagrant@docker$ docker stack rm stackdemo

Removing service stackdemo_web
Removing service stackdemo_redis
Removing network stackdemo_default
```

Bring the registry down with docker service rm:

```shell
vagrant@docker$ docker service rm registry
```

If you’re just testing things out on a local machine and want to bring your Docker Engine out of swarm mode, use docker swarm leave:

```shell
vagrant@docker$ docker swarm leave --force

Node left the swarm.
```