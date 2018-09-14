---
title: 'Snabbstart: Analysera bilder med API:et för visuellt innehåll och PHP | Microsoft Docs'
titleSuffix: Microsoft Cognitive Services
description: I den här snabbstarten ska du analysera en bild med hjälp av Visuellt innehåll och PHP i Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 114674f47ee945717e866d97ffed747ae45decc8
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2018
ms.locfileid: "43771947"
---
# <a name="quickstart-analyze-a-remote-image---rest-php"></a>Snabbstart: Analysera en fjärrbild – REST, PHP

I den här snabbstarten ska du analysera en bild för att extrahera visuella funktioner med hjälp av Visuellt innehåll.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att använda Visuellt innehåll behöver du en prenumerationsnyckel. Mer information finns i avsnittet [Obtaining Subscription Keys](../Vision-API-How-to-Topics/HowToSubscribe.md) (Hämta prenumerationsnycklar).

## <a name="analyze-image-request"></a>Bildanalysbegäran

Med metoden [Analyze Image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) (Analysera bild) kan du extrahera visuella funktioner baserat på bildinnehåll. Du kan ladda upp en bild eller ange en bild-URL och välja vilka funktioner som ska returneras, inklusive:

* En detaljerad lista över taggar relaterade till bildinnehållet.
* En beskrivning av bildinnehållet i en fullständig mening.
* Koordinater, kön och ålder för ansikten som finns i bilden.
* Bildtyp (ClipArt eller en linjeteckning).
* Den mest framträdande färgen, accentfärgen eller huruvida en bild är svartvit.
* Kategorin som definierats i den här [taxonomin](../Category-Taxonomy.md).
* Innehåller bilden innehåll som inte är lämpligt för barn?

För att köra exemplet följer du dessa steg:

1. Kopiera följande kod till ett redigeringsprogram.
1. Ersätt `<Subscription Key>` med en giltig prenumerationsnyckel.
1. Ändra `uriBase` till den plats där du hämtade dina prenumerationsnycklar om det behövs.
1. Du kan också ange `imageUrl` till den bild som du vill analysera.
1. Du kan också ändra språket för svar (`'language' => 'en'`).
1. Spara filen med tillägget `.php`.
1. Öppna filen i ett webbläsarfönster med PHP-stöd.

Det här exemplet använder [HTTP_Request2](http://pear.php.net/package/HTTP_Request2)-paketet för PHP5.

```php
<html>
<head>
    <title>Analyze Image Sample</title>
</head>
<body>
<?php
// Replace <Subscription Key> with a valid subscription key.
$ocpApimSubscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to obtain
// your subscription keys. For example, if you obtained your subscription keys
// from westus, replace "westcentralus" in the URL below with "westus".
$uriBase = 'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/';

$imageUrl = 'http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg';

require_once 'HTTP/Request2.php';

$request = new Http_Request2($uriBase . '/analyze');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',
    'Ocp-Apim-Subscription-Key' => $ocpApimSubscriptionKey
);
$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'visualFeatures' => 'Categories,Description',
    'details' => '',
    'language' => 'en'
);
$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body parameters
$body = json_encode(array('url' => $imageUrl));

// Request body
$request->setBody($body);

try
{
    $response = $request->send();
    echo "<pre>" .
        json_encode(json_decode($response->getBody()), JSON_PRETTY_PRINT) . "</pre>";
}
catch (HttpException $ex)
{
    echo "<pre>" . $ex . "</pre>";
}
?>
</body>
</html>
```

## <a name="analyze-image-response"></a>Bildanalyssvar

Ett svar som anger att åtgärden lyckades returneras i JSON, till exempel:

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "requestId": "ebf5a1bc-3ba2-4c56-99b4-bbd20ba28705",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="next-steps"></a>Nästa steg

Utforska API:erna för visuellt innehåll som används för att analysera en bild, identifiera kändisar och landmärken, skapa en miniatyrbild och extrahera tryckt och handskriven text. Du kan snabbt experimentera med API:erna för visuellt innehåll genom att prova [Open API-testkonsolen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Utforska API:er för visuellt innehåll](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
