---
title: 'Snabbstart: Skapa en miniatyrbild – REST, PHP'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten genererar du en miniatyrbild från en bild med hjälp av API för visuellt innehåll och PHP.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 7239ae1bdad59b87f316d3ee27213420447fcc7e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57839482"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-php-in-computer-vision"></a>Snabbstart: Generera en miniatyrbild med hjälp av REST-API:t och PHP i Visuellt innehåll

I den här snabbstarten genererar du en miniatyrbild från en bild med hjälp av REST-API:t i Visuellt innehåll. Med metoden [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) kan du generera en miniatyrbild av en bild. Du anger höjden och bredden, som kan skilja sig från den ursprungliga bildens proportioner. Visuellt innehåll använder smart beskärning för att identifiera det område som är intressant och generera koordinater för beskärning baserat på det området.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

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
    1. Ersätt värdet för `uriBase` med slutpunkt-URL:en för metoden [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) från Azure-regionen där du fått dina prenumerationsnycklar, om så krävs.
    1. Du kan också ersätta värdet för `imageUrl` med webbadressen till en annan bild som du vill generera en miniatyrbild för.
1. Spara koden som en fil med tillägget `.php`. Till exempel `get-thumbnail.php`.
1. Öppna ett webbläsarfönster med PHP-stöd.
1. Dra och släpp filen till webbläsarfönstret.

```php
<html>
<head>
    <title>Get Thumbnail Sample</title>
</head>
<body>
<?php
// Replace <Subscription Key> with a valid subscription key.
$ocpApimSubscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to obtain
// your subscription keys. For example, if you obtained your subscription keys
// from westus, replace "westcentralus" in the URL below with "westus".
$uriBase = 'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/';

$imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg';

require_once 'HTTP/Request2.php';

$request = new Http_Request2($uriBase . 'generateThumbnail');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',
    'Ocp-Apim-Subscription-Key' => $ocpApimSubscriptionKey
);
$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'width' => '100',  // Width of the thumbnail.
    'height' => '100', // Height of the thumbnail.
    'smartCropping' => 'true',
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

Ett lyckat svar returneras i form av binärdata som representerar bilddata för miniatyrbilden. Om begäran misslyckas visas svaret i webbläsarfönstret. Svaret för en misslyckad begäran innehåller en felkod och ett meddelande som hjälper dig att avgöra vad som gick fel.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte behöver filen längre kan du ta bort den och sedan avinstallera PHP5-paketet `HTTP_Request2`. Avinstallera paketet på följande sätt:

1. Öppna ett kommandotolksfönster som administratör.
2. Kör följande kommando:

   ```console
   pear uninstall HTTP_Request2
   ```

3. Stäng kommandotolksfönstret när paketet har avinstallerats.

## <a name="next-steps"></a>Nästa steg

Utforska den API för visuellt innehåll för att analysera en bild, identifiera kändisar och landmärken, skapa en miniatyrbild och extrahera utskrivna och handskriven text. Du kan experimentera med API för visuellt innehåll i [Open API-testkonsolen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Utforska API för visuellt innehåll](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
