---
title: Syntaxreferens för mönster - LUIS
description: Skapa entiteter för att extrahera viktiga data från användaryttranden i LUIS-appar (Language Understanding). Extraherade data används av klientprogrammet.
ms.topic: reference
ms.date: 04/14/2020
ms.author: diberry
ms.openlocfilehash: cc24667f43dfedc032f52c40fc5f8fe5c80bad70
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382151"
---
# <a name="pattern-syntax"></a>Mönstersyntax

Mönstersyntax är en mall för ett uttryck. Mallen ska innehålla ord och entiteter som du vill matcha samt ord och [skiljetecken](luis-reference-application-settings.md#punctuation-normalization) som du vill ignorera. Det är **inte** ett reguljärt uttryck.

> [!CAUTION]
> Mönster inkluderar endast maskininlärda entitetsföräldrar, inte underkomponenter.

Entiteter i mönster omges av `{}`lockiga parenteser. Mönster kan inkludera entiteter och entiteter med roller. [Pattern.any](luis-concept-entity-types.md#patternany-entity) är en entitet som endast används i mönster.

Mönstersyntax stöder följande syntax:

|Funktion|Syntax|Kapslingsnivå|Exempel|
|--|--|--|--|
|Enhet| {}- lockiga konsoler|2|Var finns formuläret {entity-name}?|
|valfri|[] - hakparenteser<BR><BR>Det finns en gräns på 3 på häckningsnivåer för valfri kombination av valfria och gruppering |2|Frågetecknet är valfritt [?]|
|Gruppering|() - parenteser|2|är (a \| b)|
|eller| \|- vertikal bar (rör)<br><br>Det finns en gräns på 2 på de vertikala staplarna (Eller) i en grupp |-|Var är formen ({form-name-short} &#x7c; {form-name-long} &#x7c; {form-number})|
|början och/eller slutet av yttrandet|^ - caret|-|^börja yttrandet<br>yttrandet är gjort ^<br>^strikt bokstavlig matchning för hela yttrandet med {number} entitet^|

## <a name="nesting-syntax-in-patterns"></a>Kapsla syntax i mönster

Den **valfria** syntaxen, med hakparenteser, kan kapslas två nivåer. Till exempel: `[[this]is] a new form`. Det här exemplet tillåter följande yttranden:

|Kapslat valfritt uttrycksexempel|Förklaring|
|--|--|
|Detta är ett nytt formulär|matchar alla ord i mönster|
|är ett nytt formulär|matchar yttre valfria ord och icke-valfria ord i mönster|
|ett nytt formulär|matchar endast obligatoriska ord|

Syntaxen **för gruppering,** med parenteser, kan kapslas med två nivåer. Till exempel: `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`. Med den här funktionen kan någon av de tre entiteterna matchas.

Om Entitet1 är en plats med roller som ursprung (Seattle) och mål (Kairo) och entitet 2 är ett känt byggnadsnamn från en listentitet (RedWest-C), mappas följande yttranden till det här mönstret:

|Exempel på kapslad gruppering av uttryck|Förklaring|
|--|--|
|RedWest-C|matchar yttre grupperingsentitet|
|Seattle|matchar en av de inre grupperingsentiteterna|
|Cairo|matchar en av de inre grupperingsentiteterna|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>Kapslingsgränser för grupper med valfri syntax

En kombination av **gruppering** med **valfri** syntax har en gräns på 3 kapslingsnivåer.

|Tillåtet|Exempel|
|--|--|
|Ja|( [ ( test1 &#x7c; test2 ) ] &#x7c; test3 )|
|Inga|( [ [ [ test1 ] &#x7c; test2 ) ] &#x7c; test3 )|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>Kapslingsgränser för grupper med eller-ing-syntax

En kombination av **gruppering** med **eller-ing** syntax har en gräns på 2 lodräta staplar.

|Tillåtet|Exempel|
|--|--|
|Ja|(test1 &#x7c; test2 &#x7c; ( test3 &#x7c; test4 ) )|
|Inga|( test1 &#x7c; test2 &#x7c; test3 &#x7c; ( test4 &#x7c; test5 ) ) |

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Syntax för att lägga till en entitet i en mönstermall
Om du vill lägga till en entitet i mönstermallen `Who does {Employee} manage?`omger du entitetsnamnet med klammerparenteser, till exempel .

|Mönster med entitet|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Syntax för att lägga till en entitet och roll i en mönstermall
En entitetsroll `{entity:role}` betecknas som med entitetsnamnet följt av ett kolon och sedan rollnamnet. Om du vill lägga till en entitet med en roll i mönstermallen `Book a ticket from {Location:Origin} to {Location:Destination}`omger du entitetsnamnet och rollnamnet med klammerparenteser, till exempel .

|Mönster med entitetsroller|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Syntax för att lägga till ett mönster.alla i mönstermallen
Med Pattern.any-entiteten kan du lägga till en entitet av varierande längd i mönstret. Så länge mönstermallen följs kan pattern.any vara valfri längd.

Om du vill lägga till en **Pattern.any-entitet** i mönstermallen omger du pattern.any-entiteten med klammerparenteserna, till exempel `How much does {Booktitle} cost and what format is it available in?`.

|Mönster med Pattern.any-entitet|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|Boktitlar i mönstret|
|--|
|Hur mycket **stjäl den här boken** kostnaden och vilket format är det tillgängligt i?|
|Hur mycket **frågar** kostnad och vilket format är det tillgängligt i?|
|Hur mycket kostar **The Curious Incident of the Dog in the Night-Time** och vilket format finns det i?|

Orden i bokens titel är inte förvirrande för LUIS eftersom LUIS vet var bokens titel slutar, baserat på Pattern.any-entiteten.

## <a name="explicit-lists"></a>Explicita listor

skapa en [explicit lista](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) via det auktoritära API:et för att tillåta undantaget när:

* Ditt mönster innehåller ett [Pattern.any](luis-concept-entity-types.md#patternany-entity)
* Och det mönstersyntaxen möjliggör möjligheten av en felaktig entitetsextrahering baserat på uttrycket.

Anta till exempel att du har ett `[]`mönster som `{}`innehåller både valfri syntax och entitetssyntax, som kombineras på ett felaktigt sätt för att extrahera data.

Tänk på mönstret '[find] email about {subject} [from {person}]'.

I följande yttranden extraheras **ämnet** och **personentiteten** korrekt och felaktigt:

|Yttrande|Entitet|Korrekt extrahering|
|--|--|:--:|
|e-post om hundar från Chris|subject=hundar<br>person=Chris|✔|
|e-post om mannen från La Mancha|subject=mannen<br>person=La Mancha|X|

I föregående tabell ska ämnet `the man from La Mancha` vara (en boktitel) men eftersom `from`ämnet innehåller det valfria ordet förutses titeln felaktigt.

Om du vill åtgärda det `the man from la mancha` här undantaget i mönstret lägger du till som en explicit listmatchning för entiteten {subject} med hjälp av [redigerings-API:et för explicit lista](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Syntax för att markera valfri text i ett mallyttrande
Markera valfri text i uttrycket med den `[]`vanliga uttrycksparentessyntaxen . Den valfria texten kan bara kapsla hakparenteser upp till två parenteser.

|Mönster med valfri text|Betydelse|
|--|--|
|`[find] email about {subject} [from {person}]`|`find`och `from {person}` är valfria|
|"Kan du hjälpa mig[?]|Skiljetecknet är valfritt|

Skiljetecken (`?` `!`, `.`, ) bör ignoreras och du måste ignorera dem med hjälp av hakparentessyntaxen i mönster.

## <a name="next-steps"></a>Nästa steg

Läs mer om mönster:

* [Så här lägger du till mönster](luis-how-to-model-intent-pattern.md)
* [Så här lägger du till pattern.any-entitet](luis-how-to-add-entities.md#add-a-patternany-entity)
* [Mönster Begrepp](luis-concept-patterns.md)

Förstå hur [sentiment](luis-reference-prebuilt-sentiment.md) returneras i .json-svaret.