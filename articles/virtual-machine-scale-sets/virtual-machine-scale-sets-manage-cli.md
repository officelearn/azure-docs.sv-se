---
title: Hantera VM-Skalningsuppsättningar med Azure CLI | Microsoft Docs
description: Vanliga Azure CLI-kommandon för att hantera Virtual Machine Scale Sets, till exempel att starta och stoppa en instans eller ändra skala Ange kapacitet.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: cynthn
ms.openlocfilehash: 014e6ff2bc769675e47743cfdd4fbf7fc6234974
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46999842"
---
# <a name="manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>Hantera en VM-skalningsuppsättning med Azure CLI
Under livscykeln för en VM-skalningsuppsättning kan du behöva köra en eller flera hanteringsuppgifter. Dessutom kanske du vill skapa skript som automatiserar olika livscykeluppgifter. Den här artikeln beskriver några av de vanliga Azure CLI-kommandon som kan du utföra följande uppgifter.

Om du vill utföra dessa hanteringsuppgifter, måste den senaste Azure CLI. Mer information finns i [installera Azure CLI](/cli/azure/install-azure-cli). Om du vill skapa en skalningsuppsättning för virtuell dator kan du [skapa en skalningsuppsättning med Azure CLI](quick-create-cli.md).


## <a name="view-information-about-a-scale-set"></a>Visa information om en skalningsuppsättning
Du kan visa den övergripande informationen om en skalningsuppsättning [az vmss show](/cli/azure/vmss#az_vmss_show). I följande exempel hämtar information om skalningsuppsättningen *myScaleSet* i den *myResourceGroup* resursgrupp. Ange egna namn enligt följande:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
```


## <a name="view-vms-in-a-scale-set"></a>Visa virtuella datorer i en skalningsuppsättning
Du kan visa en lista över VM-instans i en skalningsuppsättning [az vmss list-instances](/cli/azure/vmss#list-instances). I följande exempel visar en lista över alla Virtuella datorinstanser i skalningsuppsättningen *myScaleSet* i den *myResourceGroup* resursgrupp. Ange egna värden för dessa namn:

```azurecli
az vmss list-instances \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --output table
```

Om du vill visa mer information om en specifik VM-instans, lägger du till den `--instance-id` parameter [az vmss get-instance-view](/cli/azure/vmss#get-instance-view) och anger en instans för att visa. I följande exempel visar information om den Virtuella datorinstansen *0* i skalningsuppsättningen *myScaleSet* och *myResourceGroup* resursgrupp. Ange egna namn enligt följande:

```azurecli
az vmss get-instance-view \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --instance-id 0
```


## <a name="list-connection-information-for-vms"></a>Lista anslutningsinformation för virtuella datorer
För att ansluta till de virtuella datorerna i en skaluppsättning du SSH eller RDP till en tilldelade offentliga IP-adress och portnummer. Som standard läggs regler för network address translation (NAT) till Azure-belastningsutjämnaren som vidarebefordrar trafik för anslutning till varje virtuell dator. Om du vill visa adressen och portarna för att ansluta till VM-instanser i en skalningsuppsättning, använder [az vmss list-instans-connection-info](/cli/azure/vmss#list-instance-connection-info). I följande exempel visas anslutningsinformationen för VM-instanser i skalningsuppsättningen *myScaleSet* och i den *myResourceGroup* resursgrupp. Ange egna värden för dessa namn:

```azurecli
az vmss list-instance-connection-info \
    --resource-group myResourceGroup \
    --name myScaleSet
```


## <a name="change-the-capacity-of-a-scale-set"></a>Ändra kapaciteten för en skalningsuppsättning
I föregående kommandon som visar information om din skalningsuppsättning och VM-instanser. Om du vill öka eller minska antalet instanser i skalningsuppsättningen, kan du ändra kapaciteten. Skalningsuppsättningen skapar eller tar bort det begärda antalet virtuella datorer och sedan konfigurerar de virtuella datorerna för att ta emot trafik.

Om du vill se antalet instanser som du för närvarande har i en skalningsuppsättning använder du [az vmss show](/cli/azure/vmss#az_vmss_show) och frågar efter *sku.capacity*:

```azurecli
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

Du kan sedan manuellt öka eller minska antalet virtuella datorer i skalningsuppsättningen med [az vmss scale](/cli/azure/vmss#az_vmss_scale). I följande exempel anger hur många virtuella datorer i din skalningsuppsättning till *5*:

```azurecli
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 5
```

Det tar några minuter att uppdatera kapaciteten för din skalningsuppsättning. Om du minskar kapaciteten för en skala ange de virtuella datorerna med den högsta instans-ID tas bort först.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Stoppa och starta virtuella datorer i en skalningsuppsättning
Om du vill stoppa en eller flera virtuella datorer i en skalningsuppsättning, använder [az vmss stop](/cli/azure/vmss#az-vmss-stop). Parametern `--instance-ids` låter dig ange en eller flera virtuella datorer att stoppa. Om du inte anger ett instans-ID, stoppas alla virtuella datorer i skalningsuppsättningen. Stoppa flera virtuella datorer genom att avgränsa varje instans-ID med ett blanksteg.

I följande exempel stoppar instansen *0* i skalningsuppsättningen *myScaleSet* och *myResourceGroup* resursgrupp. Ange egna värden enligt följande:

```azurecli
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```

Stoppade virtuella datorer är fortfarande allokerade och fortsätter att kosta beräkningsavgifter. Om du istället vill att de virtuella datorerna ska frigöras och endast kosta lagringsavgifter, använder [az vmss deallocate](/cli/azure/vmss#az_vmss_deallocate). Avgränsa varje instans-ID ska frigöras flera virtuella datorer med ett blanksteg. I följande exempel stoppar och frigör instansen *0* i skalningsuppsättningen *myScaleSet* och *myResourceGroup* resursgrupp. Ange egna värden enligt följande:

```azurecli
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


### <a name="start-vms-in-a-scale-set"></a>Starta virtuella datorer i en skalningsuppsättning
Starta en eller flera virtuella datorer i en skalningsuppsättning med [az vmss start](/cli/azure/vmss#az_vmss_start). Parametern `--instance-ids` låter dig ange en eller flera virtuella datorer att starta. Om du inte anger ett instans-ID, startas alla virtuella datorer i skalningsuppsättningen. Starta flera virtuella datorer genom att avgränsa varje instans-ID med ett blanksteg.

Följande exempel startar instansen *0* i skalningsuppsättningen *myScaleSet* och *myResourceGroup* resursgrupp. Ange egna värden enligt följande:

```azurecli
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="restart-vms-in-a-scale-set"></a>Starta om virtuella datorer i en skalningsuppsättning
Om du vill starta om en eller flera virtuella datorer i en skalningsuppsättning, använder [az vmss starta om](/cli/azure/vmss#az_vmss_restart). Parametern `--instance-ids` låter dig ange en eller flera virtuella datorer att starta om. Om du inte anger ett instans-ID, startas alla virtuella datorer i skalningsuppsättningen om. Avgränsa varje instans-ID för att starta om flera virtuella datorer med ett blanksteg.

I följande exempel startar om instansen *0* i skalningsuppsättningen *myScaleSet* och *myResourceGroup* resursgrupp. Ange egna värden enligt följande:

```azurecli
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="remove-vms-from-a-scale-set"></a>Ta bort virtuella datorer från en skalningsuppsättning
Ta bort en eller flera virtuella datorer i en skalningsuppsättning med [az vmss delete-instances](/cli/azure/vmss#delete-instances). Den `--instance-ids` parametern kan du ange en eller flera virtuella datorer att ta bort. Om du anger * för instansen-ID, alla virtuella datorer i skalningsuppsättningen tas bort. Ta bort flera virtuella datorer genom att avgränsa varje instans-ID med ett blanksteg.

I följande exempel tar bort instansen *0* i skalningsuppsättningen *myScaleSet* och *myResourceGroup* resursgrupp. Ange egna värden enligt följande:

```azurecli
az vmss delete-instances --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="next-steps"></a>Nästa steg
Andra vanliga aktiviteter för scale sets omfattar så [distribuera ett program](virtual-machine-scale-sets-deploy-app.md), och [uppgradera VM-instanser](virtual-machine-scale-sets-upgrade-scale-set.md). Du kan också använda Azure CLI för att [konfigurera regler för automatisk skalning](virtual-machine-scale-sets-autoscale-overview.md).
