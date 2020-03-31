---
title: Använda Docker Compose
description: Så här installerar och använder du Docker och Compose på virtuella Linux-datorer med Azure CLI.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 02/14/2019
ms.author: cynthn
ms.openlocfilehash: 434a3ef8c9bc1738252d59a5dca5bec16d85e45e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970312"
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Kom igång med Docker och Compose för att definiera och köra ett program med flera behållare i Azure
Med [Compose](https://github.com/docker/compose)använder du en enkel textfil för att definiera ett program som består av flera Docker-behållare. Du startar sedan ditt program i ett enda kommando som gör allt för att distribuera din definierade miljö. Som ett exempel visar den här artikeln hur du snabbt ställer in en WordPress-blogg med en serverd MariaDB SQL-databas på en Ubuntu-vm. Du kan också använda Compose för att konfigurera mer komplexa program.

Den här artikeln testades senast den 2019-02-14 med [Azure Cloud Shell](https://shell.azure.com/bash) och Azure [CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) version 2.0.58.

## <a name="create-docker-host-with-azure-cli"></a>Skapa Docker-värd med Azure CLI
Installera den senaste [Azure CLI](/cli/azure/install-az-cli2) och logga in på ett Azure-konto med [az login](/cli/azure/reference-index).

Skapa först en resursgrupp för Docker-miljön med [az-gruppskapa](/cli/azure/group). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på *eastus-platsen:*

```azurecli-interactive
az group create --name myDockerGroup --location eastus
```

Skapa en fil med namnet *cloud-init.txt* och klistra in följande konfiguration. Ange `sensible-editor cloud-init.txt` för att skapa filen och visa en lista över tillgängliga redigeringsprogram. 

```yaml
#include https://get.docker.com
```

Skapa nu en virtuell dator med [az vm create](/cli/azure/vm#az-vm-create). Använd parametern `--custom-data` för att skicka in din cloud-init-konfigurationsfil. Ange den fullständiga sökvägen till *cloud-init.txt* om du sparat filen utanför din aktuella arbetskatalog. I följande exempel skapas en virtuell dator med namnet *myDockerVM* och port 80 öppnas för webbtrafik.

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

                 

## <a name="install-compose"></a>Installera compose


SSH till din nya Docker-värd-VM. Ange din egen IP-adress.

```bash
ssh azureuser@10.10.111.11
```

Installera Komponera på den virtuella datorn.

```bash
sudo apt install docker-compose
```


## <a name="create-a-docker-composeyml-configuration-file"></a>Skapa en docker-compose.yml-konfigurationsfil
Skapa `docker-compose.yml` en konfigurationsfil för att definiera Docker-behållarna som ska köras på den virtuella datorn. Filen anger vilken avbildning som ska köras på varje behållare, nödvändiga miljövariabler och beroenden, portar och länkar mellan behållare. Mer information om syntaxen för yml-fil finns i [Skriv filreferens](https://docs.docker.com/compose/compose-file/).

Skapa en *docker-compose.yml-fil.* Använd din favorittextredigerare för att lägga till data i filen. I följande exempel skapas filen `sensible-editor` med en uppmaning om att välja en redigerare som du vill använda.

```bash
sensible-editor docker-compose.yml
```

Klistra in följande exempel i Docker Compose-filen. Denna konfiguration använder bilder från [DockerHub-registret](https://registry.hub.docker.com/_/wordpress/) för att installera WordPress (open source blogging och content management system) och en länkad serverd MariaDB SQL-databas. Ange din egen *MYSQL_ROOT_PASSWORD*.

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
I samma katalog som *filen docker-compose.yml* kör du följande kommando (beroende på `docker-compose` `sudo`din miljö kan du behöva köra med):

```bash
sudo docker-compose up -d
```

Det här kommandot startar Docker-behållarna som anges i *docker-compose.yml*. Det tar en minut eller två för detta steg att slutföra. Du ser utdata som liknar följande:

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```


Om du vill kontrollera att `sudo docker-compose ps`behållarna är uppe skriver du . Du bör se något liknande följande:

```
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

Du kan nu ansluta till WordPress direkt på den virtuella datorn på port 80. Öppna en webbläsare och ange IP-adressnamnet för den virtuella datorn. Du bör nu se WordPress startskärmen, där du kan slutföra installationen och komma igång med programmet.

![Startskärmen för WordPress](./media/docker-compose-quickstart/wordpressstart.png)

## <a name="next-steps"></a>Nästa steg
* I [kommandoradsreferensen för skriv](https://docs.docker.com/compose/reference/) rad och [användarhandboken](https://docs.docker.com/compose/) finns fler exempel på hur du skapar och distribuerar appar med flera behållare.
* Använd en Azure Resource Manager-mall, antingen din egen eller en bidragit från [communityn,](https://azure.microsoft.com/documentation/templates/)för att distribuera en Virtuell Azure-dator med Docker och ett program som konfigurerats med Compose. Till exempel använder [Distribuera en WordPress blogg med Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) mall Docker och Komponera för att snabbt distribuera WordPress med en MySQL backend på en Ubuntu VM.
* Prova att integrera Docker Compose med ett Docker Swarm-kluster. Se [Använda Komponera med svärm](https://docs.docker.com/compose/swarm/) för scenarier.

