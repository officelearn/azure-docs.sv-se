---
title: Vanliga frågor och svar om Akustik - Cognitive Services
description: Den här sidan innehåller svar på frågor och svar ofta om projektet Akustik, inklusive hämta anvisningarna och skapa processen.
services: cognitive-services
author: kegodin
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 7e27b5117f5f0ea98916ecfefcbb0dac9323e1d9
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2018
ms.locfileid: "40181770"
---
# <a name="frequently-asked-questions"></a>Vanliga frågor och svar

## <a name="what-is-project-acoustics"></a>Vad är projektet Akustik?

Projektet Akustik Unity-plugin-programmet är ett Akustik system som beräknar ljud wave beteende före körning, som statiska belysning. Molnet har tungrodda wave fysik vid designtillfället, så körningskostnaden CPU är låg.  

## <a name="where-can-i-download-the-plugin"></a>Var kan jag hämta plugin-programmet?

Om du är intresserad av att utvärdera Akustik plugin-programmet registrera [här](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u) till förhandsversionen Designer.

## <a name="is-azure-used-at-runtime"></a>Används Azure vid körning?

Nej, molnintegration används endast under precompute scenen vid designtillfället.
 
## <a name="what-is-simulation-input"></a>Vad är simulering indata? 

Indata för simuleringen är din 3D-scenen, virtuell miljö eller spel nivå. Projektet Akustik utför 3D överför wave simuleringar som modellerar fysik av ljud noggrant, inklusive smidig ocklusion och spridning.
 
## <a name="what-is-the-runtime-cost"></a>Vad kostar runtime?

Akustik tar cirka 0,01% av CPU per källa per ram. RAM-användning beror på scen storlek och kan vara mellan 10 och 100 MB.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>Behöver jag förenkla nivå geometrin? Styra triangel antal? Kontrollera nät vattentäta?

Nej. Systemet kan mata in detaljerad nivå geometri direkt. Det kommer att voxelized för intern bearbetning.
 
## <a name="whats-in-the-runtime-lookup-table"></a>Vad ingår i uppslagstabellen?

ACE-filen är en tabell med akustiska parametrar mellan ett stort antal par för källa och lyssnare plats.
 
## <a name="can-it-handle-moving-sources"></a>Kan den hantera glidande källor?

Ja, den **Microsoft Acoustics** Unity spatializer plugin-programmet konsultationer uppslagstabell på varje ljud bearbetning skalstreck med de aktuella platserna för käll- och lyssnaren. Den spatializer DSP uppdaterar smidigt parametrarna akustiska bearbetning på varje skalstreck.
 
## <a name="can-it-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>Kan den hantera dynamiska geometri? Stänga dörrar? Väggar blåses direkt?

Nej. Akustiska parametrarna är förberäknade baserat på statisk tillstånd för en game nivå. Vi föreslår att du lämnar dörren geometri utanför Akustik och sedan tillämpa ytterligare ocklusion baserat på tillståndet för förstörbara och Lös spel objekt som använder etablerad teknik.
 
## <a name="does-it-handle-materials"></a>Hanterar det material?

Ja. Material som hämtas från de fysiska samband namnen på din nivå Driver absorptivity.
 
## <a name="what-do-the-probes-represent"></a>Vad ”avsökningar” representerar?

Avsökningar är ett urval av möjliga player platser. Varje avsökning representerar en separat wave simulering av scenen kommer på plats för avsökning. Vid körning, är akustiska parametrar för lyssnare platsen interpolerade från i närheten avsökningen platser.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>Varför lägga så mycket beräkning i molnet? Vad den köpa mig?

Projektet Akustik ger korrekta och tillförlitliga akustiska parametrar även för extremt komplexa-miljöer, hänsyn tas till hela arkitekturen. Får du smidig är spärrat/hinder utan manuellt arbete och dynamiska eko variation utan att rita volymer. Allt medan återstående lätta på CPU under körning.

## <a name="what-exactly-happens-during-baking"></a>Vad händer exakt under ”gräddning”?

Är den potentiella player platser att generera en uppsättning jämnt fördelade ”avsökning” exempel positioner. En ändamålet för en nivå består av oberoende aktiviteter för varje avsökning: anses cuboid ”simulering Region” inriktade på avsökningen och har en detaljerad wave-simulering i den regionen med upp till 25 cm-upplösning.

## <a name="next-steps"></a>Nästa steg
* Utforska den [exempel scen](sample-walkthrough.md)

