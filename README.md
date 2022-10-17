# Private Docker Registry

## **Table of Contents**
- [Private Docker Registry](#private-docker-registry)
  - [**Table of Contents**](#table-of-contents)
  - [**Introduction**](#introduction)
  - [**Server-Side Configurations**](#server-side-configurations)
    - [Setup System (Ubuntu20.04)](#setup-system-ubuntu2004)
    - [Setup Variables & Certificates](#setup-variables--certificates)
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
- Download This Repo @ /home/${USER}/
### Setup Variables & Certificates
- Change Directory
```
cd /home/${USER}/private-docker-registry
```
- Change MYUSER variable in .env file -> set equal to the output of "echo ${USER}"
- Change DOMAIN variable in .env file -> set DOMAIN to your desired value (e.g. DOMAIN=mydomain)
- Export DOMAIN variable
```
export DOMAIN=mydomain
```
- Generate Self-Signed Certificate
```
openssl req -newkey rsa:4096 -nodes -sha256 -keyout certs/${DOMAIN}.key -addext "subjectAltName = DNS:private-docker-registry.${DOMAIN}.it" -x509 -days 365 -out certs/${DOMAIN}.crt
```
- Setup Certificate
```
sudo cp certs/${DOMAIN}.crt /usr/local/share/ca-certificates/private-docker-registry.${DOMAIN}.it.crt && sudo update-ca-certificates
```

### Setup Volumes
- Create Docker Registry Volume Folder
```
sudo mkdir -p /mnt/private-docker-registry
```

### Start Private Docker Registry
```
docker compose up -d
```
---
## **Client-Side Configurations**
### _macOS_
- Copy Self-Signed Certificate on macOS
- Setup Self-Signed Certificate: in the following command, substitute ${DOMAIN} with the value used on the server-side configuration
```
sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain ${DOMAIN}.crt
```
- Append the following entry to the /etc/hosts file. Substitute x.x.x.x with the IP of the Docker Registry Server and ${DOMAIN} with the value used on the server-side configuration
```
x.x.x.x private-docker-registry private-docker-registry.${DOMAIN}.it
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
- Select Generic Docker Registry use the following URI. Remember to substitute ${DOMAIN} with the value used on the server-side configuration.
```
https://private-docker-registry.${DOMAIN}.it
```
- Do not set any username/password
- Reboot MacOS

### _Windows_
TODO

### _Ubuntu_
TODO

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
