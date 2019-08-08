---
title: Vanliga frågor och svar om projekt akustiskt
titlesuffix: Azure Cognitive Services
description: På den här sidan får du svar på frågor som ofta ifrågasätts om projekt akustiskt, inklusive hämtnings anvisningar och bageri processen.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: f0484fc9fc2af5514ba0f5b61277146a51757057
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855032"
---
# <a name="project-acoustics-frequently-asked-questions"></a>Vanliga frågor och svar om projekt akustiskt

## <a name="what-is-project-acoustics"></a>Vad är Project Acoustics?

Projektet akustiskt plugin-program är ett akustiskt system som beräknar ljud vågens beteende före körning, via till statisk belysning. Molnet gör den tunga Upplyftningen av Wave fysik-beräkningar, så körnings PROCESSORns kostnad är låg.  

## <a name="where-can-i-download-the-plugin"></a>Var kan jag hämta plugin-programmet?

Du kan ladda ned plugin-programmet för [Project Akustiske Unit](https://www.microsoft.com/download/details.aspx?id=57346) eller [Project akustisks Unreal](https://www.microsoft.com/download/details.aspx?id=58090).

## <a name="does-project-acoustics-support-ltxgt-platform"></a>Stöder &lt;Project akustisk x&gt; -plattformen?

Stöd för projekt akustiska plattformar utvecklas utifrån kundernas behov. Kontakta oss i avsnittet [projekt akustiska problem](https://github.com/microsoft/ProjectAcoustics/issues) och fråga om stöd för ytterligare plattformar.

## <a name="is-azure-used-at-runtime"></a>Används Azure vid körning?

Nej, Cloud integration används endast under för beräknings fasen som en del av scen installationen.
 
## <a name="what-is-simulation-input"></a>Vad är simulerings ingångar? 

Simulerings indatamängden är din 3D-scen, virtuell miljö eller spel nivå. Med projektets akustiska modeller utförs 3D-våg simuleringar som modellerar fysiken av ljud tätt, inklusive mjuk ocklusion och spridning.
 
## <a name="what-is-the-runtime-cost"></a>Vad är körnings kostnaden?

Akustiska steg tar cirka 0,01% av processor per källa per bild ruta. RAM-användningen beror på scen storleken och kan vara mellan 10 och 100 MB.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>Behöver jag förenkla nivå geometrin? Vill du kontrol lera antalet trianglar? Får du vatten täta maskor?

Nej. Systemet kommer att hämta information om den detaljerade nivån direkt. Den kommer att vara voxelized för intern bearbetning.
 
## <a name="whats-in-the-runtime-lookup-table"></a>Vad är en söknings tabell för körnings miljön?

ACE-filen innehåller en tabell med akustiska parametrar mellan flera olika käll-och lyssnar plats par, samt voxelized Scene-geometri som används för parameter-interpolation.
 
## <a name="can-project-acoustics-handle-moving-sources"></a>Kan projekt akustiskt hantera rörliga källor?

Ja, projekt akustiskt rådfrågar uppslags tabellen och uppdaterar ljudet DSP på varje Tick, så att det kan hantera rörliga källor och lyssnare.
 
## <a name="can-project-acoustics-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>Kan projekt akustiskt hantera dynamisk geometri? Stänger du dörrar? Väggar har lämnats bort?

Nej. Akustiska parametrar är förberäknade baserat på en spel nivås statiska tillstånd. Vi rekommenderar att du lämnar dörr geometrin ur akustiska enheter och sedan tillämpar ytterligare ocklusion baserat på status för destructible och flyttbara spel objekt med etablerade tekniker.
 
## <a name="does-project-acoustics-use-acoustic-materials"></a>Använder Project akustiskt akustiskt material?

Ja. Material plockas från fysiska material namn på din nivå, med absorptivity.
 
## <a name="what-do-the-probes-represent"></a>Vad representerar "avsökningar"?

Avsökningar är en sampling av möjliga Player-platser. Varje avsökning representerar en separat våg simulering av den scen som kommer från avsöknings platsen. Vid körning interpoleras akustiska parametrar för lyssnar platsen från nära avsöknings platser.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>Varför spendera så mycket data bearbetning i molnet? Vad köper jag mig?

Project-Akustisker ger korrekta och pålitliga akustiska parametrar, även för extremt komplexa virtuella miljöer, som tar alla arkitektur aspekt i beräkningen. Den ger smidig ocklusion och hinder och dynamisk reverb-variation utan manuell arbete för att rita volymer. Allt medan återstående ljus på processor under körning.

## <a name="what-exactly-happens-during-baking"></a>Vad händer exakt under "bak"?

En bageri består av akustiska våg simuleringar av cuboid simulerings regioner centrerade vid varje lyssnare-avsökning.

## <a name="next-steps"></a>Nästa steg
* Försök med [projektet akustiskt exempel på innehåll](unity-quickstart.md) eller [Unreal exempel innehåll](unreal-quickstart.md)

