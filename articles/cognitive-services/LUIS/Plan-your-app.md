---
title: Planera din THOMAS program | Microsoft Docs
description: Beskriver relevanta appen avsikter och entiteter och skapa planer för programmet i språk förstå Intelligent tjänster (THOMAS).
services: cognitive-services
author: DeniseMak
manager: hsalama
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2017
ms.author: v-geberr
ms.openlocfilehash: 7aec5d5b90ac7145ce9f337ec74c590b4b88c6b1
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266370"
---
# <a name="plan-your-luis-app"></a>Planera din THOMAS app

Det är viktigt att planera din app innan du börjar skapa i THOMAS. Förbered en disposition eller schemat för möjliga avsikter och enheter som är relevant för avsnittet domänspecifika för programmet.  

## <a name="identify-your-domain"></a>Identifiera din domän
En THOMAS app är uppbyggd kring ett domänspecifika ämne.  Du kan till exempel ha en resa app som utför boka biljetter, flygplan, hotell och uthyrning bilar. En annan app kan ge innehåll som rör utöva, spårning lämplighet arbete och ställa in mål. 

> [!TIP]
> THOMAS erbjuder [färdiga domäner](luis-how-to-use-prebuilt-domains.md) för många vanliga scenarier.
> Kontrollera om du kan använda en fördefinierad domän som en startpunkt för din app.

## <a name="identify-your-intents"></a>Identifiera dina avsikter
Tänk på hur den [intents](luis-concept-intent.md) som är viktiga för ditt program aktivitet. Låt oss ta exempel på en app på resor, med funktioner för att boka en svarta och kontrollera väder på användarens mål. Du kan definiera ”BookFlight” och ”GetWeather” avsikter för dessa åtgärder. Du har flera avsikter i en mer komplicerad app med flera funktioner och du definiera dem noga så att den inte är specifika för. Till exempel ”BookFlight” och ”BookHotel” kan behöva separata avsikter men ”BookInternationalFlight” och ”BookDomesticFlight” kan vara för liknande.

> [!NOTE]
> Det är bäst att bara använda så många avsikter som behövs för att fungera som din app. Om du definierar för många avsikter, blir det svårare för THOMAS att klassificera utterances korrekt. Om du definierar för några, de kan vara allmän så att de vara överlappande.


## <a name="identify-your-entities"></a>Identifiera dina enheter
För att boka en svarta måste viss information som mål, datum, flygbolag, biljett kategori och reser klass. Du kan lägga till dem som [entiteter](luis-concept-entity-types.md) eftersom de är viktiga för att köra syftet. 

När du bestämmer vilka enheter som ska användas i din app, Kom ihåg att det finns olika typer av enheter för att samla in relationer mellan olika typer av objekt. [Entiteter i THOMAS](luis-concept-entity-types.md) ger mer detaljerad information om olika typer.

### <a name="simple-entity"></a>Enkel enhet
En enkel enhet beskriver ett enda koncept.

![enkel enhet](./media/luis-plan-your-app/simple-entity.png)

Se [Data extrahering](luis-concept-data-extraction.md#simple-entity-data) vill veta mer om hur du extraherar den enkla enheten från slutpunkten JSON frågesvar. Försök enkel enhet [quickstart](luis-quickstart-primary-and-secondary-data.md) vill veta mer om hur du använder en enkel enhet.

### <a name="hierarchical-entity"></a>Hierarkisk entitet
En hierarkisk entitet är en särskild typ av en **enkel** entitet; definierar en kategori och dess medlemmar i form av överordnade-underordnade relationen.

![hierarkisk entitet](./media/luis-plan-your-app/hierarchical-entity.png)

Se [Data extrahering](luis-concept-data-extraction.md#hierarchical-entity-data) lära dig mer om att extrahera hierarkiska entiteten från slutpunkten JSON-svar för frågan. Försök hierarkiska entiteten [quickstart](luis-quickstart-intent-and-hier-entity.md) vill veta mer om hur du använder en hierarkisk entitet.

### <a name="composite-entity"></a>Sammansatt entitet
En sammansatt entitet består av andra entiteter som bildar delar av en helhet. 

![sammansatt entitet](./media/luis-plan-your-app/composite-entity.png)

Se [Data extrahering](luis-concept-data-extraction.md#composite-entity-data) lära dig mer om att extrahera sammansatt entitet från slutpunkten JSON-svar för frågan. Försök sammansatta entiteten [kursen](luis-tutorial-composite-entity.md) vill veta mer om hur du använder en sammansatt entitet.

### <a name="prebuilt-entity"></a>Fördefinierade entitet
THOMAS ger [färdiga entiteter](Pre-builtEntities.md) för vanliga typer som `Number`, som du kan använda för antal biljetter i en biljett ordning.

![Antalet färdiga entitet](./media/luis-plan-your-app/number-entity.png)

Se [Data extrahering](luis-concept-data-extraction.md#prebuilt-entity-data) lära dig mer om att extrahera reguljärt uttryck entiteter från slutpunkten JSON-svar för frågan. 

### <a name="list-entity"></a>Lista entitet 
En entitet i listan är en explicit angiven lista med värden. Varje värde består av en eller flera synonymer. Du kan välja att skapa en lista över entitet som representerar en flygplats namn i en resa-app.

![lista entitet](./media/luis-plan-your-app/list-entity.png)

Se [Data extrahering](luis-concept-data-extraction.md#list-entity-data) lära dig mer om att extrahera listan entiteter från slutpunkten JSON-svar för frågan. Försök i [quickstart](luis-quickstart-intent-and-list-entity.md) vill veta mer om hur du använder en enhet i listan.

### <a name="regular-expression-entity"></a>Reguljärt uttryck entitet
En entitet för reguljära uttryck kan THOMAS att extrahera data från en utterance baserat på en regex-uttrycket.

![Reguljärt uttryck entitet](./media/luis-plan-your-app/regex-entity.png)

Se [Data extrahering](luis-concept-data-extraction.md#regular-expression-entity-data) lära dig mer om att extrahera reguljärt uttryck entiteter från slutpunkten JSON-svar för frågan. Försök i [quickstart](luis-quickstart-intents-regex-entity.md) vill veta mer om hur du använder ett reguljärt uttryck för entiteten.

## <a name="after-getting-endpoint-utterances"></a>När slutpunkten utterances
När din app hämtar endpoint utterances, planerar att implementera förutsägelse förbättringar i [active learning](label-suggested-utterances.md), [fras listor](luis-concept-feature.md), och [mönster](luis-concept-patterns.md). 

### <a name="patternany-entity"></a>Pattern.any entitet
Patterns.any är en platshållare för variabel längd som används i en [mönstret](luis-concept-patterns.md) mallen utterance Markera där entiteten börjar och slutar. Mallen utterances motsvara [korrekt syntax](luis-concept-patterns.md#pattern-syntax) att identifiera enheter och kan ignoreras text.


## <a name="next-steps"></a>Nästa steg
* Se [skapa din första app språk förstå Intelligent tjänster (THOMAS)] [ luis-get-started-create-app] för en snabb genomgång av hur du skapar en THOMAS.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app