---
title: Mönster hjälpa förutsägelse
titleSuffix: Language Understanding - Azure Cognitive Services
description: Ett mönster kan du få mer precision för en avsikt utan att ange många fler yttranden.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: diberry
ms.openlocfilehash: 42ac75b6ed0d4489ccae014b9cfe3b08269c1218
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57547426"
---
# <a name="patterns-improve-prediction-accuracy"></a>Mönster förbättra prognosens noggrannhet
Mönster är utformade för att förbättra noggrannheten när flera uttryck är mycket lika.  Ett mönster kan du få mer precision för en avsikt utan att ange många fler yttranden. 

## <a name="patterns-solve-low-intent-confidence"></a>Mönster lösa låg avsikt förtroende
Överväg en personalapp som rapporterar om Organisationsschema i förhållande till en medarbetare. Baserat på en medarbetares namn och relationen, returnerar LUIS anställda som ingår. Överväg att en anställd, Tom, med en chef namn Alice och ett team med underordnade med namnet: Michael Rebecca och Carl.

![Bild av organisationsschema](./media/luis-concept-patterns/org-chart.png)

|Yttranden|Avsikten förutse|Avsiktshantering poäng|
|--|--|--|
|Vem är Toms underordnad server?|GetOrgChart|.30|
|Vem är Tom underordnat?|GetOrgChart|.30|

Om en app har mellan 10 och 20 yttranden med olika längd av mening och olika ordföljden med olika ord (synonymer ”underordnad”, ”hantera”, ”report”), kan LUIS returnera ett låga förtroenderesultat. Skapa ett mönster för att hjälpa LUIS förstår vikten av word-ordning. 

Mönster lösa följande situationer: 

* Avsiktshantering poängen är låg
* Rätt avsikten är inte den främsta poängen men för nära övre poängen. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Mönster är inte en garanti för avsikt
Mönster använder en blandning av tekniker för förutsägelse. Ange ett syfte med en mall för uttryck i ett mönster är ingen garanti för avsiktlig förutsägelser men det är en stark signal. 

<a name="patterns-do-not-improve-entity-detection"/>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>Mönster förbättras inte datorn lärt dig entitet identifiering

Ett mönster är främst avsett att hjälpa förutsägelser av avsikter och roller. Pattern.any entiteten används för att extrahera fri form entiteter. Mönster använda entiteter, hjälper arbetsprofilen inte identifiera en dator lärt dig entitet.  

Inte räknar med att se förbättrad entitet förutsägelse när du minimerar flera uttryck i ett enda mönster. För enkla enheter innan behöver du lägga till yttranden eller Använd listan över entiteter annan mönstret inte utlöses.

## <a name="patterns-use-entity-roles"></a>Mönster använder entiteten
Om två eller flera entiteter i ett mönster är sammanhangsmässigt relaterade, mönster använda entiteten [roller](luis-concept-roles.md) extrahera sammanhangsberoende information om entiteter. Detta motsvarar hierarkisk entitet barn, men är **endast** tillgängliga i ett mönster. 

## <a name="prediction-scores-with-and-without-patterns"></a>Förutsägelse poäng med och utan mönster
Med tillräckligt med exempel yttranden kan skulle LUIS kunna öka förutsägelsen utan mönster. Mönster öka förtroendepoäng utan att behöva tillhandahålla så många yttranden.  

## <a name="pattern-matching"></a>Mönstermatchning
Ett mönster matchas baserat på identifiera entiteterna i mönstret först och sedan validerar resten av orden och ordföljden i mönstret. Entiteter krävs i mönstret för ett mönster som ska matchas. Mönstret tillämpas på nivån token inte tecknet-nivå. 

## <a name="pattern-syntax"></a>Mönstret syntax
Mönstret syntaxen är en mall för ett uttryck. Mallen bör innehålla ord och entiteter som du vill matcha samt ord och skiljetecken som du vill ignorera. Det är **inte** ett reguljärt uttryck. 

