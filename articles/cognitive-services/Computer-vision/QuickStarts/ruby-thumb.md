---
title: 'Snabbstart: API:et för visuellt innehåll och Ruby | Microsoft Docs'
titleSuffix: Microsoft Cognitive Services
description: I den här snabbstarten skapar du en miniatyrbild från en bild med hjälp av Visuellt innehåll med Ruby i Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: a5f6c345b3e1f5e4dab923d43dd239344c66aab1
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2018
ms.locfileid: "43772143"
---
# <a name="quickstart-generate-a-thumbnail---rest-ruby"></a>Snabbstart: Skapa en miniatyrbild – REST, Ruby

I den här snabbstarten skapar du en miniatyrbild från en bild med hjälp av Visuellt innehåll.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att använda Visuellt innehåll behöver du en prenumerationsnyckel. Mer information finns i avsnittet [Obtaining Subscription Keys](../Vision-API-How-to-Topics/HowToSubscribe.md) (Hämta prenumerationsnycklar).

## <a name="get-thumbnail-request"></a>Begäran om att hämta miniatyrbild

Med metoden [Get Thumbnail](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) (Hämta miniatyrbild) kan du skapa en miniatyrbild av en bild. Du anger höjden och bredden, som kan skilja sig från den ursprungliga bildens proportioner. Visuellt innehåll använder smart beskärning för att identifiera det område som är intressant och generera koordinater för beskärning baserat på det området.

För att köra exemplet följer du dessa steg:

1. Kopiera följande kod till ett redigeringsprogram.
1. Ersätt `<Subscription Key>` med en giltig prenumerationsnyckel.
1. Ändra värdet `uri` till den plats där du hämtade dina prenumerationsnycklar om det behövs.
1. Du kan också ändra bilden (`{\"url\":\"...`) som ska analyseras om du vill.
1. Spara filen med tillägget `.rb`.
1. Öppna Ruby-kommandotolken och kör filen, till exempel: `ruby myfile.rb`.

```ruby
require 'net/http'

# You must use the same location in your REST call as you used to get your
# subscription keys. For example, if you got your subscription keys from westus,
# replace "westcentralus" in the URL below with "westus".
uri = URI('https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail')
uri.query = URI.encode_www_form({
    # Request parameters
    'width' => '100',
    'height' => '100',
    'smartCropping' => 'true'
})

request = Net::HTTP::Post.new(uri.request_uri)

# Request headers
# Replace <Subscription Key> with your valid subscription key.
request['Ocp-Apim-Subscription-Key'] = '<Subscription Key>'
request['Content-Type'] = 'application/json'

request.body =
    "{\"url\": \"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/" +
    "Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request(request)
end

#puts response.body
```

## <a name="get-thumbnail-response"></a>Svar från miniatyrhämtning

Om åtgärden lyckas innehåller svaret binärfilen för miniatyrbilden. Om begäran misslyckas innehåller svaret en felkod och ett meddelande som beskriver vad som gick fel.

## <a name="next-steps"></a>Nästa steg

Utforska API:erna för visuellt innehåll som används för att analysera en bild, identifiera kändisar och landmärken, skapa en miniatyrbild och extrahera tryckt och handskriven text. Du kan snabbt experimentera med API:erna för visuellt innehåll genom att prova [Open API-testkonsolen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Utforska API:er för visuellt innehåll](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
