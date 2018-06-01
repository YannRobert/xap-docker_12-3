This repository helps reproduce an issue with accessing XAP RESTfull Management API from the host when running XAP inside a docker swarm stack.

Steps :

# 1 : prove image is usable with plain docker-compose


Leave docker swarm if your docker is in swarm mode
```
docker swarm leave --force
```

Launch the docker container using docker-compose
```
docker-compose -f ./docker-compose-xap.yml up -d
```

Checks logs to verify everything should be ok
```
docker-compose -f ./docker-compose-xap.yml logs -f
```

Access the management console using the port that is forwarded to the container

Using curl
```
curl http://localhost:8090/v2/hosts
```
or any web browser.


It should display something similar to 

```
[{"name":"3d6a7b61f458","address":"172.22.0.2","containers":[]}]
```

Stop the docker containers
```
docker-compose -f ./docker-compose-xap.yml kill
docker-compose -f ./docker-compose-xap.yml rm --force
```

# 2 : indicate image is not usable with docker swarm stack

Init docker swarm if your docker is not part of a swarm
```
docker swarm init
```

Launch the docker container using docker stack
```
docker stack deploy xap -c ./docker-stack-xap.yml
```

Checks logs to verify everything should be ok
```
docker service logs -f xap_xap
```

Using curl
```
curl http://localhost:8090/v2/hosts
```

The expected result is the same as the result we got in step 1.

But instead, we get an error :

There is an error indicating
```
curl: (52) Empty reply from server
```

or any web browser
```
ERR_EMPTY_RESPONSE
```

Stop the docker containers
```
docker stack rm xap
```

Leave docker swarm if your docker is in swarm mode
```
docker swarm leave --force
```

