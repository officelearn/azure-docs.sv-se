---
title: "Anpassa en Linux VM på första start i Azure | Microsoft Docs"
description: "Lära sig använda molnet init och Key Vault till customze virtuella Linux-datorer första gången de startar i Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/11/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 4e2d07a03902a8c837150da8d50ab9abec8d1c95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-customize-a-linux-virtual-machine-on-first-boot"></a>Så här anpassar du en virtuell Linux-dator vid den första starten
I en tidigare kursen du lärt dig hur du vill SSH till en virtuell dator (VM) och manuellt installera NGINX. Om du vill skapa virtuella datorer på ett snabbt och konsekvent sätt önskade vanligtvis någon form av automatisering. Ett vanligt sätt att anpassa en VM på första start är att använda [moln init](https://cloudinit.readthedocs.io). I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en konfigurationsfil för molnet initiering
> * Skapa en virtuell dator som använder en moln-init-fil
> * Visa en Node.js-app som körs när den virtuella datorn skapas
> * Använda Nyckelvalv för att lagra certifikat på ett säkert sätt
> * Automatisera säker distribution av NGINX med molnet initiering


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kursen krävs att du använder Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).  



## <a name="cloud-init-overview"></a>Översikt över molntjänster initiering
[Molnet init](https://cloudinit.readthedocs.io) är ett vanligt sätt att anpassa en Linux VM när den startas för första gången. Du kan använda molnet init för att installera paket och skriva filer eller för att konfigurera användare och säkerhet. Eftersom molnet init körs under den ursprungliga startprocessen, det inte finns några ytterligare steg krävs agenter att tillämpa konfigurationen.

Molnet init fungerar även över distributioner. Exempelvis kan du inte använda **lgh get installera** eller **yum installera** att installera ett paket. I stället kan du definiera en lista över paket som ska installeras. Molnet init används automatiskt det ursprungliga paket hanteringsverktyget för distro som du väljer.

Vi arbetar med våra partner att hämta molnet initiering ingår och arbeta med bilder som skickas till Azure. I följande tabell beskrivs aktuella molnet init tillgängligheten på Azure-plattformen bilder:

| Alias | Utgivare | Erbjudande | SKU | Version |
|:--- |:--- |:--- |:--- |:--- |:--- |
| UbuntuLTS |Canonical |UbuntuServer |16.04 LTS |senaste |
| UbuntuLTS |Canonical |UbuntuServer |14.04.5-LTS |senaste |
| CoreOS |CoreOS |CoreOS |Stable |senaste |


## <a name="create-cloud-init-config-file"></a>Skapa moln init-konfigurationsfil
Skapa en virtuell dator som installerar NGINX och kör en enkel ”Hello World” Node.js-app för att se molnet initiering i åtgärden. Följande konfiguration i molnet init installeras de nödvändiga paketen, skapar en Node.js-app och sedan initiera och startar appen.

Skapa en fil med namnet i din aktuella shell *moln init.txt* och klistra in följande konfiguration. Till exempel skapa filen i molnet Shell inte på den lokala datorn. Du kan använda valfri redigerare som du vill. Ange `sensible-editor cloud-init.txt` att skapa filen och se en lista över tillgängliga redigerare. Se till att hela molnet init-filen har kopierats korrekt, särskilt den första raden:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

Mer information om molnet init konfigurationsalternativ finns [moln init config exempel](https://cloudinit.readthedocs.io/en/latest/topics/examples.html).

## <a name="create-virtual-machine"></a>Skapa en virtuell dator
Innan du kan skapa en virtuell dator, skapa en resursgrupp med [az gruppen skapa](/cli/azure/group#create). I följande exempel skapas en resursgrupp med namnet *myResourceGroupAutomate* i den *eastus* plats:

```azurecli-interactive 
az group create --name myResourceGroupAutomate --location eastus
```

Nu skapa en virtuell dator med [az vm skapa](/cli/azure/vm#create). Använd den `--custom-data` parametern för att skicka in din moln-init-konfigurationsfilen. Ange den fullständiga sökvägen till den *moln init.txt* config om du har sparat filen utanför arbetskatalogen finns. I följande exempel skapas en virtuell dator med namnet *myAutomatedVM*:

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
```

Det tar några minuter för den virtuella datorn skapas, paket för att installera och starta appen. Det finns bakgrundsaktiviteter för att fortsätta att köras när Azure CLI återgår till Kommandotolken. Det kan vara en annan några minuter innan du kan komma åt appen. När den virtuella datorn har skapats, notera den `publicIpAddress` visas av Azure CLI. Den här adressen används för att komma åt Node.js-appen via en webbläsare.

Tillåt Internet-trafik till den virtuella datorn genom att öppna port 80 från Internet med [az vm öppna port](/cli/azure/vm#open-port):

```azurecli-interactive 
az vm open-port --port 80 --resource-group myResourceGroupAutomate --name myVM
```

## <a name="test-web-app"></a>Testa webbprogram
Nu kan du öppna en webbläsare och ange *http://<publicIpAddress>*  i adressfältet. Ange dina egna offentliga IP-adress från den virtuella datorn skapa processen. Node.js-appen visas som i följande exempel:

![Visa körs NGINX-webbplats](./media/tutorial-automate-vm-deployment/nginx.png)


## <a name="inject-certificates-from-key-vault"></a>Mata in certifikat från Nyckelvalvet
Det här valfria avsnittet visar hur du säkert lagra certifikat i Azure Key Vault och mata in dem under distribution av Virtuella datorer. I stället för att använda en anpassad avbildning som innehåller certifikaten inbyggd-modulen för den här processen säkerställer att de senaste certifikat är matas in till en virtuell dator på första start. Under processen certifikatet aldrig lämnar Azure-plattformen eller exponeras i ett skript, kommandoradsverktyget historik eller mall.

Azure Key Vault skyddar kryptografiska nycklar och hemligheter, till exempel certifikat eller lösenord. Key Vault hjälper till att effektivisera nyckelhanteringen och låter dig behålla kontrollen över nycklar som kommer åt och krypterar dina data. Det här scenariot beskriver vissa Key Vault-begrepp för att skapa och använda ett certifikat, men är inte en fullständig översikt över hur du använder Key Vault.

Följande steg visar hur du kan:

- Skapa ett Azure Key Vault
- Generera och överföra ett certifikat till Nyckelvalvet
- Skapa en hemlighet från certifikat för att mata in i till en virtuell dator
- Skapa en virtuell dator och mata in certifikatet

### <a name="create-an-azure-key-vault"></a>Skapa ett Azure Key Vault
Skapa först ett Nyckelvalv med [az keyvault skapa](/cli/azure/keyvault#create) och aktivera den för användning när du distribuerar en virtuell dator. Varje Key Vault kräver ett unikt namn och bör vara alla gemen. Ersätt *mykeyvault* i följande exempel med dina egna unika Key Vault-namn:

```azurecli-interactive 
keyvault_name=mykeyvault
az keyvault create \
    --resource-group myResourceGroupAutomate \
    --name $keyvault_name \
    --enabled-for-deployment
```

### <a name="generate-certificate-and-store-in-key-vault"></a>Generera certifikat och lagra i Key Vault
För produktion, ska du importera ett giltigt certifikat som signerats av en betrodd provider med [az keyvault certifikat import](/cli/azure/keyvault/certificate#import). Den här självstudiekursen i följande exempel visas hur du kan skapa ett självsignerat certifikat med [az keyvault certifikat skapa](/cli/azure/keyvault/certificate#create) som använder standardprincipen för certifikat:

```azurecli-interactive 
az keyvault certificate create \
    --vault-name $keyvault_name \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy)"
```


### <a name="prepare-certificate-for-use-with-vm"></a>Förbereda certifikat för användning med VM
Använda certifikatet under den virtuella datorn Skapa process, Hämta ID för certifikatet med [az keyvault lista-versionerna](/cli/azure/keyvault/secret#list-versions). Den virtuella datorn måste certifikatet i ett visst format att injicera på Start, så konvertera certifikatet med [az vm format-hemlighet](/cli/azure/vm#format-secret). I följande exempel tilldelas resultatet av dessa kommandon till variabler för att underlätta användning i nästa steg:

```azurecli-interactive 
secret=$(az keyvault secret list-versions \
          --vault-name $keyvault_name \
          --name mycert \
          --query "[?attributes.enabled].id" --output tsv)
vm_secret=$(az vm format-secret --secret "$secret")
```


### <a name="create-cloud-init-config-to-secure-nginx"></a>Skapa moln init config om du vill skydda NGINX
När du skapar en virtuell dator, certifikat och nycklar lagras i den skyddade */var/lib/waagent/* directory. Du kan använda en uppdaterad molnet initiering config från föregående exempel för att automatisera lägger till certifikatet till den virtuella datorn och konfigurera NGINX.

Skapa en fil med namnet *moln-init-secured.txt* och klistra in följande konfiguration. Om du använder molnet Shell skapa igen, molnet init-konfigurationsfilen finns det och inte på den lokala datorn. Använd `sensible-editor cloud-init-secured.txt` att skapa filen och se en lista över tillgängliga redigerare. Se till att hela molnet init-filen har kopierats korrekt, särskilt den första raden:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/mycert.cert;
        ssl_certificate_key /etc/nginx/ssl/mycert.prv;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - secretsname=$(find /var/lib/waagent/ -name "*.prv" | cut -c -57)
  - mkdir /etc/nginx/ssl
  - cp $secretsname.crt /etc/nginx/ssl/mycert.cert
  - cp $secretsname.prv /etc/nginx/ssl/mycert.prv
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-secure-vm"></a>Skapa säker virtuell dator
Nu skapa en virtuell dator med [az vm skapa](/cli/azure/vm#create). Certifikatdata injekteras från Nyckelvalvet med den `--secrets` parameter. Som i föregående exempel du också ange finns i molnet init-config med den `--custom-data` parameter:

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myVMSecured \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-secured.txt \
    --secrets "$vm_secret"
```

Det tar några minuter för den virtuella datorn skapas, paket för att installera och starta appen. Det finns bakgrundsaktiviteter för att fortsätta att köras när Azure CLI återgår till Kommandotolken. Det kan vara en annan några minuter innan du kan komma åt appen. När den virtuella datorn har skapats, notera den `publicIpAddress` visas av Azure CLI. Den här adressen används för att komma åt Node.js-appen via en webbläsare.

För att säkra Internet-trafik till den virtuella datorn ska du öppna port 443 från Internet med [az vm öppna port](/cli/azure/vm#open-port):

```azurecli-interactive 
az vm open-port \
    --resource-group myResourceGroupAutomate \
    --name myVMSecured \
    --port 443
```

### <a name="test-secure-web-app"></a>Testa säkra webbprogram
Nu kan du öppna en webbläsare och ange *https://<publicIpAddress>*  i adressfältet. Ange dina egna offentliga IP-adress från den virtuella datorn skapa processen. Acceptera säkerhetsvarningen om du använder ett självsignerat certifikat:

![Acceptera web Säkerhetsvarning för webbläsare](./media/tutorial-automate-vm-deployment/browser-warning.png)

Skyddad NGINX-platsen och Node.js app visas som i följande exempel:

![Visa körs säker NGINX-webbplats](./media/tutorial-automate-vm-deployment/secured-nginx.png)


## <a name="next-steps"></a>Nästa steg
I kursen får konfigurerat du virtuella datorer på första start med molnet initiering. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en konfigurationsfil för molnet initiering
> * Skapa en virtuell dator som använder en moln-init-fil
> * Visa en Node.js-app som körs när den virtuella datorn skapas
> * Använda Nyckelvalv för att lagra certifikat på ett säkert sätt
> * Automatisera säker distribution av NGINX med molnet initiering

Gå vidare till nästa kurs att lära dig hur du skapar anpassade VM-avbildningar.

> [!div class="nextstepaction"]
> [Skapa anpassade avbildningar av en virtuell dator](./tutorial-custom-images.md)
