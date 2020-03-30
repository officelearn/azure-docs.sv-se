---
title: Skapa en virtuell Linux-dator i Azure med flera nätverkskort
description: Lär dig hur du skapar en virtuell Linux-dator med flera nätverkskort anslutna till den med azure CLI- eller Resource Manager-mallarna.
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: networking
ms.topic: article
ms.workload: infrastructure
ms.date: 06/07/2018
ms.author: cynthn
ms.openlocfilehash: ecbff4beadd9d10a8489c89cc322c0bb67ec5f40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267188"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Så här skapar du en virtuell Linux-dator i Azure med flera nätverkskort


I den här artikeln beskrivs hur du skapar en virtuell dator med flera nätverkskort med Azure CLI.

## <a name="create-supporting-resources"></a>Skapa stödresurser
Installera den senaste [Azure CLI](/cli/azure/install-az-cli2) och logga in på ett Azure-konto med [az login](/cli/azure/reference-index).

I följande exempel ersätter du exempelparameternamn med dina egna värden. Exempelparameternamn inkluderade *myResourceGroup,* *mystorageaccount*och *myVM*.

Skapa först en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på *eastus-platsen:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Skapa det virtuella nätverket med [az nätverk vnet skapa](/cli/azure/network/vnet). I följande exempel skapas ett virtuellt nätverk med namnet *myVnet* och undernät med namnet *mySubnetFrontEnd:*

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 10.0.1.0/24
```

Skapa ett undernät för backend-trafik med [az network vnet-undernät skapa](/cli/azure/network/vnet/subnet). I följande exempel skapas ett undernät med namnet *mySubnetBackEnd:*

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 10.0.2.0/24
```

Skapa en nätverkssäkerhetsgrupp med [az network nsg create](/cli/azure/network/nsg). I följande exempel skapas en nätverkssäkerhetsgrupp med namnet *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Skapa och konfigurera flera nätverkskort
Skapa två nätverkskort med [az nätverk nic skapa](/cli/azure/network/nic). I följande exempel skapas två nätverkskort med namnet *myNic1* och *myNic2*, anslutna till nätverkssäkerhetsgruppen, med ett nätverkskort som ansluter till varje undernät:

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

## <a name="create-a-vm-and-attach-the-nics"></a>Skapa en virtuell dator och bifoga nätverkskort
När du skapar den virtuella datorn anger `--nics`du de nätverkskort som du skapade med . Du måste också vara försiktig när du väljer vm-storlek. Det finns gränser för det totala antalet nätverkskort som du kan lägga till i en virtuell dator. Läs mer om [Linux VM storlekar](sizes.md).

Skapa en virtuell dator med [az vm create](/cli/azure/vm). I följande exempel skapas en virtuell dator med namnet *myVM:*

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

