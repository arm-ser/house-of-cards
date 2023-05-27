#### Docker

<img src="https://github.com/arm-ser/house-of-cards/blob/a611c42de22c306cf98aaaf1be3242cec8c75f3d/logos/youtube.png" width="15" /> [Get Docker organized for easier backups & replication. Trust me, an hour can save you days!](https://www.youtube.com/watch?v=sGtTvV0xbYg)



Youtube Logo
```html
<img src="https://github.com/arm-ser/house-of-cards/blob/a611c42de22c306cf98aaaf1be3242cec8c75f3d/logos/youtube.png" width="15" />
```
Docker Logo
```html
<img src="https://github.com/arm-ser/house-of-cards/blob/d6699680eceb39d2fed1ced15b7a8582ef162ca5/logos/docker.png" width="15" />
```


docker-compose.yml
```yaml

Version: " "

services:
    service-name:
       image: ' '
       container_name: 
       ports:
           - " "
           - " "
       environment:
           ENV_VARIABLE_1:" "
           ENV_VARIABLE_2: " "    
       volumes:
           - ./docker/.....
           - ./docker/.....
       restart: unless:stopped
    
```