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
ms.date: 09/26/2017
ms.author: iainfou
ms.openlocfilehash: d981ffc9a0053ed8bf2d49f386f7c1c82d50c907
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Så här skapar du en virtuell Linux-dator i Azure med flera nätverkskort
Du kan skapa en virtuell dator (VM) i Azure som har flera virtuella nätverksgränssnitt (NIC) ansluten till den. Ett vanligt scenario är att ha olika undernät för frontend och backend-anslutning eller ett nätverk som är dedikerad för en lösning för övervakning eller säkerhetskopiering. Den här artikeln beskrivs hur du skapar en virtuell dator med flera nätverkskort som är kopplade till den och lägga till eller ta bort nätverkskort från en befintlig virtuell dator. Olika [VM-storlekar](sizes.md) stöder olika antal nätverkskort, så därför storlek den virtuella datorn.

Den här artikeln beskrivs hur du skapar en virtuell dator med flera nätverkskort med Azure CLI 2.0. Du kan också utföra dessa steg med [Azure CLI 1.0](multiple-nics-nodejs.md).


## <a name="create-supporting-resources"></a>Skapa stödresurser
Installera senaste [Azure CLI 2.0](/cli/azure/install-az-cli2) och logga in till en Azure med hjälp av [az inloggningen](/cli/azure/reference-index#az_login).

Ersätt exempel parameternamn med egna värden i följande exempel. Exempel parameternamn ingår *myResourceGroup*, *mittlagringskonto*, och *myVM*.

Skapa först en resursgrupp med [az group create](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Skapa ett virtuellt nätverk med [az network vnet skapa](/cli/azure/network/vnet#az_network_vnet_create). I följande exempel skapas ett virtuellt nätverk med namnet *myVnet* och undernät med namnet *mySubnetFrontEnd*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 192.168.1.0/24
```

Skapa ett undernät för backend-trafik med [az undernät för virtuellt nätverk skapa](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). I följande exempel skapas ett undernät med namnet *mySubnetBackEnd*:

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 192.168.2.0/24
```

Skapa en nätverkssäkerhetsgrupp med [az nätverket nsg skapa](/cli/azure/network/nsg#az_network_nsg_create). I följande exempel skapas en nätverkssäkerhetsgrupp med namnet *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Skapa och konfigurera flera nätverkskort
Skapa två nätverkskort med [az nätverket nic skapa](/cli/azure/network/nic#az_network_nic_create). I följande exempel skapas två nätverkskort som heter *myNic1* och *myNic2*, anslutna nätverkssäkerhetsgruppen med ett nätverkskort som ansluter till varje undernät:

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

## <a name="create-a-vm-and-attach-the-nics"></a>Skapa en virtuell dator och koppla nätverkskorten
När du skapar den virtuella datorn, ange nätverkskort du skapade med `--nics`. Du måste också vara försiktig när du väljer VM-storlek. Det finns begränsningar för det totala antalet nätverkskort som du kan lägga till en virtuell dator. Läs mer om [Linux VM-storlekar](sizes.md). 

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

Lägg till routningstabeller gästoperativsystemet genom att slutföra stegen i [konfigurera gästoperativsystemet för flera nätverkskort](#configure-guest-os-for- multiple-nics).

## <a name="add-a-nic-to-a-vm"></a>Lägga till ett nätverkskort till en virtuell dator
De här stegen kan du skapa en virtuell dator med flera nätverkskort. Du kan också lägga till nätverkskort till en befintlig virtuell dator med Azure CLI 2.0. Olika [VM-storlekar](sizes.md) stöder olika antal nätverkskort, så därför storlek den virtuella datorn. Om det behövs kan du [ändra storlek på en virtuell dator](change-vm-size.md).

Skapa ett annat nätverkskort med [az nätverket nic skapa](/cli/azure/network/nic#az_network_nic_create). I följande exempel skapas ett nätverkskort med namnet *myNic3* anslutna till backend-undernät och nätverket säkerhetsgruppen skapade i föregående steg:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Om du vill lägga till ett nätverkskort i en befintlig virtuell dator, först frigöra den virtuella datorn med [az vm frigöra](/cli/azure/vm#az_vm_deallocate). I följande exempel tar bort den virtuella datorn med namnet *myVM*:


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

Lägg till routningstabeller gästoperativsystemet genom att slutföra stegen i [konfigurera gästoperativsystemet för flera nätverkskort](#configure-guest-os-for- multiple-nics).

## <a name="remove-a-nic-from-a-vm"></a>Ta bort ett nätverkskort från en virtuell dator
Om du vill ta bort ett nätverkskort från en befintlig virtuell dator, först frigöra den virtuella datorn med [az vm frigöra](/cli/azure/vm#az_vm_deallocate). I följande exempel tar bort den virtuella datorn med namnet *myVM*:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Ta bort nätverkskortet med [az vm nic ta bort](/cli/azure/vm/nic#az_vm_nic_remove). I följande exempel tar bort *myNic3* från *myVM*:

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

Du kan läsa en komplett exempel på [skapar flera nätverkskort med hjälp av Resource Manager-mallar](../../virtual-network/virtual-network-deploy-multinic-arm-template.md).

Lägg till routningstabeller gästoperativsystemet genom att slutföra stegen i [konfigurera gästoperativsystemet för flera nätverkskort](#configure-guest-os-for- multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Konfigurera gästoperativsystemet för flera nätverkskort
När du lägger till flera nätverkskort till en Linux-VM, måste du skapa regler för routning. De här reglerna kan den virtuella datorn att skicka och ta emot trafik som hör till ett visst nätverkskort. Annars trafik som hör till *eth1*, till exempel kan inte bearbetas på rätt sätt med definierade standardvägen.

För att åtgärda problemet routning du först lägga till två routningstabeller till */etc/iproute2/rt_tables* på följande sätt:

```bash
echo "200 eth0-rt" >> /etc/iproute2/rt_tables
echo "201 eth1-rt" >> /etc/iproute2/rt_tables
```

För att göra ändringen beständiga och används under aktiveringen av network-stacken, redigera */etc/sysconfig/network-scripts/ifcfg-eth0* och */etc/sysconfig/network-scripts/ifcfg-eth1*. Ändra raden *”NM_CONTROLLED = yes”* till *”NM_CONTROLLED = no”*. Utan det här steget tillämpas ytterligare regler/routning automatiskt inte.
 
Utöka sedan routningstabeller. Antar vi att vi har följande inställningar:

*Routning*

```bash
default via 10.0.1.1 dev eth0 proto static metric 100
10.0.1.0/24 dev eth0 proto kernel scope link src 10.0.1.4 metric 100
10.0.1.0/24 dev eth1 proto kernel scope link src 10.0.1.5 metric 101
168.63.129.16 via 10.0.1.1 dev eth0 proto dhcp metric 100
169.254.169.254 via 10.0.1.1 dev eth0 proto dhcp metric 100
```

*Gränssnitt*

```bash
lo: inet 127.0.0.1/8 scope host lo
eth0: inet 10.0.1.4/24 brd 10.0.1.255 scope global eth0    
eth1: inet 10.0.1.5/24 brd 10.0.1.255 scope global eth1
```

Du sedan skapa följande filer och lägga till lämpliga regler och vägar till varje:

- */etc/sysconfig/network-scripts/rule-eth0*

    ```bash
    from 10.0.1.4/32 table eth0-rt
    to 10.0.1.4/32 table eth0-rt
    ```

- */etc/sysconfig/network-scripts/route-eth0*

    ```bash
    10.0.1.0/24 dev eth0 table eth0-rt
    default via 10.0.1.1 dev eth0 table eth0-rt
    ```

- */etc/sysconfig/network-scripts/rule-eth1*

    ```bash
    from 10.0.1.5/32 table eth1-rt
    to 10.0.1.5/32 table eth1-rt
    ```

- */etc/sysconfig/network-scripts/route-eth1*

    ```bash
    10.0.1.0/24 dev eth1 table eth1-rt
    default via 10.0.1.1 dev eth1 table eth1-rt
    ```

Om du vill tillämpa ändringarna genom att starta om den *nätverk* tjänsten enligt följande:

```bash
systemctl restart network
```

Regler för vidarebefordran är nu korrekt på plats och du kan ansluta med antingen gränssnitt efter behov.


## <a name="next-steps"></a>Nästa steg
Granska [Linux VM-storlekar](sizes.md) vid försök att skapa en virtuell dator med flera nätverkskort. Ta hänsyn till det maximala antalet nätverkskort som har stöd för varje VM-storlek. 
