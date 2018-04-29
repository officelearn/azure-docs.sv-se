---
title: Diagnostisera virtuella routning nätverksproblem - Azure CLI | Microsoft Docs
description: I den här artikeln får lära du att diagnostisera virtuella routning nätverksproblem med nästa hopp-funktionen i Azure Nätverksbevakaren.
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
ms.openlocfilehash: fcb7ec2e40b5c0e8794d2f4d70395dcbecca019c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>Diagnostisera virtuella routning nätverksproblem - Azure CLI

I den här artikeln distribuera en virtuell dator (VM) och kontrollera kommunikationen till IP-adress och URL: en. Du kan fastställa orsaken till ett kommunikationsfel och hur du kan lösa.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt i den här artikeln kräver att du använder Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). När du har kontrollerat CLI-versionen kör `az login` att skapa en anslutning med Azure. CLI-kommandona i den här artikeln har formaterats för att köras i ett Bash-gränssnitt.

## <a name="create-a-vm"></a>Skapa en virtuell dator

Innan du kan skapa en virtuell dator, måste du skapa en resursgrupp som innehåller den virtuella datorn. Skapa en resursgrupp med [az group create](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Skapa en virtuell dator med [az vm create](/cli/azure/vm#az_vm_create). Om det inte redan finns SSH-nycklar på en standardnyckelplats skapar kommandot dem. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`. I följande exempel skapas en virtuell dator med namnet *myVm*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn. Inte fortsätta med stegen tills den virtuella datorn skapas och CLI returnerar utdata.

## <a name="test-network-communication"></a>Testa nätverkskommunikation

Om du vill testa kommunikation med Nätverksbevakaren måste du först aktivera en nätverksbevakaren i den region som den virtuella datorn som du vill testa och sedan använda nätverket Watcher nästa hopp-funktionen för att testa kommunikation.

### <a name="enable-network-watcher"></a>Aktivera nätverksbevakaren

Om du redan har en nätverksbevakaren aktiverat i östra USA kan gå vidare till [Använd nästa hopp](#use-next-hop). Använd den [az nätverksbevakaren konfigurera](/cli/azure/network/watcher#az-network-watcher-configure) kommando för att skapa en nätverksbevakaren i östra USA:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>Använd nästa hopp

Azure skapar automatiskt vägar till standard mål. Du kan skapa anpassade vägar som åsidosätter standardvägar. Anpassade vägar kan ibland orsaka kommunikation misslyckas. Testa routning från en virtuell dator med [az nätverk watcher visa-next-hop](/cli/azure/network/watcher?view=azure-cli-latest#az-network-watcher-show-next-hop) att avgöra routning nexthop när trafiken är avsedda för en specifik adress.

Testa utgående kommunikation från den virtuella datorn till en IP-adresser för www.bing.com:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 13.107.21.200 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

Efter några sekunder utdata informerar som den **nextHopType** är **Internet**, och att den **routeTableId** är **Systemväg**. Det här resultatet kan du vet att det är en giltig väg till målet.

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

Utdatan informerar som **ingen** är den **nextHopType**, och att den **routeTableId** är också **Systemväg**. Det här resultatet kan du vet att det finns en giltig väg till målet, men det finns ingen nästa hopp för att vidarebefordra trafiken till målet.

## <a name="view-details-of-a-route"></a>Visa information om en väg

Granska för att analysera routning mer effektiva vägarna för nätverksgränssnitt med den [az nätverk nic visa-gällande--routningstabellen](/cli/azure/network/nic#az-network-nic-show-effective-route-table) kommando:

```azurecli-interactive
az network nic show-effective-route-table \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

Följande ingår i den returnerade utdatan:

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

När du har använt den `az network watcher show-next-hop` kommando för att testa utgående kommunikation till 13.107.21.200 i [Använd nästa hopp](#use-next-hop), vägen med den **addressPrefix** 0.0.0.0/0** användes för att dirigera trafik till adressen eftersom Ingen väg i utdata innehåller adressen. Som standard dirigeras alla adresser som angetts i adressprefixet för en annan väg till internet.

När du har använt den `az network watcher show-next-hop` kommandot för att testa utgående kommunikation till 172.31.0.100 men, resultatet känner att det inte fanns några nästa hopptyp. I den returnerade utdatan visas också följande text:

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

Som du ser i utdata från den `az network watcher nic show-effective-route-table` kommandot, men det finns en standardväg till 172.16.0.0/12 prefix, som innehåller 172.31.0.100 adress, den **nextHopType** är **ingen**. Azure skapar en standardväg till 172.16.0.0/12, men anger inte en nästa hopptyp tills det finns en orsak till. Om till exempel att du lagt till adressintervallet 172.16.0.0/12 adressutrymmet för det virtuella nätverket, Azure ändras den **nextHopType** till **för virtuella nätverk** för vägen. En kontroll kan sedan visa **för virtuella nätverk** som den **nextHopType**.

## <a name="clean-up-resources"></a>Rensa resurser

När resurserna inte behövs längre kan du använda [az group delete](/cli/azure/group#az_group_delete) för att ta bort resursgruppen och alla relaterade resurser den innehåller:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat en virtuell dator och diagnostiserats nätverksroutning från den virtuella datorn. Du har lärt dig att Azure skapar flera standardvägar och testat routning till två olika mål. Lär dig mer om [routning i Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) och hur du [skapa anpassade vägar](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

För utgående anslutningar av virtuell dator, du kan också bestämma svarstiden och för tillåten respektive nekad nätverkstrafiken mellan den virtuella datorn och en slutpunkt som använder nätverket Watcher [anslutning felsöka](network-watcher-connectivity-cli.md) kapaciteten. Du kan övervaka kommunikation mellan en virtuell dator och en slutpunkt som en IP-adress eller URL, med tiden med Nätverksbevakaren anslutning övervakaren funktionen. Mer information finns i avsnittet [övervaka en nätverksanslutning](connection-monitor.md).