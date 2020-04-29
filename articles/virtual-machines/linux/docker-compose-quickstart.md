---
title: Använda Docker Compose
description: Installera och använda Docker och skapa virtuella Linux-datorer med Azure CLI.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 02/14/2019
ms.author: cynthn
ms.openlocfilehash: 434a3ef8c9bc1738252d59a5dca5bec16d85e45e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "78970312"
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Kom igång med Docker och skapa för att definiera och köra ett program med flera behållare i Azure
Med [Skriv](https://github.com/docker/compose), använder du en enkel textfil för att definiera ett program som består av flera Docker-behållare. Sedan kan du sätta upp ditt program i ett enda kommando som gör allt för att distribuera din definierade miljö. I den här artikeln visas till exempel hur du snabbt konfigurerar en WordPress-blogg med en backend-MariaDB SQL-databas på en Ubuntu VM. Du kan också använda komponera för att ställa in mer komplexa program.

Den här artikeln testades senast 2/14/2019 med hjälp av [Azure Cloud Shell](https://shell.azure.com/bash) och [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) -versionen 2.0.58.

## <a name="create-docker-host-with-azure-cli"></a>Skapa Docker-värd med Azure CLI
Installera den senaste versionen av [Azure CLI](/cli/azure/install-az-cli2) och logga in på ett Azure-konto med [AZ-inloggning](/cli/azure/reference-index).

Skapa först en resurs grupp för Docker-miljön med [AZ Group Create](/cli/azure/group). I följande exempel skapas en resurs grupp med namnet *myResourceGroup* på platsen för *öster* :

```azurecli-interactive
az group create --name myDockerGroup --location eastus
```

Skapa en fil med namnet *init. txt* och klistra in följande konfiguration. Ange `sensible-editor cloud-init.txt` för att skapa filen och visa en lista över tillgängliga redigeringsprogram. 

```yaml
#include https://get.docker.com
```

Skapa nu en virtuell dator med [az vm create](/cli/azure/vm#az-vm-create). Använd parametern `--custom-data` för att skicka in din cloud-init-konfigurationsfil. Ange den fullständiga sökvägen till *cloud-init.txt* om du sparat filen utanför din aktuella arbetskatalog. I följande exempel skapas en virtuell dator med namnet *myDockerVM* och öppnar port 80 för webb trafik.

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

                 

## <a name="install-compose"></a>Installera Skriv


SSH till din nya Docker-värd VM. Ange din egen IP-adress.

```bash
ssh azureuser@10.10.111.11
```

Installera skapa på den virtuella datorn.

```bash
sudo apt install docker-compose
```


## <a name="create-a-docker-composeyml-configuration-file"></a>Skapa en konfigurations fil för filen Docker. yml
Skapa en `docker-compose.yml` konfigurations fil för att definiera Docker-behållare som ska köras på den virtuella datorn. Filen anger den avbildning som ska köras på varje behållare, nödvändiga miljövariabler och beroenden, portar och länkarna mellan behållare. Mer information om YML finns i referens för [Skriv fil](https://docs.docker.com/compose/compose-file/).

Skapa en *filen Docker. yml* -fil. Använd text redigeraren för att lägga till data i filen. I följande exempel skapas filen med en prompt för `sensible-editor` för att välja en redigerare som du vill använda.

```bash
sensible-editor docker-compose.yml
```

Klistra in följande exempel i din Docker-filfil. Den här konfigurationen använder avbildningar från [DockerHub-registret](https://registry.hub.docker.com/_/wordpress/) för att installera WordPress (blogg-och innehålls hanterings system med öppen källkod) och en länkad server dels MariaDB SQL-databas. Ange din egen *MYSQL_ROOT_PASSWORD*.

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

## <a name="start-the-containers-with-compose"></a>Starta behållarna med Skriv
I samma katalog som din *filen Docker. yml* -fil kör du följande kommando (beroende på din miljö kan du behöva köra `docker-compose` med `sudo`):

```bash
sudo docker-compose up -d
```

Det här kommandot startar Docker-behållare som anges i *filen Docker. yml*. Det tar en minut eller två innan det här steget har slutförts. Du ser utdata som liknar följande:

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```


Kontrol lera att behållarna är upp genom `sudo docker-compose ps`att skriva. Du bör se något liknande följande:

```
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

Nu kan du ansluta till WordPress direkt på den virtuella datorn på port 80. Öppna en webbläsare och ange IP-adress namnet för den virtuella datorn. Du bör nu se den WordPress start skärmen där du kan slutföra installationen och komma igång med programmet.

![WordPress-Start skärm](./media/docker-compose-quickstart/wordpressstart.png)

## <a name="next-steps"></a>Nästa steg
* Kolla in [kommando rads referens](https://docs.docker.com/compose/reference/) och [användar guide](https://docs.docker.com/compose/) för att se fler exempel på hur du skapar och distribuerar appar för flera behållare.
* Använd en Azure Resource Manager mall, antingen din egen eller en som har bidragit från [communityn](https://azure.microsoft.com/documentation/templates/), för att distribuera en virtuell Azure-dator med Docker och ett program som har kon figurer ATS med Skriv. Till exempel kan [distribuera en WordPress-blogg med Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) -mallen använda Docker och skapa för att snabbt distribuera WordPress med en MySQL-Server del på en virtuell Ubuntu-dator.
* Prova att integrera Docker tillsammans med ett Docker Swarm-kluster. Se [använda komponera med Swarm](https://docs.docker.com/compose/swarm/) för scenarier.

