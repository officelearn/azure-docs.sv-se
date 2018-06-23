---
title: 'Hur du använder API: et för Avvikelseidentifiering Finder med cURL - kognitiva Microsoft-tjänster | Microsoft Docs'
description: 'Hämta information som hjälper dig att snabbt komma igång med API: er för Avvikelseidentifiering Finder och cURL i kognitiva tjänster.'
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 3c1d791b8c0478715b4ffa93cd7dfa43f9be4586
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35353316"
---
# <a name="use-the-anomaly-finder-api-with-curl"></a>Använda API: et för Avvikelseidentifiering Finder med cURL

Den här artikeln innehåller information och kodexempel som hjälper dig att snabbt komma igång med API: et för Avvikelseidentifiering Finder med cURL för att utföra uppgiften för att få avvikelseidentifiering resultatet av tid series-data.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-the-anomaly-finder-api-using-curl"></a>Hämta avvikelseidentifiering punkter med Avvikelseidentifiering Finder API: et med cURL 

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Exempel på tid series-data

Exempel på den tid som datapunkter i serien är som följer.

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-curl-example"></a>Analysera data och få avvikelseidentifiering punkter cURL-exempel

Stegen i exemplet är som följer.

1. Ersätt den `[YOUR_SUBSCRIPTION_KEY]` värde med en giltig prenumeration nyckel.
2. Ersätt den `[YOUR_REGION]` att använda den plats där du har köpt din prenumeration nycklar.
3. Ersätt den `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` med exemplet eller egna datapunkter.
4. Köra och kontrollera svaret.

```cURL

curl -v -X POST "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: [YOUR_SUBSCRIPTION_KEY]"
--data-ascii "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]" 

```

### <a name="example-response"></a>Exempelsvar
Ett lyckat svar returneras i JSON. Exempelsvar är följande: [!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API-referens](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
