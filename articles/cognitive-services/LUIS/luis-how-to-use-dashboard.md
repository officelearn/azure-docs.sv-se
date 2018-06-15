---
title: Instrumentpanel för program för THOMAS appar | Microsoft Docs
description: Mer information om instrumentpanelen för program, ett visualiserade reporting verktyg som gör att du kan övervaka dina appar på ett enda ögonblick.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/01/2018
ms.author: v-geberr
ms.openlocfilehash: 4e117aa734c551f76c3602f81e3e2ebc6aa9f337
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "35355779"
---
# <a name="application-dashboard"></a>Instrumentpanel för program
Appinstrumentpanelen kan du övervaka din app på en enda överblick över. Den **instrumentpanelen** visas när du öppnar en app genom att klicka på namnet på programmet på **Mina appar** sidan Välj **instrumentpanelen** från övre panelen. 

> [!CAUTION]
> Om du vill använda de senaste mätvärdena för THOMAS måste du:
> * Använd en THOMAS [slutpunktsnyckel](luis-how-to-azure-subscription.md) skapas i Azure
> * Använd THOMAS slutpunktsnyckel för alla endpoint begäranden inklusive THOMAS [API](https://aka.ms/luis-endpoint-apis) och bot
> * Använd olika endpoint nyckel för varje THOMAS app. Använd inte en enda slutpunkt-nyckel för alla appar. Nyckeln endpoint spåras på nivån nyckel inte på app-nivå.  

Den **instrumentpanelen** sidan ger en översikt över appen THOMAS inklusive den aktuella modellen tillstånd samt [endpoint](luis-glossary.md#endpoint) användning över tid. <!--The following image shows the **Dashboard** page.-->

<!-- TBD: Get a working screen shot
![The Dashboard](./media/luis-how-to-use-dashboard/dashboard.png)
-->

<!-- TBD: IS THIS STILL TRUE?
At the top of the **Dashboard** page, a contextual notification bar constantly displays notifications to update you on the required or recommended actions appropriate for the current state of your app. It also provides useful tips and alerts as needed. A detailed description of the data reported on the **Dashboard** page follows.
-->
  
## <a name="app-status"></a>Appstatus
Instrumentpanelen innehåller programmets utbildning och publicering av status, inklusive datum och tid när appen senast tränas och publiceras.  

![Instrumentpanelen - appens Status](./media/luis-how-to-use-dashboard/app-state.png)

## <a name="model-data-statistics"></a>Data modellstatistik
Instrumentpanelen visar det totala antalet avsikter, enheter och märkt utterances befintliga i appen. 

![AppData statistik](./media/luis-how-to-use-dashboard/app-model-count.png)

## <a name="endpoint-hits"></a>Slutpunkten träffar
Instrumentpanelen visar de totala endpoint träffar som THOMAS appen tar emot och låter dig visa träffar inom en period som du anger. Det totala antalet träffar som visas är summan av slutpunkten träffar som använder en [slutpunktsnyckel](./luis-concept-keys.md#endpoint-key) och slutpunkten träffar som använder en [redigering nyckeln](./luis-concept-keys.md#authoring-key).

<!-- TBD: this image is old but I don't have a new one based on usage -->
![Slutpunkten träffar](./media/luis-how-to-use-dashboard/dashboard-endpointhits.png)

> [!NOTE] 
> Antal träffar i den senaste slutpunkten i Azure portal på THOMAS serviceöversikt. 
 
### <a name="total-endpoint-hits"></a>Totalt antal endpoint träffar
Det totala antalet endpoint träffar som tagits emot i appen och sedan skapa upp till det aktuella datumet.

### <a name="endpoint-hits-per-period"></a>Slutpunkten träffar per period
Antal träffar emot inom en tidigare period visas per dag. Punkter mellan start- och slutdatum representerar dagar som omfattas av i denna period. Muspekaren över varje punkt Se träffarna räkna varje dag under tidsperioden. 

Att välja en tid att visa i diagrammet:
 
1. Klicka på **ytterligare inställningar** ![ytterligare inställningar för](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) komma åt listan punkter. Du kan välja perioder som sträcker sig från en vecka upp till ett år. 

    ![Slutpunkten träffar per Period](./media/luis-how-to-use-dashboard/timerange.png)

2. Välj en punkt i listan och klicka sedan på pilen Bakåt ![Bakåt](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) att visa diagrammet.

### <a name="key-usage"></a>Nyckelanvändning
Antal träffar förbrukad från programnyckel prenumeration. Mer information om prenumerationen nycklar finns [nycklar i THOMAS](luis-concept-keys.md). 
  
## <a name="intent-breakdown"></a>Avsiktshantering uppdelning
Den **avsikt uppdelning** visar en uppdelning av intents baserat på märkt utterances eller slutpunkt träffar. Den här översiktsdiagrammet visar varje avsikt relativa betydelse i appen. När du håller muspekaren över ett segment visas avsiktshantering namn och procentandelen representerar det totala antalet träffar märkt utterances-slutpunkten. 

![Avsiktshantering uppdelning](./media/luis-how-to-use-dashboard/intent-breakdown.png)

Kontrollera om en analys på detaljnivå baseras på märkt utterances eller slutpunkt träffar:

1. Klicka på **ytterligare inställningar** ![ytterligare inställningar för](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) komma åt listan enligt följande bild:

    ![Avsiktshantering uppdelning lista](./media/luis-how-to-use-dashboard/intent-breakdown-based-on.png)
2. Välj ett värde i listan och klicka sedan på pilen Bakåt ![Bakåt](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) att visa diagrammet.

## <a name="entity-breakdown"></a>Entiteten uppdelning
Instrumentpanelen visar en uppdelning av enheter baserat på märkt utterances eller slutpunkt träffar. Den här översiktsdiagrammet visar en avvägning mellan kraven för varje entitet i appen. När du håller muspekaren över ett segment visas entitetsnamnet och procent i märkt utterances eller slutpunktens träffar. 

![Entiteten uppdelning](./media/luis-how-to-use-dashboard/entity-breakdown.png)

Kontrollera om en analys på detaljnivå baseras på märkt utterances eller slutpunkt träffar:

1. Klicka på **ytterligare inställningar** ![ytterligare inställningar för](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) komma åt listan enligt följande bild:

    ![Entiteten uppdelning lista](./media/luis-how-to-use-dashboard/entity-breakdown-based-on.png)
2. Välj ett värde i listan och klicka sedan på pilen Bakåt ![Bakåt](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) att visa diagrammet därefter.
