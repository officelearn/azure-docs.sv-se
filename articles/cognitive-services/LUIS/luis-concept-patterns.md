---
title: Hjälp förutsägelse för mönster – LUIS
titleSuffix: Azure Cognitive Services
description: Med ett mönster får du bättre exakthet för en avsikt utan att behöva tillhandahålla många fler yttranden.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: diberry
ms.openlocfilehash: 4ca3a27a63f84eccb66b24d5046e2ae7d751387d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487550"
---
# <a name="patterns-improve-prediction-accuracy"></a>Mönster förbättrar förutsägelse noggrannhet
Mönster är utformade för att förbättra precisionen när flera yttranden är mycket lika.  Med ett mönster får du bättre exakthet för en avsikt utan att behöva tillhandahålla många fler yttranden. 

## <a name="patterns-solve-low-intent-confidence"></a>Mönster löser låg säkerhet
Överväg en personalavdelningen som rapporterar om organisations diagrammet i förhållande till en medarbetare. Utifrån en medarbetares namn och relation returnerar LUIS de anställda. Överväg en anställd, tom, med ett chefs namn Alice och ett lag med underordnade med namnet: Michael, Rebecca och Carl.

![Bild av organisations schema](./media/luis-concept-patterns/org-chart.png)

|Yttranden|Förväntad avsikt|Avsikts poängen|
|--|--|--|
|Vem är tom som underordnad?|GetOrgChart|.30|
|Vem är underordnad tom?|GetOrgChart|.30|

Om en app har mellan 10 och 20 yttranden med olika längd på mening, olika ord ordning och även olika ord (synonymer till "underordnad", "hantera", "rapport") kan LUIS returnera en låg exakthet. Skapa ett mönster för att hjälpa LUIS förstå ord ordningens betydelse. 

Mönster löser följande situationer: 

* Poängen för avsikt är låg
* Rätt avsikt är inte det översta resultatet, men för nära det högsta resultatet. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Mönster är inte en garanti för avsikt
Mönster använder en blandning av förutsägelse teknik. Att ange ett avsikts sätt för en mall uttryck i ett mönster är inte en garanti för avsikts förutsägelse, men det är en stark signal. 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>Mönster ger inte bättre identifiering av enhets identifiering

Ett mönster är främst avsett att hjälpa till att förutsäga avsikter och roller. Mönstret. en entitet används för att extrahera fria formulär enheter. När mönster använder entiteter kan ett mönster inte identifiera en enhet som har lärts ur enheten.  

Vänta inte att se förbättrad enhets förutsägelse om du komprimerar flera yttranden till ett enda mönster. För att enkla entiteter ska kunna startas måste du lägga till yttranden eller använda List entiteter, annars går det inte att starta mönstret.

## <a name="patterns-use-entity-roles"></a>Mönster använder entitets roller
Om två eller flera entiteter i ett mönster är kontext bundna, använder mönster enhets [roller](luis-concept-roles.md) för att extrahera sammanhangsbaserad information om entiteter.  

## <a name="prediction-scores-with-and-without-patterns"></a>Förutsägelse resultat med och utan mönster
Med tillräckligt många exempel yttranden skulle LUIS kunna öka förutsägelse förtroendet utan mönster. Mönster ökar förtroende poängen utan att behöva ange så många yttranden.  

## <a name="pattern-matching"></a>Mönster matchning
Ett mönster matchas baserat på identifiering av entiteterna inuti mönstret först och sedan verifieras resten av orden och ord ordningen för mönstret. Entiteter krävs i mönstret för ett mönster som ska matchas. Mönstret används på token-nivå, inte på Character-nivå. 

## <a name="pattern-syntax"></a>Pattern-syntax
Pattern-syntax är en mall för en uttryck. Mallen ska innehålla ord och entiteter som du vill matcha samt de ord och skiljetecken som du vill ignorera. Det är **inte** ett reguljärt uttryck. 

