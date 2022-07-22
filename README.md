# Traefik - Docker : Example

This is an example of how to use **Traefik** as reverse-proxy to _load-balance_ between instances of your apps, and to _route_ domains to specific services.

## What will we do ?
For this example, we will build a very simple app composed by two containers (imagine something like a frontend and a backend of an app).
In front of these two containers, we'll add another container (You guess it... **Traefik**) as a _reverse-proxy_.\
We'll see how Traefik will help us to manage the **load balancing** dynamically (between two or more instances of a service) and how to use it to **route our requests** from a domain  to a specific service.  
> ex:  \
> first.localhost => service1
> - container1
> - container2
> - container3\
>
> second.localhost => service2
> - container4
> - container5
> - container6

## Requirements
- Docker is needed (Docker compose too)
- We'll use 2 ports on host so be sure they are not already used or change them on the _docker-compose.yml_ file:
	- 80
	- 8080

## How-to use it ?

### Launch app

- Download (or clone) this project
- Open a terminal inside the 'traefik-docker' folder and launch command\

```bash
docker compose up -d

# I mainly use 'docker compose' command (without dash), depending of your docker version, you may have to use 'docker-compose' command
```
### Check if everything is OK
- Open your favorite browser at [http://127.0.0.1:8080](http://127.0.0.1:8080)
> You should see the Traefik's Dashboard
- To check the services you can open [http://first.localhost](http://first.localhost) or [http://second.localhost](http://second.localhost)
> You can see informations about the hostname, request, IPs...

## Routing
As you might see on the docker-compose file, we have configured Traefik with **rules** to route request with host:first.localhost to the service1.
We can check it with a simple curl command
```
curl -H Host:first.localhost http://127.0.0.1
```
And now you can see that the *Hostname* is your service1 container id.
You obviously can do the same with the other app
```
curl -H Host:second.localhost http://127.0.0.1
```

## Load balancing
Now we will add some instances to our service1 & service2
```
docker-compose up -d --scale service1=3 --scale service2=5
```
Ok so with a ```docker ps``` you should see 8 containous/whoami containers running.
Let's relaunch our curl command:
```
curl -H Host:first.localhost http://127.0.0.1
```
And note how the IP is changing between multiple values (3 for this example) each time you relauch the command. It's because Traefik is load-balancing requests between each instances of the service.
Now, even better you can stop one of the instance, and dynamically, Traefik know that there is only two containers available, so all requests goes to running containers. And if you restart the stopped container, Traefik will load-balance again on it...

