---
title: "Använda Azure Docker VM-tillägget med Azure CLI 1.0 | Microsoft Docs"
description: "Lär dig hur du använder Docker VM-tillägget för att snabbt och säkert distribuera en Docker-miljö i Azure med hjälp av Resource Manager-mallar."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: a3cbcf63533f4042dcd695e141655c5814bd7068
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension-with-the-azure-cli-10"></a>Skapa en Docker-miljö i Azure med Azure CLI 1.0 Docker VM-tillägget
Docker är en populär behållarhantering och avbildningsverktyg plattform som gör att du snabbt arbeta med behållare för Linux (och även Windows). I Azure det, finns olika sätt som du kan distribuera Docker efter dina behov. Den här artikeln fokuserar på att använda Docker VM-tillägget och Azure Resource Manager-mallar. 

Mer information om olika distributionsmetoder, inklusive användning av Docker-dator och Azure Container Services finns i följande artiklar:

* Att snabbt prototyp en app du kan skapa en enda Docker-värd med [Docker datorn](docker-machine.md).
* För större och mer stabilt miljöer, kan du använda Azure Docker VM-tillägget, som också stöder [Docker Compose](https://docs.docker.com/compose/overview/) att generera konsekvent behållardistributionerna. Den här artikeln information med hjälp av Azure Docker VM-tillägget.
* När du skapar produktionsklara, skalbara miljöer som ger ytterligare schemaläggning och hanteringsverktygen ska du distribuera en [Docker Swarm-kluster på Azure Container Service](../../container-service/dcos-swarm/container-service-deployment.md).

## <a name="cli-versions-to-complete-the-task"></a>CLI-versioner för att slutföra uppgiften
Du kan slutföra uppgiften med någon av följande CLI-versioner:

- [Azure CLI 1.0](#azure-docker-vm-extension-overview) – våra CLI för klassisk och resurs management på distributionsmodeller (den här artikeln)
- [Azure CLI 2.0](dockerextension.md) – vår nästa generations CLI för distributionsmodellen resurshantering 

## <a name="azure-docker-vm-extension-overview"></a>Översikt av Azure Docker VM-tillägg
Azure Docker VM-tillägget installeras och konfigureras Docker-daemon, Docker-klienten och Docker Compose i Linux-dator (VM). Med hjälp av Azure Docker VM-tillägget kan ha mer kontroll och funktioner än att bara använda Docker-dator eller skapa Docker-värd själv. Dessa ytterligare funktioner som [Docker Compose](https://docs.docker.com/compose/overview/), se Azure Docker VM-tillägget som passar för stabilare utvecklare eller produktion miljöer.

Azure Resource Manager-mallar definiera hela strukturen för din miljö. Mallar kan du skapa och konfigurera resurser, till exempel Docker värden virtuella datorer, lagring, rollbaserad åtkomstkontroll (RBAC) och diagnostik. Du kan återanvända dessa mallar för att skapa ytterligare distributioner på ett konsekvent sätt. Mer information om Azure Resource Manager och mallar finns [översikt över Resource Manager](../../azure-resource-manager/resource-group-overview.md). 

## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>Distribuera en mall med Azure Docker VM-tillägg
Vi ska använda en befintlig mall för Snabbstart för att skapa en Ubuntu VM som använder Azure Docker VM-tillägget för att installera och konfigurera Docker-värden. Du kan visa den här mallen: [enkel distribution av en Ubuntu VM med Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

Du behöver den [senaste Azure CLI](../../cli-install-nodejs.md) installerad och logga in med Resource Manager-läget på följande sätt:

```azurecli
azure config mode arm
```

Distribuera mallen med hjälp av Azure CLI, ange URI för mallen. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *westus*. Använda egna resursgruppens namn och plats på följande sätt:

```azurecli
azure group create \
    --name myResourceGroup \
    --location westus \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Besvara anvisningarna för att namnge ditt lagringskonto, ange ett användarnamn och lösenord och ange ett DNS-namn. Utdata ser ut ungefär så här:

```azurecli
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Updating resource group myResourceGroup
info:    Updated resource group myResourceGroup
info:    Supply values for the following parameters
newStorageAccountName: mystorageaccount
adminUsername: azureuser
adminPassword: P@ssword!
dnsNameForPublicIP: mypublicidns
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

Azure CLI-returnerar du fråga efter endast några sekunder, men Docker-värden är fortfarande som skapat och konfigurerat Azure Docker VM-tillägget. Det tar några minuter för att distributionen ska slutföras. Du kan visa information om hur du använder för Docker värden status i `azure vm show` kommando.

I följande exempel kontrollerar status för den virtuella datorn med namnet *myDockerVM* (standardnamnet från mall - inte ändrar namnet) i resursgrupp med namnet *myResourceGroup*. Ange namnet på resursgruppen som du skapade i föregående steg:

```azurecli
azure vm show --resource-group myResourceGroup --name myDockerVM
```

Utdata från den `azure vm show` kommando som liknar följande exempel:

```azurecli
info:    Executing command vm show
+ Looking up the VM "myDockerVM"
+ Looking up the NIC "myVMNicD"
+ Looking up the public ip "myPublicIPD"
data:    Id                              :/subscriptions/guid/resourceGroups/myresourcegroup/providers/Microsoft.Compute/virtualMachines/MyDockerVM
data:    ProvisioningState               :Succeeded
data:    Name                            :MyDockerVM
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
[...]
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-D3-95
data:          Provisioning State        :Succeeded
data:          Name                      :myVMNicD
data:          Location                  :westus
data:            Public IP address       :13.91.107.235
data:            FQDN                    :mypublicdns.westus.cloudapp.azure.com
data:
data:    Diagnostics Instance View:
info:    vm show command OK
```

Längst upp i utdata, finns det **ProvisioningState** av den virtuella datorn. När detta visar *lyckades*distributionen är klar och du kan SSH till den virtuella datorn.

Mot slutet av utdata, *FQDN* visar fullständigt kvalificerade domännamnet för Docker-värden. Den här FQDN är det du använder till SSH till Docker-värden i stegen.

## <a name="deploy-your-first-nginx-container"></a>Distribuera din första nginx-behållare
När distributionen är klar, SSH till din nya Docker-värden från den lokala datorn. Ange användarnamn och FQDN på följande sätt:

```bash
ssh ops@mypublicdns.westus.cloudapp.azure.com
```

När inloggad till Docker-värden kan vi köra en nginx-behållare:

```bash
sudo docker run -d -p 80:80 nginx
```

Utdata liknar följande exempel som nginx-avbildningen hämtas och en behållare igång:

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

Utdata liknar följande exempel, visar att nginx-behållaren körs och TCP-portarna 80 och 443 och vidarebefordras:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

För att se din behållare i åtgärden, öppna en webbläsare och ange FQDN-namnet på din Docker-värd:

![Körs ngnix behållare](./media/dockerextension/nginxrunning.png)

## <a name="azure-docker-vm-extension-template-reference"></a>Azure Docker VM-mall tilläggsreferens
Det föregående exemplet används en befintlig mall för Snabbstart. Du kan också distribuera Azure Docker VM-tillägget med egna Resource Manager-mallar. Om du vill göra det lägger du till följande Resource Manager-mallar, definiera den *vmName* på den virtuella datorn på rätt sätt:

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
    "typeHandlerVersion": "1.1",
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

