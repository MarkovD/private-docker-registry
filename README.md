# Private Docker Registry

## **Table of Contents**
- [Private Docker Registry](#private-docker-registry)
  - [**Table of Contents**](#table-of-contents)
  - [**Introduction**](#introduction)
  - [**Server-Side Configurations**](#server-side-configurations)
    - [Setup System (Ubuntu20.04)](#setup-system-ubuntu2004)
    - [Setup Variables](#setup-variables)
    - [Setup Volumes](#setup-volumes)
    - [Start Private Docker Registry](#start-private-docker-registry)
  - [**Client-Side Configurations**](#client-side-configurations)
    - [_macOS_](#macos)
    - [_Windows_](#windows)
    - [_Ubuntu_](#ubuntu)
  - [**Push/Pull Docker Images**](#pushpull-docker-images)
    - [Push Docker Image](#push-docker-image)
    - [Pull Docker Image](#pull-docker-image)

## **Introduction**
TODO

## **Server-Side Configurations**

### Setup System (Ubuntu20.04)
- Install Docker and Docker Compose plugin (see [this guide](https://docs.docker.com/compose/install/linux/))
- Install softwares
```
sudo apt update && sudo apt install ca-certificates
```
### Setup System
- Obtain a Certificate from a CA (Out of Scope)
- Download This Repo @ /home/${USER}/

### Setup Variables
- Change Directory
```
cd /home/${USER}/private-docker-registry
```
- Change MYUSER variable in .env file -> set equal to the output of "echo ${USER}"
- Change CERT_NAME variable in .env file -> set it to the CA certificate name

### Setup Volumes
- Create Docker Registry Volume Folder
```
sudo mkdir -p /mnt/private-docker-registry
```
- Create CA certificate (.key and .crt) folder
```
mkdir -p /home/${USER}/private-docker-registry/certs
```
- Create authentication folder and auth file (htpasswd)
```
mkdir -p /home/${USER}/private-docker-registry/auth
touch /home/${USER}/private-docker-registry/auth
```
### Setup Authentication
- Set user account (username: consultant - password: consultant)
```
htpasswd  -Bbn consultant consultant > /home/${USER}/private-docker-registry/auth/htpasswd
```


### Start Private Docker Registry
```
docker compose up -d
```

## Client Side Configurations
### macOS
- Make sure to have the certificate of the CA (ca.crt) that signed the private registry certificate
```
sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain ca.crt
```
- Append the following entry to the /etc/hosts file. Substitute x.x.x.x with the IP of the Docker Registry Server and ${DOMAIN} with the correct value
```
x.x.x.x private-docker-registry private-docker-registry.DOMAIN.it
```
- Test DNS resolution
```
user@MacBookPro ~ % ping private-docker-registry
PING private-docker-registry (x.x.x.x): 56 data bytes
64 bytes from x.x.x.x: icmp_seq=0 ttl=64 time=9.568 ms
64 bytes from x.x.x.x: icmp_seq=1 ttl=64 time=4.220 ms
64 bytes from x.x.x.x: icmp_seq=2 ttl=64 time=75.928 ms
```
- Open Visual Studio>DockerExtension>REGISTRIES and click on "connect registry"
- Select Generic Docker Registry and substitute DOMAIN with the correct value
```
https://private-docker-registry.DOMAIN.it
```
- Do not set any username/password
- Reboot MacOS

### _Windows_
TODO

### _Ubuntu_
- Make sure to have the certificate of the CA (ca.crt) that signed the private registry certificate
```
sudo cp ca.crt /usr/local/share/ca-certificates/
sudo update-ca-certificates
```
- Append the following entry to the /etc/hosts file. Substitute x.x.x.x with the IP of the Docker Registry Server and ${DOMAIN} with the correct value
```
x.x.x.x private-docker-registry private-docker-registry.DOMAIN.it
```
- Test DNS resolution
```
user@ubuntu ~ % ping private-docker-registry
PING private-docker-registry (x.x.x.x): 56 data bytes
64 bytes from x.x.x.x: icmp_seq=0 ttl=64 time=9.568 ms
64 bytes from x.x.x.x: icmp_seq=1 ttl=64 time=4.220 ms
64 bytes from x.x.x.x: icmp_seq=2 ttl=64 time=75.928 ms
```
- Test
```
docker pull ubuntu:20.04
docker tag ubuntu:20.04 private-docker-registry.DOMAIN.it/my-ubuntu
docker push private-docker-registry.DOMAIN.it/my-ubuntu
docker pull private-docker-registry.DOMAIN.it/my-ubuntu
```

---

## **Push/Pull Docker Images**
### Push Docker Image
- Tag Image (e.g. ubuntu image), substitute \${IMAGE_ID} and ${DOMAIN} with the correct values
```
docker tag ${IMAGE_ID} private-docker-registry.${DOMAIN}.it/ubuntu
```
- Push
```
docker push private-docker-registry.${DOMAIN}.it/ubuntu
```

### Pull Docker Image
```
docker pull private-docker-registry.${DOMAIN}.it/ubuntu
```
