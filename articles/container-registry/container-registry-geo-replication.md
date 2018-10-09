---
title: GEO-replikera ett Azure container registry
description: Börja skapa och hantera geo-replikerat Azure-behållarregister.
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: overview-article
ms.date: 04/10/2018
ms.author: stevelas
ms.openlocfilehash: 784174c1fb2427441e0ed1a13b147d2440539fa9
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48870346"
---
# <a name="geo-replication-in-azure-container-registry"></a>Geo-replikering i Azure Container Registry

Företag som vill ha en lokal närvaro, eller en frekvent säkerhetskopiering, välja att köra tjänster från flera Azure-regioner. Ett bra tips är kan att placera ett behållarregister i varje region där bilder körs nätverksnära åtgärder, för att aktivera snabb och tillförlitlig bild layer överföringar. GEO-replikering kan en Azure container registry kan fungera som ett enda register som betjänar flera regioner med flera huvudservrar regionala register.

Ett geo-replikerade register ger följande fördelar:

* Den enda register/bildtagg namn kan användas i flera regioner
* Nätverksnära registeråtkomst från regionala distributioner
* Inga ytterligare utgående avgifter som avbildningar hämtas från en lokal, replikerade registret i samma region som din behållarvärd
* Enkel hantering av ett register över flera regioner

## <a name="example-use-case"></a>Exempel användningsfall
Contoso körs en offentlig närvaro webbplats finns i USA, Kanada och Europa. För att leverera dessa marknader med lokal och nätverksnära innehåll, Contoso körs [Azure Container Service](/azure/container-service/kubernetes/) (ACS) Kubernetes-kluster i västra USA, östra USA, Kanada, centrala och Europa, västra. Webbplats-program som distribueras som en Docker-avbildning, använder samma kod och bild i alla regioner. Innehåll, lokala för den regionen hämtas från en databas som har etablerats unikt i varje region. Varje regionala distribution har unika konfigurationen för resurser som den lokala databasen.

Utvecklingsteamet finns i Seattle, WA, använder Datacenter för västra USA.

![Push-överföring till flera register](media/container-registry-geo-replication/before-geo-replicate.png)<br />*Push-överföring till flera register*

Innan du använder funktioner för geo-replikering, hade Contoso ett amerikanska register i västra USA, med en ytterligare register i Västeuropa. Utvecklingsteamet behövde push-överför avbildningar till två olika register för att leverera dessa olika regioner.

```bash
docker push contoso.azurecr.io/public/products/web:1.2
docker push contosowesteu.azurecr.io/public/products/web:1.2
```
![Hämta från flera register](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*Hämta från flera register*

Typiska utmaningarna i att flera register är:

* Östra USA, västra USA och Kanada, centrala klustren alla hämta från registret västra USA, ådrar sig några avgifter för utgående trafik som var och en av värdarna fjärrbehållaren hämta avbildningar från Datacenter för västra USA.
* Utvecklingsteamet måste skicka avbildningar till register för västra USA och Västeuropa.
* Utvecklingsteamet måste konfigurera och underhålla varje regionala distribution med avbildningsnamn som refererar till det lokala registret.
* Registeråtkomst måste konfigureras för varje region.

## <a name="benefits-of-geo-replication"></a>Fördelarna med geo-replikering

![Hämta från en geo-replikerade register](media/container-registry-geo-replication/after-geo-replicate-pull.png)

Funktionen geo-replikering för Azure Container Registry kan realiseras dra nytta av funktionerna:

* Hantera ett enda register över alla regioner: `contoso.azurecr.io`
* Hantera en enda konfiguration av distributioner av avbildningen som används samma bild-URL för alla regioner: `contoso.azurecr.io/public/products/web:1.2`
* Push-Överför till ett enda register, medan ACR hanterar geo-replikering, inklusive regionala webhookar för lokala meddelanden

## <a name="configure-geo-replication"></a>Konfigurera geo-replikering
Konfigurera geo-replikering är lika enkelt som att klicka på regioner på en karta.

GEO-replikering är en funktion i [Premium-register](container-registry-skus.md) endast. Om ditt register inte ännu Premium, du kan ändra från Basic och Standard till Premium i den [Azure-portalen](https://portal.azure.com):

![Växla SKU: er i Azure portal](media/container-registry-skus/update-registry-sku.png)

Om du vill konfigurera geo-replikering för Premium-register, logga in på Azure Portal på http://portal.azure.com.

Navigera till Azure Container Registry och välj **replikeringar**:

![Replikeringar i användargränssnittet i containerregister i Azure-portalen](media/container-registry-geo-replication/registry-services.png)

En karta visas som visar alla aktuella Azure-regioner:

 ![Regionskarta i Azure Portal](media/container-registry-geo-replication/registry-geo-map.png)

* Blå Sexhörningar som representerar aktuell repliker
* Gröna Sexhörningar som representerar möjliga repliken områden
* Grå Sexhörningar som representerar Azure-regioner finns ännu inte för replikering

Om du vill konfigurera en replik, Välj en gröna Sexhörning och välj sedan **skapa**:

 ![Skapa replikerings-UI i Azure-portalen](media/container-registry-geo-replication/create-replication.png)

Om du vill konfigurera ytterligare repliker gröna Sexhörningar för andra regioner och sedan klicka på **skapa**.

ACR börjar synkronisera avbildningar över konfigurerade replikerna. När du är klar visar portalen *redo*. Replikens status på portalen uppdateras inte automatiskt. Använd uppdateringsknappen om du vill visa uppdaterad status.

## <a name="geo-replication-pricing"></a>Priser för GEO-replikering

GEO-replikering är en funktion i den [Premium-SKU](container-registry-skus.md) för Azure Container Registry. När du replikerar ett register till din önskade regioner debiteras avgifter för Premium-register för varje region.

I föregående exempel konsoliderade Contoso två register som ett, att lägga till repliker i östra USA, Kanada, centrala och Europa, västra. Contoso betalar Premium fyra gånger per månad, utan ytterligare konfiguration eller hantering. Varje region tar nu emot deras bilder lokalt, förbättra prestanda, tillförlitlighet utan nätverksöverskridande från västra USA, Kanada och östra USA.

## <a name="summary"></a>Sammanfattning

Med geo-replikering, kan du hantera dina regionala datacenter som ett globalt moln. Eftersom avbildningar används i många Azure-tjänster, du kan dra nytta av en enskild hanteringsplan samtidigt underhålla nätverksnära, snabba och tillförlitliga lokala avbildningen hämtar.

## <a name="next-steps"></a>Nästa steg

Kolla in den här självstudieserien i tre delar [Geo-replikering i Azure Container Registry](container-registry-tutorial-prepare-registry.md). Beskriver hur du skapar en geo-replikerade register, att skapa en behållare och distribuera den med en enda `docker push` kommandot till flera regionala Web Apps for Containers-instanser.

> [!div class="nextstepaction"]
> [GEO-replikering i Azure Container Registry](container-registry-tutorial-prepare-registry.md)
