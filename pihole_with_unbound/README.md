## Example

```yaml
services:

  unbound:
    container_name: unbound
    image: klutchell/unbound:latest
    platform: linux/arm64
    hostname: unbound
    networks:
      macvlan_net:
        ipv4_address: 192.168.0.5
    volumes:
      - './unbound:/etc/unbound/custom.conf.d'
    cap_add:
      - NET_BIND_SERVICE
    restart: unless-stopped

  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    platform: linux/arm64
    hostname: pihole
    networks:
      macvlan_net:
        ipv4_address: 192.168.0.6
        ipv6_address: fd00::6
    environment:
      TZ: 'Asia/Kolkata'
      FTLCONF_webserver_api_password: 'pihole'
      FTLCONF_dns_listeningMode: 'all'
      FTLCONF_dns_upstreams: '192.168.0.5#53'
      FTLCONF_dhcp_start: '192.168.0.15'
      FTLCONF_dhcp_end: '192.168.0.249'
      FTLCONF_dhcp_router: '192.168.0.1'
      FTLCONF_dhcp_active: 'true'
      FTLCONF_dhcp_ipv6: 'true'
      DNSMASQ_USER: 'root'
    depends_on:
      - unbound
    volumes:
      - './etc-pihole:/etc/pihole'
      - './etc-dnsmasq.d:/etc/dnsmasq.d'
    cap_add:
      - CHOWN
      - NET_ADMIN
      - NET_BIND_SERVICE
      - NET_RAW
      - SYS_TIME
      - SYS_NICE
    restart: unless-stopped

networks:
  macvlan_net:
    external: true
```