---
title: Planera din app-LUIS
description: Disponera relevanta appar och entiteter och skapa sedan dina program planer i Language Understanding intelligenta tjänster (LUIS).
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: dfed27a05973a2ea2e9a97eaa1c233b847b33d87
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81382296"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>Planera ditt LUIS-AppData med ämnes domän och data extrahering

Ett LUIS app-schema innehåller [intentor](luis-glossary.md#intent) och [entiteter](luis-glossary.md#entity) som är relevanta för din ämnes [domän](luis-glossary.md#domain). Avsikten klassificerar användarens [yttranden](luis-glossary.md#utterance)och enheterna extraherar data från användaren yttranden.

## <a name="identify-your-domain"></a>Identifiera din domän

En LUIS-app är centrerad runt en ämnes domän. Du kan till exempel ha en rese-app som hanterar bokning av biljetter, flygningar, hotell och hyr bilar. En annan app kan ge innehåll som handlar om hur man utövar, spårar lämplighet och ställer in mål. Att identifiera domänen hjälper dig att hitta ord eller fraser som är relevanta för din domän.

> [!TIP]
> LUIS erbjuder [färdiga domäner](luis-how-to-use-prebuilt-domains.md) för många vanliga scenarier. Kontrol lera om du kan använda en fördefinierad domän som start punkt för din app.

## <a name="identify-your-intents"></a>Identifiera dina avsikter

Tänk på vilka [avsikter](luis-concept-intent.md) som är viktiga för programmets uppgift.

Låt oss ta exemplet på en rese app, med funktioner för att boka en flygning och kontrol lera väder på användarens mål. Du kan definiera `BookFlight` och `GetWeather` avsikten för dessa åtgärder.

I en mer komplex app med fler funktioner har du fler syften och du bör definiera dem noggrant så att avsikterna inte är för speciella. Till exempel `BookFlight` och `BookHotel` kan behöva vara separata avsikter, men `BookInternationalFlight` det `BookDomesticFlight` kan vara för likt.

> [!NOTE]
> Vi rekommenderar att du bara använder så många syften som du behöver för att utföra funktionerna i din app. Om du definierar för många avsikter blir det svårare för LUIS att klassificera yttranden korrekt. Om du definierar för få kan de vara så generella att de överlappar varandra.

Om du inte behöver identifiera övergripande användar avsikt lägger du till alla exempel på användarens yttranden till `None` avsikten. Om din app växer för att kräva fler avsikter kan du skapa dem senare.

## <a name="create-example-utterances-for-each-intent"></a>Skapa exempel-yttranden för varje avsikt

Undvik att skapa för många yttranden för varje avsikt att börja med. När du har fastställt avsikterna skapar du 15 till 30 exempel yttranden per avsikt. Varje uttryck bör skilja sig från den tidigare angivna yttranden. En bra variation i yttranden omfattar övergripande antal ord, Word-val, verb och [interpunktion](luis-reference-application-settings.md#punctuation-normalization).

Mer information finns i [förstå god yttranden för Luis-appar](luis-concept-utterance.md).

## <a name="identify-your-entities"></a>Identifiera dina entiteter

I exemplet yttranden identifierar du de entiteter som du vill extrahera. Om du vill boka en flygning behöver du information som mål, datum, flyg, biljett kategori och rese klass. Skapa entiteter för dessa data typer och markera sedan [entiteterna](luis-concept-entity-types.md) i exemplet yttranden. Entiteter är viktiga för att kunna utföra en avsikt.

När du bestämmer vilka entiteter som ska användas i appen, Tänk på att det finns olika typer av entiteter för att fånga relationer mellan objekt typer. [Entiteter i Luis](luis-concept-entity-types.md) ger mer information om de olika typerna.

> [!TIP]
> LUIS erbjuder [färdiga entiteter](luis-prebuilt-entities.md) för vanliga scenarier med konversations användare. Överväg att använda färdiga entiteter som utgångs punkt för program utvecklingen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär LUIS Development lifecylce](luis-concept-app-iteration.md)

