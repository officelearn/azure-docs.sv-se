---
title: Projektet Akustik Unity Design självstudien
titlesuffix: Azure Cognitive Services
description: Den här självstudien beskrivs arbetsflödet för design för projektet Akustik i Unity.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 01783aa12f586f61583b1503c796f9b523770104
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61433097"
---
# <a name="project-acoustics-unity-design-tutorial"></a>Projektet Akustik Unity Design självstudien
Den här självstudien beskrivs designverktyg och arbetsflöde för projektet Akustik i Unity.

Krav:
* Unity 2018.2 + för Windows
* En Unity-scen med en tillgång för bakade Akustik

I den här självstudien får du en Unity-scen med en tillgång för bakade Akustik på två sätt:
* [Lägg till projektet Akustik i projektet Unity](unity-integration.md), sedan [skaffa en Azure Batch-konto](create-azure-account.md), sedan [skapa Unity-scen](unity-baking.md)
* Du kan också använda den [projekt Akustik Unity exemplen](unity-quickstart.md).

## <a name="review-design-process-concepts"></a>Granska processen designbegrepp
Projektet Akustik använder vanliga ljud digitala signalbearbetning (DSP) metoder för att bearbeta dina källor och ger dig kontroll över välbekanta Akustik egenskaper, inklusive ocklusion och igång/torr blandning genljudet pilslut längd (RT60). Men grundläggande [projekt Akustik layout processen med](design-process.md) är som snarare än om du anger dessa egenskaper direkt kan du styra hur simulering resultaten används för att öka de här egenskaperna. Standardinställningarna för varje kontroll representerar fysiskt korrekt Akustik.

## <a name="design-acoustics-for-each-source"></a>Design Akustik för varje källa
Projektet Akustik innehåller ett antal kontroller för specifik Akustik design. På så sätt kan du styra vilken blandning i en scen med betonar vissa datakällor och inaktivering emphasizing andra.

### <a name="adjust-distance-based-attenuation"></a>Justera avståndet-baserade dämpning
Ljudet DSP tillhandahålls av den **projekt Akustik** Unity spatializer plugin-programmet respekterar de per källkod avståndet-baserade dämpning bygger i Unity-redigeraren. Kontroller för avståndet-baserade dämpning finns i den **ljud källa** komponent finns i den **Inspector** med ljud datakällor under **3D-ljudinställningar**:

![Skärmbild av Unity avståndet dämpning panelen](media/distance-attenuation.png)

Akustik utför beräkning i en ”simulering region” heldag player-platsen. Om en bra källa är långt från spelare, utanför den här regionen för simulering, påverkas endast geometri i rutan ljud spridning (till exempel orsakar ocklusion) som fungerar förhållandevis bra när occluders är i närheten av spelaren. Men i fall kan när spelaren är i öppet område men occluders närmar sig avlägsna ljudkälla ljudet bli orealistiskt disoccluded. Vår Föreslagen lösning är att säkerställa i sådana fall att ljud dämpning ligger till 0 på ungefär 45 m, standardavståndet spelaren vid gränsen till rutan.

![Skärmbild av Unity SpeakerMode alternativpanel](media/speaker-mode.png)

### <a name="adjust-occlusion-and-transmission"></a>Justera ocklusion och överföring
Koppla den **AcousticsAdjust** skriptet till en datakälla kan justering parametrar för den här källan. Om du vill koppla skriptet, klickar du på **Lägg till komponent** längst ned på den **Inspector** panelen och gå till **skript > Akustik justera**. Skriptet har sex kontroller:

![Skärmbild av Unity AcousticsAdjust skript](media/acoustics-adjust.png)

* **Aktivera Akustik** – styr om Akustik tillämpas på den här källan. När alternativet är avmarkerat källan ska vara spatialized med HRTFs eller panorering men det finns inga Akustik. Det innebär att inga hinder eller ocklusion dynamisk genljudet parametrar, t.ex nivå och decay tid. Genljudet används fortfarande med ett fast nivå och decay tid.
* **Ocklusion** -gäller en multiplikator för ocklusion dB-nivån beräknas av Akustik-system. Om den här multiplikatorn är större än 1, är spärrat vara överdrivna när värden mindre än 1 är spärrat effekten mer diskreta och kontrollera värdet 0 inaktiverar ocklusion.
* **Överföring (dB)** -ange dämpning (i dB) på grund av överföring via geometri. Ange det här reglaget till den lägsta nivån att inaktivera överföringen. Akustik spatializes inledande torr ljud som inkommer runt scen geometri (portaling). Överföringen ger ett ytterligare torr ankomst spatialized i linje med för att se riktning. Observera att tillämpas också avståndet dämpning kurvan för källan.

