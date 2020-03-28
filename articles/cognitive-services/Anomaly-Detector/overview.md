---
title: Vad är API:et för avvikelseidentifiering?
titleSuffix: Azure Cognitive Services
description: Använd API:et för avvikelsedetektorns avancerade algoritmer för att identifiera avvikelser i tidsseriedata.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: 9237e670dd8d43c4036f996c477948944718e3aa
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80053702"
---
# <a name="what-is-the-anomaly-detector-api"></a>Vad är API:et för avvikelseidentifiering?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Med API:et för avvikelsedetektorer kan du övervaka och identifiera avvikelser i dina tidsseriedata med maskininlärning. Api:et för avvikelsedetektor anpassar sig genom att automatiskt identifiera och tillämpa de modeller som passar bäst på dina data, oavsett bransch, scenario eller datavolym. Med hjälp av tidsseriedata bestämmer API:et gränser för avvikelseidentifiering, förväntade värden och vilka datapunkter som är avvikelser.

![Identifiera mönsterändringar i tjänstbegäranden](./media/anomaly_detection2.png)

Att använda avvikelsedetektorn kräver ingen tidigare erfarenhet av maskininlärning, och RESTful API gör att du enkelt kan integrera tjänsten i dina program och processer.

## <a name="features"></a>Funktioner

Med avvikelsedetektorn kan du automatiskt identifiera avvikelser i hela tidsseriedata, eller när de inträffar i realtid.

|Funktion  |Beskrivning  |
|---------|---------|
|Identifiera avvikelser när de inträffar i realtid. | Identifiera avvikelser i dina strömmande data med hjälp av tidigare sedda datapunkter för att avgöra om den senaste är en avvikelse. Den här åtgärden genererar en modell med hjälp av de datapunkter du skickar och avgör om målpunkten är en avvikelse. Genom att anropa API:et med varje ny datapunkt som du genererar kan du övervaka dina data när de skapas. |
|Identifiera avvikelser i hela datauppsättningen som en batch. | Använd tidsserierna för att identifiera eventuella avvikelser som kan finnas i dina data. Den här åtgärden genererar en modell med hjälp av hela tidsseriedata, där varje punkt analyseras med samma modell.         |
| Få ytterligare information om dina data. | Få användbar information om dina data och eventuella observerade avvikelser, inklusive förväntade värden, avvikelsegränser och positioner. |
| Justera avvikelseidentifieringsgränser. | API:et för avvikelsedetektor skapar automatiskt gränser för avvikelseidentifiering. Justera dessa gränser för att öka eller minska API:ets känslighet för dataavvikelser och bättre anpassa dina data. |

## <a name="demo"></a>Demo

Kolla in denna [interaktiva demo](https://aka.ms/adDemo) för att förstå hur Anomaly Detector fungerar.
Om du vill köra demon måste du skapa en avvikelsedetektorresurs och hämta API-nyckeln och slutpunkten.

## <a name="notebook"></a>Notebook-fil

Om du vill veta hur du anropar API:et för avvikelsedetektorer [provar](https://aka.ms/adNotebook)du den här Azure Notebook . Den här webbaserade jupyterda anteckningsboken visar hur du skickar en API-begäran och visualiserar resultatet.

Så här kör du anteckningsboken:

1. Hämta en giltig API-prenumerationsnyckel för avvikelsedetektor och en API-slutpunkt. Avsnittet nedan innehåller instruktioner för hur du registrerar dig.
1. Logga in och klicka på Klona i det övre högra hörnet.
1. Avmarkera alternativet "offentlig" i dialogrutan innan du slutför klonåtgärden, annars är anteckningsboken, inklusive eventuella prenumerationsnycklar, offentlig.
1. Klicka **på Kör på fri beräkning**
1. Välj en av anteckningsböckerna.
1. Lägg till din giltiga API-prenumerationsnyckel för avvikelsedetektor i variabeln. `subscription_key`
1. Ändra `endpoint` variabeln till slutpunkten. Exempel: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/last/detect`
1. Klicka på **Cell**på den övre menyraden och sedan **Kör alla**.

## <a name="workflow"></a>Arbetsflöde

Anomaly Detector API är en RESTful webbtjänst, vilket gör det enkelt att anropa från alla programmeringsspråk som kan göra HTTP-begäranden och tolka JSON.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

Efter registreringen:

1. Ta dina tidsseriedata och konvertera dem till ett giltigt JSON-format. Använd [metodtips](concepts/anomaly-detection-best-practices.md) när du förbereder dina data för att få bästa resultat.
1. Skicka en begäran till API:et för avvikelsedetektor med dina data.
1. Bearbeta API-svaret genom att tolka det returnerade JSON-meddelandet.

## <a name="algorithms"></a>Algoritmer

* Se följande tekniska bloggar för information om de algoritmer som används:
    * [Introduktion till API för Azure-avvikelseidentifiering](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162)
    * [Översikt över SR-CNN-algoritm i Azure Anomaly Detector](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Overview-of-SR-CNN-algorithm-in-Azure-Anomaly-Detector/ba-p/982798)

Du kan läsa dokumentet [Time-Series Anomaly Detection Service på Microsoft](https://arxiv.org/abs/1906.03821) (accepterad av KDD 2019) om du vill veta mer om SR-CNN-algoritmerna som utvecklats av Microsoft.


> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="join-the-anomaly-detector-community"></a>Delta i communityn för avvikelseidentifiering

* Gå med i [gruppen Avvikelsedetektorrådgivare i Microsoft Teams](https://aka.ms/AdAdvisorsJoin)
* Visa valt [användargenererat innehåll](user-generated-content.md)

## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Identifiera avvikelser i tidsseriedata med REST API:et för avvikelsedetektor](quickstarts/detect-data-anomalies-csharp.md)
* Den Anomali Detector API [online demo](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector)
* [REST-referensen för avvikelsedetektorns REST-API](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
