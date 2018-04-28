---
title: Skapa en Linux-VM i Azure med flera nätverkskort | Microsoft Docs
description: Lär dig hur du skapar en Linux VM med flera nätverkskort som är kopplade till den med hjälp av Azure CLI eller Resource Manager-mallar.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: 5a4e19f232abce8701e6d2e12a876c55079b91a2
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="create-a-linux-virtual-machine-with-multiple-nics-using-the-azure-cli-10"></a>Skapa en virtuell Linux-dator med flera nätverkskort som använder Azure CLI 1.0
Du kan skapa en virtuell dator (VM) i Azure som har flera virtuella nätverksgränssnitt (NIC) ansluten till den. Ett vanligt scenario är att ha olika undernät för frontend och backend-anslutning eller ett nätverk som är dedikerad för en lösning för övervakning eller säkerhetskopiering. Den här artikeln innehåller snabb kommandon för att skapa en virtuell dator med flera nätverkskort som är kopplade till den. Olika [VM-storlekar](sizes.md) stöder olika antal nätverkskort, så därför storlek den virtuella datorn.

> [!WARNING]
> När du skapar en virtuell dator – du kan inte lägga till nätverkskort i en befintlig virtuell dator med Azure CLI 1.0 måste du koppla flera nätverkskort. Du kan [lägga till nätverkskort i en befintlig virtuell dator med Azure CLI 2.0](multiple-nics.md). Du kan också [skapa en virtuell dator baserat på de ursprungliga virtuella diskarna](copy-vm.md) och skapa flera nätverkskort som du distribuerar den virtuella datorn.


## <a name="cli-versions-to-complete-the-task"></a>CLI-versioner för att slutföra uppgiften
Du kan slutföra uppgiften med någon av följande CLI-versioner:

