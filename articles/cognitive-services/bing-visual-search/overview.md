---
title: Vad är Bing Visual Search?
titleSuffix: Azure Cognitive Services
description: Bing Visual Search ger information eller insikter om en bild som likartade bilder eller shoppingkällor.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: overview
ms.date: 04/10/2018
ms.author: scottwhi
ms.openlocfilehash: 9e323e9ec928d9f8b0592850adcd088a589ebf28
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55863099"
---
# <a name="what-is-the-bing-visual-search-api"></a>Vad är API:et för visuell sökning i Bing?

API:et för visuell sökning i Bing ger liknande avbildningsdetaljer som dem visas på Bing.com/images. Om du laddar upp en bild eller tillhandahåller URL-adressen till en bild kan detta API identifiera en mängd information om bilden, inklusive snarlika bilder, shoppingkällor, webbsidor som innehåller bilden och mycket mer. Om du använder [API för bildsökning i Bing](../bing-image-search/overview.md) kan du använda token för insikter som är bifogade till API-sökresultatet i stället för att ladda upp en bild.

## <a name="insights"></a>Insikter

Följande är de insikter som du kan upptäcka med visuell sökning:

| Insight                              | Beskrivning |
|--------------------------------------|-------------|
| Visuellt likartade bilder              | En lista med avbildningar som visuellt liknar den inmatade bilden. |
| Visuellt liknande produkter            | Produkter som är visuellt likartade för produkten som visas.            |
| Shoppingkällor                     | En lista över platser där du kan köpa objektet visas i den inmatade bilden.            |
| Närliggande sökningar                     | Relaterade sökningar som har gjorts av andra eller som är baserat på innehållet i bilden.            |
| Webbsidor som inkluderar bilden     | Webbsidor som inkluderar den inmatade bilden.            |
| Recept                              | En lista över webbsidor som innehåller recept för att laga rätten på den inmatade bilden            |

Förutom dessa insikter returnerar Visual Search också en mängd olika villkor (taggar) som härletts från den inmatade bilden. Med de här taggarna kan användarna utforska begrepp som finns i avbildningen. Om till exempel den inmatade bilden föreställer en berömd idrottare kan en av taggarna kan vara namnet på idrottaren, och en annan tagg kan vara sport. Eller om den inmatade bilden föreställer en äppelpaj kan taggarna vara äppelpaj, pajer, desserter så att användarna kan utforska närliggande begrepp.

Det visuella sökresultatet innehåller också avgränsningsrektanglar för intressanta regioner på bilden. Om bilden till exempel föreställer flera kändisar kan resultatet omfatta avgränsningsrektanglar för var och en av identifierade kändisarna på bilden. Eller om Bing känner av en produkt eller klädesplagg på bilden kan resultatet innehålla en avgränsningsruta för den produkt eller det klädesplagg som känns igen.

> [!IMPORTANT]
> Överväg att byta till ett API för visuell sökning i Bing, som ger mer omfattande insikter om du får information om bilder med hjälp av API för bildsökning i Bing.

## <a name="workflow"></a>Arbetsflöde

API:et för visuell sökning i Bing är en RESTful-webbtjänst, vilket innebär att den är enkel att anropa från alla programmeringsspråk som kan göra HTTP-begäranden och tolka JSON. Du kan använda tjänsten med hjälp av antingen REST API eller SDK.

1. Skapa ett Cognitive Services-API-konto med åtkomst till API:er för Bing-sökresultat. Om du inte har någon Azure-prenumeration kan du skapa ett konto kostnadsfritt.
2. Skicka en begäran till API:et med en giltig sökfråga.
3. Bearbeta API-svaret genom att tolka det returnerade JSON-meddelandet.


## <a name="next-steps"></a>Nästa steg

Prova först med den [interaktiva demonstrationen](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/) av API:et för bildsökning i Bing.
Den här demonstrationen visar hur du snabbt kan anpassa en sökfråga och finkamma webben i jakt på bilder.

När du är redo att anropa API:et skapar du ett [Cognitive Services-API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Om du inte har någon Azure-prenumeration kan du [skapa ett konto](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) kostnadsfritt.

Kom igång snabbt med din första begäran genom att läsa snabbstarterna: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md).


## <a name="see-also"></a>Se även

* Referensdokumentationen för [API:et för visuell sökning i Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) innehåller definitioner och information om slutpunkterna, rubrikerna, API-svaren och frågeparametrarna som du kan använda till att begära bildsökresultat.

* I [användnings- och visningskraven för Bing](./use-and-display-requirements.md) specificeras godtagbar användning för det innehåll och den information du får via API:erna för Bing-sökning.
