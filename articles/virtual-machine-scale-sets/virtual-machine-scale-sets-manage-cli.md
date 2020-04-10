---
title: Hantera skalningsuppsättningar för virtuella datorer med Azure CLI
description: Vanliga Azure CLI-kommandon för att hantera skalningsuppsättningar för virtuella datorer, till exempel hur du startar och stoppar en instans, eller ändrar skalningsuppsättningskapaciteten.
author: ju-shim
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: jushiman
ms.openlocfilehash: ad07a237dd40d836705b332c6c10356720901481
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011590"
---
# <a name="manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>Hantera en skalningsuppsättning för virtuella datorer med Azure CLI
Under livscykeln för en VM-skalningsuppsättning kan du behöva köra en eller flera hanteringsuppgifter. Dessutom kanske du vill skapa skript som automatiserar olika livscykeluppgifter. I den här artikeln beskrivs några av de vanliga Azure CLI-kommandon som gör att du kan utföra dessa uppgifter.

För att slutföra dessa hanteringsuppgifter behöver du den senaste Azure CLI. Mer information finns [i Installera Azure CLI](/cli/azure/install-azure-cli). Om du behöver skapa en skalningsuppsättning för virtuella datorer kan du [skapa en skalningsuppsättning med Azure CLI](quick-create-cli.md).


## <a name="view-information-about-a-scale-set"></a>Visa information om en skalningsuppsättning
Om du vill visa den övergripande informationen om en skalningsuppsättning använder du [az vmss show](/cli/azure/vmss). I följande exempel får du information om skalningsuppsättningen *myScaleSet* i resursgruppen *myResourceGroup.* Ange dina egna namn på följande sätt:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
```


## <a name="view-vms-in-a-scale-set"></a>Visa virtuella datorer i en skalningsuppsättning
Om du vill visa en lista över VM-instans i en skalningsuppsättning använder du [az vmss list-instanser](/cli/azure/vmss). I följande exempel visas alla VM-instanser i skalningsuppsättningen *myScaleSet* i resursgruppen *myResourceGroup.* Ange dina egna värden för dessa namn:

```azurecli
az vmss list-instances \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --output table
```

Om du vill visa ytterligare information `--instance-id` om en viss VM-instans lägger du till parametern i [az vmss get-instance-view](/cli/azure/vmss) och anger en instans att visa. I följande exempel visar information om VM-instans *0* i skalningsuppsättningen *myScaleSet* och *myResourceGroup-resursgruppen.* Ange dina egna namn på följande sätt:

```azurecli
az vmss get-instance-view \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --instance-id 0
```


## <a name="list-connection-information-for-vms"></a>Lista anslutningsinformation för virtuella datorer
Om du vill ansluta till de virtuella datorerna i en skalningsuppsättning kan du SSH eller RDP till en tilldelad offentlig IP-adress och portnummer. Som standard läggs NAT-regler (Network Address Translation) till i Azure load balancer som vidarebefordrar fjärranslutningstrafik till varje virtuell dator. Om du vill visa adressen och portarna för att ansluta till VM-instanser i en skalningsuppsättning använder du [az vmss list-instance-connection-info](/cli/azure/vmss). I följande exempel visas anslutningsinformation för VM-instanser i skalningsuppsättningen *myScaleSet* och i resursgruppen *myResourceGroup.* Ange dina egna värden för dessa namn:

```azurecli
az vmss list-instance-connection-info \
    --resource-group myResourceGroup \
    --name myScaleSet
```


## <a name="change-the-capacity-of-a-scale-set"></a>Ändra kapaciteten för en skalningsuppsättning
Föregående kommandon visade information om din skalningsuppsättning och VM-instanser. Om du vill öka eller minska antalet instanser i skalningsuppsättningen kan du ändra kapaciteten. Skalningsuppsättningen skapar eller tar bort det antal virtuella datorer som krävs och konfigurerar sedan de virtuella datorerna för att ta emot programtrafik.

Om du vill se antalet instanser som du för närvarande har i en skalningsuppsättning använder du [az vmss show](/cli/azure/vmss) och frågar efter *sku.capacity*:

```azurecli
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

