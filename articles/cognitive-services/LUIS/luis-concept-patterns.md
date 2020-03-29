---
title: Mönster hjälp förutsägelse - LUIS
titleSuffix: Azure Cognitive Services
description: Ett mönster gör att du kan få mer noggrannhet för en avsikt utan att ge många fler yttranden.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 6c1b548de25369c162b4a08dfa20fce62c17f99f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75890276"
---
# <a name="patterns-improve-prediction-accuracy"></a>Mönster förbättrar förutsägelsenoggrannheten
Mönster är utformade för att förbättra noggrannheten när flera yttranden är mycket lika.  Ett mönster gör att du kan få mer noggrannhet för en avsikt utan att ge många fler yttranden. 

## <a name="patterns-solve-low-intent-confidence"></a>Mönster löser förtroende med låg avsikt
Överväg en personalapp som rapporterar i organisationsschemat i förhållande till en medarbetare. Med tanke på en medarbetares namn och relation returnerar LUIS de berörda medarbetarna. Tänk dig en anställd, Tom, med ett chefsnamn Alice, och ett team av underordnade som heter: Michael, Rebecca och Carl.

![Bild av organisationsschema](./media/luis-concept-patterns/org-chart.png)

|Yttranden|Avsikt förutsagd|Poäng för avsikt|
|--|--|--|
|Vem är Toms underordnade?|GetOrgChart (0)|.30|
|Vem är Toms underordnade?|GetOrgChart (0)|.30|

Om en app har mellan 10 och 20 yttranden med olika längder på meningen, olika ordordningar och till och med olika ord (synonymer med "underordnade", "hantera", "rapport") kan LUIS returnera en låg konfidenspoäng. Skapa ett mönster som hjälper LUIS att förstå vikten av ordordningen. 

Mönster löser följande situationer: 

* Avsiktspoängen är låg
* Den korrekta avsikten är inte den högsta poängen men för nära den högsta poängen. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Mönster är inte en garanti för uppsåt
Mönster använder en blandning av förutsägelseteknik. Att ange en avsikt för en mallutsägels i ett mönster är inte en garanti för avsiktsförutsägelsen, men det är en stark signal. 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>Mönster förbättrar inte identifiering av maskininlärd entitet

Ett mönster är främst tänkt att hjälpa förutsägelse av avsikter och roller. Pattern.any-entiteten används för att extrahera friformentiteter. _pattern.any_ Medan mönster använder entiteter hjälper ett mönster inte att identifiera en maskininlärd entitet.  

Förvänta dig inte att se förbättrad entitetsförutsägels om du döljer flera yttranden i ett enda mönster. För att enkla entiteter ska aktiveras måste du lägga till yttranden eller använda listentiteter annars kommer ditt mönster inte att avfyras.

## <a name="patterns-use-entity-roles"></a>Mönster använder entitetsroller
Om två eller flera entiteter i ett mönster [roles](luis-concept-roles.md) är kontextuellt relaterade använder mönster entitetsroller för att extrahera kontextuell information om entiteter.  

## <a name="prediction-scores-with-and-without-patterns"></a>Förutsägelsepoäng med och utan mönster
Med tillräckligt många exempel yttranden, luis skulle kunna öka förutsägelse förtroende utan mönster. Mönster ökar konfidenspoängen utan att behöva ge så många yttranden.  

## <a name="pattern-matching"></a>Mönstermatchning
Ett mönster matchas baserat på att identifiera entiteterna i mönstret först och sedan validera resten av orden och ordordningen i mönstret. Entiteter krävs i mönstret för att ett mönster ska matcha. Mönstret används på tokennivå, inte teckennivån. 

## <a name="pattern-only-apps"></a>Appar med endast mönster
Du kan skapa en app med avsikter som inte har några exempelyttranden, så länge det finns ett mönster för varje avsikt. För en app med endast mönster bör mönstret inte innehålla maskininlärda entiteter eftersom dessa kräver exempelyttranden. 

## <a name="best-practices"></a>Bästa praxis
Läs [bästa praxis](luis-concept-best-practices.md).

## <a name="pattern-syntax"></a>Mönstersyntax

Lär dig mönstersyntax från [mönstersyntaxens referens](reference-pattern-syntax.md). 

## <a name="next-steps"></a>Nästa steg

Läs mer om mönster:

* [Så här lägger du till mönster](luis-how-to-model-intent-pattern.md)
* [Så här lägger du till pattern.any-entitet](luis-how-to-add-entities.md#add-a-patternany-entity)
* [Syntax för mönster](reference-pattern-syntax.md)

> [!div class="nextstepaction"]
> [Lär dig hur du implementerar mönster i den här självstudien](luis-tutorial-pattern.md)
