---
title: Aktivera SSL i Azure Container Instances
description: Skapa en SSL eller TLS-slutpunkt för en behållargrupp som körs i Azure Container Instances
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/03/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 10c015a9aee4ed8be54805f7adaae5bb4b5c422f
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870394"
---
# <a name="enable-an-ssl-endpoint-in-a-container-group"></a>Aktivera SSL-slutpunkten i en behållargrupp

Den här artikeln visar hur du skapar en [behållargruppen](container-instances-container-groups.md) med en programbehållare och en sidovagnsbehållare som kör en SSL-provider. Genom att konfigurera en behållargrupp med en separat slutpunkt SSL kan aktivera du SSL-anslutningar för ditt program utan att ändra programkoden.

Du ställer in en behållargrupp som består av två behållare:
* En programbehållare som kör en enkel webbapp som använder offentliga Microsoft [aci-helloworld](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld) bild. 
* En sidovagnsbehållare allmänheten [Nginx](https://hub.docker.com/_/nginx) bild har konfigurerats för användning av SSL. 

I det här exemplet exponerar behållargruppen endast port 443 för Nginx med dess offentliga IP-adress. Nginx dirigerar HTTPS-begäranden till kompletterande webbapp, som internt lyssnar på port 80. Du kan anpassa exemplet för behållarappar som lyssnar på andra portar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att slutföra den här artikeln. Om du vill använda den lokalt, version 2.0.55 eller senare rekommenderas. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat

Om du vill konfigurera Nginx som SSL-leverantör, behöver du ett SSL-certifikat. Den här artikeln visar hur du skapar och konfigurerar ett självsignerat SSL-certifikat. I produktionsscenarier bör du skaffa ett certifikat från en certifikatutfärdare.

Du kan skapa ett självsignerat SSL-certifikat med den [OpenSSL](https://www.openssl.org/) verktyget finns i Azure Cloud Shell och många Linux-distributioner eller Använd en jämförbar klientverktyg i ditt operativsystem.

Skapa först en certifikatbegäran (CSR-filen) i en lokal arbetskatalog:

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

Följ anvisningarna för att lägga till ID-information. Ange värdnamnet som är associerade med certifikatet för eget namn. Tryck på RETUR utan att ange om du vill hoppa över att lägga till ett lösenord när du uppmanas att göra ett lösenord.

Kör följande kommando för att skapa det självsignerade certifikatet (.crt-fil) från certifikatbegäran. Exempel:

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

Du bör nu se tre filer i katalogen: certifikatbegäran (`ssl.csr`), den privata nyckeln (`ssl.key`), och det självsignerade certifikatet (`ssl.crt`). Du använder `ssl.key` och `ssl.crt` i senare steg.

## <a name="configure-nginx-to-use-ssl"></a>Konfigurera Nginx för att använda SSL

### <a name="create-nginx-configuration-file"></a>Skapa Nginx-konfigurationsfilen

I det här avsnittet skapar du en konfigurationsfil för Nginx du använder SSL. Börja genom att kopiera följande text i en ny fil med namnet`nginx.conf`. Du kan använda Visual Studio Code i Azure Cloud Shell för att skapa filen i arbetskatalogen:

```console
code nginx.conf
```

I `location`, måste du ange `proxy_pass` med rätt port för appen. I det här exemplet anger vi port 80 för den `aci-helloworld` behållare.

```console
# nginx Configuration File
# http://wiki.nginx.org/Configuration

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

### <a name="base64-encode-secrets-and-configuration-file"></a>Base64-koda hemligheter och konfigurationsfilen

Base64-koda Nginx-konfigurationsfilen, SSL-certifikatet och nyckeln för SSL. Du kan använda kodade innehållet för att konfigurera Nginx-behållaren.

```console
cat nginx.conf | base64 -w 0 > base64-nginx.conf
cat ssl.crt | base64 -w 0 > base64-ssl.crt
cat ssl.key | base64 -w 0 > base64-ssl.key
```

## <a name="deploy-container-group"></a>Distribuera behållargruppen

Nu distribuera behållargruppen genom att ange container-konfigurationer i en [YAML-fil](container-instances-multi-container-yaml.md).

### <a name="create-yaml-file"></a>Skapa YAML-fil

Kopiera följande YAML till en ny fil med namnet `deploy-aci.yaml`. Du kan använda Visual Studio Code i Azure Cloud Shell för att skapa filen i arbetskatalogen:

```console
code deploy-aci.yaml
```

Ange innehållet i base64-kodade filer där det anges `secret`. Under distributionen av dessa filer läggs till i en [hemlighetsvolymen](container-instances-volume-secret.md) i behållargruppen. I det här exemplet monteras hemlighetsvolymen Nginx-behållaren.

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
      ssl.crt: <base64-ssl.crt>
      ssl.key: <base64-ssl.key>
      nginx.conf: <base64-nginx.conf>
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

### <a name="deploy-the-container-group"></a>Distribuera behållargruppen

Skapa en resursgrupp med det [az gruppen skapa](/cli/azure/group#az-group-create) kommando:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Distribuera behållargrupp med den [az container skapa](/cli/azure/container#az-container-create) kommando, skicka YAML-fil som ett argument.

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>Visa status för distribution

Om du vill visa statusen för distributionen, använder du följande [az container show](/cli/azure/container#az-container-show) kommando:

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

För en lyckad distribution utdata som genereras liknar följande:

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   mcr.microsoft.com/azuredocs/nginx, aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-ssl-connection"></a>Verifiera SSL-anslutning

Gå till dess IP-adress i webbläsaren om du vill visa programmet som körs. Till exempel IP-adressen som visas i det här exemplet är `52.157.22.76`. Du måste använda `https://<IP-ADDRESS>` att se programmet som körs på grund av Nginx-serverkonfigurationen. Försöker ansluta med `http://<IP-ADDRESS>` misslyckas.

![Skärmbild från webbläsaren som visar ett program som körs i en instans av Azure-containern](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> Eftersom det här exemplet använder ett självsignerat certifikat och inte en från en certifikatutfärdare, visar webbläsaren en säkerhetsvarning när du ansluter till webbplatsen via HTTPS. Det här beteendet är förväntat.
>

## <a name="next-steps"></a>Nästa steg

Den här artikeln visade hur du konfigurerar en Nginx-behållare för att aktivera SSL-anslutningar till en webbapp som körs i behållargruppen. Du kan anpassa det här exemplet för appar som lyssnar på port än port 80. Du kan också uppdatera Nginx-konfigurationsfilen för att automatiskt omdirigera server-anslutningar på port 80 (HTTP) för att använda HTTPS.

Den här artikeln använder Nginx i sidovagnen, du kan använda en annan SSL-provider som [hållare för lagringsenheter](https://caddyserver.com/).

En annan metod för att aktivera SSL i en behållargrupp är att distribuera gruppen i en [Azure-nätverk](container-instances-vnet.md) med en [Programgateway i Azure](../application-gateway/overview.md). Gatewayen kan ställas in som en SSL-slutpunkt. Se ett exempel [Distributionsmall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) du kan anpassa om du vill aktivera SSL-avslutning på gatewayen.
