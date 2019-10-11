---
title: Integrering och distribution av projekt akustiska delar
titlesuffix: Azure Cognitive Services
description: Den här artikeln beskriver hur du integrerar plugin-programmet för Project Akustiske Unit i ditt Unity-projekt.
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
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243005"
---
# <a name="project-acoustics-unity-integration"></a>Integrering av projekt akustiskt union
Den här artikeln beskriver hur du integrerar plugin-programmet för Project Akustiske Unit i ditt Unity-projekt.

Program varu krav:
* [Unity 2018.2 +](https://unity3d.com) för Windows
* [Project Akustiske Unity-paket](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plug-in"></a>Importera plugin-programmet
1. Importera de akustiska UnityPackage i ditt projekt. 
 I Unity går du till **till gångar** > **import paket** > **anpassade paketet**.

    ![Menyn Unity import Package](media/import-package.png)  

1. Välj **ProjectAcoustics. unitypackage**.

1. Välj knappen **Importera** för att integrera Unity-paketet i projektet.

    ![Dialog rutan Unity import Package](media/import-dialog.png)  

Om du importerar plugin-programmet till ett befintligt projekt kanske projektet redan har en *MCS. rsp* -fil i projekt roten. Den här filen anger alternativ för C# kompilatorn. Sammanfoga innehållet i filen med MCS. rsp-filen som medföljer plugin-programmet för projekt akustiskt.

## <a name="enable-the-plug-in"></a>Aktivera plugin-programmet
Bageri delen av akustiskt Toolkit kräver .NET 4. *x* skript körnings version. Paket import uppdaterar dina inställningar i Unit Player. Omstarts-Unit för att den här inställningen ska börja gälla.

![Panelen Inställningar i enhets spelaren](media/player-settings.png)

![Panelen Inställningar för enhets spelaren med .NET 4,5 valt](media/net45.png)

## <a name="set-up-audio-dsp"></a>Konfigurera ljud-DSP
Projekt akustiska innehåller ljud körnings-DSP som integreras i Spatializer-ramverket för Unity-ljudmotorn. Den innehåller både HRTF-baserad och panorering av spatialization. Om du vill aktivera projektet akustiskt DSP går du till **redigera** > **projekt inställningar** > -**ljud** för att öppna enhetens ljud inställningar. Välj **projekt akustisker** som **Spatializer-plugin-program** för ditt projekt. Se till att **storleken på DSP-buffertstorleken** har angetts till *bästa prestanda*.

![Menyn Unity Project Settings](media/project-settings.png)  

![Panelen Unit Spatializer Settings med Project akustiska Spatializer valda](media/choose-spatializer.png)

Öppna sedan ljud mixern (**Window** > **ljud mixer**). Se till att du har minst en mixer med en grupp. Om du inte har en sådan, väljer du knappen **+** till höger om **mixers**. Högerklicka på den nedre delen av kanal remsan i avsnittet effekter och Lägg till effekten **Microsoft akustisker mixer** . Endast ett projekt akustiskt mixer stöds i taget.

![Enhetens ljud mixer som är värd för projektet akustiska mixer](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Aktivera akustiska ljud källor
Skapa en ljud Källa: Markera kryss rutan **Spatialize** längst ned i panelen AudioSource kontrollant. Se till att den **spatiala över gången** är inställd på full *3D*.  

![Panelen enhet för ljud källa](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Aktivera akustisk design
Koppla *AcousticsAdjust* -skriptet till en ljud källa i din scen för att aktivera ytterligare käll design parametrar: Välj **Lägg till komponent** och välj **skript** > **akustiska ljud**.

![Unity AcousticsAdjust-skriptet](media/acoustics-adjust.png)

## <a name="next-steps"></a>Nästa steg
* [Bageri din scen med projekt akustiska för Unity](unity-baking.md).
* [Skapa ett Azure Batch konto](create-azure-account.md) för att gå till din scen i molnet.
* Utforska [design processen för Project Akustiske Unit](unity-workflow.md).
