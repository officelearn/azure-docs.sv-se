---
title: Aktivera SSL i Azure Container Instances
description: Skapa en SSL-eller TLS-slutpunkt för en behållar grupp som körs i Azure Container Instances
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/03/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: f11fb209f8d2ace51081fd81f453faf9505af27c
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326067"
---
# <a name="enable-an-ssl-endpoint-in-a-container-group"></a>Aktivera en SSL-slutpunkt i en behållar grupp

Den här artikeln visar hur du skapar en behållar [grupp](container-instances-container-groups.md) med en program behållare och en sidvagn-behållare som kör en SSL-Provider. Genom att konfigurera en behållar grupp med en separat SSL-slutpunkt aktiverar du SSL-anslutningar för ditt program utan att ändra program koden.

Du konfigurerar en behållar grupp bestående av två behållare:
* En program behållare som kör en enkel webbapp med hjälp av den offentliga Microsoft [ACI-HelloWorld-](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld) avbildningen. 
* En sidvagn-behållare som kör den offentliga [nginx](https://hub.docker.com/_/nginx) -avbildningen som kon figurer ATS för att använda SSL. 

I det här exemplet exponerar behållar gruppen bara port 443 för nginx med dess offentliga IP-adress. Nginx dirigerar HTTPS-begäranden till den medföljande webbappen, som lyssnar internt på port 80. Du kan anpassa exemplet för behållar appar som lyssnar på andra portar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att slutföra den här artikeln. Om du vill använda det lokalt rekommenderar vi version 2.0.55 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat

Om du vill konfigurera nginx som en SSL-Provider behöver du ett SSL-certifikat. Den här artikeln visar hur du skapar och konfigurerar ett självsignerat SSL-certifikat. För produktions scenarier bör du skaffa ett certifikat från en certifikat utfärdare.

Om du vill skapa ett självsignerat SSL-certifikat använder du [openssl](https://www.openssl.org/) -verktyget som finns i Azure Cloud Shell och många Linux-distributioner eller använder ett jämförbart klient verktyg i operativ systemet.

Skapa först en certifikat förfrågan (. CSR-fil) i en lokal arbets katalog:

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

Följ anvisningarna för att lägga till identifierings informationen. För eget namn anger du det värdnamn som är associerat med certifikatet. När du uppmanas att ange ett lösen ord trycker du på RETUR utan att skriva för att hoppa över tillägg av lösen ord.

Kör följande kommando för att skapa det självsignerade certifikatet (. CRT-filen) från certifikat förfrågan. Exempel:

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

Nu bör du se tre filer i katalogen: certifikat förfrågan (`ssl.csr`), den privata nyckeln (`ssl.key`) och det självsignerade certifikatet (`ssl.crt`). Du använder `ssl.key` och `ssl.crt` i senare steg.

## <a name="configure-nginx-to-use-ssl"></a>Konfigurera nginx för att använda SSL

### <a name="create-nginx-configuration-file"></a>Skapa konfigurations fil för nginx

I det här avsnittet skapar du en konfigurations fil för nginx för att använda SSL. Börja med att kopiera följande text till en ny fil med`nginx.conf`namnet. I Azure Cloud Shell kan du använda Visual Studio Code för att skapa filen i din arbets katalog:

```console
code nginx.conf
```

I `location`, se till att du `proxy_pass` anger rätt port för appen. I det här exemplet anger vi port 80 för `aci-helloworld` behållaren.

```console
# nginx Configuration File
# https://wiki.nginx.org/Configuration

# Run as a less privileged user for security reasons.
user nginx;

worker_processes auto;

events {
  worker_connections 1024;
}

pid        /var/run/nginx.pid;

http {

    #Redirect to https, using 307 instead of 301 to preserve post data

    server {
        listen [::]:443 ssl;
        listen 443 ssl;

        server_name localhost;

        # Protect against the BEAST attack by not using SSLv3 at all. If you need to support older browsers (IE6) you may need to add
        # SSLv3 to the list of protocols below.
        ssl_protocols              TLSv1 TLSv1.1 TLSv1.2;

        # Ciphers set to best allow protection from Beast, while providing forwarding secrecy, as defined by Mozilla - https://wiki.mozilla.org/Security/Server_Side_TLS#Nginx
        ssl_ciphers                ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:ECDHE-RSA-RC4-SHA:ECDHE-ECDSA-RC4-SHA:AES128:AES256:RC4-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!3DES:!MD5:!PSK;
        ssl_prefer_server_ciphers  on;

        # Optimize SSL by caching session parameters for 10 minutes. This cuts down on the number of expensive SSL handshakes.
        # The handshake is the most CPU-intensive operation, and by default it is re-negotiated on every new/parallel connection.
        # By enabling a cache (of type "shared between all Nginx workers"), we tell the client to re-use the already negotiated state.
        # Further optimization can be achieved by raising keepalive_timeout, but that shouldn't be done unless you serve primarily HTTPS.
        ssl_session_cache    shared:SSL:10m; # a 1mb cache can hold about 4000 sessions, so we can hold 40000 sessions
        ssl_session_timeout  24h;


        # Use a higher keepalive timeout to reduce the need for repeated handshakes
        keepalive_timeout 300; # up from 75 secs default

        # remember the certificate for a year and automatically connect to HTTPS
        add_header Strict-Transport-Security 'max-age=31536000; includeSubDomains';

        ssl_certificate      /etc/nginx/ssl.crt;
        ssl_certificate_key  /etc/nginx/ssl.key;

        location / {
            proxy_pass http://localhost:80; # TODO: replace port if app listens on port other than 80
            
            proxy_set_header Connection "";
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $remote_addr;
        }
    }
}
```

### <a name="base64-encode-secrets-and-configuration-file"></a>Base64-koda hemligheter och konfigurations fil

Base64-koda konfigurations filen för nginx, SSL-certifikatet och SSL-nyckeln. I nästa avsnitt anger du det kodade innehållet i en YAML-fil som används för att distribuera behållar gruppen.

```console
cat nginx.conf | base64 -w 0 > base64-nginx.conf
cat ssl.crt | base64 -w 0 > base64-ssl.crt
cat ssl.key | base64 -w 0 > base64-ssl.key
```

## <a name="deploy-container-group"></a>Distribuera behållar grupp

Distribuera nu behållar gruppen genom att ange behållar konfigurationerna i en [yaml-fil](container-instances-multi-container-yaml.md).

### <a name="create-yaml-file"></a>Skapa YAML-fil

Kopiera följande YAML till en ny fil med namnet `deploy-aci.yaml`. I Azure Cloud Shell kan du använda Visual Studio Code för att skapa filen i din arbets katalog:

```console
code deploy-aci.yaml
```

Ange innehållet i base64-kodade filer som anges under `secret`. Till exempel var `cat` och en av de base64-kodade filerna för att se dess innehåll. Under distributionen läggs de här filerna till i en [hemlig volym](container-instances-volume-secret.md) i behållar gruppen. I det här exemplet monteras den hemliga volymen till behållaren nginx.

```YAML
api-version: 2018-10-01
location: westus
name: app-with-ssl
properties:
  containers:
  - name: nginx-with-ssl
    properties:
      image: nginx
      ports:
      - port: 443
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - name: nginx-config
        mountPath: /etc/nginx
  - name: my-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  volumes:
  - secret:
      ssl.crt: <Enter contents of base64-ssl.crt here>
      ssl.key: <Enter contents of base64-ssl.key here>
      nginx.conf: <Enter contents of base64-nginx.conf here>
    name: nginx-config
  ipAddress:
    ports:
    - port: 443
      protocol: TCP
    type: Public
  osType: Linux
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="deploy-the-container-group"></a>Distribuera behållar gruppen

Skapa en resurs grupp med kommandot [AZ Group Create](/cli/azure/group#az-group-create) :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Distribuera behållar gruppen med kommandot [AZ container Create](/cli/azure/container#az-container-create) och skicka yaml-filen som ett argument.

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>Visa distributions status

Om du vill visa status för distributionen använder du följande [AZ container show](/cli/azure/container#az-container-show) -kommando:

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

För att distributionen ska lyckas ser utdata ut ungefär så här:

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   mcr.microsoft.com/azuredocs/nginx, aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-ssl-connection"></a>Verifiera SSL-anslutning

Om du vill visa det program som körs, navigerar du till dess IP-adress i webbläsaren. IP-adressen som visas i det här exemplet är `52.157.22.76`till exempel. Du måste använda `https://<IP-ADDRESS>` för att se programmet som körs, på grund av nginx-serverkonfigurationen. Anslutnings försök `http://<IP-ADDRESS>` Miss lyckas.

![Skärmbild från webbläsaren som visar ett program som körs i en instans av Azure-containern](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> Eftersom det här exemplet använder ett självsignerat certifikat och inte ett från en certifikat utfärdare, visar webbläsaren en säkerhets varning när du ansluter till platsen via HTTPS. Det här beteendet är förväntat.
>

## <a name="next-steps"></a>Nästa steg

Den här artikeln visar hur du konfigurerar en nginx-behållare för att aktivera SSL-anslutningar till en webbapp som körs i behållar gruppen. Du kan anpassa det här exemplet för appar som lyssnar på andra portar än port 80. Du kan också uppdatera konfigurations filen för nginx för att automatiskt omdirigera Server anslutningar på port 80 (HTTP) för att använda HTTPS.

Den här artikeln använder nginx i den sidvagn, men du kan använda en annan SSL-Provider, till exempel [Caddy](https://caddyserver.com/).

En annan metod för att aktivera SSL i en behållar grupp är att distribuera gruppen i ett [virtuellt Azure-nätverk](container-instances-vnet.md) med en [Azure Application Gateway](../application-gateway/overview.md). Gatewayen kan konfigureras som en SSL-slutpunkt. Se en exempel [distributions mall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) som du kan anpassa för att aktivera SSL-avslutning på gatewayen.
