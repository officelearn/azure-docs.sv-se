---
title: Snabb start för Project-ljud med enhets enhet
titlesuffix: Azure Cognitive Services
description: Använd exempel innehåll för att experimentera med projektets design kontroller i enhet och distribuera till Windows-skrivbordet.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fabdd221ef99414eae0156babbd76dedb1f0745d
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243021"
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
1. I Unity går du till **till gångar** > **import paket** > **anpassade paketet**.

    ![Unity import Package Options](media/import-package.png)  

1. Välj **ProjectAcoustics. unitypackage**.

1. Välj knappen **Importera** för att integrera Unity-paketet i projektet.  
  
    ![Dialog rutan Unity import Package](media/import-dialog.png)  

Om du vill importera paketet till ett befintligt projekt, se [unions integrering](unity-integration.md) för ytterligare steg och anmärkningar.

>[!NOTE]
>Flera fel meddelanden visas i konsol loggen när importen är klar. Fortsätt till nästa steg och starta om Unity.

## <a name="restart-unity"></a>Omstarts enhet
Bageri delen av akustiskt Toolkit kräver .NET 4. *x* skript körnings version. Paket importen uppdaterar inställningarna för enhets spelaren. Omstarts-Unit för att den här inställningen ska börja gälla. Kontrol lera att inställningen har gällt genom att öppna **Player** -inställningarna:

![Menyn Unity Project Settings](media/player-settings.png)  

![Inställnings panelen för Unit Player med .NET 4. x valt](media/net45.png)  

>[!NOTE]
>Den här skärm bilden hämtades från Unity 2018. *x*. Bilden kan skilja sig i nyare versioner av Uniting.

## <a name="open-the-project-acoustics-bake-window"></a>Öppna fönstret projekt akustiskt i bak-fönstret
I Unity väljer du **akustiskt** i menyn **fönster** .

![Enhets redigeraren med akustiskt alternativ markerat på Fönster-menyn](media/window-acoustics.png)

Ett flytande **akustiskt** fönster öppnas. I det här fönstret anger du egenskaperna för akustisk simulering.

![Unity-redigeraren med akustiskt fönster öppen](media/unity-editor-plugin-window.png)  

## <a name="experiment-with-the-design-controls"></a>Experimentera med design kontrollerna
Öppna exempel scenen i *ProjectAcousticsSample* -mappen och välj knappen Spela upp i Unity-redigeraren. Använd W-, A-, S-och D-nycklarna och musen för att flytta runt. Om du vill jämföra hur scenen låter med och utan akustisker väljer du R-tangenten tills överlägget blir röd och visar "Akustisker: inaktive rad". Om du vill se kortkommandon för fler kontroller väljer du F1. Du kan också högerklicka för att välja en åtgärd och sedan vänsterklicka på den åtgärden.

*AcousticsAdjust* -skriptet är kopplat till ljud källorna i exempel scenen. Det aktiverar design parametrarna per källa.

![Unity AcousticsAdjust-skriptet](media/acoustics-adjust.png)

I följande avsnitt beskrivs några av de effekter som du kan skapa med hjälp av de tillgängliga kontrollerna. Detaljerad information om varje kontroll finns i [själv studie kursen om projekt akustiska](unity-workflow.md)enhets design.

### <a name="modify-distance-based-attenuation"></a>Ändra avstånds beroende dämpning
Den digitala signal bearbetningen i **Project Akustiske** Unit Spatializer-plugin-programmet respekterar den avstånds dämpning per källa som är inbyggd i Unity-redigeraren. Kontrollerna för avstånds dämpare finns i komponenten **ljud källa** , som finns på panelen **kontrollant** i ljud källor under **Inställningar för 3D-ljud**:

![Panelen alternativ för uppsvagning av enhets avstånd](media/distance-attenuation.png)

Projekt akustiskt beräknas i en "simulerings region"-ruta som är centrerad runt Player-platsen. De akustiska till gångarna i exempel paketet var bakade i en simulerings regions storlek på 45 meter runt spelaren. Därför bör ljud dämpningen utformas för att falla 0 vid cirka 45 m.

### <a name="modify-occlusion-and-transmission"></a>Ändra ocklusion och överföring
* Om **ocklusion** multiplikator är större än 1 (Standardvärdet är 1) är ocklusion exaggerated. Om du vill göra ocklusion mer diskret anger du den till mindre än 1.

* Om du vill aktivera överföring via vägg flyttar du skjutreglaget för **överföring (dB)** bort från den lägsta inställningen.

### <a name="modify-wetness-for-a-source"></a>Ändra wetness för en källa
* Om du vill ändra hur snabbt wetness ändringar med avstånd använder du **perceptuellens avstånds tänjning**. Med hjälp av simuleringen beräknar Project-akustiskt våta nivåer, vilket ger perceptuell avstånds ikoner och varierar smidigt med avstånd. Genom att öka avstånds förvrängningen exaggerates du den här påverkan genom att öka avstånds våta nivåer. Om du tänjer värden på mindre än 1 blir den avstånds Reverberation mer diskret.

   Ändra inställningen för **wetness (dB)** om du vill göra bättre justeringar av den här ändringen.

* Om du vill öka minsknings tiden i hela utrymmet justerar du **tids skalan för minskning**. Om simulerings resultatet för en viss plats paret för en viss källa är en minsknings tid på 1,5 sekunder och **minsknings tiden** är inställd på 2, är den avgångs tid som tillämpas på källan 3 sekunder.

## <a name="next-steps"></a>Nästa steg
* Läs mer om de enhets [design kontroller som är framgångs](unity-workflow.md)kraft.
* Lär dig mer om begreppen bakom [design processen](design-process.md).
* [Skapa ett Azure-konto](create-azure-account.md) för att utforska för-och bak-processerna.
