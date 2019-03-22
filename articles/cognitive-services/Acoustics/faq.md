---
title: Vanliga och frågor svar om projektet Akustik
titlesuffix: Azure Cognitive Services
description: Den här sidan innehåller svar på frågor och svar ofta om projektet Akustik, inklusive hämta anvisningarna och skapa processen.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: resources
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 3426681aad19bbe01c0f7e88ca16e79c0b490c36
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317314"
---
# <a name="project-acoustics-frequently-asked-questions"></a>Vanliga och frågor svar om projektet Akustik

## <a name="what-is-project-acoustics"></a>Vad är Project Acoustics?

Projektet Akustik uppsättning plugin-program är ett Akustik system som beräknar ljud wave beteende före körning, som statiska belysning. Molnet har wave tungrodda fysik beräkningar, så körningskostnaden CPU är låg.  

## <a name="where-can-i-download-the-plugin"></a>Var kan jag hämta plugin-programmet?

Du kan ladda ned den [projekt Akustik Unity-plugin-programmet](https://www.microsoft.com/download/details.aspx?id=57346) eller [projekt Akustik Unreal plugin-programmet](https://www.microsoft.com/download/details.aspx?id=58090).

## <a name="does-project-acoustics-support-x-platform"></a>Har stöd för projektet Akustik <x> plattform?

Projektstöd Akustik plattform utvecklas baserat på kundernas behov. Kontakta oss på den [projekt Akustik forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=projectacoustics) att fråga om stöd för ytterligare plattformar.

## <a name="is-azure-used-at-runtime"></a>Används Azure vid körning?

Nej, molnintegration används endast under fasen precompute som en del av installationen scen.
 
## <a name="what-is-simulation-input"></a>Vad är simulering indata? 

Indata för simuleringen är din 3D-scenen, virtuell miljö eller spel nivå. Projektet Akustik utför 3D överför wave simuleringar som modellerar fysik av ljud noggrant, inklusive smidig ocklusion och spridning.
 
## <a name="what-is-the-runtime-cost"></a>Vad kostar runtime?

Akustik tar cirka 0,01% av CPU per källa per ram. RAM-användning beror på scen storlek och kan vara mellan 10 och 100 MB.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>Behöver jag förenkla nivå geometrin? Styra triangel antal? Kontrollera nät vattentäta?

Nej. Systemet kan mata in detaljerad nivå geometri direkt. Det kommer att voxelized för intern bearbetning.
 
## <a name="whats-in-the-runtime-lookup-table"></a>Vad ingår i uppslagstabellen?

ACE-filen innehåller en tabell över akustiska parametrar mellan ett stort antal källa och lyssnare plats par samt voxelized scen geometri som används för parametern interpolation.
 
## <a name="can-project-acoustics-handle-moving-sources"></a>Kan hantera projekt Akustik glidande källor?

Ja, projekt Akustik konsultationer uppslagstabellen och uppdaterar ljud DSP på varje skalstreck så att den kan hantera flytta källor och lyssnaren.
 
## <a name="can-project-acoustics-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>Projektet Akustik kan hantera dynamiska geometri? Stänga dörrar? Väggar blåses direkt?

Nej. Akustiska parametrarna är förberäknade baserat på statisk tillstånd för en game nivå. Vi rekommenderar att lämna dörren geometri utanför Akustik och sedan tillämpa ytterligare ocklusion baserat på status för förstörbara och Lös spel objekt med hjälp av upprättas tekniker.
 
## <a name="does-project-acoustics-use-acoustic-materials"></a>Använder projekt Akustik akustiska material?

Ja. Material som hämtas från de fysiska samband namnen på din nivå Driver absorptivity.
 
## <a name="what-do-the-probes-represent"></a>Vad ”avsökningar” representerar?

Avsökningar är ett urval av möjliga player platser. Varje avsökning representerar en separat wave simulering av scenen kommer på plats för avsökning. Vid körning, är akustiska parametrar för lyssnare platsen interpolerade från i närheten avsökningen platser.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>Varför lägga så mycket beräkning i molnet? Vad den köpa mig?

Projektet Akustik ger korrekta och tillförlitliga akustiska parametrar även för extremt komplexa-miljöer, hänsyn tas till hela arkitekturen. Får du smidig ocklusion och hinder och dynamiska eko variation utan manuellt arbete för att rita volymer. Allt medan återstående lätta på CPU under körning.

## <a name="what-exactly-happens-during-baking"></a>Vad händer exakt under ”gräddning”?

En ändamålet består av akustiska wave simuleringar cuboid simulering regioner som är inriktade på varje lyssnare avsökningen.

## <a name="next-steps"></a>Nästa steg
* Prova den [projekt Akustik Unity exemplen](unity-quickstart.md) eller [Unreal exemplen](unreal-quickstart.md)

