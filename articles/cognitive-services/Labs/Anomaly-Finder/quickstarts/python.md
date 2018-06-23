---
title: 'Hur du använder API: et för Avvikelseidentifiering Finder med Python - kognitiva Microsoft-tjänster | Microsoft Docs'
description: Hämta information och exempel på kod för att snabbt komma igång med Avvikelseidentifiering Finder med Python i kognitiva Services.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: c14916b0644edab613b298d6e71f8bbb9a6bb804
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35353310"
---
# <a name="use-the-anomaly-finder-api-with-python"></a>Använda Avvikelseidentifiering Finder API med Python

Den här artikeln innehåller information och kodexempel som hjälper dig att snabbt komma igång med API: et för Avvikelseidentifiering Finder med Python för att utföra uppgiften för att få avvikelseidentifiering resultat för tid series-data.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-python"></a>Hämta avvikelseidentifiering punkter med Avvikelseidentifiering Finder API använder Python 

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Exempel på tid series-data

Exempel på den tid som datapunkter i serien är som följer.

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-python-example"></a>Analysera data och få avvikelseidentifiering punkter Python-exempel

Kontrollera att du har installerat python3 och sedan skapa en python körbar fil med namnet detect.py. I detect.py, bör du inkludera koden nedan. Innan du kör koden, Kom ihåg att ersätta den `[YOUR_SUBSCRIPTION_KEY]` värde med en giltig prenumeration nyckel.
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

Ett lyckat svar returneras i JSON. Exempelsvar är som följer.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Python-appen](../tutorials/python-tutorial.md)
