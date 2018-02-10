---
title: "Hur du skapar Linux Azure VM-avbildningar med förpackaren | Microsoft Docs"
description: "Lär dig mer om förpackaren skapa avbildningar av virtuella Linux-datorer i Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/13/2017
ms.author: iainfou
ms.openlocfilehash: 49a3e7f3aab3ae95c6f40b167880bb48d0fc851b
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Hur du använder förpackaren för att skapa avbildningar av Linux virtuella datorer i Azure
Varje virtuell dator (VM) i Azure skapas från en avbildning som definierar distribution för Linux och OS-version. Avbildningar kan innehålla förinstallerade program och konfigurationer. Azure Marketplace innehåller många första och tredje parts avbildningar för programmet miljöer och de vanligaste distributioner eller skapa egna anpassade avbildningar som är anpassade efter era behov. Den här artikeln beskriver hur du använder verktyget öppen källkod [förpackaren](https://www.packer.io/) att definiera och skapa anpassade avbildningar i Azure.


## <a name="create-azure-resource-group"></a>Skapa Azure-resursgrupp
När du skapar skapar förpackaren tillfälliga Azure-resurser som den skapar den Virtuella källdatorn. För att avbilda den Virtuella källdatorn som ska användas som en avbildning måste du definiera en resursgrupp. Utdata från processen för att bygga förpackaren lagras i den här resursgruppen.

Skapa en resursgrupp med [az group create](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli
az group create -n myResourceGroup -l eastus
```


## <a name="create-azure-credentials"></a>Skapa autentiseringsuppgifter för Azure
Packare autentiserar med Azure med hjälp av ett huvudnamn för tjänsten. En Azure-tjänstens huvudnamn är en säkerhetsidentitet som du kan använda med appar, tjänster och verktyg för automatisering som förpackaren. Du styr och definiera behörigheter för vilka åtgärder som tjänstens huvudnamn kan utföra i Azure.

Skapa en tjänstens huvudnamn med [az ad sp skapa-för-rbac](/cli/azure/ad/sp#create-for-rbac) och utdata autentiseringsuppgifterna som förpackaren måste:

```azurecli
az ad sp create-for-rbac --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"
```

Ett exempel på utdata från föregående kommandona är följande:

```azurecli
{
    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Om du vill autentisera till Azure måste du också behöva hämta ditt Azure prenumerations-ID med [az konto visa](/cli/azure/account#az_account_show):

```azurecli
az account show --query "{ subscription_id: id }"
```

Du kan använda utdata från dessa två kommandon i nästa steg.


## <a name="define-packer-template"></a>Definiera förpackaren mall
För att skapa avbildningar, skapa en mall som en JSON-fil. I mallen definierar du builders och provisioners som utför faktiska skapar. Förpackaren har en [provisioner för Azure](https://www.packer.io/docs/builders/azure.html) som kan du definiera Azure-resurser, t.ex. de huvudsakliga Tjänstereferenser som skapade i föregående steg.

Skapa en fil med namnet *ubuntu.json* och klistra in följande innehåll. Ange egna värden för följande:

| Parameter                           | Var kan hämtas |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Första raden i utdata från `az ad sp` skapa kommando - *appId* |
| *client_secret*                     | Andra raden på utdata från `az ad sp` skapa kommando - *lösenord* |
| *tenant_id*                         | Tredje raden på utdata från `az ad sp` skapa kommando - *klient* |
| *subscription_id*                   | Utdata från `az account show` kommando |
| *managed_image_resource_group_name* | Namnet på resursgruppen som du skapade i det första steget |
| *managed_image_name*                | Namn för den hanterade diskavbildning som har skapats |


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

Den här mallen bygger en avbildning Ubuntu 16.04 LTS, installerar NGINX och deprovisions den virtuella datorn.

> [!NOTE]
> Om du expanderar på den här mallen för att etablera användarens autentiseringsuppgifter, justera kommandot provisioner som deprovisions Azure-agenten att läsa `-deprovision` snarare än `deprovision+user`.
> Den `+user` flaggan tar bort alla användarkonton från den Virtuella källdatorn.


## <a name="build-packer-image"></a>Skapa förpackaren bild
Om du inte redan har förpackaren installerad på din lokala dator [Följ installationsanvisningarna förpackaren](https://www.packer.io/docs/install/index.html).

Skapa avbildningen genom att ange din förpackaren mallfilen på följande sätt:

```bash
./packer build ubuntu.json
```

Ett exempel på utdata från föregående kommandona är följande:

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

Det tar några minuter för förpackaren att bygga den virtuella datorn körs provisioners och rensa distributionen.


## <a name="create-vm-from-azure-image"></a>Skapa virtuell dator från Azure-avbildning
Nu kan du skapa en virtuell dator från avbildningen med [az vm skapa](/cli/azure/vm#az_vm_create). Ange den avbildning som du skapade med den `--image` parameter. I följande exempel skapas en virtuell dator med namnet *myVM* från *myPackerImage* och genererar SSH-nycklar, om de inte redan finns:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myPackerImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn. När den virtuella datorn har skapats, notera den `publicIpAddress` visas av Azure CLI. Den här adressen används för åtkomst till NGINX-webbplatsen via en webbläsare.

Tillåt Internet-trafik till den virtuella datorn genom att öppna port 80 från Internet med [az vm öppna port](/cli/azure/vm#open-port):

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>Testa virtuell dator och NGINX
Nu kan du öppna en webbläsare och ange `http://publicIpAddress` i adressfältet. Ange din offentliga IP-adress från skapandeprocessen av den virtuella datorn. Sidan NGINX visas som i följande exempel:

![NGINX-standardwebbplats](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>Nästa steg
I det här exemplet används förpackaren för att skapa en VM-avbildning med NGINX som redan har installerats. Du kan använda den här Virtuella datorn tillsammans med befintliga arbetsflöden för distribution, som att distribuera din app till virtuella datorer skapas från avbildningen med Ansible, Chef eller Puppet.

Ytterligare exempel förpackaren mallar för andra Linux-distributioner, se [GitHub-repo](https://github.com/hashicorp/packer/tree/master/examples/azure).