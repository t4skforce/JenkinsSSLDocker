# jenkins-ssl
Docker Container - Jenkins HTTPS secured Web-UI, Oracle Java 8, Maven. 
[GitHub repo](https://github.com/t4skforce/jenkins-ssl)

[![](https://badge.imagelayers.io/t4skforce/jenkins-ssl:latest.svg)](https://imagelayers.io/?images=t4skforce/jenkins-ssl:latest 'Get your own badge on imagelayers.io')

# About the Container

This build is based on [jenkins:latest](https://hub.docker.com/_/jenkins/) with added features:
* HTTPS Secured WEB-UI
* OpenJDK 8u72
* Maven 3.3.9

# How to use this image
`docker run --name jenkins-master -p 443:8443 -p 50000:50000 t4skforce/jenkins-ssl:latest`
This will store the workspace in `/var/jenkins_home`. All Jenkins data lives in there - including plugins and configuration. You will probably want to make that a persistent volume (recommended):

`docker run --name jenkins-master -p 443:8443 -p 50000:50000 -v /your/home:/var/jenkins_home t4skforce/jenkins-ssl:latest`
This will store the jenkins data in `/your/home` on the host. Ensure that `/your/home` is accessible by the jenkins user in container (jenkins user - uid 1000) or use `-u some_other_user` parameter with `docker run`.

You can also use a volume container:

`docker run --name myjenkins -p 443:8443 -p 50000:50000 -v /var/jenkins_home  t4skforce/jenkins-ssl:latest`

Then jenkins-master container has the volume (please do read about docker volume handling to find out more).

# Opening Web-UI
[https://yourservernameorip/](https://127.0.0.1/)

# Upgrade
```bash
#!/bin/bash
# download updates
docker pull t4skforce/jenkins-ssl:latest
# stop current running image
docker stop jenkins-master 
# remove container
docker rm jenkins-master
# start with new base image
docker run --name jenkins-master -d -p 443:8443 -p 50000:50000 -v /your/home:/var/jenkins_home t4skforce/jenkins-ssl:latest
```
# Autostart
To enable Jankins to start at system-startup we need to create a systemd service file `vim /lib/systemd/system/jenkins.service`:

```ini
[Unit]
Description=Jenkins-Server
Requires=docker.service
After=docker.service

[Service]
Restart=always
ExecStart=/usr/bin/docker start -a jenkins-master
ExecStop=/usr/bin/docker stop -t 2 jenkins-master

[Install]
WantedBy=multi-user.target
```

To start the service manually call `systemctl start jenkins`. For retreaving the current service status call `systemctl status jenkins`

```bash
root@jenkins:~# systemctl status jenkins
● jenkins.service - Jenkins-Server
   Loaded: loaded (/lib/systemd/system/jenkins.service; disabled)
   Active: active (running) since Sun 2016-04-17 11:42:57 BST; 2s ago
 Main PID: 2642 (docker)
   CGroup: /system.slice/jenkins.service
           └─2642 /usr/bin/docker start -a jenkins-master
```


# Further Information

For further information on configuring the Image please refer to [jenkins:latest](https://hub.docker.com/_/jenkins/)
