---
title: Komma igång med projektet Akustik - Cognitive Services
description: Den här snabbstartsguiden visar hur du integrerar plugin-programmet i dina Unity-projekt, skapa din scen och tillämpa Akustik till ljud källor.
services: cognitive-services
author: kegodin
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 72a9d476b5e5dfc55c6a28dff7e09a20faab332c
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48246998"
---
# <a name="getting-started-with-project-acoustics"></a>Komma igång med projektet Akustik
Den här snabbstartsguiden visar hur du integrerar plugin-programmet i dina Unity-projekt, skapa din scen och tillämpa Akustik till ljud källor. Den här snabbstarten måste du först skapa en [Azure batch-kontot](create-azure-account.md). Den här guiden förutsätter bekant med Unity.

## <a name="download-the-plugin"></a>Hämta plugin-programmet
Registrera [här](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u) till förhandsversionen Designer.

## <a name="supported-platforms-for-quickstart"></a>Plattformar som stöds för Snabbstart
* [Unity 2018.2 +](http://www.unity3d.com)
  * Kräver att ställa in ditt projekt i **.NET 4.x motsvarande** skript körningsversion 
  * Kräver Windows-baserade Unity-redigeraren

## <a name="import-the-plugin"></a>Importera plugin-programmet
Importera Akustik UnityPackage i projektet. 
* I Unity, går du till **tillgångar > Importera paket > anpassat paket...**

![Importera paket](media/ImportPackage.png)  

* Välj **MicrosoftAcoustics.unitypackage**

Om du importerar plugin-programmet till ett befintligt projekt, ditt projekt kan redan ha en **mcs.rsp** filen i projektroten, som anger alternativ för att C#-kompilatorn. Du måste slå samman innehållet i filen med den mcs.rsp-fil som ingår i projektet Akustik plugin-programmet.

## <a name="enable-the-plugin"></a>Aktivera plugin-programmet
Ändamålet delen av verktyget Akustik kräver .NET 4.x scripting runtime-versionen. Paketimport uppdaterar dina Unity player-inställningar. Starta om Unity för den här inställningen ska börja gälla.

![Player-inställningar](media/PlayerSettings.png)

![.NET 4.5](media/Net45.png)

## <a name="create-a-navigation-mesh"></a>Skapa en navigering nät
Använda standarden [Unity arbetsflöde](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html) att skapa ett navigering nät för ditt projekt. Information om hur du använder egna navigering nät finns i den [ändamålet Användargränssnittet igenom](bake-ui-walkthrough.md).

## <a name="mark-static-meshes-for-acoustics"></a>Markera statiska nät för Akustik
Ta fram en Akustik fönstret med hjälp av **Fönster > Akustik** i Unity. Det här fönstret dockas bredvid Inspector.

![Öppna Akustik fönster](media/WindowAcoustics.png)

Avmarkera alla markerade objekt i Unitys hierarki-fönstret. I Akustik **objekt** fliken klickar du på kryssrutan ”Akustik geometri” om du vill markera alla nät och terrains på scenen som Akustik geometri.

På den **material** fliken, tilldela de akustiska material till material som används i din scen. Den **standard** material har absorption motsvarar konkret. Mer information om hur du anger egna material-egenskaper finns i den [design processen sidan](design-process.md).

![Material fliken](media/MaterialsTab.png)

## <a name="preview-the-probes"></a>Förhandsgranska avsökningar
På den **avsökningar** fliken **Calculate**. Den här beräkningen kan ta några minuter beroende på scen storlek. När beräkningen är klar visas flytande områden i vyn scen som markerar platser för Akustik simulering, som kallas ”avsökning poäng”. Om du får nära nog till ett objekt i fönstret scen kan se du också scen voxelization. Grön voxels ska rikta in objekt som du markerat som geometri. Avsökningen poäng och voxel visar kan växlas på menyn Gizmos uppe till höger i scen vyn.

![GizmosPreview](media/BakePreviewWithGizmos.png)

## <a name="bake-the-scene"></a>Skapa scenen
I den **skapa** fliken, ange dina autentiseringsuppgifter för Azure och på **skapa**. Om du inte har ett Batch-konto kan du läsa [den här genomgången för våra rekommenderade kontokonfiguration](create-azure-account.md).
När ändamålet är klar visas filen automatiskt laddas ned till den **tillgångar/AcousticsData** katalogen i projektet.

## <a name="set-up-audio-runtime-dsp"></a>Ställ in ljud runtime DSP
Vi bädda in ljud runtime DSP för Akustik i Unity's spatializer framework och integrera det med HRTF-baserade spatialization. Om du vill aktivera Akustik bearbetning, växla till den **Microsoft Acoustics** spatializer genom att gå till **Redigera > Inställningar > ljud**, och välj **Microsoft Acoustics** som den **Spatializer plugin-programmet** för ditt projekt. Kontrollera också att den **DSP-buffertstorleken** är inställd på bästa möjliga prestanda.

![Projektinställningar](media/ProjectSettings.png)  

![Projektet Akustik Spatializer](media/ChooseSpatializer.png)

Öppna ljud Mixer (**Fönster > ljud Mixer**). Kontrollera att du har minst en Mixer med en grupp. Om du inte har det, klickar på knappen ”+” till höger om **blandare**. Högerklicka längst ned på kanal-remsans i avsnittet effekter och lägga till den **Microsoft Akustik Mixer** effekt. Observera att endast en projekt Akustik Mixer stöds i taget.

![Ljud Mixer](media/AudioMixer.png)

## <a name="set-up-the-acoustics-lookup-table"></a>Konfigurera Akustik uppslagstabell
Dra och släpp den **Microsoft Acoustics** prefab från projektpanelen i din scen:

![Akustik Prefab](media/AcousticsPrefab.png)

Klicka på den **ProjectAcoustics** spel objektet och gå till dess inspector panel. Ange platsen för ändamålet resultatet (. FÖRBERED-fil i **tillgångar/AcousticsData**) genom att dra och släppa den till skriptet Akustik Manager eller genom att klicka på knappen cirkel bredvid textrutan.

![Akustik Manager](media/AcousticsManager.png)  

## <a name="apply-acoustics-to-sound-sources"></a>Tillämpa Akustik till ljud källor
Skapa en ljudkälla. Klicka på kryssrutan längst ned på panelen för den AudioSource granska där det står **Spatialize**. Se till att **Spatial Blend** är inställd på fullständig 3D.  

![Ljudkälla](media/AudioSource.png)

## <a name="apply-post-bake-design"></a>Tillämpa efter ändamålet design
Du kan koppla skriptet **AcousticsDesign** till en bra källa på scenen att aktivera ytterligare design källparametrar, genom att klicka på **Lägg till komponent** och välja **skript > Akustik Design**:

![Käll-anpassning](media/AcousticsDesign.png)

Det finns också parametrar på den **Microsoft Akustik Mixer**. Läs mer om efter ändamålet design, [utforma parametrar](design-process.md).

## <a name="next-steps"></a>Nästa steg
* Prova den [exempel scen](sample-walkthrough.md)
* Lär dig mer om den fullständiga uppsättningen [skapa funktioner](bake-ui-walkthrough.md)
* Utforska mer detaljerad [utforma parametrar](design-process.md)

