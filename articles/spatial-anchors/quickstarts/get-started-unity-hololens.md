---
title: Snabbstart – skapa en HoloLens Unity-app med Azure Spatial ankare | Microsoft Docs
description: I den här snabbstarten lär du dig att skapa en HoloLens-app med Unity med hjälp av Spatial Anchors.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: ce9bca3f11911677a6009b4cfe0c3f6413b9f837
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60240596"
---
# <a name="quickstart-create-a-hololens-unity-app-that-uses-azure-spatial-anchors"></a>Snabbstart: Skapa en HoloLens Unity-app som använder Azure Spatial ankare

I den här snabbstarten skapar du en HoloLens Unity-app som använder [Azure Spatial ankare](../overview.md). Spatial fästpunkter är en plattformsoberoende developer-tjänst som hjälper dig att skapa Mixad verklighet upplevelser med objekt som kvarstår befann i enheter över tid. När du är klar har du en HoloLens-app som skapats med Unity och som kan spara och återkalla en spatial fästpunkt.

Du lär dig följande:

- Skapa ett konto för Spatial fästpunkter.
- Förbered Unity Versionsinställningar.
- Konfigurera Spatial ankare konto-ID och kontonyckeln.
- Exportera HoloLens Visual Studio-projektet.
- Distribuera appen och kör den på en HoloLens-enhet.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här snabbstarten behöver du:


- Behöver du en Windows-dator där <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3</a> eller senare och <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a> eller senare är installerat. Visual Studio-installationen måste innehålla den **Universal Windows Platform-utveckling** arbetsbelastning. Du måste också installera <a href="https://git-scm.com/download/win" target="_blank">Git för Windows</a>.
- Du behöver en HoloLens enhet där [utvecklarläget](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) aktiverat. [Uppdatering för Windows 10 oktober 2018](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018) (även kallat RS5) måste vara installerad på enheten. Om du vill uppdatera till den senaste versionen på HoloLens, öppna den **inställningar** app, gå till **uppdatering och säkerhet**, och välj sedan **söka efter uppdateringar**.
- På din app, måste du aktivera den **SpatialPerception** kapaciteten. Den här inställningen finns under **Versionsinställningar** > **Player-inställningar** > **publiceringsinställningarna**  >   **Funktioner**.
- På din app, måste du aktivera **virtuella verklighet stöds** med **Windows Mixed Reality SDK**. Den här inställningen finns under **Versionsinställningar** > **Player-inställningar** > **XR inställningar**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Öppna exempelprojektet i Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Öppna projektet i mappen Unity i Unity.

Öppna **Build Settings** (Bygginställningar) genom att välja **Arkiv** > **Build Settings** (Bygginställningar).

I avsnittet **Plattform** väljer du **Universell Windows-plattform**. Ändra den **rikta enhet** till **HoloLens**.

Välj **Switch Platform** (Växla plattform) för att ändra plattformen till **Universell Windows-plattform**. Unity bli du ombedd att installera komponenter för UWP-supporten om de saknas.

   ![Unity Versionsinställningar fönster](./media/get-started-unity-hololens/unity-build-settings.png)

Stäng fönstret **Build Settings** (Bygginställningar).

## <a name="configure-the-account-identifier-and-key"></a>Konfigurera konto ID och nyckel

I den **projekt** rutan, gå till `Assets/AzureSpatialAnchorsPlugin/Examples` och öppna den `AzureSpatialAnchorsBasicDemo.unity` scenfilen.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Spara scenen genom att välja **Arkiv** > **Spara**.

## <a name="export-the-hololens-visual-studio-project"></a>Exportera HoloLens Visual Studio-projektet

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Välj **skapa**. I dialogrutan väljer du en mapp där du vill exportera HoloLens Visual Studio-projektet.

När exporten är klar visas en mapp som innehåller det exporterade HoloLens-projektet.

## <a name="deploy-the-hololens-application"></a>Distribuera HoloLens-programmet

I mappen, dubbelklickar du på **HelloAR U3D.sln** att öppna projektet i Visual Studio.

Ändra den **lösningskonfiguration** till **versionen**, ändra den **Lösningsplattform** till **x86**, och välj **enhet**  från distributionsalternativ för målet.

Om du använder HoloLens 2, använda **ARM** som den **Lösningsplattform**, i stället för **x86**.

   ![Visual Studio-konfiguration](./media/get-started-unity-hololens/visual-studio-configuration.png)

Aktivera enheten HoloLens, logga in och ansluta enheten till datorn med hjälp av en USB-kabel.

Välj **Felsök** > **Starta felsökning** för att distribuera appen och börja felsöka.

Följ instruktionerna i appen för att placera och återkalla en fästpunkt.

Stoppa appen i Visual Studio genom att välja antingen **stoppa felsökning** eller SKIFT + F5.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Självstudie: Dela spatial ankare över enheter](../tutorials/tutorial-share-anchors-across-devices.md)
