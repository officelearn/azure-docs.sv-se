---
title: 'Snabbstart: Känsloigenkänning i ansikten i en bild – Känslo-API, Ruby'
titlesuffix: Azure Cognitive Services
description: Hämta information och exempel på kod som hjälper dig att snabbt komma igång med känslo-API med Ruby.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: quickstart
ms.date: 05/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: bcab24334c1ee4e47061ce6ea28bd60039e17b3f
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48239037"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Snabbstart: Skapa en app för att känna igen känslor i ansikten på en bild.

> [!IMPORTANT]
> Känslo-API:et blir inaktuellt den 15 februari 2019. Funktionen för känsloigenkänning är nu allmänt tillgänglig som en del av [ansikts-API:et](https://docs.microsoft.com/azure/cognitive-services/face/). 

Den här artikeln innehåller information och kodexempel som hjälper dig att snabbt komma igång med hjälp av [metoden Recognize för Känslo-API:et](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) med Ruby för att känna igen känslor som uttrycks av en eller flera personer i en bild.

## <a name="prerequisite"></a>Krav
* Få din kostnadsfria prenumerationsnyckel [här](https://azure.microsoft.com/try/cognitive-services/)

## <a name="recognize-emotions-ruby-example-request"></a>Exempelbegäran för känsloigenkänning med Ruby

Ändra REST URL:en för att använda platsen där du fick dina prenumerationsnycklar, ersätt värdet ”Ocp-Apim-Subscription-Key” med din giltiga prenumerationsnyckel och lägg till en webbadress till variabeln `body`.

```ruby
require 'net/http'

# NOTE: You must use the same region in your REST call as you used to obtain your subscription keys.
#   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the
#   URL below with "westcentralus".
uri = URI('https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize')
uri.query = URI.encode_www_form({
})

request = Net::HTTP::Post.new(uri.request_uri)
# Request headers
request['Content-Type'] = 'application/json'
# NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
request['Ocp-Apim-Subscription-Key'] = '13hc77781f7e4b19b5fcdd72a8df7156'
# Request body
request.body = "{\"url\":\"http://example.com/1.jpg\"}"

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request(request)
end

puts response.body

```

## <a name="recognize-emotions-sample-response"></a>Exempelsvar för känsloigenkänning
Ett genomfört anrop returnerar en matris med ansiktsposter och deras associerade känsloresultat, rankade efter ansiktsrektangelns storlek i fallande ordning. Ett tomt svar indikerar att inga ansikten kändes igen. En känslopost innehåller följande fält:
* faceRectangle – ansiktsrektangelns placering i bilden.
* scores – känsloresultat för varje ansikte i bilden.

```json
application/json
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]
