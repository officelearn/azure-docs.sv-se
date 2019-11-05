---
title: Planera din app-LUIS
titleSuffix: Azure Cognitive Services
description: Disponera relevanta appar och entiteter och skapa sedan dina program planer i Language Understanding intelligenta tjänster (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: b5e5df111b81cb60b6d194be190421bdb5ce2683
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467710"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>Planera ditt LUIS-AppData med ämnes domän och data extrahering

Ett LUIS app-schema innehåller intentor och entiteter som är relevanta för din ämnes domän. Avsikten klassificerar användarens yttranden och enheterna extraherar data från användaren yttranden. 

## <a name="identify-your-domain"></a>Identifiera din domän

En LUIS-app är centrerad runt ett domänbaserat ämne.  Du kan till exempel ha en rese-app som utför bokning av biljetter, flygningar, hotell och hyr bilar. En annan app kan ge innehåll som handlar om hur man utövar, spårar lämplighet och ställer in mål. Att identifiera domänen hjälper dig att hitta ord eller fraser som är viktiga för din domän.

> [!TIP]
> LUIS erbjuder [färdiga domäner](luis-how-to-use-prebuilt-domains.md) för många vanliga scenarier.
> Kontrol lera om du kan använda en fördefinierad domän som start punkt för din app.

## <a name="identify-your-intents"></a>Identifiera dina avsikter

Tänk på vilka [avsikter](luis-concept-intent.md) som är viktiga för programmets uppgift. 

Låt oss ta exemplet på en rese app, med funktioner för att boka en flygning och kontrol lera väder på användarens mål. Du kan definiera `BookFlight` och `GetWeather` avsikter för dessa åtgärder. 

I en mer komplex app med fler funktioner har du fler syften och du bör definiera dem noggrant så att avsikterna inte är för speciella. Till exempel kan `BookFlight` och `BookHotel` behöva vara separata avsikter, men `BookInternationalFlight` och `BookDomesticFlight` kan vara för likartade.

> [!NOTE]
> Vi rekommenderar att du bara använder så många syften som du behöver för att utföra funktionerna i din app. Om du definierar för många avsikter blir det svårare för LUIS att klassificera yttranden korrekt. Om du definierar för få kan de vara så generella att de överlappar varandra.

Om du inte behöver identifiera övergripande användar avsikt lägger du till alla exempel på användar yttranden till ingen avsikt. Om din app växer för att kräva fler avsikter kan du skapa dem senare. 

## <a name="create-example-utterances-for-each-intent"></a>Skapa exempel-yttranden för varje avsikt

När du har fastställt avsikterna skapar du 15 till 30 exempel yttranden för varje avsikt. För att börja med har du inte färre än det här antalet eller så skapar du för många yttranden för varje avsikt. Varje uttryck bör skilja sig från föregående uttryck. En bra variation i yttranden innehåller övergripande antal ord, Word-val, verb-och skiljetecken. 

Granska [yttranden](luis-concept-utterance.md) för mer information.

## <a name="identify-your-entities"></a>Identifiera dina entiteter

I exemplet yttranden identifierar du de entiteter som du vill extrahera. Om du vill boka en flygning behöver du information som mål, datum, flyg, biljett kategori och rese klass. Skapa entiteter för dessa data typer och markera sedan [entiteterna](luis-concept-entity-types.md) i exemplet yttranden eftersom de är viktiga för att utföra ett avsikts sätt. 

När du bestämmer vilka entiteter som ska användas i appen bör du tänka på att det finns olika typer av entiteter för att fånga relationer mellan olika typer av objekt. [Entiteter i Luis](luis-concept-entity-types.md) ger mer information om de olika typerna.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om den typiska [utvecklings cykeln](luis-concept-app-iteration.md).  