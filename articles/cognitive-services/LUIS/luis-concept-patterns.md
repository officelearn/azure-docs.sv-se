---
title: Hjälp förutsägelse för mönster – LUIS
titleSuffix: Azure Cognitive Services
description: Ett mönster kan du få mer precision för en avsikt utan att ange många fler yttranden.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: bad3bdc2b4508c082ca50647d5de5e7265c763a1
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639193"
---
# <a name="patterns-improve-prediction-accuracy"></a>Mönster förbättra prognosens noggrannhet
Mönster är utformade för att förbättra noggrannheten när flera uttryck är mycket lika.  Ett mönster kan du få mer precision för en avsikt utan att ange många fler yttranden. 

## <a name="patterns-solve-low-intent-confidence"></a>Mönster lösa låg avsikt förtroende
Överväg en personalapp som rapporterar om Organisationsschema i förhållande till en medarbetare. Baserat på en medarbetares namn och relationen, returnerar LUIS anställda som ingår. Överväg en anställd, tom, med ett chefs namn Alice och ett lag med underordnade med namnet: Michael, Rebecca och Carl.

![Bild av organisationsschema](./media/luis-concept-patterns/org-chart.png)

|Yttranden|Avsikten förutse|Avsiktshantering poäng|
|--|--|--|
|Vem är Toms underordnad server?|GetOrgChart|.30|
|Vem är Tom underordnat?|GetOrgChart|.30|

Om en app har mellan 10 och 20 yttranden med olika längd av mening och olika ordföljden med olika ord (synonymer ”underordnad”, ”hantera”, ”report”), kan LUIS returnera ett låga förtroenderesultat. Skapa ett mönster för att hjälpa LUIS förstå ord ordningens betydelse. 

Mönster lösa följande situationer: 

* Poängen för avsikt är låg
* Rätt avsikt är inte det översta resultatet, men för nära det högsta resultatet. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Mönster är inte en garanti för avsikt
Mönster använder en blandning av tekniker för förutsägelse. Ange ett syfte med en mall för uttryck i ett mönster är ingen garanti för avsiktlig förutsägelser men det är en stark signal. 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>Mönster ger inte bättre identifiering av enhets identifiering

Ett mönster är främst avsett att hjälpa till att förutsäga avsikter och roller. Mönstret. en entitet används för att extrahera fria formulär enheter. När mönster använder entiteter kan ett mönster inte identifiera en enhet som har lärts ur enheten.  

Vänta inte att se förbättrad enhets förutsägelse om du komprimerar flera yttranden till ett enda mönster. För att enkla entiteter ska kunna startas måste du lägga till yttranden eller använda List entiteter, annars går det inte att starta mönstret.

## <a name="patterns-use-entity-roles"></a>Mönster använder entiteten
Om två eller flera entiteter i ett mönster är sammanhangsmässigt relaterade, mönster använda entiteten [roller](luis-concept-roles.md) extrahera sammanhangsberoende information om entiteter.  

## <a name="prediction-scores-with-and-without-patterns"></a>Förutsägelse poäng med och utan mönster
Med tillräckligt med exempel yttranden kan skulle LUIS kunna öka förutsägelsen utan mönster. Mönster öka förtroendepoäng utan att behöva tillhandahålla så många yttranden.  

## <a name="pattern-matching"></a>Mönstermatchning
Ett mönster matchas baserat på identifiera entiteterna i mönstret först och sedan validerar resten av orden och ordföljden i mönstret. Entiteter krävs i mönstret för ett mönster som ska matchas. Mönstret tillämpas på nivån token inte tecknet-nivå. 

## <a name="pattern-syntax"></a>Mönstret syntax
Mönstret syntaxen är en mall för ett uttryck. Mallen bör innehålla ord och entiteter som du vill matcha samt ord och skiljetecken som du vill ignorera. Det är **inte** ett reguljärt uttryck. 

Entiteter i mönster omges av klammerparenteser, `{}`. Mönster kan innehålla entiteter och entiteter med roller. [Mönster. alla](luis-concept-entity-types.md#patternany-entity) är en entitet som endast används i mönster. 

Pattern-syntaxen stöder följande syntax:

|Funktion|Syntax|Kapslings nivå|Exempel|
|--|--|--|--|
|entitet| {}– typografiska hakparenteser|2|Var finns formatet {Entity-Name}?|
|valfri|[]-hakparenteser<BR><BR>Det finns en gräns på 3 på kapslings nivåer för valfri kombination av valfria och gruppering |2|Frågetecknet är valfritt [?]|
|baserat|() – parenteser|2|is (a \| b)|
|eller| \|– lodrätt streck (pipe)<br><br>Det finns en gräns på 2 på de lodräta staplarna (eller) i en grupp |-|Där är form ({form-Name-Short} &#x7c; {form-Name-Long} &#x7c; {form-Number})| 
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
|Kairo|matchar en av de inre gruppiska enheterna|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>Kapslade gränser för grupper med valfri syntax

En kombination av **gruppering** med **valfri** syntax har en gräns på 3 kapslings nivåer.

|Behörig|Exempel|
|--|--|
|Ja|([(TEST1 &#x7c; TEST2)] &#x7c; test3)|
|Nej|([([TEST1] &#x7c; TEST2)] &#x7c; test3)|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>Kapslade gränser för grupper med eller-ing-syntax

En kombination av **gruppering** med **eller-ing-** syntaxen har en gräns på 2 lodräta staplar.

|Behörig|Exempel|
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

I den föregående tabellen ska ämnet vara `the man from La Mancha` (en bok titel) men eftersom ämnet innehåller det valfria ordet `from`är rubriken felaktigt förväntad. 

Åtgärda det här undantaget i mönstret genom att lägga till `the man from la mancha` som en explicit lista matchning för entiteten {subject} med den [redigering API för explicit lista](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Syntaxen för att markera valfri text i en mall-uttryck
Markera valfri text i uttryck som använder syntax för reguljära uttryck hakparentes, `[]`. Valfri text kan kapsla hakparenteser upp till två hakparenteser.

|Mönstret med valfri text|Betydelse|
|--|--|
|`[find] email about {subject} [from {person}]`|`find`och `from {person}` är valfria|
|' Kan du hjälpa mig [?]|Interpunktion-tecknet är valfritt|

Skiljetecken (`?`, `!`, `.`) ska ignoreras och du måste ignorera dem med hjälp av klammer i mönster. 

## <a name="pattern-only-apps"></a>Endast Pattern-appar
Du kan bygga en app med avsikter som inte har något exempel yttranden, så länge det finns ett mönster för varje avsikt. För en app-only-app bör mönstret inte innehålla enhets enheter, eftersom dessa kräver exempel yttranden. 

## <a name="best-practices"></a>Bästa praxis
Lär dig [bästa praxis](luis-concept-best-practices.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du implementerar mönster i den här självstudien](luis-tutorial-pattern.md)
