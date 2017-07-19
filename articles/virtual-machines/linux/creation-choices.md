---
title: "Olika sätt att skapa en virtuell Linux-dator i Azure | Microsoft Azure"
description: "Lär dig mer om vilka alternativ som du kan välja mellan när du skapar en virtuell Linux-dator i Azure, inklusive länkar till verktyg och självstudier för varje metod."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f38f8a44-6c88-4490-a84a-46388212d24c
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: b2f93579eb1c7a69d0dbd1b0ef112aed9b2168c3
ms.contentlocale: sv-se
ms.lasthandoff: 05/31/2017


---
# <a name="different-ways-to-create-a-linux-vm"></a>Olika sätt att skapa en virtuell Linux-dator
I Azure har du tillräcklig flexibilitet för att kunna skapa en virtuell Linux-dator (VM) med hjälp av de verktyg och arbetsflöden som du känner dig mest bekväm med. I den här artikeln sammanfattas skillnader och exempel när du ska skapa dina virtuella Linux-datorer, inklusive Azure CLI 2.0. Du kan också visa andra alternativ för skapandet, som [Azure CLI 1.0](creation-choices-nodejs.md).

[Azure CLI 2.0](/cli/azure/install-az-cli2) är tillgängligt på flera plattformar via ett npm-paket, distro-paket eller en Docker-behållare. Installera den lämpligaste versionen för din miljö och logga in på ett Azure-konto med hjälp av [az login](/cli/azure/#login)

* [Skapa en virtuell Linux-dator med hjälp av Azure CLI 2.0](quick-create-cli.md)
  
  * Skapa en resursgrupp med [az group create](/cli/azure/group#create) med namnet *myResourceGroup*: 
   
    ```azurecli
    az group create --name myResourceGroup --location eastus
    ```
    
  * Skapa en virtuell dator med [az vm create](/cli/azure/vm#create) som du ger namnet *myVM* med den senaste *UbuntuLTS*-bilden och skapa SSH-nycklar om dessa inte redan finns i *~/.ssh*:

    ```azurecli
    az vm create \
        --resource-group myResourceGroup \
        --name myVM \
        --image UbuntuLTS \
        --generate-ssh-keys
    ```

* [Skapa en virtuell Linux-dator med hjälp av en Azure-mall](create-ssh-secured-vm-from-template.md)
  
  * I följande exempel används [az group deployment create](/cli/azure/group/deployment#create) för att skapa en virtuell dator med hjälp av en mall som lagras på GitHub:
    
    ```azurecli
    az group deployment create --resource-group myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
* [Skapa en virtuell Linux-dator och anpassa med cloud-init](tutorial-automate-vm-deployment.md)

* [Skapa en belastningsutjämnat och mycket tillgängligt program på flera virtuella Linux-datorer](tutorial-load-balancer.md)


## <a name="azure-portal"></a>Azure Portal
I [Azure Portal](https://portal.azure.com) kan du snabbt skapa en virtuell dator eftersom det inte finns något att installera i ditt system. Använd Azure Portal när du skapar den virtuella datorn:

* [Skapa en virtuell Linux-dator med hjälp av Azure Portal](quick-create-portal.md) 


## <a name="operating-system-and-image-choices"></a>Alternativ för operativsystem och avbildning
När du skapar en virtuell dator, väljer du en avbildning baserat på vilket operativsystem du vill köra. Azure och dess samarbetspartner erbjuder många avbildningar, varav några innehåller förinstallerade program och verktyg. Eller ladda upp en av dina egna avbildningar (se [följande avsnitt](#use-your-own-image)).

### <a name="azure-images"></a>Azure-avbildningar
Använd [az vm image](/cli/azure/vm/image)-kommandona för att se vad som är tillgängligt efter utgivare, distributionsutgåva och version.

Lista tillgängliga utgivare:

```azurecli
az vm image list-publishers --location eastus
```

Lista tillgängliga produkter (erbjudanden) för en viss utgivare:

```azurecli
az vm image list-offers --publisher Canonical --location eastus
```

Lista tillgängliga SKU:er (distributionsutgåvor) för ett givet erbjudande:

```azurecli
az vm image list-skus --publisher Canonical --offer UbuntuServer --location eastus
```

Lista alla tillgängliga bilder för en viss version:

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS --location eastus
```

Se [Analysera och välja avbildningar för virtuell Azure-datorer med Azure CLI](cli-ps-findimage.md) för fler exempel på hur du hittar och använder tillgängliga avbildningar.

[az vm create](/cli/azure/vm#create)-kommandot har alias som du kan använda för att snabbt komma åt vanliga distributioner och deras senaste versioner. Det går snabbare att använda alias än ange utgivare, erbjudande, SKU och version varje gång du skapar en virtuell dator:

| Alias | Utgivare | Erbjudande | SKU | Version |
|:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |Centos |7.2 |senaste |
| CoreOS |CoreOS |CoreOS |Stable |senaste |
| Debian |credativ |Debian |8 |senaste |
| openSUSE |SUSE |openSUSE |13.2 |senaste |
| RHEL |Redhat |RHEL |7.2 |senaste |
| SLES |SLES |SLES |12-SP1 |senaste |
| UbuntuLTS |Canonical |UbuntuServer |14.04.4-LTS |senaste |

### <a name="use-your-own-image"></a>Använda en egen avbildning
Om du behöver specifika anpassningar kan du använda en avbildning baserad på en befintlig virtuell Azure-dator genom att avbilda den virtuella datorn. Du kan också ladda upp en avbildning som skapats lokalt. Mer information om distributioner som stöds och hur du använder egna avbildningar finns i följande artiklar:

* [Azure-godkända distributioner](endorsed-distros.md)
* [Information om icke-godkända distributioner](create-upload-generic.md)
* [Så här skapar du en avbildning från en befintliga virtuell Azure-dator](tutorial-custom-images.md).
  
  * Snabbstart med exempel på kommandon för att skapa en avbildning från en befintlig Azure VM:
    
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm generalize --resource-group myResourceGroup --name myVM
    az vm image create --resource-group myResourceGroup --source myVM --name myImage
    ```

## <a name="next-steps"></a>Nästa steg
* Skapa en virtuell Linux-dator från [CLI](quick-create-cli.md), från [portalen](quick-create-portal.md) eller genom att använda en [Azure Resource Manager-mall](../windows/cli-deploy-templates.md).
* När du har skapat en Linux VM ska du [lära dig mer om Azure-diskar och lagring](tutorial-manage-disks.md).
* Snabba steg för att [återställa ett lösenord eller SSH-nycklar och hantera användare](using-vmaccess-extension.md).

