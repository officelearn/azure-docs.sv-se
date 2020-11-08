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
ms.openlocfilehash: 50db66d54993a4b5ee8afb4756be6b1f4387a1d0
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369349"
---
# <a name="what-is-the-bing-visual-search-api"></a>Vad är API:et för visuell sökning i Bing?

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](https://aka.ms/cogsvcs/bingmove).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](https://aka.ms/cogsvcs/bingmigration).

API för visuell sökning i Bing returnerar insikter för en bild. Du kan antingen ladda upp en avbildning eller ange en URL till en. Insikter är visuellt likartade bilder, shopping källor, webb sidor som innehåller avbildningen med mera. Insikter som returneras av API för visuell sökning i Bing liknar de som visas på Bing.com/images. 

Om du använder [API för bildsökning i Bing](../bing-image-search/overview.md)kan du använda Insight-token från det API: s Sök Resultat för din visuell sökning i Bing i stället för att ladda upp en avbildning.

> [!IMPORTANT]
> Överväg att byta till ett API för visuell sökning i Bing, som ger mer omfattande insikter om du får information om bilder med hjälp av API för bildsökning i Bing.

## <a name="insights"></a>Insikter

Du kan identifiera följande insikter genom att använda Visuell sökning i Bing:

| Insight                              | Description |
|--------------------------------------|-------------|
| Visuellt likartade bilder              | En lista med avbildningar som visuellt liknar den inmatade bilden. |
| Visuellt liknande produkter            | Produkter som är visuellt likartade för produkten som visas.            |
| Shoppingkällor                     | En lista över platser där du kan köpa objektet visas i den inmatade bilden.            |
| Närliggande sökningar                     | Relaterade sökningar som har gjorts av andra eller som är baserat på innehållet i bilden.            |
| Webb sidor som innehåller avbildningen     | Webbsidor som inkluderar den inmatade bilden.            |
| Recept                              | Webb sidor som innehåller recept för att göra den skål som visas i indatabilden.            |
| Entiteter                             | Välkända personer, platser och saker. |

Förutom insikter returnerar Visuell sökning i Bing en rad olika villkor (dvs. taggar) som härletts från indatabilden. Taggarna gör det möjligt för användare att utforska begrepp som finns i avbildningen. Om indata-bilden till exempel är av en berömda idrottare kan en av taggarna vara namnet på idrottare, en annan tagg kan vara idrotts. Eller, om indata-bilden är av en äpple cirkel, kan taggarna vara äpple cirkel-, pajer-och desserter.

Visuell sökning i Bing resultat inkluderar också avgränsnings rutor för intresse områden i bilden. Om bilden till exempel innehåller flera kändisar, kan resultaten omfatta avgränsnings rutor för var och en av de identifierade kändisar. Eller, om Bing känner igen en produkt eller kläder i bilden, kan resultatet innehålla en avgränsnings ruta för det identifierade objektet.

## <a name="workflow"></a>Arbetsflöde

API:et för visuell sökning i Bing är en RESTful-webbtjänst, vilket innebär att den är enkel att anropa från alla programmeringsspråk som kan göra HTTP-begäranden och tolka JSON. Du kan använda antingen REST API eller SDK för tjänsten.

1. Skapa ett [Cognitive Services konto](../cognitive-services-apis-create-account.md) för att få åtkomst till API:er för Bing-sökresultat. Om du inte har någon Azure-prenumeration kan du [skapa ett konto kostnadsfritt](https://azure.microsoft.com/free/cognitive-services/).
2. Skicka en begäran till API: et med en giltig Sök fråga.
3. Bearbeta API-svaret genom att tolka det returnerade JSON-meddelandet.

## <a name="next-steps"></a>Nästa steg

Börja med att testa API för visuell sökning i Bing [interaktiva demonstrationen](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/).
Demonstrationen visar hur du snabbt kan anpassa en Sök fråga och publicera webben efter bilder.

Kom igång snabbt med din första begäran genom att läsa snabbstarterna:

* [C#](quickstarts/csharp.md)

* [Java](quickstarts/java.md)

* [node.js](quickstarts/nodejs.md)

* [Python](quickstarts/python.md)

## <a name="see-also"></a>Se även

* Referensen [avbildnings visuell sökning](/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) beskriver definitioner och information om slut punkterna, begärandehuvuden, svar och frågeparametrar som du kan använda för att begära bildbaserade Sök resultat.

* [Användnings-och visnings kraven för Bing-sökning-API](../bing-web-search/use-display-requirements.md) anger acceptabel användning av innehållet och information som erhålls via Bing Search-API: erna.

* Gå till [sidan Bing-sökning API Hub](../bing-web-search/overview.md) och utforska de andra tillgängliga API: erna.