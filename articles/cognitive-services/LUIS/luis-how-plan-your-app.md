---
title: Planera din app
titleSuffix: Language Understanding - Azure Cognitive Services
description: Beskriver relevanta avsikter och entiteter och sedan skapa dina program-planer i Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: 9d54cff81f39f41b60800e9b33f3b4da1a735d85
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58893443"
---
# <a name="plan-your-luis-app-with-subject-domain-intents-and-entities"></a>Planera din LUIS-app med ämne domän, avsikter och entiteter

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

## <a name="next-steps"></a>Nästa steg

När din app har tränats publicerats och hämtar endpoint yttranden, planerar att implementera förutsägelse förbättringar med [aktiv inlärning](luis-how-to-review-endpoint-utterances.md), [fras listor](luis-concept-feature.md), och [mönster](luis-concept-patterns.md). 


* Se [skapa din första app Language Understanding Intelligent Service (LUIS)](luis-get-started-create-app.md) för en snabb genomgång av hur du skapar en LUIS-app.
