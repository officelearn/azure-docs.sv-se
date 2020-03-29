---
title: Använd Docker Machine för att skapa Linux-värdar
description: Beskriver hur du använder Docker Machine för att skapa Docker-värdar i Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: c3165410809d98fd0ac4eeb515fbf30578633ef3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968808"
---
# <a name="how-to-use-docker-machine-to-create-hosts-in-azure"></a>Så här använder du Docker Machine för att skapa värdar i Azure
I den här artikeln beskrivs hur du använder [Docker Machine](https://docs.docker.com/machine/) för att skapa värdar i Azure. Kommandot `docker-machine` skapar en virtuell Linux-dator (VM) i Azure och installerar sedan Docker. Du kan sedan hantera dina Docker-värdar i Azure med samma lokala verktyg och arbetsflöden. Om du vill använda docker-maskin i Windows 10 måste du använda Linux bash.

## <a name="create-vms-with-docker-machine"></a>Skapa virtuella datorer med Docker Machine
Skaffa först ditt Azure-prenumerations-ID med [az-kontoprogram](/cli/azure/account) enligt följande:

```azurecli
sub=$(az account show --query "id" -o tsv)
```

Du skapar Docker-värd virtuella `docker-machine create` datorer i Azure med genom att ange *azure* som drivrutin. Mer information finns i Dokumentationen till [Docker Azure Driver](https://docs.docker.com/machine/drivers/azure/)

I följande exempel skapas en virtuell dator med namnet *myVM*, baserat på "Standard D2 v2"-plan, skapar ett användarkonto med namnet *azureuser*och öppnar port *80* på värddatorn. Följ alla uppmaningar om att logga in på ditt Azure-konto och bevilja Docker Machine-behörigheter för att skapa och hantera resurser.

```bash
docker-machine create -d azure \
    --azure-subscription-id $sub \
    --azure-ssh-user azureuser \
    --azure-open-port 80 \
    --azure-size "Standard_DS2_v2" \
    myvm
```

Utdata liknar följande exempel:

```bash
Creating CA: /Users/user/.docker/machine/certs/ca.pem
Creating client certificate: /Users/user/.docker/machine/certs/cert.pem
Running pre-create checks...
(myvm) Completed machine pre-create checks.
Creating machine...
(myvm) Querying existing resource group.  name="docker-machine"
(myvm) Creating resource group.  name="docker-machine" location="westus"
(myvm) Configuring availability set.  name="docker-machine"
(myvm) Configuring network security group.  name="myvm-firewall" location="westus"
(myvm) Querying if virtual network already exists.  rg="docker-machine" location="westus" name="docker-machine-vnet"
(myvm) Creating virtual network.  name="docker-machine-vnet" rg="docker-machine" location="westus"
(myvm) Configuring subnet.  name="docker-machine" vnet="docker-machine-vnet" cidr="192.168.0.0/16"
(myvm) Creating public IP address.  name="myvm-ip" static=false
(myvm) Creating network interface.  name="myvm-nic"
(myvm) Creating storage account.  sku=Standard_LRS name="vhdski0hvfazyd8mn991cg50" location="westus"
(myvm) Creating virtual machine.  location="westus" size="Standard_A2" username="azureuser" osImage="canonical:UbuntuServer:16.04.0-LTS:latest" name="myvm
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with ubuntu(systemd)...
Installing Docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env myvm
```

## <a name="configure-your-docker-shell"></a>Konfigurera dockerskalet
Om du vill ansluta till Docker-värden i Azure definierar du lämpliga anslutningsinställningar. Som anges i slutet av utdata kan du visa anslutningsinformationen för Docker-värden på följande sätt: 

```bash
docker-machine env myvm
```

Utdata ser ut ungefär så här:

```bash
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://40.68.254.142:2376"
export DOCKER_CERT_PATH="/Users/user/.docker/machine/machines/machine"
export DOCKER_MACHINE_NAME="machine"
# Run this command to configure your shell:
# eval $(docker-machine env myvm)
```

Om du vill definiera anslutningsinställningarna kan`eval $(docker-machine env myvm)`du antingen köra det föreslagna konfigurationskommandot ( ) eller ange miljövariabler manuellt. 

## <a name="run-a-container"></a>Köra en behållare
Om du vill visa en behållare i åtgärd kan du köra en grundläggande NGINX-webbserver. Skapa en `docker run` behållare med och exponera port 80 för webbtrafik enligt följande:

```bash
docker run -d -p 80:80 --restart=always nginx
```

Utdata ser ut ungefär så här:

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
ff3d52d8f55f: Pull complete
226f4ec56ba3: Pull complete
53d7dd52b97d: Pull complete
Digest: sha256:41ad9967ea448d7c2b203c699b429abe1ed5af331cd92533900c6d77490e0268
Status: Downloaded newer image for nginx:latest
675e6056cb81167fe38ab98bf397164b01b998346d24e567f9eb7a7e94fba14a
```

Visa löpande `docker ps`behållare med . Följande exempelutdata visar NGINX-behållaren som körs med port 80 exponerad:

```bash
CONTAINER ID    IMAGE    COMMAND                   CREATED          STATUS          PORTS                          NAMES
d5b78f27b335    nginx    "nginx -g 'daemon off"    5 minutes ago    Up 5 minutes    0.0.0.0:80->80/tcp, 443/tcp    festive_mirzakhani
```

## <a name="test-the-container"></a>Testa behållaren
Hämta den offentliga IP-adressen för Docker-värden enligt följande:


```bash
docker-machine ip myvm
```

Om du vill se behållaren i aktion öppnar du en webbläsare och anger den offentliga IP-adress som anges i utdata från föregående kommando:

![Köra ngnix-behållare](./media/docker-machine/nginx.png)

## <a name="next-steps"></a>Nästa steg
Exempel på hur du använder Docker Compose finns [i Komma igång med Docker och Compose i Azure](docker-compose-quickstart.md).
