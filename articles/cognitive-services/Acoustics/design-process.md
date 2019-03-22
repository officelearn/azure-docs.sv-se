---
title: Designbegrepp för akustiksimulering
titlesuffix: Azure Cognitive Services
description: Den här konceptuell översikt förklarar hur projekt Akustik införlivar akustiska simuleringen till ljud designprocessen.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 4a1a0b15da091a1c020eb132f6b14b9ee14d334c
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316090"
---
# <a name="project-acoustics-design-process-concepts"></a>Projektet Akustik designbegrepp Process

Den här konceptuell översikt förklarar hur projekt Akustik inkluderar fysiska akustiska simulering i ljud designprocessen.

## <a name="sound-design-with-audio-dsp-parameters"></a>Bra design med ljud DSP-parametrar

3D-interaktiva rubriker uppnå sina visst ljud med hjälp av digitala ljudsignalen bearbetning (DSP) block i en motor för ljud. Dessa block-intervallet i allt från enkla blanda, genljudet, eko, fördröjning, Utjämning, komprimering och begränsar, och andra effekter. Att välja, ordna och ställa in parametrar på effekterna ansvarar ljud designern, som skapar en ljud graf som ger dess estetiska egenskaper och spelupplevelse målen i miljön.

I en interaktiv rubrik, när ljud och lyssnare flyttar i hela 3D-utrymmet, hur parametrarna efter behov ändra villkor? Ljud designern ordnar ofta volymer under hela utrymmet som är programmerade för att utlösa parametern ändringar för att uppnå ändringar i genljudet effekter, till exempel eller ankor ljud av sammansättning som lyssnaren flyttar från en del av scenen till en annan. Akustik system är också tillgängliga som kan automatisera vissa av dessa effekter.

3D-rubriker använda belysning och kinematiska fysik-system som motiveras av fysik men designer justeras att uppnå en blandning av introduktion och spelupplevelse mål. Ett visuellt designverktyg Ange inte enskilda pixelvärden, men i stället justerar 3D-modeller, material och ljus transportsystem som är alla fysiskt-baserade att byta ut visual estetik och CPU-kostnader. Vad skulle vara motsvarande processen för ljud? Projektet Akustik är ett första steg i att utforska av den här frågan. Först ska vi pratar vad det innebär att transportera akustiska energi genom ett blanksteg.

![Skärmbild av AltSpace scen i ett lager med eko zoner](media/reverb-zones-altspace.png)

## <a name="impulse-responses-acoustically-connecting-two-points-in-space"></a>Impuls svar: Akustiskt ansluter två punkter i utrymmet

Om du är bekant med ljud design kan kanske du är bekant med akustiska impuls svar. Ett akustiska impuls svar modeller transport av ett ljud från en källa till en lyssnare. Ett impuls svar kan därför avbilda var intressant effekten av utrymme Akustik, till exempel är spärrat och genljudet. Impuls svar har också vissa kraftfulla egenskaper som tillåter ljud DSP-effekter att skala. Att lägga till två ljud signaler tillsammans och bearbetning med ett impuls svar ger samma resultat som tillämpar impuls svaret separat på varje signal och lägga till resultatet. Akustiska spridning och impuls svar beror inte också på ljudet bearbetas bara på scenen som modelleras och platserna som käll- och lyssnaren. Ett impuls svar reduceras kort sagt så att scenens effekt på ljud spridning.

Ett impuls svar samlar in alla intressanta rummet akustiska effekt, och vi kan använda den för ljud effektivt med ett filter och vi kan få impuls svar mätning eller simulering. Men vad händer om vi inte riktigt vill Akustik så att den matchar fysiken exakt, men i stället mold känslomässig kraven från en scen att det? Men pixelvärden, ett impuls svar är liksom bara en lista över tusentals siffror, hur kan vi eventuellt justera dem efter aesthetic behov? Och om vi vill ha är spärrat/hinder som varierar smidigt vid överföring via dörrar eller bakom hinder, hur många impuls svar vi behöver att få en jämn inverkan? Vad händer om källan flyttar snabbt? Hur vi interpolera?

