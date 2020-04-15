---
title: Planera din app - LUIS
description: Beskriv relevanta appavsikter och entiteter och skapa sedan dina programplaner i Language Understanding Intelligent Services (LUIS).
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: dfed27a05973a2ea2e9a97eaa1c233b847b33d87
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382296"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>Planera luis-appschemat med ämnesdomän och dataextrahering

Ett LUIS-appschema innehåller [avsikter](luis-glossary.md#intent) och [entiteter](luis-glossary.md#entity) som är relevanta för [ämnesdomänen](luis-glossary.md#domain). Avsikterna klassificerar [användaryttranden](luis-glossary.md#utterance)och entiteterna extraherar data från användarens yttranden.

## <a name="identify-your-domain"></a>Identifiera din domän

En LUIS-app är centrerad kring en ämnesdomän. Du kan till exempel ha en reseapp som hanterar bokning av biljetter, flyg, hotell och hyrbilar. En annan app kan tillhandahålla innehåll som är relaterat till att utöva, spåra träningsinsatser och sätta upp mål. Genom att identifiera domänen kan du hitta ord eller fraser som är relevanta för din domän.

> [!TIP]
> LUIS erbjuder [fördefinierade domäner](luis-how-to-use-prebuilt-domains.md) för många vanliga scenarier. Kontrollera om du kan använda en fördefinierad domän som utgångspunkt för din app.

## <a name="identify-your-intents"></a>Identifiera dina avsikter

Tänk på de [avsikter](luis-concept-intent.md) som är viktiga för programmets uppgift.

Låt oss ta exemplet med en reseapp, med funktioner för att boka ett flyg och kontrollera vädret på användarens destination. Du kan `BookFlight` definiera `GetWeather` och avsikter för dessa åtgärder.

I en mer komplex app med fler funktioner har du fler avsikter och du bör definiera dem noggrant så att avsikterna inte är för specifika. Till `BookFlight` exempel, `BookHotel` och kan behöva vara `BookInternationalFlight` separata `BookDomesticFlight` avsikter, men och kan vara för lika.

> [!NOTE]
> Det är bäst att bara använda så många avsikter som du behöver för att utföra funktionerna i din app. Om du definierar för många avsikter blir det svårare för LUIS att klassificera yttranden korrekt. Om du definierar för få kan de vara så allmänna att de överlappar varandra.

Om du inte behöver identifiera den övergripande användaravsikten lägger `None` du till alla exempelanvändaryttranden i avsikten. Om appen växer till att behöva ha fler avsikter kan du skapa dem senare.

## <a name="create-example-utterances-for-each-intent"></a>Skapa exempelyttranden för varje avsikt

Börja med att undvika att skapa för många yttranden för varje avsikt. När du har bestämt avsikter, skapa 15 till 30 exempel yttranden per avsikt. Varje uttryck bör skilja sig från de tidigare angivna yttrandena. En bra variation i yttranden inkluderar övergripande ordräkning, ordval, verb tempus och [interpunktion](luis-reference-application-settings.md#punctuation-normalization).

Mer information finns i [förstå bra yttranden för LUIS-appar](luis-concept-utterance.md).

## <a name="identify-your-entities"></a>Identifiera dina entiteter

Identifiera de entiteter som du vill extrahera i exempelyttrandena. För att boka ett flyg behöver du information som destination, datum, flygbolag, biljettkategori och reseklass. Skapa entiteter för dessa datatyper och markera sedan [entiteterna](luis-concept-entity-types.md) i exempelyttrandena. Entiteter är viktiga för att uppnå en avsikt.

När du bestämmer vilka entiteter som ska användas i din app bör du tänka på att det finns olika typer av entiteter för att fånga relationer mellan objekttyper. [Entiteter i LUIS](luis-concept-entity-types.md) innehåller mer information om de olika typerna.

> [!TIP]
> LUIS erbjuder [fördefinierade entiteter](luis-prebuilt-entities.md) för vanliga konversationsanvändarscenarier. Överväg att använda fördefinierade entiteter som utgångspunkt för programutvecklingen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Att lära sig LUIS utveckling lifecylce](luis-concept-app-iteration.md)

