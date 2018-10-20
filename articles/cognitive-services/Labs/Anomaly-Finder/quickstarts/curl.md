---
title: 'Hur du använder API: T för Avvikelseidentifiering Finder med cURL - Microsoft Cognitive Services | Microsoft Docs'
description: 'Hämta information som hjälper dig att snabbt komma igång med cURL och API: T för Avvikelseidentifiering Finder i Cognitive Services.'
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 9f47a49b41156967e0d4ef1c729a6bbb1cca1770
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49456311"
---
# <a name="use-the-anomaly-finder-api-with-curl"></a>Använda API: T för Avvikelseidentifiering Finder med cURL

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Den här artikeln innehåller information och kodexempel som hjälper dig att snabbt komma igång med API: T för Avvikelseidentifiering Finder med cURL till att utföra uppgiften för att få avvikelseidentifiering resultatet av time series-data.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-the-anomaly-finder-api-using-curl"></a>Hämta avvikelseidentifiering punkter med Avvikelseidentifiering Finder API med cURL 

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Exempel på time series-data

Exempel på den tid som datapunkter i serien är som följer.

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-curl-example"></a>Analysera data och få avvikelseidentifiering punkter cURL-exempel

Steg för att använda exemplet är som följer.

1. Ersätt värdet `[YOUR_SUBSCRIPTION_KEY]` med en giltig prenumerationsnyckel.
2. Ersätt den `[YOUR_REGION]` att använda den plats där du har fått din prenumerationsnycklar.
3. Ersätt den `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` med exemplet eller dina egna datapunkter.
4. Kör och kontrollera svaret.

```cURL

curl -v -X POST "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: [YOUR_SUBSCRIPTION_KEY]"
--data-ascii "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]" 

```

### <a name="example-response"></a>Exempelsvar
Ett svar som anger att åtgärden lyckades returneras i JSON. Exempel på ett svar är följande: [!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API-referens](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
