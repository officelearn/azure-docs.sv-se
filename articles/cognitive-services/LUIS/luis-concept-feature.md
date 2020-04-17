---
title: Funktioner - LUIS
titleSuffix: Azure Cognitive Services
description: Lägg till funktioner i en språkmodell för att ge tips om hur du känner igen indata som du vill märka eller klassificera.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: diberry
ms.openlocfilehash: 5b8257e24cf52d01be8065d97db17fd685aa316d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531906"
---
# <a name="machine-learned-features"></a>Maskininlärda funktioner

I maskininlärning är en _funktion_ ett särskiljande drag eller attribut för data som ditt system observerar & lär sig igenom. I Language Understanding (LUIS) beskriver och förklarar en funktion vad som är viktigt med dina avsikter och entiteter.

I [luis-portalen för förhandsversionen](https://preview.luis.ai)är funktionerna _beskrivningar_ eftersom de används för att _beskriva_ avsikten eller entiteten.

## <a name="features-_descriptors_-in-language-understanding"></a>Funktioner _(deskriptorer)_ i språk understanding

Funktioner, även kallade deskriptorer, beskriver ledtrådar för att hjälpa Språkförståelse att identifiera exempelyttranden. Funktionerna omfattar:

* Fraslista som en funktion i avsikter eller entiteter
* Entiteter som funktioner i avsikter eller entiteter

Funktioner bör betraktas som en nödvändig del av schemat för modellförsprutning.

## <a name="what-is-a-phrase-list"></a>Vad är en fraslista

En fraslista är en lista med ord, fraser, siffror eller andra tecken som hjälper till att identifiera det koncept du försöker identifiera. Listan är skiftlägesokänslig.

## <a name="when-to-use-a-phrase-list"></a>När ska en fraslista användas

Med en fraslista tar LUIS hänsyn till kontext och generaliserar för att identifiera objekt som liknar, men inte en exakt textmatchning. Om du behöver LUIS-appen för att kunna generalisera och identifiera nya objekt använder du en fraslista.

När du vill kunna känna igen nya instanser, till exempel en mötesschemaläggare som ska känna igen namnen på nya kontakter eller en lagerapp som ska känna igen nya produkter, börjar du med en datorinlärd entitet. Skapa sedan en fraslista som hjälper LUIS att hitta ord med liknande innebörd. Den här fraslistan vägleder LUIS att känna igen exempel genom att lägga till ytterligare betydelse för värdet av dessa ord.

Fraslistor är som domänspecifika ordförråd som hjälper till att förbättra kvaliteten på förståelsen av både avsikter och entiteter.

## <a name="considerations-when-using-a-phrase-list"></a>Överväganden när du använder en fraslista

En fraslista används som standard på alla modeller i appen. Detta kommer att fungera för fraslistor som kan korsa alla avsikter och entiteter. För förmultning bör du använda en fraslista på endast de modeller som den är relevant för.

Om du skapar en fraslista (skapad globalt som standard) och sedan senare använder den som en deskriptor (funktion) på en viss modell tas den bort från de andra modellerna. Den här borttagningen lägger till relevans i fraslistan för modellen som den tillämpas på, vilket bidrar till att förbättra noggrannheten som den ger i modellen.

Flaggan `enabledForAllModels` styr den här modellens scope i API:et.

<a name="how-to-use-phrase-lists"></a>

### <a name="how-to-use-a-phrase-list"></a>Så här använder du en fraslista

[Skapa en fraslista](luis-how-to-add-features.md) när din avsikt eller entitet har ord eller fraser som är viktiga, till exempel:

* branschtermer
* Slang
* Förkortningar
* företagsspecifikt språk
* språk som kommer från ett annat språk men som ofta används i appen
* nyckelord och fraser i ditt exempel yttranden

Lägg **inte** till alla möjliga ord eller fraser. Lägg i stället till några ord eller fraser i taget och sedan träna om och publicera. När listan växer med tiden kan vissa termer finnas många formulär (synonymer). Dela upp de här i en annan lista.

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="when-to-use-an-entity-as-a-feature"></a>När ska en entitet användas som funktion

En entitet kan läggas till som en funktion på avsikts- eller entitetsnivå.

### <a name="entity-as-a-feature-to-an-intent"></a>Entitet som en funktion i avsikt

Lägg till en entitet som en deskriptor (funktion) i en avsikt när identifieringen av den entiteten är viktig för avsikten.

Om avsikten till exempel är att boka en flygning och enheten är biljettinformation (till exempel antalet platser, ursprung och destination) ska du hitta entiteten för biljettinformation lägga vikt vid förutsägelsen av bokens flygavsikt.

### <a name="entity-as-a-feature-to-another-entity"></a>Entitet som en funktion till en annan entitet

En entitet (A) ska läggas till som en funktion i en annan enhet (B) när detektionen av den enheten (A) är betydande för förutsägelsen av entitet (B).

Om gatuadressentiteten (A) till exempel identifieras lägger gatuadressen (A) vikt till förutsägelsen för leveransadressentiteten (B).

## <a name="best-practices"></a>Bästa praxis
Läs [bästa praxis](luis-concept-best-practices.md).

## <a name="next-steps"></a>Nästa steg

* [Utöka](schema-change-prediction-runtime.md) dina appmodeller vid förutsägelsekörning
* Se [Lägg till funktioner](luis-how-to-add-features.md) om du vill veta mer om hur du lägger till funktioner i LUIS-appen.
