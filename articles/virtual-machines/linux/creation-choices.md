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
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: d7ff1317cdf6ccfe6b4e5035878fc4e096fcc0f9
ms.lasthandoff: 04/03/2017


---
# <a name="different-ways-to-create-a-linux-vm"></a>Olika sätt att skapa en virtuell Linux-dator
I Azure har du tillräcklig flexibilitet för att kunna skapa en virtuell Linux-dator (VM) med hjälp av de verktyg och arbetsflöden som du känner dig mest bekväm med. I den här artikeln sammanfattas skillnader och exempel när du ska skapa dina virtuella Linux-datorer, inklusive Azure CLI 2.0. Du kan också visa andra alternativ för skapandet, som [Azure CLI 1.0](creation-choices-nodejs.md).

[Azure CLI 2.0](/cli/azure/install-az-cli2) är tillgängligt på flera plattformar via ett npm-paket, distro-paket eller en Docker-behållare. Installera den lämpligaste versionen för din miljö och logga in på ett Azure-konto med hjälp av [az login](/cli/azure/#login)

I följande exempel används Azure CLI 2.0. Läs alla artiklarna för mer information om de kommandon som visas. Du hittar också exempel på hur du skapar virtuella Linux-datorer med hjälp av [Azure CLI 1.0](creation-choices-nodejs.md).

* [Skapa en virtuell Linux-dator med hjälp av Azure CLI 2.0](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * I det här exemplet används [az group create](/cli/azure/group#create) för att skapa en resursgrupp med namnet `myResourceGroup`: 
-    
    ```azurecli
    az group create --name myResourceGroup --location westus
    ```
    
  * I det här exemplet används [az vm create](/cli/azure/vm#create) för att skapa en virtuell dator med namnet `myVM` med hjälp av den senaste Debian-avbildningen med Azure Managed Disks och en offentlig nyckel med namnet `id_rsa.pub`:

    ```azurecli
    az vm create \
    --image credativ:Debian:8:latest \
     --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
az vm disk attach –g myResourceGroup –-vm-name myVM –-disk myDataDisk  –-new --size-gb 5    --public-ip-address-dns-name myPublicDNS \
    --resource-group myResourceGroup \
    --location westus \
    --name myVM
    ```

    * Om du vill använda ohanterade diskar lägger du till `--use-unmanaged-disks`-flaggan till kommandot ovan. Ett lagringskonto skapas åt dig. Mer information finns i [Översikt över Azure Managed Disks](../../storage/storage-managed-disks-overview.md).

* [Skapa en säker virtuell Linux-dator med hjälp av en Azure-mall](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * I följande exempel används [az group deployment create](/cli/azure/group/deployment#create) för att skapa en virtuell dator med hjälp av en mall som lagras på GitHub:
    
    ```azurecli
    az group deployment create --resource-group myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
    
* [Skapa en fullständig Linux-miljö med Azure CLI](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Innefattar att skapa en belastningsutjämnare och flera virtuella datorer i en tillgänglighetsuppsättning.

* [Lägg till en disk till en virtuell Linux-dator](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * I följande exempel används [az vm disk attach-new](/cli/azure/vm/disk#attach-new) till att lägga till en hanterad disk på 50 GB med namnet `myVM`:
  
    ```azurecli
    az vm disk attach –g myResourceGroup –-vm-name myVM –-disk myDataDisk  \
    –-new --size-gb 50
    ```

## <a name="azure-portal"></a>Azure Portal
I [Azure Portal](https://portal.azure.com) kan du snabbt skapa en virtuell dator eftersom det inte finns något att installera i ditt system. Använd Azure Portal när du skapar den virtuella datorn:

* [Skapa en virtuell Linux-dator med hjälp av Azure Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 
* [Anslut en disk med hjälp av Azure Portal](../windows/attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="operating-system-and-image-choices"></a>Alternativ för operativsystem och avbildning
När du skapar en virtuell dator, väljer du en avbildning baserat på vilket operativsystem du vill köra. Azure och dess samarbetspartner erbjuder många avbildningar, varav några innehåller förinstallerade program och verktyg. Eller ladda upp en av dina egna avbildningar (se [följande avsnitt](#use-your-own-image)).

### <a name="azure-images"></a>Azure-avbildningar
Använd [az vm image](/cli/azure/vm/image)-kommandona för att se vad som är tillgängligt efter utgivare, distributionsutgåva och version.

Lista tillgängliga utgivare:

```azurecli
az vm image list-publishers --location WestUS
```

Lista tillgängliga produkter (erbjudanden) för en viss utgivare:

```azurecli
az vm image list-offers --publisher Canonical --location WestUS
```

Lista tillgängliga SKU:er (distributionsutgåvor) för ett givet erbjudande:

```azurecli
az vm image list-skus --publisher Canonical --offer UbuntuServer --location WestUS
```

Lista alla tillgängliga bilder för en viss version:

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS --location WestUS
```

Se [Analysera och välja avbildningar för virtuell Azure-datorer med Azure CLI](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) för fler exempel på hur du hittar och använder tillgängliga avbildningar.

**az vm create**-kommandot har alias som du kan använda för att snabbt komma åt vanliga distributioner och deras senaste versioner. Det går snabbare att använda alias än ange utgivare, erbjudande, SKU och version varje gång du skapar en virtuell dator:

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
Om du behöver specifika anpassningar kan du använda en avbildning baserad på en befintlig virtuell Azure-dator genom att *avbilda* den virtuella datorn. Du kan också ladda upp en avbildning som skapats lokalt. Mer information om distributioner som stöds och hur du använder egna avbildningar finns i följande artiklar:

* [Azure-godkända distributioner](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Information om icke-godkända distributioner](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Avbilda en virtuell Linux-dator som en Resource Manager-mall](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
  
  * Snabbstart med **az vm**-exempelkommandon för att avbilda en befintlig virtuell dator med ohanterade diskar:
    
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm generalize --resource-group myResourceGroup --name myVM
    az vm capture --resource-group myResourceGroup --name myVM --vhd-name-prefix myCapturedVM
    ```

## <a name="next-steps"></a>Nästa steg
* Skapa en virtuell Linux-dator från [portalen](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), med [CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller genom att använda en [Azure Resource Manager-mall](../windows/cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* När du har skapat en virtuell Linux-dator kan du [lägga till en datadisk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Snabba steg för att [återställa ett lösenord eller SSH-nycklar och hantera användare](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

