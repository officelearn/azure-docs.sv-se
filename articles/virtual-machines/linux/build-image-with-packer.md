---
title: Så här skapar du Linux Azure VM-avbildningar med Packer | Microsoft Docs
description: Lär dig hur du använder Packer för att skapa avbildningar av virtuella Linux-datorer i Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/07/2019
ms.author: cynthn
ms.openlocfilehash: c0ec2616d8bdcf3cfd6d649f12e9bfceea33690a
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467750"
---
# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Hur du använder Packer för att skapa Linux-avbildningar i Azure
Varje virtuell dator (VM) i Azure skapas från en avbildning som definierar Linux-distribution och operativsystemsversion. Bilder kan innehålla förinstallerade program och konfigurationer. Azure Marketplace innehåller många avbildningar av första och tredje part för de vanligaste distributioner och programmiljöer eller du kan skapa dina egna anpassade avbildningar som är specialanpassade utifrån dina behov. Den här artikeln beskriver hur du använder verktyg med öppen källkod [Packer](https://www.packer.io/) att definiera och skapa anpassade avbildningar i Azure.

> [!NOTE]
> Azure har nu en tjänst, Azure Image Builder (förhandsversion) för att definiera och skapa dina egna anpassade avbildningar. Azure Image Builder är byggt på Packer, så du kan även använda din befintliga Packer provisioner kommandoskript med den. Kom igång med Azure Image Builder, se [skapa en Linux-VM med Azure Image Builder](image-builder.md).


## <a name="create-azure-resource-group"></a>Skapa Azure-resursgrupp
När du skapar skapar Packer tillfällig Azure-resurser som den bygger den Virtuella källdatorn. För att avbilda den Virtuella källdatorn för användning som en avbildning måste du definiera en resursgrupp. Utdata från skapandeprocessen Packer lagras i den här resursgruppen.

Skapa en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli
az group create -n myResourceGroup -l eastus
```


## <a name="create-azure-credentials"></a>Skapa Azure-autentiseringsuppgifter
Packer autentiserar med Azure med ett huvudnamn för tjänsten. Ett huvudnamn för Azure-tjänsten är en säkerhetsidentitet som du kan använda med appar, tjänster och automatiseringsverktyg som Packer. Du kontrollerar och definiera behörigheter om vilka åtgärder som tjänstens huvudnamn kan utföra i Azure.

Skapa ett tjänstobjekt med [az ad sp create-for-rbac](/cli/azure/ad/sp) och matar ut de autentiseringsuppgifter som Packer behöver:

```azurecli
az ad sp create-for-rbac --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"
```

Ett exempel på utdata från föregående kommandon är följande:

```azurecli
{
    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Om du vill autentisera till Azure måste du också behöva hämta ditt Azure-prenumeration-ID med [az konto show](/cli/azure/account):

```azurecli
az account show --query "{ subscription_id: id }"
```

Du kan använda utdata från dessa två kommandon i nästa steg.


## <a name="define-packer-template"></a>Definiera Packer-mall
Avbildningar skapa måste du en mall som en JSON-fil. I mallen definierar du builders och provisioners som utför den faktiska skapandeprocessen. Packer har en [provisioner för Azure](https://www.packer.io/docs/builders/azure.html) som gör det möjligt att definiera Azure-resurser, t.ex. autentiseringsuppgifterna för tjänstobjektet skapade i föregående steg.

Skapa en fil med namnet *ubuntu.json* och klistra in följande innehåll. Ange dina egna värden för följande:

| Parameter                           | Var de kan hämtas |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Första raden i utdata från `az ad sp` skapa command - *appId* |
| *client_secret*                     | Andra raden på utdata från `az ad sp` skapa command - *lösenord* |
| *tenant_id*                         | Tredje raden på utdata från `az ad sp` skapa command - *klient* |
| *subscription_id*                   | Utdata från `az account show` kommando |
| *managed_image_resource_group_name* | Namnet på resursgruppen som du skapade i det första steget |
| *managed_image_name*                | Namn för den hantera diskavbildningen som har skapats |


```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",

    "managed_image_resource_group_name": "myResourceGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Linux",
    "image_publisher": "Canonical",
    "image_offer": "UbuntuServer",
    "image_sku": "16.04-LTS",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "execute_command": "chmod +x {{ .Path }}; {{ .Vars }} sudo -E sh '{{ .Path }}'",
    "inline": [
      "apt-get update",
      "apt-get upgrade -y",
      "apt-get -y install nginx",

      "/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync"
    ],
    "inline_shebang": "/bin/sh -x",
    "type": "shell"
  }]
}
```

Den här mallen skapar en Ubuntu 16.04 LTS-avbildning, installerar NGINX och sedan deprovisions den virtuella datorn.

> [!NOTE]
> Om du expanderar på den här mallen för att etablera användarens autentiseringsuppgifter, justerar du kommandot provisioner som deprovisions Azure-agenten för att läsa `-deprovision` snarare än `deprovision+user`.
> Den `+user` flaggan tar bort alla användarkonton från den Virtuella källdatorn.


## <a name="build-packer-image"></a>Skapa Packer-avbildning
Om du inte redan har Packer som installerats på den lokala datorn [Följ installationsanvisningarna Packer](https://www.packer.io/docs/install/index.html).

Skapa avbildningen genom att ange din Packer mallfilen på följande sätt:

```bash
./packer build ubuntu.json
```

Ett exempel på utdata från föregående kommandon är följande:

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> dept : Engineering
==> azure-arm:  ->> task : Image deployment
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> DeploymentName    : ‘pkrdpswtxmqm7ly’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> DeploymentName    : ‘pkrdpswtxmqm7ly’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.218.147’
==> azure-arm: Waiting for SSH to become available...
==> azure-arm: Connected to SSH!
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-shell868574263
    azure-arm: WARNING! The waagent service will be stopped.
    azure-arm: WARNING! Cached DHCP leases will be deleted.
    azure-arm: WARNING! root password will be disabled. You will not be able to login as root.
    azure-arm: WARNING! /etc/resolvconf/resolv.conf.d/tail and /etc/resolvconf/resolv.conf.d/original will be deleted.
    azure-arm: WARNING! packer account and entire home directory will be deleted.
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> ComputeName       : ‘pkrvmswtxmqm7ly’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-swtxmqm7ly/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> ComputeName       : ‘pkrvmswtxmqm7ly’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> Compute Name              : ‘pkrvmswtxmqm7ly’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

Det tar några minuter för Packer att skapa den virtuella datorn, kör provisioners och rensa distributionen.


## <a name="create-vm-from-azure-image"></a>Skapa virtuell dator från Azure-avbildning
Du kan nu skapa en virtuell dator från avbildningen med [az vm skapa](/cli/azure/vm). Ange den avbildning som du skapade med den `--image` parametern. I följande exempel skapas en virtuell dator med namnet *myVM* från *myPackerImage* och genererar SSH-nycklar om de inte redan finns:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myPackerImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Om du vill skapa virtuella datorer i en annan resursgrupp eller region än din Packer-avbildning, ange avbildnings-ID i stället för namnet på avbildningen. Du kan hämta avbildnings-ID med [az image show](/cli/azure/image#az-image-show).

Det tar några minuter att skapa den virtuella datorn. När du har skapat den virtuella datorn, ta del av den `publicIpAddress` visas av Azure CLI. Den här adressen används för att komma åt NGINX-webbplats via en webbläsare.

För att låta webbtrafik nå din virtuella dator öppnar du port 80 från Internet med [az vm open-port](/cli/azure/vm):

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>Testa virtuell dator och NGINX
Nu kan du öppna en webbläsare och ange `http://publicIpAddress` i adressfältet. Ange din offentliga IP-adress från skapandeprocessen av den virtuella datorn. NGINX-standardsida visas som i följande exempel:

![NGINX-standardwebbplats](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>Nästa steg
Du kan också använda befintliga Packer provisioner skript med [Azure Image Builder](image-builder.md).
