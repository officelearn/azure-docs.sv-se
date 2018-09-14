---
title: Metodtips – QnA Maker
titlesuffix: Azure Cognitive Services
description: Använda dessa bästa metoder för att förbättra din kunskapsbas och ge bättre resultat för din robot program/chatt slutanvändare.
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: saneppal
ms.openlocfilehash: c82c117d149da39fba7b9a243aebb3e127540881
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2018
ms.locfileid: "45542940"
---
# <a name="best-practices"></a>Bästa praxis
Den [kunskapsbas för säkerhetsutveckling](../Concepts/development-lifecycle-knowledge-base.md) guidar dig om hur du hanterar du KB slutpunkt till slutpunkt. Använda dessa bästa metoder för att förbättra din kunskapsbas och ge bättre resultat för din robot program/chatt slutanvändare.

## <a name="extraction"></a>Extrahering
QnA Maker är kontinuerligt förbättra de algoritmer som extraherar kunskapsbaser från innehåll och expandera listan över fil- och HTML-sida-format som stöds. Följ den [riktlinjer](../Concepts/data-sources-supported.md) för extrahering baserat på vilken typ av dokument du extraherar från. 

Vanliga frågor och svar sidor ska i allmänhet vara fristående och inte kombinerade med annan information. Produkthandböcker bör ha tydliga rubriker och helst en indexsida. 

## <a name="rankingmatching"></a>Rangordning/matchar
Kontrollera att du gör på bästa sätt rangordning-funktioner som har stöd för QnA Maker. Detta kommer att förbättra sannolikheten för en viss användarfrågan besvaras med ett lämpligt svar.

### <a name="add-alternate-questions"></a>Lägga till alternativa frågor
[Alternativa frågor](../How-To/edit-knowledge-base.md) förbättra sannolikheten för en matchning med en användarfråga. Alternativa frågor är användbara när det finns flera sätt samma fråga kan bli ombedd. Detta kan omfatta ändringar i strukturen för mening (till exempel *”är tillgängliga parkering”?* jämfört med *”har du bil park”?* ) eller ändringar i word-format och slang (till exempel *”Hi”* jämfört med *”Yo”*, *”Hey, det”!* ).

### <a name="use-metadata-filters"></a>Använda filter för filmetadata
[Metadata](../How-To/edit-knowledge-base.md) lägger till möjligheten att begränsa resultatet av en användarfråga baserat på filter. Kunskapsbas-svar kan variera beroende på metadatataggen, även om frågan är samma. Till exempel *”var är parkering finns”* kan ha olika svar om grenen restaurang finns olika – det vill säga metadata är *plats: Seattle* jämfört med *plats: Redmond*.)

### <a name="use-synonyms"></a>Använda synonymer
Även om det finns vissa stöd för synonymer i på engelska, [word förändras](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fd) att lägga till synonymer i nyckelord som tar olika format (exempel: *köpa* -> *köpa*  eller *netbanking* -> *net bank*. Synonymer bör läggas på QnA Maker-tjänstnivå och delas av alla kunskapsbaser i tjänsten.

### <a name="use-distinct-words-to-differentiate-questions"></a>Använd distinkta ord för att skilja frågor
QnA Maker matchning och rangordning algoritmer som matchar en användarfråga med en fråga i kunskapsbasen fungerar bäst om varje fråga adresser olika behov. Upprepning av samma ord ange mellan frågor minskar risken för att rätt svar väljs för en viss användarfråga med orden.

## <a name="collaborate"></a>Samarbeta
QnA Maker kan användarna [samarbeta](../How-to/collaborate-knowledge-base.md) på en kunskapsbas. Användare behöver komma åt Azure QnA Maker resursgruppen för att komma åt kunskapsbaser. Vissa organisationer vilja indrivningen kunskapsbas redigering och underhåll och fortfarande kunna skydda åtkomsten till deras Azure-resurser. Den här redigeraren godkännaren-modellen kan åstadkommas genom att ställa in två identiska [QnA Maker services](../How-to/set-up-qnamaker-service-azure.md) i olika prenumerationer och utser en för testning av redigera cykeln. När testningen är klar, innehållet i kunskapsbasen kan överföras med en [import / export-](../Tutorials/migrate-knowledge-base.md) bearbeta till QnA Maker-tjänsten till godkännaren slutligen publicera kunskapsbasen och uppdatera slutpunkten.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Redigera en kunskapsbas](../How-to/edit-knowledge-base.md)

