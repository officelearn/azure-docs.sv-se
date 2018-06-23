---
title: Område parsning i språkliga analys API | Microsoft Docs
description: Läs mer om hur område parsning, även kallat ”fras struktur parsning” identifierar fraser i texten.
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 03/21/2016
ms.author: lesun
ms.openlocfilehash: 1cd5ac3eceb9b36654f1b012bce482c5151c4462
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351753"
---
# <a name="constituency-parsing"></a>Område parsning

Målet med område parsning (även kallat ”frasen strukturen parsning”) är att identifiera fraser i texten.
Detta kan vara användbart när information från text.
Kunder kan vill hitta funktionsnamn eller viktiga fraser från en stor brödtext och se modifierare och åtgärder som omger varje sådan frasen.

## <a name="phrases"></a>Fraser

Att lingvisten, en *frasen* är mer än bara en sekvens med orden.
En grupp med ord måste komma samman för att spela upp en specifik roll i meningen för att vara en fras.
Gruppen ord. kan flyttas tillsammans eller ersättas hela och meningen vara löpande och grammatik.

Överväg att meningen

> Jag vill hitta en ny hybrid bil med Bluetooth.

Den här meningen innehåller substantiv fras: ”en ny hybrid bil med Bluetooth”.
Hur vet vi att det här är en fras?
Vi kan skriva om meningen (något poetically) genom att flytta den hela frasen framför:

> En ny hybrid bil med Bluetooth som du vill söka efter.

Eller vi kan ersätta den frasen med en fras med liknande funktion och betydelse, exempelvis ”en avancerad bil”:

> Jag vill hitta en avancerad bil.

Om vi plockats i stället annan delmängd av ord, skulle uppgifterna ersättning leda till konstigt eller oläsliga meningar.
Tänk vad som händer när det flytta ”hitta en ny” framför:

> Hitta en ny jag vill hybrid bil med Bluetooth.

Resultatet är mycket svårt att läsa och förstå.

Målet med en parser är att hitta alla fraser.
Interestingly, i naturligt språk brukar fraser finnas inuti ett annat.
En fysisk representation av dessa fraser är ett träd, till exempel följande:

![Träd](./Images/tree.png)

I den här trädstrukturen är filialer markerad ”NP” substantivfraser.
Det finns flera fraser: *jag*, *en ny hybrid bil*, *Bluetooth*, och *en ny hybrid bil med Bluetooth*.

## <a name="phrase-types"></a>Frasen typer

| Etikett | Beskrivning | Exempel |
|-------|-------------|---------|
|ADJP   | Adjective frasen | ”så oartigt” |
|ADVP   | Adverb fras | ”klar genom” |
|CONJP  | Tillsammans frasen | ”samt” |
|FRAGM   | Fragment, användas för ofullständiga eller fragmentary indata | ”Rekommenderas starkt...” |
|INTJ   | interjection | ”Vilken fröjdefull jul” |
|LISTA    | Lista markör, inklusive skiljetecken | ”#4)” |
|NAC    | Inte en ingående, används för att ange omfattningen för en icke-komponent fras |  ”och för en hel del” i ”du att allt och en bra hantera” |
|NP | Substantiv frasen | ”en lång tilldelats pannkaka” |
|NX | Används i vissa komplexa NPs för att markera huvudet| |
|PP | Prepositioner frasen| ”i poolen” |
|PRN    | Parentetiska| ”(så kallade)” |
|PRT    | Partikeln| ”out” i ”rippade ut” |
|QP | Antal frasen (d.v.s. komplexa mått/belopp) inom en substantiv fras| ”runt $75” |
|RRC    | Minskad relativt-sats.| ”fortfarande olöst” i ”många problem fortfarande olöst” |
|S  | Meningen eller -satsen. | ”Det här är en mening”.
|SBAR   | Underordnad-satsen, ofta som introducerats av subordinating tillsammans | ”som jag vänster” i ”jag tittat som jag har lämnat”.|
|SBARQ  | Direkt fråga som introducerades av vad ord eller -fraser | ”Vad har punkten”? |
|SINV   | Omvända deklarativ meningen | ”Vid något tillfälle var de medveten om”. (Observera hur normal ämne ”de” har flyttats till när verbet ”var”) |
|SQ | Inverterad Ja/Nej fråga eller main-satsen i en vad-fråga | ”De fick bilen”? |
|UCP    | Till skillnad från samordnade frasen| ”kort och med buggar” (Observera hur ett adjektiv och en fras preposition conjoined med ”och”)|
|VP | Verbet frasen | ”råkade ut för skogen” |
|WHADJP | Vad adjektiv frasen | ”hur obekväm” |
|WHADVP | Vad adverb fras| ”När” |
|WHNP   | Vad-substantiv frasen| ”som tilldelats”, ”hur mycket soppa”|
|WHPP   | Vad prepositional frasen| ”i vilket land”|
|X  | Okänd, osäkra eller unbracketable.| första ”i” i ”den... soppa” |


## <a name="specification"></a>Specifikationen

Träd här välja S-uttryck i [Sandberg Treebank](https://www.cis.upenn.edu/~treebank/).
