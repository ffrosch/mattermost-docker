# Mattermost Docker

A fork of the official Docker deployment solution for Mattermost.

It is specifically configured to run with [nginx-docker](https://github.com/ffrosch/nginx-docker) which will also be refered to as `nginx-proxy`, which is also the name of that project's nginx-container and that project't network to which all proxied containers must connect.

## Install

Also refer to the [Mattermost Docker deployment guide](https://docs.mattermost.com/install/install-docker.html) for general instructions and gotchas.

To take full advantage of this repo first install and run [nginx-docker](https://github.com/ffrosch/nginx-docker).

Get the code:

```shell
git clone https://github.com/ffrosch/mattermost-docker ./mattermost-docker
cd mattermost-docker
```

Create the required directories and set their permissions:

```shell
mkdir -p ./volumes/app/mattermost/{config,data,logs,plugins,client/plugins,bleve-indexes}
sudo chown -R 2000:2000 ./volumes/app/mattermost
```

Create a new `.env`:

```shell
cp env.example .env
```

For use in development on a `localhost` no other changes are necessary.

For production use change at least these environment variables in `.env`:

- `DOMAIN`
- `POSTGRES_USER`
- `POSTGRES_PASSWORD`
- `LETSENCRYPT_EMAIL`
- `LETSENCRYPT_TEST`: set to `false` to use the production API for production SSL certificates

## Usage with `nginx-proxy`

Copy the configuration files to `nginx-proxy`. If you are not on `localhost` give the correct domain name (same you specified in `.env`):

```shell
DOMAIN=localhost

docker cp ./nginx/vhost.d/my.domain.com_location nginx-proxy:/etc/nginx/vhost.d/${DOMAIN}_location

docker cp ./nginx/conf.d/mattermost_proxy.conf nginx-proxy:/etc/nginx/conf.d/mattermost_proxy.conf
```

Now run the container:

```shell
docker compose up -d
```

It might take a moment until it's up and running. Go to:

```shell
http://localhost  # or your domain if you are running it in production
```

## Usage for local testing without Nginx

```shell
docker compose -f docker-compose.mattermost.yml -f docker-compose.without-nginx.yml up -d
```

Go to `localhost:8065`.
