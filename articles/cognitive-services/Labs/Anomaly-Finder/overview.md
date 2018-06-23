---
title: Vad är Avvikelseidentifiering Finder? -Kognitiva Microsoft-tjänster | Microsoft Docs
description: Använd avancerade algoritmer i Avvikelseidentifiering Finder som hjälper dig att identifiera avvikelser i serien tidsdata och returnerar information i kognitiva Microsoft-tjänster.
services: cognitive-services
author: tonyxing
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 04/19/2018
ms.author: tonyxing
ms.openlocfilehash: 1080bb0ad1d901a8b9a5ace4993d4e0d46924a03
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35353343"
---
# <a name="what-is-anomaly-finder"></a>Vad är Avvikelseidentifiering Finder?

Avvikelseidentifiering Finder kan du övervaka data över tid och identifiera avvikelser med machine learning som anpassar sig till unika data genom att använda direkt statistiska modellen oavsett bransch, scenario eller data volymen automatiskt. Med hjälp av en tidsserie som indata Avvikelseidentifiering Finder API returnerar huruvida en datapunkt är en avvikelseidentifiering anger förväntat värde och övre och nedre gränser för visualisering. Som en fördefinierad AI-tjänst kräver Avvikelseidentifiering Finder inte någon maskininlärning kunskaper utöver att förstå hur du använder en RESTful-API. Detta gör utveckling enkel och flexibel eftersom den fungerar med alla tid series-data och kan också vara inbyggt i strömmande data system. Avvikelseidentifiering Finder omfattar ett brett intervall av användningsområden – till exempel finansiella verktyg för att hantera bedrägeri, stöld, ändra marknader och potentiella business incidenter eller övervaka trafik för IoT-enheter samtidigt som anonym. Den här lösningen kan också monetized som en del av en tjänst för end-kunder att förstå ändringar i data, utgifter, avkastning på investeringar eller användaraktivitet.
Prova att använda API: et för Avvikelseidentifiering Finder och få djupare förståelse för dina data. 

Se vad du kan bygga med den här API:

* Lär dig att förutsäga förväntade värden baserat på historisk data i tidsserien
* Avgöra om en datapunkt är en avvikelseidentifiering utanför tidigare mönstret
* Generera ett band om du vill visualisera intervallet för ”normalt” värde

![Anomaly_Finder](./media/anomaly_detection1.png) 

Figur 1: Identifiera avvikelser i försäljning intäkter

![Anomaly_Finder](./media/anomaly_detection2.png)

Figur 2: Identifiera mönster ändringar i tjänstbegäranden

## <a name="requirements"></a>Krav

- Minsta mängd data för indata time series: minst 13 data pekar för tidsserier utan att radera periodicitet minst 4 cykler data pekar för tidsserier med kända periodicitet. 
- Dataintegritet: tid seriens datapunkter avskiljs i samma intervall och inga saknas punkter. 

## <a name="identify-anomalies"></a>Identifiera avvikelser

Avvikelseidentifiering API returnerar resultat som om alla angivna datapunkter är avvikelser eller inte och tillhandahåller ytterligare information på följande sätt
* Period - periodiciteten API: et används för att identifiera avvikelseidentifiering punkter.
* WarningText - möjliga varningsinformation.
* ExpectedValue - förutsägelsevärdet med inlärning baserat modellen
* IsAnomaly - resultatet om datapunkter är avvikelser eller inte
* IsAnomaly_Neg - resultatet om datapunkter är avvikelser i negativ riktning (korta)
* IsAnomaly_Pos - resultatet om datapunkter är avvikelser i positiv riktning (toppar)
* UpperMargin - summan av ExpectedValue och UpperMargin anger den övre gränsen för datapunkt kan fortfarande anses som vanligt
* LowerMargin - (ExpectedValue - LowerMargin) avgör undre gränsvärde att datapunkt fortfarande tänkte som vanligt

> [!Note]
> UpperMargin och LowerMargin kan användas för att generera ett band runt faktiska tidsserier visualisera normal värdeintervallet. 

## <a name="adjusting-lower-and-upper-bounds-in-post-processing-on-the-response"></a>Justera nedre och övre gränser i efter bearbetning i svaret

Avvikelseidentifiering API returnerar standardresultatet på om en datapunkt avvikelseidentifiering eller inte och den övre och nedre gränsen kan beräknas från ExpectedValue och UpperMargin/LowerMargin. Dessa standardvärden bör fungera bra i de flesta fall. Vissa scenarier kräver dock olika gränser än standardinställningarna. Rekommenderad praxis tillämpar en coefficiency på UpperMargin eller LowerMargin för att justera dynamiska gränser.

### <a name="examples-with-1152-as-coefficiency"></a>Exempel med 2/1/1.5 som coefficiency

![Standard känslighet](./media/sensitivity_1.png)

![1.5 känslighet](./media/sensitivity_1.5.png)

![2 känslighet](./media/sensitivity_2.png)

Begäran med exempeldata

[!INCLUDE [Request](./includes/request.md)]

Exempel JSON-svar

[!INCLUDE [Response](./includes/response.md)]
