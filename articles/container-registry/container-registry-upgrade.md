---
title: "Uppgradera ett register för klassiska Azure-behållare"
description: "Dra nytta av den expanderade funktionsuppsättningen Basic, Standard och Premium hanteras behållaren register genom att uppgradera ohanterade klassisk behållaren registret."
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 12/20/2017
ms.author: marsma
ms.openlocfilehash: 19090bb69d7165c1e904450dc93b925e23e44782
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="upgrade-a-classic-container-registry"></a>Uppgradera ett klassiskt behållaren register

Azure Container registret (ACR) är tillgänglig i flera servicenivåer, [kallas SKU: er](container-registry-skus.md). Den första versionen av ACR erbjuds en enda SKU klassisk som saknar flera funktioner som ingår i Basic, Standard och Premium-SKU: er (kallas gemensamt *hanteras* register). Den här artikeln beskrivs hur du migrerar ohanterade klassisk registret till en hanterad SKU: er så att du kan dra nytta av de förbättrade funktionerna.

## <a name="why-upgrade"></a>Varför uppgradera?

På grund av begränsade möjligheter för klassisk ohanterad register rekommenderar vi alla klassiska register är uppgraderade till Basic, Standard eller Premium hanterade register. Dessa på högre nivå SKU: er integrera djupare registret funktionerna i Azure.

Hanterade register innehåller:

