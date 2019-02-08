---
title: Appinstrumentpanel
titleSuffix: Language Understanding - Azure Cognitive Services
description: Läs mer om instrumentpanelen för program, ett visualiserade verktyg som hjälper dig att övervaka dina appar i korthet enkel.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: 8927213dc4f1cf01eccdd8f54a946f9dbbaaa04d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55869967"
---
# <a name="model-and-usage-statistics-in-the-dashboard"></a>Modell- och användningsdata statistik på instrumentpanelen
Appinstrumentpanelen kan du övervaka din app på en snabb titt. Den **instrumentpanelen** visas när du öppnar en app genom att klicka på namnet på programmet på **Mina appar** sedan väljer **instrumentpanelen** från övre panelen. 

> [!CAUTION]
> Om du vill använda de senaste uppdaterade mått för LUIS måste du:
> * Använda en LUIS [slutpunktsnyckeln](luis-how-to-azure-subscription.md) skapats i Azure
> * Använd LUIS slutpunktsnyckeln för alla endpoint begäranden, inklusive LUIS [API](https://aka.ms/luis-endpoint-apis) och bot
> * Använd olika slutpunktsnyckeln för varje LUIS-app. Använd inte en enda slutpunkt-nyckel för alla appar. Slutpunktsnyckeln spåras på viktiga-nivå, inte på appnivå.  

Den **instrumentpanelen** sidan ger dig en översikt över LUIS-app, inklusive den aktuella modellen tillstånd, samt [endpoint](luis-glossary.md#endpoint) användning över tid. 
  
## <a name="app-status"></a>Appstatus
Instrumentpanelen visar programmets utbildning och Publiceringsstatus, inklusive datum och tid när appen senast tränas och publiceras.  

![Instrumentpanel – Appstatus](./media/luis-how-to-use-dashboard/app-state.png)

## <a name="model-data-statistics"></a>Data modellstatistik
Instrumentpanelen visar det totala antalet avsikter och entiteter taggade yttranden befintliga i appen. 

![AppData statistik](./media/luis-how-to-use-dashboard/app-model-count.png)

## <a name="endpoint-hits"></a>Slutpunkten träffar
Instrumentpanelen visar de totala endpoint träffar som LUIS-app tar emot och kan du visa når inom en period som du anger. Det totala antalet träffar som visas är summan av slutpunkten träffar som använder en [slutpunktsnyckeln](./luis-concept-keys.md#endpoint-key) och slutpunkten når som använder en [redigering nyckeln](./luis-concept-keys.md#authoring-key).

![Slutpunkten träffar](./media/luis-how-to-use-dashboard/dashboard-endpointhits.png)

> [!NOTE] 
> Antal träffar i den senaste slutpunkten i Azure portal på tjänstöversikt LUIS. 
 
### <a name="total-endpoint-hits"></a>Totalt antal endpoint träffar
Totalt antal endpoint träffar som tagits emot i din app sedan appskapande upp till det aktuella datumet.

### <a name="endpoint-hits-per-period"></a>Slutpunkten träffar per period
Antal träffar emot inom en tidigare period, visas per dag. Punkterna mellan start- och slutdatumen motsvarar dagar som omfattas av i den här perioden. Muspekaren över varje punkt om du vill se träffarna antal i varje dag under perioden. 

Att välja en period om du vill visa i diagrammet:
 
1. Klicka på **ytterligare inställningar** ![knappen ytterligare inställningar](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) att komma åt listan över perioder. Du kan välja perioder som sträcker sig från en vecka upp till ett år. 

    ![Slutpunkten träffar per Period](./media/luis-how-to-use-dashboard/timerange.png)

2. Välj en punkt i listan och klicka sedan på bakåtpilen ![Bakåtpil](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) att visa diagrammet.

### <a name="key-usage"></a>Nyckelanvändning
Antal träffar som konsumeras från programmets slutpunktsnyckeln. Mer information om slutpunkt-nycklar finns i [nycklar i LUIS](luis-concept-keys.md). 
  
## <a name="intent-breakdown"></a>Avsiktshantering analys på detaljnivå
Den **avsikt analys på detaljnivå** visar en uppdelning av avsikter utifrån taggade yttranden eller slutpunkten träffar. Den här översiktsdiagrammet visar den relativa prioriteten för varje avsikt i appen. När du håller muspekaren över en sektor visas namnet på avsiktlig och procentandelen representerar det totala antalet träffar taggade yttranden eller slutpunktens. 

![Avsiktshantering analys på detaljnivå](./media/luis-how-to-use-dashboard/intent-breakdown.png)

Kontrollera om en analys på detaljnivå baseras på märkta yttranden eller slutpunkten träffar:

1. Klicka på **ytterligare inställningar** ![ytterligare inställningar för att åtkomstlista](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) att få åtkomst till listan som i följande bild:

    ![Avsiktshantering analys på detaljnivå lista](./media/luis-how-to-use-dashboard/intent-breakdown-based-on.png)
2. Välj ett värde i listan och klicka sedan på bakåtpilen ![Bakåtpil](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) att visa diagrammet.

## <a name="entity-breakdown"></a>Entiteten analys på detaljnivå
Instrumentpanelen visar en uppdelning av enheter baserat på märkta yttranden eller slutpunkten träffar. Den här översiktsdiagrammet visar den relativa prioriteten för varje entitet i appen. När du håller muspekaren över en sektor visas enhetens namn och procent i taggade yttranden eller slutpunktens träffar. 

![Entiteten analys på detaljnivå](./media/luis-how-to-use-dashboard/entity-breakdown.png)

Kontrollera om en analys på detaljnivå baseras på märkta yttranden eller slutpunkten träffar:

1. Klicka på **ytterligare inställningar** ![ytterligare inställningar för att hämta lista](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) att få åtkomst till listan som i följande bild:

    ![Uppdelning Entitetslista](./media/luis-how-to-use-dashboard/entity-breakdown-based-on.png)
2. Välj ett värde i listan och klicka sedan på bakåtpilen ![Bakåtpil](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) att visa diagrammet i enlighet med detta.