Lägg till routningstabeller i gästoperativsystemet genom att slutföra stegen i [Konfigurera gästoperativsystemet för flera nätverkskort](#configure-guest-os-for-multiple-nics).

## <a name="add-a-nic-to-a-vm"></a>Lägga till ett nätverkskort till en virtuell dator
De tidigare stegen skapade en virtuell dator med flera nätverkskort. Du kan också lägga till nätverkskort till en befintlig virtuell dator med Azure CLI. Olika [vm-storlekar](sizes.md) stöder ett varierande antal nätverkskort, så storlek på den virtuella datorn därefter. Om det behövs kan du [ändra storlek på en virtuell dator](change-vm-size.md).

Skapa ett annat nätverkskort med [az-nätverksnic create](/cli/azure/network/nic). I följande exempel skapas ett nätverkskort med namnet *myNic3* som är anslutet till backend-undernätet och nätverkssäkerhetsgruppen som skapats i föregående steg:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Om du vill lägga till ett nätverkskort till en befintlig virtuell dator, först deallocate den virtuella datorn med [az vm deallocate](/cli/azure/vm). I följande exempel frigörs den virtuella datorn med namnet *myVM:*


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Lägg till nätverkskortet med [az vm nic add](/cli/azure/vm/nic). I följande exempel läggs *myNic3* till *myVM:*

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Starta den virtuella datorn med [az vm-start:](/cli/azure/vm)

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

Lägg till routningstabeller i gästoperativsystemet genom att slutföra stegen i [Konfigurera gästoperativsystemet för flera nätverkskort](#configure-guest-os-for-multiple-nics).

## <a name="remove-a-nic-from-a-vm"></a>Ta bort ett nätverkskort från en virtuell dator
Om du vill ta bort ett nätverkskort från en befintlig virtuell dator, först deallocate den virtuella datorn med [az vm deallocate](/cli/azure/vm). I följande exempel frigörs den virtuella datorn med namnet *myVM:*

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Ta bort nätverkskortet med [az vm nic remove](/cli/azure/vm/nic). I följande exempel tas *myNic3* bort från *myVM:*

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Starta den virtuella datorn med [az vm-start:](/cli/azure/vm)

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Skapa flera nätverkskort med Resource Manager-mallar
Azure Resource Manager-mallar använder deklarativa JSON-filer för att definiera din miljö. Du kan läsa en [översikt över Azure Resource Manager](../../azure-resource-manager/management/overview.md). Resource Manager-mallar är ett sätt att skapa flera instanser av en resurs under distributionen, till exempel skapa flera nätverkskort. Du använder *kopia* för att ange hur många instanser som ska skapas:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Läs mer om [hur du skapar flera instanser med hjälp av *kopia*](../../resource-group-create-multiple.md). 

Du kan också `copyIndex()` använda en för att sedan lägga till ett `myNic1` `myNic2`nummer till ett resursnamn, vilket gör att du kan skapa , , etc. Följande visar ett exempel på att lägga till indexvärdet:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Du kan läsa ett komplett exempel på [hur du skapar flera nätverkskort med Hjälp av Resource Manager-mallar](../../virtual-network/template-samples.md).

Lägg till routningstabeller i gästoperativsystemet genom att slutföra stegen i [Konfigurera gästoperativsystemet för flera nätverkskort](#configure-guest-os-for-multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Konfigurera gästoperativ för flera nätverkskort

De föregående stegen skapade ett virtuellt nätverk och undernät, bifogade nätverkskort och skapade sedan en virtuell dator. En offentlig IP-adress och nätverkssäkerhetsgruppsregler som tillåter SSH-trafik skapades inte. Om du vill konfigurera gästoperativsystemet för flera nätverkskort måste du tillåta fjärranslutningar och köra kommandon lokalt på den virtuella datorn.

Om du vill tillåta SSH-trafik skapar du en regel för nätverkssäkerhetsgrupper med [az-nätverks nsg-regel skapa](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) enligt följande:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name allow_ssh \
    --priority 101 \
    --destination-port-ranges 22
```

Skapa en offentlig [IP-adress med az network public-ip skapa](/cli/azure/network/public-ip#az-network-public-ip-create) och tilldela den till det första nätverkskortet med [az nätverk nic ip-config uppdatering:](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update)

```azurecli
az network public-ip create --resource-group myResourceGroup --name myPublicIP

az network nic ip-config update \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --name ipconfig1 \
    --public-ip myPublicIP
```

Om du vill visa den offentliga IP-adressen för den virtuella datorn använder du [az vm-vyn](/cli/azure/vm#az-vm-show) enligt följande::

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Nu SSH till den offentliga IP-adressen för din virtuella dator. Standardanvändarnamnet som angavs i ett tidigare steg var *azureuser*. Ange ditt eget användarnamn och offentliga IP-adress:

```bash
ssh azureuser@137.117.58.232
```

Om du vill skicka till eller från ett sekundärt nätverksgränssnitt måste du manuellt lägga till beständiga vägar till operativsystemet för varje sekundärt nätverksgränssnitt. I den här artikeln är *eth1* det sekundära gränssnittet. Instruktioner för att lägga till beständiga vägar till operativsystemet varierar beroende på distribution. Se dokumentation för din distribution för instruktioner.

När du lägger till vägen till operativsystemet är gateway-adressen *1* för vilket undernät nätverksgränssnittet är i. Om nätverksgränssnittet till exempel tilldelas adressen *10.0.2.4*är den gateway som du anger för vägen *10.0.2.1*. Du kan definiera ett specifikt nätverk för ruttens mål eller ange ett mål på *0.0.0.0,* om du vill att all trafik för gränssnittet ska gå igenom den angivna gatewayen. Gatewayen för varje undernät hanteras av det virtuella nätverket.

När du har lagt till vägen för ett sekundärt gränssnitt kontrollerar du att vägen finns i din rutttabell med `route -n`. Följande exempelutdata är för vägtabellen som har de två nätverksgränssnitten tillagda i den virtuella datorn i den här artikeln:

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

Bekräfta att vägen som du lade till kvarstår över omstarter genom att kontrollera vägtabellen igen efter en omstart. Om du vill testa anslutningen kan du till exempel ange följande kommando där *eth1* är namnet på ett sekundärt nätverksgränssnitt:

```bash
ping bing.com -c 4 -I eth1
```

## <a name="next-steps"></a>Nästa steg
Granska [Storlekar på virtuella linux-datorer](sizes.md) när du försöker skapa en virtuell dator med flera nätverkskort. Var uppmärksam på det maximala antalet nätverkskort som varje vm-storlek stöder.

Om du vill skydda dina virtuella datorer ytterligare använder du just i tids-VM-åtkomst. Den här funktionen öppnar regler för nätverkssäkerhetsgrupp för SSH-trafik när det behövs och under en bestämd tidsperiod. Mer information finns i [Manage virtual machine access using just in time](../../security-center/security-center-just-in-time.md) (Hantera åtkomsten till virtuella datorer med Just-In-Time).
