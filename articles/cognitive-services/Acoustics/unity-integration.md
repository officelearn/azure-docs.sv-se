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
ms.date: 03/14/2019
ms.author: kegodin
ms.openlocfilehash: 7d8edd7b092ee1ed4f953d753b2bbe864e075378
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58137740"
---
# <a name="project-acoustics-unity-integration"></a>Projektet Akustik Unity-integrering
Den här anvisningen förklarar integrering av projektet Akustik Unity-plugin-programmet i dina Unity-projekt.

Programvarukrav:
* [Unity 2018.2 +](http://unity3d.com) för Windows
* [Projektet Akustik Unity-paketet](https://www.microsoft.com/en-us/download/details.aspx?id=57346)

## <a name="import-the-plugin"></a>Importera plugin-programmet
Importera Akustik UnityPackage i projektet. 
* I Unity, går du till **tillgångar > Importera paket > anpassat paket...**

    ![Importera paket](media/import-package.png)  

* Choose **ProjectAcoustics.unitypackage**

Om du importerar plugin-programmet till ett befintligt projekt, ditt projekt kan redan ha en **mcs.rsp** filen i projektroten, som anger alternativ för att C#-kompilatorn. Du måste slå samman innehållet i filen med den mcs.rsp-fil som ingår i projektet Akustik plugin-programmet.

## <a name="enable-the-plugin"></a>Aktivera plugin-programmet
Ändamålet delen av verktyget Akustik kräver .NET 4.x scripting runtime-versionen. Paketimport uppdaterar dina Unity player-inställningar. Starta om Unity för den här inställningen ska börja gälla.

![Player-inställningar](media/player-settings.png)

![.NET 4.5](media/net45.png)

## <a name="set-up-audio-dsp"></a>Ställ in ljud DSP
Projektet Akustik innehåller ljud runtime DSP som integreras i Unity ljud motorn spatializer framework. Den innehåller både HRTF- och panoreringsverktyget spatialization. Aktivera Akustik DSP-projekt genom att öppna de ljud Unity-inställningar med hjälp av **Redigera > Inställningar > ljud**, sedan välja **projekt Akustik** som den **Spatializer plugin-programmet** för ditt projekt. Kontrollera att den **DSP-buffertstorleken** är inställd på bästa möjliga prestanda.

![Projektinställningar](media/project-settings.png)  

![Projektet Akustik Spatializer](media/choose-spatializer.png)

Öppna ljud Mixer (**Fönster > ljud Mixer**). Kontrollera att du har minst en Mixer med en grupp. Om du inte har det, klickar på knappen ”+” till höger om **blandare**. Högerklicka längst ned på kanal-remsans i avsnittet effekter och lägga till den **projekt Akustik Mixer** effekt. Observera att endast en projekt Akustik Mixer stöds i taget.

![Ljud Mixer](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Aktivera Akustik på ljud källor
Skapa en ljudkälla. Klicka på kryssrutan längst ned på panelen för den AudioSource granska där det står **Spatialize**. Se till att **Spatial Blend** är inställd på fullständig 3D.  

![Ljudkälla](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Aktivera akustiska design
Bifoga skriptet **AcousticsAdjust** till en bra källa på scenen att aktivera ytterligare design källparametrar, genom att klicka på **Lägg till komponent** och välja **skript > Akustik justera** :

![AcousticsAdjust](media/acoustics-adjust.png)

## <a name="next-steps"></a>Nästa steg
* [Skapa din scen med projekt Akustik för Unity](unity-baking.md)
* [Skapa ett Batch-konto](create-azure-account.md) att skapa din scen i molnet
* Utforska den [projekt Akustik Unity designprocessen](unity-workflow.md).

