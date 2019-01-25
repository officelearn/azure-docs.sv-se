---
title: Använda tillägget Azure Docker VM | Microsoft Docs
description: Lär dig hur du använder Docker VM-tillägget för att snabbt och säkert distribuera en Docker-miljö i Azure med hjälp av Resource Manager-mallar och Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: 936d67d7-6921-4275-bf11-1e0115e66b7f
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/18/2017
ms.author: cynthn
ms.openlocfilehash: 59dbbb8374455088d759a5e837b8d3bc22145d3e
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54882771"
---
# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension"></a>Skapa en Docker-miljö i Azure med Docker VM-tillägget

Docker är en populär behållarhantering- och avbildningsplattform som gör att du snabbt kan arbeta med behållare på Linux. I Azure finns det olika sätt som du kan distribuera Docker efter dina behov. Den här artikeln handlar om hur du använder Docker VM-tillägget och Azure Resource Manager-mallar med Azure CLI. 

> [!WARNING]
> Azure Docker VM-tillägg för Linux är inaktuell och kommer att dras tillbaka November 2018.
> Tillägget installeras bara Docker, så att alternativ såsom cloud-init eller tillägget för anpassat skript är ett bättre sätt att installera Docker-versionen av val. Mer information om hur du använder cloud-init finns [anpassa en Linux VM med cloud-init](tutorial-automate-vm-deployment.md).

## <a name="azure-docker-vm-extension-overview"></a>Översikt av Azure Docker VM-tillägg
Tillägget Azure Docker VM installerar och konfigurerar Docker-daemon, Docker-klienten och Docker Compose i din Linux-dator (VM). Genom att använda tillägget Azure Docker VM kan ha du mer kontroll och funktioner än att bara använda Docker Machine eller att skapa Docker-värd själv. Dessa ytterligare funktioner som [Docker Compose](https://docs.docker.com/compose/overview/), gör tillägget Azure Docker VM passar för stabilare developer- eller produktion.

Mer information om olika distributionsmetoder, inklusive användning av Docker-dator och Azure Container Services finns i följande artiklar:

* Snabbt prototyper en app kan du skapa en enkel Docker-värd med [Docker Machine](docker-machine.md).
* Om du vill skapa produktionsklara, skalbara miljöer som innehåller ytterligare verktyg för schemaläggning och hantering, kan du distribuera en [Kubernetes](../../container-service/kubernetes/index.yml) eller [Docker Swarm](../../container-service/dcos-swarm/index.yml) -kluster i Azure Container Services.


## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>Distribuera en mall med tillägget Azure Docker VM
Vi använda en befintlig snabbstartsmall för för att skapa en Ubuntu VM som använder Azure Docker VM-tillägget för att installera och konfigurera Docker-värd. Du kan visa den här mallen: [Enkel distribution av ett Ubuntu-VM med Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Du behöver senast [Azure CLI](/cli/azure/install-az-cli2) installerat och loggat in till en Azure-konto med hjälp av [az-inloggning](/cli/azure/reference-index#az_login).

Skapa först en resursgrupp med [az group create](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Distribuera en virtuell dator med [az group deployment skapa](/cli/azure/group/deployment#az_group_deployment_create) som innehåller Azure Docker VM-tillägget från [Azure Resource Manager-mallen på GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). När du uppmanas, anger du ditt eget unika värden för *newStorageAccountName*, *adminUsername*, *adminPassword*, och *dnsNameForPublicIP*:

```azurecli
az group deployment create --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Det tar några minuter innan distributionen är klar.


## <a name="deploy-your-first-nginx-container"></a>Distribuera din första NGINX-behållare
Du kan visa information om den virtuella datorn, inklusive DNS-namnet [az vm show](/cli/azure/vm#az_vm_show):

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --show-details \
    --query [fqdns] \
    --output tsv
```

SSH till din nya Docker-värd. Ange ditt eget användarnamn och DNS-namnet från föregående steg:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

När du loggat in till Docker-värd kan vi köra en NGINX-behållare:

```bash
sudo docker run -d -p 80:80 nginx
```

Utdata liknar följande exempel som NGINX-avbildningen har laddats ned och en behållare igång:

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

Kontrollera status för de behållare som körs på Docker-värd på följande sätt:

```bash
sudo docker ps
```

Utdata liknar följande exempel, som visar att NGINX-behållaren körs och TCP-portarna 80 och 443 och vidarebefordras:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

Om du vill se din behållare i praktiken, öppna en webbläsare och ange DNS-namnet på din Docker-värd:

![Behållaren som körs ngnix](./media/dockerextension/nginxrunning.png)

## <a name="azure-docker-vm-extension-template-reference"></a>Mallreferensen för Azure Docker VM-tillägg
Det föregående exemplet används en befintlig snabbstartsmall för. Du kan också distribuera Azure Docker VM-tillägget med din egen Resource Manager-mallar. Om du vill göra det lägger du till följande Resource Manager-mallar, definiera den `vmName` för den virtuella datorn på rätt sätt:

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

Du hittar mer detaljerad genomgång om hur du använder Resource Manager-mallar genom att läsa [översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

## <a name="next-steps"></a>Nästa steg
Du kanske vill [konfigurera Docker-daemon TCP-port](https://docs.docker.com/engine/reference/commandline/dockerd/#/bind-docker-to-another-hostport-or-a-unix-socket), Förstå [Docker security](https://docs.docker.com/engine/security/security/), eller distribuera behållare med hjälp av [Docker Compose](https://docs.docker.com/compose/overview/). Mer information om tillägget Azure Docker VM själva finns i den [GitHub-projektet](https://github.com/Azure/azure-docker-extension/).

Läs mer om de ytterligare alternativen i Azure gäller Docker-distribution:

* [Använda Docker Machine med Azure-drivrutin](docker-machine.md)  
* [Kom igång med Docker och Compose för att definiera och köra program med flera behållare på Azure-datorer](docker-compose-quickstart.md).
* [Distribuera ett Azure Container Service-kluster](../../container-service/dcos-swarm/container-service-deployment.md)

