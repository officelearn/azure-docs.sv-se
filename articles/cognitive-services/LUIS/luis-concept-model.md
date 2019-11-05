---
title: Design med modeller – LUIS
titleSuffix: Azure Cognitive Services
description: Språk förståelse tillhandahåller flera olika typer av modeller. Vissa modeller kan användas på fler än ett sätt.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 4cbca96ed95167615f3ff2876e27e546d08d92f5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507719"
---
# <a name="design-with-intent-and-entity-models"></a>Design med avsikts-och enhets modeller 

Språk förståelse tillhandahåller flera olika typer av modeller. Vissa modeller kan användas på fler än ett sätt. 

## <a name="v3-authoring-uses-machine-teaching"></a>V3-redigering använder dator undervisning

LUIS gör det möjligt för personer att enkelt lära sig begrepp till en dator. Datorn kan sedan bygga modeller (funktionella uppskattningar av koncept som till exempel klassificerare och utdrag) som kan användas för att köra Power intelligent program. Medan LUIS drivs av maskin inlärning är det inte nödvändigt att förstå maskin inlärning för att använda den. I stället kommunicerar dator lärare koncepten med LUIS genom att visa positiva och negativa exempel på konceptet och förklara hur ett koncept bör modelleras med andra relaterade begrepp. Lärare kan också förbättra LUIS-modellen interaktivt genom att identifiera och korrigera förutsägelse felen. 

## <a name="v3-authoring-model-decomposition"></a>V3 redigera modell diskomposition

LUIS stöder _modell dekomposition_ med v3-redigerings-API: erna och avbryter modellen i mindre delar. På så sätt kan du skapa modeller med förtroende för hur de olika delarna konstrueras och förväntas.

Modell dispositionen har följande delar:

