# Load-Balancer (NGINX)

Load Balancer is a server that distribute traffic to different servers. It can also act as reverse proxy to redirect different apis to different servers

Implemented for nginx server on local machine to act as load balancer

## Configuration of nginx as a load balancer


Install nginx and start on machine

```
brew install nginx
brew services start nginx
```

nginx starts and runs on 8080 by default
modify nginx.conf as in code

### run the apis 
```
nohup Python apis.py
```

nginx run on some port and is made it public. so it listens on 0.0.0.0 i.e., all calls over internet

once it receives a https call it redirects to servers(that apis are running), update them accordingly in location block

It can also redirect based on path like /orders to one server and /users to another etc.,

### SSL
This load blancer is made secure using SSL
for SSL we need to install certbot 
```
brew install certbot
generate certificate certbot --nginx
```

