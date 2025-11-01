## Example

```yaml
services:
  qbittorrent:
    image: lscr.io/linuxserver/qbittorrent:latest
    container_name: qbittorrent
    platform: linux/arm64
    hostname: qbittorrent
    networks:
      macvlan_net:
        ipv4_address: 192.168.0.9
    environment:
      TZ: 'Asia/Kolkata'
      PUID: '0'
      PGID: '0'
      WEBUI_PORT: '80'
      TORRENTING_PORT: '6881'
    volumes:
      - ./config:/config
      - /mnt/share/jellyfin/movies:/movies
      - /mnt/share/jellyfin/shows:/shows
      - /mnt/share/jellyfin/music:/music
    restart: unless-stopped

networks:
  macvlan_net:
    external: true
```