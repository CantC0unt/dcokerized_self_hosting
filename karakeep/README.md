## Example

```yaml
services:
  web:
    image: ghcr.io/karakeep-app/karakeep:release
    container_name: karakeep
    hostname: karakeep
    networks:
      macvlan_net:
        ipv4_address: 192.168.0.10
    volumes:
      - ./data:/data
      - ./metrics:/metrics
    environment:
      MEILI_ADDR: http://192.168.0.12:7700
      PORT: 80
      BROWSER_WEB_URL: http://192.168.0.11:9222
      NEXTAUTH_SECRET: HM0NyQbem22X7f6e1w2+OH22LGMgSfQR5f4JL8WlFJ2qcPep
      MEILI_MASTER_KEY: HDYcaT3Zlcx+Aj0JjGYv5e3vGUW0YGdyf1jy1XaUo5ETc0e6
      NEXTAUTH_URL: http://kara.keep
      # OPENAI_API_KEY: ...
      DATA_DIR: /data # DON'T CHANGE THIS
    restart: unless-stopped
  chrome:
    image: gcr.io/zenika-hub/alpine-chrome:124
    container_name: chrome
    hostname: chrome
    networks:
      macvlan_net:
        ipv4_address: 192.168.0.11
    command:
      - --no-sandbox
      - --disable-gpu
      - --disable-dev-shm-usage
      - --remote-debugging-address=0.0.0.0
      - --remote-debugging-port=9222
      - --hide-scrollbars
    restart: unless-stopped
  meilisearch:
    image: getmeili/meilisearch:v1.13.3
    container_name: meilisearch
    hostname: meilisearch
    networks:
      macvlan_net:
        ipv4_address: 192.168.0.12
    environment:
      MEILI_NO_ANALYTICS: "true"
    volumes:
      - ./meilisearch:/meili_data
    restart: unless-stopped

networks:
  macvlan_net:
    external: true
```


## "The Guide"

### Install Directory

The install can be done in any directory, I am going to use a new directory in home for convenience. If you choose to install somewhere else, dont forget to make the change for the install directory in the other commands.

```bash
mkdir ~/dockerized_karakeep
```


### Karakeep Compose (web)

