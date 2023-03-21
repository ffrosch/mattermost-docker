# Mattermost Docker

A fork of the official Docker deployment solution for Mattermost.

It is specifically configured to run with [nginx-docker](https://github.com/ffrosch/nginx-docker) which will also be refered to as `nginx-proxy`, which is also the name of that project's nginx-container and that project't network to which all proxied containers must connect.

## Install

See [Mattermost Docker deployment guide](https://docs.mattermost.com/install/install-docker.html) for general instructions.

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

**Note**: you need to install and run [nginx-docker](https://github.com/ffrosch/nginx-docker) first.

Copy the configuration files to `nginx-proxy`. If you are not on `localhost` give the correct domain name (same you specified in `.env`):

```shell
DOMAIN=localhost

docker cp ./nginx/vhost.d/my.domain.com_location nginx-proxy:/etc/nginx/vhost.d/${DOMAIN}_location

docker cp ./nginx/conf.d/mattermost_proxy.conf nginx-proxy:/etc/nginx/conf.d/mattermost_proxy.conf
```

### Localhost

Run the container:

```shell
docker compose up -d
```

It might take a moment until it's up and running. Go to:

```shell
http://localhost  # or your domain if you are running it in production
```

### Localhost NGROK

Run **ngrok** as a docker container:

```shell
docker run --net=host -it -e NGROK_AUTHTOKEN=<your-token> ngrok/ngrok:latest http 80
```

From the **ngrok** output copy the domain-part of the `Forwarding` address (exclude "https://"). Keep **ngrok** running. Start a new shell, assign the `DOMAIN` variable and run the container:

```shell
DOMAIN=<ngrok-domain>; docker compose up -d
```

After a moment you will be able to access your mattermost service with `https` over the **ngrok**-address!

## Usage for local testing without Nginx

```shell
docker compose -f docker-compose.mattermost.yml -f docker-compose.without-nginx.yml up -d
```

Go to `localhost:8065`.

## Update

Add the original repo as an upstream repo

```shell
git remote add upstream https://github.com/mattermost/docker.git
```

The easiest way to synchronise the github repo AND the local copy is using the github cli

```shell
# use the -force flag if sync is not possible due to conflicts
gh repo sync ffrosch/mattermost-docker -b main
```

Alternatively vanilla git can be used

```shell
git fetch upstream
git checkout main
git merge upstream/main
git push
```
