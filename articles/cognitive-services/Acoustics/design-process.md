---
title: Designbegrepp för akustiksimulering
titlesuffix: Azure Cognitive Services
description: Den här konceptuella översikten förklarar hur projekt akustiskt införlivar akustisk simulering med ljud design processen.
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
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854294"
---
# <a name="project-acoustics-design-process-concepts"></a>Process koncept för projekt akustiska design processer

Den här konceptuella översikten förklarar hur projekt akustiskt införlivar fysisk akustisk simulering i ljud design processen.

## <a name="sound-design-with-audio-dsp-parameters"></a>Ljud design med parametrar för ljud-DSP

interaktiva 3D-titlar får sitt specifika ljud med DSP-block (digital signal process bearbetning) som finns i en ljud motor. Dessa block varierar i komplexitet från enkel mixning, till Reverberation, eko, fördröjning, utjämning, komprimering och begränsning samt andra effekter. Om du väljer, ordnar och ställer in parametrar för dessa effekter är ljud designern ansvar, som skapar ett ljud diagram som uppnår miljöns smak och spel mål.

I en interaktiv rubrik, när ljud och lyssnare flyttas i 3D-rymden, hur kan parametrarna anpassas till ändrade villkor? Ljud designern ordnar ofta volymer i det utrymme som är programmerat för att utlösa parameter ändringar för att uppnå ändringar i Reverberation-effekter, till exempel eller för att sätta upp ljud i blandningen när lyssnaren flyttas från en del av scenen till en annan. Akustiska system är också tillgängliga som kan automatisera vissa av dessa effekter.

3D-rubriker använder belysnings-och kinematik fysik system som är fysik-motiverade men designer-justerade för att uppnå en blandning av nedspännande och spel mål. En visuell designer anger inte enskilda pixel värden, utan justerar i stället 3D-modeller, material och lätta transport system som är fysiskt baserade på att utbyta visuella objekt och CPU-kostnader. Vad är den likvärdiga processen för ljud? Projekt akustiska är ett första steg i utforskningen av den här frågan. Först trycker vi på vad det innebär att transportera akustisk energi genom ett utrymme.

![Skärm bild av AltSpace-scen överlappad med Reverb-zoner](media/reverb-zones-altspace.png)

## <a name="impulse-responses-acoustically-connecting-two-points-in-space"></a>Impulse svar: Att akustiskt ansluta två punkter i rymden

Om du är bekant med ljud design kan du vara bekant med akustiska Impulse-svar. Ett akustiskt Impulse-svar modellerar transporten av ett ljud från en källa till en lyssnare. Ett Impulse-svar kan därför fånga alla intressanta effekter av rums akustiskhet, till exempel ocklusion och Reverberation. Impulse-svar har också vissa kraftfulla egenskaper som gör att ljud-DSP-effekter kan skalas. Genom att lägga till två ljud signaler tillsammans och bearbetningen med ett Impulse-svar får du samma resultat som när du tillämpar Impulse-svaret separat på varje signal och lägger till resultaten. Akustisk spridning och Impulse svar är inte heller beroende av att ljudet bearbetas, endast på den scen som är modell och käll-och lyssnar platser. Ett Impulse-svar använder sig av till att från scenens effekt vid ljud spridningen.

Ett Impulse-svar fångar varje intressant rums akustisk och vi kan använda den för att effektivt kunna använda ett filter, och vi kan få Impulse svar från mätningar eller simuleringar. Men vad händer om vi inte vill att de akustiska ljuden ska matcha fysiken exakt, utan i stället forma den för att matcha känslomässig-kraven för en scen? Men ungefär som pixel värden är ett Impulse-svar bara en lista med tusentals siffror, hur kan vi ändra det för att möta de smak behoven? Och vad gör vi om vi vill ha ocklusion/hinder som varierar smidigt samtidigt som de passerar genom doorways eller bakom hinder, hur många Impulse-svar behöver vi för att få en smidig påverkan? Vad händer om källan flyttas snabbt? Hur interpolerar vi?

