# Morse MicroServices - Jenkins

This repository contains the jenkinsfile and documentation.


## Files:
[Jenkins pipline configuration](https://gist.github.com/eran101/0534d89dd1fc550c8b49eb3634e8db70)

[An example Jenkins job log](log.txt)

## Assumptions:

* Jenkins is installed and running on the EC2 instance.
* The EC2 instance has enough resources to run Docker engine, containers and Jenkins.
* The user completed / disabled the Jenkins "getting started" wizard.
* The jenkins user should be in the docker users group:
sudo usermod -aG docker jenkins
newgrp docker
sudo systemctl restart jenkins 
(For Linux distros using Systemd)
* The Jekins pipeline is using the configuration from the [config file above](#Files).
* Jenkins is configured to run on boot if required:
sudo systemctl enable jenkins
(For Linux distros using Systemd)
* Git, GitHub and Docker Jenkins plugins are installed.
* To test triggering on every new commit, the Git repository can be cloned to another account.
Then, the repository URL should be adjusted.
* To push the Docker images, the docker repository URL and credentials should be adjusted.
* The Docker hub credentials should use the ID: dockerhub_id	
It is possible to use other ID name and adjust it in the jenkinsfile.
* Docker engine is installed and running on the Jenkins VM.
* There are no other containers with the same name on the specified docker network.
* There are IP addresses available on the specified docker network.
* Linux kernel security module isn't configured to restrict Docker and / or Jenkins (AppArmor / SELinux)
* The EC2 instance network including security group is configured properly.
* The client container image default shell is busybox.
It doesn't support loop ranges.

## Notes:

For production environment:
* It is a good practice to use 2 containers for the server:
1 web server and 1 PHP-FPM
* The DocumentRoot directory permissions should be more secure.
* The current server image is using mod_php (DSO) which is less secure:
https://www.knownhost.com/wiki/control-panels/misc/php-handlers-explained#dso
* Server headers should be disabled in the services configuration (PHP and Web server versions)
* Containers should be scanned for vulnerabilities 
* There will be warning in the Jenkins job log about using a credential helper (for token too). <br/>
This can fixed: <br/>
https://docs.docker.com/engine/reference/commandline/login/#credentials-store
* The server code dependencies can be installed using composer.
* Composer has zip dependencies.
* Composer is mostly in use for production images. <br/>
It is not included in the dockerfile to simpilfy the process. <br/>
https://github.com/mlocati/docker-php-extension-installer/issues/52*issuecomment-552089901 
* The EC2 deployment, Jenkins and Docker installation can be automated. <br/>
This can be done using AWS CLI and user data script / IaC tool such as Terraform.
* It is possible to split each container run Jenkins step to 2: pull and run.
* The server container is running Apache HTTP. <br/>
It might quit with SIGWINCH when it is attached: <br/>
https://github.com/docker-library/php/issues/892#issuecomment-531310247
https://github.com/docker-library/php/issues/892#issuecomment-531328870
