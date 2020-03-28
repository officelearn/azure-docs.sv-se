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
ms.date: 12/19/2019
ms.author: scottwhi
ms.openlocfilehash: 52e3e65fb4f42530373decca547674a28a60f759
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446663"
---
# <a name="what-is-the-bing-visual-search-api"></a>Vad är API:et för visuell sökning i Bing?

Api:et för visuell sökning i Bing returnerar insikter för en bild. Du kan antingen ladda upp en bild eller ange en webbadress till en. Insikter är visuellt liknande bilder, shoppingkällor, webbsidor som innehåller bilden med mera. Insikter som returneras av API:et för visuell sökning i Bing liknar dem som visas på Bing.com/images. 

Om du använder API:et [för bing-bildsökning](../bing-image-search/overview.md)kan du använda insiktstoken från API:ets sökresultat för din visuella Bing-sökning i stället för att ladda upp en bild.

> [!IMPORTANT]
> Överväg att byta till ett API för visuell sökning i Bing, som ger mer omfattande insikter om du får information om bilder med hjälp av API för bildsökning i Bing.

## <a name="insights"></a>Insikter

Du kan upptäcka följande insikter med hjälp av Bing Visual Search:

| Insight                              | Beskrivning |
|--------------------------------------|-------------|
| Visuellt likartade bilder              | En lista med avbildningar som visuellt liknar den inmatade bilden. |
| Visuellt liknande produkter            | Produkter som är visuellt likartade för produkten som visas.            |
| Shoppingkällor                     | En lista över platser där du kan köpa objektet visas i den inmatade bilden.            |
| Närliggande sökningar                     | Relaterade sökningar som har gjorts av andra eller som är baserat på innehållet i bilden.            |
| Webbsidor som innehåller bilden     | Webbsidor som inkluderar den inmatade bilden.            |
| Recept                              | Webbsidor som innehåller recept för att göra skålen visas i indatabilden.            |
| Entiteter                             | Välkända människor, platser och saker. |

Förutom insikter returnerar Bing Visual Search en mängd olika termer (det villan, taggar) som härleds från indatabilden. Taggarna gör det möjligt för användare att utforska begrepp som finns i bilden. Till exempel, om indatabilden är av en berömd idrottsman, en av taggarna kan vara namnet på idrottaren, en annan tagg kan vara Sport. Om indatabilden är av en äppelpaj kan taggarna vara Apple Pie, Pies och Desserts.

Bing Visuella sökresultat innehåller också markeringsrutor för områden av intresse för bilden. Om bilden till exempel innehåller flera kändisar kan resultatet innehålla markeringsrutor för var och en av de erkända kändisarna. Om Bing känner igen en produkt eller ett klädnad i bilden kan resultatet innehålla en markeringsram för det tolkade objektet.

## <a name="workflow"></a>Arbetsflöde

API:et för visuell sökning i Bing är en RESTful-webbtjänst, vilket innebär att den är enkel att anropa från alla programmeringsspråk som kan göra HTTP-begäranden och tolka JSON. Du kan använda antingen REST API eller SDK för tjänsten.

1. Skapa ett [Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) för att komma åt API:erna för Bing-sökning. Om du inte har någon Azure-prenumeration kan du [skapa ett konto kostnadsfritt](https://azure.microsoft.com/free/). Du kan hämta din prenumerationsnyckel från [Azure-portalen](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) när du har skapat ditt konto eller [Azure-webbplats](https://azure.microsoft.com/try/cognitive-services/my-apis) efter att du har aktiverat en kostnadsfri utvärderingsversion.
2. Skicka en begäran till API:et med en giltig sökfråga.
3. Bearbeta API-svaret genom att tolka det returnerade JSON-meddelandet.

## <a name="next-steps"></a>Nästa steg

Prova först [den interaktiva demonstrationen](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/)för Bing Visual Search API .
Demon visar hur du snabbt kan anpassa en sökfråga och söka igenom webben efter bilder.

Information om hur du kommer igång snabbt med din första begäran finns i snabbstarterna: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md).

## <a name="see-also"></a>Se även

* Referensen [Bilder - Visuell sökning](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) beskriver definitioner och information om slutpunkter, begäranden, svar och frågeparametrar som du kan använda för att begära bildbaserade sökresultat.

* [Bing Search API använder och visar krav](../bing-web-search/use-display-requirements.md) anger godtagbar användning av innehåll och information som erhållits via Bing-sök-API:erna.

* Besök [hubben för Bing Search API](../bing-web-search/search-the-web.md) för att utforska andra tillgängliga API:er.