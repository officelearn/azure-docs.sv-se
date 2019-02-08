---
title: Översikt över designen för projektet Akustik
titlesuffix: Azure Cognitive Services
description: Det här dokumentet beskriver hur du express design avsikt i alla tre faser av projektet Akustik arbetsflödet.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: bb5f309a96feac2caea85fbe81b7216eecfc4b79
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55873945"
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

### <a name="voxel-size-discrepancies"></a>Voxel storlek avvikelser
Du kanske märker att storleken på voxels som används för att illustrera som från scenen nät som deltar i Akustik ändamålet är olika i designvyerna tid och runtime. Detta påverkar inte kvalitet/Granulariteten för din valda simulering frekvens men är i stället en biproduct av runtime voxelized scenen. Vid körning är simulering voxels ”förfinade” för att stödja interpolering mellan källplatser. Detta kan också design tid placeringen av ljud källor närmare scen-nät än simulering voxel storlek tillåter – eftersom datakällor i en voxel som innehåller ett akustiskt behandlade nät inte ska du göra något ljud.

Här följer två bilder som visar skillnaden mellan design (före ändamålet) voxels och runtime (efter ändamålet) voxels som visualiseras av Unity-plugin-programmet:

Utforma tid voxels:

![VoxelsDesignTime](media/VoxelsDesignTime.png)

Runtime voxels:

![VoxelsRuntime](media/VoxelsRuntime.png)

Beslutet om huruvida voxel nät korrekt representerar arkitektur/strukturella scen nät ska göras med hjälp av design läge voxels inte förfinade voxels runtime visualiseringen.

## <a name="post-bake-design"></a>Skapa efter design
Ändamålet resultaten lagras i filen ACE som är spärrat och genljudet parametrar för alla käll-listener plats par i hela scenen. Den här fysiskt korrekt resultat kan användas för ditt projekt som- och är en utmärkt utgångspunkt för designen. Efter ändamålet designprocessen anger regler för hur du transformerar ändamålet resultatet parametrarna vid körning.

### <a name="distance-based-attenuation"></a>Avståndet-baserade dämpning
Ljudet DSP tillhandahålls av den **Microsoft Acoustics** Unity spatializer plugin-programmet respekterar de per källkod avståndet-baserade dämpning bygger i Unity-redigeraren. Kontroller för avståndet-baserade dämpning finns i den **ljud källa** komponent finns i den **Inspector** med ljud datakällor under **3D-ljudinställningar**:

![Avståndet dämpning](media/distanceattenuation.png)

Akustik utför beräkning i en ”simulering region” heldag player-platsen. Om en bra källa är långt från spelare, utanför den här regionen för simulering, påverkas endast geometri i rutan ljud spridning (till exempel orsakar ocklusion) som fungerar förhållandevis bra när occluders är i närheten av spelaren. Men i fall kan när spelaren är i öppet område men occluders närmar sig avlägsna ljudkälla ljudet bli orealistiskt disoccluded. Vår Föreslagen lösning är att säkerställa i sådana fall att ljud dämpning ligger till 0 på ungefär 45 m, standardavståndet spelaren vid gränsen till rutan.

### <a name="tuning-scene-parameters"></a>Justering scenparametrar
Om du vill justera parametrarna för alla källor, klicka på kanalen remsans i Unity's **ljud Mixer**, och justera parametrarna på den **projekt Akustik Mixer** effekt.

![Mixer-anpassning](media/MixerParameters.png)

* **Wetness justera** -justerar eko-kraften i dB från alla källor i scenen baserat på käll-listener avstånd. Positiva värden göra ett ljud mer reverberant medan negativa värden göra ett ljud mer torr.
* **RT60 skala** – Multiplicerande skalära för eko tid.
* **Använd panorering** -kontroller om ljud returneras som binaural (0) eller multichannel panorering (1). Vilket värde som helst förutom 1 anger binaural. Binaural utdata spatialized med HRTFs för användning med hörlurar och multichannel utdata är spatialized med VBAP för användning med flerkanals omge talare system. Om du använder flera panner måste du markera högtalarläge som matchar dina Enhetsinställningar, finns under **Projektinställningar** > **ljud**.

![SpeakerMode](media/SpeakerMode.png)

