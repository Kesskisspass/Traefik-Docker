# Traefik Example

This is an example of how to use **Traefik** as reverse-proxy to _load-balance_ between instances of your apps, and to _route_ domains to specific services.

## What will we do ?
For this example, we will build a very simple app composed by two containers (imagine something like a frontend and a backend of an app).
In front of these two containers, we'll add another container (You guess it... **Traefik**) as a _reverse-proxy_.\
We'll see how Traefik will help us to manage the **load balancing** dynamically (between two or more instances of a service) and how to use it to **route our requests** from a domain  to a specific service.  
> ex:  \
> first.localhost -> service1\
> second.localhost -> service2

## Requirements
- Docker is needed (Docker-compose too)
- We'll use 2 ports on host so be sure they are not already used or change them on the _docker-compose.yml_ file:
	- 80
	- 8080

## How-to use it ?

### Launch app

- Download (or clone) this project
- Open a terminal inside the folder and launch command\

```
docker-compose up -d
```
### Check if everythins is OK
- Open your favorite browser at [http://127.0.0.1:8080](http://127.0.0.1:8080)
- You should see the Traefik's Dashboard

## Routing
As you might see on the docker-compose file, we have configured Traefik with **rules** to route request with host:first.localhost to the app1.
We can check it with a simple curl command
```
curl -H Host:first.localhost http://127.0.0.1
```
And now you can see that the *Hostname* is your app1 container id.
You obviously can do the same with the other app
```
curl -H Host:second.localhost http://127.0.0.1
```

## Load balancing
Now we will add some instances to our services app1 & app2
```
docker-compose up -d --scale service1=2 --scale service2=2
```
Ok so with a ```docker ps``` you should see 4 containous/whoami containers running.
Let's relaunch our curl command:
```
curl -H Host:first.localhost http://127.0.0.1
```
And note how the IP is changing between two values each time you relaucn the command. It's because Traefik is load-balancing requests between two instances of the service.
Now, even better you can stop one of the instance, and dynamically, Traefik know that there is only one container available, so all requests goes to the only running container. And if you restart the second container, Traefik will load-balance again...
