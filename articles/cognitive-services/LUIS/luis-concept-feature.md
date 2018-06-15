---
title: Förstå funktioner i THOMAS appar i Azure | Microsoft Docs
description: Lär dig mer om funktioner som förbättrar prestandan för en THOMAS app. Funktioner är frasen listor och mönster för att identifiera reguljära uttryck.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 04/18/2018
ms.author: v-geberr
ms.openlocfilehash: 416fadaf52d7a71dcaab81aab3bf6099213e5af5
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35356377"
---
# <a name="phrase-list-features-in-luis"></a>Funktioner i frasen i THOMAS

I machine learning, en *funktionen* är en särskiljande egenskap eller ett attribut av data där systemet. 

Lägga till funktioner till en språkmodell ge tips om hur du identifierar indata som du vill etikett eller klassificera. THOMAS identifierar både avsikter och enheter med hjälp av funktionerna, men funktioner är inte avsikter eller enheter själva. Funktioner kan i stället innehåller exempel på tillhörande villkor.  

## <a name="what-is-a-phrase-list-feature"></a>Vad är en fras lista funktion?
En fras lista innehåller en uppsättning värden (ord eller fraser) som tillhör samma klass och måste behandlas på samma sätt (t.ex, namn på städer eller produkter). Vad THOMAS lär sig om en av dem används automatiskt för alla andra program. Detta är inte en stängd [listan entiteten](luis-concept-entity-types.md#types-of-entities) (exakt matchar text) matchade ord.

En fras lista lägger till ordförråd i domänen appen som en andra signal till THOMAS om dessa ord.

## <a name="how-to-use-phrase-lists"></a>Hur du använder frasen listor
Skapa en fras lista med namnet ”orter” som innehåller värdena London och Paris Kairo i en resa agent-app. Om du anger ett av dessa värden som en enkel enhet i en [exempel utterance](luis-how-to-add-example-utterances.md#add-simple-entity-label) i en avsikt, THOMAS lär sig att känna igen de andra. 

En fras lista kan vara utbytbara eller ej utbytbara. En *utbytbara* frasen listan är för värden som är synonymer, och en *ej utbytbara* frasen listan är avsedd för värden som inte är synonymer men liknar varandra i ett annat sätt. 

## <a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>Frasen Visar hjälp identifiera enkla utbytbara enheter
Utbytbara frasen listor är ett bra sätt att justera prestanda för THOMAS appen. Om din app har problem med att förutsäga utterances till rätt avsikten eller identifiera enheter, tänka på om utterances innehålla ovanliga ord eller ord som kan vara tvetydig i betydelse. Dessa ord är bra kandidater ska ingå i en fras lista.

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>Frasen Visar hjälp identifiera avsikter genom att bättre förstå kontext
Använd frasen listor för sällsynta upphovsrättsskyddad och främmande ord. THOMAS kanske inte går att identifiera sällsynt och egna ord, samt ord (utanför appen kultur) och därför de ska läggas till en fras lista. Den här listan frasen markeras ej utbytbara, att indikera att en uppsättning sällsynta ord utgör en klass som THOMAS ska lära dig att identifiera, men de är inte synonymer eller utbytbara med varandra.

En fras lista är inte en instruktion till THOMAS utför strikt matchande eller alla villkor i listan frasen alltid etiketten exakt samma. Det är bara ett tips. Du kan ha en fras lista som anger att ”Patti” och ”Selma” är namn, men THOMAS kan fortfarande använda kontextuella information för att identifiera de betyder något annat i ”Skapa en reservation för 2 på Patti's Diner för middag” och ”fem me körning anvisningar till Selma, Georgia ”. 

Lägga till en fras lista är ett alternativ till att lägga till fler exempel utterances syftet. 

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>När du ska använda frasen listor jämfört med listan entiteter
När både en fras lista och listan entiteter kan påverka utterances över alla avsikter, gör de detta på olika sätt. Använda en fras ska påverka avsiktshantering förutsägelse poäng. Använda en lista över entitet för att påverka entitet extrahering efter en exakt matchning. 

### <a name="use-a-phrase-list"></a>Använd en fras lista
Med en fras lista THOMAS fortfarande ta hänsyn till kontexten och generalize för att identifiera objekt som liknar, men inte en exakt matchning, som i en lista. Om du behöver THOMAS appen för att kunna generalisera och identifiera nya objekt i en kategori kan du använda en fras lista. 

När du vill kunna identifiera nya instanser av en entitet som en möte Schemaläggaren som ska identifiera namnen på nya kontakter eller en inventering app som ska identifiera nya produkter, Använd en annan typ av dator lärt dig entitet som en enkel eller hierarkisk entitet. Skapa en fras lista över ord och fraser som hjälper THOMAS efter ord som liknar enheten. Den här listan hjälper THOMAS för att identifiera exempel på enheten genom att lägga till ytterligare signifikans värdet för orden. 

Frasen listor är som domänspecifika ordförråd som hjälper till med att förbättra kvaliteten på förståelse för både avsikter och enheter. En gemensam användning av en fras lista är namn, till exempel city namn. Namnet på en stad kan vara flera ord inklusive bindestreck eller apostrofer.
 
### <a name="dont-use-a-phrase-list"></a>Använd inte en fras lista 
En lista över entitet definierar uttryckligen varje värde för en entitet kan ta och bara identifierar värden som matchar exakt. En lista över entitet kan vara lämpligt för en app där alla instanser av en entitet är kända och inte ändras ofta, som mat objekten på en restaurang meny som ändras sällan. Om du behöver en exakt matchning av en entitet kan inte använda en fras lista. 

## <a name="best-practices"></a>Bästa praxis
Läs [metodtips](luis-concept-best-practices.md).

## <a name="next-steps"></a>Nästa steg

Se [Lägg till funktioner](luis-how-to-add-features.md) lära dig mer om hur du lägger till funktioner i appen THOMAS.