---
title: 'Snabbstart: Skicka sökfrågor med hjälp av REST-API för sökning i Bing med hjälp av PHP'
description: I den här snabbstarten har skickar du sökfrågor till Bing Search API att hämta en lista över relevanta avbildningar med hjälp av PHP.
services: cognitive-services
documentationcenter: ''
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: d91021c4bd5e0f78e518811f3794055b397c1a39
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2018
ms.locfileid: "41988142"
---
# <a name="quickstart-send-search-queries-using-the-rest-api-and-php"></a>Snabbstart: Skicka sökfrågor med hjälp av REST API och PHP

Sökning i Bing ger en upplevelse som liknar Bing.com/Images genom att låta dig skicka en sökfråga för användaren till Bing och få tillbaka en lista över relevanta avbildningar.

Den här artikeln innehåller ett enkelt konsolprogram som utför en fråga för bildsökning i Bing och visar de returnerade raw sökresultat, som är i JSON-format. Även om det här programmet är skriven i PHP, är API: et en RESTful-webb-tjänst som är kompatibel med alla programmeringsspråk som kan göra HTTP-begäranden och parsa JSON. 

## <a name="prerequisites"></a>Förutsättningar

Du behöver [PHP 5.6.x](http://php.net/downloads.php) att köra den här koden.

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="running-the-application"></a>Köra programmet

Följ dessa steg om du vill köra det här programmet.

1. Se till att säkra HTTP-stöd är aktiverat i din `php.ini` enligt beskrivningen i kodkommentar. På Windows, är den här filen i `C:\windows`.
2. Skapa ett nytt PHP-projekt i din favorit-IDE eller redigerare.
3. Lägg till den angivna koden.
4. Ersätt den `accessKey` värde med en giltig åtkomstnyckel för din prenumeration.
5. Kör programmet.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the accessKey string value with your valid access key.
$accessKey = 'enter key here';

// Verify the endpoint URI.  At this writing, only one endpoint is used for Bing
// search APIs.  In the future, regional endpoints may be available.  If you
// encounter unexpected authorization errors, double-check this value against
// the endpoint for your Bing Search instance in your Azure dashboard.
$endpoint = 'https://api.cognitive.microsoft.com/bing/v7.0/images/search';

$term = 'puppies';

function BingImageSearch ($url, $key, $query) {
    // Prepare HTTP request
    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // http://php.net/manual/en/function.stream-context-create.php
    $headers = "Ocp-Apim-Subscription-Key: $key\r\n";
    $options = array ( 'http' => array (
                           'header' => $headers,
                           'method' => 'GET' ));

    // Perform the Web request and get the JSON response
    $context = stream_context_create($options);
    $result = file_get_contents($url . "?q=" . urlencode($query), false, $context);

    // Extract Bing HTTP headers
    $headers = array();
    foreach ($http_response_header as $k => $v) {
        $h = explode(":", $v, 2);
        if (isset($h[1]))
            if (preg_match("/^BingAPIs-/", $h[0]) || preg_match("/^X-MSEdge-/", $h[0]))
                $headers[trim($h[0])] = trim($h[1]);
    }

    return array($headers, $result);
}

if (strlen($accessKey) == 32) {

    print "Searching images for: " . $term . "\n";
    
    list($headers, $json) = BingImageSearch($endpoint, $accessKey, $term);
    
    print "\nRelevant Headers:\n\n";
    foreach ($headers as $k => $v) {
        print $k . ": " . $v . "\n";
    }
    
    print "\nJSON Response:\n\n";
    echo json_encode(json_decode($json), JSON_PRETTY_PRINT);

} else {

    print("Invalid Bing Search API subscription key!\n");
    print("Please paste yours into the source code.\n");

}
?>
```

## <a name="json-response"></a>JSON-svar

Ett svar i exemplet nedan. Om du vill begränsa storleken på JSON, visas bara ett enskilt resultat och andra delar av svaret har trunkerats. 

```json
{
  "_type": "Images",
  "instrumentation": {},
  "readLink": "https://api.cognitive.microsoft.com/api/v7/images/search?q=puppies",
  "webSearchUrl": "https://www.bing.com/images/search?q=puppies&FORM=OIIARP",
  "totalEstimatedMatches": 955,
  "nextOffset": 1,
  "value": [
    {
      "webSearchUrl": "https://www.bing.com/images/search?view=detailv...",
      "name": "So cute - Puppies Wallpaper",
      "thumbnailUrl": "https://tse3.mm.bing.net/th?id=OIP.jHrihoDNkXGS1t...",
      "datePublished": "2014-02-01T21:55:00.0000000Z",
      "contentUrl": "http://images4.contoso.com/image/photos/14700000/So-cute-puppies...",
      "hostPageUrl": "http://www.contoso.com/clubs/puppies/images/14749028/...",
      "contentSize": "394455 B",
      "encodingFormat": "jpeg",
      "hostPageDisplayUrl": "www.contoso.com/clubs/puppies/images/14749...",
      "width": 1600,
      "height": 1200,
      "thumbnail": {
        "width": 300,
        "height": 225
      },
      "imageInsightsToken": "ccid_jHrihoDN*mid_F68CC526226E163FD1EA659747AD...",
      "insightsMetadata": {
        "recipeSourcesCount": 0
      },
      "imageId": "F68CC526226E163FD1EA659747ADCB8F9FA36",
      "accentColor": "8D613E"
    }
  ],
  "queryExpansions": [
    {
      "text": "Shih Tzu Puppies",
      "displayText": "Shih Tzu",
      "webSearchUrl": "https://www.bing.com/images/search?q=Shih+Tzu+Puppies...",
      "searchLink": "https://api.cognitive.microsoft.com/api/v7/images/search?q=Shih...",
      "thumbnail": {
        "thumbnailUrl": "https://tse2.mm.bing.net/th?q=Shih+Tzu+Puppies&pid=Api..."
      }
    }
  ],
  "pivotSuggestions": [
    {
      "pivot": "puppies",
      "suggestions": [
        {
          "text": "Dog",
          "displayText": "Dog",
          "webSearchUrl": "https://www.bing.com/images/search?q=Dog&tq=%7b%22pq%...",
          "searchLink": "https://api.cognitive.microsoft.com/api/v7/images/search?q=Dog...",
          "thumbnail": {
            "thumbnailUrl": "https://tse1.mm.bing.net/th?q=Dog&pid=Api&mkt=en-US..."
          }
        }
      ]
    }
  ],
  "similarTerms": [
    {
      "text": "cute",
      "displayText": "cute",
      "webSearchUrl": "https://www.bing.com/images/search?q=cute&FORM=...",
      "thumbnail": {
        "url": "https://tse2.mm.bing.net/th?q=cute&pid=Api&mkt=en-US..."
      }
    }
  ],
  "relatedSearches": [
    {
      "text": "Cute Puppies",
      "displayText": "Cute Puppies",
      "webSearchUrl": "https://www.bing.com/images/search?q=Cute+Puppies",
      "searchLink": "https://api.cognitive.microsoft.com/api/v7/images/sear...",
      "thumbnail": {
        "thumbnailUrl": "https://tse4.mm.bing.net/th?q=Cute+Puppies&pid=..."
      }
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Bildsökning i Bing ensidesapp självstudien](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Se också 

[Bildsökning i Bing-översikt](../overview.md)  
[Prova](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
[Hämta en kostnadsfri utvärderingsversion åtkomstnyckel](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
[Referens för bildsökning i Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
