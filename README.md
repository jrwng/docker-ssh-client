# docker-ssh-client

A openssh-client docker image for secure remote login, command execution and file transfer. 

## How to use this image

### Using in GitLab CI/CD

Deploy static site to server.

``` yml
deploy:
  image: jrwng/ssh-client:latest
  stage: deploy
  before_script:
    - eval $(ssh-agent -s)
    - echo "$SSH_PRIVATE_KEY" > deploy.key
    - chmod 0600 deploy.key
    - ssh-add deploy.key
    - mkdir -p ~/.ssh
    - '[[ -f /.dockerenv ]] && echo -e "Host *\n\tStrictHostKeyChecking no\n\n" > ~/.ssh/config'
    - echo "$SSH_KNOWN_HOSTS" >> ~/.ssh/known_hosts
    - chmod 644 ~/.ssh/known_hosts
  script: 
    - scp -C -r public/* server_user@server_host:/usr/share/nginx/html/blog
```

Update docker service.

``` yml
deploy:
  image: jrwng/ssh-client:latest
  stage: deploy
  before_script:
    - eval $(ssh-agent -s)
    - echo "$SSH_PRIVATE_KEY" > deploy.key
    - chmod 0600 deploy.key
    - ssh-add deploy.key
    - mkdir -p ~/.ssh
    - '[[ -f /.dockerenv ]] && echo -e "Host *\n\tStrictHostKeyChecking no\n\n" > ~/.ssh/config'
    - echo "$SSH_KNOWN_HOSTS" >> ~/.ssh/known_hosts
    - chmod 644 ~/.ssh/known_hosts
  script: 
    - ssh server_user@server_host 'docker service update --image $REGISTRY/$CI_PROJECT_NAME:$TAG $CI_PROJECT_NAME'
```

### Using in Docker

``` shell
docker run -it --rm -v "$(pwd)":/tmp jrwng/ssh-client:latest scp /tmp/backup.tar server_user@server_host:/tmp
```