* [avsikter](#intents-classify-utterances)
    * [beskrivningar](#descriptors-are-features) som tillhandahålls av funktioner
* [enheter som har lärts till enheten](#machine-learned-entities)
    * [del komponenter](#entity-subcomponents-help-extract-data) (även enhets belärt entiteter)
        * [beskrivningar](#descriptors-are-features) som tillhandahålls av funktioner 
        * [begränsningar](#constraints-are-text-rules) som tillhandahålls av icke-maskinläsbara entiteter som t. ex. (reguljära uttryck och listor)

## <a name="v2-authoring-models"></a>V2-redigerings modeller

LUIS stöder sammansatta entiteter med API: erna för redigering av v2. Detta ger liknande modell diskomposition men är inte samma som v3 modell diskomposition. Den rekommenderade modell arkitekturen är att flytta till modell diskompositionen i v3-redigerings-API: erna. 

## <a name="intents-classify-utterances"></a>Avsikter klassificera yttranden

En avsikt klassificerar exempel yttranden för att lära LUIS om avsikten. Exempel på yttranden i ett avsikts sätt används som positiva exempel på uttryck. Samma yttranden används som negativa exempel i alla andra syften.

Överväg en app som behöver fastställa en användares avsikt att beställa en bok och en app som behöver leverans adressen för kunden. Den här appen har två avsikter: `OrderBook` och `ShippingLocation`.

Följande uttryck är ett **positivt exempel** för `OrderBook` avsikt och ett **negativt exempel** för `ShippingLocation` och `None` avsikter: 

`Buy the top-rated book on bot architecture.`

Resultatet av väldesignade syften, med deras exempel yttranden, är en hög förutsägelse förutsägelse. 

## <a name="entities-extract-data"></a>Enheter extraherar data

En entitet representerar en data enhet som du vill extrahera från uttryck. 

### <a name="machine-learned-entities"></a>Enheter som har lärts till enheten

En enhet som har lärts in är en enhet på den översta nivån som innehåller del komponenter, som också är enhets belärt entiteter. 

**Använd en enhet som har registrerats av enheten**:

* När del komponenterna behövs av klient programmet
* hjälp av Machine Learning-algoritmen för att skapa entiteter

Varje del komponent kan ha:

* under komponenterna
* begränsningar (reguljärt uttryck entitet eller lista entitet)
* beskrivningar (funktioner som en fras lista) 

Ett exempel på en enhet som har lärts in är en ordning för en plan biljett. Det här är en enskild transaktion med många mindre data enheter, till exempel datum, tid, antal platser, typ av säte, till exempel första klass eller turist klass, ursprungs plats, målplats och mål områdes val.


### <a name="entity-subcomponents-help-extract-data"></a>Del komponenter för entiteten hjälper till att extrahera data

En del komponent är en underordnad underordnad enhet som har registrerats i en dator som är inlärd. 

**Använd del komponenten för att**:

* dela upp delarna i den enhet som har lärts från enheten (överordnad entitet).

Nedan följer en enhet som har lärts upp av enheten med alla dessa separata data typer:

* TravelOrder (enhet som har lärts)
    * DateTime (fördefinierad datetimeV2)
    * Plats (enhets medveten entitet)
        * Ursprung (roll hittades genom kontext som `from`)
        * Mål (rollen hittades via kontexten, till exempel `to`)
    * Plats (enheten har lärts)
        * Kvantitet (fördefinierat nummer)
        * Kvalitet (enhet som sparats av enheten med beskrivning av fras lista)
    * Mål platser (enhets medveten entitet med begränsning av list entitet som livsmedels val)

En del av dessa data, till exempel ursprungs platsen och mål platsen, bör registreras från uttryck-kontexten, kanske med sådan formulering som `from` och `to`. Andra delar av data kan extraheras med exakta sträng matchningar (`Vegan`) eller fördefinierade entiteter (geographyV2 `Seattle` och `Cairo`). 

Du utformar hur data matchas och extraheras enligt vilka modeller du väljer och hur du konfigurerar dem.

### <a name="constraints-are-text-rules"></a>Begränsningar är text regler

En begränsning är en text matchnings regel som tillhandahålls av en icke-känd entitet, till exempel entiteten reguljärt uttryck eller en lista entitet. Begränsningen används vid förutsägelse tiden för att begränsa förutsägelsen och tillhandahålla enhets matchning som krävs av klient programmet. Du definierar dessa regler när du redigerar del komponenten. 

**Använd en begränsning**:
* När du känner till den exakta text som ska extraheras.

Begränsningar är:

* entiteter med [reguljära uttryck](reference-entity-regular-expression.md)
* [lista](reference-entity-list.md) entiteter 
* [fördefinierade](luis-reference-prebuilt-entities.md) entiteter

Om du fortsätter med exemplet på plan biljetten kan flyg plats koderna finnas i en List-entitet för exakt text matchning. 

För en flyg plats lista är List posten för Seattle namnet ort, `Seattle` och synonymerna för Seattle innehåller flyg plats koden för Seattle tillsammans med omgivande Towns och städer:

|`Seattle` lista entitets synonymer|
|--|
|`Sea`|
|`seatac`|
|`Bellevue`|

Om du bara vill identifiera tre brev koder för flyg plats koder använder du ett reguljärt uttryck som begränsningen. 

`/^[A-Z]{3}$/`

## <a name="intents-versus-entities"></a>Avsikter jämfört med entiteter

Avsikt är det önskade resultatet av _hela_ uttryck medan entiteter är bitar av data som extraherats från uttryck. Vanligt vis är avsikter knutna till åtgärder som klient programmet ska vidta och entiteter är information som behövs för att utföra den här åtgärden. Från ett programmerings perspektiv utlöser en avsikt ett metod anrop och entiteterna används som parametrar för det metod anropet.

Den här uttryck _måste_ ha en avsikt och _kan_ ha entiteter:

`Buy a airline ticket from Seattle to Cairo`

Den här uttryck har en enda avsikt:

* Köpa en plan biljett

Den här uttryck _kan_ ha flera entiteter:

* Platser för Seattle (ursprung) och Cairo (mål)
* Antalet för en enskild biljett

## <a name="descriptors-are-features"></a>Beskrivningar är funktioner

En beskrivning är en funktion som tillämpas på en modell i inlärnings tid, inklusive fras listor och entiteter. 

**Använd en beskrivning när du vill**:

* förstärka betydelsen av ord och fraser som identifieras av beskrivningen
* Låt LUIS rekommendera nya texter eller fraser för att rekommendera beskrivningen
* åtgärda ett fel i tränings data

## <a name="next-steps"></a>Nästa steg

* Förstå [avsikter](luis-concept-intent.md) och [entiteter](luis-concept-entity-types.md). 