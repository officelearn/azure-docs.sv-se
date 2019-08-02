---
title: Uppgradera ett klassiskt Azure Container Registry
description: Dra nytta av den utökade funktions uppsättningen med register för hanterade behållare för Basic, standard och Premium genom att uppgradera det ohanterade klassiska behållar registret.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 03/26/2019
ms.author: danlep
ms.openlocfilehash: 05c227e7de078c6bb371049f16e191598b9ca4e5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68310382"
---
# <a name="upgrade-a-classic-container-registry"></a>Uppgradera ett klassiskt behållar register

Azure Container Registry (ACR) finns på flera tjänst nivåer, [som kallas SKU: er](container-registry-skus.md). Den första versionen av ACR erbjöd en enda SKU, klassisk, som saknar flera funktioner som ingår i SKU: erna Basic, standard och Premium (gemensamt kallat *hanterade* register).

Den klassiska SKU: n är inaktuell och kommer inte att vara tillgänglig efter 2019 april. Den här artikeln beskriver hur du migrerar det ohanterade klassiska registret till en av de hanterade SKU: erna så att du kan dra nytta av deras förbättrade funktions uppsättning.

## <a name="why-upgrade"></a>Varför uppgradera?

Den klassiska register-SKU:n är inaktuell och kommer inte att vara tillgänglig efter **2019 april**. Alla befintliga klassiska register bör uppgraderas före 2019 april. Portal hanterings funktionerna i klassiska register kommer att avvecklas. Skapandet av nya klassiska register kommer att inaktive ras efter 2019 april.

På grund av planerade inaktuella och begränsade funktioner i klassiska ohanterade register, bör alla klassiska register uppgraderas till hanterade register (Basic, standard eller Premium). Dessa SKU: er på högre nivå integrerar registret i funktionerna i Azure. Mer information om priser och funktioner för de olika tjänst nivåerna finns i [container Registry SKU: er](container-registry-skus.md).

Det klassiska registret är beroende av det lagrings konto som Azure automatiskt etablerar i din Azure-prenumeration när du skapar registret. Som standard kan SKU: er för Basic, standard och Premium dra nytta av Azures [avancerade lagrings funktioner](container-registry-storage.md) genom att transparent hantera lagringen av dina avbildningar åt dig. Ett separat lagrings konto har inte skapats i din egen prenumeration.

Hanterad register lagring ger följande fördelar:

