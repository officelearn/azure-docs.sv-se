---
title: Snabb start för Project-ljud med enhets enhet
titlesuffix: Azure Cognitive Services
description: Med hjälp av exempel innehåll kan du experimentera med projektets design kontroller i Unity och distribuera dem till Windows-skrivbordet.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 0ea020ca76381a4ae5d6b6e480c94e63f9aa2dab
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933088"
---
# <a name="project-acoustics-unity-quickstart"></a>Snabb start för projekt akustiskt
Använd projekt akustiska exempel innehåll för att kunna experimentera med simulerings design kontroller.

Program varu krav:
* [Unity 2018.2 +](https://unity3d.com) för Windows
* [Innehålls paket för projekt akustiska exempel](https://www.microsoft.com/download/details.aspx?id=57346)

Vad ingår i exempel paketet?
* Unity-scen med geometri, ljud källor och spel kontroller
* Plugin-program för projekt akustiskt 
* Bakade akustiska till gångar för scenen

## <a name="import-the-sample-package"></a>Importera exempel paketet
Importera exempel paketet till ett nytt Unity-projekt. 
* I Unity går du till **till gångar > importera paket > anpassat paket...**

    ![Skärm bild av alternativen för Unity import Package](media/import-package.png)  

* Välj **ProjectAcoustics. unitypackage**

* Klicka på knappen **Importera** för att integrera Unity-paketet i projektet  
  
    ![Skärm bild av dialog rutan Unity import Package](media/import-dialog.png)  

Om du importerar paketet till ett befintligt projekt, se unions [integrering](unity-integration.md) för ytterligare steg och anmärkningar.

>[!NOTE] 
>Flera fel visas i konsol loggen när importen har slutförts.  Fortsätt till nästa steg och starta om Unity.

## <a name="restart-unity"></a>Omstarts enhet
Bageri delen av akustiskt Toolkit kräver .NET 4. x skript körnings version. Paket import uppdaterar inställningarna för enhets spelaren. Omstarts-Unit för att den här inställningen ska börja gälla.

Du kan kontrol lera att den här inställningen träder i funktion genom att öppna **Player-inställningarna**:

![Skärm bild av inställnings panelen i Unit Player](media/player-settings.png)  

![Skärm bild av inställnings panelen för Unit Player med .NET 4,5 valt](media/net45.png)  

>[!NOTE]
>Skärm bilden hämtas från Unity 2018. x. Nyare versioner av Unit kan variera.

## <a name="open-the-project-acoustics-bake-window"></a>Öppna fönstret projekt akustiskt i bak-fönstret
Välj **fönster > ljud** från menyn uppmenyn:

![Skärm bild av meny alternativet Unitbar Editor med akustiskt fönster markerat](media/window-acoustics.png)

Ett nytt flytande fönster med namnet akustisker visas.  I det här fönstret anges egenskaperna för akustisk simulering.

![Skärm bild av enhets redigering med akustiskt fönster öppet](media/unity-editor-plugin-window.png)  

## <a name="experiment-with-design-controls"></a>Experimentera med design kontroller
Öppna exempel scenen i **ProjectAcousticsSample** -mappen och klicka på knappen Spela upp i Unity-redigeraren. Använd W, A, S, D och musen för att flytta runt. Om du vill jämföra hur scenen låter med och utan akustik trycker du på knappen **R** tills överläggstexten blir röd och spelar upp ”Acoustics: Disabled.” (”Akustik: inaktiverad”.) Om du vill se tangentbordsgenvägar för fler kontroller trycker du på **F1**. Kontroller är också användbara genom att högerklicka för att välja vilken åtgärd som ska utföras och sedan klicka på för att utföra åtgärden.

Skriptet **AcousticsAdjust** är kopplat till ljud källorna i exempel scenen, vilket möjliggör design parametrar per källa. 

![Skärm bild av Unit AcousticsAdjust-skriptet](media/acoustics-adjust.png)

Följande utforskar några av de effekter som kan skapas med de angivna kontrollerna. Detaljerad information om varje kontroll finns i [själv studie kursen om projekt akustiska](unity-workflow.md)enhets design.

### <a name="modify-distance-based-attenuation"></a>Ändra avstånds beroende dämpning
Ljud-DSP som tillhandahålls av Spatializer-plugin-programmet för **Project Akustiske** units följer den avstånds dämpning per källa som är inbyggd i Unity-redigeraren. Kontroller för avstånds dämpare finns i komponenten **ljud källa** som finns på panelen **kontrollant** i ljud källor, under **Inställningar för 3D-ljud**:

![Skärm bild av panel alternativ för försvagat avstånd](media/distance-attenuation.png)

Project-Akustisker utför beräkningar i rutan "simulerings region" centrerad runt Player-platsen. Eftersom de akustiska till gångarna i exempel paketet var bakade med en simulerings region storlek på 45m som omger spelaren, ska ljud dämpningen vara utformad för att falla 0 vid cirka 45 m.

### <a name="modify-occlusion-and-transmission"></a>Ändra ocklusion och överföring
* Om **ocklusion** multiplikator är större än 1 (Standardvärdet är 1) blir ocklusion exaggerated. Om du anger den som mindre än 1 blir ocklusion-effekterna mer diskret.

* Om du vill aktivera överföring via vägg flyttar du skjutreglaget för **överföring (dB)** från den lägsta nivån. 

### <a name="modify-wetness-for-a-source"></a>Ändra wetness för en källa
* Om du vill ändra hur snabbt wetness ändringar med avstånd använder du **perceptuellens avstånds tänjning**. **Projekt akustiskt** beräknar de våta nivåerna i hela utrymmet från simuleringen, vilket kan vara smidigt med avstånd och ge perceptuell avstånds ikoner. Genom att öka avstånds förvrängningen exaggerates du den här påverkan genom att öka avstånds våta nivåer. Om du tänjer värden som är mindre än 1 blir det avstånds Reverberation mer diskret. Den här funktionen kan också justeras i detalj rikedom genom att justera **wetness (dB)** .

* Öka minsknings tiden i hela utrymmet genom att justera **tids skalan för minskning**. Om simulerings resultatet för ett visst plats par för en viss plats lyssnar är en minsknings tid på 1,5 s, och **tids skalan för minskning** är inställd på 2, är den minsknings tid som tillämpas på källan 3 s.

## <a name="next-steps"></a>Nästa steg
* Läs fullständig information om enhets design kontroller för enhets [drivna projekt](unity-workflow.md)
* Lär dig mer om koncepten bakom [design processen](design-process.md)
* [Skapa ett Azure-konto](create-azure-account.md) för att utforska för-och bak-processerna

