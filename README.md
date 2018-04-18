# docker-registry
**How to install own docker registry on Amazon instance. It useful for corporate solutions for provide docker images**

1. Request SSL certificate for your domain, using ACM (AWS Certificate Manager).  
2. Create an AWS Load Balancer. Select your load balancer and go to the "Listeners" tab. Edit your configuration so you have HTTP:80 -> HTTP:80 and HTTPS:443 -> HTTP:80 and select your certificate for HTTPS (using ACM for SSL Certificate).  
So, we added SSL to the load balancer, not our AWS instance.  
3. Add your domain to AWS Route 53 (as CNAME) and put your Load Balancer name for CNAME value.

Make sure you have docker and docker-compose are installed and running on your AWS instance.  
Example of docker-compose installation:

```
sudo curl -L https://github.com/docker/compose/releases/download/1.21.0/docker-compose-$(uname -s)-$(uname -m) -o /usr/bin/docker-compose
chmod 755 /usr/bin/docker-compose
```

Clone this repo and run:

```
cd docker-registry
docker-compose up -d
```

You can stop registry by:
```
docker-compose down
```

Check your installation, open the link: `https://<YOUR_DOMAIN>/v2/_catalog`  
Output would be `{"repositories":[]}`

Now we are ready to push the images into registry (for example, nginx image):
```
$ docker pull nginx:latest
$ docker tag nginx:latest <YOUR_DOMAIN>/nginx:latest
$ docker push <YOUR_DOMAIN>/nginx:latest
```

Open the link: `https://<YOUR_DOMAIN>/v2/_catalog` again, make sure you have:
```
{"repositories":["nginx"]}
```

Now you can delete the local nginx image:
```
$ docker rmi nginx:latest
```

Check the pull on **client** machine by:
```
$ docker pull <YOUR_DOMAIN>/nginx:latest
```
