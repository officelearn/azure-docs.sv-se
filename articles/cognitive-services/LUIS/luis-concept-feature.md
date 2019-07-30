---
title: Funktioner – LUIS
titleSuffix: Azure Cognitive Services
description: Lägga till funktioner till en språkmodell ge tips om hur du identifierar indata som du vill märka eller klassificera.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: e85f9a90af778a7e68bf761d8d2159e808cf122d
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639205"
---
# <a name="phrase-list-features-in-your-luis-app"></a>Fras List funktioner i LUIS-appen

I machine learning, en *funktionen* är en särskiljande egenskap eller ett attribut av data där dina system. 

Lägga till funktioner till en språkmodell ge tips om hur du identifierar indata som du vill märka eller klassificera. Funktionerna bidrar LUIS kunna identifiera både avsikter och entiteter, men funktioner är inte avsikter eller entiteter själva. Funktioner kan i stället innehåller exempel på relaterade villkor.  

## <a name="what-is-a-phrase-list-feature"></a>Vad är en fras lista funktion?
En fras lista är en lista över ord eller fraser som är viktiga för din app, mer än andra ord i yttranden. En fras lista lägger till i program domänens vokabulär som en ytterligare signal för att LUIS om orden. Vad LUIS lär sig om en av dem tillämpas automatiskt på alla andra program. Den här listan är inte en stängd [list-entitet](luis-concept-entity-types.md#types-of-entities) med exakt text matchning.

Fras listorna bidrar inte till att du behöver lägga till uttryck-exempel som använder en rad olika ord listor och fraser.

## <a name="phrase-lists-help-all-models"></a>Fras listorna hjälper alla modeller

Fras listor är inte länkade till en speciell avsikt eller entitet, men läggs till som en betydande ökning av alla avsikter och entiteter. Syftet med detta är att förbättra identifieringen av avsikts identifiering och enhets klassificering.

## <a name="how-to-use-phrase-lists"></a>Hur du använder frasen listor

[Skapa en fras](luis-how-to-add-features.md) lista när din app har ord eller fraser som är viktiga för appen, till exempel:

* bransch villkor
* slang
* förkortningar
* företagsspecifika språk
* språk som är från ett annat språk men som ofta används i din app
* nyckelord och fraser i ditt exempel yttranden

När du har angett några ord eller fraser använder du den **rekommenderade** funktionen för att hitta relaterade värden. Granska de relaterade värdena innan du lägger till dem i dina fras List värden.

|Listtyp|Syfte|
|--|--|
|Utbytbara|Synonymer eller ord som, när de ändras till ett annat ord i listan, har samma avsikts-och enhets extrahering.|
|Icke-utbytbart|Appens vokabulär, som är specifik för din app, mer än vanligt vis andra ord på det språket.|

### <a name="interchangeable-lists"></a>Listor med utbytbara

En lista med utbytbara fraser är för värden som är synonymer. Om du till exempel vill att alla vatten förekomster hittas och du har exempel yttranden som: 

* Vilka städer är nära de fantastiska sjöarna? 
* Vilken väg körs i sjön-Havasu?
* Var börjar och slutar Nileen? 

Varje uttryck bör bestämmas för både avsikt och entiteter oavsett vatten förekomst: 

* Vilka städer är nära [bodyOfWater]?
* Vilken väg körs tillsammans [bodyOfWater]?
* Var börjar och slutar [bodyOfWater]? 

Eftersom ord eller fraser för bröd texten i vatten är synonyma och kan användas utbytbara i yttranden, använder du den utbytbara inställningen på fras listan. 

### <a name="non-interchangeable-lists"></a>Listor som inte är utbytbara

En icke-utbytbar fras lista är en signal som höjer identifieringen till LUIS. Fras listan visar ord eller fraser som är mer betydande än andra ord. Detta hjälper både att identifiera matchning av avsikt och entitet. Anta till exempel att du har en ämnes domän som en global domän (vilket innebär att den är i flera kulturer men fortfarande på ett och samma språk). Det finns ord och fraser som är viktiga för appen, men som inte är synonyma. 

För ett annat exempel använder du en lista med icke-utbytbara fraser för ovanliga, tillverkarspecifika och främmande ord. LUIS kanske inte att identifiera sällsynta och egna ord, samt främmande ord (utanför appen kultur). Icke-utbytbara inställningen anger att uppsättning sällsynta ord utgör en klass som LUIS bör lär sig att känna igen, men de är inte synonymer eller utbytbara med varandra.

Lägg inte till alla möjliga ord eller fraser i en fras lista, Lägg till några ord eller fraser i taget, och omträna och publicera. 

När fras listan växer med tiden kan det hända att vissa termer har många former (synonymer). Bryt ut dessa i en annan fras lista som är utbytbara. 

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="phrase-lists-help-identify-simple-interchangeable-entities"></a>Fras listor hjälper till att identifiera enkla utbytbara entiteter
Utbytbara frasen listor är ett bra sätt att finjustera prestanda för LUIS-appen. Om din app har problem med att förutsäga yttranden till rätt avsikt eller känna igen entiteter kan du tänka på om talade innehålla ovanliga ord eller ord som kan vara tvetydiga i betydelse. Dessa ord är bra kandidater ska ingå i en fras-lista.

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>Fras Visar hjälp med att identifiera avsikter genom att bättre förstå kontexten
En lista med frasen är inte en instruktion till LUIS för att utföra strikt matchar eller alla villkor i listan över frasen alltid märka likadant. Det är bara ett tips. Du kan ha en fras-lista som anger att ”Patti” och ”Selma” namn, men LUIS kan fortfarande använda kontextinformation att känna igen de betyder något annat i ”gör en reservation för 2 på Patti's Diner till middag” och ”hitta mig Driver anvisningar för att Selma, Georgien ”. 

Att lägga till en fras lista är ett alternativ till att lägga till fler exempel yttranden till en avsikt. 

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>När du ska använda frasen listor jämfört med listan över entiteter
Både en fras lista och en [lista över entiteter](reference-entity-list.md) kan påverka yttranden i alla avsikter, varje gör detta på ett annat sätt. Använda en fras ska påverka avsikt förutsägelse poäng. Använd en entitet i listan för att påverka entitetextrahering efter en exakt denna matchning. 

### <a name="use-a-phrase-list"></a>Använd en fras-lista
Med en fras lista LUIS fortfarande ta hänsyn till kontext och generalisera för att identifiera objekt som liknar, men inte en exakt matchning som objekt i en lista. Om du behöver LUIS-appen för att kunna generalisera och identifiera nya objekt i en kategori kan du använda en fras-lista. 

När du vill kunna identifiera nya instanser av en entitet, till exempel en schemaläggare för möten som ska identifiera namnen på nya kontakter, eller en inventerings app som ska identifiera nya produkter, använder du en annan typ av enhets medveten entitet, till exempel en enkel enhet. Skapa sedan en lista över ord och fraser fras som hjälper dig att hitta andra ord liknar entiteten LUIS. Den här listan visar LUIS för att identifiera exempel på entiteten genom att lägga till ytterligare multipel till värdet för orden. 

Fras listor liknar domänspecifika ordförråd hjälp med att förbättra kvaliteten på förståelse för både avsikter och entiteter. En gemensam användning av en fras lista är egennamn, till exempel stadsnamn. Namnet på en stad kan vara flera ord, inklusive bindestreck eller apostrofer.
 
### <a name="dont-use-a-phrase-list"></a>Använd inte en fras-lista 
En lista över entitet definierar uttryckligen varje värde en entitet kan ta och bara identifierar värden som matchar exakt. En lista över entitet kan vara lämpligt för en app där alla instanser av en entitet är kända och inte ändras ofta. Exempel är mat objekt på en restaurang-meny som ändras sällan. Om du behöver en exakt denna matchning av en entitet kan du inte använda en fras-lista. 

## <a name="best-practices"></a>Bästa praxis
Lär dig [bästa praxis](luis-concept-best-practices.md).

## <a name="next-steps"></a>Nästa steg

Se [Lägg till funktioner](luis-how-to-add-features.md) mer information om hur du lägger till funktioner till din LUIS-app.
