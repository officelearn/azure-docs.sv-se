---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: 155d1833e7fe910a0a93aa5cbaa0a22d02c60082
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2020
ms.locfileid: "85838989"
---
## <a name="prerequisites"></a>Förutsättningar

Innan du börjar:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Skapa en Azure tal-resurs<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Konfigurera utvecklings miljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md)
> * Kontrol lera att du har åtkomst till en mikrofon för ljud inspelning

## <a name="create-a-new-website-folder"></a>Skapa en ny webbplatsmapp

Skapa en ny, tom mapp. Om du vill värdbasera exemplet på en webbserver ser du till att webbservern kan komma åt mappen.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>Packa upp Speech SDK för JavaScript i den mappen

Ladda ned Speech SDK som ett [.zip-paket](https://aka.ms/csspeech/jsbrowserpackage) och packa upp det i den nya mappen. Detta resulterar i två filer packas upp, `microsoft.cognitiveservices.speech.sdk.bundle.js` och `microsoft.cognitiveservices.speech.sdk.bundle.js.map`.
Den senare filen är valfri och är användbar för felsökning i SDK-koden.

## <a name="create-an-indexhtml-page"></a>Skapa en index.html-sida

Skapa en ny fil i mappen, med namnet `index.html` och öppna filen med en textredigerare.

1. Skapa följande HTML-stomme:

 [!code-html [SampleCode](~/samples-cognitive-services-speech-sdk/quickstart/javascript/browser/index-from-mic.html)]

## <a name="create-the-token-source-optional"></a>Skapa tokenkälla (valfritt)

Om du vill värdbasera webbsidan på en webbserver kan du ange en tokenkälla för demoappen.
På så sätt lämnar prenumerationen aldrig servern och tillåter att användare använder talfunktioner utan att själva behöva ange en auktoriseringskod.

Skapa en ny fil med namnet `token.php`. I det här exemplet förutsätter vi att webbservern stöder PHP-skriptspråket. Ange följande kod:

```php
<?php
header('Access-Control-Allow-Origin: ' . $_SERVER['SERVER_NAME']);

// Replace with your own subscription key and service region (e.g., "westus").
$subscriptionKey = 'YourSubscriptionKey';
$region = 'YourServiceRegion';

$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, 'https://' . $region . '.api.cognitive.microsoft.com/sts/v1.0/issueToken');
curl_setopt($ch, CURLOPT_POST, 1);
curl_setopt($ch, CURLOPT_POSTFIELDS, '{}');
curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: application/json', 'Ocp-Apim-Subscription-Key: ' . $subscriptionKey));
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
echo curl_exec($ch);
?>
```

> [!NOTE]
> Auktoriseringstoken har en begränsad livslängd.
> Det här förenklade exemplet visar inte hur du uppdaterar auktoriseringstoken automatiskt. Som användare kan du manuellt läsa in sidan igen eller trycka på F5 för att uppdatera.

## <a name="build-and-run-the-sample-locally"></a>Skapa och köra exemplet lokalt

Starta appen genom att dubbelklicka på index.html-filen eller öppna index.html med valfri webbläsare. Ett enkelt grafiskt användargränssnitt visas där du kan ange din prenumerationsnyckel och [region](../../../../regions.md) och utlösa en igenkänning med mikrofonen.

> [!NOTE]
> Den här metoden fungerar inte i Safari-webbläsaren.
> På Safari måste exempel webb sidan finnas på en webb server. Safari tillåter inte att webbplatser läses in från en lokal fil för att använda mikrofonen.

## <a name="build-and-run-the-sample-via-a-web-server"></a>Skapa och köra exemplet via en webbserver

Du kan starta appen genom att öppna valfri webbläsare och leda den till den offentliga URL där mappen finns, ange din [region](../../../../regions.md) och utlösa en igenkänning med mikrofonen. Om det har konfigurerats hämtas en token från tokenkällan.


## <a name="next-steps"></a>Nästa steg

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

