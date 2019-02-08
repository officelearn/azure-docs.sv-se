---
title: Parsning av valkrets - API för Lingvistisk analys
titlesuffix: Azure Cognitive Services
description: Läs mer om hur beståndsdelar, även kallat ”fras struktur parsning” identifierar fraser i texten.
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 03/21/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 7611f5f16111b5d8b0d2d293750f658125e50837
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878222"
---
# <a name="constituency-parsing"></a>Parsning av valkrets

> [!IMPORTANT]
> Förhandsversionen av Språkanalys upphörde den 9 augusti 2018. Vi rekommenderar att du använder [Azure Machine Learning-textanalysmoduler](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) för textbearbetning och -analys.

Målet med beståndsdelar (även kallat ”frasen struktur parsning”) är att identifiera fraser i texten.
Detta kan vara användbart vid extrahering av information från text.
Kunder kanske vill att hitta funktionsnamn eller nyckelfraser från en stor mängd text och för att se modifierare och åtgärder som omger varje sådan fras.

## <a name="phrases"></a>Fraser

Att lingvisten, en *frasen* är mer än bara en sekvens av ord.
Om du vill att en fras, måste en grupp med ord kommer för att spela upp en viss roll i meningen.
Gruppen av ord som kan flyttas tillsammans eller ersatts som helhet och meningen ska vara fluent och grammatik.

Överväg att meningen

> Jag vill hitta en ny hybrid bil med Bluetooth.

Den här meningen innehåller frasen substantiv: ”en ny hybrid bil med Bluetooth”.
Hur vet vi att det här är en mening?
Vi kan skriva om meningen (något poetically) genom att flytta den hel frasen framför:

> En ny hybrid bil med Bluetooth som jag vill hitta.

Eller vi kan ersätta den frasen med en mening med liknande funktion och betydelse, exempelvis ”en avancerad bil”:

> Jag vill hitta en avancerad ny bil.

Om vi valt i stället olika delmängd av ord, skulle uppgifterna ersättning leda till onormalt eller oläsliga meningar.
Tänk på vad som händer när vi flytta ”hitta en ny” framför:

> Hitta en ny jag vill hybrid bil med Bluetooth.

Resultatet är mycket svårt att läsa och förstå.

Målet med en parser är att hitta alla fraser.
Därför, på naturligt språk brukar fraserna vara kapslad i varandra.
En naturlig representation av dessa fraser är ett träd, till exempel följande:

![Träd](./Images/tree.png)

I den här trädstrukturen är grenarna markerats ”NP” substantivfraser.
Det finns flera fraser: *Jag*, *en ny bil hybrid*, *Bluetooth*, och *en ny hybrid bil med Bluetooth*.

## <a name="phrase-types"></a>Fras typer

| Etikett | Beskrivning | Exempel |
|-------|-------------|---------|
|ADJP   | Adjective frasen | ”så oartigt” |
|ADVP   | Adverb frasen | ”Rensa via” |
|CONJP  | Tillsammans frasen | ”samt” |
|FRAG   | Fragment som används för ofullständiga eller fragmentary indata | ”Rekommenderas starkt...” |
|INTJ   | interjection | ”Hurra” |
|LST    | Lista markör, inklusive skiljetecken | "#4)" |
|NAC    | Inte en konstituerande, används för att ange omfång för en icke-komponent fras |  ”och för en hel” i ”du allt och en bra hantera” |
|NP | Substantiv frasen | ”en lång ljus pannkaka” |
|NX | Används i vissa komplexa NPs för att markera i sidhuvudet| |
|PP | Prepositioner frasen| ”i poolen” |
|PRN    | Parentetiska| ”(så kallade)” |
|PRT    | Particle| ”ut” i ”rippade ut” |
|QP | Kvantitet frasen (d.v.s. komplexa mått/belopp) inom ett substantiv frasen| ”runt $75” |
|RRC    | Minskad relativt-sats.| ”fortfarande olöst” i ”många problem fortfarande olöst” |
|S  | Meningen eller -satsen. | ”Det är en mening”.
|SBAR   | Underordnad-satsen, ofta introducerats av en subordinating tillsammans | ”som jag vänster” i ”jag tittade som jag har lämnat”.|
|SBARQ  | Direkt fråga som introducerades av ett n-ord eller -uttryck | ”Vad var punkten”? |
|SINV   | Vägar i inverterad deklarativa mening | ”Vid något tillfälle har de medveten”. (Observera hur normal ämnet ”de” har flyttats till när verbet ”var”) |
|SQ | Inverterad Ja/Nej fråga eller main-satsen för en n-fråga | ”De fick bilen”? |
|UCP    | Till skillnad från samordnad frasen| ”små och med buggar” (Observera hur ett adjektiv och en fras preposition conjoined med ”och”)|
|VP | Verb frasen | ”råkade ut för skogen” |
|WHADJP | Vad adjektiv frasen | ”hur obekväm” |
|WHADVP | Vad adverb frasen| "when" |
|WHNP   | N-substantiv frasen| ”vilka ljus”, ”hur mycket från början”|
|WHPP   | Vad prepositional frasen| ”i vilket land”|
|X  | Okänd, osäker eller unbracketable.| första ”den” i ”den... det från början” |


## <a name="specification"></a>Specifikationen

Träd här använda S-uttryck från den [Penn Treebank](https://catalog.ldc.upenn.edu/LDC99T42).
