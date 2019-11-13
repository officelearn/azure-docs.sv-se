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
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 6234a13d07ac024849d1c890d82ef03e19c11af4
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74011998"
---
# <a name="patterns-improve-prediction-accuracy"></a>Mönster förbättra prognosens noggrannhet
Mönster är utformade för att förbättra noggrannheten när flera uttryck är mycket lika.  Med ett mönster får du bättre exakthet för en avsikt utan att behöva tillhandahålla många fler yttranden. 

## <a name="patterns-solve-low-intent-confidence"></a>Mönster lösa låg avsikt förtroende
Överväg en personalapp som rapporterar om Organisationsschema i förhållande till en medarbetare. Baserat på en medarbetares namn och relationen, returnerar LUIS anställda som ingår. Överväg att en anställd, Tom, med en chef namn Alice och ett team med underordnade med namnet: Michael och Rebecca Carl.

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

## <a name="pattern-only-apps"></a>Endast Pattern-appar
Du kan bygga en app med avsikter som inte har något exempel yttranden, så länge det finns ett mönster för varje avsikt. För en app-only-app bör mönstret inte innehålla enhets enheter, eftersom dessa kräver exempel yttranden. 

## <a name="best-practices"></a>Bästa praxis
Lär dig [bästa praxis](luis-concept-best-practices.md).

## <a name="pattern-syntax"></a>Mönstret syntax

Lär dig mönstermatchningssyntax från [Pattern-syntaxen](reference-pattern-syntax.md). 

## <a name="next-steps"></a>Nästa steg

Lär dig mer om mönster:

* [Lägga till mönster](luis-how-to-model-intent-pattern.md)
* [Hur du lägger till mönster. valfri entitet](luis-how-to-add-entities.md##add-a-patternany-entity)
* [Syntax för mönster](reference-pattern-syntax.md)

> [!div class="nextstepaction"]
> [Lär dig hur du implementerar mönster i den här självstudien](luis-tutorial-pattern.md)
