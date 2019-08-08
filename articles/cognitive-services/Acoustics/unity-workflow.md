---
title: Själv studie kurs om Project Akustiske Unit design
titlesuffix: Azure Cognitive Services
description: I den här självstudien beskrivs design arbets flödet för projekt akustiska i Unity.
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
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854269"
---
# <a name="project-acoustics-unity-design-tutorial"></a>Själv studie kurs om Project Akustiske Unit design
I den här självstudien beskrivs design verktygen och arbets flödet för projekt akustiska i Unity.

Krav:
* Unity 2018.2 + för Windows
* En Unity-scen med en bakade-akustisk till gång

I den här självstudien får du en Unity-scen med en bakade akustisk till gång på två sätt:
* [Lägg till projekt akustiskt i ditt Unity-projekt](unity-integration.md)och [hämta sedan ett Azure Batch konto](create-azure-account.md)och sedan din enhets enhets [scen](unity-baking.md)
* Du kan också använda [exempel innehållet i Project Akustiske Unit](unity-quickstart.md).

## <a name="review-design-process-concepts"></a>Granska design process koncept
Projekt akustiska använder vanliga metoder för digital signal bearbetning (DSP) för att bearbeta dina källor och ger dig kontroll över välbekanta akustiska egenskaper, inklusive ocklusion, våt/torr mix och Reverberation slut längd (RT60). Men huvud [projektet akustiskt design process koncept](design-process.md) är att i stället för att ställa in dessa egenskaper direkt, styr du hur simulerings resultaten används för att köra dessa egenskaper. Standardinställningarna för varje kontroll representerar fysiskt exakta akustiska ljud.

## <a name="design-acoustics-for-each-source"></a>Design akustiska ljud för varje källa
Project-Akustisker ger ett antal verksamhetsspecifika akustiska design kontroller. På så sätt kan du styra blandningen i en scen genom att framhäva vissa källor och de som framhäver andra.

### <a name="adjust-distance-based-attenuation"></a>Justera avstånds beroende dämpande
Ljud-DSP som tillhandahålls av Spatializer-plugin-programmet för **Project Akustiske** units följer den avstånds dämpning per källa som är inbyggd i Unity-redigeraren. Kontroller för avstånds dämpare finns i komponenten **ljud källa** som finns på panelen **kontrollant** i ljud källor, under **Inställningar för 3D-ljud**:

![Skärm bild av panel alternativ för försvagat avstånd](media/distance-attenuation.png)

Akustiska data utför beräkningar i rutan "simulerings region" centrerad runt Player-platsen. Om en ljud källa är avlägsen från spelaren, som finns utanför den här simulerings regionen, kommer endast geometrin i rutan att påverka ljud spridningen (till exempel att orsaka ocklusion) som fungerar bra när occluders är i närheten av spelaren. Men om spelaren är i öppet utrymme men occluders är nära den avlägsen ljud källan, kan ljudet bli disoccluded. Vår rekommenderade lösning är att se till att ljud dämpningen faller på 0 vid cirka 45 m, och det vågräta standard avståndet i spelaren till rutans kant.

![Skärm bild av alternativet Unit SpeakerMode option panel](media/speaker-mode.png)

### <a name="adjust-occlusion-and-transmission"></a>Justera ocklusion och överföring
Att bifoga **AcousticsAdjust** -skriptet till en källa möjliggör justerings parametrar för den källan. Om du vill bifoga skriptet klickar du på **Lägg till komponent** längst ned i panelen **Granska** och navigerar till **skript > akustiskt justera**. Skriptet innehåller sex kontroller:

![Skärm bild av Unit AcousticsAdjust-skriptet](media/acoustics-adjust.png)

* **Aktivera akustiska** – styr om akustiska ljud används för den här källan. När alternativet är avmarkerat kommer källan att behållas med HRTFs eller panorering, men det kommer inte att finnas några akustisker. Detta innebär att inga hinder, ocklusion eller dynamiska Reverberation-parametrar, t. ex. nivå och minsknings tid. Reverberation tillämpas fortfarande med en fast nivå och en minsknings tid.
* **Ocklusion** – Använd en multiplikator till den ocklusion DB-nivå som beräknas av akustiskt system. Om den här multiplikatorn är större än 1, kommer ocklusion att exaggerated, medan värden som är mindre än 1 gör ocklusion-effekterna mer diskret och värdet 0 inaktiverar ocklusion.
* **Överföring (dB)** – ange den försvagade (i dB) som orsakas av överföring via geometri. Ange det här skjutreglaget till lägsta nivån för att inaktivera överföringen. Akustiskt spatializes det första torra ljudet som kommer runt scen geometrin (portal). Överföringen ger ytterligare en torr ankomst som är spatial i den detaljerade riktningen. Observera att den försvagade avstånds kurvan för källan också appliceras.

