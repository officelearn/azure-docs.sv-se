---
title: Metodtips kognitiva - frågor och svar om Maker - Azure-tjänster | Microsoft Docs
description: Använd följande rekommenderade metoder att förbättra kunskapsbasen och ge bättre resultat till ditt program/chatt bot slutanvändare.
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 7a85ebbc3892a90e98e73a73425c1f8ec1de0b35
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "35355926"
---
# <a name="best-practices"></a>Bästa praxis
Den [kunskapsbas utvecklingslivscykeln](../Concepts/development-lifecycle-knowledge-base.md) hjälper dig om hur du hanterar KB slutpunkt till slutpunkt. Använd följande rekommenderade metoder att förbättra kunskapsbasen och ge bättre resultat till ditt program/chatt bot slutanvändare.

## <a name="extraction"></a>Extrahering
Frågor och svar om Maker är kontinuerligt förbättra algoritmer som extraherar QnAs från innehåll och expandera fil- och HTML-sida-format som stöds. Följ den [riktlinjer](../Concepts/data-sources-supported.md) för extrahering baserat på vilken typ av dokument du extraherar från. 

Vanliga frågor och svar sidor ska i allmänhet vara fristående och inte kombinerat med annan information. Produkthandböcker bör ha Rensa rubriker och helst en indexsida. 

## <a name="rankingmatching"></a>Rangordning/matchande
Kontrollera att du gör på bästa sätt funktionerna rangordning för frågor och svar om Maker stöder. Då ökar sannolikheten för en viss användarfrågan besvaras med ett lämpligt svar.

### <a name="add-alternate-questions"></a>Lägg till alternativa frågor
[Alternativa frågor](../How-To/edit-knowledge-base.md) öka sannolikheten för en matchning med en användarfråga. Alternativa frågor är användbara när det finns flera sätt där samma fråga kan du bli uppmanad. Detta kan inkludera ändringar i den mening-strukturen (till exempel *”är tillgängliga parkering”?* jämfört med *”har du bil park”?* ) eller ändringar i word-format och slang (till exempel *”Hej”* jämfört med *”Yo”*, *”det Hey”!* ).

### <a name="use-metadata-filters"></a>Använd filter för filmetadata
[Metadata](../How-To/edit-knowledge-base.md) lägger till möjligheten att begränsa resultaten av en användarfråga baserat på filter. Kunskapsbasen svaret kan variera beroende på metadata-taggen även om frågan är samma. Till exempel *”där är parkering finns”* kan ha olika svar om grenen restaurang finns olika - metadata är *plats: Seattle* jämfört med *plats: Redmond*.)

### <a name="use-synonyms"></a>Använd synonymer
Det finns vissa stöd för synonymer på engelska, använda [word eller](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fd) att lägga till synonymer nyckelord som kan ha olika form (exempel: *köpa* -> *köpa*  eller *netbanking* -> *net bank*. Synonymer bör läggas på frågor och svar om Maker-tjänstnivå och delas av alla knowledge baser i tjänsten.

### <a name="use-distinct-words-to-differentiate-questions"></a>Använd distinkta ord för att skilja frågor
Frågor och svar om Maker matchar och rang algoritmer som matchar en användarfråga med en fråga i knowledge base fungerar bäst om varje fråga adresser olika behov. Upprepning av samma ord mellan frågor minskar sannolikheten för att det är fel väljs för en viss användarfråga med orden.

## <a name="collaborate"></a>Samarbeta
Frågor och svar om Maker tillåter användare att [samarbeta](../How-to/collaborate-knowledge-base.md) på en knowledge base. Användare behöver åtkomst till Azure frågor och svar om Maker resursgruppen för att komma åt knowledge baser. Vissa organisationer vilja flytta ut kunskapsbas redigering och underhåll och fortfarande att kunna skydda åtkomst till sina Azure-resurser. Den här modellen editor godkännare kan åstadkommas genom att ställa in två identiska [frågor och svar om Maker services](../How-to/set-up-qnamaker-service-azure.md) i olika prenumerationer och utse en för testning av redigera cykeln. När testet är klart, knowledge base-innehåll kan överföras med en [importera exportera](../Tutorials/migrate-knowledge-base.md) processer till tjänsten frågor och svar om Maker till godkännaren slutligen publiceras i knowledge base och uppdatera slutpunkten.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Redigera en kunskapsbas](../How-to/edit-knowledge-base.md)

