---
title: Uppdatera behållargrupp
description: Lär dig hur du uppdaterar behållare som körs i behållargrupperna för Azure-behållarinstanser.
ms.topic: article
ms.date: 09/03/2019
ms.openlocfilehash: f57ebcf050b5563b45f10af57c1721338df88ff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533304"
---
# <a name="update-containers-in-azure-container-instances"></a>Uppdatera containers i Azure Container Instances

Under normal drift av behållarinstanserna kan det vara nödvändigt att uppdatera behållarna som körs i en [behållargrupp](container-instances-container-groups.md). Du kanske till exempel vill uppdatera avbildningsversionen, ändra ett DNS-namn, uppdatera miljövariabler eller uppdatera tillståndet för en behållare vars program har kraschat.

> [!NOTE]
> Det går inte att uppdatera slutna eller borttagna behållargrupper. När en behållargrupp har avslutats (är i ett lyckades eller misslyckat tillstånd) eller har tagits bort, måste gruppen distribueras som ny.

## <a name="update-a-container-group"></a>Uppdatera en behållargrupp

Uppdatera behållarna i en behållargrupp som körs genom att distribuera om en befintlig grupp med minst en modifierad egenskap. När du uppdaterar en behållargrupp startas alla behållare som körs i gruppen om på plats, vanligtvis på samma underliggande behållarvärd.

Distribuera om en befintlig behållargrupp genom att utfärda kommandot skapa (eller använda Azure-portalen) och ange namnet på en befintlig grupp. Ändra minst en giltig egenskap för gruppen när du utfärdar kommandot skapa för att utlösa omdistributionen och lämna de återstående egenskaperna oförändrade (eller fortsätta att använda standardvärden). Alla behållare gruppegenskaper är inte giltiga för omdistribution. Se [Egenskaper som kräver borttagning](#properties-that-require-container-delete) för en lista över egenskaper som inte stöds.

Följande Azure CLI-exempel uppdaterar en behållargrupp med en ny DNS-namnetikett. Eftersom egenskapen DNS-namnetikett för gruppen kan uppdateras, distribueras behållargruppen om och behållarna startas om.

Första distributionen med DNS-namnetikett *myapplication-staging:*

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Uppdatera behållargruppen med en ny DNS-namnetikett, *myapplication*och lämna de återstående egenskaperna oförändrade:

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Uppdatera fördelar

Den främsta fördelen med att uppdatera en befintlig behållargrupp är snabbare distribution. När du distribuerar om en befintlig behållargrupp hämtas dess behållaravbildningslager från de cachelagrade av den tidigare distributionen. I stället för att dra alla bildlager färska från registret som görs med nya distributioner, endast ändrade lager (om någon) dras.

Program som baseras på större behållaravbildningar som Windows Server Core kan se betydande förbättringar i distributionshastigheten när du uppdaterar i stället för att ta bort och distribuera nya.

## <a name="limitations"></a>Begränsningar

Alla egenskaper för en containergrupp stöder inte uppdateringar. Om du vill ändra vissa egenskaper för en behållargrupp måste du först ta bort och sedan distribuera om gruppen. Mer information finns i [Egenskaper som kräver att behållaren tas bort](#properties-that-require-container-delete).

Alla behållare i en behållargrupp startas om när du uppdaterar behållargruppen. Du kan inte utföra en uppdatering eller omstart på plats av en viss behållare i en grupp med flera behållare.

IP-adressen för en behållare ändras vanligtvis inte mellan uppdateringar, men den är inte garanterad att förbli densamma. Så länge behållargruppen distribueras till samma underliggande värd, behåller behållargruppen sin IP-adress. Även om azure-behållarinstanser gör allt för att distribuera till samma värd, finns det vissa Azure-interna händelser som kan orsaka omfördelning till en annan värd. Om du vill minska problemet använder du alltid en DNS-namnetikett för behållarinstanserna.

Det går inte att uppdatera slutna eller borttagna behållargrupper. När en behållargrupp har stoppats (är i tillståndet *Avslutad)* eller har tagits bort distribueras gruppen som ny.

## <a name="properties-that-require-container-delete"></a>Egenskaper som kräver borttagning av behållare

Som tidigare nämnts kan inte alla behållare gruppegenskaper uppdateras. Om du till exempel vill ändra portarna eller starta om principen för en behållare måste du först ta bort behållargruppen och sedan skapa den igen.

Dessa egenskaper kräver borttagning av behållargrupper före omdistribution:

* TYP AV OS
* Processor
* Minne
* Omstartsprincip
* Portar

När du tar bort en behållargrupp och återskapar den är den inte "omdestri del av", utan skapas ny. Alla avbildningslager hämtas färska från registret, inte från de som cachelagras av en tidigare distribution. IP-adressen för behållaren kan också ändras på grund av att distribueras till en annan underliggande värd.

## <a name="next-steps"></a>Nästa steg

Nämns flera gånger i den här artikeln är **behållargruppen**. Varje behållare i Azure Container Instances distribueras i en behållargrupp och behållargrupper kan innehålla mer än en behållare.

[Behållargrupper i Azure Container Instances](container-instances-container-groups.md)

[Distribuera en grupp med flera containrar](container-instances-multi-container-group.md)

[Stoppa eller starta behållare manuellt i Azure Container Instances](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[azure-cli-install]: /cli/azure/install-azure-cli
