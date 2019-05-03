---
title: Förstärkande inlärning - Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizer använder information om åtgärder och aktuell kontext för att göra bättre rangordning förslag. Information om dessa åtgärder och kontext är attribut eller egenskaper som kallas funktioner.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 30f009f76c25d80281d748e1e484175380ca9743
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027175"
---
# <a name="what-is-reinforcement-learning"></a>Vad är förstärkande inlärning?


Förstärkande inlärning är en metod för maskininlärning som lär sig beteenden genom att hämta feedback från dess användning.
 
Förstärkande inlärning fungerar genom att:

* Att erbjuda en affärsmöjlighet eller frihetsgrader att införa ett beteende – till exempel beslut eller val.
* Ger detaljerad information om miljön och alternativ.
* Ge feedback om hur väl beteendet uppnår ett visst mål.

Det finns många underordnade typer och format för förstärkande inlärning, är detta hur konceptet fungerar i Personalizer:

* Programmet ger möjlighet att visa en del av innehållet från en lista med alternativ.
* Programmet innehåller information om varje alternativ och kontexten för användaren.
* Program-beräkningarna en _belöna poäng_.

Till skillnad från vissa metoder för förstärkande inlärning kräver Personalizer inte en simulering att arbeta i. Dess learning-algoritmer är utformade för att reagera på en omvärlden (jämfört med kontroll över den) och lär dig från varje datapunkt genom att förstå att det är en unik möjlighet som kostar tid och pengar för att skapa och att det finns en noll ångrar (förlust av möjliga utmärkelse) om icke-optimal prestanda sker.

## <a name="what-type-of-reinforcement-learning-algorithms-does-personalizer-use"></a>Vilken typ av förstärkt algoritmer använder Personalizer?

Den aktuella versionen av Personalizer använder **sammanhangsberoende bandits**, en metod för att förstärkande inlärning dvs framed runt gör beslut eller val mellan diskreta åtgärder i ett visst sammanhang.

Den _beslut minne_, den modell som har tränats för att avbilda det bästa möjliga beslutet får en kontext använder en uppsättning linjära modeller. Dessa upprepade gånger har visat affärsresultat och är en beprövad metod, delvis eftersom de kan lära sig från den verkliga världen mycket snabbt utan att behöva flera pass utbildning och delvis eftersom de kan komplettera övervakade learning-modeller och djupa neurala nätverk-modeller.

Utforska/utnyttja trafik fördelningen sker slumpmässigt följa i procent för utforskning och Standardalgoritmen för utforskning är epsilon girig.

### <a name="history-of-contextual-bandits"></a>Historik över sammanhangsberoende Bandits

John Langford uttrycket i namnet sammanhangsberoende Bandits (Langford och Madsen [2007]) för att beskriva en tractable delmängd av förstärkande inlärning och har arbetat med ett halvt dussin papers förbättra vår förståelse för hur du lär dig i den här paradigm:

* Beygelzimer et al. [2011]
* Dudík et al. [2011a, b]
* Agarwal et al. [2014, 2012]
* Beygelzimer och Langford [2009]
* Li et al. [2010]

John har också meddelat flera självstudier tidigare om ämnen som gemensamma förutsägelse (ICML 2015), sammanhangsberoende Bandit teorin (NIPS 2013), aktiv inlärning (ICML 2009) och exempel komplexiteten gränser (ICML 2003)

## <a name="what-machine-learning-frameworks-does-personalizer-use"></a>Vilka ramverk för maskininlärning använder Personalizer?

För närvarande använder personalizer [Vowpal Wabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki) som grund för machine learning. Det här ramverket kan maximalt dataflöde och lägsta svarstid när att göra personanpassning rangordnar och träna modellen med alla händelser.

## <a name="references"></a>Referenser

* [Sammanhangsberoende beslutsfattande med låg tekniska skulder](https://arxiv.org/abs/1606.03966)
* [En metod för sänkt att Fair klassificering](https://arxiv.org/abs/1803.02453)
* [Effektiv sammanhangsberoende Bandits i icke-stillastående världar](https://arxiv.org/abs/1708.01799ds)
* [Återstående förlust förutsägelse: Förstärkt: learning med Nej inkrementella Feedback](https://openreview.net/pdf?id=HJNMYceCW)
* [Mappa anvisningar och Visual observationer till åtgärder med förstärkande inlärning](https://arxiv.org/abs/1704.08795)
* [Lär dig att Search bättre än din lärare](https://arxiv.org/abs/1502.02206)

## <a name="next-steps"></a>Nästa steg

[Offline-utvärdering](concepts-offline-evaluation.md) 