# dockervers
Docker servers

## Usage

- Rename `env.example` to `.env`

```
cp env.example .env
```

- Configurate your code path in `.env`

```
# Point to the path of your applications code on your host
APP_CODE_PATH_HOST=

# Choose storage path on your machine. For all storage systems
DATA_PATH_HOST=

# Docker host ip
DOCKER_HOST_IP=
```

- Run your container

```
docker-compose up -d php-fpm nginx mysql redis
```
