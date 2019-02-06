---
title: Använda Docker Compose på en Linux-dator i Azure | Microsoft Docs
description: Hur du använder Docker och Compose på Linux-datorer med Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 02ab8cf9-318d-4a28-9d0c-4a31dccc2a84
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: cynthn
ms.openlocfilehash: 257083e1ae0c3c1cb3c5421882ffd0e06e2d1f5c
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55752151"
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Kom igång med Docker och Compose för att definiera och köra program med flera behållare i Azure
Med [Compose](http://github.com/docker/compose), du använda en exempeltextfil för att definiera ett program som består av flera Docker-behållare. Sedan sätta upp ditt program i ett enda kommando som gör allt för att distribuera din definierade miljö. Exempelvis kan visar den här artikeln hur du snabbt konfigurera en WordPress-blogg med en serverdel MariaDB SQL-databas på en Ubuntu-VM. Du kan också använda Skriv för att ställa in mer komplexa program.


## <a name="set-up-a-linux-vm-as-a-docker-host"></a>Konfigurera en Linux VM som Docker-värd
Du kan använda olika Azure procedurer och tillgängliga avbildningar eller Resource Manager-mallar på Azure Marketplace för att skapa en Linux-VM och installera den som en Docker-värd. Se exempelvis [använder Docker VM-tillägg för att distribuera din miljö](dockerextension.md) du snabbt skapar en Ubuntu VM med tillägget Azure Docker VM med hjälp av en [snabbstartsmall](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

När du använder Docker VM-tillägget kan den virtuella datorn konfigureras automatiskt som en Docker-värd och Skriv redan är installerad.


### <a name="create-docker-host-with-azure-cli"></a>Skapa Docker-värd med Azure CLI
Installera senast [Azure CLI](/cli/azure/install-az-cli2) och logga in på Azure med hjälp av [az-inloggning](/cli/azure/reference-index).

Skapa först en resursgrupp för din Docker-miljö med [az gruppen skapa](/cli/azure/group). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Distribuera en virtuell dator med [az group deployment skapa](/cli/azure/group/deployment) som innehåller Azure Docker VM-tillägget från [Azure Resource Manager-mallen på GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). När du uppmanas, anger du ditt eget unika värden för *newStorageAccountName*, *adminUsername*, *adminPassword*, och *dnsNameForPublicIP*:

```azurecli
az group deployment create --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Det tar några minuter innan distributionen är klar.


## <a name="verify-that-compose-is-installed"></a>Kontrollera att Skriv är installerat
Du kan visa information om den virtuella datorn, inklusive DNS-namnet [az vm show](/cli/azure/vm):

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

Om du vill kontrollera att Skriv är installerat på den virtuella datorn, kör du följande kommando:

```bash
docker-compose --version
```

Du ser utdata som liknar *docker-compose 1.6.2, skapa 4d 72027*.

> [!TIP]
> Om du använder en annan metod för att skapa en Docker-värd och måste installera Compose själv, finns i den [Compose dokumentation](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md).


## <a name="create-a-docker-composeyml-configuration-file"></a>Skapa en docker-compose.yml-konfigurationsfil
Sedan skapar du en `docker-compose.yml` -fil som är bara en text konfigurationsfil, definiera Docker-behållare för att köra på den virtuella datorn. Filen anger avbildningen som ska köras på varje behållare (eller det kan vara en version från en Dockerfile) behövs miljövariabler och beroenden, portar och länkarna mellan behållare. Mer information om yml file-syntax finns [Compose filreferens](https://docs.docker.com/compose/compose-file/).

Skapa en *docker-compose.yml* fil. Använd valfri textredigerare för att lägga till vissa data i filen. I följande exempel skapas filen med en uppmaning för `sensible-editor` att välja en redigerare som du vill använda:

```bash
sensible-editor docker-compose.yml
```

Klistra in i följande exempel i Docker Compose-fil. Den här konfigurationen använder avbildningar från den [DockerHub registret](https://registry.hub.docker.com/_/wordpress/) att installera WordPress (öppen källkod blogga och innehåll hanteringssystemet) och en länkad serverdel MariaDB SQL-databas. Ange ett eget *MYSQL_ROOT_PASSWORD* på följande sätt:

```sh
wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>
```

## <a name="start-the-containers-with-compose"></a>Starta behållarna med Compose
I samma katalog som din *docker-compose.yml* fil, kör du följande kommando (beroende på din miljö kan du behöva köra `docker-compose` med `sudo`):

```bash
docker-compose up -d
```

Det här kommandot startar Docker-behållare som angetts i *docker-compose.yml*. Det tar en minut eller två för det här steget att slutföra. Du ser utdata som liknar följande exempel:

```bash
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```

> [!NOTE]
> Se till att använda den **-d** alternativet på Start så att behållarna som körs kontinuerligt i bakgrunden.


Så här kontrollerar du att behållarna som är igång `docker-compose ps`. Du bör se något som liknar:

```bash
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

Du kan nu ansluta till WordPress direkt på den virtuella datorn på port 80. Öppna en webbläsare och ange DNS-namnet på den virtuella datorn (till exempel `http://mypublicdns.eastus.cloudapp.azure.com`). Du bör nu se WordPress startskärmen, där du kan slutföra installationen och kom igång med programmet.

![Startskärmen för WordPress][wordpress_start]

## <a name="next-steps"></a>Nästa steg
* Gå till den [Användarhandbok för Docker VM-tillägget](https://github.com/Azure/azure-docker-extension/blob/master/README.md) för fler alternativ för att konfigurera Docker och Compose i dina virtuella datorer med Docker. Till exempel är ett alternativ att placera filen Skriv yml (omvandlat till JSON) direkt i konfigurationen av Docker VM-tillägget.
* Kolla in den [Compose Kommandoradsreferens](http://docs.docker.com/compose/reference/) och [användarhandboken](http://docs.docker.com/compose/) fler exempel på att skapa och distribuera appar för flera behållare.
* Använda en Azure Resource Manager-mall, antingen din egen eller en som tillförts från den [community](https://azure.microsoft.com/documentation/templates/), för att distribuera en virtuell Azure-dator med Docker och ett program som konfigurerats med Compose. Till exempel den [distribuera en WordPress-blogg med Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) mallen använder Docker och Compose för att snabbt distribuera WordPress med MySQL-serverdel på en Ubuntu-VM.
* Försök integrera Docker Compose med ett Docker Swarm-kluster. Se [med hjälp av Compose med Swarm](https://docs.docker.com/compose/swarm/) för scenarier.

<!--Image references-->

[wordpress_start]: media/docker-compose-quickstart/WordPress.png
