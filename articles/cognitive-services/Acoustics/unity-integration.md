---
title: Projektet Akustik Unity-integrering och distribution
titlesuffix: Azure Cognitive Services
description: Den här anvisningen förklarar integrering av projektet Akustik Unity-plugin-programmet i dina Unity-projekt.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 8605fe2ea86f875b5c5fd8ea451a287442dcd51b
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64922494"
---
# <a name="project-acoustics-unity-integration"></a>Projektet Akustik Unity-integrering
Den här anvisningen förklarar integrering av projektet Akustik Unity-plugin-programmet i dina Unity-projekt.

Programvarukrav:
* [Unity 2018.2 +](https://unity3d.com) för Windows
* [Projektet Akustik Unity-paketet](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plugin"></a>Importera plugin-programmet
Importera Akustik UnityPackage i projektet. 
* I Unity, går du till **tillgångar > Importera paket > anpassat paket...**

    ![Skärmbild av Unity Importera paket-menyn](media/import-package.png)  

* Choose **ProjectAcoustics.unitypackage**

Om du importerar plugin-programmet till ett befintligt projekt, ditt projekt kan redan ha en **mcs.rsp** filen i projektroten, som anger alternativ för att C#-kompilatorn. Du måste slå samman innehållet i filen med den mcs.rsp-fil som ingår i projektet Akustik plugin-programmet.

## <a name="enable-the-plugin"></a>Aktivera plugin-programmet
Ändamålet delen av verktyget Akustik kräver .NET 4.x scripting runtime-versionen. Paketimport uppdaterar dina Unity player-inställningar. Starta om Unity för den här inställningen ska börja gälla.

![Skärmbild av Unity Player inställningspanelen](media/player-settings.png)

![Skärmbild av Unity Player inställningspanelen med .NET 4.5 har valts](media/net45.png)

## <a name="set-up-audio-dsp"></a>Ställ in ljud DSP
Projektet Akustik innehåller ljud runtime DSP som integreras i Unity ljud motorn spatializer framework. Den innehåller både HRTF- och panoreringsverktyget spatialization. Aktivera Akustik DSP-projekt genom att öppna de ljud Unity-inställningar med hjälp av **Redigera > Inställningar > ljud**, sedan välja **projekt Akustik** som den **Spatializer plugin-programmet** för ditt projekt. Kontrollera att den **DSP-buffertstorleken** är inställd på bästa möjliga prestanda.

![Skärmbild av Unity Project inställningspanelen](media/project-settings.png)  

![Skärmbild av Unity Spatializer inställningspanelen med projekt Akustik spatializer valt](media/choose-spatializer.png)

Öppna ljud Mixer (**Fönster > ljud Mixer**). Kontrollera att du har minst en Mixer med en grupp. Om du inte har det, klickar på knappen ”+” till höger om **blandare**. Högerklicka längst ned på kanal-remsans i avsnittet effekter och lägga till den **projekt Akustik Mixer** effekt. Observera att endast en projekt Akustik Mixer stöds i taget.

![Skärmbild av Unity ljud Mixer som är värd för projektet Akustik mixer](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Aktivera Akustik på ljud källor
Skapa en ljudkälla. Klicka på kryssrutan längst ned på panelen för den AudioSource granska där det står **Spatialize**. Se till att **Spatial Blend** är inställd på fullständig 3D.  

![Skärmbild av Unity ljud källa panelen](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Aktivera akustiska design
Bifoga skriptet **AcousticsAdjust** till en bra källa på scenen att aktivera ytterligare design källparametrar, genom att klicka på **Lägg till komponent** och välja **skript > Akustik justera** :

![Skärmbild av Unity AcousticsAdjust skript](media/acoustics-adjust.png)

## <a name="next-steps"></a>Nästa steg
* [Skapa din scen med projekt Akustik för Unity](unity-baking.md)
* [Skapa ett Batch-konto](create-azure-account.md) att skapa din scen i molnet
* Utforska den [projekt Akustik Unity designprocessen](unity-workflow.md).

