---
title: "Använda Docker-dator för att skapa Linux-värdar i Azure | Microsoft Docs"
description: "Beskriver hur du kan använda Docker-dator för att skapa Docker-värdar i Azure."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
ms.assetid: 164b47de-6b17-4e29-8b7d-4996fa65bea4
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: iainfou
ms.openlocfilehash: efd0b60079017e476b54acfc65ffe8f35ffe4933
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2017
---
# <a name="how-to-use-docker-machine-to-create-hosts-in-azure"></a>Använda Docker-datorn för att skapa värdar i Azure
Den här artikeln beskriver hur du använder [Docker datorn](https://docs.docker.com/machine/) att skapa värdar i Azure. Den `docker-machine` kommando skapar en Linux-dator (VM) i Azure och sedan installerar Docker. Du kan sedan hantera Docker-värdar i Azure med hjälp av samma lokala verktyg och arbetsflöden. Om du vill använda docker-dator i Windows 10, måste du använda Linux bash.

## <a name="create-vms-with-docker-machine"></a>Skapa virtuella datorer med Docker-dator
Först hämta ditt Azure prenumerations-ID med [az konto visa](/cli/azure/account#show) på följande sätt:

```azurecli
sub=$(az account show --query "id" -o tsv)
```

Du skapar virtuella datorer Docker-värden i Azure med `docker-machine create` genom att ange *azure* som drivrutinen. Mer information finns i [Docker Azure Driver-dokumentationen](https://docs.docker.com/machine/drivers/azure/)

I följande exempel skapas en virtuell dator med namnet *myVM*, baserat på ”Standard D2 v2” planen, skapar ett användarkonto med namnet *azureuser*, och öppnar port *80* på den Virtuella värddatorn. Följ anvisningarna för att logga in på kontot och bevilja Docker datorn behörigheter att skapa och hantera resurser.

```bash
docker-machine create -d azure \
    --azure-subscription-id $sub \
    --azure-ssh-user azureuser \
    --azure-open-port 80 \
    --azure-size "Standard_D2_v2" \
    myvm
```

Utdata liknar följande exempel:

```bash
Creating CA: /Users/user/.docker/machine/certs/ca.pem
Creating client certificate: /Users/user/.docker/machine/certs/cert.pem
Running pre-create checks...
(myvmdocker) Completed machine pre-create checks.
Creating machine...
(myvmdocker) Querying existing resource group.  name="docker-machine"
(myvmdocker) Creating resource group.  name="docker-machine" location="westus"
(myvmdocker) Configuring availability set.  name="docker-machine"
(myvmdocker) Configuring network security group.  name="myvmdocker-firewall" location="westus"
(myvmdocker) Querying if virtual network already exists.  rg="docker-machine" location="westus" name="docker-machine-vnet"
(myvmdocker) Creating virtual network.  name="docker-machine-vnet" rg="docker-machine" location="westus"
(myvmdocker) Configuring subnet.  name="docker-machine" vnet="docker-machine-vnet" cidr="192.168.0.0/16"
(myvmdocker) Creating public IP address.  name="myvmdocker-ip" static=false
(myvmdocker) Creating network interface.  name="myvmdocker-nic"
(myvmdocker) Creating storage account.  sku=Standard_LRS name="vhdski0hvfazyd8mn991cg50" location="westus"
(myvmdocker) Creating virtual machine.  location="westus" size="Standard_A2" username="azureuser" osImage="canonical:UbuntuServer:16.04.0-LTS:latest" name="myvmdocker"
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
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env myvmdocker
```

## <a name="configure-your-docker-shell"></a>Konfigurera Docker-gränssnittet
Definiera rätt anslutningsinställningar för att ansluta till Docker-värden i Azure. Som anges i slutet av utdata visa anslutningsinformationen för Docker-värden på följande sätt: 

```bash
docker-machine env myvmdocker
```

Utdata ser ut ungefär så här:

```bash
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://40.68.254.142:2376"
export DOCKER_CERT_PATH="/Users/user/.docker/machine/machines/machine"
export DOCKER_MACHINE_NAME="machine"
# Run this command to configure your shell:
# eval $(docker-machine env myvmdocker)
```

Anslutningen ska definieras inställningar som du kan antingen köra kommandot föreslagna configuration (`eval $(docker-machine env myvmdocker)`), eller att du anger miljövariabler manuellt. 

## <a name="run-a-container"></a>Kör en behållare
Du vill se en behållare i åtgärden, kan köra en grundläggande NGINX-webbserver. Skapa en behållare med `docker run` och exponera port 80 för Internet-trafik på följande sätt:

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

Visa kör behållare med `docker ps`. Följande visas exempel NGINX-behållaren med port 80 som visas:

```bash
CONTAINER ID    IMAGE    COMMAND                   CREATED          STATUS          PORTS                          NAMES
d5b78f27b335    nginx    "nginx -g 'daemon off"    5 minutes ago    Up 5 minutes    0.0.0.0:80->80/tcp, 443/tcp    festive_mirzakhani
```

## <a name="test-the-container"></a>Testa behållaren
Hämta den offentliga IP-adressen för Docker-värd på följande sätt:


```bash
docker-machine ip myvmdocker
```

Om du vill visa behållaren i åtgärden, öppna en webbläsare och ange den offentliga IP-adressen som anges i utdata från kommandot ovan:

![Körs ngnix behållare](./media/docker-machine/nginx.png)

## <a name="next-steps"></a>Nästa steg
Du kan också skapa värdar med den [Docker VM-tillägget](dockerextension.md). Exempel på med Docker Compose finns [Kom igång med Docker och Skriv i Azure](docker-compose-quickstart.md).
