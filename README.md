# Local installation of synapse + ma1sd behind nginx

Following are steps run a local instance of matrix services. It has a 
- nginx reverse proxy (config in `./proxy`) 
- synapse server (config in `./synapse`)
- ma1sd server (config in `./ma1sd`)
- a web server to test reverse proxy configuration (config in `./web`) 


ma1sd intercepts certain endpoints and handles authentication/directory services. The interception is configured in nginx configuration based on https://github.com/ma1uta/ma1sd/blob/master/docs/features/authentication.md 

To use ma1sd, synpase needs to be configured to use a password provider. This requires an external module installed on the base synapse docker image and is set up in `Dockerfile.synapse`

## Running via docker compose

1. Change relevant config files and run `docker-compose up`. This will build the customised synapse container and start `nginx`, `synapse` and `ma1sd`. Docker compose sets up container networking and containers can access other containers using the container name e.g. `http://ma1sd:8090` is the `ma1sd` endpoint when accessed via `nginx` or `synapse`

2. Go to app.element.io and set homeserver to `http://localhost:8080`

3. To change configuration, kill the `docker-compose` command, update config files and run `docker-compose up` again.

## Running services individually

1. Generate homeserver config

```
docker run -it --rm -v /local/path:/data -e SYNAPSE_SERVER_NAME=my.matrix.host -e SYNAPSE_REPORT_STATS=yes matrixdotorg/synapse:latest generate
```

2. Run homeserver

```
docker run -it --rm -v /local/path/:/data -p 8008:8008 matrixdotorg/synapse:latest
```

3. Run ma1sd

```
docker run --rm -e MATRIX_DOMAIN=localhost:8008 -v /local/path/ma1sd/var:/var/ma1sd -v /local/path/ma1sd/etc:/etc/ma1sd -p 8090:8090 -t ma1uta/ma1sd:latest
```


## References

- REST auth provider https://github.com/ma1uta/ma1sd/blob/master/docs/features/authentication.md

Configure synapse password provider: 

1. install `pip install git+https://github.com/ma1uta/matrix-synapse-rest-password-provider`

2. modify homeserver.yaml
```
password_providers:
  - module: "rest_auth_provider.RestAuthProvider"
    config:
      endpoint: "http://ma1sd:8090"
```
- https://zingmars.info/2019/12/29/Running-a-personal-Matrix-server-using-docker/