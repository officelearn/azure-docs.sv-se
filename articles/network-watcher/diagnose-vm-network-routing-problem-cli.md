---
title: Diagnostisera ett problem med vm-nätverksroutning - Azure CLI
titleSuffix: Azure Network Watcher
description: I den här artikeln får du lära dig hur du diagnostiserar ett problem med en virtuell datornätverksroutning med hjälp av nästa hopp-funktion i Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: damendo
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
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: ae139ea7aca7c3896fcd7b0acf2bf6673490a2f4
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382910"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>Diagnostisera ett problem med nätverksroutning för virtuella datorer - Azure CLI

I den här artikeln distribuerar du en virtuell dator (VM) och kontrollerar sedan kommunikationen till en IP-adress och url. Du lär dig också hur du fastställer orsaken till ett kommunikationsfel och hur du löser problemet.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda Azure CLI lokalt kräver den här artikeln att du kör Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). När du har verifierat `az login` Azure CLI-versionen körs du för att skapa en anslutning med Azure. Azure CLI-kommandona i den här artikeln är formaterade för att köras i ett Bash-skal.

## <a name="create-a-vm"></a>Skapa en virtuell dator

Innan du kan skapa en virtuell dator måste du skapa en resursgrupp som innehåller den virtuella datorn. Skapa en resursgrupp med [az group create](/cli/azure/group#az-group-create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på *eastus-platsen:*

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

Det tar några minuter att skapa den virtuella datorn. Fortsätt inte med återstående steg förrän den virtuella datorn har skapats och Azure CLI returnerar utdata.

## <a name="test-network-communication"></a>Testa nätverkskommunikationen

Om du vill testa nätverkskommunikation med Network Watcher måste du först aktivera en nätverksbevakare i den region som den virtuella datorn som du vill testa finns i och sedan använda Network Watchers nästa hoppkapacitet för att testa kommunikationen.

### <a name="enable-network-watcher"></a>Aktivera nätverksbevakare

Om du redan har aktiverat en nätverksbevakare i regionen östra USA går du till [Använd nästa hopp](#use-next-hop). Använd kommandot [konfigurera az-nätverksbevakare](/cli/azure/network/watcher#az-network-watcher-configure) för att skapa en nätverksbevakare i regionen östra USA:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>Använda funktionen för nästa hopp

Azure skapar automatiskt vägar till olika standardmål. Du kan skapa egna vägar som ersätter standardvägarna. Ibland kan egna vägar göra att kommunikationen misslyckas. Om du vill testa routning från en virtuell dator använder du [az network watcher show-next-hop](/cli/azure/network/watcher?view=azure-cli-latest#az-network-watcher-show-next-hop) för att bestämma nästa routningshopp när trafiken är avsedd för en viss adress.

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

Efter några sekunder informerar utdata dig om att **nextHopType** är **Internet**och att **routeTableId** är **System Route**. Detta resultat låter dig veta att det finns en giltig väg till destinationen.

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

Utdata som returneras informerar dig om att **Ingen** är **nextHopType**och att **routeTableId** också är **System route**. Resultatet visar att det visserligen finns en giltig systemväg till målet, men att det inte finns något nästa hopp för att dirigera trafiken till målet.

## <a name="view-details-of-a-route"></a>Visa information om en väg

Om du vill analysera routning ytterligare granskar du de effektiva vägarna för nätverksgränssnittet med kommandot [az network nic show-effective-route-table:](/cli/azure/network/nic#az-network-nic-show-effective-route-table)

```azurecli-interactive
az network nic show-effective-route-table \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

Följande text ingår i den returnerade utdata:

```
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

När du `az network watcher show-next-hop` använde kommandot för att testa utgående kommunikation till 13.107.21.200 i [Använd nästa hopp](#use-next-hop)användes vägen med **adressenPrefix** 0.0.0/0** för att dirigera trafik till adressen, eftersom ingen annan väg i utdata innehåller adressen. Som standard dirigeras alla adresser som inte anges inom adressprefixet till en annan väg till internet.

När du `az network watcher show-next-hop` använde kommandot för att testa utgående kommunikation till 172.31.0.100 dock, meddelade resultatet dig att det inte fanns någon nästa hop typ. I den returnerade utdata ser du också följande text:

```
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

Som du kan se i `az network watcher nic show-effective-route-table` utdata från kommandot, även om det finns en standardväg till prefixet 172.16.0.0/12, som innehåller adressen 172.31.0.100, är **nextHopType** **Ingen**. Azure skapar en standardväg till 172.16.0.0/12 men anger inte en nästa hopptyp förrän det finns någon anledning till det. Om du till exempel har lagt till adressintervallet 172.16.0.0/12 i adressutrymmet i det virtuella nätverket, ändras azure **nextHopType** till **virtuellt nätverk** för vägen. En kontroll skulle då visa **virtuellt nätverk** som **nextHopType**.

## <a name="clean-up-resources"></a>Rensa resurser

När resurserna inte behövs längre kan du använda [az group delete](/cli/azure/group#az-group-delete) för att ta bort resursgruppen och alla relaterade resurser den innehåller:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln skapade du en virtuell dator och diagnostiserade nätverksdirigering från den virtuella datorn. Du har lärt dig att Azure skapar flera standardvägar och testat routning till två olika mål. Läs mer om [routning i Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) och hur du [skapar egna vägar](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

För utgående VM-anslutningar kan du också bestämma svarstiden och tillåten och nekad nätverkstrafik mellan den virtuella datorn och en slutpunkt med hjälp av Network Watchers [anslutningsfelsökningsfunktion.](network-watcher-connectivity-cli.md) Du kan övervaka kommunikationen mellan en virtuell dator och en slutpunkt, till exempel en IP-adress eller URL, med tiden med hjälp av anslutningsövervakningen för Nätverksbevakning. Mer information finns i [Övervaka en nätverksanslutning](connection-monitor.md).
