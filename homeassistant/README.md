## Example

```yaml
services:
  homeassistant:
    image: ghcr.io/home-assistant/home-assistant:stable
    container_name: homeassistant
    platform: linux/arm64
    hostname: homeassistant
    networks:
      macvlan_net:
        ipv4_address: 192.168.0.7
    environment:
      TZ: 'Asia/Kolkata'
    volumes:
      - ./config:/config
      - /etc/localtime:/etc/localtime:ro
    restart: unless-stopped
    privileged: true


networks:
  macvlan_net:
    external: true
```