### <a name="adjust-reverberation"></a>Justera genljudet
* **Wetness (dB)** -justerar eko-kraften i dB enligt avståndet från källan. Positiva värden göra ett ljud mer reverberant medan negativa värden göra ett ljud mer torr. Klicka på kontrollen kurvan (gröna linjen) att ta fram kurvan redigeraren. Ändra kurvan genom vänsterklicka för att lägga till punkterna och dra dessa datapunkter för att skapa funktionen som du vill. X-axeln är avståndet från källan och y-axeln är eko justering i dB. Mer information om hur du redigerar kurvor finns i den här [Unity manuell](https://docs.unity3d.com/Manual/EditingCurves.html). Om du vill återställa kurvan till standard, högerklicka på **Wetness** och välj **återställa**.
* **Decay skala** -justerar en multiplikator för decay-tid. Om resultatet ändamålet anger en decay tid på 750 millisekunder, men det här värdet anges till 1.5, är decay-tiden som tillämpas på källan 1,125 millisekunder.
* **Outdoorness** -additiva justering på systemet Akustik uppskattning av hur ”utomhus” genljudet på en källa bör ljud. Ange det här värdet till 1 gör en källa alltid ljud helt utomhus när ange den till -1 gör en källa ljud helt inne.

Koppla den **AcousticsAdjustExperimental** skriptet till en datakälla kan ytterligare experimentella justering parametrar för den här källan. Om du vill koppla skriptet, klickar du på **Lägg till komponent** längst ned på den **Inspector** panelen och gå till **skript > Akustik justera experimentella**. Det finns för närvarande en experimentell kontroll:

![Skärmbild av Unity AcousticsAdjustExperimental skript](media/acoustics-adjust-experimental.png)

* **Perceptuell avståndet tänja** -använda en exponentiell förvrängning avståndet används för att beräkna förhållandet torr igång. Akustik beräknas våt nivåer i hela utrymmet som kan variera smidigt med avståndet och ger Perceptuell avståndet tips. Skev värden som är större än 1 exaggerate detta genom att öka avståndet-relaterade genljudet nivåer, vilket gör ljudet ”avlägsna”. Avståndet-baserade genljudet förvrängning värden mindre än 1 Kontrollera ändra mer diskreta, och det bra mer ”aktuella”.

## <a name="design-acoustics-for-all-sources"></a>Design Akustik för alla källor
Om du vill justera parametrarna för alla källor, klicka på kanalen remsans i Unity's **ljud Mixer**, och justera parametrarna på den **projekt Akustik Mixer** effekt.

![Skärmbild av projektet Akustik Unity Mixer anpassning panelen](media/mixer-parameters.png)

* **Wetness justera** -justerar eko-kraften i dB från alla källor i scenen baserat på käll-listener avstånd. Positiva värden göra ett ljud mer reverberant medan negativa värden göra ett ljud mer torr.
* **RT60 skala** – Multiplicerande skalära för eko tid.
* **Använd panorering** -kontroller om ljud returneras som binaural (0) eller multichannel panorering (1). Vilket värde som helst förutom 1 anger binaural. Binaural utdata spatialized med HRTFs för användning med hörlurar och multichannel utdata är spatialized med VBAP för användning med flerkanals omge talare system. Om du använder flera panner måste du markera högtalarläge som matchar dina Enhetsinställningar, finns under **Projektinställningar** > **ljud**.

## <a name="check-proper-sound-source-placement"></a>Kontrollera att rätt ljudkälla placering
Ljud källor som placeras inuti upptagna voxels kommer inte att hämta akustiska behandling. Eftersom voxels sträcka synliga scen geometri, är det möjligt att placera en källa inuti en voxel när den visas unoccluded genom visual geometri. Du kan visa projekt Akustik voxels genom att använda voxel grid kryssrutan i den **Gizmos** menyn uppe till höger på den **scen** vy.

![Skärmbild av Unity Gizmos menyn](media/gizmos-menu.png)  

Voxel visningen kan också avgör om visuella komponenter i spelet har en transformering som tillämpas. I så fall gäller samma transformering för värd för GameObject den **Akustik Manager**.

### <a name="bake-time-vs-run-time-voxels"></a>Skapa tid jämfört med körningstid voxels
Det är möjligt att visa voxels i fönstret redigeraren vid designtillfället spel och i fönstret vid körning. Storleken på voxels skiljer sig i dessa vyer. Det beror på att Akustik runtime interpolering använder en mer detaljerad voxel grid jämnare interpolering resultat. Ljudkälla placering bör kontrolleras med hjälp av runtime-voxels.

Utforma tid voxels:

![Skärmbild av projektet Akustik voxels vid designtillfället](media/voxels-design-time.png)

Runtime voxels:

![Skärmbild av projektet Akustik voxels under körning](media/voxels-runtime.png)

## <a name="next-steps"></a>Nästa steg
* Utforska fallstudier markering koncepten bakom den [utforma processen](design-process.md)

