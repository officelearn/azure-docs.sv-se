---
title: Designbegrepp för akustiksimulering
titlesuffix: Azure Cognitive Services
description: Den här konceptuella översikten förklarar hur Project Acoustics innehåller akustisk simulering i ljuddesignprocessen.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 11e1e3f45b5198ddedb6c31fcd354185adef445d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854294"
---
# <a name="project-acoustics-design-process-concepts"></a>Projekt Akustik Design ProcessKoncept

Den här konceptuella översikten förklarar hur Project Acoustics införlivar fysisk akustisk simulering i ljuddesignprocessen.

## <a name="sound-design-with-audio-dsp-parameters"></a>Ljuddesign med DSP-parametrar för ljud

3D interaktiva titlar uppnå sitt speciella ljud med hjälp av ljud digital signalbehandling (DSP) block värd i en ljudmotor. Dessa block varierar i komplexitet från enkel blandning, till efterklang, eko, fördröjning, utjämning, komprimering och begränsande, och andra effekter. Att välja, ordna och ställa in parametrar på dessa effekter är ljuddesignerns ansvar, som bygger ett ljuddiagram som uppnår upplevelsens estetiska och gameplay-mål.

I en interaktiv titel, som ljud och lyssnare flytta hela 3D-rymden, hur dessa parametrar anpassa sig till förändrade förhållanden? Ljuddesignern ordnar ofta volymer i hela utrymmet som är programmerade för att utlösa parameterändringar för att uppnå ändringar i efterklangseffekter, till exempel, eller för att ducka ljud i mixen när lyssnaren flyttar från en del av scenen till en annan. Akustik system finns också som kan automatisera några av dessa effekter.

3D-titlar använder belysning och kinematiska fysiksystem som är fysikmotiverade men designanpassade för att uppnå en blandning av nedsänknings- och spelmål. En visuell designer anger inte enskilda pixelvärden, utan justerar snarare 3D-modeller, material och lätta transportsystem som alla är fysiskt baserade för att kompromissa med visuell estetik och CPU-kostnader. Vad skulle vara motsvarande process för ljud? Project Acoustics är ett första steg i utforskningen av denna fråga. Först ska vi beröra vad det innebär att transportera akustisk energi genom ett utrymme.

![Skärmbild av AltSpace-scenen överlagrad med reverb-zoner](media/reverb-zones-altspace.png)

## <a name="impulse-responses-acoustically-connecting-two-points-in-space"></a>Impulssvar: Akustiskt ansluta två punkter i rymden

Om du är bekant med ljuddesign kan du känna till akustiska impulssvar. En akustisk impulsrespons modellerar transporten av ett ljud från en källa till en lyssnare. Därför kan ett impulssvar fånga varje intressant effekt av rumsakustik som ocklusion och efterklang. Impulssvar har också vissa kraftfulla egenskaper som gör att ljud DSP effekter att skala. Att lägga till två ljudsignaler tillsammans och bearbeta med ett impulssvar ger samma resultat som att tillämpa impulssvaret separat på varje signal och lägga till resultaten. Akustisk spridning och impulssvar beror inte heller på vilket ljud som bearbetas, bara på scenen som modelleras och käll- och lyssnarplatserna. Kort sagt, ett impulssvar destillerar scenens effekt på ljudutbredning.

Ett impulssvar fångar varje intressant rum akustisk effekt, och vi kan tillämpa den på ljud effektivt med ett filter, och vi kan få impulssvar från mätning eller simulering. Men tänk om vi inte riktigt vill att akustiken ska matcha fysiken exakt, utan snarare forma den för att matcha de känslomässiga kraven på en scen? Men ungefär som pixelvärden, är en impuls svar bara en lista med tusentals siffror, hur kan vi möjligen justera den för att möta estetiska behov? Och vad händer om vi vill ha ocklusion / obstruktion som varierar smidigt när du passerar genom dörröppningar eller bakom hinder, hur många impulssvar behöver vi för att få en smidig effekt? Vad händer om källan rör sig snabbt? Hur interpolerar vi?

