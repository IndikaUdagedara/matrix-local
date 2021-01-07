# Running docker compose

1. Change synapse/ma1sd configs
```
docker-compose up
```
2. Go to app.element.io and set homeserver to http://localhost:8080

# Running services individually

1. Generate homeserver config

```
docker run -it --rm     -v /local/path:/data     -e SYNAPSE_SERVER_NAME=my.matrix.host     -e SYNAPSE_REPORT_STATS=yes     matrixdotorg/synapse:latest generate
```

2. Run homeserver

```
docker run -it --rm     -v /local/path/:/data  -p 8008:8008 matrixdotorg/synapse:lates
```

3. Run ma1sd

```
docker run --rm -e MATRIX_DOMAIN=localhost:8008 -v /local/path/ma1sd/var:/var/ma1sd -v /local/path/ma1sd/etc:/etc/ma1sd -p 8090:8090 -t ma1uta/ma1s
```


# Ref
- REST auth provider https://github.com/ma1uta/ma1sd/blob/master/docs/features/authentication.md


install 
```
pip install git+https://github.com/ma1uta/matrix-synapse-rest-password-provider 
```

homeserver.yaml
```
password_providers:
  - module: "rest_auth_provider.RestAuthProvider"
    config:
      endpoint: "http://ma1sd:8090"
```
- https://zingmars.info/2019/12/29/Running-a-personal-Matrix-server-using-docker/