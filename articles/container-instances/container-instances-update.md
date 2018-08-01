---
title: Uppdatera behållare i Azure Container Instances
description: Lär dig hur du uppdaterar behållare som körs i dina grupper för Azure Container Instances-behållare.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 08/01/2018
ms.author: marsma
ms.openlocfilehash: cce7befeb7f0f770bf42fcd7926979da97a6a44a
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391874"
---
# <a name="update-containers-in-azure-container-instances"></a>Uppdatera behållare i Azure Container Instances

Under normal drift av dina behållarinstanser kan det vara nödvändigt att uppdatera behållare i en behållargrupp. Du kanske exempelvis vill uppdatera versionsnumret för avbildningen, ändra ett DNS-namn, uppdatera miljövariabler eller uppdatera tillståndet för en behållare som vars program har kraschat.

## <a name="update-a-container-group"></a>Uppdatera en behållargrupp

Uppdatera behållare i en behållargrupp genom att omdistribuera en befintlig grupp med minst en ändrad egenskap. När du uppdaterar en behållargrupp alla behållare som körs i gruppen har startats om på plats.

Distribuera om en befintlig behållargrupp genom att utfärda kommandot för att skapa (eller Använd Azure portal) och ange namnet på en befintlig grupp. Ändra minst en giltig egenskap i gruppen när du utfärdar kommandot create att utlösa omdistributionen. Inte alla egenskaperna för behållargruppen är giltiga för omdistribution. Se [egenskaper som kräver delete](#properties-that-require-delete) en lista över egenskaper som inte stöds.

I följande Azure CLI-exempel uppdaterar en behållargrupp med en ny DNS-namnetikett. Eftersom etikettegenskapen för DNS-namn för gruppen ändras behållargruppen omdistribueras och dess behållare startas om.

Inledande distribution med DNS-Namnetiketten *myapplication mellanlagring*:

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Uppdatera behållargruppen med en ny DNS-Namnetiketten *myapplication*:

```azurecli-interactive
# Update container group (restarts container)
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Uppdatera fördelar

Den främsta fördelen för att uppdatera en befintlig behållargrupp är snabbare distribution. När du distribuerar om en befintlig behållargrupp hämtas dess behållare avbildningslager från de som cachelagras av föregående distributionen. I stället för att hämta alla avbildningslager ny från registret som görs med nya distributioner, hämtas endast ändrade lager (om sådan finns).

Programmen baserat på större behållaravbildningar som Windows Server Core kan se viktig förbättringar i distributionen hastighet när du uppdaterar i stället för ta bort och distribuera nya.

## <a name="limitations"></a>Begränsningar

Inte alla egenskaper för en behållargrupp har stöd för uppdateringar. Om du vill ändra vissa egenskaper för en behållargrupp måste du först ta bort och sedan distribuera om gruppen. Mer information finns i [egenskaper som kräver behållare ta bort](#properties-that-require-container-delete).

Alla behållare i en behållargrupp startas om när du uppdaterar behållargruppen. Du kan inte utföra en uppdatering eller på plats måste starta om en specifik behållare i en grupp med flera behållare.

IP-adressen för en behållare vanligtvis ändras inte mellan uppdateringar, men det har inte säkert förblir oförändrade. Så länge behållargruppen har distribuerats till samma underliggande värd, behåller sin IP-adress i behållargruppen. Även om det är sällsynt, och även om Azure Container Instances gör allt för att distribuera om till samma värd, finns det vissa Azure-interna händelser som kan orsaka omdistribution till en annan värd. För att minimera detta problem genom att alltid använda en DNS-Namnetiketten för container instances.

Avslutade eller borttagna behållargrupper kan inte uppdateras. När en behållargrupp har stoppats (finns i den *Uppsagd* tillstånd) eller har tagits bort, gruppen har distribuerats när nya.

## <a name="properties-that-require-container-delete"></a>Egenskaper som kräver container delete

Som tidigare nämnts kan du uppdatera alla egenskaperna för behållargruppen. Till exempel för att ändra portarna eller starta om princip för en behållare, måste du först ta bort behållargruppen och sedan skapa det igen.

De här egenskaperna kräver borttagning av behållare innan omdistribution:

* OS-typ
* Processor
* Minne
* Starta om princip
* Portar

När du tar bort en behållargrupp och återskapa den, har det inte ”omdistribueras”, men skapas nya. Alla avbildningslager hämtas ny från registret, inte från cachelagrade för en tidigare distribution. IP-adressen för behållaren kan också ändras på grund av att distribueras till en annan underliggande värd.

## <a name="next-steps"></a>Nästa steg

Nämns flera gånger i den här artikeln är den **behållargruppen**. Varje behållare i Azure Container Instances är distribuerat i en behållargrupp och behållargrupper kan innehålla flera behållare.

[Behållargrupper i Azure Container Instances](container-instances-container-groups.md)

[Distribuera en grupp med flera behållare](container-instances-multi-container-group.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az_container_logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show
[azure-cli-install]: /cli/azure/install-azure-cli
