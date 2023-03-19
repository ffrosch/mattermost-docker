# Mattermost Docker
A fork of the official Docker deployment solution for Mattermost.

## Install

Also refer to the [Mattermost Docker deployment guide](https://docs.mattermost.com/install/install-docker.html) for instructions on how to install and use this.


```shell
# Copy repo
git clone https://github.com/ffrosch/mattermost-docker ./mattermost-docker
cd mattermost-docker

# Copy standard .env
cp env.example .env

# Create the required directories and set their permissions
mkdir -p ./volumes/app/mattermost/{config,data,logs,plugins,client/plugins,bleve-indexes}
sudo chown -R 2000:2000 ./volumes/app/mattermost
```

Set environment variables

```shell
# !IMPORTANT!
DOMAIN=localhost

POSTGRES_USER=mmuser
POSTGRES_PASSWORD=mmuser_password
```

Transfer environment variables to `.env`

```shell
sed -i "s/DOMAIN=mm.example.com/DOMAIN=${DOMAIN}/g" .env
sed -i "s/POSTGRES_USER=mmuser/POSTGRES_USER=${POSTGRES_USER}/g" .env
sed -i "s/POSTGRES_PASSWORD=mmuser_password/POSTGRES_PASSWORD=${POSTGRES_PASSWORD}/g" .env
```

## Usage

```shell
docker compose -f docker-compose.yml -f docker-compose.without-nginx.yml up -d
```

Go to `localhost:8065`.
