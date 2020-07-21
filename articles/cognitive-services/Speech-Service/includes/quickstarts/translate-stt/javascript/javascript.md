---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: b6829f52cb8aaac6f243d3a4a1fa7f6bc1b150d7
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86544466"
---
## <a name="prerequisites"></a>Förutsättningar

Innan du börjar:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Skapa en Azure tal-resurs<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Konfigurera utvecklings miljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md)

## <a name="create-a-new-website-folder"></a>Skapa en ny webbplatsmapp

Skapa en ny, tom mapp. Om du vill värdbasera exemplet på en webbserver ser du till att webbservern kan komma åt mappen.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>Packa upp Speech SDK för JavaScript i den mappen

Ladda ned Speech SDK som ett [.zip-paket](https://aka.ms/csspeech/jsbrowserpackage) och packa upp det i den nya mappen. Detta resulterar i att fem filer packas upp, inklusive `microsoft.cognitiveservices.speech.sdk.bundle.js` och `microsoft.cognitiveservices.speech.sdk.bundle.js.map` .
Den senare filen är valfri och är användbar för felsökning i SDK-koden.

## <a name="create-an-indexhtml-page"></a>Skapa en index.html-sida

Skapa en ny fil i mappen, med namnet `index.html` och öppna filen med en textredigerare.

1. Skapa följande HTML-stomme:

 [!code-html [SampleCode](~/samples-cognitive-services-speech-sdk/quickstart/javascript/browser/index-translate-stt.html)]

## <a name="create-the-token-source-optional"></a>Skapa tokenkälla (valfritt)

Om du vill värdbasera webbsidan på en webbserver kan du ange en tokenkälla för demoappen.
På så sätt lämnar prenumerationen aldrig servern och tillåter att användare använder talfunktioner utan att själva behöva ange en auktoriseringskod.

Skapa en ny fil med namnet `token.php`. I det här exemplet antar vi att webb servern stöder PHP-skript språket med aktive rad vändning. Ange följande kod:

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

Starta appen genom att dubbelklicka på index.html-filen eller öppna index.html med valfri webbläsare. Det visar ett enkelt användar gränssnitt som gör att du kan ange din prenumerations nyckel och din [region](../../../../regions.md) och utlösa text avskriften för indataports tal.

## <a name="build-and-run-the-sample-via-a-web-server"></a>Skapa och köra exemplet via en webbserver

Starta din app genom att öppna din favorit webbläsare och peka på den offentliga URL som du är värd för mappen på, ange din [region](../../../../regions.md)och utlösa text avskriften för Indataporten. Om det har konfigurerats hämtas en token från tokenkällan.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]