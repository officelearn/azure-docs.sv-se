---
title: Planera din app-LUIS
titleSuffix: Azure Cognitive Services
description: Beskriver relevanta avsikter och entiteter och sedan skapa dina program-planer i Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: dc648b30dc1236080be06044f510557ae0ce9476
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638305"
---
# <a name="plan-your-luis-app-with-subject-domain-intents-and-entities"></a>Planera din LUIS-app med ämnes domän, avsikter och entiteter

För att planera din app, identifiera din ämnes områdes domän. Detta inkluderar möjliga avsikter och entiteter som är relevanta för ditt program.  

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

När du har fastställt avsikterna skapar du 15 till 30 exempel yttranden för varje avsikt. För att börja med har du inte färre än det här antalet eller så skapar du för många yttranden för varje avsikt. Varje uttryck ska skilja sig från föregående uttryck. En bra rad i talade innehåller övergripande ordräkning, word-val, verb Tempus och skiljetecken. 

Granska [yttranden](luis-concept-utterance.md) för mer information.

## <a name="identify-your-entities"></a>Identifiera dina entiteter

Identifiera de entiteter som du vill extrahera i exempel-yttranden. Om du vill boka en flygning behöver du information som mål, datum, flyg, biljett kategori och rese klass. Skapa entiteter för dessa data typer och markera sedan [entiteterna](luis-concept-entity-types.md) i exemplet yttranden eftersom de är viktiga för att utföra ett avsikts sätt. 

När du bestämmer vilka entiteter som ska använda i din app, Kom ihåg att det finns olika typer av enheter för att samla in relationer mellan olika typer av objekt. [Entiteter i LUIS](luis-concept-entity-types.md) innehåller mer information om de olika typerna.

## <a name="next-steps"></a>Nästa steg

När din app har tränats publicerats och hämtar endpoint yttranden, planerar att implementera förutsägelse förbättringar med [aktiv inlärning](luis-how-to-review-endpoint-utterances.md), [fras listor](luis-concept-feature.md), och [mönster](luis-concept-patterns.md). 


* Se [skapa din första app Language Understanding Intelligent Service (LUIS)](luis-get-started-create-app.md) för en snabb genomgång av hur du skapar en LUIS-app.
