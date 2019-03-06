---
title: Snabbstart – Skapa HoloLens Unity-app med Azure Spatial Anchors | Microsoft Docs
description: I den här snabbstarten lär du dig att skapa en HoloLens-app med Unity med hjälp av Spatial Anchors.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: b2bfec47bc92ebf5db1561d8fca33940dc376866
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752948"
---
# <a name="quickstart-create-a-hololens-unity-app-using-azure-spatial-anchors"></a>Snabbstart: Skapa en HoloLens Unity-app med hjälp av Azure Spatial Anchors

I den här snabbstarten beskrivs hur du skapar en HoloLens Unity-app med hjälp av [Azure Spatial Anchors](../overview.md). Azure Spatial Anchors är en plattformsoberoende utvecklartjänst som du kan använda för att skapa Mixed Reality-upplevelser med hjälp av objekt som bevarar sin plats mellan enheter över tid. När du är klar har du en HoloLens-app som skapats med Unity och som kan spara och återkalla en spatial fästpunkt.

Du lär dig följande:

> [!div class="checklist"]
> * Skapa ett Spatial Anchors-konto
> * Förbereda Unity-bygginställningar
> * Konfigurera kontoidentifierare och kontonyckel för Spatial Anchors
> * Exportera HoloLens Visual Studio-projektet
> * Distribuera och köra på en HoloLens-enhet

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Kontrollera att du har följande så att du kan utföra den här snabbstarten:

- En Windows-dator med <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3+</a> och <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017+</a> installerat med arbetsbelastningen **Universal Windows Platform-utveckling**.
- En HoloLens-enhet med [utvecklarläge](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) aktiverat.
- Appen måste ange funktionen **SpatialPerception** under **Build Settings (Bygginställningar)**->**Player Settings (Spelarinställningar)**->**Publishing Settings (Publiceringsinställningar)**->**Capabilities (Funktioner)**.
- Appen måste aktivera **Virtual Reality Supported** (Virtuell verklighet stöds) med **Windows Mixed Reality SDK** under **Build Settings (Bygginställningar)**->**Player Settings (Spelarinställningar)**->**XR Settings (XR-inställningar)**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Öppna exempelprojektet i Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Öppna Unity och öppna projektet i mappen `Unity`.

Öppna **Build Settings** (Bygginställningar) genom att välja **Arkiv** -> **Build Settings** (Bygginställningar).

I avsnittet **Plattform** väljer du **Universell Windows-plattform**. Sedan ändrar du **Målenhet** till **HoloLens**.

Välj **Switch Platform** (Växla plattform) för att ändra plattformen till **Universell Windows-plattform**.

![Unity-bygginställningar](./media/get-started-unity-hololens/unity-build-settings.png)

Stäng fönstret **Build Settings** (Bygginställningar).

## <a name="configure-account-identifier-and-key"></a>Konfigurera kontoidentifierare och nyckel

I fönstret **Projekt** går du till `Assets/AzureSpatialAnchorsPlugin/Examples` och öppnar scenfilen `AzureSpatialAnchorsBasicDemo.unity`.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Spara scenen genom att välja **Arkiv** -> **Spara**.

## <a name="export-the-hololens-visual-studio-project"></a>Exportera HoloLens Visual Studio-projektet

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Välj **Build** (Skapa) för att öppna en dialogruta. Välj sedan en mapp för att exportera HoloLens Visual Studio-projektet.

När exporten är klar visas en mapp som innehåller det exporterade HoloLens-projektet.

## <a name="deploy-the-hololens-application"></a>Distribuera HoloLens-programmet

I mappen dubbelklickar du på `HelloAR U3D.sln` för att öppna projektet i Visual Studio.

Ändra **Lösningskonfiguration** till **Version**, ändra **Lösningsplattform** till **x86** och välj **Enhet** bland alternativen för distributionsmål.

![Visual Studio-konfiguration](./media/get-started-unity-hololens/visual-studio-configuration.png)

Slå på HoloLens-enheten, logga in och anslut den till datorn via en USB-kabel.

Välj **Felsök** > **Starta felsökning** för att distribuera appen och börja felsöka.

Följ instruktionerna i appen för att placera och återkalla en fästpunkt.

Stoppa appen i Visual Studio genom att välja **Stoppa felsökning** eller trycka på **Skift+F5**.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Självstudier: Dela Spatial Anchors mellan olika enheter](../tutorials/tutorial-share-anchors-across-devices.md)
