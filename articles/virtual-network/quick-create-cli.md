---
title: "Skapa ett virtuellt nätverk - Azure CLI | Microsoft Docs"
description: "Snabbt lära dig skapa ett virtuellt nätverk med Azure CLI. Ett virtuellt nätverk gör det möjligt för flera olika typer av Azure-resurser till privat kommunicera med varandra."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: ff27d557f221be61a7384f6aaf6e57cef5cebb81
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>Skapa ett virtuellt nätverk med Azure CLI

I den här artikeln får du lära dig hur du skapar ett virtuellt nätverk. När du har skapat ett virtuellt nätverk distribuerar du två virtuella datorer i det virtuella nätverket för att testa privata nätverkskommunikation mellan dem.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt i den här artikeln kräver att du använder Azure CLI version 2.0.4 eller senare. Du hittar den installerade versionen genom att köra `az --version`. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az_group_create). En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. 

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*. Alla Azure-resurser skapas inom en Azure-plats (eller en region).

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med den [az network vnet skapa](/cli/azure/network/vnet#az_network_vnet_create) kommando. I följande exempel skapas ett virtuellt nätverk för standard heter *myVirtualNetwork* med ett undernät med namnet *standard*. Eftersom en plats har inte angetts, skapar Azure det virtuella nätverket på samma plats som resursgruppen.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

När det virtuella nätverket har skapats är en del av den returnerade informationen på följande sätt.

```azurecli
"newVNet": {
    "addressSpace": {
      "addressPrefixes": [
        "10.0.0.0/16"
```

Alla virtuella nätverk har en eller flera adressprefix tilldelad. Eftersom ett adressprefix har inte angetts när du skapar det virtuella nätverket, definierad Azure adressutrymmet 10.0.0.0/16 som standard. Adressutrymmet anges i CIDR-notering. Adressutrymme 10.0.0.0/16 omfattar 10.0.0.0-10.0.255.254.

En annan del av informationen som returnerades är den **addressPrefix** av *10.0.0.0/24* för den *standard* undernät som anges i kommandot. Ett virtuellt nätverk innehåller noll eller flera undernät. Kommandot har skapat ett undernät med namnet *standard*, men inga adressprefix har angetts för undernätet. När ett adressprefix har inte angetts för ett virtuellt nätverk eller undernät, definierar Azure 10.0.0.0/24 som adressprefixet för det första undernätet som standard. Därför undernätet omfattar 10.0.0.0-10.0.0.254, men endast 10.0.0.4-10.0.0.254 är tillgängliga, eftersom Azure reserverar de första fyra adresserna (0-3) och den sista adressen i varje undernät.

## <a name="test-network-communication"></a>Testa nätverkskommunikation

Ett virtuellt nätverk gör det möjligt för flera typer av Azure-resurser till privat kommunicera med varandra. En typ av resurs som du kan distribuera till ett virtuellt nätverk är en virtuell dator. Skapa två virtuella datorer i det virtuella nätverket så att du kan verifiera privata kommunikationen mellan dem i ett senare steg.

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa en virtuell dator med kommandot [az vm create](/cli/azure/vm#az_vm_create). I följande exempel skapas en virtuell dator med namnet *myVm1*. Om SSH-nycklar inte redan finns en nyckel standardplatsen, skapar dem i kommandot. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`. Den `--no-wait` alternativet skapar den virtuella datorn i bakgrunden så du kan fortsätta till nästa steg.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

Azure skapar automatiskt den virtuella datorn i den *standard* undernätet för den *myVirtualNetwork* virtuella nätverk, eftersom det virtuella nätverket finns i resursgruppen och inga virtuella nätverk eller undernät har angetts i kommandot. Azure DHCP tilldelas automatiskt 10.0.0.4 till den virtuella datorn när den skapas, eftersom det är den första tillgängliga adressen i den *standard* undernät. Platsen som en virtuell dator skapas i måste vara på samma plats som det virtuella nätverket finns i. Den virtuella datorn är inte nödvändigt att vara i samma resursgrupp som det virtuella nätverket även om det är i den här artikeln.

Skapa en andra virtuell dator. Azure skapar även den här virtuella datorn i den *standard* undernät.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn. När du har skapat den virtuella datorn returnerar Azure CLI-utdata liknar följande exempel: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm1",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

I det här exemplet kan du se det i **privateIpAddress** är *10.0.0.5*. Azure DHCP tilldelas automatiskt *10.0.0.5* till den virtuella datorn eftersom den var nästa tillgängliga adress i den *standard* undernät. Anteckna den **publicIpAddress**. Den här adressen används för åtkomst till den virtuella datorn från Internet i ett senare steg. Den offentliga IP-adressen är inte tilldelas från virtuellt nätverk eller undernät adressprefix. Offentliga IP-adresser tilldelas från en [adresspool som tilldelats varje Azure-region](https://www.microsoft.com/download/details.aspx?id=41653). Medan Azure vet vilken offentliga IP-adress har tilldelats en virtuell dator, har operativsystemet som körs på en virtuell dator inga medvetenhet om någon offentlig IP-adress som tilldelats.

### <a name="connect-to-a-virtual-machine"></a>Ansluta till en virtuell dator

Använd följande kommando för att skapa en SSH-session med den *myVm2* virtuella datorn. Ersätt `<publicIpAddress>` med offentliga IP-adressen för den virtuella datorn. I exemplet ovan, IP-adressen är *40.68.254.142*.

```bash 
ssh <publicIpAddress>
```

### <a name="validate-communication"></a>Validera kommunikation

Använd följande kommando för att bekräfta kommunikation med *myVm1* från *myVm2*:

```bash
ping myVm1 -c 4
```

Du får fyra svar från *10.0.0.4*. Du kan kommunicera med *myVm1* från *myVm2*eftersom både virtuella datorer har den privata IP-adresser tilldelas från den *standard* undernät. Du kan pinga värdnamnet eftersom automatiskt ger DNS-namnmatchning för alla värdar inom ett virtuellt nätverk i Azure.

Använd följande kommando för att bekräfta utgående kommunikation till Internet:

```bash
ping bing.com -c 4
```

Du får fyra svar från bing.com. Som standard kan en virtuell dator i ett virtuellt nätverk kommunicera utgående till Internet.

Avsluta SSH-session till den virtuella datorn.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behövs kan du använda den [ta bort grupp az](/cli/azure/group#az_group_delete) kommando för att ta bort resursgruppen och alla resurser som den innehåller:

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

Du har distribuerat en standard virtuellt nätverk med ett undernät i den här artikeln. Mer information om hur du skapar en anpassad virtuellt nätverk med flera undernät, även fortsättningsvis självstudierna för att skapa egna virtuella nätverk.

> [!div class="nextstepaction"]
> [Skapa en anpassad virtuellt nätverk](virtual-networks-create-vnet-arm-cli.md)
