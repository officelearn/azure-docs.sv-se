---
title: Snabbstart för projektakustik med Unreal
titlesuffix: Azure Cognitive Services
description: Använd exempelinnehåll för att experimentera med designkontroller för Projektakustik i Unreal och Wwise och distribuera till Windows-skrivbordet.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: d3afcded894f72626a4f24bcbe85c34ac1329c29
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72242922"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Projekt Akustik Unreal / Wwise snabbstart
I den här snabbstarten experimenterar du med designkontroller för Projektakustik med hjälp av exempelinnehåll för Unreal Engine och Wwise.

Programvarukrav för användning av exempelinnehållet:
* [Overklig motor](https://www.unrealengine.com/) 4,22
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2019.1.2

## <a name="download-the-sample-package"></a>Ladda ner exempelpaketet
Ladda ner [exempelpaketet Project Acoustics Unreal och Wwise](https://www.microsoft.com/download/details.aspx?id=58090). Provförpackningen innehåller:
- Overkligt motorprojekt
- Wwise-projektet för Unreal-projektet
- Projekt Akustik Wwise plug-in

## <a name="set-up-the-project-acoustics-sample-project"></a>Konfigurera exempelprojektet Projekt Akustik
Installera först plug-in-programmet Project Acoustics i Wwise. Distribuera sedan Wwise-binärfilerna till Unreal-projektet. Justera sedan Wwise Unreal-plugin-programmet för att stödja Projektakustik.

### <a name="install-the-project-acoustics-wwise-plug-in"></a>Installera plug-in-programmet Project Acoustics Wwise
Öppna Wwise Launcher. På fliken **Plugins** under **Installera nya plugin-program**väljer du **Lägg till från katalogen**. Välj katalogen *AcousticsWwisePlugin\ProjectAcoustics* som ingick i paketet som du hämtade.

![Alternativet att installera Wwise-plugin-programmet i Wwise Launcher](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>Lägg till Wwise-binärfiler i exempelprojektet Project Acoustics Unreal
1. Välj fliken **Unreal Engine** i Wwise Launcher. 
1. Välj menyn "hamburgare" (ikon) **bredvid Senaste unreal engine-projekt**och välj sedan **Bläddra efter projekt**. Öppna exempelfilen Unreal project *.uproject* i paketet *AcousticsSample\AcousticsGame\AcousticsGame.uproject*.

   ![Fliken Overkligt i Wwise Launcher](media/wwise-unreal-tab.png)

3. Bredvid exempelprojektet Project Acoustics väljer du **Integrera Wwise i Project**.

   ![Wwise Launcher visar akustikspelet Unreal-projektet med alternativet Integrera markerat.](media/wwise-acoustics-game-project.png)

### <a name="extend-wwise-unreal-plug-in-functionality"></a>Utöka Wwise Unreal plug-in-funktioner
Projektet Akustik Unreal plug-in behöver ytterligare beteende exponeras från Wwise Unreal plug-in API. Kör batchfilen som medföljde plugin-programmet Project Acoustics Unreal för att automatisera dessa ändringar.
* Inuti *AcousticsGame\Plugins\ProjectAcoustics\Resurser*, kör *PatchWwise.bat*.

    ![Skriptet för att korrigera Wwise-projektet markerat i ett Utforskarfönster](media/patch-wwise-script.png)

* Om du inte har DirectX SDK installerat: Beroende på vilken version av Wwise du använder, kan `DXSDK_DIR` du behöva kommentera den linje som finns i *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs*:

    ![Koden redaktör som visar "DXSDK" kommenterade ut](media/directx-sdk-comment.png)

* Om du kompilerar med Visual Studio 2019: För att komma runt ett länkfel med Wwise ändrar du standardvärdet *VSVersion* i *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* till **vc150**:

    ![Kodredigeraren med VSVersion har ändrats till "vc150"](media/vsversion-comment.png)

### <a name="open-the-unreal-project"></a>Öppna unrealprojektet 
När du öppnar Unreal-projektet uppmanas du att återskapa moduler. Välj **Ja**.

Om det går inte att öppna projektet på grund av byggfel kontrollerar du att du har installerat plugin-programmet Project Acoustics Wwise till samma version av Wwise som användes i exempelprojektet Project Acoustics.

Om du använder en version av [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) tidigare än version 2019.1 kan du inte generera ljudbanker med hjälp av exempelprojektet Project Acoustics. Du måste integrera Wwise version 2019.1 i exempelprojektet.

## <a name="experiment-with-project-acoustics-design-controls"></a>Experimentera med designkontroller för projektakustik
Lyssna på hur scenen låter genom att välja uppspelningsknappen i Unreal-redigeraren. Använd knapparna W, A, S och D och musen för att flytta runt. Om du vill visa kortkommandon för ytterligare kontroller väljer du F1.

Följande information beskriver några designaktiviteter att prova.

### <a name="modify-occlusion-and-transmission"></a>Ändra ocklusion och överföring
Det finns designkontroller per källa för Project Acoustics på varje Unreal-ljudskådespelare.

![Designkontrollerna för Overklig editorakustik](media/demo-scene-sound-source-design-controls.png)

Om **Ocklusionsmultiplikatorn** är större än 1 (standardvärdet är 1) överdrivs ocklusionen. En inställning på mindre än 1 gör ocklusionseffekten mer subtil.

Om du vill aktivera växellåda flyttar du skjutreglaget **Transmission (dB)** från den lägsta nivån.

### <a name="modify-wetness-for-a-source"></a>Ändra väta för en källa
Om du vill ändra hur snabbt vätan förändras med avstånd använder du **Perceptuell distansförskjutning**. Project Acoustics beräknar våta nivåer i hela utrymmet genom simulering. Nivåerna varierar smidigt med avstånd och ger perceptuella avståndssignaler. För att överdriva denna effekt, öka avståndsrelaterade våtnivåer för att öka avståndsförskjutningen. Warping värden på mindre än 1 gör avstånd-baserade efterklang ändras mer subtila. Du kan också göra finare justeringar av denna effekt genom inställningen **Wetness (dB).**

Justera Förfallstiden i hela utrymmet genom att justera **Förfallstidsskalan**. Tänk dig ett fall där simuleringsresultatet är en sönderfallstid på 1,5 sekunder. Om du ställer in **förfallstidsskalan** till 2 resulterar det i en sönderfallstid på 3 sekunder som tillämpas på källan.

### <a name="modify-distance-based-attenuation"></a>Ändra avståndsbaserad dämpning
Projektet Akustik Wwise mixer plug-in avseender per källa avstånd-baserad dämpning som är inbyggd i Wwise. Om du ändrar kurvan ändras torrbanasnivån. Plug-in-projektet akustik justerar våtnivån för att bibehålla den våta/torra blandning som anges i simulerings- och konstruktionskontrollerna.

![Wwise dämpning kurva panel som visar dämpning går till 0 före simuleringsgränsen](media/demo-sounds-attenuation.png)

Project Acoustics beräknar i en "simuleringsregion"-ruta som är centrerad kring varje simulerad spelarplats. Akustiktillgångarna i provpaketet bakades med en simuleringsregionradie på 45 meter. Dämpningar var utformade för att falla till 0 innan 45 meter. Även om denna falloff är inte ett strikt krav, bär det förbehållet att endast geometri inom 45 meter från lyssnaren kommer occlude ljud.

## <a name="next-steps"></a>Nästa steg
* [Integrera plug-in-programmet Project Acoustics](unreal-integration.md) i ditt Unreal-projekt.
* [Skapa ett Azure-konto](create-azure-account.md) för dina egna bakar.
