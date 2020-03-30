---
title: Aktivera SSL med sidovagnsbehållare
description: Skapa en SSL- eller TLS-slutpunkt för en behållargrupp som körs i Azure Container Instances genom att köra Nginx i en sidovagnsbehållare
ms.topic: article
ms.date: 02/14/2020
ms.openlocfilehash: 43b39c7c13d6d5e52aae2ce1706e4880ab27d225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294945"
---
# <a name="enable-an-ssl-endpoint-in-a-sidecar-container"></a>Aktivera en SSL-slutpunkt i en sidovagnsbehållare

Den här artikeln visar hur du skapar en [behållargrupp](container-instances-container-groups.md) med en programbehållare och en sidovagnsbehållare som kör en SSL-provider. Genom att konfigurera en behållargrupp med en separat SSL-slutpunkt aktiverar du SSL-anslutningar för ditt program utan att ändra programkoden.

Du kan ställa in en exempelbehållaregrupp som består av två behållare:
* En programbehållare som kör en enkel webbapp med den offentliga Microsoft [aci-helloworld-avbildningen.](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld) 
* En sidovagnsbehållare som kör den offentliga [Nginx-avbildningen,](https://hub.docker.com/_/nginx) konfigurerad för att använda SSL. 

I det här exemplet exponerar behållargruppen endast port 443 för Nginx med sin offentliga IP-adress. Nginx dirigerar HTTPS-begäranden till den medföljande webbappen, som lyssnar internt på port 80. Du kan anpassa exemplet för behållarappar som lyssnar på andra portar. 

Se [Nästa steg](#next-steps) för andra metoder för att aktivera SSL i en behållargrupp.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att slutföra den här artikeln. Om du vill använda den lokalt rekommenderas version 2.0.55 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat

Om du vill konfigurera Nginx som SSL-leverantör behöver du ett SSL-certifikat. Den här artikeln visar hur du skapar och konfigurerar ett självsignerat SSL-certifikat. För produktionsscenarier bör du hämta ett certifikat från en certifikatutfärdar.

Om du vill skapa ett självsignerat SSL-certifikat använder du [OpenSSL-verktyget](https://www.openssl.org/) som är tillgängligt i Azure Cloud Shell och många Linux-distributioner, eller använder ett jämförbart klientverktyg i operativsystemet.

Skapa först en certifikatbegäran (.csr-fil) i en lokal arbetskatalog:

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

Följ anvisningarna för att lägga till identifieringsinformationen. Ange det värdnamn som är associerat med certifikatet för vanligt namn. När du uppmanas att ange ett lösenord trycker du på Retur utan att skriva för att hoppa över att lägga till ett lösenord.

Kör följande kommando för att skapa det självsignerade certifikatet (.crt-filen) från certifikatbegäran. Ett exempel:

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

Du bör nu se tre filer i`ssl.csr`katalogen: certifikatbegäran ( ), den privata nyckeln (`ssl.key`) och det självsignerade certifikatet (`ssl.crt`). Du `ssl.key` använder `ssl.crt` och i senare steg.

## <a name="configure-nginx-to-use-ssl"></a>Konfigurera Nginx för att använda SSL

### <a name="create-nginx-configuration-file"></a>Skapa Nginx-konfigurationsfil

I det här avsnittet skapar du en konfigurationsfil för Nginx för att använda SSL. Börja med att kopiera följande text `nginx.conf`till en ny fil med namnet . I Azure Cloud Shell kan du använda Visual Studio-kod för att skapa filen i arbetskatalogen:

```console
code nginx.conf
```

I `location`, se `proxy_pass` till att ställa in med rätt port för din app. I det här exemplet anger vi `aci-helloworld` port 80 för behållaren.

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
        ssl_protocols              TLSv1.2;

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

### <a name="base64-encode-secrets-and-configuration-file"></a>Base64-koda hemligheter och konfigurationsfil

Base64-koda Nginx-konfigurationsfilen, SSL-certifikatet och SSL-nyckeln. I nästa avsnitt anger du det kodade innehållet i en YAML-fil som används för att distribuera behållargruppen.

```console
cat nginx.conf | base64 > base64-nginx.conf
cat ssl.crt | base64 > base64-ssl.crt
cat ssl.key | base64 > base64-ssl.key
```

## <a name="deploy-container-group"></a>Distribuera behållargrupp

Distribuera nu behållargruppen genom att ange behållarkonfigurationerna i en [YAML-fil](container-instances-multi-container-yaml.md).

### <a name="create-yaml-file"></a>Skapa YAML-fil

Kopiera följande YAML till en `deploy-aci.yaml`ny fil med namnet . I Azure Cloud Shell kan du använda Visual Studio-kod för att skapa filen i arbetskatalogen:

```console
code deploy-aci.yaml
```

Ange innehållet i de base64-kodade filerna `secret`där det anges under . Till exempel `cat` var och en av de base64-kodade filerna för att se dess innehåll. Under distributionen läggs dessa filer till i en [hemlig volym](container-instances-volume-secret.md) i behållargruppen. I det här exemplet monteras den hemliga volymen på Nginx-behållaren.

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

### <a name="deploy-the-container-group"></a>Distribuera behållargruppen

Skapa en resursgrupp med kommandot [az group create:](/cli/azure/group#az-group-create)

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

Distribuera behållargruppen med kommandot [az container create](/cli/azure/container#az-container-create) och skicka YAML-filen som ett argument.

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>Visa distributionstillstånd

Om du vill visa tillståndet för distributionen använder du följande kommando [för az-behållarvisning:](/cli/azure/container#az-container-show)

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

För en lyckad distribution liknar utdata följande:

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   nginx, mcr.microsoft.com/azuredocs/aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-ssl-connection"></a>Verifiera SSL-anslutning

Använd webbläsaren för att navigera till den offentliga IP-adressen för behållargruppen. IP-adressen som visas `52.157.22.76`i det här **https://52.157.22.76**exemplet är , så webbadressen är . Du måste använda HTTPS för att se programmet som körs på grund av Nginx-serverkonfigurationen. Försök att ansluta via HTTP misslyckas.

![Skärmbild från webbläsaren som visar ett program som körs i en instans av Azure-containern](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> Eftersom det här exemplet använder ett självsignerat certifikat och inte ett från en certifikatutfärdare, visar webbläsaren en säkerhetsvarning när du ansluter till webbplatsen via HTTPS. Du kan behöva acceptera varningen eller justera webbläsar- eller certifikatinställningarna för att gå vidare till sidan. Det här beteendet är förväntat.

>

## <a name="next-steps"></a>Nästa steg

Den här artikeln visar hur du konfigurerar en Nginx-behållare för att aktivera SSL-anslutningar till en webbapp som körs i behållargruppen. Du kan anpassa det här exemplet för appar som lyssnar på andra portar än port 80. Du kan också uppdatera Nginx-konfigurationsfilen för att automatiskt omdirigera serveranslutningar på port 80 (HTTP) för att använda HTTPS.

Medan den här artikeln använder Nginx i sidovagnen kan du använda en annan SSL-leverantör som [Caddy](https://caddyserver.com/).

Om du distribuerar din behållargrupp i ett [virtuellt Azure-nätverk](container-instances-vnet.md)kan du överväga andra alternativ för att aktivera en SSL-slutpunkt för en serverdelsbehållareinstans, inklusive:

* [Azure-funktioner proxyservrar](../azure-functions/functions-proxies.md)
* [Azure API Management](../api-management/api-management-key-concepts.md)
* [Azure Application Gateway](../application-gateway/overview.md) - se en [exempeldistributionsmall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet).
