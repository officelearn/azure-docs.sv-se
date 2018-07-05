---
title: Planera din LUIS-program | Microsoft Docs
description: Beskriver relevanta avsikter och entiteter och sedan skapa dina program-planer i Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: DeniseMak
manager: hsalama
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2017
ms.author: v-geberr
ms.openlocfilehash: 66036da19b0c7f49935b32272e963b3766497605
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37438180"
---
# <a name="plan-your-luis-app"></a>Planera din LUIS-app

Det är viktigt att planera din app innan du börjar skapa i LUIS. Förbered en disposition eller schemat för möjliga avsikter och entiteter som är relevanta för domänspecifika avsnittet om ditt program.  

## <a name="identify-your-domain"></a>Identifiera din domän
En LUIS-app är uppbyggd kring ett domänspecifika ämne.  Du kanske till exempel en reseapp som utför bokning av biljetter, flyg, hotell och uthyrning bilar. En annan app kan tillhandahålla innehåll som rör utöva, spåra arbete lämplighet och ange mål. 

> [!TIP]
> LUIS erbjuder [fördefinierade domäner](luis-how-to-use-prebuilt-domains.md) för många vanliga scenarier.
> Kontrollera om du kan använda en fördefinierade domän som en startpunkt för din app.

## <a name="identify-your-intents"></a>Identifiera dina avsikter
Tänk på hur den [avsikter](luis-concept-intent.md) som är viktiga för ditt programs uppgift. Låt oss ta exempel på en reseapp med funktioner för att boka en flygning och kontrollera vädret vid användarens mål. Du kan definiera avsikterna ”BookFlight” och ”GetWeather” för dessa åtgärder. Du har flera avsikter i mer komplexa appar med fler funktioner, och du bör definiera dem noggrant så att den inte är specifika för. Till exempel ”BookFlight” och ”BookHotel” kan behöva vara separata avsikter men ”BookInternationalFlight” och ”BookDomesticFlight” kan vara för liknande.

> [!NOTE]
> Det är en bra idé att bara använda så många avsikter som behövs för att fungera som din app. Om du definierar för många avsikter, blir det svårare för LUIS för att klassificera yttranden korrekt. Om du definierar för några, de kan vara så allmänt att vara överlappande.


## <a name="identify-your-entities"></a>Identifiera dina entiteter
För att boka en flygning behöver viss information som mål, datum, flygbolag, biljett kategori och reser klass. Du kan lägga till dessa som [entiteter](luis-concept-entity-types.md) eftersom de är viktiga för att utföra en avsikt. 

När du bestämmer vilka entiteter som ska använda i din app, Kom ihåg att det finns olika typer av enheter för att samla in relationer mellan olika typer av objekt. [Entiteter i LUIS](luis-concept-entity-types.md) innehåller mer information om de olika typerna.

### <a name="simple-entity"></a>Enkel entitet
En enkel enhet beskriver ett enda koncept.

![enkel enhet](./media/luis-plan-your-app/simple-entity.png)

Se [Dataextrahering](luis-concept-data-extraction.md#simple-entity-data) mer information om hur du extraherar den enkla enheten från slutpunkten JSON frågesvaret. Testa enkel enhet [snabbstarten](luis-quickstart-primary-and-secondary-data.md) mer information om hur du använder en enkel enhet.

### <a name="hierarchical-entity"></a>Hierarkisk entitet
En hierarkisk entitet är en särskild typ av en **enkel** entitet, definiera en kategori och dess medlemmar i form av överordnad-underordnad-relation.

![hierarkisk entitet](./media/luis-plan-your-app/hierarchical-entity.png)

Se [Dataextrahering](luis-concept-data-extraction.md#hierarchical-entity-data) mer information om hur du extraherar den hierarkiska entiteten från slutpunkten JSON frågesvaret. Försök hierarkisk entiteten [snabbstarten](luis-quickstart-intent-and-hier-entity.md) mer information om hur du använder en hierarkisk entitet.

### <a name="composite-entity"></a>Sammansatt entitet
En sammansatt entitet består av andra entiteter som utgör delar av en helhet. 

![sammansatt entitet](./media/luis-plan-your-app/composite-entity.png)

Se [Dataextrahering](luis-concept-data-extraction.md#composite-entity-data) mer information om hur du extraherar den sammansatta entiteten från slutpunkten JSON frågesvaret. Försök sammansatta entiteten [självstudien](luis-tutorial-composite-entity.md) mer information om hur du använder en sammansatt entitet.

### <a name="prebuilt-entity"></a>Fördefinierade entitet
LUIS ger [förskapade entiteter](luis-prebuilt-entities.md) för vanliga typer som `Number`, som du kan använda för antal ärenden i en biljett ordning.

![Antal fördefinierade entitet](./media/luis-plan-your-app/number-entity.png)

Se [Dataextrahering](luis-concept-data-extraction.md#prebuilt-entity-data) mer information om hur du extraherar reguljärt uttryck entiteter från slutpunkten JSON frågesvaret. 

### <a name="list-entity"></a>Lista entitet 
En lista över entitet är en explicit angiven lista med värden. Varje värde består av en eller flera synonymer. Du kan välja att skapa en lista över entitet som representerar en flygplats namnen i en reseapp.

![lista entitet](./media/luis-plan-your-app/list-entity.png)

Se [Dataextrahering](luis-concept-data-extraction.md#list-entity-data) mer information om extrahering av listan över entiteter från slutpunkten JSON frågesvaret. Prova den [snabbstarten](luis-quickstart-intent-and-list-entity.md) mer information om hur du använder en entitet i listan.

### <a name="regular-expression-entity"></a>Reguljärt uttryck entitet
En entitet i reguljära uttryck kan LUIS för att extrahera data från ett uttryck baserat på ett regex-uttryck.

![Reguljärt uttryck entitet](./media/luis-plan-your-app/regex-entity.png)

Se [Dataextrahering](luis-concept-data-extraction.md#regular-expression-entity-data) mer information om hur du extraherar reguljärt uttryck entiteter från slutpunkten JSON frågesvaret. Prova den [snabbstarten](luis-quickstart-intents-regex-entity.md) mer information om hur du använder en entitet för reguljärt uttryck.

## <a name="after-getting-endpoint-utterances"></a>När slutpunkten yttranden
När din app hämtar endpoint yttranden, planerar att implementera förutsägelse förbättringar med [aktiv inlärning](label-suggested-utterances.md), [fras listor](luis-concept-feature.md), och [mönster](luis-concept-patterns.md). 

### <a name="patternany-entity"></a>Pattern.any entitet
Patterns.any är en platshållare för variabel längd som används endast i en [mönstret](luis-concept-patterns.md) mall-uttryck för att markera där entiteten börjar och slutar. Mallen yttranden överensstämmer med [korrekt syntax](luis-concept-patterns.md#pattern-syntax) att identifiera entiteter och ignorable text.


## <a name="next-steps"></a>Nästa steg
* Se [skapa din första app Language Understanding Intelligent Service (LUIS)](luis-get-started-create-app.md) för en snabb genomgång av hur du skapar en LUIS-app.