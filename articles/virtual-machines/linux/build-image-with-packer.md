---
title: Skapa Linux Azure VM-avbildningar med Packer
description: Lär dig hur du använder Packer för att skapa avbildningar av virtuella Linux-datorer i Azure
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure
ms.date: 05/07/2019
ms.author: cynthn
ms.openlocfilehash: 3aec50b8c8f2033b7340bde15ea7670c1a0b6bb9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534227"
---
# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Så här använder du Packer för att skapa Linux-avbildningar för virtuella datorer i Azure
Varje virtuell dator (VM) i Azure skapas från en avbildning som definierar Linux-distribution och OS-versionen. Avbildningar kan innehålla förinstallerade program och konfigurationer. Azure Marketplace innehåller många avbildningar från första och tredje part för de vanligaste distributionerna och programmiljöerna, eller så kan du skapa egna anpassade avbildningar som är anpassade efter dina behov. I den här artikeln beskrivs hur du använder verktyget för öppen källkod [Packer](https://www.packer.io/) för att definiera och skapa anpassade avbildningar i Azure.

> [!NOTE]
> Azure har nu en tjänst, Azure Image Builder (förhandsversion), för att definiera och skapa egna anpassade avbildningar. Azure Image Builder bygger på Packer, så du kan även använda dina befintliga Packer shell provisioner skript med den. Information om hur du kommer igång med Azure Image Builder finns i [Skapa en virtuell Linux-dator med Azure Image Builder](image-builder.md).


## <a name="create-azure-resource-group"></a>Skapa Azure-resursgrupp
Under byggprocessen skapar Packer tillfälliga Azure-resurser när den bygger källdatorn. Om du vill fånga den virtuella källdatorn för att användas som avbildning måste du definiera en resursgrupp. Utdata från Packer-byggprocessen lagras i den här resursgruppen.

Skapa en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på *eastus-platsen:*

```azurecli
az group create -n myResourceGroup -l eastus
```


## <a name="create-azure-credentials"></a>Skapa Azure-autentiseringsuppgifter
Packer autentiserar med Azure med hjälp av ett tjänsthuvudnamn. Ett Azure-tjänsthuvudnamn är en säkerhetsidentitet som du kan använda med appar, tjänster och automatiseringsverktyg som Packer. Du styr och definierar behörigheterna för vilka åtgärder tjänstens huvudnamn kan utföra i Azure.

Skapa ett tjänsthuvudnamn med [az ad sp create-for-rbac](/cli/azure/ad/sp) och mata ut de autentiseringsuppgifter som Packer behöver:

```azurecli
az ad sp create-for-rbac --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"
```

Ett exempel på utdata från föregående kommandon är följande:

```output
{
    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

För att autentisera till Azure måste du också skaffa ditt Azure-prenumerations-ID med [az-kontoprogram:](/cli/azure/account)

```azurecli
az account show --query "{ subscription_id: id }"
```

Du använder utdata från dessa två kommandon i nästa steg.


## <a name="define-packer-template"></a>Definiera packermall
Om du vill skapa bilder skapar du en mall som en JSON-fil. I mallen definierar du byggare och etablerare som utför den faktiska byggprocessen. Packer har en [provisioner för Azure](https://www.packer.io/docs/builders/azure.html) som låter dig definiera Azure-resurser, till exempel tjänstens huvudreferenser som skapats i föregående steg.

Skapa en fil med namnet *ubuntu.json* och klistra in följande innehåll. Ange dina egna värden för följande:

| Parameter                           | Om att få |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Första raden av `az ad sp` utdata från skapa kommando - *appId* |
| *client_secret*                     | Andra raden av `az ad sp` utdata från skapa kommando - *lösenord* |
| *tenant_id*                         | Tredje raden av `az ad sp` utdata från skapa kommando - *klient* |
| *subscription_id*                   | Utdata `az account show` från kommandot |
| *managed_image_resource_group_name* | Namn på resursgrupp som du skapade i det första steget |
| *managed_image_name*                | Namn på den hanterade diskavbildningen som skapas |


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

Den här mallen skapar en Ubuntu 16.04 LTS-avbildning, installerar NGINX och avetablerar sedan den virtuella datorn.

> [!NOTE]
> Om du expanderar på den här mallen för att etablera användarautentiseringsuppgifter justerar du etableringskommandot som avetablerar Azure-agenten för att läsa `-deprovision` i stället `deprovision+user`för .
> Flaggan `+user` tar bort alla användarkonton från källdatorn.


## <a name="build-packer-image"></a>Skapa Packer-avbildning
Om Packer inte redan har installerat Packer på den lokala datorn [följer du installationsinstruktionerna för Packer](https://www.packer.io/docs/install/index.html).

Skapa avbildningen genom att ange mallfilen Packer på följande sätt:

```bash
./packer build ubuntu.json
```

Ett exempel på utdata från föregående kommandon är följande:

```output
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

Det tar några minuter för Packer att skapa den virtuella datorn, köra etableringselementen och rensa distributionen.


## <a name="create-vm-from-azure-image"></a>Skapa virtuell dator från Azure Image
Du kan nu skapa en virtuell dator från din avbildning med [az vm skapa](/cli/azure/vm). Ange den bild som `--image` du skapade med parametern. I följande exempel skapas en virtuell dator med namnet *myVM* från *myPackerImage* och SSH-nycklar genereras om de inte redan finns:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myPackerImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Om du vill skapa virtuella datorer i en annan resursgrupp eller region än Packer-avbildningen anger du bild-ID:t i stället för bildnamnet. Du kan hämta bild-ID med [az bild show](/cli/azure/image#az-image-show).

Det tar några minuter att skapa den virtuella datorn. När den virtuella datorn har skapats bör du notera den `publicIpAddress` som visas av Azure CLI. Den här adressen används för att komma åt NGINX-webbplatsen via en webbläsare.

För att låta webbtrafik nå din virtuella dator öppnar du port 80 från Internet med [az vm open-port](/cli/azure/vm):

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>Testa VM och NGINX
Nu kan du öppna en webbläsare och ange `http://publicIpAddress` i adressfältet. Ange din offentliga IP-adress från skapandeprocessen av den virtuella datorn. Standardsidan för NGINX visas som i följande exempel:

![NGINX-standardwebbplats](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>Nästa steg
Du kan också använda befintliga Packer-etableringsskript med [Azure Image Builder](image-builder.md).
