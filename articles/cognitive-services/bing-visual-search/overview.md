---
title: Vad är API:et för visuell sökning i Bing?
titleSuffix: Azure Cognitive Services
description: Bing Visual Search ger information eller insikter om en bild som likartade bilder eller shoppingkällor.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: overview
ms.date: 03/27/2019
ms.author: scottwhi
ms.openlocfilehash: 8bcb0372ebb60ac3a46cf06bf85322b288e153ba
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630214"
---
# <a name="what-is-the-bing-visual-search-api"></a>Vad är API:et för visuell sökning i Bing?

Bing Visual Search API returnerar insikter för en bild. Du kan ladda upp en bild, eller så kan du ange en URL till en. Insikterna är snarlika bilder, perioder källor, webbsidor som innehåller bilden och mycket mer. Insikter som returneras av Bing Visual Search API liknar de som visas på Bing.com/images.

Om du använder den [bildsökning i Bing](../bing-image-search/overview.md), du kan använda insight-token från det API: et sökresultaten för dina Bing Visual Search i stället för att ladda upp en avbildning.

> [!IMPORTANT]
> Överväg att byta till ett API för visuell sökning i Bing, som ger mer omfattande insikter om du får information om bilder med hjälp av API för bildsökning i Bing.

## <a name="insights"></a>Insikter

Du kan identifiera följande insikter med hjälp av Bing Visual Search:

| Insight                              | Beskrivning |
|--------------------------------------|-------------|
| Visuellt likartade bilder              | En lista med avbildningar som visuellt liknar den inmatade bilden. |
| Visuellt liknande produkter            | Produkter som är visuellt likartade för produkten som visas.            |
| Shoppingkällor                     | En lista över platser där du kan köpa objektet visas i den inmatade bilden.            |
| Närliggande sökningar                     | Relaterade sökningar som har gjorts av andra eller som är baserat på innehållet i bilden.            |
| Webbsidor som innehåller bilden     | Webbsidor som inkluderar den inmatade bilden.            |
| Recept                              | Webbsidor som innehåller recept för att göra rätten på inkommande bild.            |

Utöver insyn returnerar Bing Visual Search en mängd olika termer (det vill säga taggar) som härletts från inmatad bild. Taggar kan du utforska koncepten finns i avbildningen. Till exempel om det är avbildningen av en berömda Idrottare, en av taggarna kan vara namnet på Idrottare, en annan tagg kan vara sport. Eller om det är avbildningen av ett apple-cirkel, taggarna kan vara Apple cirkel, Pies och efterrätter.

Bing Visual Search-resultat även innehålla avgränsar rutorna för regioner intressanta i avbildningen. Till exempel om avbildningen innehåller flera kändisar, kan resultaten omfatta avgränsar rutorna för var och en av de identifierade kändisarna. Eller, om Bing känner av en produkt eller kläder i avbildningen kan resultatet kan innehålla en avgränsningsruta för det identifierade objektet.

## <a name="workflow"></a>Arbetsflöde

API:et för visuell sökning i Bing är en RESTful-webbtjänst, vilket innebär att den är enkel att anropa från alla programmeringsspråk som kan göra HTTP-begäranden och tolka JSON. Du kan använda REST-API: et eller SDK: N för tjänsten.

1. Skapa en [Cognitive Services-kontot](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) att få åtkomst till API: er för Bing-sökresultat. Om du inte har någon Azure-prenumeration kan du [skapa ett konto kostnadsfritt](https://azure.microsoft.com/free/). Du kan hämta din prenumerationsnyckel från den [Azure-portalen](https://docs.microsoft.com/en-us/azure/cognitive-services/cognitive-services-apis-create-account#access-your-resource) när du har skapat ditt konto eller [Azure-webbplatsen](https://azure.microsoft.com/try/cognitive-services/my-apis) efter aktivering av en kostnadsfri utvärderingsversion.
2. Skicka en begäran till API: et med en giltig sökfråga.
3. Bearbeta API-svaret genom att tolka det returnerade JSON-meddelandet.

## <a name="next-steps"></a>Nästa steg

Försök först Bing Visual Search API [Interaktiv demo](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/).
Den här presentationen beskrivs hur du snabbt anpassa en sökfråga och finkamma webben i jakt avbildningar.

Kom igång snabbt med din första begäran genom att läsa snabbstarterna: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md).

## <a name="see-also"></a>Se också

* Den [avbildningar - Visual Search](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) ref definitioner och information om slutpunkter, begärandehuvuden, svar, och frågeparametrar som du kan använda på begäran avbildningsbaserad sökresultat.

* Den [i Bing använder och visa kraven](../bing-web-search/use-display-requirements.md) Ange tillåten användning av innehåll och information som du får via API: er för Bing-sökning.