* Azure Active Directory-integrering för [enskilda inloggning](container-registry-authentication.md#individual-login-with-azure-ad)
* Stöd för avbildningen och tagg borttagning
* [Geo-replikering](container-registry-geo-replication.md)
* [Webhooks](container-registry-webhook.md)

De flesta av alla, beror ett klassiskt register på lagringskontot att Azure etablerade i Azure-prenumerationen automatiskt när du skapade i registret. Däremot Basic, Standard och Premium-SKU: er dra nytta av *hanteras lagring*. Det vill säga Azure transparent hanterar lagring på bilderna du--skapas inte ett separat lagringskonto i din egen prenumeration.

Hanterade registret lagring ger följande fördelar:

* Behållaren bilderna [krypterat i vila](../storage/common/storage-service-encryption.md).
* Avbildningar lagras med [geo-redundant lagring](../storage/common/storage-redundancy.md#geo-redundant-storage), tillåta säkerhetskopiering av dina bilder med flera regioner replikering.
* Möjlighet att fritt [flytta mellan SKU: er](container-registry-skus.md#changing-skus), aktivera högre kapacitet när du väljer en högre nivå SKU. Med varje SKU uppfyller ACR dina behov för genomströmning som dina behov öka.
* Enhetlig säkerhetsmodell för registret och dess lagring ger förenklad rights management. Du kan hantera bara behörigheter för behållaren registret, utan att även hantera behörigheter för ett separat lagringskonto.

## <a name="migration-considerations"></a>Överväganden vid migrering

När du ändrar ett klassiskt register till en hanterad registret måste Azure kopiera alla befintliga behållaren bilder från ACR skapade lagringskontot i din prenumeration till ett lagringskonto som hanteras av Azure. Beroende på storleken på registret kan kan den här processen ta några minuter till flera timmar.

Under konverteringen, alla `docker push` åtgärder blockeras medan `docker pull` fortsätter att fungera.

Inte ta bort eller ändra innehållet i lagringskontot säkerhetskopiering klassisk registret under konverteringen. Det kan resultera i fel i behållaren-avbildningar.

När migreringen är klar, används storage-konto i din prenumeration som ursprungligen säkerhetskopierades från registret klassisk längre av ACR. När du har verifierat att migreringen lyckades, Överväg att ta bort lagringskontot för att minimera kostnaderna.

>[!IMPORTANT]
> Uppgradering från klassiska till en hanterad SKU: er är en **envägsprocess**. När du har konverterat en klassisk registret till Basic, Standard och Premium, kan du återgå till klassiska. Du kan dock fritt flytta mellan hanterade SKU: er med tillräckligt med kapacitet för registret.

## <a name="how-to-upgrade"></a>Så här uppgraderar du

Du kan uppgradera en ohanterad klassisk registret till en hanterad SKU: er på flera sätt. I följande avsnitt beskrivs processen för att använda den [Azure CLI] [ azure-cli] och [Azure-portalen][azure-portal].

## <a name="upgrade-in-azure-cli"></a>Uppgradera i Azure CLI

Om du vill uppgradera en klassisk registret i Azure CLI kör den [az acr uppdatering] [ az-acr-update] kommando och ange den nya SKU: N för registret. I följande exempel visas ett klassiskt register med namnet *myclassicregistry* uppgraderas till Premium-SKU:

```azurecli-interactive
az acr update --name myclassicregistry --sku Premium
```

När migreringen är klar bör du se utdata som liknar följande. Observera att den `sku` är ”Premium- och `storageAccount` är” null ”, som anger att nu hanteras av Azure bildlagring för den här registernyckeln.

```JSON
{
  "adminUserEnabled": false,
  "creationDate": "2017-12-12T21:23:29.300547+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "location": "eastus",
  "loginServer": "myregistry.azurecr.io",
  "name": "myregistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "name": "Premium",
    "tier": "Premium"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Om du anger ett hanterade register SKU vars maximala kapacitet är mindre än storleken på klassisk registret, får du ett felmeddelande som liknar följande.

`Cannot update the registry SKU due to reason: Registry size 12936251113 bytes exceeds the quota value 10737418240 bytes for SKU Basic. The suggested SKU is Standard.`

Om du får en liknande fel kan köra den [az acr uppdatering] [ az-acr-update] kommandot på nytt och ange den föreslagna SKU: N som är närmast nivån SKU: N som kan hantera dina bilder.

## <a name="upgrade-in-azure-portal"></a>Uppgradera i Azure-portalen

När du uppgraderar en klassisk registret med hjälp av Azure portal väljer den lägsta nivån SKU som kan hantera dina bilder automatiskt i Azure. Till exempel om registret innehåller 12 GiB i bilder, Azure väljs automatiskt och konverterar klassisk registret-standarden (100 GiB maximalt).

Om du vill uppgradera klassisk registret med hjälp av Azure portal, navigera till behållaren registret **översikt** och välj **uppgradering till hanterade registret**.

![Klassiska registret uppgradera knappen i Azure portal UI][update-classic-01-upgrade]

Välj **OK** att bekräfta att du vill uppgradera till en hanterad registret.

![Klassiska registret uppgradera bekräftelse i Azure portal UI][update-classic-02-confirm]

Under migreringen, portalen indikerar att i registret **Etableringsstatus** är *uppdatering*. Som tidigare nämnts `docker push` har inaktiverats under migreringen, och du inte ta bort eller uppdatering storage-konto som används av klassisk registret medan migreringen pågår--gör det kan resultera i skadade avbildningen.

![Klassiska registret Uppgraderingsförlopp i Azure portal UI][update-classic-03-updating]

När migreringen är klar, den **Etableringsstatus** anger *lyckades*, och du kan igen `docker push` i registret.

![Klassiska registret uppgraderingstillstånd för slutförande i Azure portal UI][update-classic-04-updated]

## <a name="next-steps"></a>Nästa steg

När du har uppgraderat en klassisk registret till Basic, Standard och Premium, används inte längre Azure storage-konto som ursprungligen säkerhetskopierades klassisk registret. För att minska kostnaden, Överväg att ta bort lagringskontot eller Blob-behållaren i kontot som innehåller bilderna gamla behållaren.

<!-- IMAGES -->
[update-classic-01-upgrade]: ./media/container-registry-upgrade\update-classic-01-upgrade.png
[update-classic-02-confirm]: ./media/container-registry-upgrade\update-classic-02-confirm.png
[update-classic-03-updating]: ./media/container-registry-upgrade\update-classic-03-updating.png
[update-classic-04-updated]: ./media/container-registry-upgrade\update-classic-04-updated.png

<!-- LINKS - internal -->
[az-acr-update]: /cli/azure/acr#az_acr_update
[azure-cli]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com