---
title: Offline utvärdering - Personalizer
titleSuffix: Azure Cognitive Services
description: Skapa feedback-loop i den här C# Snabbstart med Personalizer-tjänsten.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 29caea481b1999086440db2021b86d949ce6cbc6
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027238"
---
# <a name="offline-evaluation"></a>Offline-utvärdering

Offline-utvärderingen är en metod som gör att du kan testa och utvärdera effektiviteten i tjänsten Personalizer utan att ändra koden eller påverkar användarupplevelsen. Offline utvärdering använder data som skickas från ditt program för rangordning-API för att jämföra hur olika rangordningar har utfört tidigare.

Offline utvärderingen utförs på ett datumintervall. Intervallet kan slutföras så sent som den aktuella tiden. I början av intervallet får inte innehålla fler än antalet dagar som angetts för [datakvarhållning](how-to-settings.md).

Med hjälp av offline utvärdering kan du besvara följande frågor:

* Hur effektiva är Personalizer rangordningar för lyckade anpassnings?
    * Vilka är de genomsnittliga belöningarna uppnås genom att de Personalizer online machine learning-princip?
    * Hur Personalizer jämfört med effektiviteten i vad programmet skulle ha gjort som standard?
    * Vad skulle ha varit jämförande effektiviteten med en slumpmässig för anpassning?
    * Vad skulle ha varit jämförande effektiviteten i olika learning principer som har angetts manuellt?
* Vilka funktioner i kontexten bidrar mer eller mindre till lyckade anpassnings?
* Vilka funktioner i åtgärderna som mer eller mindre bidrar till lyckade anpassnings?

Dessutom kan Offline utvärdering användas för att identifiera mer optimerade learning principer som Personalizer kan använda för att förbättra resultatet i framtiden.

Offline utvärderingar innehåller vägledning om procentandelen av händelser som ska användas för utforskning.

## <a name="prerequisites-for-offline-evaluation"></a>Krav för offline-utvärdering

Följande är viktiga överväganden för den representativa offline utvärderingen:

* Ha tillräckligt med data. Rekommenderad minsta är minst 50 000 händelser.
* Samla in data från perioder med representativa användarbeteenden och trafik.

## <a name="discovering-the-optimized-learning-policy"></a>Identifiera optimerade learning-princip

Personalizer kan använda offline utvärderingsprocessen för att identifiera en mer optimala learning princip automatiskt.

Du kan se Personalizer jämförande effektivitet med den nya principen jämfört med den aktuella online-principen när du har utfört offline utvärderingen. Du kan sedan använda learning principen så att den blir gälla omedelbart Personalizer eller hämta den för framtida analys eller använda.

## <a name="understanding-the-relevance-of-offline-evaluation-results"></a>Förstå upplevelsen av offline utvärderingsresultat

När du kör en offline utvärdering, det är mycket viktigt att analysera _förtroende gränser_ resultat. Om de är breda, innebär det att ditt program inte har mottagit tillräckligt med data för trafik uppskattningarna vara exakt eller betydande. När systemet ackumulerar mer data och du kör offline utvärderingar under längre perioder, blir konfidensintervall smalare.

## <a name="how-offline-evaluations-are-done"></a>Hur offline utvärderingar är klar

Offline utvärderingar är klar med hjälp av en metod som kallas **Counterfactual utvärdering**. 

Personalizer bygger på antagandet att användarnas beteende (och därmed belöningar) är omöjligt att förutsäga efterhand (Personalizer kan inte vet vad som skulle ha hände om användaren hade visas något annorlunda än vad de såg), och endast för att lära sig från uppmätt belöningar. 

Detta är en konceptuell process som används för utvärderingar:

```
[For a given _learning policy), such as the online learning policy, uploaded learning policies, or optimized candidate policies]:
{
    Initialize a virtual instance of Personalizer with that policy and a blank model;

    [For every chronological event in the logs]
    {
        - Perform a Rank call
    
        - Compare the reward of the results against the logged user behavior.
            - If they match, train the model on the observed reward in the logs.
            - If they don't match, then what the user would have done is unknown, so the event is discarded and not used for training or measurement.
        
    }

    Add up the rewards and statistics that were predicted, do some aggregation to aid visualizations, and save the results.
}
```

Offline utvärderingen använder endast observerade användarbeteende. Den här processen ignorerar stora mängder data, särskilt om ditt program rangordnas anrop med stort antal åtgärder.


## <a name="evaluation-of-features"></a>Utvärdering av funktioner

Offline utvärderingar kan ge information om hur mycket av specifika funktioner för åtgärder eller kontext väger för högre belöningar. Informationen beräknas med utvärderingen mot den angivna tidsperioden och data och kan variera med tiden.

Vi rekommenderar att studera funktionen utvärderingar och ber:

* Vilka andra, extra funktioner kan ditt program eller system ange längs rader med de som är mer effektiva?
* Vilka funktioner kan tas bort på grund av låg effektivitet? Lägg till funktioner för låg effektivitet _bruset_ om machine learning.
* Finns det några funktioner som ingår på av misstag? Exempel på dessa är: personligt identifierbar information (PII), dubblett-ID: N och så vidare.
* Finns det några oönskade funktioner som inte bör användas för att anpassa på grund av regler eller ansvarig använda överväganden? Finns det funktioner som skulle kunna proxy (det vill säga nära spegling eller kombinera med) oönskade funktioner?


## <a name="next-steps"></a>Nästa steg

[Konfigurera Personalizer](how-to-settings.md)