Det låter svårt att använda simulering och impulssvar för vissa aspekter av akustik i interaktiva titlar. Men vi kan fortfarande bygga ett ljudtransportsystem som stöder designer justeringar om vi kan ansluta våra impulssvar från simulering med våra välbekanta ljud DSP effektparametrar.

## <a name="connecting-simulation-to-audio-dsp-with-parameters"></a>Ansluta simulering till ljud DSP med parametrar

Ett impulssvar innehåller varje intressant (och varje ointressant) akustisk effekt. Audio DSP block, när deras parametrar är korrekt inställda, kan göra intressant akustisk effekt. Använda akustisk simulering för att driva ett ljud DSP block för att automatisera ljudtransport i en 3D-scen är bara en fråga om att mäta ljud DSP parametrar från en impulsrespons. Denna mätning är väl förstådd för vissa vanliga och viktiga akustiska effekter inklusive ocklusion, obstruktion, portalling och efterklang.

Men om simuleringen är ansluten direkt till ljud DSP parametrar, var är designern justering? Vad fick vi? Tja, vi får en betydande mängd minne tillbaka genom att kasta impulssvar och behålla några DSP parametrar. Och för att ge designern lite makt över slutresultatet behöver vi bara hitta ett sätt att infoga designern mellan simuleringen och ljudet DSP.

![Graf med stiliserad impulsrespons med parametrar överlagrad](media/acoustic-parameters.png)

## <a name="sound-design-by-transforming-audio-dsp-parameters-from-simulation"></a>Ljuddesign genom att omvandla DSP-parametrar för ljud från simulering

Tänk på vilken effekt dina solglasögon har på din syn på världen. En ljus dag kan glasögonen reducera glansen till något bekvämare. I ett mörkt rum kanske du inte kan se någonting alls. Glasögonen ställer inte in en viss nivå av ljusstyrka i alla situationer; de gör allt mörkare.

Om vi använder simulering för att driva vårt ljud DSP med ocklusion och efterklang parametrar, kan vi lägga till ett filter efter simulatorn för att justera de parametrar som DSP "ser". Filtret skulle inte genomdriva en viss nivå av ocklusion eller reverb svanslängd, ungefär som solglasögon inte gör varje rum samma ljusstyrka. Filtret kan bara göra varje occluder ocklude mindre. Eller ocklude mer. Genom att lägga till och justera en "mörkare" ocklusion parameter filter, stora, öppna rum skulle fortfarande ha liten eller ingen ocklusion effekt, medan dörröppningar skulle öka från ett medium till en stark ocklusion effekt, samtidigt som jämnhet i effekt övergångar som simuleringen ger.

I detta paradigm ändras designerns uppgift från att välja akustiska parametrar för varje situation, till att välja och justera filter för att tillämpa på de viktigaste DSP-parametrarna som kommer från simuleringen. Det höjer designerns verksamhet från den smala oro för att inrätta smidiga övergångar till högre oro för intensiteten av ocklusion och efterklang effekter och förekomsten av källor i mixen. Naturligtvis, när situationen kräver, ett filter alltid tillgänglig är att helt enkelt gå tillbaka till att välja DSP parametrar för en viss källa i en viss situation.

## <a name="sound-design-in-project-acoustics"></a>Ljuddesign i Project Acoustics

Project Acoustics-paketet integrerar var och en av de komponenter som beskrivs ovan: en simulator, en kodare som extraherar parametrar och bygger akustiktillgången, ljud-DSP och ett urval av filter. Ljuddesign med Project Acoustics innebär att välja parametrar för de filter som justerar ocklusions- och efterklangsparametrarna som härleds från simulering och tillämpas på ljud-DSP, med dynamiska kontroller exponerade inuti spelredigeraren och ljudmotorn.

## <a name="next-steps"></a>Nästa steg
* Prova designparadigmet med quickstart för [Project Acoustics för Unity](unity-quickstart.md) eller project [acoustics quickstart för Unreal](unreal-quickstart.md)
* Utforska [designkontrollerna för Project Acoustics för Unity](unity-workflow.md) eller [designkontrollerna project acoustics för Unreal](unreal-workflow.md)