Du kan sedan manuellt öka eller minska antalet virtuella datorer i skalningsuppsättningen med [az vmss scale](/cli/azure/vmss). I följande exempel anges antalet virtuella datorer i skalan som *5:*

```azurecli
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 5
```

Det tar några minuter att uppdatera kapaciteten för din skalningsuppsättning. Om du minskar kapaciteten för en skalningsuppsättning tas de virtuella datorerna med de högsta instans-ID:na bort först.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Stoppa och starta virtuella datorer i en skalningsuppsättning
Om du vill stoppa en eller flera virtuella datorer i en skalningsuppsättning använder du [az vmss stop](/cli/azure/vmss#az-vmss-stop). Parametern `--instance-ids` låter dig ange en eller flera virtuella datorer att stoppa. Om du inte anger ett instans-ID, stoppas alla virtuella datorer i skalningsuppsättningen. Om du vill stoppa flera virtuella datorer separerar du varje instans-ID med ett blanksteg.

I följande exempel stoppas instans *0* i skalningsuppsättningen *myScaleSet* och resursgruppen *myResourceGroup.* Ange dina egna värderingar enligt följande:

```azurecli
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```

Stoppade virtuella datorer förblir allokerade och fortsätter att ådra sig beräkningsavgifter. Om du i stället vill att de virtuella datorerna ska frigöras och endast ådra sig lagringsavgifter använder du [az vmss deallocate](/cli/azure/vmss). Om du vill frigöra flera virtuella datorer separerar du varje instans-ID med ett blanksteg. Följande exempel stoppar och behandlar instans *0* i skalningsuppsättningen *myScaleSet* och *myResourceGroup-resursgruppen.* Ange dina egna värderingar enligt följande:

```azurecli
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


### <a name="start-vms-in-a-scale-set"></a>Starta virtuella datorer i en skalningsuppsättning
Om du vill starta en eller flera virtuella datorer i en skalningsuppsättning använder du [az vmss start](/cli/azure/vmss). Parametern `--instance-ids` låter dig ange en eller flera virtuella datorer att starta. Om du inte anger ett instans-ID, startas alla virtuella datorer i skalningsuppsättningen. Om du vill starta flera virtuella datorer separerar du varje instans-ID med ett blanksteg.

I följande exempel startar instans *0* i skalningsuppsättningen *myScaleSet* och resursgruppen *myResourceGroup.* Ange dina egna värderingar enligt följande:

```azurecli
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="restart-vms-in-a-scale-set"></a>Starta om virtuella datorer i en skalningsuppsättning
Om du vill starta om en eller flera virtuella datorer i en skalningsuppsättning använder du [omstart av az vmss](/cli/azure/vmss). Parametern `--instance-ids` låter dig ange en eller flera virtuella datorer att starta om. Om du inte anger ett instans-ID, startas alla virtuella datorer i skalningsuppsättningen om. Om du vill starta om flera virtuella datorer separerar du varje instans-ID med ett blanksteg.

I följande exempel startas instans *0* i skalningsuppsättningen *myScaleSet* och resursgruppen *myResourceGroup.* Ange dina egna värderingar enligt följande:

```azurecli
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="remove-vms-from-a-scale-set"></a>Ta bort virtuella datorer från en skaluppsättning
Om du vill ta bort en eller flera virtuella datorer i en skalningsuppsättning använder du [az vmss delete-instances](/cli/azure/vmss). Med `--instance-ids` parametern kan du ange en eller flera virtuella datorer som ska tas bort. Om du anger * för instans-ID tas alla virtuella datorer i skalningsuppsättningen bort. Om du vill ta bort flera virtuella datorer separerar du varje instans-ID med ett blanksteg.

I följande exempel tas instans *0* bort i skalningsuppsättningen *myScaleSet* och resursgruppen *myResourceGroup.* Ange dina egna värderingar enligt följande:

```azurecli
az vmss delete-instances --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="next-steps"></a>Nästa steg
Andra vanliga uppgifter för skalningsuppsättningar är hur [du distribuerar ett program](virtual-machine-scale-sets-deploy-app.md)och [uppgraderar VM-instanser](virtual-machine-scale-sets-upgrade-scale-set.md). Du kan också använda Azure CLI för att [konfigurera regler för automatisk skalning](virtual-machine-scale-sets-autoscale-overview.md).