Entiteter i mönster omges av klammerparenteser, `{}`. Mönster kan innehålla entiteter och entiteter med roller. [Pattern.ANY](luis-concept-entity-types.md#patternany-entity) är en entitet som endast används i mönster. 

Mönstret syntax stöder följande syntax:

|Funktion|Syntax|Kapslingsnivå|Exempel|
|--|--|--|--|
|entitet| {} -klammerparenteser|2|Var är formuläret {entity-name}?|
|valfri|[] - hakparenteser<BR><BR>Det finns en gräns på 3 på kapslingsnivåer av valfri kombination av valfria och gruppering |2|Frågetecknet är valfritt [?]|
|Gruppering|() - parenteser|2|is (a \| b)|
|eller| \| -vertikalstreck (pipe)<br><br>Det finns en gräns på 2 på de lodräta staplarna (eller) i en grupp |-|Där är formuläret ({formulär-name-kort} &#x7c; {formuläret namn-långa} &#x7c; {formuläret number})| 
|början och/eller slutet av uttryck|^-hatt|-|^ börjar uttryck<br>uttryck görs ^<br>^ strikt literal matchning av hela uttryck med entiteten {number} ^|

## <a name="nesting-syntax-in-patterns"></a>Kapsling syntax i mönster

Den **valfritt** syntax, med hakparenteser och kan vara två nivåer. Till exempel: `[[this]is] a new form`. Det här exemplet möjliggör följande uttryck: 

|Kapslade valfritt uttryck exempel|Förklaring|
|--|--|
|Det här är ett nytt formulär|matchar alla orden i mönstret|
|är ett nytt formulär|matchar yttre valfritt word och icke-valfritt ord i mönstret|
|ett nytt formulär|matchningar krävs ord|

Den **gruppering** syntax, med parenteser kan vara två nivåer. Till exempel: `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`. På så sätt kan någon av de tre enheterna som ska matchas. 

Om Entity1 är en plats med roller som ursprung (Seattle) och mål (Kairo) och entiteten 2 är ett känt att skapa namn från en lista över entitet (RedWest-C), skulle följande talade mappa till det här mönstret:

|Kapslad gruppering uttryck exempel|Förklaring|
|--|--|
|RedWest-C|matchar yttre gruppering entitet|
|Seattle|matchar ett av de inre gruppering entiteterna|
|Kairo|matchar ett av de inre gruppering entiteterna|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>Kapsling gränser för grupper med valfria syntax

En kombination av **gruppering** med **valfritt** syntax har en begränsning på 3 kapslingsnivåer.

|Behörig|Exempel|
|--|--|
|Ja|( [ ( test1 &#x7c; test2 ) ] &#x7c; test3 )|
|Nej|([([test1] &#x7c; test2)] &#x7c; test3)|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>Kapsling gränser för grupper med eller-ing-syntax

En kombination av **gruppering** med **eller-ing** syntax har en gräns på 2 lodräta staplar.

|Behörig|Exempel|
|--|--|
|Ja|( test1 &#x7c; test2 &#x7c; ( test3 &#x7c; test4 ) )|
|Nej|( test1 &#x7c; test2 &#x7c; test3 &#x7c; ( test4 &#x7c; test5 ) ) |

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

Orden i rubriken för boken är inte förvirrande för LUIS eftersom LUIS vet var slutar Bokens titel, baserat på Pattern.any entitet.

## <a name="explicit-lists"></a>Explicit listor

Skapa en [Explicit lista](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) via redigering API för att tillåta undantag när:

* Mönstret innehåller en [Pattern.any](luis-concept-entity-types.md#patternany-entity)
* Och att mönstret syntax gör möjligheten för ett ogiltigt entitetextrahering baserat på uttryck. 

Anta exempelvis att du har ett mönster som innehåller både valfria syntax `[]`, och entiteten syntax `{}`, kombinerade på ett sätt att extrahera data felaktigt.

Att kartlägga mönstret ”[Sök efter] e-post om {ämne} [från {namn}].

I följande uttryck i **ämne** och **person** entitet extraheras korrekt och felaktigt:

|Yttrande|Entitet|Korrekt extrahering|
|--|--|:--:|
|e-post om hundar från Chris|ämne = hundar<br>person = Chris|✔|
|e-post om man från La Mancha|ämne = mannen<br>person = La Mancha|X|

I den föregående tabellen gäller ämne bör vara `the man from La Mancha` (en bok rubrik) men eftersom ämnet innehåller valfria ordet `from`, rubriken felaktigt förväntas. 

Åtgärda det här undantaget i mönstret genom att lägga till `the man from la mancha` som en explicit lista matchning för entiteten {subject} med den [redigering API för explicit lista](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Syntaxen för att markera valfri text i en mall-uttryck
Markera valfri text i uttryck som använder syntax för reguljära uttryck hakparentes, `[]`. Valfri text kan kapsla hakparenteser upp till två hakparenteser.

|Mönstret med valfri text|Betydelse|
|--|--|
|`[find] email about {subject} [from {person}]`|`find` och `from {person}` är valfria|
|”Kan du hjälpa mig [?]|Skiljetecknet är valfritt|

Skiljetecken (`?`, `!`, `.`) ska ignoreras och du behöver att ignorera dem med hjälp av hakparentes-syntax i ett mönster. 

## <a name="pattern-only-apps"></a>Endast mönstret appar
Du kan skapa en app med avsikter som har inga exempel yttranden så länge det finns ett mönster för varje avsikt. För en app för endast mönstret får inte mönstret innehålla datorn lärt dig entiteter, eftersom dessa kräver exempel yttranden. 

## <a name="best-practices"></a>Bästa praxis
Lär dig [bästa praxis](luis-concept-best-practices.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du implementerar mönster i den här självstudien](luis-tutorial-pattern.md)
