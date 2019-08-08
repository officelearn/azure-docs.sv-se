---
title: Vad är API:et för avvikelseidentifiering?
titleSuffix: Azure Cognitive Services
description: Använd de avancerade algoritmerna för avvikelse detektorns API för att identifiera avvikelser i dina tids serie data.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 70cb4a008391ad55562bc55fb45d877988e68643
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854781"
---
# <a name="what-is-the-anomaly-detector-api"></a>Vad är API:et för avvikelseidentifiering?

Med API: t för avvikelse identifiering kan du övervaka och identifiera avvikelser i dina Time Series-data med Machine Learning. API: et för avvikelse detektor anpassningar genom att automatiskt identifiera och tillämpa de bästa passnings modellerna för dina data, oavsett bransch, scenario eller data volym. Med hjälp av dina Time Series-data fastställer API gränser för avvikelse identifiering, förväntade värden och vilka data punkter som är avvikelser.

![Identifiera mönster ändringar i tjänst begär Anden](./media/anomaly_detection2.png)

Att använda avvikelse detektorn kräver ingen tidigare erfarenhet av Machine Learning, och RESTful-API: et gör att du enkelt kan integrera tjänsten i dina program och processer.

## <a name="features"></a>Funktioner

Med avvikelse identifieraren kan du automatiskt identifiera avvikelser i tids serie data, eller när de inträffar i real tid. 

|Funktion  |Beskrivning  |
|---------|---------|
|Identifiera avvikelser när de inträffar i real tid. | Identifiera avvikelser i dina strömmande data genom att använda tidigare visade data punkter för att avgöra om det senaste är en avvikelse. Den här åtgärden genererar en modell med de data punkter som du skickar och avgör om mål punkten är en avvikelse. Genom att anropa API: et med varje ny data punkt som du skapar kan du övervaka dina data när de skapas. |
|Identifiera avvikelser i alla data uppsättningar som en batch. | Använd din tids serie för att identifiera eventuella avvikelser som kan finnas i dina data. Den här åtgärden genererar en modell med alla tids serie data, där varje punkt analyseras med samma modell.         |
| Få mer information om dina data. | Få värdefull information om dina data och observerade avvikelser, inklusive förväntade värden, avvikande gränser och positioner. |
| Justera gränser för avvikelse identifiering. | API: t för avvikelse detektor skapar automatiskt gränser för avvikelse identifiering. Justera gränserna för att öka eller minska API: ns känslighet till data avvikelser och bättre anpassa dina data. |

## <a name="demo"></a>Demo

Kolla den här [interaktiva demonstrationen](https://aka.ms/adDemo) för att förstå hur avvikelse detektor fungerar.
Om du vill köra demonstrationen måste du skapa en avvikelse identifierings resurs och hämta API-nyckeln och slut punkten.

## <a name="notebook"></a>Notebook-fil

Om du vill veta hur du anropar API: t för avvikelse detektor kan du prova den här [Azure](https://aka.ms/adNotebook)-anteckningsboken. Den här webb värd Jupyter Notebook visar hur du skickar en API-begäran och visualiserar resultatet.

Utför följande steg för att köra antecknings boken:

1. Hämta en giltig API-prenumerations nyckel för avvikelse detektor och en API-slutpunkt. Avsnittet nedan innehåller anvisningar för att registrera dig.
1. Logga in och klicka på klona i det övre högra hörnet.
1. Avmarkera alternativet "offentlig" i dialog rutan innan du slutför klonings åtgärden, annars är din bärbara dator, inklusive eventuella prenumerations nycklar, offentlig.
1. Klicka på **Kör vid kostnads fri beräkning**
1. Välj en av antecknings böckerna.
1. Lägg till din giltiga API-prenumerations nyckel för avvikelse `subscription_key` detektor i variabeln. 
1. `endpoint` Ändra variabeln till din slut punkt. Exempel: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/last/detect`
1. Klicka på **cell**och **Kör alla**på den översta meny raden.

## <a name="workflow"></a>Arbetsflöde

API: t för avvikelse detektor är en RESTful-webbtjänst, vilket gör det enkelt att anropa från valfritt programmeringsspråk som kan göra HTTP-begäranden och parsa JSON.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

Efter registreringen:

1. Ta dina Time Series-data och konvertera dem till ett giltigt JSON-format. Använd [bästa praxis](concepts/anomaly-detection-best-practices.md) när du förbereder dina data för att få bästa möjliga resultat.
1. Skicka en begäran till avvikelse identifierings-API: et med dina data.
1. Bearbeta API-svaret genom att tolka det returnerade JSON-meddelandet.

## <a name="algorithms"></a>Algoritmer

* I den här tekniska bloggen [presenterar vi Azures API för avvikelse detektor](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162) om algoritmerna under huven.
* Se den här pappers [seriens avvikelse identifierings tjänst på Microsoft](https://arxiv.org/abs/1906.03821) (godkänd av KDD 2019) för de CNN-algoritmer som utvecklats av Microsoft.

> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Identifiera avvikelser i dina tids serie data med hjälp av avvikelse detektor REST API](quickstarts/detect-data-anomalies-csharp.md)
* API: t online- [demon](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector) för avvikelse detektor
* Avvikelse detektor [REST API referens](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
