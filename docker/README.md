# Docker guidelines

## Install Docker on Ubuntu 20.04
```
sudo apt install docker.io
sudo apt install docker-compose
sudo systemctl enable --now docker
sudo usermod -aG docker MYUSERNAME
```

## Install additional tools
* [dive](https://github.com/wagoodman/dive) - explore docker images in console.

## Build Dockers for x86_64 and ARM64
```
export REPOSITORY=jurajveverka
export IMAGE_NAME=docker-example
export VERSION=1.0.0
 
# on x86 AMD64 device:
docker build -t ${REPOSITORY}/${IMAGE_NAME}:${VERSION}-amd64 --build-arg ARCH=amd64 --file ./Dockerfile . 
docker push ${REPOSITORY}/${IMAGE_NAME}:${VERSION}-amd64

# on ARM64 v8 device:
docker build -t ${REPOSITORY}/${IMAGE_NAME}:${VERSION}-arm64v8 --build-arg ARCH=arm64v8 --file ./Dockerfile . 
docker push ${REPOSITORY}/${IMAGE_NAME}:${VERSION}-arm64v8

# on x86 AMD64 device: 
docker manifest create \
${REPOSITORY}/${IMAGE_NAME}:${VERSION} \
--amend ${REPOSITORY}/${IMAGE_NAME}:${VERSION}-amd64 \
--amend ${REPOSITORY}/${IMAGE_NAME}:${VERSION}-arm64v8

docker manifest push ${REPOSITORY}/${IMAGE_NAME}:${VERSION}
```
