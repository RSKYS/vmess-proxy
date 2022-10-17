# VMess Proxy with an IR Server as a Router

For this you need two servers, one in Iran and the other outside of Iran.</br>
you can google it or using websites like [Sindad](https://sindad.com/) or [ParsPack](https://parspack.com/) or any other services you like.</br>
Note: You don't need anything fancy, even the most basic and simple plans of the VPS's will do the magic.
<hr>
</br>
Your server inside Iran will act as a router. It's only job is to route traffic from within the country to the main server (non-IR) which hosts the actual Vmess proxy. </br>
All commands are base on debian-based linux distros. e.g. Ubuntu.</br></br>
Note: There is <strong>no authentication</strong> mechanism applied for the sake of simplicity. </br>
Share it with your loved ones</br></br>
<bold>#MahsaAmini</bold></br>
<bold>#womanlifefreedom</bold></br></br>
Let's get started.</br></br>
You can either clone this repo on your servers and just set your configurations or go by the following steps.</br></br>

## on Your non-IR Server</br>

#### 1. update & upgrade the packages </br>
```shell script
sudo apt update && apt upgrade -y
``` 
#### 2. Create & edit docker compose </br>
```shell script
nano docker-compose.yaml
```
#### 3. Add the following to the docker-compose.yaml file and save: </br>
```shell script
version: "3"
services:
  v2ray443:
    image: v2fly/v2fly-core
    restart: always
    network_mode: host
    environment:
      - V2RAY_VMESS_AEAD_FORCED=false
    volumes:
      - ./config.json:/etc/v2ray/config.json:ro
```
#### 4.  Create & edit docker compose </br>
```shell script
nano config.json
```
#### 5. Add the following to the config.json file: </br>
Navigate to [UUID Generator](https://www.uuidgenerator.net/version1) and grab a UUID V1, and replace it with `xxx-xxx-xxx` </br>
Write your UUID in notepad, you gonna need it later.</br>
```shell script
{
  "log": {
    "loglevel": "warning"
  },
  "inbounds": [
    {
      "port": 80,
      "protocol": "vmess",
      "allocate": {
        "strategy": "always"
      },
      "settings": {
        "clients": [
          {
            "id": "xxx-xxx-xxx",
            "level": 1,
            "alterId": 0,
            "email": "client@example.com"
          }
        ],
        "disableInsecureEncryption": true
      },
      "streamSettings": {
        "network": "ws",
        "wsSettings": {
          "connectionReuse": true,
          "path": "/graphql"
        },
        "security": "none",
        "tcpSettings": {
          "header": {
            "type": "http",
            "response": {
              "version": "1.1",
              "status": "200",
              "reason": "OK",
              "headers": {
                "Content-Type": [
                  "application/octet-stream",
                  "application/x-msdownload",
                  "text/html",
                  "application/x-shockwave-flash"
                ],
                "Transfer-Encoding": ["chunked"],
                "Connection": ["keep-alive"],
                "Pragma": "no-cache"
              }
            }
          }
        }
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "freedom",
      "settings": {}
    }
  ]
}
```
#### 6. Save & close your `config.json`.</br>
#### 7. Or instead, Replace the `YOUR_NEW_UUID` in the following command with your newly generated UUID</br>
```shell script
sed -i 's/xxx-xxx-xxx/YOUR_NEW_UUID/g' ./config.json
```
Note: the `docker-compose.yaml` and `config.json` files need to be under the same directory.</br>
#### 8. Install docker and docker-compose: </br>
```shell script
sudo apt install docker docker-compose
```
#### 9. Start the Vmess Server
```shell script
docker-compose up -d
```