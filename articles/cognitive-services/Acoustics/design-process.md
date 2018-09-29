---
title: Översikt över designen för Akustik - Cognitive Services
description: Det här dokumentet beskriver hur du express design avsikt i alla tre faser av projektet Akustik arbetsflödet.
services: cognitive-services
author: kegodin
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 8f594be67c4677fae00cb01598d3899e30dae1e8
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47433232"
---
# <a name="design-process-overview"></a>Processöversikt för design
Du kan uttrycka design avsikt i alla tre faser av projektet Akustik arbetsflödet: förväg skapa scen konfiguration, ljudkälla placering och efter ändamålet design. Processen kräver mindre markup som är associerade med placera eko volymer samtidigt som du behåller designer kontroll över hur en scen låter.

## <a name="pre-bake-design"></a>Skapa förväg design
Före ändamålet scen installationsprocessen skapar scen och metadata som används för ljud wave-simulering, som omfattar att välja vilket scen element ska delta i simuleringen att tillhandahålla occlusions, reflektioner och genljudet. Metadata för scenen är valet av akustiska material för varje scen-element. Akustiska material styra mängden ljud energi som visas från varje ytan.

Absorptionskoefficient standard för alla ytor är 0.04, vilket är reflekterar. Du kan uppnå dess estetiska egenskaper och spelupplevelsen genom att justera absorptionskoefficient av olika material i hela scenen, som är särskilt framstående till lyssnare när de hör övergångar från ett område från scenen till en annan. Till exempel förbättrar övergång från ett mörkt reverberant rum till en klar, icke-reverberant utomhus scen effekten av övergången. Finjustera absorptionskoefficient på högre utomhus scen material för att uppnå detta.

Genljudet tidpunkten för ett visst material i ett rum är relaterat till dess absorptionskoefficient, med de flesta material med absorption värden i intervallet 0,01 0.20 omvänt. Material med absorptionskoefficient utanför det här intervallet är mycket absorberande.

![Eko graf](media/ReverbTimeGraph.png)

Den [ändamålet Användargränssnittet igenom](bake-ui-walkthrough.md) beskriver före ändamålet kontroller i detalj.

## <a name="sound-source-placement"></a>Ljudkälla placering
Visa voxels och avsökning punkter vid körning kan hjälpa att felsöka problem med ljud källor som fastnat i voxelized geometri. Aktivera voxel grid och avsökning pekar visning, klickar du på motsvarande kryssrutan Gizmos menyn uppe till höger i vyn scen. Om ljudkällan inuti en vägg voxel måste du flytta den till en air voxel.

![Gizmos menyn](media/GizmosMenu.png)  

Voxel visningen kan avgöra om visuella komponenter i spelet har en transformering som tillämpas. I så fall gäller samma transformering för värd för GameObject den **Akustik Manager**.

## <a name="post-bake-design"></a>Skapa efter design
Ändamålet resultaten lagras i filen ACE som är spärrat och genljudet parametrar för alla käll-listener plats par i hela scenen. Den här fysiskt korrekt resultat kan användas för ditt projekt som- och är en utmärkt utgångspunkt för designen. Efter ändamålet designprocessen anger regler för hur du transformerar ändamålet resultatet parametrarna vid körning.

### <a name="distance-based-attenuation"></a>Avståndet-baserade dämpning
Ljudet DSP tillhandahålls av den **Microsoft Acoustics** Unity spatializer plugin-programmet respekterar de per källkod avståndet-baserade dämpning bygger i Unity-redigeraren. Kontroller för avståndet-baserade dämpning finns i den **ljud källa** komponent finns i den **Inspector** med ljud datakällor under **3D-ljudinställningar**:

![Avståndet dämpning](media/distanceattenuation.png)

### <a name="tuning-scene-parameters"></a>Justering scenparametrar
Om du vill justera parametrarna för alla källor, klicka på kanalen remsans i Unity's **ljud Mixer**, och justera parametrarna på den **Akustik Mixer** effekt.

![Mixer-anpassning](media/MixerParameters.png)

### <a name="tuning-source-parameters"></a>Justering källparametrar
Koppla den **AcousticsSourceCustomization** skriptet till en datakälla kan justering parametrar för den här källan. Om du vill koppla skriptet, klickar du på **Lägg till komponent** längst ned på den **Inspector** panelen och gå till **skript > Akustik källa anpassning**. Skriptet har tre parametrar:

![Käll-anpassning](media/SourceCustomization.png)

* **Eko Power justera** -justerar eko-kraften i dB. Positiva värden göra ett ljud mer reverberant medan negativa värden göra ett ljud mer torr.
* **Decay skala** -justerar en multiplikator för decay-tid. Om resultatet ändamålet anger en decay tid på 750 millisekunder, men det här värdet anges till 1.5, är decay-tiden som tillämpas på källan 1,125 millisekunder.
* **Aktivera Akustik** – styr om Akustik tillämpas på den här källan. När alternativet är avmarkerat spatialized källan med HRTFs, men utan Akustik, vilket innebär utan hinder, är spärrat och dynamiska genljudet parametrar, t.ex nivå och decay tid. Genljudet används fortfarande med ett fast nivå och decay tid.

Olika källor kan kräva olika inställningar att uppnå vissa dess estetiska egenskaper eller spelupplevelse effekter. Dialogrutan är ett exempel som möjligt. Mänskliga ensa är mer attuned genljudet i tal, medan dialogrutan ofta måste vara begriplig för spel. Du kan ta hänsyn till detta utan att göra dialogrutan icke-diegetic genom att justera eko kraften nedåt.
