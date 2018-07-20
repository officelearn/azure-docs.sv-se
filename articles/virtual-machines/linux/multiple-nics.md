---
title: Skapa en Linux-VM i Azure med flera nätverkskort | Microsoft Docs
description: Lär dig hur du skapar en Linux VM med flera nätverkskort som är kopplade till den med hjälp av Azure CLI 2.0 eller Resource Manager-mallar.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: 5d2d04d0-fc62-45fa-88b1-61808a2bc691
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/07/2018
ms.author: iainfou
ms.openlocfilehash: aae71dafd3685e44975049c4287c083abc2330bc
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2018
ms.locfileid: "39144864"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Så här skapar du en Linux-dator i Azure med flera nätverkskort
Du kan skapa en virtuell dator (VM) i Azure som har flera virtuella nätverksgränssnitt (NIC) kopplade till den. Ett vanligt scenario är att ha olika undernät för frontend och backend-anslutning eller ett nätverk som är dedikerad till en lösning för övervakning eller säkerhetskopiering. Den här artikeln beskriver hur du skapar en virtuell dator med flera nätverkskort som är kopplade till den och lägga till eller ta bort nätverkskort från en befintlig virtuell dator. Olika [VM-storlekar](sizes.md) stöd för olika antal nätverkskort, storlek, så den virtuella datorn i enlighet med detta.

Den här artikeln beskriver hur du skapar en virtuell dator med flera nätverkskort med Azure CLI 2.0. Du kan också utföra dessa steg med [Azure CLI 1.0](multiple-nics-nodejs.md).


