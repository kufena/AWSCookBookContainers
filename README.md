# AWSCookBookContainers

First is to get the thing running from an EC2 instance.  First you need to install Docker.  
You don't need to install dotnet as that should be in the container image.

But what of the image itself?  Firstly, the Dockerfile that comes with the project is a bit odd 
as you need to run it in the root of the solution not the root of the project.  So in this case you might do something like:

    docker build . --file SimpleWebAPI/Dockerfile --tag simplewebapi:latest
    docker run -dp 127.0.0.1:5000:80 simplewebapi:latest

which builds the container image and then runs it, pointing you local machines port 5000 to port 80 inside the container.
If this works, you need to push the container to a repository.  At the moment, I'm using the Docker Hub repository.  I did this
to make it work:

    docker image tag simplewebapi:latest potterboy67/simplewebapi:latest
    docker image push potterboy67/simplewebapi:latest

which pushes the newly tagged image to docker.io/potterboy67/simplewebapi with the latest tag.  You will need to log in
to your repository, obviously, using something like

    docker login

Then fire up an EC2 instance, ensuring that the network allows the instance to be open to HTTP and HTTPS traffic.  I do this
selecting 'MyIP' so that no one else can see it.  Then connect to the instance and install Docker:

    sudo yum update -y
    sudo amazon-linux-extras install docker
    sudo service docker start
    sudo groupadd docker
    sudo usermod -a -G docker ec2-user
    newgrp docker

At this point (and there may be some interaction required during this process, like pressing 'y' to make some changes happen)
you should have Docker installed.  You can check with

    sudo service docker status
    docker --version

You should now be able to pull the image you have tagged and uploaded to your repository, using

    docker pull potterboy67/simplewebapi:latest

and then create a running instance

    docker run -d -p 80:80 potterboy67/simplewebapi:latest

Using the IP address of the instance you can check by pointing a browser at

    http://<ip address>/weatherforecast

You should see a page of JSON or some weather-like info.

I couldn't quite work out the URL to use here - the app is started with root /app/ but that doesn't seem to apply when
I ran it, locally or remotely.

