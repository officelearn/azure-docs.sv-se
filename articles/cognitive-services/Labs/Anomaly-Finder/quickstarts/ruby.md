---
title: 'Hur du använder API: T för Avvikelseidentifiering Finder med Ruby - Microsoft Cognitive Services | Microsoft Docs'
description: 'Hämta information och exempel på kod som hjälper dig att snabbt komma igång med Ruby- och API: T för Avvikelseidentifiering Finder i Cognitive Services.'
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 6eb559f8971583afe9619fb41fe331bd3013bb69
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2018
ms.locfileid: "41988773"
---
# <a name="use-the-anomaly-finder-api-with-ruby"></a>Använda Avvikelsesökare API med Ruby

Den här artikeln innehåller information och kodexempel som hjälper dig att snabbt komma igång med API: T för Avvikelseidentifiering Finder med Ruby för att utföra uppgiften för att få Identifieringsresultat för avvikelseidentifiering av time series-data.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-ruby"></a>Hämta avvikelseidentifiering punkter med Avvikelseidentifiering Finder API med hjälp av Ruby 
[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Exempel på time series-data
Exempel på den tid som datapunkter i serien är enligt följande

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-ruby-example"></a>Analysera data och få avvikelseidentifiering punkter Ruby exempel

Steg för att använda exemplet är som följer.

1. Installera [rest-klient](https://github.com/rest-client/rest-client) genom att köra 'gem installera rest-klient ”.
2. Spara under koden som en .rb-fil.
3. Ersätt den `[YOUR_SUBSCRIPTION_KEY]` värde med giltig prenumeration-nyckel.
4. Ersätt den `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` med exemplet eller dina egna datapunkter.
5. Kör och kontrollera svaret.

```ruby
# https://github.com/rest-client/rest-client
require 'rest_client'

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
subscription_key = '[YOUR_SUBSCRIPTION_KEY]';

endpoint = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

# Replace the request data with your real data.
requestData = '[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]';

header = {
    :content_type => 'application/json',
    :'Ocp-Apim-Subscription-Key' => subscription_key
}

response = RestClient::Request.execute(
    :url => endpoint,
    :method => :post,
    :verify_ssl => true,
    :payload => requestData,
    :header => header)

# You will see the response with anomaly results
puts response.body
```

### <a name="example-response"></a>Exempelsvar

Ett lyckat svar returneras i JSON. Exempelsvar är som följer.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API-referens](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
