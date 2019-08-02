---
title: Snabb start för projekt med Unreal
titlesuffix: Azure Cognitive Services
description: Med hjälp av exempel innehåll kan du experimentera med projekts akustiska design kontroller i Unreal och Wwise och distribuera till Windows-skrivbordet.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: kegodin
ROBOTS: NOINDEX
ms.openlocfilehash: 06023b2758d09fe8ebe7c1301ef1a03d9c54aa41
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704774"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Snabb start för projekt Akustisker Unreal/Wwise
I den här snabb starten kommer du att experimentera med projekt akustiska design kontroller som använder tillhandahållet exempel innehåll för Unreal-motorn och Wwise.

Program varu krav:
* [Unreal-motor](https://www.unrealengine.com/) 4,21
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) -2018.1.6

## <a name="download-the-sample-package"></a>Ladda ned exempel paketet
Hämta [exempel paketet Project Akustiske Unreal + Wwise](https://www.microsoft.com/download/details.aspx?id=58090). Exempel paketet innehåller ett Unreal-motorprogram, Wwise-projektet för det Unreal-projektet och projektet akustiska Wwise-plugin-programmet.

## <a name="set-up-the-project-acoustics-sample-project"></a>Konfigurera projektets akustiska exempel projekt
Om du vill ställa in projekt akustiskt Unreal/Wwise-exempelprojektet måste du först installera plugin-programmet för projekt i Wwise. Distribuera sedan Wwise-binärfilerna till Unreal-projektet och justera Wwises Unreal-plugin-program för att stödja projekt akustiska.

### <a name="install-the-project-acoustics-wwise-plugin"></a>Installera plugin-programmet för Project Akustisker Wwise
Öppna Wwise start och välj **Lägg till från katalog**under **installera nya plugin**-program på fliken **plugin** -program. Välj den `AcousticsWwisePlugin\ProjectAcoustics` katalog som ingår i paketet som du laddade ned.

![Skärm bild av Wwise Launcher som visar alternativet Installera Wwise plugin-program](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>Lägga till Wwise-binärfiler i projekt Akustiske Unreal-exempelprojektet
Från Wwise Launcher klickar du på fliken **Unreal motor** och klickar sedan på Hamburger-menyn bredvid de **senaste Unreal-motorns projekt** och väljer **Bläddra efter projekt**. Öppna exempel projekt `.uproject` filen Unreal i paketet `AcousticsSample\AcousticsGame\AcousticsGame.uproject`.

![Skärm bild av Wwise Launcher Unreal-fliken](media/wwise-unreal-tab.png)

Klicka sedan på **integrera Wwise i projekt**bredvid projektets akustiska exempel projekt.

![Skärm bild av Wwise Launcher som visar akustiskt spel Unreal-projekt](media/wwise-acoustics-game-project.png)

### <a name="extend-wwises-unreal-plugin-functionality"></a>Utöka Wwise-funktioner för Unreal-plugin
Unreal-plugin-programmet för Project Akustiske kräver att ytterligare beteenden exponeras från Wwise Unreal plugin API. Kör batch-filen som medföljer plugin-programmet för Project Akustiske Unreal för att automatisera följande ändringar:
* I `AcousticsGame\Plugins\ProjectAcoustics\Resources` kör`PatchWwise.bat`du.

    ![Skärm bild av Windows Explorer-fönster som visar skript för att korrigera Wwise-projekt](media/patch-wwise-script.png)

* Om du inte har installerat DirectX SDK, beroende på vilken version av Wwise du använder, kan du behöva kommentera ut raden som innehåller `DXSDK_DIR` i: `AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`

    ![Skärm bild av kod redigeraren som visar DXSDK kommenterad](media/directx-sdk-comment.png)

### <a name="open-the-unreal-project"></a>Öppna Unreal-projektet. 
Du uppmanas att återskapa moduler. Klicka på Ja.

>Om det inte går att öppna projektet vid build-fel, kontrollerar du att du har installerat plugin-programmet för Project Akustiske Wwise till samma version av Wwise som används i projektets akustiska exempel projekt.

>Om du inte använder [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1.6 måste du återskapa ljud banker innan ljudet kan spelas upp i exempelprojektet.

## <a name="experiment-with-project-acoustics-design-controls"></a>Experimentera med design kontroller för projekt akustiskt
Lyssna på hur scenen låter genom att klicka på uppspelnings knappen i Unreal-redigeraren. Använd W, A, S, D och musen på Skriv bordet för att flytta runt. Om du vill se tangentbordsgenvägar för fler kontroller trycker du på **F1**. Här är några design aktiviteter att prova:

### <a name="modify-occlusion-and-transmission"></a>Ändra ocklusion och överföring
Det finns projekt med projekt akustiskt per käll projekt på varje Unreal-ljud skådespelare:

![Skärm bild av Unreal-redigeraren akustiska design kontroller](media/demo-scene-sound-source-design-controls.png)

Om **ocklusion** multiplikator är större än 1 (Standardvärdet är 1) blir ocklusion exaggerated. Om du anger den som mindre än 1 blir ocklusion-effekterna mer diskret.

Om du vill aktivera överföring via vägg flyttar du skjutreglaget för **överföring (dB)** från den lägsta nivån. 

### <a name="modify-wetness-for-a-source"></a>Ändra wetness för en källa
Om du vill ändra hur snabbt wetness ändringar med avstånd använder du **perceptuellens avstånds tänjning**. Projekt akustiskt beräknar de våta nivåerna i hela utrymmet från simuleringen, vilket kan vara smidigt med avstånd och ge perceptuell avstånds ikoner. Genom att öka avstånds förvrängningen exaggerates du den här påverkan genom att öka avstånds våta nivåer. Om du tänjer värden som är mindre än 1 blir det avstånds Reverberation mer diskret. Den här funktionen kan också justeras i detalj rikedom genom att justera **wetness (dB)** .

Öka minsknings tiden i hela utrymmet genom att justera **tids skalan för minskning**. Tänk på det fall där simulerings resultatet är en minsknings tid på 1,5 s. Om du ställer in **minsknings tiden** till 2 resulterar det i en minsknings tid som tillämpas på källan till 3 s.

### <a name="modify-distance-based-attenuation"></a>Ändra avstånds beroende dämpning
Wwise mixer-plugin-programmet för Project akustisker respekterar den avstånds dämpning per källa som är inbyggd i Wwise. Om du ändrar kurvan så ändras den torra Sök vägs nivån. Plugin-programmet för Project-akustiskt justerar den våta nivån för att bibehålla den våta torr mixen som anges av simulerings-och design kontroller.

![Skärm bild av Wwise-dämpande kurv panel med dämpning till noll före simulerings gränser](media/demo-sounds-attenuation.png)

Projekt akustiska data i en "simulerings region" centreras runt varje simulerad uppspelnings plats. De akustiska till gångarna i exempel paketet var bakade med en radie på 45 m för simulerings region och de dämparna har utformats för att falla 0 före 45 m. Även om den här utfallet inte är ett strikt krav, medför det att endast geometri inom 45 m av lyssnaren kommer att occlude ljud.

## <a name="next-steps"></a>Nästa steg
* Integrera plugin-programmet [för projekt](unreal-integration.md) i ditt Unreal-projekt
* [Skapa ett Azure-konto](create-azure-account.md) för dina egna bakes


