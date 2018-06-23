---
title: 'Hur du använder API: et för Avvikelseidentifiering Finder med Ruby - kognitiva Microsoft-tjänster | Microsoft Docs'
description: Hämta information och exempel på kod för att snabbt komma igång med Ruby och Avvikelseidentifiering Finder API i kognitiva tjänster.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: ca4754514ba5012f7e9e28981d0869d174561fb3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35353325"
---
# <a name="use-the-anomaly-finder-api-with-ruby"></a>Använda Avvikelseidentifiering Finder API med Ruby

Den här artikeln innehåller information och kodexempel som hjälper dig att snabbt komma igång med API: et för Avvikelseidentifiering Finder med Ruby för att utföra uppgiften för att få resultat av identifiering av avvikelseidentifiering tid serie data.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-ruby"></a>Hämta avvikelseidentifiering punkter med Avvikelseidentifiering Finder API: et med Ruby 
[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Exempel på tid series-data
Exempel på den tid som datapunkter i serien är enligt följande [!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-ruby-example"></a>Analysera data och få avvikelseidentifiering punkter Ruby exempel

Stegen i exemplet är som följer.

1. Installera [rest-klient](https://github.com/rest-client/rest-client) genom att köra 'gem installera rest-klient'.
2. Spara under koden som en .rb.
3. Ersätt den `[YOUR_SUBSCRIPTION_KEY]` värde med en giltig prenumeration nyckel.
4. Ersätt den `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` med exemplet eller egna datapunkter.
5. Köra och kontrollera svaret.

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
