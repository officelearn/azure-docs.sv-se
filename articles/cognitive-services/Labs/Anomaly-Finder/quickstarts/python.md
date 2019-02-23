---
title: 'Hur du använder API: T för Avvikelseidentifiering Finder med Python – Microsoft Cognitive Services | Microsoft Docs'
description: Hämta information och exempel på kod som hjälper dig att snabbt komma igång med Avvikelsesökare med Python i Cognitive Services.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: df894fee3328b2199b8f7436adabfd3db62f4004
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737135"
---
# <a name="use-the-anomaly-finder-api-with-python"></a>Använda Avvikelsesökare API med Python

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Den här artikeln innehåller information och kodexempel som hjälper dig att snabbt komma igång med Avvikelseidentifiering Finder API med Python för att utföra uppgiften för att få avvikelseidentifiering resultatet för time series-data.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-python"></a>Hämta avvikelseidentifiering punkter med Avvikelseidentifiering Finder API med Python 

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Exempel på time series-data

Exempel på den tid som datapunkter i serien är som följer.

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-python-example"></a>Analysera data och få avvikelseidentifiering punkter Python-exempel

Kontrollera att du har installerat python3 och sedan skapa en python körbar fil med namnet detect.py. I detect.py, bör du ta med koden nedan. Innan du kör koden, Kom ihåg att ersätta den `[YOUR_SUBSCRIPTION_KEY]` värde med giltig prenumeration-nyckel.
Ersätt den `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` med datapunkterna.

```python
import requests
import json


def detect(url, subscription_key, request_data):
    headers = {'Content-Type': 'application/json', 'Ocp-Apim-Subscription-Key': subscription_key}
    response = requests.post(url, data=json.dumps(request_data), headers=headers)
    if response.status_code == 200:
        return json.loads(response.content.decode("utf-8"))
    else:
        print(response.status_code)
        raise Exception(response.text)


sample_data = "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]"
endpont = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection"
subscription_key = "[YOUR_SUBSCRIPTION_KEY]"

result = detect(endpont, subscription_key, sample_data)
print(result)

```

### <a name="example-response"></a>Exempelsvar

Ett svar som anger att åtgärden lyckades returneras i JSON. Exempelsvar är som följer.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Python-app](../tutorials/python-tutorial.md)
