---
title: Geo-replikering i ett Azure-containerregister
description: Komma igång med att skapa och hantera geo-replikerade Azure-containerregister.
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: overview
ms.date: 04/10/2018
ms.author: stevelas
ms.openlocfilehash: 2dc314dd1d1e728f03c1d0c660d9339254ddc462
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57541867"
---
# <a name="geo-replication-in-azure-container-registry"></a>Geo-replikering i Azure Container Registry

Företag som vill ha en lokal närvaro, eller en direkt säkerhetskopia, väljer att köra tjänster från flera Azure-regioner. Som bästa praxis kan ett containerregister placeras i varje region där avbildningarna körs för att medge nätverksnära åtgärder med snabba och tillförlitliga avbildningslageröverföringar. Geo-replikering gör att ett Azure-containerregister kan fungera som ett enskilt register som betjänar flera regioner med regionala register med flera original.

Ett geo-replikerat register ger följande fördelar:

* Namn på enskilda register/avbildningar/taggar kan användas i flera regioner
* Nätverksnära registeråtkomst från regionala distributioner
* Inga ytterligare utgående avgifter eftersom avbildningar hämtas från ett lokalt replikerat register i samma region som din containervärd
* Enskild hantering av ett register i flera regioner

> [!NOTE]
> Om du behöver upprätthålla kopior av containeravbildningar i mer än ett Azure-containerregister har Azure Container Registry även stöd för [avbildningsimport](container-registry-import-images.md). Till exempel kan du i ett DevOps-arbetsflöde importera en avbildning från ett utvecklingsregister till ett produktionsregister utan att behöva använda Docker-kommandon.
>

## <a name="example-use-case"></a>Exempel på användningsfall
Contoso kör en webbplats med offentlig närvaro i USA, Kanada och Europa. För att betjäna dessa marknader med lokalt och nätverksnära innehåll kör Contoso [Azure Kubernetes Service](/azure/aks/)-kluster (AKS) i USA, västra; USA, östra; Kanada, centrala samt Europa, västra. Webbappen, som distribueras som en Docker-avbildning, använder samma kod och avbildning i alla regioner. Innehåll, som är lokalt för den regionen, hämtas från en databas som etableras unikt i varje region. Varje regional distribution har en unik konfiguration för resurser som den lokala databasen.

Utvecklingsteamet finns i Seattle, WA, och använder datacentret för USA, västra.

![Push-överföra till flera register](media/container-registry-geo-replication/before-geo-replicate.png)<br />*Push-överföra till flera register*

Innan Contoso började använda de geo-replikerade funktionerna hade de ett USA-baserat register i USA, västra med ytterligare ett register i Europa, västra. För att betjäna dessa olika regioner push-överförde utvecklingsteamet till två olika register.

```bash
docker push contoso.azurecr.io/public/products/web:1.2
docker push contosowesteu.azurecr.io/public/products/web:1.2
```
![Hämta från flera register](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*Hämta från flera register*

Typiska utmaningar med flera register är:

* Klustren för USA, västra; USA, östra och Kanada, centrala hämtar alla från registret USA, västra och ådrar sig utgående avgifter när dessa fjärrcontainervärdar hämtar avbildningar från datacenter för USA, västra.
* Utvecklingsteamet måste push-överföra avbildningar till registren för USA, västra och Europa, västra.
* Utvecklingsteamet måste konfigurera och underhålla varje regional distribution med avbildningsnamn som refererar till det lokala registret.
* Registeråtkomst måste konfigureras för varje region.

## <a name="benefits-of-geo-replication"></a>Fördelar med geo-replikering

![Hämta från ett geo-replikerat register](media/container-registry-geo-replication/after-geo-replicate-pull.png)

Användning av funktionen för geo-replikering i Azure Container Registry ger följande fördelar:

* Hantera ett enskilt register i alla regioner: `contoso.azurecr.io`
* Hantera en enskild konfiguration av avbildningsdistributioner eftersom alla regioner använder samma avbildnings-URL: `contoso.azurecr.io/public/products/web:1.2`
* Push-överför till ett enskilt register medan ACR hanterar geo-replikering, däribland regionala webhooks för lokala meddelanden

## <a name="configure-geo-replication"></a>Konfigurera geo-replikering
Konfiguration av geo-replikering är så enkelt som att klicka på regioner på en karta.

Geo-replikering är en funktion som endast finns i [Premium-register](container-registry-skus.md). Om ditt register ännu inte är Premium kan du ändra från Basic och Standard till Premium i [Azure-portalen](https://portal.azure.com):

![Växla SKU:er i Azure-portalen](media/container-registry-skus/update-registry-sku.png)

För att konfigurera geo-replikering för Premium-registret loggar du in på Azure-portalen på https://portal.azure.com.

Gå till ditt Azure Container Registry och välj **Replikeringar**:

![Replikeringar i användargränssnittet i containerregister i Azure-portalen](media/container-registry-geo-replication/registry-services.png)

En karta visas med alla aktuella Azure-regioner:

 ![Regionskarta i Azure Portal](media/container-registry-geo-replication/registry-geo-map.png)

* Blå sexhörningar representerar aktuella repliker
* Gröna sexhörningar representerar möjliga replikregioner
* Grå sexhörningar representerar Azure-regioner som ännu inte är tillgängliga för replikering

Om du vill konfigurera en replik markerar du en grön sexhörning och väljer **Skapa**:

 ![Skapa replikerings-UI i Azure-portalen](media/container-registry-geo-replication/create-replication.png)

Om du vill konfigurera ytterligare repliker markerar du de gröna sexhörningarna för andra regioner och klickar på **Skapa**.

ACR börjar synkronisera avbildningar mellan de konfigurerade replikerna. När det är klart visar portalen *Klar*. Replikstatus i portalen uppdateras inte automatiskt. Använd uppdateringsknappen om du vill visa uppdaterad status.

## <a name="geo-replication-pricing"></a>Prissättning för Geo-replikering

Geo-replikering är en funktion i [Premium SKU](container-registry-skus.md) för Azure Container Registry. När du replikerar ett register till din önskade regioner debiteras du avgifter för Premium-register för varje region.

I föregående exempel konsoliderade Contoso två register till ett och lade till repliker i USA, östra; Kanada, centrala samt Europa, västra. Contoso betalade då fyra gånger Premium per månad, utan ytterligare konfiguration eller hantering. Varje region hämtar nu sina avbildningar lokalt, vilket förbättrar prestanda och tillförlitlighet utan utgående nätverksavgifter från USA, västra till Kanada och USA, östra.

## <a name="summary"></a>Sammanfattning

Med geo-replikering kan du hantera dina regionala datacenter som ett globalt moln. Eftersom avbildningar används i många Azure-tjänster kan du dra nytta av ett enskilt hanteringsplan och samtidigt underhålla nätverksnära, snabba och tillförlitliga hämtningar av lokala avbildningar.

## <a name="next-steps"></a>Nästa steg

Läs självstudieserien i tre delar, [Geo-replikering i Azure Container Registry](container-registry-tutorial-prepare-registry.md). Gå igenom att skapa ett geo-replikerat register samt skapa en container och sedan distribuera den med ett enda `docker push`-kommando till flera regionala Web Apps for Containers-instanser.

> [!div class="nextstepaction"]
> [Geo-replikering i Azure Container Registry](container-registry-tutorial-prepare-registry.md)