### <a name="tuning-source-parameters"></a>Justering källparametrar
Koppla den **AcousticsAdjust** skriptet till en datakälla kan justering parametrar för den här källan. Om du vill koppla skriptet, klickar du på **Lägg till komponent** längst ned på den **Inspector** panelen och gå till **skript > Akustik justera**. Skriptet har sex kontroller:

![AcousticsAdjust](media/AcousticsAdjust.png)

* **Aktivera Akustik** – styr om Akustik tillämpas på den här källan. När alternativet är avmarkerat källan ska vara spatialized med HRTFs eller panorering men det finns inga Akustik. Det innebär att inga hinder och är spärrat dynamisk genljudet parametrar, t.ex nivå och decay tid. Genljudet används fortfarande med ett fast nivå och decay tid.
* **Ocklusion** -gäller en multiplikator för ocklusion dB-nivån beräknas av Akustik-system. Om den här multiplikatorn är större än 1, är spärrat vara överdrivna när värden mindre än 1 är spärrat effekten mer diskreta och kontrollera värdet 0 inaktiverar ocklusion.
* **Överföring (dB)** -ange dämpning (i dB) på grund av överföring via geometri. Ange det här reglaget till den lägsta nivån att inaktivera överföringen. Akustik spatializes inledande torr ljud som inkommer runt scen geometri (portaling). Överföringen ger ett ytterligare torr ankomst spatialized i linje med för att se riktning. Observera att tillämpas också avståndet dämpning kurvan för källan.
* **Wetness (dB)** -justerar eko-kraften i dB enligt avståndet från källan. Positiva värden göra ett ljud mer reverberant medan negativa värden göra ett ljud mer torr. Klicka på kontrollen kurvan (gröna linjen) att ta fram kurvan redigeraren. Ändra kurvan genom vänsterklicka för att lägga till punkterna och dra dessa datapunkter för att skapa funktionen som du vill. X-axeln är avståndet från källan och y-axeln är eko justering i dB. Mer information om hur du redigerar kurvor finns i den här [Unity manuell](https://docs.unity3d.com/Manual/EditingCurves.html). Om du vill återställa kurvan till standard, högerklicka på **Wetness** och välj **återställa**.
* **Decay skala** -justerar en multiplikator för decay-tid. Om resultatet ändamålet anger en decay tid på 750 millisekunder, men det här värdet anges till 1.5, är decay-tiden som tillämpas på källan 1,125 millisekunder.
* **Outdoorness** -additiva justering på systemet Akustik uppskattning av hur ”utomhus” genljudet på en källa bör ljud. Ange det här värdet till 1 gör en källa alltid ljud helt utomhus när ange den till -1 gör en källa ljud inne.

Olika källor kan kräva olika inställningar att uppnå vissa dess estetiska egenskaper eller spelupplevelse effekter. Dialogrutan är ett exempel som möjligt. Mänskliga ensa är mer attuned genljudet i tal, medan dialogrutan ofta måste vara begriplig för spel. Du kan ta hänsyn till detta utan att göra dialogrutan icke-diegetic genom att flytta den **Wetness** nedåt, justera den **Perceptuell avståndet tänja** parameter som beskrivs nedan, som att lägga till några  **Överföringen** för vissa torr ljud boost sprider via väggar och/eller minska den **ocklusion** från 1 till har flera ljud som tas emot via portaler.

Koppla den **AcousticsAdjustExperimental** skriptet till en datakälla kan ytterligare experimentella justering parametrar för den här källan. Om du vill koppla skriptet, klickar du på **Lägg till komponent** längst ned på den **Inspector** panelen och gå till **skript > Akustik justera experimentella**. Det finns för närvarande en experimentell kontroll:

![AcousticsAdjustExperimental](media/AcousticsAdjustExperimental.png)

* **Perceptuell avståndet tänja** -använda en exponentiell förvrängning avståndet används för att beräkna förhållandet torr igång. Akustik beräknas våt nivåer i hela utrymmet som kan variera smidigt med avståndet och ger Perceptuell avståndet tips. Skev värden som är större än 1 exaggerate detta genom att öka avståndet-relaterade genljudet nivåer, vilket gör ljudet ”avlägsna”. Avståndet-baserade genljudet förvrängning värden mindre än 1 Kontrollera ändra mer diskreta, och det bra mer ”aktuella”.

