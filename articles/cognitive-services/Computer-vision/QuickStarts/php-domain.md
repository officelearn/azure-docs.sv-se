---
title: 'Snabbstart: Domänspecifikt bildinnehåll – REST, PHP'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten använder du en domänmodell för att identifiera landmärken i en bild med hjälp av API:et för Visuellt innehåll med PHP.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 86b2d7ae385fb7ad9342cf2067283ff8dc69762f
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55210973"
---
# <a name="quickstart-recognize-domain-specific-content-using-the-rest-api-and-php-with-computer-vision"></a>Snabbstart: Identifiera domänspecifikt innehåll med REST-API och PHP med visuellt innehåll

I den här snabbstarten använder du en domänmodell för att identifiera landmärken eller kändisar i en fjärrlagrad bild med hjälp av REST API för visuellt innehåll. Med metoden [Recognize Domain Specific Content](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) (Identifiera domänspecifikt innehåll) kan du tillämpa en domänspecifik modell för att känna igen innehåll i en bild.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Du måste ha [PHP](https://secure.php.net/downloads.php) installerat.
- Du måste ha [Pear](https://pear.php.net) installerat.
- Du måste ha en prenumerationsnyckel för Visuellt innehåll. Du kan skaffa en prenumerationsnyckel genom att följa anvisningarna i [Skaffa prenumerationsnycklar](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="create-and-run-the-sample"></a>Skapa och köra exemplet

Så här skapar du och kör exemplet:

1. Installera PHP5-paketet [`HTTP_Request2`](https://pear.php.net/package/HTTP_Request2).
   1. Öppna ett kommandotolksfönster som administratör.
   1. Kör följande kommando:

      ```console
      pear install HTTP_Request2
      ```

   1. Stäng kommandotolksfönstret när paketet har installerats.

1. Kopiera följande kod till en textredigerare.
1. Gör nedanstående ändringar i koden där det behövs:
    1. Ersätt värdet för `subscriptionKey` med din prenumerationsnyckel.
    1. Ersätt värdet för `uriBase` med slutpunktsadressen för metoden [Identifiera domänspecifikt innehåll](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) från den Azure-region där du fått dina prenumerationsnycklar om det behövs.
    1. Du kan också ersätta värdet för `imageUrl` med webbadressen till en annan bild som du vill analysera.
    1. Du kan också ersätta värdet för parametern `domain` för begäran med `celebrites` om du vill använda domänmodellen `celebrities` i stället för `landmarks`.
1. Spara koden som en fil med tillägget `.php`. Till exempel `use-domain-model.php`.
1. Öppna ett webbläsarfönster med PHP-stöd.
1. Dra och släpp filen till webbläsarfönstret.

```php
<html>
<head>
    <title>Analyze Domain Model Sample</title>
</head>
<body>
<?php
// Replace <Subscription Key> with a valid subscription key.
$ocpApimSubscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to obtain
// your subscription keys. For example, if you obtained your subscription keys
// from westus, replace "westcentralus" in the URL below with "westus".
$uriBase = 'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/';

// Change 'landmarks' to 'celebrities' to use the Celebrities model.
$domain = 'landmarks';

$imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/2/23/Space_Needle_2011-07-04.jpg';

require_once 'HTTP/Request2.php';

$request = new Http_Request2($uriBase . 'models/' . $domain . '/analyze');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',
    'Ocp-Apim-Subscription-Key' => $ocpApimSubscriptionKey
);
$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'model' => $domain
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

## <a name="examine-the-response"></a>Granska svaret

Ett svar som anger att åtgärden lyckades returneras i JSON. Exempelwebbplatsen parsar och visar ett lyckat svar i webbläsarfönstret enligt följande exempel:

```json
{
    "result": {
        "landmarks": [
            {
                "name": "Space Needle",
                "confidence": 0.9998177886009216
            }
        ]
    },
    "requestId": "4d26587b-b2b9-408d-a70c-1f8121d84b0d",
    "metadata": {
        "height": 4132,
        "width": 2096,
        "format": "Jpeg"
    }
}
```

## <a name="clean-up-resources"></a>Rensa resurser

När du inte behöver filen längre kan du ta bort den och sedan avinstallera PHP5-paketet `HTTP_Request2`. Avinstallera paketet på följande sätt:

1. Öppna ett kommandotolksfönster som administratör.
2. Kör följande kommando:

   ```console
   pear uninstall HTTP_Request2
   ```

3. Stäng kommandotolksfönstret när paketet har avinstallerats.

## <a name="next-steps"></a>Nästa steg

Utforska API:et för visuellt innehåll, som används för att analysera en bild, identifiera kändisar och landmärken, skapa en miniatyrbild och extrahera tryckt och handskriven text. Du kan experimentera med API för visuellt innehåll i [Open API-testkonsolen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Utforska API för visuellt innehåll](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
