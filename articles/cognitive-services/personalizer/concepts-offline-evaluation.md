---
title: Använd metoden Offline evaluation - Personalizer
titleSuffix: Azure Cognitive Services
description: I den här artikeln beskrivs hur du använder offlineutvärdering för att mäta appens effektivitet och analysera din utbildningsloop.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: diberry
ms.openlocfilehash: f8ceef5e80bf15f0ba52a9c289e617018febfb5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623594"
---
# <a name="offline-evaluation"></a>Offlineutvärdering

Offlineutvärdering är en metod som gör att du kan testa och bedöma personalizer-tjänstens effektivitet utan att ändra koden eller påverka användarupplevelsen. Offlineutvärdering använder tidigare data, som skickas från ditt program till rank- och belönings-API:erna, för att jämföra hur olika rang har presterat.

Offlineutvärdering utförs på ett datumintervall. Intervallet kan avslutas så sent som den aktuella tiden. Början av intervallet kan inte vara mer än det antal dagar som angetts för [datalagring](how-to-settings.md).

Offlineutvärdering kan hjälpa dig att svara på följande frågor:

* Hur effektiva är Personalizer leden för framgångsrik personalisering?
    * Vilka är de genomsnittliga belöningar som uppnås av Personalizer online machine learning policy?
    * Hur jämför Personalizer med effektiviteten i vad programmet skulle ha gjort som standard?
    * Vad skulle ha varit den jämförande effektiviteten av ett slumpmässigt val för personalisering?
    * Vad skulle ha varit den jämförande effektiviteten hos olika utbildningsstrategier som anges manuellt?
* Vilka funktioner i sammanhanget bidrar mer eller mindre till framgångsrik personalisering?
* Vilka funktioner i åtgärderna bidrar mer eller mindre till framgångsrik personalisering?

Dessutom kan offlineutvärdering användas för att upptäcka mer optimerade inlärningsprinciper som Personalizer kan använda för att förbättra resultaten i framtiden.

Offlineutvärderingar ger inte vägledning om hur stor procentandel av händelser som ska användas för utforskning.

## <a name="prerequisites-for-offline-evaluation"></a>Förutsättningar för offlineutvärdering

Följande är viktiga överväganden för den representativa offlineutvärderingen:

* Har tillräckligt med data. Det rekommenderade minimumet är minst 50 000 händelser.
* Samla in data från perioder med representativt användarbeteende och trafik.

## <a name="discovering-the-optimized-learning-policy"></a>Identifiera den optimerade inlärningsprincipen

Personalizer kan använda offlineutvärderingsprocessen för att automatiskt identifiera en mer optimal inlärningspolicy.

När du har utfört offlineutvärderingen kan du se den jämförande effektiviteten hos Personalizer med den nya principen jämfört med den aktuella onlinepolicyn. Du kan sedan tillämpa den inlärningspolicyn för att göra den effektiv omedelbart i Personalizer, genom att ladda ner den och ladda upp den i panelen Modeller och policy. Du kan också ladda ner den för framtida analys eller användning.

Nuvarande politik som ingår i utvärderingen:

| Inställningar för utbildning | Syfte|
|--|--|
|**Online-policy**| Den nuvarande utbildningspolitiken som används i Personalizer |
|**Baslinje**|Programmets standard (som bestäms av den första åtgärden som skickades i Rank-anrop)|
|**Slumpmässig policy**|Ett imaginärt Rank-beteende som alltid returnerar slumpmässigt val av åtgärder från de medföljande.|
|**Anpassade principer**|Ytterligare utbildningsprinciper som har överförts när utvärderingen påbörjas.|
|**Optimerad princip**|Om utvärderingen startades med möjligheten att upptäcka en optimerad policy, kommer det också att jämföras, och du kommer att kunna ladda ner den eller göra det till online learning policy, som ersätter den nuvarande.|

## <a name="understanding-the-relevance-of-offline-evaluation-results"></a>Förstå relevansen av offlineutvärderingsresultat

När du kör en offlineutvärdering är det mycket viktigt att analysera _konfidens bounds_ av resultaten. Om de är breda betyder det att din ansökan inte har fått tillräckligt med data för att belöningsuppskattningarna ska vara exakta eller signifikanta. När systemet ackumulerar mer data och du kör offlineutvärderingar under längre perioder blir konfidensintervallen smalare.

## <a name="how-offline-evaluations-are-done"></a>Så här görs offlineutvärderingar

Offlineutvärderingar görs med hjälp av en metod som kallas **kontrafaktisk utvärdering**.

Personalizer bygger på antagandet att användarnas beteende (och därmed belöningar) är omöjliga att förutsäga retroaktivt (Personalizer kan inte veta vad som skulle ha hänt om användaren hade visat något annat än vad de såg), och bara att lära av uppmätta belöningar.

Detta är den konceptuella process som används för utvärderingar:

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

Offlineutvärderingen använder endast observerat användarbeteende. Den här processen ignorerar stora mängder data, särskilt om ditt program gör Rank-anrop med ett stort antal åtgärder.


## <a name="evaluation-of-features"></a>Utvärdering av funktioner

Offlineutvärderingar kan ge information om hur mycket av de specifika funktionerna för åtgärder eller sammanhang som väger för högre belöningar. Informationen beräknas med hjälp av utvärderingen mot den angivna tidsperioden och data och kan variera med tiden.

Vi rekommenderar att du tittar på funktionsutvärderingar och frågar:

* Vilka andra, ytterligare, funktioner kan ditt program eller system ge i linje med dem som är mer effektiva?
* Vilka funktioner kan tas bort på grund av låg effektivitet? Funktioner med låg effektivitet ger _ljud_ i maskininlärningen.
* Finns det några funktioner som av misstag ingår? Exempel på dessa är: användaridentifierbar information, dubbla ID: er, etc.
* Finns det några oönskade funktioner som inte bör användas för att anpassa på grund av regulatoriska eller ansvarsfulla användningsöverväganden? Finns det funktioner som kan proxy (det vill säga nära spegel eller korrelera med) oönskade funktioner?


## <a name="next-steps"></a>Nästa steg

[Konfigurera Personalizer](how-to-settings.md)
[Run Offline Utvärderingar](how-to-offline-evaluation.md) Förstå hur [Personalizer Fungerar](how-personalizer-works.md)