Det låter svårt att använda simulering och impuls svar för vissa aspekter av Akustik i interaktiva titlar. Men vi kan skapa ett ljud transportsystem som stöder designer justeringar om vi kan ansluta vårt impuls svar från simulering med vår välbekanta ljud DSP effekt parametrar.

## <a name="connecting-simulation-to-audio-dsp-with-parameters"></a>Ansluta simulering till ljud DSP med parametrar

En impuls svaret innehåller varje intressanta (och varje ointressanta) akustiska effekt. Ljud DSP-block, kan när deras har angetts korrekt, rendera intressanta akustiska effekt. Det är bara en fråga för att mäta ljud DSP-parametrar från ett impuls svar för att använda akustiska simulering för att driva ett ljud DSP-block för att automatisera ljud transport i ett 3D-scenen. Det här måttet är välkänt för vissa vanliga och viktiga akustiska effekter, inklusive ocklusion, hinder, portalling och genljudet.

Men om simuleringen är ansluten direkt till ljud DSP-parametrar, där är designer justering? Vad vi får? Bra, får vi en betydande mängd minne igen genom att kasta impuls svar och behålla några DSP-parametrar. Och för att ge designern del ström över slutresultatet, behöver vi bara hitta ett sätt att infoga designer mellan simuleringen och ljud DSP.

![Diagram med snygg impuls svar med parametrar som ett överlägg](media/acoustic-parameters.png)

## <a name="sound-design-by-transforming-audio-dsp-parameters-from-simulation"></a>Bra design genom att omvandla ljud DSP-parametrar från simulering

Fundera över hur din solglasögonföretag har i din vy över hela världen. Ljus dag, kan glasögon minska skiner till något mer bekväm. I ett mörkt rum, kan du inte att kunna se något alls. Glasögon anger inte en viss nivå av ljusstyrka i alla situationer. de göra behöver allt mörkare.

Om vi använder simulering för att driva vår ljud DSP med hjälp av parametrarna är spärrat och genljudet kan vi lägga till ett filter efter simulatorn kan justera de parametrar som DSP-ser ”. Filtret inte tillämpa en viss nivå av ocklusion eller eko pilslut längd mycket som solglasögonföretag inte ge varje plats samma ljusstyrka. Filtret att varje occluder occlude mindre. Eller occlude mer. Genom att lägga till och justera filter för en parameter för 'göra mörkare' ocklusion, skulle stora, öppna rum fortfarande påverkar lite till inte är spärrat, medan dörrar skulle öka från ett medium till en stark ocklusion effekt samtidigt som företaget behåller jämnhet gäller övergångar att tillhandahåller simuleringen.

I den här paradigm ändras i designer-uppgiften väljer akustiska parametrar för varje fall till att välja och justera filter att tillämpa de viktigaste DSP-parametrarna som kommer från simulering. Det höjer i designer aktiviteter från smal frågor för att ställa in smooth övergår till högre funderingar intensiteten av ocklusion och genljudet effekter och förekomsten av datakällor av sammansättning. Naturligtvis när situationen kräver är ett filter som är tillgängliga helt enkelt gå tillbaka till välja DSP-parametrar för en viss källa på en specifik situation.

## <a name="sound-design-in-project-acoustics"></a>Bra design i projektet Akustik

Projektet Akustik paketet integrerar var och en av de komponenter som beskrivs ovan: en simulator, en kodare som extraherar parametrar och bygger Akustik tillgången, ljud-DSP och ett urval av filter. Bra design med projekt Akustik innebär välja parametrar för de filter som justerar parametrarna är spärrat och genljudet härleds från simulering och tillämpas på ljud-DSP med dynamiska kontroller som visas i Redigeraren för spel och ljud-motorn.

## <a name="next-steps"></a>Nästa steg
* Prova att använda en design paradigm med hjälp av den [projekt Akustik Snabbstart för Unity](unity-quickstart.md) eller [projekt Akustik Snabbstart för Unreal](unreal-quickstart.md)
* Utforska den [projekt Akustik utforma kontroller för Unity](unity-workflow.md) eller [projekt Akustik utforma kontroller för Unreal](unreal-workflow.md)

