---
title: Vad är avvikelsesökare? -Microsoft Cognitive Services | Microsoft Docs
description: Använd avancerade algoritmer i Avvikelsesökare hjälper dig att identifiera avvikelser i tidsseriedata och returnerar information i Microsoft Cognitive Services.
services: cognitive-services
author: tonyxing
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 04/19/2018
ms.author: tonyxing
ms.openlocfilehash: 2a0715d3becf695600ed84edbae38151acf055a8
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48246850"
---
# <a name="what-is-anomaly-finder"></a>Vad är avvikelsesökare?

[!INCLUDE [PrivatePreviewNote](../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Avvikelsesökare kan du övervaka data över tid och identifiera avvikelser med machine learning som kan anpassas efter dina unika data genom att automatiskt tillämpa rätt statistiska modellen oavsett bransch, scenario eller datavolym. Med hjälp av en tidsserie som indata, Avvikelseidentifiering Finder API returnerar huruvida en datapunkt är en avvikelse anger det förväntade värdet och övre och nedre gränserna för visualisering. Som en fördefinierade AI-tjänsten kräver Avvikelsesökare inte någon machine learning-expertis utöver att förstå hur du använder en RESTful-API. Detta gör utveckling enkelt och flexibelt eftersom den fungerar med alla time series-data och kan också byggas in strömmande data system. Avvikelsesökare omfattar ett brett omfång av användningsmöjligheter – till exempel finansiella verktyg för hantering av bedrägerier, stöld, ändra marknader och potentiella företag incidenter eller övervakar IoT device trafik samtidigt som anonym. Den här lösningen kan också vara utgöra som en del av en tjänst för slutkunder att förstå ändringar i data, utgifter, avkastning på investeringen eller användaraktivitet en inkomstkälla.
Prova API: T för Avvikelseidentifiering Finder och få djupare förståelse för dina data. 

Se vad du kan skapa med den här API:

* Lär dig att förutse de förväntade värdena baserat på historiska data i tidsserien
* Avgöra om en datapunkt är en avvikelse från historiska mönster
* Generera ett band om du vill visualisera antal ”normal” värde

![Anomaly_Finder](./media/anomaly_detection1.png) 

Figur 1: Identifiera avvikelser i försäljning intäkter

![Anomaly_Finder](./media/anomaly_detection2.png)

Figur 2: Identifiera ändringar i mönster i tjänstbegäranden

## <a name="requirements"></a>Krav

- Minsta mängd data för indata time series: minst 13 data pekar för tidsserier utan tydliga periodicitet minst 4 cykler data pekar för tidsserier med kända periodicitet. 
- Dataintegritet: tid serie datapunkter är avgränsade i samma intervall och inga saknas punkter. 

## <a name="identify-anomalies"></a>Identifiera avvikelser

Avvikelseidentifiering API returnerar resultat som om alla angivna datapunkter är avvikelser eller inte och ger ytterligare information på följande sätt
* Period - periodicitet som API: et används för att identifiera avvikelser punkter.
* WarningText - möjliga varningsinformation.
* ExpectedValue - förutsägelsevärdet genom utbildningsresurser-baserad modell
* IsAnomaly - resultatet på om datapunkter är avvikelser eller inte
* IsAnomaly_Neg - resultatet på om datapunkter är avvikelser i negativ riktning (dips)
* IsAnomaly_Pos - resultatet på om datapunkter är avvikelser i positiva riktning (toppar)
* UpperMargin - summan av ExpectedValue och UpperMargin anger den övre gränsen som datapunkt är fortfarande betraktas som vanligt
* LowerMargin - (ExpectedValue - LowerMargin) avgör undre gräns att datapunkt fortfarande betraktas som vanligt

> [!Note]
> UpperMargin och LowerMargin kan användas för att generera ett band runt faktiska tidsserier att visualisera det normala värdeintervallet. 

## <a name="adjusting-lower-and-upper-bounds-in-post-processing-on-the-response"></a>Justera lägre och övre gränser i inlägget bearbetning på svaret

Avvikelseidentifiering API returnerar Standardresultat på om en datapunkt är avvikelseidentifiering eller inte och den övre och nedre gränsen kan beräknas från ExpectedValue och UpperMargin/LowerMargin. Dessa standardvärden bör fungera bra för de flesta fall. Vissa scenarier kräver dock olika gränser än standardinställningarna. Det rekommenderas att metoden tillämpas en coefficiency på UpperMargin eller LowerMargin att justera dynamisk gränser.

### <a name="examples-with-1152-as-coefficiency"></a>Exempel med 1/1.5/2 som coefficiency

![Standard känslighet](./media/sensitivity_1.png)

![1.5 känslighet](./media/sensitivity_1.5.png)

![2 känslighet](./media/sensitivity_2.png)

Begäran med exempeldata

[!INCLUDE [Request](./includes/request.md)]

Exempel-JSON-svar

[!INCLUDE [Response](./includes/response.md)]
