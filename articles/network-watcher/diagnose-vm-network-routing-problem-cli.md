---
title: Diagnostisera en virtuell dator problem med nätverksroutning – Azure CLI | Microsoft Docs
description: I den här artikeln får du lära dig hur du diagnostiserar en virtuell dator problem med nätverksroutning med nästa hopp-funktionen i Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 8b881e51bfac25b83a828ad1f44fcd6d7da1c791
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948065"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>Diagnostisera en virtuell dator problem med nätverksroutning – Azure CLI

I den här artikeln får du distribuera en virtuell dator (VM) och kontrollera kommunikation till en IP-adress och URL: en. Du lär dig också hur du fastställer orsaken till ett kommunikationsfel och hur du löser problemet.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste den här artikeln kräver att du kör Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). När du har verifierat CLI-versionen kör du `az login` för att skapa en anslutning till Azure. CLI-kommandon i den här artikeln har formaterats för att köras i ett Bash-gränssnitt.

## <a name="create-a-vm"></a>Skapa en virtuell dator

Innan du kan skapa en virtuell dator måste du skapa en resursgrupp som innehåller den virtuella datorn. Skapa en resursgrupp med [az group create](/cli/azure/group#az-group-create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Skapa en virtuell dator med [az vm create](/cli/azure/vm#az-vm-create). Om det inte redan finns SSH-nycklar på en standardnyckelplats skapar kommandot dem. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`. I följande exempel skapas en virtuell dator med namnet *myVm*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn. Fortsätt inte med de återstående stegen förrän den virtuella datorn har skapats och CLI returnerar utdata.

## <a name="test-network-communication"></a>Testa nätverkskommunikationen

Om du vill testa nätverkskommunikation med Network Watcher måste du först aktivera en network watcher i den region som den virtuella datorn som du vill testa har och sedan använda Network Watcher nästa hopp-funktionen för att testa kommunikation.

### <a name="enable-network-watcher"></a>Aktivera nätverksbevakare

Om du redan har en network watcher som aktiverats i regionen USA, östra, gå vidare till [Använd nästa hopp](#use-next-hop). Använd den [az network watcher konfigurera](/cli/azure/network/watcher#az-network-watcher-configure) kan du skapa en network watcher i regionen östra USA:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>Använda funktionen för nästa hopp

Azure skapar automatiskt vägar till olika standardmål. Du kan skapa egna vägar som ersätter standardvägarna. Ibland kan egna vägar göra att kommunikationen misslyckas. Testa routning från en virtuell dator med [az network watcher show-next-hop](/cli/azure/network/watcher?view=azure-cli-latest#az-network-watcher-show-next-hop) fastställa nexthop routning när trafik är avsedd för en specifik adress.

Testa utgående kommunikation från den virtuella datorn till någon av IP-adresserna för www.bing.com:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 13.107.21.200 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

Efter några sekunder utdata informerar dig som den **nextHopType** är **Internet**, och att den **routeTableId** är **Systemväg**. Det här resultatet talar om att det finns en giltig väg till målet.

Testa utgående kommunikation från den virtuella datorn till 172.31.0.100:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 172.31.0.100 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

Informerar dig om de utdata som returneras som **ingen** är den **nextHopType**, och att den **routeTableId** är också **Systemväg**. Resultatet visar att det visserligen finns en giltig systemväg till målet, men att det inte finns något nästa hopp för att dirigera trafiken till målet.

## <a name="view-details-of-a-route"></a>Visa information om en väg

Om du vill analysera routning ytterligare, granska de effektiva vägarna för nätverksgränssnittet med den [az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table) kommando:

```azurecli-interactive
az network nic show-effective-route-table \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

Följande text som ingår i den returnerade utdatan:

```azurecli
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
  },
  "addressPrefix": [
    "0.0.0.0/0"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "Internet",
  "source": "Default",
  "state": "Active"
},
```

När du använde den `az network watcher show-next-hop` kommando för att testa utgående kommunikation till 13.107.21.200 i [Använd nästa hopp](#use-next-hop), vägen med den **addressPrefix** 0.0.0.0/0** användes för att dirigera trafik till adressen, eftersom Ingen annan väg i utdata innehåller adressen. Som standard dirigeras alla adresser som inte anges inom adressprefixet till en annan väg till internet.

När du använde den `az network watcher show-next-hop` kommandot för att testa utgående kommunikation till 172.31.0.100 men, resultatet informeras du om att det fanns inga nästa hopptyp. I den returnerade utdatan se du också följande text:

```azurecli
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
      },
  "addressPrefix": [
    "172.16.0.0/12"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "None",
  "source": "Default",
  "state": "Active"
},
```

Som du ser i utdata från den `az network watcher nic show-effective-route-table` kommandot, men det finns en standardväg till 172.16.0.0/12 prefix, som innehåller 172.31.0.100 adressen, den **nextHopType** är **ingen**. Azure skapar en standardväg till 172.16.0.0/12 men anger inte en nästa hopptyp förrän det finns någon anledning till det. Om du till exempel att du lagt till adressintervallet 172.16.0.0/12 adressutrymmet för det virtuella nätverket, Azure ändras den **nextHopType** till **virtuellt nätverk** för vägen. En kontroll visas sedan **virtuellt nätverk** som den **nextHopType**.

## <a name="clean-up-resources"></a>Rensa resurser

När resurserna inte behövs längre kan du använda [az group delete](/cli/azure/group#az-group-delete) för att ta bort resursgruppen och alla relaterade resurser den innehåller:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat en virtuell dator och diagnostiseras nätverksroutning från den virtuella datorn. Du har lärt dig att Azure skapar flera standardvägar och testat routning till två olika mål. Läs mer om [routning i Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) och hur du [skapar egna vägar](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

För utgående VM-anslutningar kan också bestämma svarstiden du och tillåtna respektive nekade nätverkstrafik mellan den virtuella datorn och en slutpunkt med Network Watchers [anslutningsfelsökning](network-watcher-connectivity-cli.md) kapaciteten. Du kan övervaka kommunikation mellan en virtuell dator och en slutpunkt, till exempel en IP-adress eller URL: en, över tid med Network Watcher anslutning monitor-funktionen. Läs hur genom att läsa [övervaka en nätverksanslutning](connection-monitor.md).