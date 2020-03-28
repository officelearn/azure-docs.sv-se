---
title: Project Akustik Unity Design Handledning
titlesuffix: Azure Cognitive Services
description: I den här självstudien beskrivs designarbetsflödet för projektakustik i Unity.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fd00e4105ce4edae9d014df2a83c5ae3aaf778da
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68854269"
---
# <a name="project-acoustics-unity-design-tutorial"></a>Project Akustik Unity Design Handledning
I den här självstudien beskrivs designverktygen och arbetsflödet för Projektakustik i Unity.

Krav:
* Unity 2018.2+ för Windows
* En Unity scen med en bakad akustik tillgång

För den här guiden kan du få en Unity scen med en bakad akustik tillgång på två sätt:
* [Lägg till projektakustik i Unity-projektet](unity-integration.md)och hämta sedan [ett Azure Batch-konto](create-azure-account.md)och baka sedan [din Unity-scen](unity-baking.md)
* Du kan också använda [exempelinnehållet Project Acoustics Unity](unity-quickstart.md).

## <a name="review-design-process-concepts"></a>Granska designprocesskoncept
Project Acoustics använder vanliga DSP-metoder (Audio Digital Signal Processing) för att bearbeta dina källor, och ger dig kontroll över välbekanta akustikegenskaper, inklusive ocklusion, våt/torr blandning och efterklangssvanslängd (RT60). Men det centrala [designprocesskonceptet för Project Acoustics](design-process.md) är att du i stället för att ställa in dessa egenskaper direkt styr hur simuleringsresultat används för att driva dessa egenskaper. Standardinställningarna för varje kontroll representerar fysiskt exakt akustik.

## <a name="design-acoustics-for-each-source"></a>Designakustik för varje källa
Project Acoustics innehåller ett antal källspecifika akustikdesignkontroller. Detta gör att du kan styra mixen i en scen genom att betona vissa källor och de-betonar andra.

### <a name="adjust-distance-based-attenuation"></a>Justera avståndsbaserad dämpning
Ljudet DSP som tillhandahålls av **Project Acoustics** Unity spatializer plugin respekterar per källa avstånd-baserad dämpning inbyggd i Unity Editor. Kontroller för avståndsbaserad dämpning finns i komponenten **Ljudkälla** som finns på **panelen Inspektör** med ljudkällor under **3D-ljudinställningar:**

![Skärmbild av panelen Alternativ för enhetsavstämningsalternativ](media/distance-attenuation.png)

Akustik utför beräkning i en "simuleringsregion" låda centrerad kring spelarens plats. Om en ljudkälla är långt från spelaren, som ligger utanför detta simuleringsområde, kommer endast geometri i lådan att påverka ljudspridningen (t.ex. orsaka ocklusion) som fungerar ganska bra när ockludrar är i närheten av spelaren. Men i de fall då spelaren är i öppet utrymme men occluders är nära den avlägsna ljudkällan, kan ljudet bli orealistiskt disoccluded. Vår föreslagna lösning är att se till att ljuddämpningen faller av till 0 på ca 45 m, spelarens standard horisontella avstånd till kanten av lådan.

![Skärmbild av alternativpanelen Unity SpeakerMode](media/speaker-mode.png)

### <a name="adjust-occlusion-and-transmission"></a>Justera ocklusion och överföring
Genom att koppla **acousticsAdjust-skriptet** till en källa kan justeringsparametrar för den källan aktiveras. Om du vill bifoga skriptet klickar du på **Lägg till komponent** längst ned på panelen **Inspektör** och navigerar till **Skript > Akustikjustering**. Skriptet har sex kontroller:

![Skärmbild av Unity AcousticsAdjust-skript](media/acoustics-adjust.png)

* **Aktivera Akustik** - Styr om akustik tillämpas på den här källan. När den inte är markerad kommer källan att spatialiseras med HRTFs eller panorering, men det blir ingen akustik. Detta innebär ingen obstruktion, ocklusion eller dynamisk efterklang parametrar såsom nivå och sönderfall tid. Efterklang tillämpas fortfarande med en fast nivå och sönderfallstid.
* **Ocklusion** - Applicera en multiplikator på ocklusion dB-nivån som beräknas av akustiksystemet. Om den här multiplikatorn är större än 1, kommer ocklusion att överdrivas, medan värden som är mindre än 1 gör ocklusionseffekten mer subtil och värdet 0 inaktiverar ocklusion.
* **Transmission (dB)** - Ställ in d) dB-dB som orsakas av överföring genom geometri. Ställ in det här skjutreglaget på den lägsta nivån för att inaktivera överföringen. Akustiken spatialiserar det ursprungliga torra ljudet som anländer runt scengeometri (portaling). Överföring ger en extra torr ankomst som är spatialized i siktlinjen riktning. Observera att även källens avståndsdämpningskurva används.

