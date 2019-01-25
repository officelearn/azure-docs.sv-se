---
title: Använda Docker Machine för att skapa Linux-värdar i Azure | Microsoft Docs
description: Beskriver hur du använder Docker Machine för att skapa Docker-värdar i Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
ms.assetid: 164b47de-6b17-4e29-8b7d-4996fa65bea4
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: a59183365ada644f2ff1aae7d52bac5fb1f42354
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54888880"
---
# <a name="how-to-use-docker-machine-to-create-hosts-in-azure"></a>Hur du använder Docker Machine för att skapa värdar i Azure
Den här artikeln beskriver hur du använder [Docker Machine](https://docs.docker.com/machine/) att skapa värdar i Azure. Den `docker-machine` kommandot skapar en Linux-dator (VM) i Azure och sedan installerar Docker. Du kan sedan hantera Docker-värdar i Azure med hjälp av samma lokala verktyg och arbetsflöden. Om du vill använda docker-dator i Windows 10, måste du använda bash i Linux.

## <a name="create-vms-with-docker-machine"></a>Skapa virtuella datorer med Docker-dator
Först hämta ditt Azure-prenumeration-ID med [az konto show](/cli/azure/account#az_account_show) på följande sätt:

```azurecli
sub=$(az account show --query "id" -o tsv)
```

Du skapar Docker-värd virtuella datorer i Azure med `docker-machine create` genom att ange *azure* som drivrutinen. Mer information finns i den [Docker Azure Driver-dokumentationen](https://docs.docker.com/machine/drivers/azure/)

I följande exempel skapas en virtuell dator med namnet *myVM*, beroende på ”Standard D2 v2” plan, skapar ett användarkonto med namnet *azureuser*, och öppnar port *80* på den Virtuella värddatorn. Följ eventuella uppmaningar att logga in på ditt Azure-konto och ge Docker Machine behörigheter att skapa och hantera resurser.

```bash
docker-machine create -d azure \
    --azure-subscription-id $sub \
    --azure-ssh-user azureuser \
    --azure-open-port 80 \
    --azure-size "Standard_DS2_v2" \
    myvm
```

Utdata ser ut ungefär så här:

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

## <a name="configure-your-docker-shell"></a>Konfigurera ditt Docker-gränssnitt
Definiera rätt anslutningsinställningar för att ansluta till din Docker-värd i Azure. Enligt vad som anges i slutet av utdata, visa anslutningsinformation för Docker-värd på följande sätt: 

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

Om du vill definiera anslutningsinställningar för, kan du köra kommandot föreslagna configuration (`eval $(docker-machine env myvm)`), eller du kan ange miljövariabler manuellt. 

## <a name="run-a-container"></a>Köra en behållare
För att se en behållare i praktiken, kan köra en grundläggande NGINX-webbserver. Skapa en behållare med `docker run` och exponera port 80 för webbtrafik på följande sätt:

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

Visa köra behållare med `docker ps`. Följande Exempelutdata visar NGINX-behållaren som körs med port 80 som visas:

```bash
CONTAINER ID    IMAGE    COMMAND                   CREATED          STATUS          PORTS                          NAMES
d5b78f27b335    nginx    "nginx -g 'daemon off"    5 minutes ago    Up 5 minutes    0.0.0.0:80->80/tcp, 443/tcp    festive_mirzakhani
```

## <a name="test-the-container"></a>Testa behållaren
Hämta den offentliga IP-adressen för Docker-värd på följande sätt:


```bash
docker-machine ip myvm
```

För att se behållaren i praktiken, öppna en webbläsare och ange den offentliga IP-adressen som anges i utdata från föregående kommando:

![Behållaren som körs ngnix](./media/docker-machine/nginx.png)

## <a name="next-steps"></a>Nästa steg
Du kan också skapa värdar med de [Docker VM-tillägg](dockerextension.md). Exempel på med Docker Compose finns [Kom igång med Docker och Compose i Azure](docker-compose-quickstart.md).
