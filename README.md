# private-docker-registry

## Server Configuration Guide

### Setup System
- Obtain a Certificate from a CA (Out of Scope)
- Download This Repo @ /home/${USER}/

### Setup Variables & Certificates
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
- Append the following entry to the /etc/hosts file. Substitute x.x.x.x with the IP of server that hosts the Docker Registry Server and DOMAIN with the correct value
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

## PUSH/PULL Docker Images
### PUSH
- Tag Image (e.g. ubuntu image), substitute \${IMAGE_ID} and ${DOMAIN} with the correct values
```
docker tag ${IMAGE_ID} private-docker-registry.${DOMAIN}.it/ubuntu
```
- Push
```
docker push private-docker-registry.${DOMAIN}.it/ubuntu
```

### PULL
```
docker pull private-docker-registry.${DOMAIN}.it/ubuntu
```