---
title: Funktioner
titleSuffix: Language Understanding - Azure Cognitive Services
description: Lägga till funktioner till en språkmodell ge tips om hur du identifierar indata som du vill märka eller klassificera.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/04/2019
ms.author: diberry
ms.openlocfilehash: fa0a8c4baa9458a3784babe1e342d768748f8a4d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55882462"
---
# <a name="phrase-list-features-in-your-luis-app"></a>Fras funktioner i din LUIS-app

I machine learning, en *funktionen* är en särskiljande egenskap eller ett attribut av data där dina system. 

Lägga till funktioner till en språkmodell ge tips om hur du identifierar indata som du vill märka eller klassificera. Funktionerna bidrar LUIS kunna identifiera både avsikter och entiteter, men funktioner är inte avsikter eller entiteter själva. Funktioner kan i stället innehåller exempel på relaterade villkor.  

## <a name="what-is-a-phrase-list-feature"></a>Vad är en fras lista funktion?
En fras lista är en lista över ord eller fraser som är relevanta för att din app, viktigare än andra ord i yttranden. En lista med frasen lägger till vokabulär app-domänen som en ytterligare signalen att LUIS om dessa ord. Vad LUIS lär sig om en av dem tillämpas automatiskt på alla andra program. Den här listan är inte en stängd [lista entitet](luis-concept-entity-types.md#types-of-entities) textens exakt matchar.

Fras listor hjälper inte med ordstamsigenkänning så du måste lägga till uttryck exempel som använder olika ordstamsigenkänning för några betydande ordförråd ord och fraser.

## <a name="phrase-lists-help-all-models"></a>Fras listorna hjälper att alla modeller

Fras listor har länkats inte till en specifik avsikt eller enhet men har lagts till som en betydande boost till alla avsikter och entiteter. Syftet är att förbättra avsikt identifiering och entiteten klassificering.

## <a name="how-to-use-phrase-lists"></a>Hur du använder frasen listor

Skapa en fras-lista när din app har ord eller fraser som är viktiga för appen som:

* branschvillkor
* slang
* förkortningar
* företagsspecifika språk
* språk som är ett annat språk men ofta används i din app
* nyckelord och fraser i exempel-uttryck

När du har angett några ord eller fraser kan du använda den **rekommenderar** funktionen för att hitta relaterade värden. Granska relaterade värden innan du lägger till din frasen listvärden.

|Listtyp|Syfte|
|--|--|
|Interchangeable|Synonymer eller ord, när ändrades till ett annat ord. i listan, har samma syfte och entitetextrahering.|
|Icke-utbytbara|App ordförråd, specifika för din app mer så än Allmänt andra ord på det språket.|

### <a name="interchangeable-lists"></a>Utbytbara listor

En *utbytbara* frasen listan är för värden som är synonymer. Om du vill hitta alla organ vatten och du har exempel yttranden som: 

* Vilka städer närmar sig bra sjöar? 
* Vilken väg körs längs Lake Havasu?
* Där Nile börja och sluta? 

Varje uttryck ska fastställas för både avsikten och entiteter, oavsett mängd vatten: 

* Vilka städer är nära [bodyOfWater]?
* Vilken väg körs längs [bodyOfWater]?
* Där [bodyOfWater] börja och sluta? 

Eftersom ord eller fraser för innehållet i water är synonyma och kan användas antingen i talade, använda den **utbytbara** på listan med fraser. 

### <a name="non-interchangeable-lists"></a>Icke-utbytbara listor

En lista med icke-utbytbara frasen är en signal som förstärker identifiering till LUIS. Listan över frasen anger ord eller fraser som är viktigare att andra ord. Detta hjälper med både avsikten och entiteten identifiering av avgörande. Anta exempelvis att du har en domän för ämne som resa som är globala (vilket innebär att över kulturer men fortfarande i ett enda språk). Det finns ord och fraser som är viktiga för appen, men är inte synonymer. 

För ett annat exempel är att använda en icke-utbytbara frasen lista för sällsynta, äganderätt och främmande ord. LUIS kanske inte att identifiera sällsynta och egna ord, samt främmande ord (utanför appen kultur). Icke-utbytbara inställningen anger att uppsättning sällsynta ord utgör en klass som LUIS bör lär sig att känna igen, men de är inte synonymer eller utbytbara med varandra.

Inte lägga till varje möjligt ord eller fraser i en fras-lista, lägga till några ord eller fraser i taget, och sedan träna och publicera. 

När listan frasen ökar med tiden, kanske vissa begrepp att ha många formulär (synonymer). Bryt dessa till en annan frasen-lista som är utbytbara. 

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="phrase-lists-help-identify-simple-interchangeable-entities"></a>Fras Visar hjälp med att identifiera enkla utbytbara enheter
Utbytbara frasen listor är ett bra sätt att finjustera prestanda för LUIS-appen. Om din app har problem med att förutsäga yttranden till rätt avsikt eller känna igen entiteter kan du tänka på om talade innehålla ovanliga ord eller ord som kan vara tvetydiga i betydelse. Dessa ord är bra kandidater ska ingå i en fras-lista.

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>Fras Visar hjälp med att identifiera avsikter genom att bättre förstå kontexten
En lista med frasen är inte en instruktion till LUIS för att utföra strikt matchar eller alla villkor i listan över frasen alltid märka likadant. Det är bara ett tips. Du kan ha en fras-lista som anger att ”Patti” och ”Selma” namn, men LUIS kan fortfarande använda kontextinformation att känna igen de betyder något annat i ”gör en reservation för 2 på Patti's Diner till middag” och ”hitta mig Driver anvisningar för att Selma, Georgien ”. 

Att lägga till en fras lista är ett alternativ till att lägga till fler exempel yttranden till en avsikt. 

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>När du ska använda frasen listor jämfört med listan över entiteter
Även om både en fras-lista och listan över entiteter kan påverka yttranden över alla avsikter, gör de detta på ett annat sätt. Använda en fras ska påverka avsikt förutsägelse poäng. Använd en entitet i listan för att påverka entitetextrahering efter en exakt denna matchning. 

### <a name="use-a-phrase-list"></a>Använd en fras-lista
Med en fras lista LUIS fortfarande ta hänsyn till kontext och generalisera för att identifiera objekt som liknar, men inte en exakt matchning som objekt i en lista. Om du behöver LUIS-appen för att kunna generalisera och identifiera nya objekt i en kategori kan du använda en fras-lista. 

När du vill identifiera nya instanser av en entitet, som ett möte scheduler som ska identifiera namnen på nya kontakter eller en inventering-app som ska identifiera nya produkter, använda en annan typ av dator lärt dig entiteten, till exempel en enkel eller hierarkisk entitet. Skapa sedan en lista över ord och fraser fras som hjälper dig att hitta andra ord liknar entiteten LUIS. Den här listan visar LUIS för att identifiera exempel på entiteten genom att lägga till ytterligare multipel till värdet för orden. 

Fras listor liknar domänspecifika ordförråd hjälp med att förbättra kvaliteten på förståelse för både avsikter och entiteter. En gemensam användning av en fras lista är egennamn, till exempel stadsnamn. Namnet på en stad kan vara flera ord, inklusive bindestreck eller apostrofer.
 
### <a name="dont-use-a-phrase-list"></a>Använd inte en fras-lista 
En lista över entitet definierar uttryckligen varje värde en entitet kan ta och bara identifierar värden som matchar exakt. En lista över entitet kan vara lämpligt för en app där alla instanser av en entitet är kända och inte ändras ofta. Exempel är mat objekt på en restaurang-meny som ändras sällan. Om du behöver en exakt denna matchning av en entitet kan du inte använda en fras-lista. 

## <a name="best-practices"></a>Bästa praxis
Lär dig [bästa praxis](luis-concept-best-practices.md).

## <a name="next-steps"></a>Nästa steg

Se [Lägg till funktioner](luis-how-to-add-features.md) mer information om hur du lägger till funktioner till din LUIS-app.
