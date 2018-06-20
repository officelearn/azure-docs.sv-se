---
title: Lär dig hur mönster öka förutsägelsefunktionen | Microsoft Docs
titleSuffix: Azure
description: Lär dig mer om designmönster för att öka avsiktshantering förutsägelse resultat och hitta entiteter.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr
ms.openlocfilehash: f45a9dfaf2863b81083fbfb647f420d9a4baed5a
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264671"
---
# <a name="patterns-improve-prediction-accuracy"></a>Mönster förbättra prognosens noggrannhet
Mönster ger bättre precision när flera utterances liknar. Genom att ange ett mönster för utterance kan THOMAS ha en hög konfidensnivå i förutsägelser. 

## <a name="patterns-solve-low-intent-confidence"></a>Mönster lösa låg avsiktshantering förtroende
Överväg att en app för personal som rapporterar om Organisationsschema i förhållande till en medarbetare. Baserat på en anställds namn och relationen, returnerar THOMAS anställda ingår. Överväga en medarbetare, Tom, med en hanterare namn Alice och en grupp av underordnade med namnet: Michael och Rebecca Carl.

![Bild av organisationsschema](./media/luis-concept-patterns/org-chart.png)

|Yttranden|Avsikten förutsade|Avsiktshantering poäng|
|--|--|--|
|Vem är Toms underordnat?|GetOrgChart|.30|
|Vem är Tom underordnat?|GetOrgChart|.30|

Om en app har mellan 10 och 20 utterances med olika längder meningen och annan word ordning med olika ord (synonymer ”underordnade”, ”hantera”, ”report”), kan THOMAS returnera en låg förtroende poäng. Skapa ett mönster för att hjälpa THOMAS förstå vikten av word ordning. 

Mönster lösa följande situationer: 

* När det avsiktshantering resultatet är låg
* När rätt avsikten är inte den översta poängen men för nära den översta poängen. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Mönster är inte av avsikt
Mönster med en blandning av tekniker för förutsägelse. Ange en utterance en mall som används i ett mönster är inte av avsiktshantering förutsägelser men det är en stark signal. 

## <a name="patterns-do-not-improve-entity-detection"></a>Mönster förbättrar inte entiteten identifiering
Medan mönster kräver entiteter, ett mönster hjälper inte att identifiera enheten. Ett mönster är bara tänkt att hjälpa förutsägelser med avsikter och roller.  

## <a name="prediction-scores-with-and-without-patterns"></a>Förutsägelse resultat med och utan mönster
Får tillräckligt med exempel utterances skulle THOMAS kunna öka förutsägelse förtroende utan mönster. Mönster öka förtroende poäng utan att ange så många utterances.  

## <a name="pattern-matching"></a>Mönstermatchning
Ett mönster matchas baserat på identifiera enheter i mönstret först och sedan Validera resten av orden och word ordning av mönstret. Enheter som krävs i mönstret för ett mönster som matchar. 

## <a name="pattern-syntax"></a>Mönstret syntax
Entiteter i mönster omges av klammerparenteser. Mönster kan innehålla entiteter och entiteter med roller. Pattern.any är en entitet som används endast för mönster. Syntaxen för var och en av dessa beskrivs i följande avsnitt.

### <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Syntax för att lägga till en entitet i en mall för mönster
Om du vill lägga till en entitet i mönstret mallen omger entitetsnamnet med klammerparenteser som `Who does {Employee} manage?`. 

```
Who does {Employee} manage?
```

### <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Syntax för att lägga till en enhet och en roll i en mall för mönster
En entitet roll är angiven som `{entity:role}` med entitetsnamnet följt av ett kolon och sedan rollnamnet. Om du vill lägga till en entitet med en roll i mönstret mallen omger enhetsnamnet och rollnamn med klammerparenteser som `Book a ticket from {Location:Origin} to {Location:Destination}`. 

```
Book a ticket from {Location:Origin} to {Location:Destination}
```

### <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Syntax för att lägga till en pattern.any mönster för mallen
Entiteten Pattern.any kan du lägga till en entitet av varierande längd i mönstret. Så länge mallen mönster följs kan av pattern.any innehålla. 

Att lägga till en **Pattern.any** entiteten i mallen mönster omger Pattern.any entiteten med klammerparenteser som `How much does {Booktitle} cost and what format is it available in?`.  

```
How much does {Booktitle} cost and what format is it available in?
```

|Book titlar i mönstret|
|--|
|Hur mycket kostar **stjäla boken** kostnaden och vilket format som är tillgängliga i?|
|Hur mycket kostar **be** kostnaden och vilket format som är tillgängliga i?|
|Hur mycket kostar **i nyfiken Incident av hund på natten tid** kostnaden och vilket format som är tillgängliga i?| 

I exemplen book rubrik är inte de kontextuella ord rubrikens book förvirrande för THOMAS. THOMAS vet där book rubrik slutar eftersom den är i ett mönster och markeras med en Pattern.any entitet.

### <a name="explicit-lists"></a>Explicit listor
Om mönstret innehåller en Pattern.any och mönster syntax gör möjligheten för en felaktig entity-extrahering baserat på utterance, skapa en [Explicit lista](https://aka.ms/ExplicitList) via redigering API för att tillåta undantag. 

Anta att du har ett mönster som innehåller både valfria syntax `[]`, och entiteten syntax `{}`, kombinerade på ett sätt att extrahera data felaktigt.

Att kartlägga mönstret ”[Sök efter] e-post om {ämne} [från {namn}]. I följande utterances den **ämne** och **person** entitet extraheras felaktigt och på rätt sätt:

|Utterance|Entitet|Korrekt extrahering|
|--|--|:--:|
|e-post om hund från Chris|ämne = hund<br>person = Chris|✔|
|e-post om man från La Mancha|ämne = man<br>person = La Mancha|X|

I föregående tabell är utterance `email about the man from La Mancha`, ämne bör vara `the man from La Mancha` (en bok rubrik) men eftersom ämnet innehåller valfria ordet `from`, titeln felaktigt förutsade. 

Åtgärda det här undantaget i mönstret genom att lägga till `the man from la mancha` som en explicit lista matchning för entiteten {ämne} med den [redigering API för explicit lista](https://aka.ms/ExplicitList).

### <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Syntax för att markera valfri text i en mall utterance
Markera valfri text i utterance med hjälp av syntaxen för reguljära uttryck hakparentes, `[]`. Valfri text kan kapsla hakparenteser upp till två hakparenteser.

```
[find] email about {subject} [from {person}]
```

Skiljetecken som `.`, `!`, och `?` kan ignoreras i hakparentes. För att ignorera dessa märken måste varje markering vara i ett separat mönster. Valfria syntaxen stöds för närvarande inte ignoreras ett objekt i en lista i flera artiklar.

## <a name="patterns-only"></a>Endast mönster
THOMAS kan en app utan några exempel utterances i avsikt. Detta är bara tillåtet om mönster används. Mönster kräver minst en entitet i varje mönster. För en endast mönster app innehålla mönstret inte datorn inlärda entiteter eftersom de kräver exempel utterances. 

## <a name="best-practices"></a>Bästa praxis
Läs [metodtips](luis-concept-best-practices.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du implementerar mönster i den här självstudiekursen](luis-tutorial-pattern.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
