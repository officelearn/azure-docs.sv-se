---
title: Uppgradera ett klassiska Azure-behållarregister
description: Dra nytta av den utökade funktionsuppsättningen i Basic, Standard och Premium managed behållarregister genom att uppgradera behållarregistret ohanterade klassisk.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 08/28/2018
ms.author: danlep
ms.openlocfilehash: fd22677c2acc9e61abc082b3515a817c01c163cb
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48855977"
---
# <a name="upgrade-a-classic-container-registry"></a>Uppgradera ett klassiskt container register

Azure Container Registry (ACR) är tillgänglig i flera servicenivåer, [kallas SKU: er](container-registry-skus.md). Den första versionen av ACR erbjuds en enda SKU klassisk som saknar flera funktioner som ingår i Basic, Standard och Premium-SKU: er (benämns gemensamt kallas *hanteras* register).

Klassiska SKU: N är inaktuell och kommer att vara tillgänglig efter mars 2019. Den här artikeln beskriver hur du migrerar dina ohanterade klassiskt register till någon av de hantera SKU: erna så att du kan dra nytta av sina förbättrad funktionsuppsättning.

## <a name="why-upgrade"></a>Varför uppgradera?

Klassisk registret SKU håller **inaktuell**, och är inte tillgängliga från **mars 2019**. Alla befintliga klassiska register bör uppgraderas före mars 2019.

På grund av planerade utfasning och begränsade möjligheter för klassisk ohanterade register vara alla klassiskt register uppgraderade till grundläggande, Standard eller Premium hanterade register. Dessa på högre nivå SKU: er integrera molnmiljön registret i funktionerna i Azure.

Hanterade register innehåller:

