---
title: Scenario-utvärderingen - Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizer kan användas i alla situationer där ditt program kan välja rätt artikeln, åtgärd eller produkt att visa – för att förbättra upplevelsen, uppnå bättre affärsresultat eller förbättra produktiviteten.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: e51ef9afd0e49b690a4f9cab09fdfbd3e86eee66
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65441052"
---
# <a name="where-can-you-use-personalizer"></a>Var kan du använda Personanpassning?

Använd Personalizer i alla situationer där programmet behöver för att välja rätt artikeln, åtgärd eller produkt som ska visa – för att förbättra upplevelsen, uppnå bättre affärsresultat eller förbättra produktiviteten. 

Personalizer använder maskininlärning att välja vilken åtgärd som ska visas för användaren. Markeringen kan variera mycket beroende på kvantitet, kvalitet och fördelning av data som skickas till tjänsten.

### <a name="checklist-for-applying-personalizer"></a>Checklista för att tillämpa Personalizer


Du kan använda Personalizer i situationer där:

* Du har en företags- eller användbarhet målet för ditt program.
* Du har en plats i ditt program där beslutet sammanhangsberoende av vad som ska visas för användarna att förbättra målet.
* Det bästa valet kan och ska hämtas från kollektiva användaren beteende och Totalt antal utmärkelse poäng.
* Användningen av maskininlärning för anpassning följer [ansvarig Använd riktlinjer](ethics-responsible-use.md) och val som du har valt.
* Sammanhangsberoende beslutet kan uttryckas som rangordning det bästa alternativet (åtgärd) från en begränsad uppsättning alternativ.
* Hur väl rankad valet arbetat för ditt program kan fastställas genom att mäta någon aspekt av användarnas beteende och anges i en _belöna poäng_. Det här är ett tal mellan 1 och 1.
* Poängen trafik Importera inte för många confounding eller externa faktorer. Experimentlängd är låg trafik poängen kan beräknas när den är fortfarande relevanta.
* Du kan uttrycka kontext för rangordningen som en lista på minst 5 [funktioner](concepts-features.md) som du tror att kunna fatta rätt beslut och som inte innehåller personligt identifierbar information. (PII).
* Du har information om varje innehåll alternativ _åtgärd_, som en lista på minst 5 [funktioner](concepts-features.md) som du tror att hjälp Personalizer fatta rätt beslut.
* Programmet kan behålla data för långvariga tillräckligt för att lagra en historik över minst 100 000 interaktioner.

## <a name="machine-learning-considerations-for-applying-personalizer"></a>Machine learning-överväganden för att tillämpa Personalizer

Personalizer baseras på förstärkt learning, en metod till machine learning som undervisats av feedback du ge den. 

Personalizer lära dig bäst i situationer där:

* Det finns tillräckligt med händelser för att hålla koll på optimal personanpassning om problemet drifts över tid (t.ex inställningar i nyheter eller Mode). Personalizer anpassas automatiskt löpande ändringar i verkligheten, men resultatet inte inte bra om det inte finns tillräckligt med händelser och data kan lära sig från att identifiera och reglera på nya mönster. Du bör välja ett användningsfall som sker tillräckligt ofta. Överväg att söker användningsfall som sker på minst 500 gånger per dag.
* Kontext och åtgärder ha tillräckligt med [funktioner](concepts-features.md) att förenkla inlärningen.
* Det finns färre än 50 åtgärder att rangordning per anrop.
* Dina inställningar för kvarhållning av data att Personalizer att samla in tillräckligt med data för att utföra offline utvärderingar och optimering av Grupprincip. Detta är vanligtvis minst 50 000 datapunkter.

## <a name="monitor-effectiveness-of-personalizer"></a>Övervaka effektiviteten i Personalizer

Du kan övervaka effektiviteten i Personalizer regelbundet genom att utföra [offline utvärderingar](concepts-offline-evaluation.md).

## <a name="use-personalizer-with-recommendation-engines"></a>Använda Personalizer med rekommendationsmotorer

Många företag använder rekommendationsmotorer, marknadsföring och campaigning verktyg segmentering av målgruppen och klustring, samarbetsfunktioner filtrering och annat sätt för att rekommendera produkter från en stor katalog till kunder.

Den [Microsoft Recommenders GitHub-lagringsplatsen](https://github.com/Microsoft/Recommenders) innehåller exempel och bästa praxis för att skapa recommendation-system, tillhandahålls som Jupyter-anteckningsböcker. Den innehåller exempel för att förbereda data, skapa modeller, utvärderar, justering och operationalisera rekommendationsmotorer för många vanliga metoder för bland annat xDeepFM, SAR, Sensorn, Ringpärmsmekanismer, DKN.

Personalizer kan arbeta med en rekommendationsmotor om den finns.

* Rekommendationsmotorer ta stora mängder objekt (exempelvis 500 000) och rekommenderar en delmängd (till exempel översta 20) från hundratals eller tusentals alternativ.
* Personalizer tar ett litet antal åtgärder med mycket information om dem och rankar i realtid för ett visst omfattande sammanhang, medan de flesta rekommendationsmotorer använder endast några attribut om användare, produkter och hur fungerar.
* Personalizer är utformad för att utforska självständigt användarinställningar hela tiden, vilket ger bättre resultat där innehållet ändras snabbt, t.ex nyheter, händelser, live community-innehåll, innehåll med dagliga uppdateringar eller säsongens innehåll.

Ett vanligt användningsområde är att ta utdata från en rekommendationsmotor (till exempel de 20 främsta produkterna för en viss kund) och använda det som indata åtgärder för Personalizer.

## <a name="next-steps"></a>Nästa steg

[Ethics & ansvarig Använd](ethics-responsible-use.md).