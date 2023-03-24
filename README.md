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
cp .env.example .env
```

Copy general configurations to `nginx-proxy`:

```shell
docker cp ./nginx/conf.d/mattermost_cache.conf nginx-proxy:/etc/nginx/conf.d/mattermost_cache.conf
```

#### Local development

No other changes are necessary for development on `localhost`.

#### Production

Change at least these environment variables in `.env`:

- `VIRTUAL_HOST`: default is `localhost`
- `POSTGRES_USER`
- `POSTGRES_PASSWORD`
- `LETSENCRYPT_TEST`: default is `true`; set to `false` to use the production API for production SSL certificates

Copy `location` block specific settings to `nginx-proxy`:

```shell
# must match the `VIRTUAL_HOST` in `.env`
VIRTUAL_HOST=localhost

docker cp ./nginx/vhost.d/mattermost_location nginx-proxy:/etc/nginx/vhost.d/${VIRTUAL_HOST}_location
```

## Usage

### Localhost

See instructions at [nginx-proxy](https://github.com/ffrosch/nginx-docker).

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

## Upgrade Mattermost Version

See [Mattermost Docker | Install a different version of Mattermost](https://docs.mattermost.com/install/install-docker.html#installing-a-different-version-of-mattermost)
