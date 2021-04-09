1. Add docker-compose.yaml
```
version: "3.3"  # optional since v1.27.0
services:
  runner:
    container_name: gitlab-runner
    image: gitlab/gitlab-runner
    ports:
      - 8093:8093
    volumes:
      - ./srv/gitlab-runner/config:/etc/gitlab-runner
      - ./var/run/docker.sock:/var/run/docker.sock
```

2. Register
```
docker exec <container-gitlab-runner-name> -it bin/bash
gitlab-runner register
```
  - Image `alpine:latest`
  - Get from group or spesific runner token in `setting/CI-CD`
  - Change privilage in `config.toml` to `true`
  - Disable shared runner

3. Add .gitlab-ci.yml
```
stages:
  - test

variables:
  http_proxy: ''
  https_proxy: ''
  no_proxy: ''
  DOCKER_HOST: tcp://docker:2375/
  DOCKER_DRIVER: overlay2
  DOCKER_TLS_CERTDIR: ""

# Docker requirement
services:
  - name: docker:dind
    entrypoint: ["env", "-u", "DOCKER_HOST"]
    command: ["dockerd-entrypoint.sh"]

docker-check:
  image: docker:stable
  stage: test
  only:
    - master
  script:
    - docker info
    - echo "Clean up success $test"
```
