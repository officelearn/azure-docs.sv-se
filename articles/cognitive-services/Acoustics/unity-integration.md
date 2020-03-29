---
title: Integrering och distribution av Project Acoustics Unity
titlesuffix: Azure Cognitive Services
description: I den här artikeln beskrivs hur du integrerar plugin-programmet Project Acoustics Unity i Unity-projektet.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: a8ddb0e4ca2ee4396a25a70c8b60b653aebb72d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "72243005"
---
# <a name="project-acoustics-unity-integration"></a>Project Acoustics Unity integration
I den här artikeln beskrivs hur du integrerar plugin-programmet Project Acoustics Unity i Unity-projektet.

Programvarukrav:
* [Unity 2018.2+](https://unity3d.com) för Windows
* [Project Acoustics Unity-paketet](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plug-in"></a>Importera plugin-programmet
1. Importera akustiken UnityPackage till ditt projekt. 
 I Unity går du till**Anpassat paket**för**importpaket** >  **för tillgångar** > .

    ![Menyn Enhetsimportpaket](media/import-package.png)  

1. Välj **ProjectAcoustics.unitypackage**.

1. Välj knappen **Importera** om du vill integrera Unity-paketet i projektet.

    ![Dialogrutan Enhetsimportpaket](media/import-dialog.png)  

Om du importerar plugin-programmet till ett befintligt projekt kanske projektet redan har en *mcs.rsp-fil* i projektroten. Den här filen anger alternativ för C#-kompilatorn. Slå ihop innehållet i filen med filen mcs.rsp som medföljer plugin-programmet Project Acoustics.

## <a name="enable-the-plug-in"></a>Aktivera plugin-programmet
Den baka delen av akustik verktygslåda kräver .NET 4. *x* skriptkörningsversion. Paketimport uppdaterar inställningarna för Unity-spelare. Starta om Unity för att den här inställningen ska börja gälla.

![Panelen Inställningar för Enhetsspelare](media/player-settings.png)

![Panelen Inställningar för Enhetsspelare med .NET 4.5 markerat](media/net45.png)

## <a name="set-up-audio-dsp"></a>Konfigurera ljud-DSP
Project Acoustics innehåller DSP-ljudkörning som integreras i Unity-ramverket för ljudmotorns spatializer. Den omfattar både HRTF-baserad och panorering spatialization. Om du vill aktivera DSP-programmet Projekt akustik går du till **Redigera** > **ljudinställningar** > **för** projektinställningar för att öppna inställningarna för Unity-ljud. Välj **Projektakustik** som **Spatializer Plugin** för ditt projekt. Kontrollera att **DSP Buffer Size** är inställt på Bästa *prestanda*.

![Menyn Inställningar för Enhetsprojekt](media/project-settings.png)  

![Inställningspanelen Unity Spatializer med Project Acoustics spatializer markerad](media/choose-spatializer.png)

Öppna sedan ljudmixern **(Window** > **Audio Mixer).** Kontrollera att du har minst en mixer, med en grupp. Om du inte har någon **+** väljer du knappen till höger om **Blandare**. Högerklicka på nederkanten av kanalremsan i effektavsnittet och lägg till effekten **Microsoft Acoustics Mixer.** Endast en Projektakustisk mixer stöds åt gången.

![Unity Audio Mixer värd för projektet Akustik mixer](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Aktivera akustik på ljudkällor
Skapa en ljudkälla: Markera kryssrutan **Spatialize** längst ned på ljudkällapanelen. Kontrollera att **Spatial Blend** är inställd på full *3D*.  

![Panelen Ljudkälla för enhet](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Aktivera akustisk design
Anslut *AcousticsAdjust-skriptet* till en ljudkälla i scenen för att aktivera ytterligare källdesignparametrar: Välj **Lägg till komponent** och välj **Skriptakustik** > **justera**.

![Unity AcousticsAdjust-manuset](media/acoustics-adjust.png)

## <a name="next-steps"></a>Nästa steg
* [Baka din scen med Project Acoustics for Unity](unity-baking.md).
* [Skapa ett Azure Batch-konto](create-azure-account.md) för att baka din scen i molnet.
* Utforska [designprocessen Project Acoustics Unity](unity-workflow.md).
