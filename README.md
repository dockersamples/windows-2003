## Migrating Apps from Legacy Windows Servers to Docker

There are three simple demo apps here which have been taken from a running Windows Server 2003 machine. This lab shows you how to migrate them all to Docker Windows containers, so you can run them on Windows Server 2016 in the datacenter or in any cloud.

> You'll deploy the applications using ZIP files that were the original deployment packages for Windows Server 2003. No need to change code to move apps to Docker.

## Pre-requisites

All you need is Docker running on Windows:

- Docker Desktop on Windows 10 *or*
- Docker Enterprise on Windows Server 2016

Optionally you can pull these base images to save time during the build:

```
microsoft/iis:nanoserver-sac2016
microsoft/iis:windowsservercore-ltsc2016
microsoft/aspnet:3.5-windowsservercore-ltsc2016
```

## Introduction
In this lab you'll see how easy it is to take workloads from Windows Server 2003 and 2008 and move them to Docker containers.

You don't need to change code, so you get your original apps running in a modern, supported platform which you can run with Docker in the datacenter or in any cloud.
Windows Server Apps

There are three demo web applications in the lab, all built originally to run on IIS in Windows Server 2003 or 2008. There's a static HTML website, a classic ASP site and an ASP.NET WebForms site.

They are all packaged in ZIP files, for a traditional deployment process where they would be copied to the server and then manually installed.

Now you can use those same ZIP files and automate the deployment with the Docker platform.

## App 1: Static HTML

Start with the static HTML site. Check out the [Dockerfile](./docker/static-html/Dockerfile) - this is a simple script which packages the application.

It uses one of Microsoft's Docker images as the base, which runs Nano Server with IIS installed. Then it copies the application ZIP file into the image.and uses PowerShell to expand the ZIP file and create a site in IIS.

You package the app with the docker image build command - run this in your Windows console:

```
docker image build `
 -t static-html `
 -f .\docker\static-html\Dockerfile .
```

That creates a Docker image, which is a portable package. You can share it on a public or private Docker registry and anyone with access can run your app in a container - they don't need Nano Server or IIS installed, they just need Docker running on Windows.

See how it works by running the app in a container:

```
docker container run `
 --detach --publish 8080:80 `
 static-html
```

The detach flag keeps the container running in the background, like a Windows Service. The publish flag publishes network ports, so now any traffic coming into the server on port 8080 will be sent to the container's port 80, and the container will process the request.

Try it - browse to port 8080 on your server, and you'll see the HTML app running in IIS, powered by Docker on Windows.

## App 2: Classic ASP

The next app uses classic ASP, a legacy web technology which had its last release in 2000. You package it in Docker the same way, using a Dockerfile.

This app uses Microsoft's Windows Server Core image with IIS installed. That image doesn't have classic ASP support, so the [Dockerfile](./docker/classic-asp/Dockerfile) installs it with the Install-WindowsFeature PowerShell cmdlet.

Build it in the same way:

```
docker image build `
 -t classic-asp `
 -f .\docker\classic-asp\Dockerfile .
```

And now run the app in a container, using a different port so both web apps can run on the same server:

```
docker container run `
 --detach --publish 8081:80 `
 classic-asp
```

Browse to port 8081 on the server, and you'll see the classic ASP app. The static HTML app is still running on port 8080.

## App 3: ASP.NET WebForms

The final app uses WebForms, the replacement for classic ASP introduced in 2002 - newer, but still a 16-year-old technology.

The [Dockerfile](./docker/webforms/Dockerfile) uses Microsoft's Windows Server Core image with IIS and ASP.NET installed. That provides all the prerequisites for the app, so the rest of the Dockerfile just copies in the ZIP file, expands it and sets up the site.

Build the Docker image:

```
docker image build `
 -t webforms `
 -f .\docker\webforms\Dockerfile .
```

Now run the app in a container, using a different port so all three web apps can run on the same server:

```
docker container run `
 --detach --publish 8082:80 `
 webforms
```

Browse to port 8082 on the server, and you'll see the ASP.NET WebForms app. The classic ASP app is still running on port 8081, and the static HTML app is on port 8080.

## Summary

You now have three apps running in Docker Windows containers. You can run them on the latest version of Windows on-prem or in the cloud. You used simple Dockerfiles to automate the deployment to build Docker images which you can share.

These are simple demo apps, but they've shown you how to get started migrating apps to Docker from older versions of Windows. They all use different technology stacks, but you build, run and manage them all in the same way.

For the next steps, check out our recorded webinar [How To Get Your Apps Off Windows 2003 And Into The Cloud With Docker Enterprise](https://blog.docker.com/2018/07/get-apps-off-windows-2003-cloud-docker-enterprise), and our session from DockerCon 2018 [5 Patterns for Success for Application Transformation](https://dockercon2018.hubs.vidyard.com/watch/w1gCK5PSt3JuGBLLELH9WG) .

