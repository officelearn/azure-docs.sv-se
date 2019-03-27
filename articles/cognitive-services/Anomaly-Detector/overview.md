---
title: 'Vad är API: T för Avvikelseidentifiering detektor? | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Använd avancerade algoritmer för Avvikelseidentifiering detektor API för att identifiera avvikelser i tidsseriedata.
services: cognitive-services
author: aahill
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 48dffb8853d3def590e434c1683ecf038e2a194e
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58473278"
---
# <a name="what-is-the-anomaly-detector-api"></a>Vad är API: T för Avvikelseidentifiering detektor?

Avvikelseidentifiering detektor API kan du övervaka och identifiera avvikelser i tidsseriedata med machine learning. API: T för Avvikelseidentifiering detektor anpassas genom att automatiskt identifiera och tillämpa bäst passningsåtgärderna modeller till dina data, oavsett bransch, scenario eller datavolym. Med time series-data kan API: et anger gränser för identifiering av avvikelser, förväntade värden och vilka datapunkter är avvikelser.

![Identifiera ändringar i mönster i tjänstbegäranden](./media/anomaly_detection2.png)

Avvikelseidentifiering detektor kräver inte någon tidigare erfarenhet av maskininlärning och RESTful API kan du enkelt integrera tjänsten i dina program och processer.

## <a name="features"></a>Funktioner

Med Avvikelseidentifiering detektor, kan du automatiskt upptäcka avvikelser i tidsseriedata, eller när de inträffar i realtid. 

|Funktion  |Beskrivning  |
|---------|---------|
|Identifiera avvikelser allteftersom de sker i realtid. | Identifiera avvikelser i din strömmande data genom att använda tidigare visad datapunkter för att avgöra om din senaste ett är ett fel. Den här åtgärden skapar en modell med datapunkter du skickar och anger om målpunkt avvikelser. Du kan övervaka dina data när den har skapats genom att anropa API: et med varje ny datapunkt som du genererar. |
|Identifiera avvikelser i hela datauppsättningen som en batch. | Använd din tidsserier för att identifiera avvikelser som kan finnas i dina data. Den här åtgärden skapar en modell som använder din hela time series-data med varje punkt som analyseras med samma modell.         |
| Få mer information om dina data. | Få användbar information om dina data och alla observerade avvikelser, inklusive förväntade värden, avvikelseidentifiering gränser och positioner. |
| Justera gränserna för identifiering av avvikelser. | API: T för Avvikelseidentifiering detektor skapar automatiskt gränser för avvikelseidentifiering. Justera dessa gränser om du vill öka eller minska API: er känslighet för data avvikelser och bättre passa för dina data. |

## <a name="demo"></a>Demo

För att snabbt börja använda API: T för Avvikelseidentifiering detektor, prova en [demonstrationen online](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector) som kan köras i webbläsaren. Den här demon körs i en webbaserat Jupyter-anteckningsbok och visar hur du skickar en begäran om API: et och visualisera resultatet.

Om du vill köra demon, gör du följande:

1. Få en giltig Avvikelseidentifiering detektor API-prenumerationsnyckel och en API-slutpunkt. I avsnittet nedan innehåller instruktioner för att logga. 
2. Logga in och klicka på klonen, i det övre högra hörnet.
3. Klicka på **körs på kostnadsfri beräkning**
4. Välj en av de bärbara datorerna för det här exemplet.
5. Lägg till din giltig Avvikelseidentifiering detektor API-prenumerationsnyckel till den `subscription_key` variabeln. Ändra den `endpoint` variabeln till slutpunkten. Exempel: `https://westus2.api.cognitive.microsoft.com`
1. På den övre menyraden klickar du på **Cell**, sedan **kör alla**.

## <a name="workflow"></a>Arbetsflöde

API: T för Avvikelseidentifiering detektor är en RESTful-webb-tjänst som gör det enkelt att anropa från alla programmeringsspråk som kan göra HTTP-begäranden och parsa JSON.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

När du registrerar dig:

1. Ta din time series-data och omvandla dem till ett giltigt JSON-format. Använd [bästa praxis](concepts/anomaly-detection-best-practices.md) när du förbereder dina data att få bästa resultat.
1. Skicka en begäran till API: T för Avvikelseidentifiering-detektor med dina data.
1. Bearbeta API-svaret genom att tolka det returnerade JSON-meddelandet.

## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Identifiera avvikelser i dina time series-data med hjälp av Avvikelseidentifiering detektor REST API](quickstarts/detect-data-anomalies-csharp.md)
* API: T för Avvikelseidentifiering detektor [demonstrationen online](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector)
* Avvikelseidentifiering detektor [REST API-referens](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)