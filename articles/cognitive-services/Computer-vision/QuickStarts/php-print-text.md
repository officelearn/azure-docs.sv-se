---
title: 'Snabbstart: Extrahera tryckt text – REST, PHP'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten extraherar du tryckt text från en bild med hjälp av API:et för visuellt innehåll med PHP.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 7a7de2807829aefc3bf48f540e1ddcc08cda93c7
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60003997"
---
# <a name="quickstart-extract-printed-text-ocr-using-the-rest-api-and-php-in-computer-vision"></a>Snabbstart: Extrahera utskriven text (OCR) med hjälp av REST API och PHP i Visuellt innehåll

I den här snabbstarten extraherar du tryckt text med optisk teckenläsning (OCR) från en bild med hjälp av REST API:et för visuellt innehåll. Med metoden [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) kan du identifiera tryckt text i en bild och extrahera de tecken som identifieras till en teckenström som kan användas på en dator.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Du måste ha [PHP](https://secure.php.net/downloads.php) installerat.
- Du måste ha [Pear](https://pear.php.net) installerat.
- Du måste ha en prenumerationsnyckel för Visuellt innehåll. Du kan få en kostnadsfri utvärderingsversion nyckel från [prova Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Eller följ instruktionerna i [skapa ett Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) att prenumerera på visuellt innehåll och få din nyckel.

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
    1. Ersätt värdet för `uriBase` med slutpunktsadressen för metoden [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) från den Azure-region där du fått dina prenumerationsnycklar om det behövs.
    1. Du kan också ersätta värdet för `imageUrl` med webbadressen till en annan bild som du vill extrahera tryckt text ur.
1. Spara koden som en fil med tillägget `.php`. Till exempel `get-printed-text.php`.
1. Öppna ett webbläsarfönster med PHP-stöd.
1. Dra och släpp filen till webbläsarfönstret.

```php
<?php
<html>
<head>
    <title>OCR Sample</title>
</head>
<body>
<?php
// Replace <Subscription Key> with a valid subscription key.
$ocpApimSubscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to obtain
// your subscription keys. For example, if you obtained your subscription keys
// from westus, replace "westcentralus" in the URL below with "westus".
$uriBase = 'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/';

$imageUrl = 'https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/' .
    'Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png';

require_once 'HTTP/Request2.php';

$request = new Http_Request2($uriBase . 'ocr');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',
    'Ocp-Apim-Subscription-Key' => $ocpApimSubscriptionKey
);
$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'language' => 'unk',
    'detectOrientation' => 'true'
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
    "language": "en",
    "orientation": "Up",
    "textAngle": 0,
    "regions": [
        {
            "boundingBox": "21,16,304,451",
            "lines": [
                {
                    "boundingBox": "28,16,288,41",
                    "words": [
                        {
                            "boundingBox": "28,16,288,41",
                            "text": "NOTHING"
                        }
                    ]
                },
                {
                    "boundingBox": "27,66,283,52",
                    "words": [
                        {
                            "boundingBox": "27,66,283,52",
                            "text": "EXISTS"
                        }
                    ]
                },
                {
                    "boundingBox": "27,128,292,49",
                    "words": [
                        {
                            "boundingBox": "27,128,292,49",
                            "text": "EXCEPT"
                        }
                    ]
                },
                {
                    "boundingBox": "24,188,292,54",
                    "words": [
                        {
                            "boundingBox": "24,188,292,54",
                            "text": "ATOMS"
                        }
                    ]
                },
                {
                    "boundingBox": "22,253,297,32",
                    "words": [
                        {
                            "boundingBox": "22,253,105,32",
                            "text": "AND"
                        },
                        {
                            "boundingBox": "144,253,175,32",
                            "text": "EMPTY"
                        }
                    ]
                },
                {
                    "boundingBox": "21,298,304,60",
                    "words": [
                        {
                            "boundingBox": "21,298,304,60",
                            "text": "SPACE."
                        }
                    ]
                },
                {
                    "boundingBox": "26,387,294,37",
                    "words": [
                        {
                            "boundingBox": "26,387,210,37",
                            "text": "Everything"
                        },
                        {
                            "boundingBox": "249,389,71,27",
                            "text": "else"
                        }
                    ]
                },
                {
                    "boundingBox": "127,431,198,36",
                    "words": [
                        {
                            "boundingBox": "127,431,31,29",
                            "text": "is"
                        },
                        {
                            "boundingBox": "172,431,153,36",
                            "text": "opinion."
                        }
                    ]
                }
            ]
        }
    ]
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

Utforska den API för visuellt innehåll för att analysera en bild, identifiera kändisar och landmärken, skapa en miniatyrbild och extrahera utskrivna och handskriven text. Du kan experimentera med API för visuellt innehåll i [Open API-testkonsolen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Utforska API för visuellt innehåll](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