Entiteter i mönster omges av klammerparenteser, `{}`. Mönster kan innehålla entiteter och entiteter med roller. [Mönster. alla](luis-concept-entity-types.md#patternany-entity) är en entitet som endast används i mönster. 

Pattern-syntaxen stöder följande syntax:

|Funktion|Syntax|Kapslings nivå|Exempel|
|--|--|--|--|
|Entitetsrelation| {} klammer|2|Var finns formatet {Entity-Name}?|
|Valfritt|[]-hakparenteser<BR><BR>Det finns en gräns på 3 på kapslings nivåer för valfri kombination av valfria och gruppering |2|Frågetecknet är valfritt [?]|
|Baserat|() – parenteser|2|är (a \| b)|
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

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Syntax för att lägga till en entitet i en mönster mall
Om du vill lägga till en entitet i mönster mal len omger du entitetsnamnet med klammerparenteser, till exempel `Who does {Employee} manage?`. 

|Mönster med entitet|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Syntax för att lägga till en entitet och roll i en mönster mall
En Entity-roll anges som `{entity:role}` med entitetsnamnet följt av ett kolon, och sedan roll namnet. Om du vill lägga till en entitet med en roll i mönster mal len omger du entitetsnamnet och roll namnet med klammerparenteser, t. ex. `Book a ticket from {Location:Origin} to {Location:Destination}`. 

|Mönster med enhets roller|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Syntax för att lägga till ett mönster. mall för mönster
Mönstret. med valfri entitet kan du lägga till en entitet med varierande längd på mönstret. Så länge mönster mal len följs, kan mönstret. det kan vara vilken längd som helst. 

Om du vill lägga till ett **mönster.** en entitet i mönster mal len omger du mönstret. alla entiteter med klammerparenteser, till exempel `How much does {Booktitle} cost and what format is it available in?`.  

|Mönster med mönster. valfri entitet|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|Bok titlar i mönstret|
|--|
|Hur mycket kan **stjäla den här bokens** kostnad och vilket format är det?|
|Hur **mycket kostar det** och vilket format är det?|
|Hur mycket är den infallande **incidenten av hund under natt tiden** och vilket format är den tillgänglig i?| 

Texten i bok rubriken är inte förvirrande för LUIS eftersom LUIS vet var bok rubriken slutar, baserat på mönstret. alla entiteter.

## <a name="explicit-lists"></a>Explicita listor

skapa en [explicit lista](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) via redigerings-API: n för att tillåta undantaget när:

* Ditt mönster innehåller ett [mönster. alla](luis-concept-entity-types.md#patternany-entity)
* Och den mönstermatchningssyntax ger möjlighet till en felaktig enhets extrahering baserat på uttryck. 

Anta till exempel att du har ett mönster som innehåller både valfri syntax, `[]`och Entity, `{}`, kombinerat på ett sätt att extrahera data på ett felaktigt sätt.

Överväg mönstret [find] e-mail om {subject} [från {person}].

I följande yttranden extraheras **ämnes** -och **person** entiteten korrekt och felaktigt:

|Yttrande|Entitet|Korrekt extrahering|
|--|--|:--:|
|e-postmeddelande om hundar från Christer|ämne = hundar<br>person = Christer|✔|
|e-postmeddelande om människan från La Mancha|ämne = man<br>person = La Mancha|X|

I tabellen ovan ska ämnet vara `the man from La Mancha` (en bok titel), men eftersom det valfria ordet `from`i ämnet, är rubriken felaktigt förväntad. 

Om du vill åtgärda det här undantaget till mönstret lägger du till `the man from la mancha` som en explicit lista matchning för entiteten {subject} med hjälp av [redigerings-API: et för en explicit lista](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Syntax för att markera valfri text i en mall uttryck
Markera valfri text i uttryck med hjälp av det reguljära uttrycket hak paren tes syntax, `[]`. Den valfria texten kan kapsla hakparenteser upp till två hakparenteser.

|Mönster med valfri text|Betydelse|
|--|--|
|`[find] email about {subject} [from {person}]`|`find` och `from {person}` är valfria|
|' Kan du hjälpa mig [?]|Interpunktion-tecknet är valfritt|

Skiljetecken (`?`, `!`, `.`) ignoreras och du måste ignorera dem med hjälp av hakparenteser i mönster. 

## <a name="pattern-only-apps"></a>Endast Pattern-appar
Du kan bygga en app med avsikter som inte har något exempel yttranden, så länge det finns ett mönster för varje avsikt. För en app-only-app bör mönstret inte innehålla enhets enheter, eftersom dessa kräver exempel yttranden. 

## <a name="best-practices"></a>Bästa praxis
Lär dig [metod tips](luis-concept-best-practices.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du implementerar mönster i den här självstudien](luis-tutorial-pattern.md)
