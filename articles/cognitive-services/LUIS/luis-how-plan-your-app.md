---
title: Planera din app
titleSuffix: Language Understanding - Azure Cognitive Services
description: Beskriver relevanta avsikter och entiteter och sedan skapa dina program-planer i Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/26/2018
ms.author: diberry
ms.openlocfilehash: e14b9f2930ed9c170f31bd654829efe3b5a99446
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091315"
---
# <a name="plan-your-luis-app"></a>Planera LUIS-appen

Det är viktigt att planera din app. Identifiera din domän, inklusive möjliga avsikter och entiteter som är relevanta för ditt program.  

## <a name="identify-your-domain"></a>Identifiera din domän
En LUIS-app är uppbyggd kring ett domänspecifika ämne.  Du kanske till exempel en reseapp som utför bokning av biljetter, flyg, hotell och uthyrning bilar. En annan app kan tillhandahålla innehåll som rör utöva, spåra arbete lämplighet och ange mål. Anger vilken domän hjälper dig att söka efter ord eller fraser som är viktiga för din domän.

> [!TIP]
> LUIS erbjuder [fördefinierade domäner](luis-how-to-use-prebuilt-domains.md) för många vanliga scenarier.
> Kontrollera om du kan använda en fördefinierade domän som en startpunkt för din app.

## <a name="identify-your-intents"></a>Identifiera dina avsikter
Tänk på hur den [avsikter](luis-concept-intent.md) som är viktiga för ditt programs uppgift. Låt oss ta exempel på en reseapp med funktioner för att boka en flygning och kontrollera vädret vid användarens mål. Du kan definiera avsikterna ”BookFlight” och ”GetWeather” för dessa åtgärder. Du har flera avsikter i mer komplexa appar med fler funktioner, och du bör definiera dem noggrant så att den inte är specifika för. Till exempel ”BookFlight” och ”BookHotel” kan behöva vara separata avsikter men ”BookInternationalFlight” och ”BookDomesticFlight” kan vara för liknande.

> [!NOTE]
> Det är en bra idé att bara använda så många avsikter som behövs för att fungera som din app. Om du definierar för många avsikter, blir det svårare för LUIS för att klassificera yttranden korrekt. Om du definierar för några, de kan vara så allmänt att vara överlappande.

## <a name="create-example-utterances-for-each-intent"></a>Skapa exempel yttranden för varje avsikt
När du har bestämt avsikter, skapa 10 eller 15 exempel yttranden för varje avsikt. Börjar med inte har färre än det här talet eller skapa många yttranden för varje avsikt. Varje uttryck ska skilja sig från föregående uttryck. En bra rad i talade innehåller övergripande ordräkning, word-val, verb Tempus och skiljetecken. 

## <a name="identify-your-entities"></a>Identifiera dina entiteter
Identifiera de entiteter som du vill extrahera i exempel-yttranden. För att boka en flygning behöver viss information som mål, datum, flygbolag, biljett kategori och reser klass. Du skapar entiteter för dessa datatyper och markera sedan den [entiteter](luis-concept-entity-types.md) i exempel-yttranden eftersom de är viktiga för att utföra en avsikt. 

När du bestämmer vilka entiteter som ska använda i din app, Kom ihåg att det finns olika typer av enheter för att samla in relationer mellan olika typer av objekt. [Entiteter i LUIS](luis-concept-entity-types.md) innehåller mer information om de olika typerna.

### <a name="simple-entity"></a>Enkel entitet
En enkel enhet beskriver ett enda koncept.

![enkel enhet](./media/luis-plan-your-app/simple-entity.png)

Se [Dataextrahering](luis-concept-data-extraction.md#simple-entity-data) mer information om hur du extraherar den enkla enheten från slutpunkten JSON frågesvaret. Prova följande [snabbstarten](luis-quickstart-primary-and-secondary-data.md) mer information om hur du använder en enkel enhet.

### <a name="hierarchical-entity"></a>Hierarkisk entitet
En hierarkisk entitet är en särskild typ av en **enkel** entitet, definiera en kategori och dess medlemmar i form av överordnad-underordnad-relation. Relationen bestäms av kontext från inom uttryck. Underordnade grupper till en hierarkisk entitet är också enkla enheter.

![hierarkisk entitet](./media/luis-plan-your-app/hierarchical-entity.png)

Se [Dataextrahering](luis-concept-data-extraction.md#hierarchical-entity-data) mer information om hur du extraherar den hierarkiska entiteten från slutpunkten JSON frågesvaret. Prova följande [snabbstarten](luis-quickstart-intent-and-hier-entity.md) mer information om hur du använder en hierarkisk entitet.

### <a name="composite-entity"></a>Sammansatt entitet
En sammansatt entitet består av andra entiteter som utgör delar av en helhet. En sammansatt entitet innehåller en mängd olika typer av enheter.

![Sammansatt entitet](./media/luis-plan-your-app/composite-entity.png)

Se [Dataextrahering](luis-concept-data-extraction.md#composite-entity-data) mer information om hur du extraherar den sammansatta entiteten från slutpunkten JSON frågesvaret. Prova följande [självstudien](luis-tutorial-composite-entity.md) mer information om hur du använder en sammansatt entitet.

### <a name="prebuilt-entity"></a>Fördefinierade entitet
LUIS ger [förskapade entiteter](luis-prebuilt-entities.md) för vanliga datatyper, till exempel antal, data, e-postadress och URL: en. Du kan använda nummer fördefinierade entiteten för antal ärenden i en biljett ordning.

![Antal fördefinierade entitet](./media/luis-plan-your-app/number-entity.png)

Se [Dataextrahering](luis-concept-data-extraction.md#prebuilt-entity-data) mer information om hur du extraherar den fördefinierade entiteten från slutpunkten JSON frågesvaret. 

### <a name="list-entity"></a>Lista entitet 
En lista över entitet är en explicit angiven lista med värden. Varje värde består av en eller flera synonymer. Du kan välja att skapa en lista över entitet som representerar en flygplats namnen i en reseapp.

![lista entitet](./media/luis-plan-your-app/list-entity.png)

Se [Dataextrahering](luis-concept-data-extraction.md#list-entity-data) mer information om extrahering av listan över entiteter från slutpunkten JSON frågesvaret. Prova följande [snabbstarten](luis-quickstart-intent-and-list-entity.md) mer information om hur du använder en entitet i listan.

### <a name="regular-expression-entity"></a>Entitet för reguljära uttryck
En entitet i reguljära uttryck kan LUIS för att extrahera välutformat data från ett uttryck baserat på ett reguljärt uttryck.

![Entitet för reguljära uttryck](./media/luis-plan-your-app/regex-entity.png)

Se [Dataextrahering](luis-concept-data-extraction.md#regular-expression-entity-data) mer information om hur du extraherar reguljärt uttryck entiteter från slutpunkten JSON frågesvaret. Prova den [snabbstarten](luis-quickstart-intents-regex-entity.md) mer information om hur du använder en entitet för reguljärt uttryck.

## <a name="next-steps"></a>Nästa steg
När din app har tränats publicerats och hämtar endpoint yttranden, planerar att implementera förutsägelse förbättringar med [aktiv inlärning](luis-how-to-review-endoint-utt.md), [fras listor](luis-concept-feature.md), och [mönster](luis-concept-patterns.md). 


* Se [skapa din första app Language Understanding Intelligent Service (LUIS)](luis-get-started-create-app.md) för en snabb genomgång av hur du skapar en LUIS-app.