Det låter svårt att använda simulerings-och Impulse-svar för vissa aspekter av akustiska titlar i interaktiva titlar. Men vi kan fortfarande bygga ett ljud transport system som stöder designer-justeringar om vi kan ansluta våra Impulse-svar från simulering med våra välkända parametrar för ljud-och-effekter.

## <a name="connecting-simulation-to-audio-dsp-with-parameters"></a>Ansluter simuleringen till Audio DSP med parametrar

Ett Impulse-svar innehåller alla intressanta (och varje intressant) akustiskt resultat. Ljud-DSP-block, när deras parametrar är korrekt inställda, kan återge en intressant akustisk funktion. Använda akustisk simulering för att driva ett ljud-DSP-block för att automatisera ljud transport i en 3D-scen är bara en fråga om att mäta egenskaperna för ljudet DSP från ett Impulse-svar. Detta mått är bra att förstå för vissa vanliga och viktiga akustiska effekter, inklusive ocklusion, hinder, portaling och Reverberation.

Men om simuleringen är direkt ansluten till ljud-DSP-parametrarna, där är designer-justeringen? Vad fick vi? Dessutom får vi en betydande mängd minne tillbaka genom att ta bort Impulse-svar och behålla några DSP-parametrar. Och för att ge designern lite kraft över slut resultatet behöver vi bara hitta ett sätt att infoga designern mellan simuleringen och ljud-DSP.

![Graf med impulset svar med parametrar överlappande](media/acoustic-parameters.png)

## <a name="sound-design-by-transforming-audio-dsp-parameters-from-simulation"></a>Ljud design genom omvandling av ljud-DSP-parametrar från simulering

Ta hänsyn till vilken påverkan dina sol glasögon har på din vy av världen. På en ljus dag kan glasögonen minska skinet till något bekvämare. I ett mörkt rum kanske du inte kan se något alls. Glasögonen anger inte en viss nivå av ljus styrka i alla situationer. de gör bara allt mörkare.

Om vi använder simulering för att köra vår ljud-DSP med parametrarna ocklusion och Reverberation kan vi lägga till ett filter efter att simulatorn har justerat parametrarna som DSP ' ser ". Filtret tvingar inte en viss nivå av ocklusion-eller reverb-längd, ungefär som sol Glaser ger inte alla rum samma ljus styrka. Filtret kan bara göra var occluder occlude mindre. Eller occlude mer. Genom att lägga till och justera ett "mörkare" ocklusion parameter filter, skulle stora öppna rum fortfarande ha lite till ingen ocklusion-effekt medan doorways skulle öka från en medels Tor till en stark ocklusion-effekt, samtidigt som den bevarar smidighet i effekt över gångar som simuleringen erbjuder.

I det här paradigmet ändrar designerns aktivitet från att välja akustiska parametrar för varje situation, för att välja och justera filter som ska tillämpas på de viktigaste DSP-parametrarna som kommer från simuleringen. Den höjer designens aktiviteter från de smala problemen med att ställa in smidiga över gångar för att öka intensiteten hos ocklusion-och Reverberation-effekter och förekomsten av källor i blandningen. När situationen kräver det är ett filter alltid tillgängligt för att gå tillbaka till att välja DSP-parametrar för en speciell källa i en speciell situation.

## <a name="sound-design-in-project-acoustics"></a>Ljud design i projekt akustiska data

Project Akustiske-paketet integrerar var och en av de komponenter som beskrivs ovan: en simulator, en kodare som extraherar parametrar och skapar en akustisk till gång, ljud-DSP och ett urval av filter. Med en ljud design med projekt akustiskt kan du välja parametrar för de filter som justerar parametrarna ocklusion och Reverberation som härleds från simuleringen och tillämpas på ljud-DSP, med dynamiska kontroller som visas i spel redigeraren och ljud motorn.

## <a name="next-steps"></a>Nästa steg
* Prova design paradigmet med hjälp av [projektet akustisk snabb start för](unity-quickstart.md) enhets-eller [projekt akustisker-snabb start för Unreal](unreal-quickstart.md)
* Utforska [design kontrollerna för projekt akustiskt för Unit](unity-workflow.md) eller [Project akustisker design Controls for Unreal](unreal-workflow.md)

