---
title: Förstå din LUIS nycklar – Azure | Microsoft Docs
description: Använd Språkförståelse (LUIS) för att skapa din app och fråga din endpoing.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/23/2018
ms.author: v-geberr
ms.openlocfilehash: fe6bd0803098854c7ced1a7d816ebbc8cce23b09
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2018
ms.locfileid: "37340621"
---
# <a name="keys-in-luis"></a>Nycklar i LUIS
LUIS använder två nycklar: [redigering](#programmatic-key) och [endpoint](#endpoint-key). Redigering nyckeln skapas automatiskt åt dig när du skapar ditt Understanding Intelligent Service-konto. När du är redo att publicera LUIS-appen kan du behöva [skapa slutpunktsnyckeln](luis-how-to-azure-subscription.md#create-luis-endpoint-key), [tilldela den](luis-how-to-manage-keys.md#assign-endpoint-key) till din LUIS-app och [använder den med slutpunkt-frågan](#use-endpoint-key-in-query). 

|Nyckel|Syfte|
|--|--|
|[Redigera nyckel](#programmatic-key)|Skapa, publicera, hantera medarbetare, versionshantering|
|[Slutpunktsnyckel](#endpoint-key)| Fråga|

Det är viktigt att skapa LUIS-appar i [regioner](luis-reference-regions.md#publishing-regions) där du också vill du publicera och fråga.

<a name="programmatic-key" ></a>
## <a name="authoring-key"></a>Redigera nyckel

En redigering nyckel, även kallat en starter-nyckel, skapas automatiskt när du skapar en LUIS-konto och det är gratis. Du har en nyckel för redigering i alla dina LUIS-appar för varje redigering [region](luis-reference-regions.md). Redigering nyckeln tillhandahålls skriva LUIS-appen eller för att testa endpoint frågor. 

För att hitta nyckeln redigering, logga in på [LUIS] [ LUIS] och klicka på namnet på kontot i det övre högra navigeringsfältet för att öppna **kontoinställningar**.

![Redigera nyckel](./media/luis-concept-keys/programatic-key.png)

När du vill göra **produktion endpoint frågor**, skapa en Azure [LUIS prenumeration](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/). 

> [!CAUTION]
> För att underlätta för många av exempel som använder den redigering nyckeln eftersom det innehåller några endpoint-anrop i dess [kvot](luis-boundaries.md#key-limits).  

## <a name="endpoint-key"></a>Slutpunktsnyckel
 När du behöver **produktion endpoint frågor**, skapa en [LUIS nyckeln](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) i Azure-portalen. Kom ihåg att namnet används för att skapa nyckeln, du behöver den när du lägger till nyckeln till appen...

När LUIS prenumerationsprocessen är klar, [Lägg till nyckel](luis-how-to-manage-keys.md#assign-endpoint-key) till appen i den **publicera** sidan. 

Slutpunktsnyckeln kan en kvot på slutpunkten träffar baserat på vilken plan för användning som du angav när du skapar nyckeln. Se [priser för Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/?v=17.23h) information om priser.

Slutpunktsnyckeln kan användas för alla dina LUIS-appar eller för specifika LUIS-appar. 

Använd inte slutpunktsnyckeln för redigering av LUIS-appar. 

## <a name="use-endpoint-key-in-query"></a>Använd slutpunktsnyckeln i fråga
LUIS-slutpunkten accepterar två sorters fråga, både använda slutpunkten nyckel, men på olika platser:

|Verb|Exempel på placering av url och nyckel|
|--|--|
|[GET](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)|https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/98998dcf-66d2-468e-840a-7c7c57549b5a?subscription-key=your-endpoint-key-here&verbose=true&timezoneOffset=0&q=turn lamporna<br><br>frågesträngsvärdet för `subscription-key`<br><br>Ändra din fråga slutpunktsvärdet för den `subscription-key` från den redigering (starter)-nyckeln till den nya slutpunktsnyckeln för att kunna använda LUIS endpoint viktiga kvot hastighet. Om du skapa nyckeln, och tilldela nyckeln men inte ändra frågan slutpunktsvärdet för prenumeration-key ”, du inte använder din viktiga kvot för slutpunkten.|
|[POST](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/98998dcf-66d2-468e-840a-7c7c57549b5a<br><br> huvudets värde för `Ocp-Apim-Subscription-Key`<br><br>Ändra din fråga slutpunktsvärdet för den `Ocp-Apim-Subscription-Key` från den redigering (starter)-nyckeln till den nya slutpunktsnyckeln för att kunna använda LUIS endpoint viktiga kvot hastighet. Om du skapar nyckeln, och tilldela nyckeln men inte ändra frågan slutpunktsvärdet för `Ocp-Apim-Subscription-Key`, du inte använder din viktiga kvot för slutpunkten.|

## <a name="api-usage-of-ocp-apim-subscription-key"></a>API-användning av Ocp-Apim-Subscription-Key
LUIS APIs använda rubriken, `Ocp-Apim-Subscription-Key`. Huvudets namn ändras inte beroende på vilken nyckel och en uppsättning API: er du använder. Ange rubriken till redigering nyckeln för redigering API: er. Om du använder slutpunkten inställd rubriken slutpunktsnyckeln. 

Du kan inte skicka slutpunktsnyckeln för redigering av API: er. Om du gör det, får du ett 401-fel – åtkomst nekad på grund av ogiltig slutpunktsnyckeln. 

## <a name="key-limits"></a>Viktiga begränsningar
Se [viktiga begränsningar](luis-boundaries.md#key-limits) och [Azure-regioner](luis-reference-regions.md). Redigering nyckeln är ledigt och använt för redigering. LUIS slutpunktsnyckeln har en kostnadsfri nivå men måste skapas av dig och som är associerade med LUIS-appen på den **publicera** sidan. Det kan inte användas för redigering, men endast slutpunkten frågor.

Publicera regioner skiljer sig från redigering regioner. Kontrollera att du skapar en app i redigering region motsvarande att publicera region som du vill.

## <a name="key-limit-errors"></a>Gräns för fel
Om du överskrider din per sekund kvot du får ett HTTP 429-fel. Om du överskrider din per månad kvot du får felmeddelandet HTTP 403. Åtgärda felen genom att hämta en LUIS [endpoint](#endpoint-key) nyckel, [tilldela](luis-how-to-manage-keys.md#assign-endpoint-key) nyckeln till appen på den **publicera** för den [LUIS] [ LUIS] webbplats.

## <a name="next-steps"></a>Nästa steg

* Lär dig [begrepp](luis-how-to-manage-keys.md#assign-endpoint-key) om nycklar för redigering och slutpunkt.

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
