---
title: Hantera Skalningsuppsättningar i virtuella datorer med Azure CLI 2.0 | Microsoft Docs
description: Vanliga Azure CLI 2.0-kommandon för att hantera Skalningsuppsättningar i virtuella datorer, till exempel att starta och stoppa en instans eller ändra skalan Ange kapacitet.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: ca447f3ca0ed6656912a0d3e5082ebd2dd308a14
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652492"
---
# <a name="manage-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Hantera en virtuell dator-skala med Azure CLI 2.0
Under livscykeln för en VM-skalningsuppsättning kan du behöva köra en eller flera hanteringsuppgifter. Dessutom kanske du vill skapa skript som automatiserar olika livscykeluppgifter. Den här artikeln beskrivs några av de vanliga Azure CLI 2.0-kommandon som gör att du kan utföra dessa uppgifter.

Om du vill utföra dessa hanteringsuppgifter, måste den senaste Azure CLI 2.0. Mer information finns i [installera Azure CLI 2.0](/cli/azure/install-azure-cli). Om du behöver skapa en skaluppsättning för virtuell dator kan du [skapa en skala med Azure CLI 2.0](quick-create-cli.md).


## <a name="view-information-about-a-scale-set"></a>Visa information om en skaluppsättning
Om du vill visa allmän information om en skalningsuppsättning [az vmss visa](/cli/azure/vmss#az_vmss_show). I följande exempel hämtar information om skaluppsättningen namngivna *myScaleSet* i den *myResourceGroup* resursgruppen. Ange egna namn på följande sätt:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
```


## <a name="view-vms-in-a-scale-set"></a>Visa virtuella datorer i en skalningsuppsättning
Du kan visa en lista över VM-instans i en skaluppsättning [az vmss listinstanserna](/cli/azure/vmss#list-instances). I följande exempel visar alla VM-instanser i skaluppsättningen namngivna *myScaleSet* i den *myResourceGroup* resursgruppen. Ange egna värden för dessa namn:

```azurecli
az vmss list-instances \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --output table
```

Om du vill visa mer information om en specifik VM-instans, lägger du till den `--instance-id` parameter till [az vmss get-instans view](/cli/azure/vmss#get-instance-view) och ange en instans för att visa. I följande exempel visar information om VM-instans *0* i skaluppsättningen namngivna *myScaleSet* och *myResourceGroup* resursgruppen. Ange egna namn på följande sätt:

```azurecli
az vmss get-instance-view \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --instance-id 0
```


## <a name="list-connection-information-for-vms"></a>Lista anslutningsinformationen för virtuella datorer
Att ansluta till virtuella datorer i en skaluppsättning du SSH eller RDP till en tilldelad offentliga IP-adress och portnummer. Som standard läggs network address translation (NAT) regler till Azure belastningsutjämnare som vidarebefordrar fjärranslutning trafik på varje virtuell dator. Visa adresser och portar för anslutning till VM-instanser i en skaluppsättning, Använd [az vmss lista-instans--anslutningsinformation](/cli/azure/vmss#list-instance-connection-info). I följande exempel visar anslutningsinformationen för VM-instanser i skaluppsättningen namngivna *myScaleSet* och i den *myResourceGroup* resursgruppen. Ange egna värden för dessa namn:

```azurecli
az vmss list-instance-connection-info \
    --resource-group myResourceGroup \
    --name myScaleSet
```


## <a name="change-the-capacity-of-a-scale-set"></a>Ändra kapaciteten för en skalningsuppsättning
Föregående kommandona visade information om din skaluppsättning och VM-instanser. Du kan ändra kapacitet för att öka eller minska antalet instanser i skaluppsättning. Skaluppsättning skapar eller tar bort antalet virtuella datorer som krävs och sedan konfigurerar de virtuella datorerna för att ta emot trafik för programmet.

Om du vill se antalet instanser som du för närvarande har i en skalningsuppsättning använder du [az vmss show](/cli/azure/vmss#az_vmss_show) och frågar efter *sku.capacity*:

```azurecli
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

Du kan sedan manuellt öka eller minska antalet virtuella datorer i skalningsuppsättningen med [az vmss scale](/cli/azure/vmss#az_vmss_scale). I följande exempel anger hur många virtuella datorer i din skaluppsättningen *5*:

```azurecli
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 5
```

Det tar några minuter att uppdatera kapaciteten för din skalningsuppsättning. Om du minskar kapaciteten för en skala ange de virtuella datorerna med högsta instans-ID tas bort först.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Stoppa och starta virtuella datorer i en skaluppsättning
Om du vill stoppa en eller flera virtuella datorer i en skaluppsättning använder [az vmss stoppa](/cli/azure/vmss/stop). Parametern `--instance-ids` låter dig ange en eller flera virtuella datorer att stoppa. Om du inte anger ett instans-ID, stoppas alla virtuella datorer i skalningsuppsättningen. Avgränsa varje instans-ID om du vill stoppa flera virtuella datorer med ett blanksteg.

Följande exempel stoppar instans *0* i skaluppsättningen namngivna *myScaleSet* och *myResourceGroup* resursgruppen. Ange egna värden på följande sätt:

```azurecli
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```

Stoppade virtuella datorer är allokerade och fortsätter att avgifter för beräkning. Om du istället vill att de virtuella datorerna till att frigöra och endast avgifter lagring använder [az vmss frigöra](/cli/azure/vmss#az_vmss_deallocate). Avgränsa varje instans-ID om du vill frigöra flera virtuella datorer med ett blanksteg. I följande exempel stoppar och tar bort instansen *0* i skaluppsättningen namngivna *myScaleSet* och *myResourceGroup* resursgruppen. Ange egna värden på följande sätt:

```azurecli
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


### <a name="start-vms-in-a-scale-set"></a>Starta virtuella datorer i en skaluppsättning
Starta en eller flera virtuella datorer i en skaluppsättning med [az vmss starta](/cli/azure/vmss#az_vmss_start). Parametern `--instance-ids` låter dig ange en eller flera virtuella datorer att starta. Om du inte anger ett instans-ID, startas alla virtuella datorer i skalningsuppsättningen. Avgränsa varje instans-ID för att starta flera virtuella datorer med ett blanksteg.

Följande exempel startar instans *0* i skaluppsättningen namngivna *myScaleSet* och *myResourceGroup* resursgruppen. Ange egna värden på följande sätt:

```azurecli
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="restart-vms-in-a-scale-set"></a>Starta om virtuella datorer i en skaluppsättning
Om du vill starta om en eller flera virtuella datorer i en skaluppsättning använder [az vmss starta om](/cli/azure/vmss#az_vmss_restart). Parametern `--instance-ids` låter dig ange en eller flera virtuella datorer att starta om. Om du inte anger ett instans-ID, startas alla virtuella datorer i skalningsuppsättningen om. Avgränsa varje instans-ID för att starta om flera virtuella datorer med ett blanksteg.

I följande exempel startar om instansen *0* i skaluppsättningen namngivna *myScaleSet* och *myResourceGroup* resursgruppen. Ange egna värden på följande sätt:

```azurecli
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="remove-vms-from-a-scale-set"></a>Ta bort virtuella datorer från en skaluppsättning
Ta bort en eller flera virtuella datorer i en skaluppsättning med [az vmss delete-instanser](/cli/azure/vmss#delete-instances). Den `--instance-ids` parametern kan du ange en eller flera virtuella datorer ska tas bort. Om du anger * för instansen-ID, alla virtuella datorer i skaluppsättning tas bort. Avgränsa varje instans-ID för att ta bort flera virtuella datorer med ett blanksteg.

I följande exempel tar bort instansen *0* i skaluppsättningen namngivna *myScaleSet* och *myResourceGroup* resursgruppen. Ange egna värden på följande sätt:

```azurecli
az vmss delete-instances --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="next-steps"></a>Nästa steg
Andra vanliga uppgifter för skalningsuppsättningar inkluderar så [distribuera ett program](virtual-machine-scale-sets-deploy-app.md), och [uppgradera VM-instanser](virtual-machine-scale-sets-upgrade-scale-set.md). Du kan också använda Azure CLI för att [konfigurera regler för automatisk skalning](virtual-machine-scale-sets-autoscale-overview.md).
