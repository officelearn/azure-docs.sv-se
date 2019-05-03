---
title: Offline-utvärdering
titleSuffix: Personalizer - Azure Cognitive Services
description: Lär dig hur du analyserar learning loopen med en offline-utvärdering
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: e99a8242e438ef5a8ab7fd917724450f8080bb41
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027063"
---
# <a name="how-to-analyze-your-learning-loop-with-an-offline-evaluation"></a>Hur du analyserar learning loopen med en offline-utvärdering


Lär dig hur du slutför en offline-utvärdering och förstå resultaten.

Offline utvärderingar kan du se hur effektiva Personalizer jämförs med standardbeteendet för ditt program, Lär dig vilka funktioner som bidrar mest till anpassning och identifiera nya machine learning inställningar automatiskt.

Läs mer om [Offline utvärderingar](concepts-offline-evaluation.md) vill veta mer.


## <a name="prerequisites"></a>Nödvändiga komponenter

1. Du måste ha en Personalizer loop som har konfigurerats
1. Personalizer loopen måste ha minst 50 000 händelser i dess loggar för meningsfulla utvärderingsresultat.

Du kan också du kan också tidigare har exporterat _learning princip_ filer du kan jämföra och testa i samma utvärderingen.

## <a name="steps-to-start-a-new-offline-evaluation"></a>Steg för att starta en ny Offline utvärdering

1. Leta upp din anpassning Loop-resurs i Azure-portalen.
1. Gå till avsnittet ”utvärdering”.
1. Klicka på ny utvärdering
1. Välj ett start- och datum för offline-utvärdering. Dessa är tidigare som anger intervallet för data som ska användas vid utvärderingen. Dessa data måste finnas i loggarna, som anges i den [Datakvarhållning](how-to-settings.md) inställningen.
1. Du kan också överföra learning principen. 
1. Ange om Personalizer ska skapa en princip för optimerad Learning bygger på användarbeteende som observerats i den här tidsperioden.
1. Starta utvärderingen

## <a name="results"></a>Resultat

Utvärderingar kan ta lång tid att köra, beroende på mängden data att bearbeta, antalet learning principer för att jämföra, och om en optimering har begärts.

När klar kan du se följande resultat:

1. Jämförelser av Learning principer, inklusive:
    * **Online-princip**: Den aktuella Learning principen som används i Personalizer
    * **Baslinjen**: Programmets standard (som bestäms av den första åtgärden som skickas som rangordnas anrop)
    * **Princip för slumpmässiga**: En tänkt rangordnas beteende som returnerar alltid slumpmässiga valet av åtgärder bland de angivna.
    * **Anpassade principer**: Ytterligare Learning principer har laddats upp vid start av utvärderingen.
    * **Optimerad princip**: Om utvärderingen startade med alternativet för att identifiera en optimerad princip, kommer också att jämföras och du kommer att kunna ladda ned det eller göra den utbildning online-princip, ersätter den aktuella.

1. Effektivitet [funktioner](concepts-features.md) för åtgärder och kontext.


## <a name="more-information"></a>Mer information

* Lär dig [hur offline utvärderingar fungerar](concepts-offline-evaluation.md).