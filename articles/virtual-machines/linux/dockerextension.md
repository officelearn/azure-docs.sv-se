---
title: "Använd Azure Docker VM-tillägget | Microsoft Docs"
description: "Lär dig hur du använder Docker VM-tillägget för att snabbt och säkert distribuera en Docker-miljö i Azure med hjälp av Resource Manager-mallar och Azure CLI 2.0"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
ms.assetid: 936d67d7-6921-4275-bf11-1e0115e66b7f
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: iainfou
ms.openlocfilehash: 0cef78edaeec9d45aa733b1912d82d5a058ba289
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension"></a>Skapa en Docker-miljö i Azure med Docker VM-tillägget
Docker är en populär behållarhantering och avbildningsverktyg plattform som gör att du snabbt arbeta med behållare på Linux. I Azure det, finns olika sätt som du kan distribuera Docker efter dina behov. Den här artikeln fokuserar på att använda Docker VM-tillägget och Azure Resource Manager-mallar med Azure CLI 2.0. Du kan också utföra dessa steg med [Azure CLI 1.0](dockerextension-nodejs.md).

## <a name="azure-docker-vm-extension-overview"></a>Översikt av Azure Docker VM-tillägg
Azure Docker VM-tillägget installeras och konfigureras Docker-daemon, Docker-klienten och Docker Compose i Linux-dator (VM). Med hjälp av Azure Docker VM-tillägget kan ha mer kontroll och funktioner än att bara använda Docker-dator eller skapa Docker-värd själv. Dessa ytterligare funktioner som [Docker Compose](https://docs.docker.com/compose/overview/), se Azure Docker VM-tillägget som passar för stabilare utvecklare eller produktion miljöer.

Mer information om olika distributionsmetoder, inklusive användning av Docker-dator och Azure Container Services finns i följande artiklar:

* Att snabbt prototyp en app du kan skapa en enda Docker-värd med [Docker datorn](docker-machine.md).
* När du skapar produktionsklara, skalbara miljöer som ger ytterligare schemaläggning och hanteringsverktygen ska du distribuera en [Docker Swarm-kluster på Azure Container Service](../../container-service/dcos-swarm/container-service-deployment.md).

## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>Distribuera en mall med Azure Docker VM-tillägg
Vi ska använda en befintlig mall för Snabbstart för att skapa en Ubuntu VM som använder Azure Docker VM-tillägget för att installera och konfigurera Docker-värden. Du kan visa den här mallen: [enkel distribution av en Ubuntu VM med Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Du behöver senast [Azure CLI 2.0](/cli/azure/install-az-cli2) installerad och inloggad till en Azure-konto med hjälp av [az inloggningen](/cli/azure/#login).

Börja med att skapa en resursgrupp med [az gruppen skapa](/cli/azure/group#create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* i den *eastus* plats:

```azurecli
az group create --name myResourceGroup --location eastus
```

Distribuera en virtuell dator med [az distribution skapa](/cli/azure/group/deployment#create) som innehåller Azure Docker VM-tillägget från [Azure Resource Manager-mallen på GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Ange dina egna unika värden för *newStorageAccountName*, *adminUsername*, *adminPassword*, och *dnsNameForPublicIP* som Så här:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --parameters '{"newStorageAccountName": {"value": "mystorageaccount"},
    "adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "dnsNameForPublicIP": {"value": "mypublicdns"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Det tar några minuter för att distributionen ska slutföras. När distributionen är klar [vidare till nästa steg](#deploy-your-first-nginx-container) till SSH till den virtuella datorn. 

För att i stället komma tillbaka till meddelandet och gör distributionen fortsätter i bakgrunden, Lägg till den `--no-wait` flagga för att kommandot ovan. Den här processen kan du utföra annat arbete i CLI medan distributionen fortsätter om en stund. 

Du kan sedan visa information om värdstatusen Docker med [az vm visa](/cli/azure/vm#show). I följande exempel kontrollerar status för den virtuella datorn med namnet *myDockerVM* (standardnamnet från mall - inte ändrar namnet) i resursgrupp med namnet *myResourceGroup*:

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --query [provisioningState] \
    --output tsv
```

När det här kommandot returnerar *lyckades*distributionen är klar och du kan SSH till den virtuella datorn i följande steg.

## <a name="deploy-your-first-nginx-container"></a>Distribuera din första NGINX-behållare
Om du vill visa information om den virtuella datorn, inklusive DNS-namnet [az vm visa](/cli/azure/vm#show):

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --show-details \
    --query [fqdns] \
    --output tsv
```

SSH till din nya Docker-värden. Ange dina egna DNS-namnet på följande sätt:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

När inloggad till Docker-värden kan vi köra en NGINX-behållare:

```bash
sudo docker run -d -p 80:80 nginx
```

Utdata liknar följande exempel som NGINX-avbildningen hämtas och en behållare igång:

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
a48df1751a97: Pull complete
8ddc2d7beb91: Pull complete
Digest: sha256:2ca2638e55319b7bc0c7d028209ea69b1368e95b01383e66dfe7e4f43780926d
Status: Downloaded newer image for nginx:latest
b6ed109fb743a762ff21a4606dd38d3e5d35aff43fa7f12e8d4ed1d920b0cd74
```

Kontrollera status för de behållare som körs på värden Docker på följande sätt:

```bash
sudo docker ps
```

Utdata liknar följande exempel, visar att NGINX-behållaren körs och TCP-portarna 80 och 443 och vidarebefordras:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

Om du vill se dina behållare i åtgärden, öppna en webbläsare och ange DNS-namnet på din Docker-värd:

![Körs ngnix behållare](./media/dockerextension/nginxrunning.png)

## <a name="azure-docker-vm-extension-template-reference"></a>Azure Docker VM-mall tilläggsreferens
Det föregående exemplet används en befintlig mall för Snabbstart. Du kan också distribuera Azure Docker VM-tillägget med egna Resource Manager-mallar. Om du vill göra det lägger du till följande Resource Manager-mallar, definiera den `vmName` på den virtuella datorn på rätt sätt:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'), '/DockerExtension'))]",
  "apiVersion": "2015-05-01-preview",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "DockerExtension",
    "typeHandlerVersion": "1.*",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

Du kan hitta mer detaljerad genomgång om hur du använder Resource Manager-mallar genom att läsa [översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

## <a name="next-steps"></a>Nästa steg
Du kanske vill [konfigurera Docker-daemon TCP-port](https://docs.docker.com/engine/reference/commandline/dockerd/#/bind-docker-to-another-hostport-or-a-unix-socket), Förstå [Docker säkerhet](https://docs.docker.com/engine/security/security/), eller distribuera behållare med [Docker Compose](https://docs.docker.com/compose/overview/). Mer information om Azure Docker VM tillägget själva finns i [GitHub projekt](https://github.com/Azure/azure-docker-extension/).

Läs mer om ytterligare Docker distributionsalternativen i Azure:

* [Använda Docker-datorn med Azure-drivrutin](docker-machine.md)  
* [Kom igång med Docker och skriv för att definiera och köra ett program för flera behållare på en virtuell dator i Azure](docker-compose-quickstart.md).
* [Distribuera ett Azure Container Service-kluster](../../container-service/dcos-swarm/container-service-deployment.md)

