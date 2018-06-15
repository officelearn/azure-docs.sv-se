---
title: GEO-replikering en Azure-behållaren registret
description: Börja skapa och hantera georeplikerad Azure-behållaren register.
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: overview-article
ms.date: 04/10/2018
ms.author: stevelas
ms.openlocfilehash: e4695428b03961f5e899007609dfb1088dde77a8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33768217"
---
# <a name="geo-replication-in-azure-container-registry"></a>Geo-replikering i Azure Container Registry

Företag som vill lokala förekomst eller en varm säkerhetskopiering, välja att köra tjänster från Azure-regioner. Som bästa praxis kan placera en behållare registret i varje region där avbildningar kör nätverket Stäng åtgärder, aktivera snabb och tillförlitlig bild layer överföringar. GEO-replikering kan en Azure-behållaren registret för att fungera som ett enda register betjäna flera regioner med multimaster regionala register.

Ett geo-replikerade register ger följande fördelar:

* Den enda registret/bildtagg namn kan användas över flera regioner
* Stäng nätverk registeråtkomst från regionala distributioner
* Inga ytterligare utgång avgifter som bilder hämtas från en lokal, replikerad registret i samma region som behållarvärd för
* Enkel hantering av ett register över flera regioner

## <a name="example-use-case"></a>Exempel användningsfall
Contoso körs en offentlig förekomst webbplats finns i USA, Kanada och Europa. Om du vill hantera dessa marknader med lokala och nätverket Stäng innehåll Contoso körs [Azure Container Service](/azure/container-service/kubernetes/) (ACS) Kubernetes-kluster i västra USA, östra USA, Kanada Central och västra Europa. Webbplatsprogrammet distribueras som en Docker-avbildning, använder samma kod och bild över alla regioner. Innehåll, lokala för den regionen hämtas från en databas som har etablerats unikt i varje region. Varje regional distribution har unika konfigurationen för resurser som den lokala databasen.

Utvecklingsteamet finns i Seattle WA, använda västra USA datacenter.

![Push-installation till flera register](media/container-registry-geo-replication/before-geo-replicate.png)<br />*Push-installation till flera register*

Innan du använder geo-replikering funktioner hade Contoso en USA-baserade registret i västra USA med en ytterligare registret i västra Europa. Om du vill hantera dessa olika områden hade Utvecklingsteamet att skicka bilder till två olika register.

```bash
docker push contoso.azurecr.io/pubic/products/web:1.2
docker push contosowesteu.azurecr.io/pubic/products/web:1.2
```
![Dra från flera register](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*Dra från flera register*

Vanliga utmaningar för flera register inkluderar:

* Alla östra USA, västra USA och Kanada Central klustren drar från registret västra USA medför utgång avgifter som var och en av dessa värdar remote behållaren hämtar bilder från USA, västra datacenter.
* Utvecklingsteamet måste skicka bilder till västra USA och Västeuropa register.
* Utvecklingsteamet måste konfigurera och underhålla varje regional distribution med Avbildningsnamnet som refererar till det lokala registret.
* Registret åtkomst måste konfigureras för varje region.

## <a name="benefits-of-geo-replication"></a>Fördelarna med geo-replikering

![Dra ett geo-replikerade register](media/container-registry-geo-replication/after-geo-replicate-pull.png)

Med funktionen geo-replikering i Azure Container registret kan realiseras dessa fördelar:

* Hantera ett enda register över alla regioner: `contoso.azurecr.io`
* Hantera en enda konfiguration av distributioner som alla regioner används samma bild-URL: `contoso.azurecr.io/public/products/web:1.2`
* Push till ett enda register medan ACR hanterar geo-replikering, inklusive regionala webhooks för lokala meddelanden

## <a name="configure-geo-replication"></a>Konfigurera geo-replikering
Konfigurera geo-replikering är lika enkelt som att välja regioner på en karta.

GEO-replikering är en funktion i [Premium register](container-registry-skus.md) endast. Om registret inte ännu Premium, du kan ändra från Basic och Standard till Premium i den [Azure-portalen](https://portal.azure.com):

![Växla SKU: er i Azure-portalen](media/container-registry-skus/update-registry-sku.png)

Om du vill konfigurera geo-replikering för Premium-registret, logga in på Azure-portalen på http://portal.azure.com.

Navigera till din Azure-behållare registret och markera **replikeringar**:

![Replikeringar i användargränssnittet i behållarregister i Azure-portalen](media/container-registry-geo-replication/registry-services.png)

En karta visas med alla aktuella Azure-regioner:

 ![Regionskarta i Azure Portal](media/container-registry-geo-replication/registry-geo-map.png)

* Blå Sexhörningar representerar aktuella repliker
* Grön Sexhörningar representerar möjliga replik områden
* Grå Sexhörningar representerar Azure-regioner ännu inte är tillgänglig för replikering

Om du vill konfigurera en replik, Välj en grön Sexhörning och sedan **skapa**:

 ![Skapa replikerings-UI i Azure-portalen](media/container-registry-geo-replication/create-replication.png)

Om du vill konfigurera ytterligare repliker, Välj grön Sexhörningar för andra regioner, och klicka sedan **skapa**.

ACR börjar synkronisera bilder över de konfigurerade replikerna. När borttagningen är klar portalen visar *klar*. Replikens status i portalen uppdateras inte automatiskt. Använd uppdateringsknappen om du vill visa den uppdaterade statusen.

## <a name="geo-replication-pricing"></a>Priser för GEO-replikering

GEO-replikering är en funktion i den [Premium-SKU](container-registry-skus.md) i registret för Azure-behållare. När du replikerar ett register till din önskade regioner innebära Premium registret avgifter för varje region.

I föregående exempel konsoliderade Contoso två register som ett, lägger till repliker i östra USA och Kanada Central västra Europa. Contoso betalar fyra gånger Premium per månad utan ytterligare konfiguration och hantering. Varje region hämtar nu deras bilder lokalt, vilket förbättrar prestanda, tillförlitlighet utan nätverk utgång avgifter från västra USA Kanada och östra USA.

## <a name="summary"></a>Sammanfattning

Du kan hantera dina regionala datacenter med geo-replikering som en global molnet. Bilder används över flera Azure-tjänster, du kan dra nytta av ett hantering från en enda plan samtidigt nätverket stängning snabb och tillförlitlig lokal image hämtar.

## <a name="next-steps"></a>Nästa steg

Kolla självstudiekursen serien tre delar [Geo-replikering i Azure Container registret](container-registry-tutorial-prepare-registry.md). Gå igenom att skapa ett geo-replikerade register, skapa en behållare och distribuerar den med en enda `docker push` kommandot till flera regionala Web Apps för behållare instanser.

> [!div class="nextstepaction"]
> [GEO-replikering i Azure-behållaren registret](container-registry-tutorial-prepare-registry.md)