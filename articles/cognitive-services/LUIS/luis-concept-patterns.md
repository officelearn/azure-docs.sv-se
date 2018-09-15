---
title: Lär dig hur mönster ökar prognosens noggrannhet
titleSuffix: Azure Cognitive Services
description: Mönster är utformade för att förbättra noggrannheten när flera uttryck är mycket lika. Ett mönster kan du få mer precision för en avsikt utan att ange många fler yttranden.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 0a61ec4407a23e8cc515c2343a92bd77460d424b
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2018
ms.locfileid: "45631420"
---
# <a name="patterns-improve-prediction-accuracy"></a>Mönster förbättra prognosens noggrannhet
Mönster är utformade för att förbättra noggrannheten när flera uttryck är mycket lika.  Ett mönster kan du få mer precision för en avsikt utan att ange många fler yttranden. 

## <a name="patterns-solve-low-intent-confidence"></a>Mönster lösa låg avsikt förtroende
Överväg en personalapp som rapporterar om Organisationsschema i förhållande till en medarbetare. Baserat på en medarbetares namn och relationen, returnerar LUIS anställda som ingår. Överväg att en anställd, Tom, med en chef namn Alice och ett team med underordnade med namnet: Michael och Rebecca Carl.

![Bild av organisationsschema](./media/luis-concept-patterns/org-chart.png)

|Yttranden|Avsikten förutse|Avsiktshantering poäng|
|--|--|--|
|Vem är Toms underordnad server?|GetOrgChart|.30|
|Vem är Tom underordnat?|GetOrgChart|.30|

Om en app har mellan 10 och 20 yttranden med olika längd av mening och olika ordföljden med olika ord (synonymer ”underordnad”, ”hantera”, ”report”), kan LUIS returnera ett låga förtroenderesultat. Skapa ett mönster för att hjälpa att LUIS förstår vikten av word-ordning. 

Mönster lösa följande situationer: 

* När den avsikt poängen är låg
* När rätt avsikten är inte den främsta poängen men för nära övre poängen. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Mönster är inte en garanti för avsikt
Mönster använder en blandning av tekniker för förutsägelse. Ange ett syfte med en mall för uttryck i ett mönster är ingen garanti för avsiktlig förutsägelser men det är en stark signal. 

## <a name="patterns-do-not-improve-entity-detection"></a>Mönster förbättras inte entiteten identifiering
Medan mönster kräver entiteter, hjälper arbetsprofilen inte identifiera entiteten. Ett mönster är endast avsedd att hjälpa förutsägelser med avsikter och roller.  

## <a name="patterns-use-entity-roles"></a>Mönster använder entiteten
Om två eller flera entiteter i ett mönster är sammanhangsmässigt relaterade, mönster använda entiteten [roller](luis-concept-roles.md) extrahera sammanhangsberoende information om entiteter. Detta motsvarar hierarkisk entitet barn, men är **endast** tillgängliga i ett mönster. 

## <a name="prediction-scores-with-and-without-patterns"></a>Förutsägelse poäng med och utan mönster
Med tillräckligt med exempel yttranden kan skulle LUIS kunna öka förutsägelsen utan mönster. Mönster öka förtroendepoäng utan att behöva tillhandahålla så många yttranden.  

## <a name="pattern-matching"></a>Mönstermatchning
Ett mönster matchas baserat på identifiera entiteterna i mönstret först och sedan validerar resten av orden och ordföljden i mönstret. Entiteter krävs i mönstret för ett mönster som ska matchas. 

## <a name="pattern-syntax"></a>Mönstret syntax
Mönstret syntaxen är en mall för ett uttryck. Mallen bör innehålla ord och entiteter som du vill matcha samt ord och skiljetecken som du vill ignorera. Det är **inte** ett reguljärt uttryck. 

Entiteter i mönster omges av klammerparenteser, `{}`. Mönster kan innehålla entiteter och entiteter med roller. Pattern.any är en entitet som endast används i mönster. Syntaxen beskrivs i följande avsnitt.

### <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Syntaxen för att lägga till en entitet i en mall för mönstret
Om du vill lägga till en entitet i mallen mönstret omger entitetsnamn med klammerparenteser, till exempel `Who does {Employee} manage?`. 

|Mönstret med entitet|
|--|
|`Who does {Employee} manage?`|

### <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Syntaxen för att lägga till en enhet och en roll i en mall för mönstret
En entitet roll är angiven som `{entity:role}` med enhetens namn följt av ett kolon och sedan namnet på rollen. Om du vill lägga till en entitet med en roll i mallen mönstret omger enhetsnamnet och rollnamn med klammerparenteser, till exempel `Book a ticket from {Location:Origin} to {Location:Destination}`. 

|Mönstret med entiteten roller|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

### <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Syntaxen för att lägga till en pattern.any mönster för mallen
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

I det här boken rubrik är sammanhangsberoende orden i rubriken för boken inte förvirrande för LUIS. LUIS vet var Bokens titel slutar eftersom den är i ett mönster och markeras med en Pattern.any entitet.

### <a name="explicit-lists"></a>Explicit listor
Om mönstret innehåller en Pattern.any och syntaxen mönstret gör möjligheten för ett ogiltigt entitetextrahering baserat på uttryck kan skapa en [Explicit lista](https://aka.ms/ExplicitList) via redigering API för att tillåta undantag. 

Anta exempelvis att du har ett mönster som innehåller både valfria syntax `[]`, och entiteten syntax `{}`, kombinerade på ett sätt att extrahera data felaktigt.

Att kartlägga mönstret ”[Sök efter] e-post om {ämne} [från {namn}]. I följande uttryck i **ämne** och **person** entitet extraheras korrekt och felaktigt:

|Yttrande|Entitet|Korrekt extrahering|
|--|--|:--:|
|e-post om hundar från Chris|ämne = hundar<br>person = Chris|✔|
|e-post om man från La Mancha|ämne = mannen<br>person = La Mancha|X|

I den föregående tabellen gäller uttryck `email about the man from La Mancha`, ämnet bör vara `the man from La Mancha` (en bok rubrik) men eftersom ämnet innehåller valfria ordet `from`, rubriken felaktigt förväntas. 

Åtgärda det här undantaget i mönstret genom att lägga till `the man from la mancha` som en explicit lista matchning för entiteten {subject} med den [redigering API för explicit lista](https://aka.ms/ExplicitList).

### <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Syntaxen för att markera valfri text i en mall-uttryck
Markera valfri text i uttryck som använder syntax för reguljära uttryck hakparentes, `[]`. Valfri text kan kapsla hakparenteser upp till två hakparenteser.

|Mönstret med valfri text|
|--|
|`[find] email about {subject} [from {person}]`|

Skiljetecken som `.`, `!`, och `?` kan ignoreras med hakparenteser. För att ignorera dessa märken måste varje mark vara i ett separat. Valfria syntax stöd för närvarande inte ignorerar ett objekt i en lista över flera objekt.

## <a name="patterns-only"></a>Endast mönster
LUIS kan en app utan yttranden exempel i avsikt. Den här användningen är bara tillåtet om mönster används. Mönster kräver minst en entitet i varje mönster. För en app för endast mönstret ska mönstret inte innehålla datorn lärt dig entiteter eftersom dessa kräver exempel yttranden. 

## <a name="best-practices"></a>Bästa praxis
Lär dig [bästa praxis](luis-concept-best-practices.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du implementerar mönster i den här självstudien](luis-tutorial-pattern.md)