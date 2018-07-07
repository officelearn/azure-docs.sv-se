---
title: Förstå funktionerna i LUIS-appar i Azure | Microsoft Docs
description: Läs mer om funktioner som förbättrar prestandan för en LUIS-app. Bland funktionerna finns frasen listor och mönster för att identifiera reguljära uttryck.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 04/18/2018
ms.author: v-geberr
ms.openlocfilehash: 597948947303b7fdf16f24576620d6f39d7c51f4
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37887454"
---
# <a name="phrase-list-features-in-luis"></a>Fras funktioner i LUIS

I machine learning, en *funktionen* är en särskiljande egenskap eller ett attribut av data där dina system. 

Lägga till funktioner till en språkmodell ge tips om hur du identifierar indata som du vill märka eller klassificera. Funktionerna bidrar LUIS kunna identifiera både avsikter och entiteter, men funktioner är inte avsikter eller entiteter själva. Funktioner kan i stället innehåller exempel på relaterade villkor.  

## <a name="what-is-a-phrase-list-feature"></a>Vad är en fras lista funktion?
En lista med frasen innehåller en uppsättning värden (ord eller fraser) som tillhör samma klass och måste behandlas på samma sätt (t.ex, namn på städer eller produkter). Vad LUIS lär sig om en av dem tillämpas automatiskt på alla andra program. Detta är inte en stängd [lista entitet](luis-concept-entity-types.md#types-of-entities) (exakt text matchar) av ord som matchade.

En lista med frasen lägger till vokabulär app-domänen som en signal för andra att LUIS om dessa ord.

## <a name="how-to-use-phrase-lists"></a>Hur du använder frasen listor
Skapa en fras lista med namnet ”städer” som innehåller värdena London, Paris och Kairo i en resa agent-app. Om du anger ett av följande värden som en enkel enhet i en [exempel uttryck](luis-how-to-add-example-utterances.md#add-simple-entity-label) i ett intent LUIS lär sig att känna igen de andra. 

En lista med frasen kanske utbytbara eller icke-utbytbara. En *utbytbara* frasen listan är för värden som är synonymer, och en *ej utbytbara* frasen listan är avsedd för värden som inte är synonymer men liknar varandra i ett annat sätt. 

## <a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>Fras Visar hjälp med att identifiera enkla utbytbara enheter
Exchangeable frasen listor är ett bra sätt att finjustera prestanda för LUIS-appen. Om din app har problem med att förutsäga yttranden till rätt avsikt eller känna igen entiteter kan du tänka på om talade innehålla ovanliga ord eller ord som kan vara tvetydiga i betydelse. Dessa ord är bra kandidater ska ingå i en fras-lista.

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>Fras Visar hjälp med att identifiera avsikter genom att bättre förstå kontexten
Använd frasen listor för sällsynta, äganderätt och främmande ord. LUIS kan inte identifiera sällsynta och egna ord, samt främmande ord (utanför appen kultur) och därför de ska läggas till i en fras-lista. Den här frasen listan bör markeras ej utbytbara, att indikera att uppsättning sällsynta ord utgör en klass som LUIS bör lär sig att känna igen, men de är inte synonymer eller utbytbara med varandra.

En lista med frasen är inte en instruktion till LUIS för att utföra strikt matchar eller alla villkor i listan över frasen alltid märka likadant. Det är bara ett tips. Du kan ha en fras-lista som anger att ”Patti” och ”Selma” namn, men LUIS kan fortfarande använda kontextinformation att känna igen de betyder något annat i ”gör en reservation för 2 på Patti's Diner till middag” och ”hitta mig Driver anvisningar för att Selma, Georgien ”. 

Att lägga till en fras lista är ett alternativ till att lägga till fler exempel yttranden till en avsikt. 

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>När du ska använda frasen listor jämfört med listan över entiteter
Även om både en fras-lista och listan över entiteter kan påverka yttranden över alla avsikter, gör de detta på ett annat sätt. Använda en fras ska påverka avsikt förutsägelse poäng. Använd en entitet i listan för att påverka entitetextrahering efter en exakt denna matchning. 

### <a name="use-a-phrase-list"></a>Använd en fras-lista
Med en fras lista LUIS fortfarande ta hänsyn till kontext och generalisera för att identifiera objekt som liknar, men inte en exakt matchning som objekt i en lista. Om du behöver LUIS-appen för att kunna generalisera och identifiera nya objekt i en kategori kan du använda en fras-lista. 

När du vill identifiera nya instanser av en entitet, som ett möte scheduler som ska identifiera namnen på nya kontakter eller en inventering-app som ska identifiera nya produkter, använda en annan typ av dator lärt dig entiteten, till exempel en enkel eller hierarkisk entitet. Skapa sedan en lista över ord och fraser fras som hjälper dig att hitta andra ord liknar entiteten LUIS. Den här listan visar LUIS för att identifiera exempel på entiteten genom att lägga till ytterligare multipel till värdet för orden. 

Fras listor liknar domänspecifika ordförråd hjälp med att förbättra kvaliteten på förståelse för både avsikter och entiteter. En gemensam användning av en fras lista är egennamn, till exempel stadsnamn. Namnet på en stad kan vara flera ord, inklusive bindestreck eller apostrofer.
 
### <a name="dont-use-a-phrase-list"></a>Använd inte en fras-lista 
En lista över entitet definierar uttryckligen varje värde en entitet kan ta och bara identifierar värden som matchar exakt. En lista över entitet kan vara lämpligt för en app där alla instanser av en entitet är kända och inte ändras ofta, som mat objekten på en restaurang-meny som ändras sällan. Om du behöver en exakt denna matchning av en entitet kan du inte använda en fras-lista. 

## <a name="best-practices"></a>Bästa praxis
Lär dig [bästa praxis](luis-concept-best-practices.md).

## <a name="next-steps"></a>Nästa steg

Se [Lägg till funktioner](luis-how-to-add-features.md) mer information om hur du lägger till funktioner till din LUIS-app.
