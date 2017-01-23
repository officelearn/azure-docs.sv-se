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
ms.date: 01/03/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 44c46fff9ccf9c7dba9ee380faf5f8213b58e3c3
ms.openlocfilehash: 4397d84ef4d97bdee387777a193ec0b969f2d5e1


---
# <a name="different-ways-to-create-a-linux-vm-including-azure-cli-20-preview"></a>Olika sätt att skapa en virtuell Linux-dator inklusive Azure CLI 2.0 (förhandsvisning)
I Azure har du tillräcklig flexibilitet för att kunna skapa en virtuell Linux-dator (VM) med hjälp av de verktyg och arbetsflöden som du känner dig mest bekväm med. I den här artikeln sammanfattas skillnader och exempel när du ska skapa dina virtuella Linux-datorer.

## <a name="azure-cli"></a>Azure CLI

Du kan slutföra uppgiften med någon av följande CLI-versioner:

- Azure CLI 1.0 – vår CLI för distributionsmodellerna klassisk och Resource Management
- [Azure CLI 2.0 (förhandsversion)](../xplat-cli-install.md) –vår nästa generations CLI för distributionsmodellen resurshantering

Azure CLI 2.0 (förhandsvisning) finns tillgänglig på plattformar via ett npm-paket, via distro-paket eller som Docker-behållare. Kontrollera att du är inloggad med **az-inloggning**.

Följande självstudier innehåller exempel på hur du använder Azure CLI 2.0 (förhandsvisning). Läs alla artiklarna för mer information om de kommandon som visas:

* [Skapa en virtuell Linux-dator med Azure CLI 2.0 (förhandsvisning)](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Det här exemplet skapar en resursgrupp som heter myResourceGroup: 
    
    ```azurecli
    az group create -n myResourceGroup -l westus
    ```

  * Det här exemplet skapar en virtuell dator i den nya resursgruppen som använder sig av den senaste Debian-avbildningen med en offentlig nyckel med namnet `id_rsa.pub`:

    ```azurecli
    az vm create \
    --image credativ:Debian:8:latest \
    --admin-username ops \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --public-ip-address-dns-name mydns \
    --resource-group myResourceGroup \
    --location westus \
    --name myVM
    ```

* [Skapa en säker virtuell Linux-dator med hjälp av en Azure-mall](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Följande exempel skapar en VM med en mall som lagrats i GitHub:
    
    ```azurecli
    az group deployment create -g myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
    
* [Skapa en fullständig Linux-miljö med Azure CLI](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Innefattar att skapa en belastningsutjämnare och flera virtuella datorer i en tillgänglighetsuppsättning.

* [Lägg till en disk till en virtuell Linux-dator](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * I följande exempel läggs en disk på 5 GB till på en befintlig virtuell dator med namnet `myVM`:
    
    ```azurecli
    az vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
      --disk-size 5 --vhd https://myStorage.blob.core.windows.net/vhds/myDataDisk1.vhd
    ```

## <a name="azure-portal"></a>Azure Portal
I [Azure Portal](https://portal.azure.com) kan du snabbt skapa en virtuell dator eftersom det inte finns något att installera i ditt system. Använd Azure Portal när du skapar den virtuella datorn:

* [Skapa en virtuell Linux-dator med hjälp av Azure Portal](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 
* [Anslut en disk med hjälp av Azure Portal](virtual-machines-linux-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="operating-system-and-image-choices"></a>Alternativ för operativsystem och avbildning
När du skapar en virtuell dator, väljer du en avbildning baserat på vilket operativsystem du vill köra. Azure och dess samarbetspartner erbjuder många avbildningar, varav några innehåller förinstallerade program och verktyg. Eller ladda upp en av dina egna avbildningar (se [följande avsnitt](#use-your-own-image)).

### <a name="azure-images"></a>Azure-avbildningar
Använd `az vm image` CLI-kommandon för att se vad som finns tillgängligt via utgivare, distributionsutgåva och version.

Lista tillgängliga utgivare:

```azurecli
az vm image list-publishers -l WestUS
```

Lista tillgängliga produkter (erbjudanden) för en viss utgivare:

```azurecli
az vm image list-offers --publisher-name Canonical -l WestUS
```

Lista tillgängliga SKU:er (distributionsutgåvor) för ett givet erbjudande:

```azurecli
az vm image list-skus --publisher-name Canonical --offer UbuntuServer -l WestUS
```

Lista alla tillgängliga bilder för en viss version:

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS -l WestUS
```

Se [Analysera och välja avbildningar för virtuell Azure-datorer med Azure CLI](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) för fler exempel på hur du hittar och använder tillgängliga avbildningar.

`az vm create`-kommandot har alias som du kan använda för att snabbt komma åt vanliga distributioner och deras senaste versioner. Det går snabbare att använda alias än ange utgivare, erbjudande, SKU och version varje gång du skapar en virtuell dator:

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

* [Azure-godkända distributioner](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Information om icke-godkända distributioner](virtual-machines-linux-create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Avbilda en virtuell Linux-dator som en Resource Manager-mall](virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
  
  * Snabbstart med exempelkommandon för att avbilda en befintlig virtuell dator:
    
    ```azurecli
    az vm deallocate -g myResourceGroup -n myVM
    az vm generalize -g myResourceGroup -n myVM
    az vm capture -g myResourceGroup -n myVM --vhd-name-prefix myCapturedVM
    ```

## <a name="next-steps"></a>Nästa steg
* Skapa en virtuell Linux-dator från [portalen](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), med [CLI](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller genom att använda en [Azure Resource Manager-mall](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* När du har skapat en virtuell Linux-dator kan du [lägga till en datadisk](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Snabba steg för att [återställa ett lösenord eller SSH-nycklar och hantera användare](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)




<!--HONumber=Jan17_HO1-->