* Azure Active Directory-integrering för [enskilda inloggning](container-registry-authentication.md#individual-login-with-azure-ad)
* Stöd för borttagning av bild och tagg
* [Geo-replikering](container-registry-geo-replication.md)
* [Webhooks](container-registry-webhook.md)

Klassiskt register beror på det lagringskonto som Azure automatiskt etablerar i din Azure-prenumeration när du skapar i registret. Däremot Basic, Standard och Premium-SKU: er dra nytta av Azures [avancerade lagringsfunktioner](container-registry-storage.md) genom att transparent hantera lagring av dina avbildningar för dig. Ett separat lagringskonto skapas inte i din egen prenumeration.

Hanterat register storage ger följande fördelar:

* Behållaravbildningar [krypterat i vila](container-registry-storage.md#encryption-at-rest).
* Bilder lagras med [geo-redundant lagring](container-registry-storage.md#geo-redundant-storage), tillåta säkerhetskopiering av dina avbildningar med replikering av flera regioner.
* Möjligheten att fritt [flytta mellan SKU: er](container-registry-skus.md#changing-skus), aktiverar högre dataflöde när du väljer en högre nivå SKU. Med varje SKU uppfyller ACR dina behov för dataflöde när behoven ökar.
* Enhetlig säkerhetsmodell för registret och dess lagring ger förenklad rights management. Du hanterar behörigheter endast för behållarregistret, utan att även hantera behörigheter för ett separat lagringskonto.

Ytterligare information om bildlagring i ACR finns i [bildlagring för behållare i Azure Container Registry](container-registry-storage.md).

## <a name="migration-considerations"></a>Överväganden vid migrering

När du ändrar ett klassiskt register till ett hanterat register måste Azure kopiera alla befintliga avbildningar från ACR-skapade lagringskontot i din prenumeration till ett lagringskonto som hanteras av Azure. Den här processen kan ta några minuter till flera timmar beroende på storleken på registret.

Under konverteringen, alla `docker push` åtgärder blockeras medan `docker pull` fortsätter att fungera.

Inte ta bort eller ändra innehållet i det lagringskonto som backar upp klassiskt register under konverteringen. Detta kan resultera i skadade av dina behållaravbildningar.

När migreringen är klar, används längre storage-konto i din prenumeration ursprungligen uppbackning klassiskt register av ACR. När du har kontrollerat att migreringen lyckades, Överväg att ta bort lagringskontot för att minimera kostnader.

>[!IMPORTANT]
> Uppgradera från klassiskt läge till en av de hanterade SKU: er är en **envägsprocess**. När du har konverterat en klassisk registret till Basic, Standard och Premium, kan du inte återgå till klassisk. Du kan dock fritt flytta mellan hanterade SKU: er med tillräckligt med kapacitet för ditt register.

## <a name="how-to-upgrade"></a>Så här uppgraderar du

Du kan uppgradera en ohanterad klassiskt register till en av de hanterade SKU: er på flera olika sätt. I följande avsnitt beskrivs processen för att använda den [Azure CLI] [ azure-cli] och [Azure-portalen][azure-portal].

## <a name="upgrade-in-azure-cli"></a>Uppgradera i Azure CLI

Om du vill uppgradera ett klassiskt register i Azure CLI, köra den [az acr update] [ az-acr-update] kommandot och ange den nya SKU: N för registret. I följande exempel visas ett klassiskt register med namnet *myclassicregistry* uppgraderas till Premium-SKU:

```azurecli-interactive
az acr update --name myclassicregistry --sku Premium
```

När migreringen är klar, bör du se utdata som liknar följande. Observera att den `sku` är ”Premium” och `storageAccount` är ”null” som anger att Azure nu hanterar bildlagring för det här registret.

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

Om du anger ett hanterat register SKU vars maximal kapacitet är mindre än storleken på registret klassisk får ett felmeddelande som liknar följande.

`Cannot update the registry SKU due to reason: Registry size 12936251113 bytes exceeds the quota value 10737418240 bytes for SKU Basic. The suggested SKU is Standard.`

Om du får ett liknande fel, kör den [az acr update] [ az-acr-update] -kommandot på nytt och ange den föreslagna SKU som är närmast på SKU: N som kan hantera dina avbildningar.

## <a name="upgrade-in-azure-portal"></a>Uppgradera Azure-portalen

När du uppgraderar ett klassiskt register med hjälp av Azure-portalen, väljer den lägsta nivå SKU som kan hantera dina avbildningar i Azure. Till exempel om ditt register innehåller 12 GiB i bilder, Azure väljs automatiskt och konverterar klassiskt register till Standard (100 GiB maximalt).

Om du vill uppgradera klassiskt register med hjälp av Azure-portalen, gå till behållarregistret **översikt** och välj **uppgradera till hanterat register**.

![Klassiska registret uppgradera knappen i Azure-portalens användargränssnitt][update-classic-01-upgrade]

Välj **OK** att bekräfta att du vill uppgradera till ett hanterat register.

![Klassiska registret uppgradera bekräftelse i Azure-portalens användargränssnitt][update-classic-02-confirm]

Under migreringen, portalen meddelar som registrets **Etableringsstatus** är *uppdaterar*. Som tidigare nämnts, `docker push` åtgärder är inaktiverade under migreringen, och du inte ta bort eller uppdatera lagringskontot som används av klassiskt register medan migreringen pågår – gör det kan resultera i skadade för avbildningen.

![Klassiska registret Uppgraderingsförlopp i Azure-portalens användargränssnitt][update-classic-03-updating]

När migreringen är klar, den **Etableringsstatus** anger *lyckades*, och du kan återigen `docker push` till registret.

![Klassiska registret uppgradera slutförande tillstånd i Azure-portalens användargränssnitt][update-classic-04-updated]

## <a name="next-steps"></a>Nästa steg

När du har uppgraderat en klassisk registret till Basic, Standard och Premium, använder Azure inte längre storage-konto som ursprungligen säkerhetskopieras klassiskt register. Ta bort lagringskontot eller Blob-behållare i kontot som innehåller dina gamla behållaravbildningar för att minska kostnaderna.

<!-- IMAGES -->
[update-classic-01-upgrade]: ./media/container-registry-upgrade\update-classic-01-upgrade.png
[update-classic-02-confirm]: ./media/container-registry-upgrade\update-classic-02-confirm.png
[update-classic-03-updating]: ./media/container-registry-upgrade\update-classic-03-updating.png
[update-classic-04-updated]: ./media/container-registry-upgrade\update-classic-04-updated.png

<!-- LINKS - internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[azure-cli]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com