![Static Badge](https://img.shields.io/badge/image-karakeep--app/karakeep:release-pink)

[![GitHub Release](https://img.shields.io/github/v/release/karakeep-app/karakeep)](https://github.com/karakeep-app/karakeep)

[![GitHub Repo stars](https://img.shields.io/github/stars/karakeep-app/karakeep)](https://github.com/karakeep-app/karakeep)
[![GitHub forks](https://img.shields.io/github/forks/karakeep-app/karakeep)](https://github.com/karakeep-app/karakeep)
[![GitHub License](https://img.shields.io/github/license/karakeep-app/karakeep)](https://github.com/karakeep-app/karakeep)
[![GitHub last commit](https://img.shields.io/github/last-commit/karakeep-app/karakeep)](https://github.com/karakeep-app/karakeep)

```yaml
  web:
    image: ghcr.io/karakeep-app/karakeep:release
    container_name: karakeep
    hostname: karakeep
    networks:
      macvlan_net:
        ipv4_address: <static ip for karakeep>
    volumes:
      - ./data:/data
      - ./metrics:/metrics
    environment:
      PORT: 80
      MEILI_ADDR: http://<static ip for meilisearch>:7700
      BROWSER_WEB_URL: http://<static ip for chrome>:9222
      NEXTAUTH_URL: http://<dns for karakeep server>
      NEXTAUTH_SECRET: <secret string>
      MEILI_MASTER_KEY: <secret string>
      # OPENAI_API_KEY: ...
      DATA_DIR: /data # DON'T CHANGE THIS
    restart: unless-stopped
```

|value|replace with|conditions|
|-|-|-|
|`<static ip for karakeep>`|Any local IP|It _**MUST NOT**_ be assigned to another device by your DHCP server.<br> Use an IP outside of DHCP range.
|`<dns for karakeep server>`|local DNS or IP|It _**MUST**_ be assigned to karakeep.<br> Can be the same as `<static ip for karakeep>`
|`<static ip for meilisearch>`|Any local IP|It _**MUST**_ be assigned to melisearch
|`<static ip for chrome>`|Any local IP|It _**MUST**_ be assigned to browserless chrome
|`<secret string>`|Any secret string|Run `openssl rand -base64 36` to get the value.<br> Re-run for every instance.


### Karakeep Volumes

```bash
mkdir ~/dockerized_karakeep/data ~/dockerized_karakeep/metrics
```
|volume|mapped volume|uses|
|-|-|-|
|`./data`|`/data`|Store DB and Assets
|`./metrics`|`/metrics`|Store metrics for prometheus


### Browserless Chrome Compose 

![Static Badge](https://img.shields.io/badge/image-zenika--hub/alpine--chrome:124-pink)

[![Docker Image Version](https://img.shields.io/docker/v/zenika/alpine-chrome/124)](https://hub.docker.com/r/zenika/alpine-chrome)
[![Docker Image Size](https://img.shields.io/docker/image-size/zenika/alpine-chrome/124)](https://hub.docker.com/r/zenika/alpine-chrome)
[![Docker Pulls](https://img.shields.io/docker/pulls/zenika/alpine-chrome)](https://hub.docker.com/r/zenika/alpine-chrome)
[![Docker Stars](https://img.shields.io/docker/stars/zenika/alpine-chrome)](https://hub.docker.com/r/zenika/alpine-chrome)

[![GitHub Repo stars](https://img.shields.io/github/stars/jlandure/alpine-chrome)](https://github.com/jlandure/alpine-chrome)
[![GitHub forks](https://img.shields.io/github/forks/jlandure/alpine-chrome)](https://github.com/jlandure/alpine-chrome)
[![GitHub License](https://img.shields.io/github/license/jlandure/alpine-chrome)](https://github.com/jlandure/alpine-chrome)
[![GitHub last commit](https://img.shields.io/github/last-commit/jlandure/alpine-chrome)](https://github.com/jlandure/alpine-chrome)

```yaml
  chrome:
    image: gcr.io/zenika-hub/alpine-chrome:124
    container_name: chrome
    hostname: chrome
    networks:
      macvlan_net:
        ipv4_address: <static ip for chrome>
    command:
      - --no-sandbox
      - --disable-gpu
      - --disable-dev-shm-usage
      - --remote-debugging-address=0.0.0.0
      - --remote-debugging-port=9222
      - --hide-scrollbars
    restart: unless-stopped
```


|value|replace with|conditions|
|-|-|-|
|`<static ip for chrome>`|Any local IP|It _**MUST NOT**_ be assigned to another device by your DHCP server.<br> Use an IP outside of DHCP range.

\* change `remote-debugging-port=9222` to change the port used in case of conflicts, but also remember to change the port in karakeep compose if you do.


### Meilisearch Compose

![Static Badge](https://img.shields.io/badge/image-getmeili/meilisearch:v1.13.3-pink)

[![Docker Image Version](https://img.shields.io/docker/v/getmeili/meilisearch/v1.13.3)](https://hub.docker.com/r/getmeili/meilisearch)
[![Docker Image Size](https://img.shields.io/docker/image-size/getmeili/meilisearch/v1.13.3)](https://hub.docker.com/r/getmeili/meilisearch)
[![Docker Pulls](https://img.shields.io/docker/pulls/getmeili/meilisearch)](https://hub.docker.com/r/getmeili/meilisearch)
[![Docker Stars](https://img.shields.io/docker/stars/getmeili/meilisearch)](https://hub.docker.com/r/getmeili/meilisearch)

[![GitHub Repo stars](https://img.shields.io/github/stars/meilisearch/meilisearch)](https://github.com/meilisearch/meilisearch)
[![GitHub forks](https://img.shields.io/github/forks/meilisearch/meilisearch)](https://github.com/meilisearch/meilisearch)
[![GitHub License](https://img.shields.io/github/license/meilisearch/meilisearch)](https://github.com/meilisearch/meilisearch)
[![GitHub last commit](https://img.shields.io/github/last-commit/meilisearch/meilisearch)](https://github.com/meilisearch/meilisearch)

```yaml
  meilisearch:
    image: getmeili/meilisearch:v1.13.3
    container_name: meilisearch
    hostname: meilisearch
    networks:
      macvlan_net:
        ipv4_address: <static ip for meilisearch>
    environment:
      MEILI_NO_ANALYTICS: "true"
    volumes:
      - ./meilisearch:/meili_data
    restart: unless-stopped
```

|value|replace with|conditions|
|-|-|-|
|`<static ip for meilisearch>`|Any local IP|It _**MUST NOT**_ be assigned to another device by your DHCP server.<br> Use an IP outside of DHCP range.


### Meilisearch Volumes

```bash
mkdir ~/dockerized_karakeep/meilisearch
```

|volume|mapped volume|uses|
|-|-|-|
|`./meilisearch`|`/meili_data`|Storage for meilisearch


### Run

\* macvlan_net must be up before this is started

\** you can replace the network configuration in the compose file with the one from `macvlan_net/docker-compose.yml`

```bash
docker compose -f ~/dockerized_karakeep/docker-compose.yml up -d
```


### Karakeep Docker Logs

```bash
docker logs karakeep
```


### Browserless Chrome Docker Logs

```bash
docker logs chrome
```


### Meilisearch Docker Logs

```bash
docker logs meilisearch
```

