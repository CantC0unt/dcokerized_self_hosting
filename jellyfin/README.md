## Example

```yaml
services:
  jellyfin:
    image: jellyfin/jellyfin
    container_name: jellyfin
    platform: linux/arm64
    hostname: homeassistant
    networks:
      macvlan_net:
        ipv4_address: 192.168.0.8
    cap_add:
      - NET_BIND_SERVICE
    volumes:
      - ./config:/config
      - ./cache:/cache
      - type: bind
        source: /mnt/share/jellyfin/music
        target: /music
      - type: bind
        source: /mnt/share/jellyfin/movies
        target: /movies
      - type: bind
        source: /mnt/share/jellyfin/shows
        target: /shows
      - type: bind
        source: ./fonts
        target: /usr/local/share/fonts/custom
        read_only: true
    restart: unless-stopped
    environment:
      - JELLYFIN_PublishedServerUrl=http://jelly.fin
    extra_hosts:
      - 'host.docker.internal:host-gateway'

networks:
  macvlan_net:
    external: true
```