## <a name="create-supporting-resources"></a>Skapa resurser
Installera senast [Azure CLI 2.0](/cli/azure/install-az-cli2) och logga in på Azure med hjälp av [az-inloggning](/cli/azure/reference-index#az_login).

I följande exempel, ersätter du exempel parameternamn med dina egna värden. Parametern exempelnamnen ingår *myResourceGroup*, *mystorageaccount*, och *myVM*.

Skapa först en resursgrupp med [az group create](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Skapa det virtuella nätverket med [az network vnet skapa](/cli/azure/network/vnet#az_network_vnet_create). I följande exempel skapas ett virtuellt nätverk med namnet *myVnet* och undernät med namnet *mySubnetFrontEnd*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 10.0.1.0/24
```

Skapa ett undernät för backend-trafik med [az network vnet-undernät skapa](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). I följande exempel skapas ett undernät med namnet *mySubnetBackEnd*:

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 10.0.2.0/24
```

Skapa en nätverkssäkerhetsgrupp med [az network nsg skapa](/cli/azure/network/nsg#az_network_nsg_create). I följande exempel skapas en nätverkssäkerhetsgrupp med namnet *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Skapa och konfigurera flera nätverkskort
Skapa två nätverkskort med [az network nic skapa](/cli/azure/network/nic#az_network_nic_create). I följande exempel skapas två nätverkskort, med namnet *myNic1* och *myNic2*, ansluten nätverkssäkerhetsgrupp, med ett nätverkskort som ansluter till varje undernät:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic1 \
    --vnet-name myVnet \
    --subnet mySubnetFrontEnd \
    --network-security-group myNetworkSecurityGroup
az network nic create \
    --resource-group myResourceGroup \
    --name myNic2 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Skapa en virtuell dator och koppla nätverkskort
När du skapar den virtuella datorn, anger du nätverkskort du skapade med `--nics`. Du måste också vara försiktig när du väljer virtuella datorstorlek. Det finns gränser för det totala antalet nätverkskort som du kan lägga till en virtuell dator. Läs mer om [Linux VM-storlekar](sizes.md).

Skapa en virtuell dator med [az vm create](/cli/azure/vm#az_vm_create). I följande exempel skapas en virtuell dator med namnet *myVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS3_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic1 myNic2
```

Lägg till routningstabeller i gästoperativsystemet genom att följa stegen i [konfigurera gästoperativsystemet för flera nätverkskort](#configure-guest-os-for- multiple-nics).

## <a name="add-a-nic-to-a-vm"></a>Lägg till ett nätverkskort till en virtuell dator
I föregående steg skapade en virtuell dator med flera nätverkskort. Du kan också lägga till nätverkskort till en befintlig virtuell dator med Azure CLI 2.0. Olika [VM-storlekar](sizes.md) stöd för olika antal nätverkskort, storlek, så den virtuella datorn i enlighet med detta. Om det behövs kan du [ändra storlek på en virtuell dator](change-vm-size.md).

Skapa ett annat nätverkskort med [az network nic skapa](/cli/azure/network/nic#az_network_nic_create). I följande exempel skapas ett nätverkskort med namnet *myNic3* är anslutna till backend-undernät och gruppen skapades i föregående steg:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Om du vill lägga till ett nätverkskort i en befintlig virtuell dator, först frigöra den virtuella datorn med [az vm deallocate](/cli/azure/vm#az_vm_deallocate). I följande exempel bort den virtuella datorn med namnet *myVM*:


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Lägga till nätverkskort med [az vm nic lägga till](/cli/azure/vm/nic#az_vm_nic_add). I följande exempel läggs *myNic3* till *myVM*:

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Starta den virtuella datorn med [az vm start](/cli/azure/vm#az_vm_start):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

Lägg till routningstabeller i gästoperativsystemet genom att följa stegen i [konfigurera gästoperativsystemet för flera nätverkskort](#configure-guest-os-for- multiple-nics).

## <a name="remove-a-nic-from-a-vm"></a>Ta bort ett nätverkskort från en virtuell dator
Om du vill ta bort ett nätverkskort från en befintlig virtuell dator, först frigöra den virtuella datorn med [az vm deallocate](/cli/azure/vm#az_vm_deallocate). I följande exempel bort den virtuella datorn med namnet *myVM*:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Ta bort nätverkskort med [az vm nic ta bort](/cli/azure/vm/nic#az_vm_nic_remove). I följande exempel tar bort *myNic3* från *myVM*:

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Starta den virtuella datorn med [az vm start](/cli/azure/vm#az_vm_start):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Skapa flera nätverkskort med hjälp av Resource Manager-mallar
Azure Resource Manager-mallar använda deklarativa JSON-filer för att definiera din miljö. Du kan läsa en [översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Resource Manager-mallar är ett sätt att skapa flera instanser av en resurs under distributionen, till exempel skapa flera nätverkskort. Du använder *kopia* kan du ange antalet instanser för att skapa:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Läs mer om [skapa flera instanser med *kopia*](../../resource-group-create-multiple.md). 

Du kan också använda en `copyIndex()` att lägga till ett tal till ett resursnamn där du kan skapa `myNic1`, `myNic2`osv. Nedan visas ett exempel på att lägga till indexvärdet:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Du kan läsa en komplett exempel på [skapar flera nätverkskort med hjälp av Resource Manager-mallar](../../virtual-network/template-samples.md).

Lägg till routningstabeller i gästoperativsystemet genom att följa stegen i [konfigurera gästoperativsystemet för flera nätverkskort](#configure-guest-os-for- multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Konfigurera gästoperativsystem för flera nätverkskort

De föregående stegen skapade ett virtuellt nätverk och undernät, anslutna nätverkskort och sedan skapat en virtuell dator. En offentlig IP-adress och ett grupp säkerhetsregler som tillåter SSH-trafik har inte skapats. Om du vill konfigurera gästoperativsystemet för flera nätverkskort måste du tillåta fjärranslutningar och köra kommandon lokalt på den virtuella datorn.

Om du vill tillåta SSH-trafik, skapar du en nätverkssäkerhetsgruppregel med [az network nsg-regel skapar](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) på följande sätt:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name allow_ssh \
    --priority 101 \
    --destination-port-ranges 22
```

Skapa en offentlig IP-adress med [az nätverket offentliga ip-skapa](/cli/azure/network/public-ip#az-network-public-ip-create) och tilldela den till det första nätverkskortet med [az network nic ip-config update](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update):

```azurecli
az network public-ip-address create --resource-group myResourceGroup --name myPublicIP

az network nic ip-config update \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --name ipconfig1 \
    --public-ip-addres myPublicIP
```

Du kan visa visa offentliga IP-adressen för den virtuella datorn [az vm show](/cli/azure/vm#az-vm-show) på följande sätt:

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Nu SSH till den offentliga IP-adressen för den virtuella datorn. Standardanvändarnamnet i ett tidigare steg har *azureuser*. Ange ditt eget användarnamn och en offentlig IP-adress:

```bash
ssh azureuser@137.117.58.232
```

Du måste manuellt lägga till vägar för operativsystemet för varje sekundära nätverksgränssnitt för att skicka till eller från ett sekundärt nätverksgränssnitt. I den här artikeln *eth1* är sekundära gränssnitt. För att lägga till vägar för operativsystemet varierar distribution. Finns i dokumentationen för din distribution anvisningar.

När du lägger till vägen för operativsystemet, gateway-adressen är *.1* för undernätet som nätverksgränssnittet finns i. Exempel: om nätverksgränssnittet har tilldelats adressen *10.0.2.4*, gatewayen som du anger för vägen är *10.0.2.1*. Du kan definiera ett visst nätverk för slutpunkten för resvägen eller ange ett mål för *0.0.0.0*om du vill att all trafik för gränssnittet för att gå igenom den angivna gatewayen. Gateway för varje undernät hanteras av det virtuella nätverket.

När du har lagt till vägen för en sekundär gränssnitt, kontrollera att flödet är i din routningstabellen med `route -n`. Följande Exempelutdata är routningstabellen som har två nätverksgränssnitt som lagts till i den virtuella datorn i den här artikeln:

```bash
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         10.0.1.1        0.0.0.0         UG    0      0        0 eth0
0.0.0.0         10.0.2.1        0.0.0.0         UG    0      0        0 eth1
10.0.1.0        0.0.0.0         255.255.255.0   U     0      0        0 eth0
10.0.2.0        0.0.0.0         255.255.255.0   U     0      0        0 eth1
168.63.129.16   10.0.1.1        255.255.255.255 UGH   0      0        0 eth0
169.254.169.254 10.0.1.1        255.255.255.255 UGH   0      0        0 eth0
```

Bekräfta att den väg som du har lagt till bevaras mellan omstarter genom att kontrollera din routningstabellen igen efter en omstart. Om du vill testa anslutningen kan du ange följande kommando, till exempel, där *eth1* är namnet på ett sekundärt nätverksgränssnitt:

```bash
ping bing.com -c 4 -I eth1
```

## <a name="next-steps"></a>Nästa steg
Granska [Linux VM-storlekar](sizes.md) när du försöker skapa en virtuell dator med flera nätverkskort. Ta hänsyn till det maximala antalet nätverkskort som har stöd för varje VM-storlek.

Att om du vill skydda dina virtuella datorer, Använd just-in-time-åtkomst till virtuell dator. Den här funktionen öppnas reglerna för nätverkssäkerhetsgrupper för SSH-trafik när det behövs, och för en angiven tidsperiod. Mer information finns i [Manage virtual machine access using just in time](../../security-center/security-center-just-in-time.md) (Hantera åtkomsten till virtuella datorer med Just-In-Time).
