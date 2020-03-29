---
title: Förstärkning Lärande - Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizer använder information om åtgärder och aktuella sammanhang för att göra bättre rankningsförslag. Informationen om dessa åtgärder och kontext är attribut eller egenskaper som kallas funktioner.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 36071cdee25cfa99fc54b0e5c0c0aa822cb5fe2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68662842"
---
# <a name="what-is-reinforcement-learning"></a>Vad är Reinforcement Learning?

Reinforcement Learning är en metod för maskininlärning som lär sig beteenden genom att få feedback från dess användning.
 
Reinforcement Learning fungerar av:

* Att ge en möjlighet eller grad av frihet att anta ett beteende - som att fatta beslut eller val.
* Ger kontextuell information om miljön och val.
* Att ge feedback om hur väl beteendet uppnår ett visst mål.

Även om det finns många subtyper och stilar av förstärkning lärande, är detta hur konceptet fungerar i Personalizer:

* Din ansökan ger möjlighet att visa en bit innehåll från en lista med alternativ.
* Ditt program innehåller information om varje alternativ och användarens kontext.
* Ditt program beräknar en _belöningspoäng_.

Till skillnad från vissa metoder för förstärkning lärande, personalizer kräver inte en simulering för att arbeta i. Dess inlärningsalgoritmer är utformade för att reagera på en omvärld (kontra kontrollera den) och lära av varje datapunkt med en förståelse för att det är en unik möjlighet som kostar tid och pengar att skapa, och att det finns en icke-noll ånger (förlust av eventuell belöning) om suboptimal prestanda händer.

## <a name="what-type-of-reinforcement-learning-algorithms-does-personalizer-use"></a>Vilken typ av förstärkningsinlärningsalgoritmer använder Personalizer?

Den nuvarande versionen av Personalizer använder **kontextuella banditer**, en metod för förstärkning lärande som är inramad kring att fatta beslut eller val mellan diskreta åtgärder, i ett visst sammanhang.

_Beslutsminnet_, den modell som har utbildats för att fånga bästa möjliga beslut, med tanke på ett sammanhang, använder en uppsättning linjära modeller. Dessa har upprepade gånger visat affärsresultat och är ett beprövat tillvägagångssätt, delvis för att de kan lära av den verkliga världen mycket snabbt utan att behöva multi-pass utbildning, och delvis för att de kan komplettera övervakade inlärningsmodeller och djupa neurala nätverksmodeller.

Utforska / utnyttja trafikallokering görs slumpmässigt efter den procentuella uppsättningen för prospektering, och standardalgoritmen för utforskning är epsilon-giriga.

### <a name="history-of-contextual-bandits"></a>Historia kontextuella banditer

John Langford myntade namnet Contextual Bandits (Langford och Zhang [2007]) för att beskriva en tarmkanalen subet av förstärkning lärande och har arbetat på ett halvt dussin papper förbättra vår förståelse för hur man lär sig i detta paradigm:

* Beygelzimer et al. [2011]
* Dudík et al. [2011a,b]
* Agarwal et al. [2014, 2012]
* Beygelzimer och Langford [2009]
* Li et al. [2010]

John har också gett flera tutorials tidigare om ämnen som Gemensam förutsägelse (ICML 2015), Contextual Bandit Theory (NIPS 2013), Active Learning (ICML 2009) och Sample Complexity Bounds (ICML 2003)

## <a name="what-machine-learning-frameworks-does-personalizer-use"></a>Vilka maskininlärningsramverk använder Personalizer?

Personalizer använder för närvarande [Vowpal Wabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki) som grund för maskininlärning. Det här ramverket möjliggör maximalt dataflöde och lägsta svarstid när du gör anpassningsleden och tränar modellen med alla händelser.

## <a name="references"></a>Referenser

* [Fatta kontextuella beslut med låg teknisk skuld](https://arxiv.org/abs/1606.03966)
* [En minskningsmetod för rättvis klassificering](https://arxiv.org/abs/1803.02453)
* [Effektiva kontextuella banditer i icke-stationära världar](https://arxiv.org/abs/1708.01799)
* [Residual Loss Prediction: Förstärkning: lärande utan inkrementell feedback](https://openreview.net/pdf?id=HJNMYceCW)
* [Kartundervisning och visuella observationer till åtgärder med förstärkningsinlärning](https://arxiv.org/abs/1704.08795)
* [Att lära sig att söka bättre än din lärare](https://arxiv.org/abs/1502.02206)

## <a name="next-steps"></a>Nästa steg

[Offlineutvärdering](concepts-offline-evaluation.md) 