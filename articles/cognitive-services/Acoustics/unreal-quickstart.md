---
title: Projektet Akustik Snabbstart med Unreal
titlesuffix: Azure Cognitive Services
description: Med hjälp av exemplen, experimentera med projekt Akustik design kontroller i Unreal och Wwise och distribuera Windows-skrivbordet.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/14/2019
ms.author: kegodin
ms.openlocfilehash: 1314e393d292145ef112e700abf6ab1ef199db7d
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58138189"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Projektet Akustik Unreal/Wwise Snabbstart
I den här snabbstarten ska du experimentera med projekt Akustik design kontroller med angivna exemplen för Unreal Engine och Wwise.

Programvarukrav:
* [Unreal Engine 4.21](https://www.unrealengine.com/)
* [Wwise 2018.1.6](https://www.audiokinetic.com/products/wwise/)

## <a name="download-the-sample-package"></a>Ladda ned exempelpaketet
Ladda ned den [projekt Akustik Unreal + Wwise exempel paketet](http://www.microsoft.com/downloads/details.aspx?FamilyID=f03dff5a-5780-462e-87ef-e6d039d0748d). Exempelpaketet innehåller ett projekt med Unreal Engine, Wwise projektet för Unreal projektet och projekt Akustik Wwise plugin-programmet.

## <a name="set-up-the-project-acoustics-sample-project"></a>Konfigurera projektet Akustik exempelprojektet
Om du vill konfigurera projektet Akustik Unreal/Wwise exempelprojektet måste du först installera plugin-programmet projekt Akustik i Wwise. Sedan distribuerar Wwise binärfiler till Unreal projektet och justera den Wwise Unreal plugin-programmet för att stödja projekt Akustik.

### <a name="install-the-project-acoustics-wwise-plugin"></a>Installera plugin-programmet för projektet Akustik Wwise
Öppna Wwise starta sedan i den **plugin-program** fliken, under **installera nya plugin-program**väljer **Lägg till från katalog**. Välj den `AcousticsWwisePlugin\ProjectAcoustics` katalog som ingick i paketet som du hämtade.

![Installera Wwise plugin-programmet](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>Lägg till Wwise binärfiler till projektet Akustik Unreal exempelprojektet
Från Wwise starta klickar du på den **Unreal Engine** och klicka sedan på hamburgarmenyn bredvid **senaste Unreal Engine projekt** och välj **Bläddra efter projekt**. Öppna exempelprojektet Unreal `.uproject` filen i paketet `AcousticsSample\AcousticsGame\AcousticsGame.uproject`.

![Wwise Unreal fliken](media/wwise-unreal-tab.png)

Bredvid exempelprojektet projekt Akustik Klicka **integrera Wwise i projektet**.

![Wwise Acoustics Game Unreal Project](media/wwise-acoustics-game-project.png)

### <a name="extend-wwises-unreal-plugin-functionality"></a>Utöka funktionerna i Wwise's Unreal plugin-programmet
Projektet Akustik Unreal plugin-programmet kräver ytterligare beteenden exponeras från Wwise Unreal plugin-programmet API. Kör kommandofilen medföljer projekt Akustik Unreal plugin-programmet att automatisera dessa ändringar:
* Inuti `AcousticsGame\Plugins\ProjectAcoustics\Resources`kör `PatchWwise.bat`.

    ![Korrigera Wwise skript](media/patch-wwise-script.png)

* Om du inte har DirectX-SDK är installerat, måste du kommentera ut den rad som innehåller DXSDK_DIR i `AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`

    ![DXSDK kommentera ut](media/directx-sdk-comment.png)

### <a name="open-the-unreal-project"></a>Öppna Unreal projektet. 
Det blir du ombedd att återskapa moduler. Klicka på Ja.

Om det går inte att öppna projektet på versionsfel, kontrollerar du att du har installerat plugin-programmet projekt Akustik Wwise till samma version av Wwise som används i projektet Akustik exempelprojektet.

## <a name="experiment-with-project-acoustics-design-controls"></a>Experimentera med projekt Akustik design kontroller
Lyssna på hur scenen låter genom att klicka på uppspelningsknappen i Unreal redigeraren. På skrivbordet, använda W, A, S, D och musen för att flytta. Om du vill se tangentbordsgenvägar för fler kontroller trycker du på **F1**. Här följer några design aktiviteter att testa:

### <a name="modify-occlusion-and-transmission"></a>Ändra ocklusion och överföring
Det finns per källkod projekt Akustik design kontroller på varje Unreal ljud skådespelare:

![DemoSceneSoundSourceDesignControls](media/demo-scene-sound-source-design-controls.png)

Om den **ocklusion** multiplikatorn är större än 1 (standardvärdet är 1), är spärrat vara överdrivna. Ange den mindre än 1 gör ocklusion i kraft mer diskreta.

Om du vill aktivera via vägg överföring, flytta den **överföring (dB)** skjutreglaget av sin lägsta nivå. 

### <a name="modify-wetness-for-a-source"></a>Ändra wetness för en källa
Du kan ändra hur snabbt wetness ändras med avståndet med den **Perceptuell avståndet tänja**. Projektet Akustik beräknar våt nivåer i hela adressutrymmet från simulering, som kan variera smidigt med avståndet och ger Perceptuell avståndet tips. Öka avståndet otroligt blir detta genom att öka avståndet-relaterade våt nivåer. Kontrollera avståndet-baserade genljudet ändra mer diskreta skev värden är mindre än 1. Detta kan också justeras i mer detaljerad information genom att justera den **Wetness (dB)**.

Öka tid som decay i hela utrymmet genom att justera **Decay skala**. Tänk dig där simulering resultatet är en decay tid på 1,5 s. Ange den **Decay skala** till 2 resulterar i taget decay tillämpas på källan för 3 s.

### <a name="modify-distance-based-attenuation"></a>Ändra avståndet-baserade dämpning
Plugin-programmet för projektet Akustik Wwise mixer värnar om den per källkod avståndet-baserade dämpning inbyggda Wwise. Om du ändrar den här kurvan ändras torr-path-nivå. Plugin-programmet projekt Akustik justerar våt nivån för att underhålla den igång torr kombination som anges av simulering och design.

![DemoSoundsAttenuation](media/demo-sounds-attenuation.png)

Projektet Akustik gör beräkningen i en ”simulering region” inriktade på varje simulerad player-plats. Akustik tillgångar i exempelpaketet har inbyggd med en simulering region radien för 45 m och attenuations har utformats för att växla till 0 innan 45 m. Den här utfall är inte ett strikta krav, innebär det villkor för att endast geometri inom 45 m för lyssnare kommer occlude ljud.

## <a name="next-steps"></a>Nästa steg
* [Integrera projekt Akustik](unreal-integration.md) plugin-programmet i projektet Unreal
* [Skapa ett Azure-konto](create-azure-account.md) för dina egna bakes


