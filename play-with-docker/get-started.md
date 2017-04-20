
## Play with docker

Run this to get the docker-machine driver

    go get github.com/franela/docker-machine-driver-pwd

Start a session at play-with-docker.com

Copy the url of the session and export it as an env variable

    PWD_URL=http://host2.labs.play-with-docker.com/p/some-long-sessions-id-here

Set up a node using machine

    docker-machine create -d pwd node1
    docker-machine create -d pwd node2
    docker-machine create -d pwd node3

Set your local docker api to the node in the session

eval $(docker-machine env node)

Create a swarm

    docker swarm init --advertise-addr eth0

Join the other nodes to the swarm

    eval $(docker-machine env node2)
    docker swarm join --token SWMTKN-your-generated-token-here  10.0.60.3:2377
    eval $(docker-machine env node3)
    docker swarm join --token SWMTKN-your-generated-token-here  10.0.60.3:2377

Switch back to first node and list out the swarm nodes

    eval $(docker-machine env node1)
    docker node ls

Run a service on the swarm

    docker service create -p 8080:80 --name foobar --replicas=2 --update-failure-action=rollback nginx

Do some update to it

    docker service update foobar --replicas=10 --image dockercloud/hello-world