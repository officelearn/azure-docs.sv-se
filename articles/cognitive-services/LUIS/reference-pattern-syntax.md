---
title: Referens för mönster-syntax – LUIS
titleSuffix: Azure Cognitive Services
description: Skapa entiteter för att extrahera nyckel data från User yttranden i Language Understanding-appar (LUIS). Extraherade data används av klient programmet.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: c1353ae530493c34413399a7fbbfe56fb74d452f
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74010769"
---
# <a name="pattern-syntax"></a>Mönstret syntax

Mönstret syntaxen är en mall för ett uttryck. Mallen bör innehålla ord och entiteter som du vill matcha samt ord och skiljetecken som du vill ignorera. Det är **inte** ett reguljärt uttryck. 

Entiteter i mönster omges av klammerparenteser, `{}`. Mönster kan innehålla entiteter och entiteter med roller. [Mönster. alla](luis-concept-entity-types.md#patternany-entity) är en entitet som endast används i mönster. 

Pattern-syntaxen stöder följande syntax:

|Funktion|Syntax|Kapslings nivå|Exempel|
|--|--|--|--|
|entitet| {} klammer|2|Var finns formatet {Entity-Name}?|
|valfritt|[]-hakparenteser<BR><BR>Det finns en gräns på 3 på kapslings nivåer för valfri kombination av valfria och gruppering |2|Frågetecknet är valfritt [?]|
|Baserat|() – parenteser|2|is (a \| b)|
|eller| \|-lodrätt streck (pipe)<br><br>Det finns en gräns på 2 på de lodräta staplarna (eller) i en grupp |-|Där är form ({form-Name-Short} &#x7c; {form-Name-Long} &#x7c; {form-Number})| 
|början och/eller slutet av uttryck|^-cirkumflex|-|^ Starta uttryck<br>uttryck är slutförd ^<br>^ Strict literal matchning av hela uttryck med {Number} Entity ^|

## <a name="nesting-syntax-in-patterns"></a>Kapsla syntax i mönster

Den **valfria** syntaxen, med hakparenteser, kan kapslas två nivåer. Till exempel: `[[this]is] a new form`. I det här exemplet används följande yttranden: 

|Exempel på kapslade valfria uttryck|Förklaring|
|--|--|
|Det här är ett nytt formulär|matchar alla ord i mönstret|
|är ett nytt formulär|matchar yttre valfria ord och icke-valfria ord i mönstret|
|ett nytt formulär|matchar endast obligatoriska ord|

**Grupperingstypen** , med parenteser, kan kapslas två nivåer. Till exempel: `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`. Med den här funktionen kan alla de tre entiteterna matchas. 

Om Entity1 är en plats med roller som t. ex. ursprung (Seattle) och destination (Cairo) och entitet 2 är ett känt byggnads namn från en List-entitet (RedWest-C) mappar följande yttranden till det här mönstret:

|Exempel på kapslad gruppering uttryck|Förklaring|
|--|--|
|RedWest-C|matchar Outer Grouping-entitet|
|Seattle|matchar en av de inre gruppiska enheterna|
|Cairo|matchar en av de inre gruppiska enheterna|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>Kapslade gränser för grupper med valfri syntax

En kombination av **gruppering** med **valfri** syntax har en gräns på 3 kapslings nivåer.

|Gett|Exempel|
|--|--|
|Ja|([(TEST1 &#x7c; TEST2)] &#x7c; test3)|
|Nej|([([TEST1] &#x7c; TEST2)] &#x7c; test3)|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>Kapslade gränser för grupper med eller-ing-syntax

En kombination av **gruppering** med **eller-ing-** syntaxen har en gräns på 2 lodräta staplar.

|Gett|Exempel|
|--|--|
|Ja|(TEST1 &#x7c; TEST2 &#x7c; (test3 &#x7c; test4))|
|Nej|(TEST1 &#x7c; TEST2 &#x7c; test3 &#x7c; (test4 &#x7c; test5)) |

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Syntaxen för att lägga till en entitet i en mall för mönstret
Om du vill lägga till en entitet i mallen mönstret omger entitetsnamn med klammerparenteser, till exempel `Who does {Employee} manage?`. 

|Mönstret med entitet|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Syntaxen för att lägga till en enhet och en roll i en mall för mönstret
En entitet roll är angiven som `{entity:role}` med enhetens namn följt av ett kolon och sedan namnet på rollen. Om du vill lägga till en entitet med en roll i mallen mönstret omger enhetsnamnet och rollnamn med klammerparenteser, till exempel `Book a ticket from {Location:Origin} to {Location:Destination}`. 

|Mönstret med entiteten roller|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Syntaxen för att lägga till en pattern.any mönster för mallen
Entiteten Pattern.any kan du lägga till en entitet med varierande längd i mönstret. Så länge som mallen mönster följs kan pattern.any vara valfri längd. 

Att lägga till en **Pattern.any** entiteten i mallen mönstret omger Pattern.any entiteten med klammerparenteser, till exempel `How much does {Booktitle} cost and what format is it available in?`.  

|Mönstret med Pattern.any entitet|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|Bok rubriker i mönstret|
|--|
|Hur mycket **stjäla den här boken** kostnaden och vilket format är tillgängliga i?|
|Hur mycket **be** kostnaden och vilket format är tillgängliga i?|
|Hur mycket **The nyfiken Incident av hund på natten-tiden** kostnaden och vilket format är tillgängliga i?| 

Texten i bok rubriken är inte förvirrande för LUIS eftersom LUIS vet var bok rubriken slutar, baserat på mönstret. alla entiteter.

## <a name="explicit-lists"></a>Explicit listor

skapa en [explicit lista](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) via redigerings-API: n för att tillåta undantaget när:

* Ditt mönster innehåller ett [mönster. alla](luis-concept-entity-types.md#patternany-entity)
* Och den mönstermatchningssyntax ger möjlighet till en felaktig enhets extrahering baserat på uttryck. 

Anta exempelvis att du har ett mönster som innehåller både valfria syntax `[]`, och entiteten syntax `{}`, kombinerade på ett sätt att extrahera data felaktigt.

Att kartlägga mönstret ”[Sök efter] e-post om {ämne} [från {namn}].

I följande uttryck i **ämne** och **person** entitet extraheras korrekt och felaktigt:

|Yttrande|Entitet|Korrekt extrahering|
|--|--|:--:|
|e-post om hundar från Chris|ämne = hundar<br>person = Chris|✔|
|e-post om man från La Mancha|ämne = mannen<br>person = La Mancha|X|

I tabellen ovan ska ämnet vara `the man from La Mancha` (en bok titel), men eftersom det valfria ordet `from`i ämnet, är rubriken felaktigt förväntad. 

Åtgärda det här undantaget i mönstret genom att lägga till `the man from la mancha` som en explicit lista matchning för entiteten {subject} med den [redigering API för explicit lista](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Syntaxen för att markera valfri text i en mall-uttryck
Markera valfri text i uttryck som använder syntax för reguljära uttryck hakparentes, `[]`. Valfri text kan kapsla hakparenteser upp till två hakparenteser.

|Mönstret med valfri text|Betydelse|
|--|--|
|`[find] email about {subject} [from {person}]`|`find` och `from {person}` är valfria|
|' Kan du hjälpa mig [?]|Interpunktion-tecknet är valfritt|

Skiljetecken (`?`, `!`, `.`) ignoreras och du måste ignorera dem med hjälp av hakparenteser i mönster. 

## <a name="next-steps"></a>Nästa steg

Lär dig mer om mönster:

* [Lägga till mönster](luis-how-to-model-intent-pattern.md)
* [Hur du lägger till mönster. valfri entitet](luis-how-to-add-entities.md##add-a-patternany-entity)
* [Mönster koncept](luis-concept-patterns.md)

Förstå hur [sentiment](luis-reference-prebuilt-sentiment.md) returneras i. JSON-svaret.