---
title: New Jenkins install to manage the builds
date: 2016-09-26
---

I need a new Jenkins installation, because the current one has an annoying encoding bug. Even though the locale is set to UTF-8 and the log files are stored in UTF-8 internally, the log file is converted to another encoding when served over the web.

Running Jenkins in a Docker container was not the best idea, in the sense that it was quite fiddly to manage. So I'll install Jenkins now on a new VM, without Docker.

## Installing Jenkins

Create a new VM on Amazon. I used an Ubuntu 16.04 HVM AMI. The instructions are for older Ubuntu versions I guess, because they mention `openjdk-7-jre`, but I suppose, the newer Java should work fine.

Follow https://wiki.jenkins-ci.org/display/JENKINS/Installing+Jenkins+on+Ubuntu:
```sh
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install -y openjdk-8-jre openjdk-8-jdk
```

```sh
wget -q -O - https://pkg.jenkins.io/debian/jenkins-ci.org.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt-get update
sudo apt-get install -y jenkins
```

Need to change the jenkins startup options in `/etc/default/jenkins` to make sure Jenkins stores files in UTF-8, because it seems to ignore the system locale:
```
JAVA_ARGS="-Dfile.encoding=UTF-8 ..."
```

The same flag must be added to the script that starts the workers, see later.

## Add/redirect DNS

Set the A record of `jenkins.r-hub.io` to the new server.

## Configure Jenkins

Make sure you have access to port 8080 on the instance, and then start configuring Jenkins from your browser.

Install no plugin from the installer. It does not have the full list,
so we'll have to install some plugins later, anyway. So we'll do it all at once.

Create an admin user called `admin`, and a secure password.

Install the following plugins using the plugin manager:
* Build timeout plugin
* Cross-platform shell plugin
* Email extension plugin
* Elastic axis plugin
* Groovy postbuild plugin
* Mailer plugin
* PowerShell plugin
* Self-Organizing Swarm Plug-in Modules
* SSH Slaves plugin
* Workspace cleanup plugins

Then configure Jenkins:
* Set the quiet period to `0`, no need to wait before starting a build.
* Setup global environment variables: `PS4="+R-HUB-R-HUB-R-HUB"`
* Jenkins URL: `http://jenkins.r-hub.io:8080/`
* Admin email address: `admin@r-hub.io`
* Extended email notification requires some configuration:
  1. SMTP server: `127.0.0.1`
  2. Default User email suffix: `@r-hub.io`
  3. Reply-to list: `admin@r-hub.io`
* In Jenkins global security config, set the TCP port for JNLP agents to fixed 50000.
* Set the Security realm to Jenkins' own user database, do not allow users to sign up
* Set the authorization to Matrix-based security, give overall *Administter* rights to `admin`
* Turn *off* the "Prevent Cross Site Request Forgery exploits" switch. This does not work currently, I am not sure why.
* Turn on Slave → Master Access Control

## Install and configure mail server

```sh
apt-get install postfix
```
The name of the system is `jenkins.r-hub.io`.

## Add workers

SSH to a Linux worker, and restart the swarm process:
```sh
restart swarm JENKINS_PASSWORD=<passwd> RHUB_IP=jenkins.r-hub.io
```

On the windows workers, just restart the Java process with the new Jenkins server

Quite annoyingly, Azure sometimes changes the IP address of the builders, and then I need to update the `jenkins` security group on AWS.

## Update builder config

Make sure that the Jenkins server, and the builder server (that runs the frontend and the backend) are both have the same (`default`) security group.

Configure the `JENKINS_URL` environment variable on dokku, use the internal IP address of the Jenkins server. E.g.

```sh
dokku config:set cron JENKINS_URL=http://admin:<passwd>@172.31.30.118:8080
```

Do this for all node apps that need access to Jenkins: `builder`, `buidler-test`, `backend` and `cron` currently.
