---
title: Förbättra en modell för Custom Speech tal-tjänsten
titleSuffix: Azure Cognitive Services
description: Särskilda typer av texter som är märkta med mänsklig märkning och relaterad text kan förbättra igenkännings precisionen för en tal till text-modell som baseras på tal scenariot.
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: v-demjoh
ms.openlocfilehash: bb904482f3cb5900b724803816269f1b10ab3720
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "83727882"
---
# <a name="improve-custom-speech-accuracy"></a>Förbättra noggrannhet i Custom Speech

I den här artikeln får du lära dig hur du förbättrar kvaliteten på din anpassade modell genom att lägga till ljud, mänskligt avskrifter och relaterad text.

## <a name="accuracy-in-different-scenarios"></a>Noggrannhet i olika scenarier

Tal igenkännings scenarier varierar beroende på ljud kvalitet och språk (vokabulär-och tal format). Följande tabell undersöker fyra vanliga scenarier:

| Scenario | Ljud kvalitet | Vokabulär | Tal format |
|----------|---------------|------------|----------------|
| Kundtjänst | Låg, 8 kHz kan vara 2 människor på 1 ljud kanal, kan komprimeras | Begränsa, unikt för domän och produkter | Konversation, löst strukturerat |
| Röst assistent (till exempel Cortana eller ett enhets fönster) | Hög, 16 kHz | Enhet tung (sång titlar, produkter, platser) | Tydligt angivna ord och fraser |
| Diktering (snabb meddelande, anteckningar, sökning) | Hög, 16 kHz | Anpassa | Antecknings bearbetning |
| Textning för video dold | Varierande, inklusive varierande mikrofon användning, tillagd musik | Varierande, från möten, omciterat tal, musik texter | Läsa, beredd eller löst strukturerad |

Olika scenarier ger olika kvalitets resultat. I följande tabell kontrol leras hur innehåll från dessa fyra scenarier i [fel frekvensen i Word (WER)](how-to-custom-speech-evaluate-data.md). Tabellen visar vilka fel typer som är mest vanliga i varje scenario.

| Scenario | Tal igenkännings kvalitet | Infognings fel | Fel vid borttagning | Ersättnings fel |
|----------|----------------------------|------------------|-----------------|---------------------|
| Kundtjänst | Medel (< 30% WER) | Låg, förutom när andra människor pratar i bakgrunden | Kan vara hög. Call Center kan vara störningar och överlappande högtalare kan förvirra modellen | Medel. Produkter och person namn kan orsaka dessa fel |
| Röstassistent | Hög (kan vara < 10% WER) | Låg | Låg | Medel, på grund av sång titlar, produkt namn eller platser |
| Diktering | Hög (kan vara < 10% WER) | Låg | Låg | Högt |
| Textning för video dold | Är beroende av video typ (kan vara < 50% WER) | Låg | Kan vara högt på grund av musik, brus, mikrofon kvalitet | Jargong kan orsaka dessa fel |

Genom att fastställa komponenterna i WER (antal fel vid infogning, borttagning och ersättning) kan du avgöra vilken typ av data som ska läggas till för att förbättra modellen. Använd [Custom Speech Portal](https://speech.microsoft.com/customspeech) för att visa kvaliteten på en bas linje modell. Portalen rapporterar infognings-, ersättnings-och borttagnings frekvenser som kombineras i rapporten för WER-kvalitet.

## <a name="improve-model-recognition"></a>Förbättra modell igenkänning

Du kan minska igenkännings felen genom att lägga till tränings data i [Custom Speech portalen](https://speech.microsoft.com/customspeech). 

Planera för att underhålla din anpassade modell genom att regelbundet lägga till käll material. Din anpassade modell behöver ytterligare utbildning för att hålla koll på ändringar i dina entiteter. Du kan till exempel behöva uppdateringar av produkt namn, låt namn eller nya tjänst platser.

I följande avsnitt beskrivs hur varje typ av ytterligare tränings data kan minska antalet fel.

### <a name="add-related-text-sentences"></a>Lägg till relaterade text meningar

Ytterligare relaterade text meningar kan främst minska ersättnings fel som är relaterade till fel igenkänning av vanliga ord och företagsspecifika ord genom att visa dem i sammanhang. Domänautentiseringsuppgifter kan vara ovanliga eller uppdelade ord, men deras uttal måste vara enkla att känna igen.

> [!NOTE]
> Undvik relaterade text meningar som innehåller brus, till exempel okända tecken eller ord.

### <a name="add-audio-with-human-labeled-transcripts"></a>Lägg till ljud med medmärkta avskrifter

Ljud med medmärkta avskrifter ger störst precisions förbättringar om ljudet kommer från mål användnings fallet. Exempel måste omfatta det fullständiga tal omfånget. Till exempel skulle ett Call Center för en detalj handels butik få flest samtal om swimwear och sol glasögon under sommaren. Se till att exemplet innehåller det fullständiga omfånget som du vill identifiera.

Överväg följande information:

* Custom Speech kan bara avbilda ord kontext för att minska ersättnings fel, inte infognings-eller borttagnings fel.
* Undvik exempel som innehåller avskrifts fel, men ta med en mångfald av ljud kvaliteten.
* Undvik meningar som inte är relaterade till problem domänen. Orelaterade meningar kan skada din modell.
* När kvaliteten på avskrifter varierar kan du duplicera undantagna meningar (t. ex. utmärkta avskrifter som innehåller viktiga fraser) för att öka deras vikt.

### <a name="add-new-words-with-pronunciation"></a>Lägg till nya ord med uttal

Ord som har skapats eller är mycket specialiserade kan ha unika uttal. Dessa ord kan identifieras om ordet kan brytas ned i mindre ord för att uttala det. Om du till exempel vill känna igen **Xbox**, uttala som **X Box**. Den här metoden kommer inte att öka den övergripande noggrannheten, men kan öka igenkänningen av dessa nyckelord.

> [!NOTE]
> Den här tekniken är bara tillgänglig för vissa språk för tillfället. Mer information finns i avsnittet anpassning av uttal i [tal-till-text-tabellen](language-support.md) .

## <a name="sources-by-scenario"></a>Källor efter scenario

I följande tabell visas scenarier för röst igenkänning och en lista över käll material som du bör tänka på i de tre kategorierna för utbildnings innehåll som anges ovan.

| Scenario | Tillhör ande text meningar | Ljud + medmärkta avskrifter | Nya ord med uttal |
|----------|------------------------|------------------------------|------------------------------|
| Kundtjänst             | marknadsförings dokument, webbplats, produkt granskningar relaterade till Call Center-aktivitet | Call Center-anrop som har tilldelats av människa | termer som har tvetydiga uttal (se Xbox ovan) |
| Röstassistent         | lista meningar med alla kombinationer av kommandon och entiteter | spela in röster genom att tala om kommandon i enheten och skriva in text | namn (filmer, låtar, produkter) som har unika uttal |
| Diktering               | skrivna ingångar, t. ex. snabb meddelanden eller e-post | liknar ovan | liknar ovan |
| Textning för video dold | TV Visa skript, filmer, marknadsförings innehåll, video sammanfattningar | exakta avskrifter av videor | liknar ovan |

## <a name="next-steps"></a>Nästa steg

- [Träna modellen](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Ytterligare resurser

- [Förbered och testa dina data](how-to-custom-speech-test-data.md)
- [Inspektera dina data](how-to-custom-speech-inspect-data.md)