# Mattermost Docker

A fork of the [official Docker deployment](https://docs.mattermost.com/install/install-docker.html) solution for Mattermost which is specifically configured to run with [nginx-docker](https://github.com/ffrosch/nginx-docker) aka `nginx-proxy`.

## Install

Install and run [nginx-docker](https://github.com/ffrosch/nginx-docker) first.

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

### Configuration

Create a new `.env`:

```shell
cp env.example .env
```

Copy general configurations to `nginx-proxy`:

```shell
docker cp ./nginx/conf.d/mattermost_cache.conf nginx-proxy:/etc/nginx/conf.d/mattermost_cache.conf
```

#### Local development

No other changes are necessary for development on `localhost`.

#### Production

Change at least these environment variables in `.env`:

- `DOMAIN`: default is `localhost`
- `POSTGRES_USER`
- `POSTGRES_PASSWORD`
- `LETSENCRYPT_EMAIL`
- `LETSENCRYPT_TEST`: set to `false` to use the production API for production SSL certificates

Set your `DOMAIN` to the same value in `.env` and the shell. Copy `location` block specific settings to `nginx-proxy`:

```shell
# must match the `DOMAIN` in `.env`
DOMAIN=localhost

docker cp ./nginx/vhost.d/mattermost_location nginx-proxy:/etc/nginx/vhost.d/${DOMAIN}_location
```

## Usage

### Localhost

Run the container:

```shell
docker compose up -d
```

It might take a moment until it's up and running. Go to:

```shell
http://localhost
```

### Localhost NGROK

Run **ngrok** as a docker container:

```shell
docker run --net=host -it -e NGROK_AUTHTOKEN=<your-token> ngrok/ngrok:latest http 80
```

From the **ngrok** output copy the domain-part of the `Forwarding` address (exclude `https://`). Keep **ngrok** running. Start a new shell, assign the `DOMAIN` variable and run the container:

```shell
export DOMAIN=<ngrok-domain>; docker compose up -d
```

After a moment you will be able to access your mattermost service with `https` over the **ngrok**-address!

For testing `https` it would be good to connect locally and not via `ngrok`.

```shell
# Stop normal DNS-Server
sudo systemctl stop systemd-resolved

# Listen at standard address for normal DNS-Server
# use Google DNS (8.8.8.8)
# listen locally on all subdomains of localhost
# NOTE: DO NOT use the domain "local", it does not work (reserved or something)
sudo dnsmasq --listen-address 127.0.0.53 --server=8.8.8.8 --no-daemon --address=/*.localhost/127.0.0.1

# Restart normal DNS-Server
sudo systemctl stop systemd-resolved
sudo systemctl start systemd-resolved
```

### Localhost Throw-away

Check it out real quick:

```shell
docker compose -f docker-compose.mattermost.yml -f docker-compose.without-nginx.yml up -d
```

Go to:

```shell
http://localhost:8065
```

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
