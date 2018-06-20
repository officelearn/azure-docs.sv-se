---
title: Förstå dina THOMAS nycklar - Azure | Microsoft Docs
description: Använd språket förstå (THOMAS) för att skapa din app och fråga din endpoing.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/23/2018
ms.author: v-geberr
ms.openlocfilehash: 70bca3b181e02f42da50e827154193936544131a
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36263826"
---
# <a name="keys-in-luis"></a>Nycklar i THOMAS
THOMAS använder två nycklar: [redigering](#programmatic-key) och [endpoint](#endpoint-key). Redigering nyckeln skapas automatiskt för dig när du skapar THOMAS-konto. När du är redo att publicera THOMAS-app, behöver du [skapa slutpunkten nyckeln](luis-how-to-azure-subscription.md#create-luis-endpoint-key), [tilldela den](Manage-keys.md#assign-endpoint-key) i appen THOMAS och [med slutpunkten frågan](#use-endpoint-key-in-query). 

|Nyckel|Syfte|
|--|--|
|[Redigera nyckel](#programmatic-key)|Redigering, publicera, hantera medarbetare, versionshantering|
|[Slutpunktsnyckel](#endpoint-key)| Fråga|

Det är viktigt att redigera THOMAS appar i [regioner](luis-reference-regions.md#publishing-regions) där du även vill publicera och fråga.

<a name="programmatic-key" ></a>
## <a name="authoring-key"></a>Redigera nyckel

En redigering nyckel, även kallat en start-nyckel, skapas automatiskt när du skapar ett konto för THOMAS och är kostnadsfri. Du har en redigering nyckel över alla THOMAS program för varje redigering [region](luis-reference-regions.md). Redigering nyckeln anges att redigera appen THOMAS eller testa endpoint frågor. 

Logga in för att hitta nyckeln för redigering i [THOMAS] [ LUIS] och klicka på namnet på kontot i det övre högra navigeringsfältet att öppna **kontoinställningar**.

![Redigera nyckel](./media/luis-concept-keys/programatic-key.png)

När du vill se **produktion endpoint frågor**, skapa en Azure [THOMAS prenumeration](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/). 

> [!CAUTION]
> För enkelhetens skull många exemplen använder nyckeln redigering eftersom den innehåller några endpoint-anrop i dess [kvot](luis-boundaries.md#key-limits).  

## <a name="endpoint-key"></a>Slutpunktsnyckel
 När du behöver **produktion endpoint frågor**, skapa en [THOMAS nyckeln](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) i Azure-portalen. Kom ihåg namnet som används för att skapa nyckeln, du behöver den när du lägger till nyckeln i appen..

När THOMAS prenumerationsprocessen är klar [lägga till nyckeln](Manage-keys.md#assign-endpoint-key) till appen på den **publicera** sidan. 

Nyckeln för slutpunkten kan en kvot på slutpunkten träffar baserat på användning planen som du angav när du skapar nyckeln. Se [kognitiva Services priser](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/?v=17.23h) information om priser.

Nyckeln för slutpunkten kan användas för alla dina appar i THOMAS eller för specifika THOMAS appar. 

Använd inte nyckeln för slutpunkten för redigering av THOMAS appar. 

## <a name="use-endpoint-key-in-query"></a>Använda slutpunktsnyckel i fråga
Slutpunkten THOMAS accepterar två typer av frågan använder både endpoint nyckel, men på olika ställen:

|Verb|Exempel på placering av URL: en och nyckel|
|--|--|
|[GET](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)|https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/98998dcf-66d2-468e-840a-7c7c57549b5a?subscription-key=your-endpoint-key-here&verbose=true&timezoneOffset=0&q=turn på indikeringar<br><br>frågesträngsvärdet för `subscription-key`<br><br>Ändra din slutpunkt frågan för den `subscription-key` från redigering (Start) nyckeln, till den nya nyckeln slutpunkt för att kunna använda THOMAS endpoint viktiga kvoten hastighet. Om du skapar du nyckel och tilldela nyckeln, inte men endpoint frågan värde för prenumerationen-nyckeln ', du inte använder din nyckel kvot för slutpunkten.|
|[POST](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/98998dcf-66d2-468e-840a-7c7c57549b5a<br><br> värde för `Ocp-Apim-Subscription-Key`<br><br>Ändra din slutpunkt frågan för den `Ocp-Apim-Subscription-Key` från redigering (Start) nyckeln, till den nya nyckeln slutpunkt för att kunna använda THOMAS endpoint viktiga kvoten hastighet. Om du skapar nyckeln, och tilldela nyckeln men inte ändra frågan slutpunktsvärde för `Ocp-Apim-Subscription-Key`, du inte använder din nyckel kvot för slutpunkten.|

## <a name="api-usage-of-ocp-apim-subscription-key"></a>API-användning av Ocp-Apim-prenumeration-nyckeln
LUIS APIs använda rubriken, `Ocp-Apim-Subscription-Key`. Huvudets namn ändras inte utifrån vilka nyckel och en uppsättning API: er som används. Ange rubriken för nyckeln redigering för redigering API: er. Om du använder slutpunkten Ange rubriken till slutpunktsnyckel. 

Du kan inte överföra nyckeln slutpunkten för API: er för redigering. Om du vill få ett 401-fel – åtkomst nekad på grund av ogiltiga prenumerationen nyckel. 

## <a name="key-limits"></a>Viktiga begränsningar
Se [nyckeln gränser](luis-boundaries.md#key-limits) och [Azure-regioner](luis-reference-regions.md). Redigering är ledigt och använt för redigering. Nyckeln THOMAS prenumerationen har en kostnadsfri nivå men måste skapas av dig och associerade med appen THOMAS på den **publicera** sidan. Den kan inte användas för redigering, men endast endpoint frågor.

Publishing regioner skiljer sig från redigering regioner. Kontrollera att du skapar en app i authoring region motsvarande publishing regionen som du vill använda.

## <a name="key-limit-errors"></a>Gräns för fel
Om du överskrider din per andra kvoten felmeddelandet HTTP 429. Om du överskrider din per månad kvoten felmeddelandet HTTP 403. Åtgärda felen genom att hämta en THOMAS [endpoint](#endpoint-key) nyckel, [tilldela](Manage-keys.md#assign-endpoint-key) nyckeln till appen på den **publicera** sida av den [THOMAS] [ LUIS] webbplats.

## <a name="next-steps"></a>Nästa steg

* Läs [begrepp](Manage-Keys.md#assign-endpoint-key) om nycklar för redigering och slutpunkt.

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
