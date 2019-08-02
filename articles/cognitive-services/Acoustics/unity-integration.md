---
title: Integrering och distribution av projekt akustiska delar
titlesuffix: Azure Cognitive Services
description: Den här instruktionen förklarar hur du integrerar plugin-programmet för Project Akustiske Unit i ditt Unity-projekt.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ROBOTS: NOINDEX
ms.openlocfilehash: 1a90f6102d35dc1a3bb97c840f2955b54f35bbad
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706648"
---
# <a name="project-acoustics-unity-integration"></a>Integrering av projekt akustiskt union
Den här instruktionen förklarar hur du integrerar plugin-programmet för Project Akustiske Unit i ditt Unity-projekt.

Program varu krav:
* [Unity 2018.2 +](https://unity3d.com) för Windows
* [Project Akustiske Unity-paket](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plugin"></a>Importera plugin-programmet
Importera de akustiska UnityPackage till projektet. 
* I Unity går du till **till gångar > importera paket > anpassat paket...**

    ![Skärm bild av menyn Unity import Package](media/import-package.png)  

* Välj **ProjectAcoustics. unitypackage**

Om du importerar plugin-programmet till ett befintligt projekt kanske projektet redan har en **MCS. rsp** -fil i projekt roten, som anger alternativ för C# kompilatorn. Du måste slå samman innehållet i filen med MCS. rsp-filen som medföljer plugin-programmet för Project akustiskt.

## <a name="enable-the-plugin"></a>Aktivera plugin-programmet
Bageri delen av akustiskt Toolkit kräver .NET 4. x skript körnings version. Paket import uppdaterar inställningarna för enhets spelaren. Omstarts-Unit för att den här inställningen ska börja gälla.

![Skärm bild av inställnings panelen i Unit Player](media/player-settings.png)

![Skärm bild av inställnings panelen för Unit Player med .NET 4,5 valt](media/net45.png)

## <a name="set-up-audio-dsp"></a>Konfigurera ljud-DSP
Projekt akustiska innehåller ljud körnings-DSP som integreras i Spatializer-ramverket för Unity-ljudmotorn. Den innehåller både HRTF-baserad och panorering av spatialization. Aktivera projektet akustiskt DSP genom att öppna enhets ljud inställningarna med **redigera > projekt inställningar > ljud**och välj sedan **projekt akustiska** som **Spatializer-plugin-program** för projektet. Se till att **storleken på DSP** -buffertstorleken har angetts till bästa prestanda.

![Skärm bild av panelen för enhets projekt inställningar](media/project-settings.png)  

![Skärm bild av enhets panelens Spatializer inställnings panel med projekt akustiska Spatializer valda](media/choose-spatializer.png)

Öppna sedan ljud mixer (**fönster > ljud mixer**). Se till att du har minst en mixer med en grupp. Om du inte gör det klickar du på knappen "+" till höger om **mixers**. Högerklicka på den nedre delen av kanal remsan i avsnittet effekter och Lägg till effekten av **projekt akustiska** effekter. Observera att endast ett projekt akustiskt mixer stöds i taget.

![Skärm bild av enhets ljud mixer som är värd för projekt akustiska mixer](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Aktivera akustiska ljud källor
Skapa en ljud källa. Klicka på kryss rutan längst ned i AudioSources panel som heter **Spatialize**. Se till att **spatial blandning** har angetts till full 3D.  

![Skärm bild av panelen för enhets ljud källa](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Aktivera akustisk design
Koppla skriptet **AcousticsAdjust** till en ljud källa i din scen för att aktivera ytterligare käll design parametrar genom att klicka på **Lägg till komponent** och välja **skript > akustiskt justera**:

![Skärm bild av Unit AcousticsAdjust-skriptet](media/acoustics-adjust.png)

## <a name="next-steps"></a>Nästa steg
* [Bageri din scen med projekt akustiska för Unity](unity-baking.md)
* [Skapa ett Azure Batch konto](create-azure-account.md) för att gå till din scen i molnet
* Utforska [design processen för Project Akustiske Unit](unity-workflow.md).

