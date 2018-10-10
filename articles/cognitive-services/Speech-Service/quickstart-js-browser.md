---
title: 'Snabbstart: Känna igen tal i JavaScript i en webbläsare med hjälp av Cognitive Services tal SDK'
titleSuffix: Microsoft Cognitive Services
description: Lär dig att känna igen tal i JavaScript i en webbläsare med hjälp av Cognitive Services tal SDK
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.component: Speech
ms.topic: article
ms.date: 09/24/2018
ms.author: fmegen
ms.openlocfilehash: 75dcda643741e3aeb1238f82128e4c5b058be840
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2018
ms.locfileid: "48883673"
---
# <a name="quickstart-recognize-speech-in-javascript-in-a-browser-using-the-cognitive-services-speech-sdk"></a>Snabbstart: Känna igen tal i JavaScript i en webbläsare med hjälp av Cognitive Services tal SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

I den här artikeln lär du dig att skapa en webbplats med hjälp av JavaScript-bindningen för Cognitive Services tal SDK för att transkribera tal till text.
Programmet är baserad på Microsoft Cognitive Services tal SDK ([Download version 1.0.0](https://aka.ms/csspeech/jsbrowserpackage)).

## <a name="prerequisites"></a>Förutsättningar

* En prenumerationsnyckel för Speech-tjänsten. Se [prova speech-tjänsten utan kostnad](get-started.md).
* En PC eller Mac med en fungerande mikrofon.
* En textredigerare.
* En aktuell version av Chrome eller Microsoft Edge.
* Du kan också en webbserver som stöder som är värd för PHP-skript.

## <a name="create-a-new-website-folder"></a>Skapa en ny webbplatsmapp

Skapa en ny, tom mapp. Om du vill vara värd för exemplet på en webbserver, se till att webbservern kan komma åt mappen.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>Packa upp tal-SDK för JavaScript till mappen

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Ladda ned SDK tal som en [ZIP-paketet](https://aka.ms/csspeech/jsbrowserpackage) och packa upp det till den nya mappen. Detta resulterar i två filer packas upp, d.v.s. `microsoft.cognitiveservices.speech.sdk.bundle.js` och `microsoft.cognitiveservices.speech.sdk.bundle.js.map`.
Filen senare är valfria och används för att felsöka i SDK-kod, om det behövs.

## <a name="create-an-indexhtml-page"></a>Skapa en index.html-sida

Skapa en ny fil i mappen med namnet `index.html` och öppna den här filen med en textredigerare.

1. Skapa följande HTML-stommen:

  ```html
  <html>
  <head>
      <title>Microsoft Cognitive Service Speech SDK JavaScript Quickstart</title>
  </head>
  <body>
    <!-- UI code goes here -->

    <!-- SDK reference goes here -->

    <!-- Optional authorization token request goes here -->

    <!-- Sample code goes here -->
  </body>
  </html>
  ```

1. Lägg till följande kod i Användargränssnittet i din fil under den första kommentaren:

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#uidiv)]

1. Lägg till en referens till tal-SDK

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#speechsdkref)]

1. Koppla upp hanterare för igenkänning av-knappen, igenkänningsresultatet och prenumeration relaterade fält som definierats av UI-kod:

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#quickstartcode)]

## <a name="create-the-token-source-optional"></a>Skapa token källan (valfritt)

Om du vill vara värd för webbsidan på en webbserver, kan du ge en token källa för ditt demoprogram.
På så sätt kan din prenumerationsnyckel aldrig lämnar din server samtidigt som användarna kan använda talfunktioner utan att ange någon auktoriseringskod själva.

1. Skapa en ny fil med namnet `token.php`. I det här exemplet förutsätter vi webbservern har stöd för PHP-skriptspråk. Ange följande kod:

  [!code-php[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/token.php)]

1. Redigera den `index.html` filen och Lägg till följande kod i filen:

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#authorizationfunction)]

> [!NOTE]
> Auktoriseringstoken som bara har en begränsad livslängd.
> Den här förenklat exempel visar inte hur du uppdaterar auktoriseringstoken som automatiskt. Du kan manuellt Läs in sidan eller tryck på F5 för att uppdatera som en användare.

## <a name="build-and-run-the-sample-locally"></a>Skapa och köra exemplet lokalt

Starta appen genom att dubbelklicka på filen index.html eller öppna index.html med valfri webbläsare. Det kommer att presentera en enkel GUI så att du kan ange din prenumerationsnyckel och [region](regions.md) och utlösa en bokföring med hjälp av mikrofonen.

## <a name="build-and-run-the-sample-via-a-web-server"></a>Skapa och köra exemplet via en webbserver

Om du vill starta din app, öppna valfri webbläsare och gå till den offentliga URL som du vara värd för mappen på, ange din [region](regions.md), och utlöser en bokföring med hjälp av mikrofonen. Om konfigurerat, hämtas en token från token källan.

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Leta efter det här exemplet i mappen `quickstart/js-browser`.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hämta våra exempel](speech-sdk.md#get-the-samples)
