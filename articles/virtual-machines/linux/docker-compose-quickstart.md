---
title: Använda Docker Compose på en Linux-dator i Azure | Microsoft Docs
description: Hur du installerar och använder Docker och Compose på Linux-datorer med Azure CLI
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
ms.date: 02/14/2019
ms.author: cynthn
ms.openlocfilehash: 3aa3b29ef44d3efb21237dc0d82a1ee6e99e729b
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2019
ms.locfileid: "56328953"
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Kom igång med Docker och Compose för att definiera och köra program med flera behållare i Azure
Med [Compose](http://github.com/docker/compose), du använda en exempeltextfil för att definiera ett program som består av flera Docker-behållare. Sedan sätta upp ditt program i ett enda kommando som gör allt för att distribuera din definierade miljö. Exempelvis kan visar den här artikeln hur du snabbt konfigurera en WordPress-blogg med en serverdel MariaDB SQL-databas på en Ubuntu-VM. Du kan också använda Skriv för att ställa in mer komplexa program.

Den här artikeln senast har testats på 2/14/2019 med hjälp av den [Azure Cloud Shell](https://shell.azure.com/bash) och [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) version 2.0.58.

## <a name="create-docker-host-with-azure-cli"></a>Skapa Docker-värd med Azure CLI
Installera senast [Azure CLI](/cli/azure/install-az-cli2) och logga in på Azure med hjälp av [az-inloggning](/cli/azure/reference-index).

Skapa först en resursgrupp för din Docker-miljö med [az gruppen skapa](/cli/azure/group). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli-interactive
az group create --name myDockerGroup --location eastus
```

Skapa en fil med namnet *cloud-init.txt* och klistra in följande konfiguration. Ange `sensible-editor cloud-init.txt` för att skapa filen och visa en lista över tillgängliga redigeringsprogram. 

```yaml
#include https://get.docker.com
```

Skapa nu en virtuell dator med [az vm create](/cli/azure/vm#az-vm-create). Använd parametern `--custom-data` för att skicka in din cloud-init-konfigurationsfil. Ange den fullständiga sökvägen till *cloud-init.txt* om du sparat filen utanför din aktuella arbetskatalog. I följande exempel skapas en virtuell dator med namnet *myDockerVM* och öppnas port 80 för webbtrafik.

```azurecli-interactive
az vm create \
    --resource-group myDockerGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
az vm open-port --port 80 \
    --resource-group myDockerGroup \
    --name myDockerVM
```

Det tar några minuter innan den virtuella datorn skapas, paketen installeras och appen startar. Det finns bakgrundsaktiviteter som fortsätter att köras när Azure CLI återgår till kommandotolken. När den virtuella datorn har skapats ska du anteckna `publicIpAddress` som visas av Azure CLI. 

                 

## <a name="install-compose"></a>Installera Compose


SSH till din nya Docker-värd VM. Ange din egen IP-adress.

```bash
ssh azureuser@10.10.111.11
```

Installera Compose på den virtuella datorn.

```bash
sudo apt install docker-compose
```


## <a name="create-a-docker-composeyml-configuration-file"></a>Skapa en docker-compose.yml-konfigurationsfil
Skapa en `docker-compose.yml` konfigurationsfilen för att definiera Docker-behållare för att köra på den virtuella datorn. Filen anger avbildningen som ska köras på varje behållare, behövs miljövariabler och beroenden, portar och länkarna mellan behållare. Mer information om yml file-syntax finns [Compose filreferens](https://docs.docker.com/compose/compose-file/).

Skapa en *docker-compose.yml* fil. Använd valfri textredigerare för att lägga till vissa data i filen. I följande exempel skapas filen med en uppmaning för `sensible-editor` att välja en redigerare som du vill använda.

```bash
sensible-editor docker-compose.yml
```

Klistra in i följande exempel i Docker Compose-fil. Den här konfigurationen använder avbildningar från den [DockerHub registret](https://registry.hub.docker.com/_/wordpress/) att installera WordPress (öppen källkod blogga och innehåll hanteringssystemet) och en länkad serverdel MariaDB SQL-databas. Ange ett eget *MYSQL_ROOT_PASSWORD*.

```yml
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
sudo docker-compose up -d
```

Det här kommandot startar Docker-behållare som angetts i *docker-compose.yml*. Det tar en minut eller två för det här steget att slutföra. Du ser utdata som liknar följande:

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```


Så här kontrollerar du att behållarna som är igång `sudo docker-compose ps`. Du bör se något som liknar:

```
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

Du kan nu ansluta till WordPress direkt på den virtuella datorn på port 80. Öppna en webbläsare och ange IP-adressnamn för den virtuella datorn. Du bör nu se WordPress startskärmen, där du kan slutföra installationen och kom igång med programmet.

![Startskärmen för WordPress](./media/docker-compose-quickstart/wordpressstart.png)

## <a name="next-steps"></a>Nästa steg
* Kolla in den [Compose Kommandoradsreferens](http://docs.docker.com/compose/reference/) och [användarhandboken](http://docs.docker.com/compose/) fler exempel på att skapa och distribuera appar för flera behållare.
* Använda en Azure Resource Manager-mall, antingen din egen eller en som tillförts från den [community](https://azure.microsoft.com/documentation/templates/), för att distribuera en virtuell Azure-dator med Docker och ett program som konfigurerats med Compose. Till exempel den [distribuera en WordPress-blogg med Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) mallen använder Docker och Compose för att snabbt distribuera WordPress med MySQL-serverdel på en Ubuntu-VM.
* Försök integrera Docker Compose med ett Docker Swarm-kluster. Se [med hjälp av Compose med Swarm](https://docs.docker.com/compose/swarm/) för scenarier.

