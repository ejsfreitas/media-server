# media-server

A complete plex media server based only on docker images to run on Raspberry Pi 2/3

## Install

Make sure that you have docker,  docker-compose and git installed on your system.

```
$ curl -sSL https://get.docker.com | sh
$ sudo apt-get install docker-compose git
```

## Configuration

Clone this repository and create and environment configuration file with your settings.

Get the plex token from Plex website: https://plex.tv/claim

```
git clone https://github.com/ejsfreitas/media-server.git
cd media-server
cp .env.sample .env
```


If you want to remove the downloads when they are finished add the following configuration to transmission. With the transmission docker image stopped, edit the file:

```
sudo vim <transmission_path>/transmission/config/settings.json
```

And change the following lines:
```
"ratio-limit": 0,
"ratio-limit-enabled": true
```

If you want to access all the tools from anywhere using https you can add the https-portal docker image to the docker-compose.yaml

```
https-portal:
  image: ejsfreitas/https-portal:armv7-1.0.0
  container_name: https-portal
  restart: always
  environment:
    - PUID=${USER_ID}
    - PGID=${GROUP_ID}
  ports:
    - '80:80'
    - '443:443'
  environment:
    DOMAINS: 'jackett.${DOMAIN_NAME} -> http://jackett:9117,
              radarr.${DOMAIN_NAME} -> http://radarr:7878,
              sonarr.${DOMAIN_NAME} -> http://sonarr:8989,
              transmission.${DOMAIN_NAME} -> http://transmission:9091,
              plex.${DOMAIN_NAME} -> http://plex:32400'
    STAGE: 'production'
    FORCE_RENEW: 'true'
  depends_on:
    - transmission
    - sonarr
    - radarr
    - jackett
    - plex
```

## Usage

To start all docker images:
```
$ sudo docker-compose up -d
```

You can now access all the tools on the following URLs or using your domains.
```
- jackett - http://<IP>:9117
- radarr - http://<IP>:7878
- sonarr - http://<IP>:8989
- transmission - http://<IP>:9091
- Plex Server - http://<IP>:32400
```
