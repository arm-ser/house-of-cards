This file contains documentation and information about the **GMKtec Mini PC** used in my home lab.

## Hardware

| Mini PC <img width=167/> | Processor                   | RAM       | Storage    | Ethernet Ports | Price |                     Amazon Link                      |
|:------------------------ |:--------------------------- |:--------- |:---------- |:-------------- |:----- |:----------------------------------------------------:|
| GMKtec Nucbox 8 Mini PC  | Intel Celeron N4100 2.4 GHz | 6GBB DDR4 | ‎128GB SSD | 1 Intel 1GbE   | $130  | [Link](https://www.amazon.com/gp/product/B0BGHGCTPB) |

## Software

- Operating System: **Ubuntu Server**

| Open Source Software <img width=160/>                                           | Description <img width=210/> | Alternative To <img width=200/> |
|:------------------------------------------------------------------------------- |:---------------------------- |:------------------------------- |
| [Duplicati](https://github.com/linuxserver/docker-duplicati)                    | Backup Solution              | Manual Backup                   |
| [NextCloud](https://github.com/nextcloud/docker)                                | Cloud Environment            | Google Drive, OneDrive          |
| [Pi-Hole DNS](https://github.com/pi-hole/pi-hole)                               | DNS Server                   | ISP, Big Tech DNS               |
| [NGINX Proxy Manager](https://github.com/NginxProxyManager/nginx-proxy-manager) | Reverse Proxy                | -                               |


## Docker Compose

These Docker Compose sample files are designed to assist you in building a similar lab environment. They provide basic configuration examples and serve as a useful starting point. Feel free to customize and expand upon them to suit your specific requirements. 

### NGINX Proxy Manager

```yml
# Default Values
# Email:    admin@example.com
# Password: changeme

version: "3"

services:
  app:
    image: 'jc21/nginx-proxy-manager:latest'  
    container_name: npm-application
    ports:
      # These ports are in format <host-port>:<container-port>
      - '80:80' # Public HTTP Port
      - '443:443' # Public HTTPS Port
      - '81:81' # Admin Web Port
    environment:
      # Mysql/Maria connection parameters:
      DB_MYSQL_HOST: db
      DB_MYSQL_PORT: 3306
      DB_MYSQL_USER: "npm"
      DB_MYSQL_PASSWORD: "npm" #CHANGE THIS
      DB_MYSQL_NAME: "npm"
      DISABLE_IPV6: 'true'
    volumes:
      - /home/YOUR-USERNAME/docker/npm/data:/data #CHANGE "YOUR-USERNAME" to your actual username
      - /home/YOUR-USERNAME/docker/npm/letsencrypt:/etc/letsencrypt #CHANGE "YOUR-USERNAME" to your actual username
    restart: unless-stopped
    depends_on:
      - db

  db:
    image: 'jc21/mariadb-aria:latest'
    container_name: npm-database
    environment:
      MYSQL_ROOT_PASSWORD: 'npm' #CHANGE THIS
      MYSQL_DATABASE: 'npm'
      MYSQL_USER: 'npm'
      MYSQL_PASSWORD: 'npm' #CHANGE THIS to match DB_MYSQL_PASSWORD
    volumes:
      - /home/YOUR-USERNAME/docker/npm/mysql:/var/lib/mysql #CHANGE "YOUR-USERNAME" to your actual username
    restart: unless-stopped
```
<img src="https://github.com/arm-ser/house-of-cards/blob/a611c42de22c306cf98aaaf1be3242cec8c75f3d/logos/youtube.png" width="15" />  [NginX Proxy Manager Tutorial](https://www.youtube.com/watch?v=RBVcnxTiIL0)    
<img src="https://github.com/arm-ser/house-of-cards/blob/a611c42de22c306cf98aaaf1be3242cec8c75f3d/logos/youtube.png" width="15" /> [Securing NGinX Proxy Manager](https://www.youtube.com/watch?v=UfCkwlPIozw)    
<img src="https://github.com/arm-ser/house-of-cards/blob/a611c42de22c306cf98aaaf1be3242cec8c75f3d/logos/youtube.png" width="15" /> [Putting it All Together - Docker, Docker-Compose, NGinx Proxy Manager, and Domain Routing](https://www.youtube.com/watch?v=cjJVmAI1Do4)     
<img src="https://github.com/arm-ser/house-of-cards/blob/a611c42de22c306cf98aaaf1be3242cec8c75f3d/logos/youtube.png" width="15" /> [Docker and Running your self-hosted applications in a more secure way behind a reverse proxy.](https://www.youtube.com/watch?v=8T68pB_Fkm4)     

### Pi-Hole, Unbound
```yaml
version: '3.0'

services:
  pihole:
    image: 'cbcrowe/pihole-unbound:latest'
    container_name: pihole
    hostname: pihole
    ports:
    - "53:53/tcp" # DNS tcp port
    - "53:53/udp" # DNS udp port
    - "5380:80/tcp" # web port
    environment:
      - WEBPASSWORD=${WEBPASSWORD}
      - PIHOLE_DNS_="127.0.0.1#5335"
      - DNSSEC="true"
      - DNSMASQ_LISTENING="single"
    volumes:
      - /home/YOUR-USERNAME/docker/pihole/etc-pihole-unbound:/etc/pihole:rw #CHANGE "YOUR-USERNAME" to your actual username
      - /home/YOUR-USERNAME/docker/pihole/etc_pihole_dnsmasq-unbound:/etc/dnsmasq.d:rw #CHANGE "YOUR-USERNAME" to your actual username
    restart: unless-stopped
```

Note: Do not Forget to Define WEBPASSWORD variable. Here is how to do it in Portainer. When creating stack, push "+ Add an environment variable" and fill up the name and value fields

![pihole-web-pass](/logos-screenshots/pihole-web-pass.png)  
<img src="https://github.com/arm-ser/house-of-cards/blob/d6699680eceb39d2fed1ced15b7a8582ef162ca5/logos/docker.png" width="15" /> [Pi-Hole Unbound GIT](https://github.com/chriscrowe/docker-pihole-unbound)   

Here is another way of configuring pihole and unbound: <img src="https://github.com/arm-ser/house-of-cards/blob/a611c42de22c306cf98aaaf1be3242cec8c75f3d/logos/youtube.png" width="15" /> [WireHole: WireGuard, Pi-Hole and Unbound in Docker](https://www.youtube.com/watch?v=DOJ39lyx6Js)  

### NextCloud
```yaml

version: '3'

services:
  app:
    image: 'nextcloud'
    container_name: nextcloud
    ports:
      - "5080:80"
    environment:
      - MYSQL_PASSWORD="password"  #CHANGE THIS
      - MYSQL_DATABASE="nextcloud"
      - MYSQL_USER="nextcloud"
      - MYSQL_HOST="db"
    volumes:
      - /home/YOUR-USERNAME/docker/nextcloud/data:/var/www/html #CHANGE "YOUR-USERNAME" to your actual username
    restart: always
    links:
      - db

  db:
    image: 'mariadb'
    container_name: nextcloud-db
    environment:
      - MYSQL_ROOT_PASSWORD="password" #CHANGE THIS
      - MYSQL_PASSWORD="password" #CHANGE THIS to match the MYSQL_PASSWORD above
      - MYSQL_DATABASE="nextcloud"
      - MYSQL_USER="nextcloud"
    volumes:
      - /home/YOUR-USERNAME/docker/nextcloud/db:/var/lib/mysql #CHANGE "YOUR-USERNAME" to your actual username
    restart: always
    command: --transaction-isolation=READ-COMMITTED --binlog-format=ROW --innodb-file-per-table=1 --skip-innodb-read-only-compressed
    
```

Here is another way of configuring nextcloud: <img src="https://github.com/arm-ser/house-of-cards/blob/a611c42de22c306cf98aaaf1be3242cec8c75f3d/logos/youtube.png" width="15" /> [Nextcloud One Container Tutorial](https://www.youtube.com/watch?v=OCLq62KOqNU)

### Duplicati
```yaml
version: "3"

services:
  duplicati:
    image: 'lscr.io/linuxserver/duplicati'
    container_name: duplicati
    ports:
      - "8200:8200"
    environment:
      - PUID="0"
      - PGID="0"
      - TZ="America/Los_Angeles" #CHANGE THIS to match your timezone
    volumes:
      - /home/YOUR-USERNAME/docker/duplicati/config:/config #CHANGE "YOUR-USERNAME" to your actual username
      - /pat/to/backup/location/duplicati/backups:/backups #CHANGE /pat/to/backup/location/ to the location where you watn to backup your data (Example Location is Where NAS is mounted)
    restart: unless-stopped
```

<img src="https://github.com/arm-ser/house-of-cards/blob/a611c42de22c306cf98aaaf1be3242cec8c75f3d/logos/youtube.png" width="15" /> [Duplicati a Set it and Forget it backup tool for local and remote backups](https://www.youtube.com/watch?v=N1NRvg4KaDE)

##### Resources
[📄 List of tz database time zones](https://infogalactic.com/info/List_of_tz_database_time_zones)   
[📄 8 Reasons Why You Should Use a Reverse Proxy in Your DMZ](https://www.jscape.com/blog/top-8-benefits-of-a-reverse-proxy)   
[📄 What to Consider When Setting Up DMZ's Reverse Proxy & Firewall](https://www.jscape.com/blog/considerations-when-setting-up-your-dmz-s-reverse-proxy-and-firewall)   
[📄 FTLDNS and Unbound Combined For Your Own All-Around DNS Solution](https://pi-hole.net/blog/2018/06/09/ftldns-and-unbound-combined-for-your-own-all-around-dns-solution/#page-content)    
[📄 Wildcard Let's Encrypt certificates with Nginx Proxy Manager and Cloudflare](https://blog.jverkamp.com/2023/03/27/wildcard-lets-encrypt-certificates-with-nginx-proxy-manager-and-cloudflare/)     
   