- [Azure CLI 1.0](#create-supporting-resources) – våra CLI för klassisk och resurs management på distributionsmodeller (den här artikeln)
- [Azure CLI 2.0](multiple-nics.md) – vår nästa generations CLI för distributionsmodellen resurshantering


## <a name="create-supporting-resources"></a>Skapa stödresurser
Se till att du har den [Azure CLI](../../cli-install-nodejs.md) loggas och med hjälp av Resource Manager-läge:

```azurecli
azure config mode arm
```

Ersätt exempel parameternamn med egna värden i följande exempel. Exempel parameternamn ingår *myResourceGroup*, *mittlagringskonto*, och *myVM*.

Först skapa en resursgrupp. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli
azure group create myResourceGroup --location eastus
```

Skapa ett lagringskonto för att lagra dina virtuella datorer. I följande exempel skapas ett lagringskonto med namnet *mittlagringskonto*:

```azurecli
azure storage account create mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --kind Storage \
    --sku-name PLRS
```

Skapa ett virtuellt nätverk för att ansluta dina virtuella datorer till. I följande exempel skapas ett virtuellt nätverk med namnet *myVnet* med en adressprefixet *192.168.0.0/16*:

```azurecli
azure network vnet create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myVnet \
    --address-prefixes 192.168.0.0/16
```

Skapa två undernät för virtuellt nätverk – ett för frontend-trafik och en för backend-trafik. I följande exempel skapas två undernät, med namnet *mySubnetFrontEnd* och *mySubnetBackEnd*:

```azurecli
azure network vnet subnet create \
    --resource-group myResourceGroup \
    --location myVnet \
    --name mySubnetFrontEnd \
    --address-prefix 192.168.1.0/24
azure network vnet subnet create \
    --resource-group myResourceGroup \
    --location myVnet \
    --name mySubnetBackEnd \
    --address-prefix 192.168.2.0/24
```

## <a name="create-and-configure-multiple-nics"></a>Skapa och konfigurera flera nätverkskort
Du kan läsa mer information om [distribution av flera nätverkskort med hjälp av Azure CLI](../../virtual-machines/linux/multiple-nics.md), inklusive skript processen att loopar genom att skapa alla nätverkskort.

I följande exempel skapas två nätverkskort som heter *myNic1* och *myNic2*, med ett nätverkskort som ansluter till varje undernät:

```azurecli
azure network nic create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNic1 \
    --subnet-vnet-name myVnet \
    --subnet-name mySubnetFrontEnd
azure network nic create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNic2 \
    --subnet-vnet-name myVnet \
    --subnet-name mySubnetBackEnd
```

Vanligtvis du också skapa en [Nätverkssäkerhetsgruppen](../../virtual-network/virtual-networks-nsg.md) eller [belastningsutjämnare](../../load-balancer/load-balancer-overview.md) för att hantera och distribuera trafik mellan dina virtuella datorer. I följande exempel skapas en Nätverkssäkerhetsgrupp med namnet *myNetworkSecurityGroup*:

```azurecli
azure network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Binda dina nätverkskort till en säkerhetsgrupp för nätverk med hjälp av `azure network nic set`. I följande exempel Binder *myNic1* och *myNic2* med *myNetworkSecurityGroup*:

```azurecli
azure network nic set \
    --resource-group myResourceGroup \
    --name myNic1 \
    --network-security-group-name myNetworkSecurityGroup
azure network nic set \
    --resource-group myResourceGroup \
    --name myNic2 \
    --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Skapa en virtuell dator och koppla nätverkskorten
När du skapar den virtuella datorn kan ange du nu flera nätverkskort. I stället använder `--nic-name` för att ge ett enda nätverkskort, i stället använder du `--nic-names` och ange en kommaavgränsad lista över nätverkskort. Du måste också vara försiktig när du väljer VM-storlek. Det finns begränsningar för det totala antalet nätverkskort som du kan lägga till en virtuell dator. Läs mer om [Linux VM-storlekar](sizes.md). I följande exempel visas hur du anger flera nätverkskort och en VM-storlek som stöder användning av flera nätverkskort (*Standard_DS2_v2*):

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --os-type linux \
    --nic-names myNic1,myNic2 \
    --vm-size Standard_DS2_v2 \
    --storage-account-name mystorageaccount \
    --image-urn UbuntuLTS \
    --admin-username azureuser \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

När du lägger till flera nätverkskort till en Linux-VM, måste du skapa regler för routning. De här reglerna kan den virtuella datorn att skicka och ta emot trafik som hör till ett visst nätverkskort. Annars trafik som hör till eth1, till exempel kan inte bearbetas på rätt sätt med definierade standardvägen. För att åtgärda problemet routning finns [konfigurera gästoperativsystemet för flera nätverkskort](multiple-nics.md#configure-guest-os-for-multiple-nics).

## <a name="create-multiple-nics-using-resource-manager-templates"></a>Skapa flera nätverkskort med hjälp av Resource Manager-mallar
Azure Resource Manager-mallar använda deklarativa JSON-filer för att definiera din miljö. Du kan läsa ett [översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Resource Manager-mallar är ett sätt att skapa flera instanser av en resurs under distributionen, till exempel skapa flera nätverkskort. Du använder *kopiera* kan du ange antalet instanser som du vill skapa:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Läs mer om [skapar flera instanser med *kopiera*](../../resource-group-create-multiple.md). 

Du kan också använda en `copyIndex()` att sedan lägga till ett tal till ett resursnamn som du kan skapa `myNic1`, `myNic2`osv. Nedan visas ett exempel på att lägga till indexvärdet:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Du kan läsa en komplett exempel på [skapar flera nätverkskort med hjälp av Resource Manager-mallar](../../virtual-network/template-samples.md).

När du lägger till flera nätverkskort till en Linux-VM, måste du skapa regler för routning. De här reglerna kan den virtuella datorn att skicka och ta emot trafik som hör till ett visst nätverkskort. Annars trafik som hör till eth1, till exempel kan inte bearbetas på rätt sätt med definierade standardvägen. För att åtgärda problemet routning finns [konfigurera gästoperativsystemet för flera nätverkskort](multiple-nics.md#configure-guest-os-for-multiple-nics).

## <a name="next-steps"></a>Nästa steg
Se till att granska [Linux VM-storlekar](sizes.md) vid försök att skapa en virtuell dator med flera nätverkskort. Ta hänsyn till det maximala antalet nätverkskort som har stöd för varje VM-storlek. 

Kom ihåg att du kan inte lägga till ytterligare nätverkskort till en befintlig virtuell dator måste du skapa alla nätverkskort när du distribuerar den virtuella datorn. Vara försiktig när du planerar din distribution Se till att du har alla nödvändiga nätverksanslutningen redan från början.

