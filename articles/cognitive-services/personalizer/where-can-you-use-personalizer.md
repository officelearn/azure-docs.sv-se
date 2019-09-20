---
title: Scenario utvärdering – Personanpassare
titleSuffix: Azure Cognitive Services
description: Du kan använda en personanpassare i alla situationer där ditt program kan välja rätt objekt, åtgärd eller produkt som ska visas för att förbättra upplevelsen, uppnå bättre affärs resultat eller förbättra produktiviteten.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 09/19/2019
ms.author: diberry
ms.openlocfilehash: 246e76a0ab94624945723b500ef136e038ab40ec
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155234"
---
# <a name="where-can-you-use-personalizer"></a>Var kan du använda Personanpassning?

Använd Personanpassare i alla situationer där ditt program behöver välja rätt objekt, åtgärd eller produkt för att visa för att förbättra upplevelsen, uppnå bättre affärs resultat eller förbättra produktiviteten. 

I personanpassaren används Machine Learning för att välja vilken åtgärd som ska visas för användaren. Valet kan variera drastiskt beroende på kvantitet, kvalitet och distribution av data som skickas till tjänsten.

### <a name="checklist-for-applying-personalizer"></a>Check lista för att använda Personanpassare


Du kan använda Personanpassare i situationer där:

* Du har ett affärs-eller användbarhets mål för ditt program.
* Du har en plats i ditt program där du får ett Sammanhangs beroende beslut av vad som ska visas för användarna att förbättra det målet.
* Det bästa valet kan och bör registreras från kollektivt användar beteende och total belönings poäng.
* Användningen av Machine Learning för anpassning följer de [rikt linjer](ethics-responsible-use.md) och alternativ som du väljer.
* Det sammanhangsbaserade beslutet kan uttryckas som rangordning av det bästa alternativet (åtgärd) från en begränsad uppsättning alternativ.
* Hur väl det rankade valet av program kan bestämmas genom att mäta viss aspekt av användar beteendet och uttrycka det i en _[belönings Poäng](concept-rewards.md)_ .
* Belönings poängen tar inte upp för många påträffande eller externa faktorer. Experimentets varaktighet är för låg nog att belönings poängen kan beräknas när den fortfarande är relevant.
* Du kan uttrycka kontexten för rang som en lista över minst 5 [funktioner](concepts-features.md) som du tror kan hjälpa dig att välja rätt och som inte innehåller personligt identifierbar information. (PII).
* Du har information om varje innehålls val, _åtgärd_, som en lista över minst 5 [funktioner](concepts-features.md) som du tror att du kan göra rätt val.
* Ditt program kan lagra data tillräckligt länge för att samla en historik på minst 100 000 interaktioner.

## <a name="machine-learning-considerations-for-applying-personalizer"></a>Maskin inlärnings överväganden för att använda Personanpassare

Personanpassa är baserad på utlösnings inlärning, en metod för maskin inlärning som är inlärt av feedback som du ger dem. 

Personanpassare kommer att lära sig bäst i situationer där:

* Det finns tillräckligt många händelser för att få bästa möjliga anpassning om problemet uppstår över tid (t. ex. inställningar i nyheter eller mode). Personanpassaren anpassas efter kontinuerliga förändringar i verkligheten, men resultatet blir inte optimalt om det inte finns tillräckligt med händelser och data för att lära sig att identifiera och lösa nya mönster. Du bör välja ett användnings fall som ofta är tillräckligt. Överväg att söka efter användnings fall som inträffar minst 500 gånger per dag.
* Kontext och åtgärder har tillräckligt med [funktioner](concepts-features.md) för att under lätta inlärningen.
* Det finns färre än 50 åtgärder att rangordna per anrop.
* Med dina inställningar för datakvarhållning kan du samla in tillräckligt med data för att utföra offline-utvärdering och optimering av principer. Detta är vanligt vis minst 50 000 data punkter.

## <a name="monitor-effectiveness-of-personalizer"></a>Övervaka effektiviteten hos Personanpassare

Du kan övervaka effektiviteten för Personanpassare regelbundet genom att utföra [offline-utvärderingar](concepts-offline-evaluation.md).

## <a name="use-personalizer-with-recommendation-engines"></a>Använda Personanpassare med rekommendations motorer

Många företag använder rekommendations motorer, marknadsförings-och kampanj verktyg, mål grupp segmentering och klustring, samordnad filtrering och andra metoder för att rekommendera produkter från en stor katalog till kunder.

[Microsoft-rekommendationerna GitHub-lagringsplatsen](https://github.com/Microsoft/Recommenders) innehåller exempel och bästa metoder för att skapa rekommendations system, som tillhandahålls som Jupyter notebook-datorer. Den innehåller exempel på hur du förbereder data, bygger modeller, utvärderar, justerar och genomför rekommendationer för att använda rekommendationer för många vanliga metoder, inklusive xDeepFM, SAR, sensorn, RINGPÄRMSMEKANISMER, DKN.

Personanpassaren kan arbeta med en rekommendations motor när den är närvarande.

* Rekommendations motorer tar stora mängder objekt (till exempel 500 000) och rekommenderar en delmängd (till exempel de 20 främsta) från hundratals eller tusentals alternativ.
* Personanpassaren tar ett litet antal åtgärder med mycket information om dem och rangordnar dem i real tid för en viss rik kontext, medan de flesta rekommendations motorer bara använder några få attribut om användare, produkter och deras interaktioner.
* Personanpassaren är utformad för att på ett autonomt sätt utforska användar inställningarna hela tiden, vilket ger bättre resultat om innehållet ändras snabbt, till exempel nyheter, direktsända evenemang, innehåll i levande community, innehåll med dagliga uppdateringar eller säsongs innehåll.

Ett vanligt användnings sätt är att ta ut utdata från en rekommendations motor (till exempel de 20 främsta produkterna för en viss kund) och använda dem som ingångs åtgärder för Personanpassaren.

## <a name="next-steps"></a>Nästa steg

[Etik & ansvarig användning](ethics-responsible-use.md).