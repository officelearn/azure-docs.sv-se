---
title: Vanliga frågor och svar om projektakustik
titlesuffix: Azure Cognitive Services
description: Den här sidan innehåller svar på frågor som ofta ställs om Projektakustik, inklusive nedladdningsinstruktioner och bakningsprocess.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fa4b6499260219b0eb8ea4df4b4ccfd5263b57bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75770211"
---
# <a name="project-acoustics-frequently-asked-questions"></a>Vanliga frågor och svar om projektakustik

## <a name="what-is-project-acoustics"></a>Vad är Project Acoustics?

Project Acoustics svit av plugins är ett akustik system som beräknar ljudvåg beteende före körning, besläktad med statisk belysning. Molnet gör tunga lyft av våg fysik beräkningar, så körning CPU kostnaden är låg.  

## <a name="where-can-i-download-the-plugin"></a>Var kan jag ladda ner plugin?

Du kan ladda ner [Project Acoustics Unity plugin](https://www.microsoft.com/download/details.aspx?id=57346) eller Project [Acoustics Unreal plugin](https://www.microsoft.com/download/details.aspx?id=58090).

## <a name="does-project-acoustics-support-ltxgt-platform"></a>Har Project Acoustics stöd &lt;x&gt; plattform?

Project Acoustics plattformssupport utvecklas baserat på kundernas behov. Vänligen kontakta oss på [Project Acoustics issue forum](https://github.com/microsoft/ProjectAcoustics/issues) för att fråga om stöd för ytterligare plattformar.

## <a name="is-azure-used-at-runtime"></a>Används Azure vid körning?

Nej, molnintegrering används endast under förkompileringsfasen som en del av sceninställningen.
 
## <a name="what-is-simulation-input"></a>Vad är simuleringsindata? 

Simuleringsingången är din 3D-scen, virtuell miljö eller spelnivå. Project Acoustics utför 3D-volymetriska vågsimuleringar som modellerar ljudfysiken noga, inklusive jämn ocklusion och spridning.
 
## <a name="what-is-the-runtime-cost"></a>Vad kostar körningen?

Akustik tar ca 0,01% cpu per källa per bildruta. RAM-användningen beror på scenstorlek och kan variera från 10 till 100 MB.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>Måste jag förenkla nivågeometrin? Kontrollera triangelantal? Göra maskor vattentäta?

Nej. Systemet kommer att inta detaljerad nivå geometri direkt. Det kommer att voxelized för intern behandling.
 
## <a name="whats-in-the-runtime-lookup-table"></a>Vad finns i uppslagstabellen för körning?

ACE-filen innehåller är en tabell över akustiska parametrar mellan många käll- och lyssnareplatspar, samt voxelized scengeometri som används för parameterinterpolering.
 
## <a name="can-project-acoustics-handle-moving-sources"></a>Kan Project Acoustics hantera rörliga källor?

Ja, Project Acoustics konsulterar uppslagstabellen och uppdaterar ljud-DSP:n på varje fästing, så att den kan hantera rörliga källor och lyssnare.
 
## <a name="can-project-acoustics-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>Kan Project Acoustics hantera dynamisk geometri? Stänga dörrar? Väggar blåst bort?

Nej. De akustiska parametrarna förbeväpnas baserat på det statiska tillståndet för en spelnivå. Vi föreslår att lämna dörren geometri ur akustik, och sedan tillämpa ytterligare ocklusion baserat på tillståndet för förstörbara och rörliga spelobjekt med hjälp av etablerade tekniker.
 
## <a name="does-project-acoustics-use-acoustic-materials"></a>Använder Project Acoustics akustiska material?

Ja. Material plockas från de fysiska materialnamnen i din nivå, driver absorptivity.
 
## <a name="what-do-the-probes-represent"></a>Vad representerar "sonderna"?

Sonder är ett urval av möjliga spelarplatser. Varje sond representerar en separat vågsimulering av scenen som kommer från sondens plats. Vid körning interpoleras akustiska parametrar för lyssnarplatsen från närliggande avsökningsplatser.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>Varför spendera så mycket beräkning i molnet? Vad köper den mig?

Project Acoustics ger exakta och tillförlitliga akustiska parametrar även för ultrakomplexa virtuella miljöer, med beaktande av alla arkitektoniska aspekter. Det ger smidig ocklusion och obstruktion och dynamisk reverb variation utan manuellt arbete ritning volymer. Allt medan återstående ljus på CPU under körning.

## <a name="what-exactly-happens-during-baking"></a>Vad exakt händer under "bakning"?

En baka består av akustiska vågsimuleringar av kubiska simuleringsområden centrerade vid varje lyssnarsond.

## <a name="is-my-source-content-secure"></a>Är mitt källinnehåll säkert?

Projektakustiken överför inte källscengeometrin till molnet. Istället fungerar simuleringen på en voxelization av din scen, som kombineras med sondplatsdata och lagras i ett eget format.     

## <a name="next-steps"></a>Nästa steg
* Prova [exempelinnehållet Project Acoustics Unity](unity-quickstart.md) eller [Unreal sample-innehåll](unreal-quickstart.md)