* Behållar avbildningarna är [krypterade i vila](container-registry-storage.md#encryption-at-rest).
* Avbildningar lagras med [Geo-redundant lagring](container-registry-storage.md#geo-redundant-storage), så att du kan säkerhetskopiera dina avbildningar med replikering i flera regioner (endast Premium-SKU).
* Möjlighet att fritt [Flytta mellan SKU: er](container-registry-skus.md#changing-skus), vilket möjliggör högre data flöde när du väljer ett lager på högre nivå. Med varje SKU kan ACR uppfylla dina data flödes krav när dina behov ökar.
* Enhetlig säkerhets modell för registret och dess lagring tillhandahåller förenklad Rights Management. Du hanterar endast behörigheter för behållar registret, utan att även behöva hantera behörigheter för ett separat lagrings konto.

Mer information om avbildnings lagring i ACR finns i [behållar avbildnings lagring i Azure Container Registry](container-registry-storage.md).

## <a name="migration-considerations"></a>Överväganden vid migrering

När du uppgraderar ett klassiskt register till ett hanterat register måste Azure kopiera alla befintliga behållar avbildningar från det ACR lagrings kontot i din prenumeration till ett lagrings konto som hanteras av Azure. Den här processen kan ta några minuter till flera timmar beroende på registrets storlek. I uppskattnings syfte förväntas en migrerings tid på ungefär 0,5 GiB per minut.

Under konverterings processen `docker push` inaktive ras åtgärder under den senaste 10% av migreringen. `docker pull`fortsätter att fungera normalt.

Ta inte bort eller ändra innehållet i lagrings kontot som backar upp det klassiska registret under konverterings processen. Detta kan leda till att behållar avbildningarna skadas.

När migreringen är klar används inte längre det lagrings konto i din prenumeration som ursprungligen säkerhetskopierade det klassiska registret av Azure Container Registry. När du har verifierat att migreringen lyckades kan du överväga att ta bort lagrings kontot för att minimera kostnaderna.

>[!IMPORTANT]
> Uppgradering från klassisk till en av de hanterade SKU: erna är en **enkelriktad process**. När du har konverterat ett klassiskt register till Basic, standard eller Premium kan du inte återgå till klassiskt. Du kan dock fritt flytta mellan hanterade SKU: er med tillräckligt med kapacitet för registret.

## <a name="how-to-upgrade"></a>Så här uppgraderar du

Du kan uppgradera ett ohanterat klassiskt register till en av de hanterade SKU: erna på flera olika sätt. I följande avsnitt beskriver vi processen för att använda [Azure CLI][azure-cli] och [Azure Portal][azure-portal].

## <a name="upgrade-in-azure-cli"></a>Uppgradera i Azure CLI

Om du vill uppgradera ett klassiskt register i Azure CLI kör du kommandot [AZ ACR Update][az-acr-update] och anger den nya SKU: n för registret. I följande exempel uppgraderas ett klassiskt register med namnet *myclassicregistry* till Premium-SKU: n:

```azurecli-interactive
az acr update --name myclassicregistry --sku Premium
```

När migreringen är klar bör du se utdata som liknar följande. Observera att `sku` är "Premium" `storageAccount` och är `null`, vilket indikerar att Azure nu hanterar avbildnings lagringen för registret.

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

Om du anger ett SKU för hanterat register vars högsta kapacitet är mindre än storleken på det klassiska registret, får du ett fel meddelande som liknar följande.

`Cannot update the registry SKU due to reason: Registry size 12936251113 bytes exceeds the quota value 10737418240 bytes for SKU Basic. The suggested SKU is Standard.`

Om du får ett liknande fel kör du kommandot [AZ ACR Update][az-acr-update] igen och anger den föreslagna SKU: n, som är den näst högsta nivån som kan hantera dina avbildningar.

## <a name="upgrade-in-azure-portal"></a>Uppgradera i Azure Portal

När du uppgraderar ett klassiskt register med hjälp av Azure Portal väljer Azure automatiskt antingen standard-eller Premium-SKU: n, beroende på vilket SKU som kan hantera dina avbildningar. Om ditt register till exempel innehåller mindre än 100 GiB i avbildningar, väljer Azure automatiskt och konverterar det klassiska registret till standard (100 GiB max).

Om du vill uppgradera det klassiska registret med hjälp av Azure Portal navigerar du till behållarens register **Översikt** och väljer **Uppgradera till hanterat register**.

![Klassisk register uppgraderings knapp i Azure Portal användar gränssnittet][update-classic-01-upgrade]

Välj **OK** för att bekräfta att du vill uppgradera till ett hanterat register.

Under migreringen anger portalen att registrets etablerings **status** *uppdateras*. Som tidigare `docker push` nämnts inaktive ras åtgärder under den senaste 10% av migreringen. Ta inte bort eller uppdatera lagrings kontot som används av det klassiska registret medan migreringen pågår – detta kan resultera i att avbildningen skadas.

![Den klassiska register uppgraderings förloppet i Azure Portal användar gränssnittet][update-classic-03-updating]

När migreringen är klar visar **etablerings statusen** slutförd ochdu kan återuppta normala åtgärder med ditt register.

![Klassiskt läge för slut för ande av register uppgradering i Azure Portal användar gränssnitt][update-classic-04-updated]

## <a name="next-steps"></a>Nästa steg

När du har uppgraderat ett klassiskt register till ett hanterat register använder Azure inte längre det lagrings konto som ursprungligen säkerhetskopierade det klassiska registret. Överväg att ta bort lagrings kontot eller BLOB-behållaren i kontot som innehåller dina gamla behållar avbildningar för att minska kostnaderna.

<!-- IMAGES -->
[update-classic-01-upgrade]: ./media/container-registry-upgrade/update-classic-01-upgrade.png
[update-classic-02-confirm]: ./media/container-registry-upgrade/update-classic-02-confirm.png
[update-classic-03-updating]: ./media/container-registry-upgrade/update-classic-03-updating.png
[update-classic-04-updated]: ./media/container-registry-upgrade/update-classic-04-updated.png

<!-- LINKS - internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[azure-cli]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com