### <a name="adjust-reverberation"></a>Justera efterklang
* **Våthet (dB)** - Justerar reverb-effekten, i dB, beroende på avstånd från källan. Positiva värden gör ett ljud mer efterklang, medan negativa värden gör ett ljud mer torrt. Klicka på kurvkontrollen (den gröna linjen) för att visa kurvredigeraren. Ändra kurvan genom att vänsterklicka för att lägga till punkter och dra dessa punkter för att bilda den funktion du vill använda. X-axeln är avstånd från källan och y-axeln är efterklang justering i dB. Mer information om redigeringskurvor finns i den här [enhetshandboken](https://docs.unity3d.com/Manual/EditingCurves.html). Om du vill återställa kurvan till standard högerklickar du på **Wetness** och väljer **Återställ**.
* **Förfallstidsskalan** - Justerar en multiplikator för sönderfallstiden. Om bakningsresultatet till exempel anger en sönderfallstid på 750 millisekunder, men det här värdet är inställt på 1,5, är sönderfallstiden som tillämpas på källan 1 125 millisekunder.
* **Utomhus -** En additiv justering på akustiksystemets uppskattning av hur "utomhus" efterklang på en källa ska låta. Om du ställer in det här värdet på 1 låter en källa alltid helt utomhus, medan du ställer in den på -1 kommer att göra ett källljud helt inomhus.

Genom att koppla **acousticsAdjustExperimental-skriptet** till en källa kan ytterligare experimentella justeringsparametrar för den källan aktiveras. Om du vill bifoga skriptet klickar du på **Lägg till komponent** längst ned på panelen **Inspektör** och navigerar till **Skript > Akustik justera experimentell .** Det finns för närvarande en experimentell kontroll:

![Skärmbild av Unity AcousticsAdjustExperimental-skript](media/acoustics-adjust-experimental.png)

* **Perceptuell distansförskjutning** - Applicera en exponentiell skevhet på det avstånd som används för att beräkna förhållandet mellan torrt och vått. Akustiksystemet beräknar våta nivåer i hela utrymmet, som varierar smidigt med avstånd och ger perceptuella avståndssignaler. Warping värden större än 1 överdriva denna effekt genom att öka avstånd-relaterade efterklang nivåer, vilket gör ljudet "avlägsen". Warping värden mindre än 1 gör avstånd-baserade efterklang förändras mer subtila, vilket gör ljudet mer "närvarande".

## <a name="design-acoustics-for-all-sources"></a>Designakustik för alla källor
Om du vill justera parametrarna för alla källor klickar du på kanalremsan i Unitys **ljudmixer**och justerar parametrarna på effekten **Project Acoustics Mixer.**

![Skärmbild av anpassningspanelen För Anpassning av Project Acoustics Unity Mixer](media/mixer-parameters.png)

* **Våthet Justera** - Justerar reverb-effekten, i dB, över alla källor i scenen baserat på källlyssnare avstånd. Positiva värden gör ett ljud mer efterklang, medan negativa värden gör ett ljud mer torrt.
* **RT60 Scale** - Multiplikativ skalär för reverb tid.
* **Använd Panorering** - Styr om ljudet matas ut som binaural (0) eller panorering med flera kanaler (1). Alla värden förutom 1 anger binaural. Binaural utgång är spatialized med HRTFs för användning med hörlurar och flerkanalig utgång är spatialized med VBAP för användning med flerkanaliga surroundhögtalare system. Om du använder paner med flera kanaler måste du välja det högtalarläge som matchar enhetsinställningarna, som finns under **Ljud i Project Settings** > **.**

## <a name="check-proper-sound-source-placement"></a>Kontrollera korrekt ljudkälla placering
Ljudkällor placerade inuti ockuperade voxels kommer inte att få akustisk behandling. Eftersom voxels sträcker sig förbi den synliga scengeometrin är det möjligt att placera en källa inuti en voxel medan den verkar okluserad av visuell geometri. Du kan visa Project Acoustics voxels genom att växla voxel rutnät kryssrutan i **Gizmos** menyn, längst upp till höger i **scenvyn.**

![Skärmbild av Unity Gizmos-menyn](media/gizmos-menu.png)  

Voxel-displayen kan också hjälpa till att avgöra om visuella komponenter i spelet har en transformering tillämpad på dem. Om så är fallet, tillämpa samma transformering på GameObject som är värd för **Acoustics Manager**.

### <a name="bake-time-vs-run-time-voxels"></a>Baka tid kontra körtid voxels
Det är möjligt att visa voxels i redigeringsfönstret vid speldesign tid och i spelfönstret vid körning. Storleken på voxels är olika i dessa vyer. Detta beror på att akustiken runtime interpolation använder en finare voxel rutnät för smidigare interpolation resultat. Placering av ljudkällan bör verifieras med hjälp av runtime voxels.

Designtid voxels:

![Skärmbild av Project Acoustics voxels under designtid](media/voxels-design-time.png)

Runtime voxels:

![Skärmbild av Project Acoustics voxels under körning](media/voxels-runtime.png)

## <a name="next-steps"></a>Nästa steg
* Utforska fallstudier som lyfter fram begreppen bakom [designprocessen](design-process.md)

