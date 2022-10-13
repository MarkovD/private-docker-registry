# private-docker-registry

## Server Configuration Guide

### Setup System
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
- Change DOMAIN variable in .env file -> set it to your desired value (e.g. DOMAIN=mydomain)
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

## Client Side Configurations
### macOS
- Copy Self-Signed Certificate on macOS
- Setup Self-Signed Certificate: substitute ${DOMAIN} to the correct value
```
sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain ${DOMAIN}.crt
```
- Reboot MacBook
- Open Visual Studio>DockerExtension>REGISTRIES and click on "connect registry"
- Select Generic Docker Registry and substitute ${DOMAIN} to the correct value
```
https://private-docker-registry.${DOMAIN}.it
```
- Do not set any username/password

## PUSH/PULL Docker Images
### PUSH
- Tag Image (e.g. ubuntu image), substitute \${IMAGE_ID} and ${DOMAIN} to the correct values
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