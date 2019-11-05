---
title: Funktioner – LUIS
titleSuffix: Azure Cognitive Services
description: Lägg till funktioner i en språk modell för att ge tips om hur du identifierar indatatyper som du vill etikettera eller klassificera.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: diberry
ms.openlocfilehash: 08f78e4945b612a92d372c832490c380d3749811
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487514"
---
# <a name="machine-learned-features"></a>Funktioner som har lästs av datorn 

I Machine Learning är en *funktion* en särskiljande egenskaps-eller dataattribut som systemet iakttar & lär sig igenom. I Language Understanding (LUIS) beskriver en funktion och förklarar vad som är viktigt om dina avsikter och entiteter.

## <a name="features-in-language-understanding"></a>Funktioner i Language Understanding

Funktioner, även kallade beskrivningar, beskriver LED trådar för att hjälpa Language Understanding att identifiera exemplet yttranden. Funktionerna omfattar: 

* Fras lista som en funktion till avsikter eller entiteter
* Entiteter som funktioner till avsikter eller entiteter

Funktioner bör betraktas som en nödvändig del av ditt schema för modells sammansättning. 

## <a name="what-is-a-phrase-list"></a>Vad är en fras lista

En fras lista är en lista över ord, fraser, siffror eller andra tecken som hjälper dig att identifiera det koncept som du försöker identifiera. Listan är Skift läges okänslig. 

## <a name="when-to-use-a-phrase-list"></a>När du ska använda en fras lista

Med en fras lista anser LUIS kontext och generalizes för att identifiera objekt som liknar, men inte en exakt text matchning. Använd en fras lista om du behöver LUIS-appen för att kunna generalisera och identifiera nya objekt. 

När du vill kunna identifiera nya instanser, till exempel en schemaläggare för möten som ska identifiera namnen på nya kontakter eller en inventerings app som ska identifiera nya produkter, börjar du med en enhet som har lärts från enheten. Skapa sedan en fras lista som hjälper LUIS att hitta ord med liknande betydelse. Den här frasen visar en lista över LUIS som hjälper dig att identifiera exempel genom att lägga till ytterligare betydelse för värdet av dessa ord. 

Fras listor är som domänbaserad vokabulär som hjälper till att förbättra kvaliteten på både avsikter och entiteter. 

## <a name="considerations-when-using-a-phrase-list"></a>Att tänka på när du använder en fras lista

En fras lista används som standard för alla modeller i appen. Detta fungerar för fras listor som kan korsa alla avsikter och entiteter. För desammansättning kan du använda en fras lista för att bara de modeller som är relevanta för. 

Om du skapar en fras lista (som skapas globalt som standard), tillämpas den senare som en beskrivning (funktion) till en speciell modell, tas den bort från de andra modellerna. Den här borttagningen lägger till relevans för fras listan för den modell som den tillämpas på, vilket ger bättre noggrannhet i modellen. 

Flaggan `enabledForAllModels` styr det här modell omfånget i API: et. 

<a name="how-to-use-phrase-lists"></a>

### <a name="how-to-use-a-phrase-list"></a>Så här använder du en fras lista

[Skapa en fras](luis-how-to-add-features.md) lista när ditt avsikt eller din entitet har ord eller fraser som är viktiga, till exempel:

* bransch villkor
* slang
* förkortningar
* företagsspecifika språk
* språk som är från ett annat språk men som ofta används i din app
* nyckelord och fraser i ditt exempel yttranden

Lägg **inte** till varje möjligt ord eller fras. Lägg i stället till några ord eller fraser i taget, och sedan träna och publicera. När listan växer med tiden kan det hända att vissa termer har många former (synonymer). Dela upp dessa i en annan lista. 

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="when-to-use-an-entity-as-a-feature"></a>När du ska använda en entitet som en funktion 

En entitet kan läggas till som en funktion på avsikts-eller enhets nivå. 

### <a name="entity-as-a-feature-to-an-intent"></a>Entitet som en funktion till ett avsikts sätt

Lägg till en entitet som en beskrivning (funktion) i avsikt när identifieringen av den enheten är signifikant för avsikten.

Om avsikten exempelvis är att boka en flygning och entiteten är biljett information (t. ex. antalet platser, ursprung och mål), ska du söka efter entiteten med biljett information genom att lägga till vikt till förutsägelsen av bokens flyg avsikt. 

### <a name="entity-as-a-feature-to-another-entity"></a>Entitet som en funktion till en annan entitet

En entitet (A) ska läggas till som en funktion till en annan entitet (B) när entitetens identifiering (A) är signifikant för (B).

Om t. ex. entiteten gatuadress (A) har identifierats lägger du till en vikt till förutsägelsen för entiteten leverans adress (B) genom att söka efter gatuadressen (A). 

## <a name="best-practices"></a>Bästa praxis
Lär dig [metod tips](luis-concept-best-practices.md).

## <a name="next-steps"></a>Nästa steg

Se [Lägg till funktioner](luis-how-to-add-features.md) för att lära dig mer om hur du lägger till funktioner i Luis-appen.