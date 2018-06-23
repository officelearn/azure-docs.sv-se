---
title: Känslo-API Ruby Snabbstart | Microsoft Docs
description: Hämta information och exempel på kod för att snabbt komma igång med Känslo-API med Ruby i kognitiva Services.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: 7ac981966a303b3d197a3e27b6f3a1b20f34c17f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352389"
---
# <a name="emotion-api-ruby-quick-start"></a>Känslo-API Ruby Snabbstart

> [!IMPORTANT]
> Förhandsgranskning av video API avslutas på 30 oktober 2017. Prova den nya [Video indexeraren API Preview](https://azure.microsoft.com/services/cognitive-services/video-indexer/) enkelt extrahera insikter från videor och förbättra innehållsidentifiering upplevelser, till exempel sökresultat med hjälp av tal, ytor, tecken och känslor. [Läs mer](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Den här artikeln innehåller information och kodexempel som hjälper dig att snabbt komma igång med den [Känslo-API känner igen metoden](https://dev.projectoxford.ai/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) med Ruby att identifiera emotikoner uttryckt genom en eller flera personer i en bild.

## <a name="prerequisite"></a>Krav
* Hämta din kostnadsfria prenumerationen nyckel [här](https://azure.microsoft.com/try/cognitive-services/)

## <a name="recognize-emotions-ruby-example-request"></a>Identifiera emotikoner Ruby Exempelbegäran

Ändra REST-URL: en om du vill använda den plats där du har köpt din prenumeration nycklar, Ersätt ”Ocp-Apim-prenumeration-Key”-värdet med giltig prenumeration-nyckel och lägga till en URL till ett foto till den `body` variabeln.

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

## <a name="recognize-emotions-sample-response"></a>Identifiera emotikoner Exempelsvar
Lyckade anrop returnerar en matris av framsidan poster och deras associerade känslo poäng rangordnas efter ansikte rektangel storlek i fallande ordning. Ett tomt svar anger att ingen ytor upptäcktes. Känslo-posten innehåller följande fält:
* faceRectangle - placering av ansikte i avbildningen.
* resultat - Place poängen för varje yta i bilden. 

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