### <a name="adjust-reverberation"></a>Justera Reverberation
* **Wetness (dB)** – justerar reverb-kraften i dB, beroende på avståndet från källan. Positiva värden gör ett ljud mer reverberant, medan negativa värden gör ett ljud mer torrt. Klicka på kurv kontrollen (grön linje) för att Visa kurv redigeraren. Ändra kurvan genom att vänsterklicka för att lägga till punkter och dra dessa punkter till den funktion du vill ha. X-axeln är avstånd från källan och y-axeln är reverb justering i dB. Mer information om hur du redigerar kurvor finns i den här enhets [hand boken](https://docs.unity3d.com/Manual/EditingCurves.html). Om du vill återställa kurvan tillbaka till standard högerklickar du på **wetness** och väljer **Återställ**.
* **Minsknings tids skala** – justerar en multiplikator för minsknings tiden. Om till exempel bakgrunds resultatet anger en minsknings tid på 750 millisekunder, men det här värdet är inställt på 1,5, är minsknings tiden som används för källan 1 125 millisekunder.
* Utdörrer – en tilläggs justering av akustiskt Systems uppskattning av hur "utomhus" Reverberation på en källa ska vara ljud. Om du ställer in det här värdet på 1 blir källan alltid helt utomhus, medan du ställer in det på-1 för att göra ett käll ljud helt inaktive ras.

Att bifoga **AcousticsAdjustExperimental** -skriptet till en källa möjliggör ytterligare experimentella justerings parametrar för den källan. Koppla skriptet genom att klicka på **Lägg till komponent** längst ned i panelen **Granska** och navigera till **skript > akustiskt justera experiment**. Det finns för närvarande en experimentell kontroll:

![Skärm bild av Unit AcousticsAdjustExperimental-skriptet](media/acoustics-adjust-experimental.png)

* **Perceptuell avstånd** -tänjning – Använd en exponentiell tänjning till avståndet som används för att beräkna det torra förhållandet. Akustiskt system beräknar våta nivåer i hela utrymmet, vilket varierar smidigt med avstånd och ger perceptuell avstånds ikoner. Du kan tänja värden som är större än 1 exaggerate genom att öka avstånds relaterade Reverberation-nivåer, vilket gör ljudet "avlägsen". Om du tänjer värden som är mindre än 1 blir de avstånds Reverberation mer diskreta, vilket gör ljudet mer "tillgängligt".

## <a name="design-acoustics-for-all-sources"></a>Design akustiska ljud för alla källor
Om du vill justera parametrar för alla källor, klickar du på kanal remsan i enhetens **ljud mixer**och justerar parametrarna i resultatet av **projektet akustiska mixer** .

![Skärm bild av anpassnings panel för Project-akustiskt enhets mixer](media/mixer-parameters.png)

* **Wetness justera** – justerar reverb-kraften i dB, över alla källor i scenen baserat på källans lyssnar avstånd. Positiva värden gör ett ljud mer reverberant, medan negativa värden gör ett ljud mer torrt.
* **RT60 Scale** – multiplicative skalär för reverb-tid.
* **Använd panorering** – styr om ljudet matas som binaural (0) eller Multichannel-panorering (1). Alla värden förutom 1 indikerar binaural. Binaural-utdata är spatiala med HRTFs för användning med hörlurar och Multichannel-utdata är spatiala med VBAP för användning med multichannel surround högtalar system. Om du använder en Multichannel-panorering, måste du välja det högtalar läge som matchar enhets inställningarna, som finns under **Inställningar** > **ljud**för projekt.

## <a name="check-proper-sound-source-placement"></a>Kontrol lera korrekt placering av ljud källan
Ljud källor som placeras i en upptagen voxels får ingen akustisk behandling. Eftersom voxels sträcker sig över den synliga scen geometrin, är det möjligt att placera en källa inuti en Voxel medan den visas Unoccluded av visuell geometri. Du kan visa projekt akustiska voxels genom att växla kryss rutan Voxel Grid på **Gizmos** -menyn längst upp till höger i vyn **scen** .

![Skärm bild av Unity Gizmos-menyn](media/gizmos-menu.png)  

Voxel-visningen kan också hjälpa dig att avgöra om visuella komponenter i spelet har en transformering som tillämpas. I så fall, använder du samma omvandling till GameObject som är värd för **Akustiske Manager**.

### <a name="bake-time-vs-run-time-voxels"></a>Bak tid jämfört med körnings tid och voxels
Det går att Visa voxels i redigerings fönstret i spel design läge och i spel fönstret vid körning. Storleken på voxels skiljer sig i dessa vyer. Detta beror på att akustiska körnings interpolation använder ett finare Voxel-rutnät för smidiga interpolation-resultat. Ljud källans placering bör verifieras med hjälp av körnings voxels.

Design tid voxels:

![Skärm bild av voxels för Project-akustiskt under design tid](media/voxels-design-time.png)

Runtime-voxels:

![Skärm bild av voxels för Project-akustiskt under kör tid](media/voxels-runtime.png)

## <a name="next-steps"></a>Nästa steg
* Utforska fallstudier som markerar koncepten bakom [design processen](design-process.md)

