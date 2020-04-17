---
title: 'Snabbstart: Skapa en HoloLens-app med Unity'
description: I den här snabbstarten lär du dig att skapa en HoloLens-app med Unity med hjälp av Spatial Anchors.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 9e946d7f4616799768711ee8c18584a2a8fcff2a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81482013"
---
# <a name="quickstart-create-a-unity-hololens-app-that-uses-azure-spatial-anchors"></a>Snabbstart: Skapa en Unity HoloLens-app som använder Azure Spatial Anchors

I den här snabbstarten skapar du en Unity HoloLens-app som använder [Azure Spatial Anchors](../overview.md). Spatial Anchors är en plattformsoberoende utvecklartjänst som gör att du kan skapa upplevelser med mixad verklighet med objekt som bevarar deras plats på olika enheter över tid. När du är klar har du en HoloLens-app som skapats med Unity och som kan spara och återkalla en spatial fästpunkt.

Du lär dig följande:

- Skapa ett rumsligt fästpunkter.Create a Spatial Anchors account.
- Förbered inställningarna för Unity-bygg.
- Konfigurera kontoidentifieraren och kontonyckeln Spatial Anchors.
- Exportera Projektet HoloLens Visual Studio.
- Distribuera appen och kör den på en HoloLens-enhet.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

För att slutföra den här snabbstarten behöver du:

- Du behöver en Windows-dator med <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.1 eller 2019.2</a> och <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> eller senare är installerade. Visual Studio-installationen måste innehålla den universella utvecklingsarbetsbelastningen för **Windows-plattformen** och **komponenten Windows 10 SDK (10.0.18362.0 eller nyare).** Du måste också installera <a href="https://git-scm.com/download/win" target="_blank">Git för Windows</a> och <a href="https://git-lfs.github.com/">Git LFS</a>.
- Du behöver en HoloLens-enhet där [utvecklarläget](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) är aktiverat. [Windows 10 Oktober 2018 Update](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018) (kallas även RS5) måste installeras på enheten. Om du vill uppdatera till den senaste versionen på HoloLens öppnar du appen **Inställningar,** går till **Uppdatera & säkerhet**och väljer sedan Sök efter **uppdateringar**.
- I din app måste du aktivera **SpatialPerception-funktionen.** Den här inställningen finns i Inställningar för **Bygginställningar** > **Player Settings** > **Publiceringsinställningar** > **Capabilities**.
- I din app måste du aktivera **Virtual Reality som stöds** med Windows Mixed Reality **SDK**. Den här inställningen finns i **Inställningar för Bygginställningar** > **Player Settings** > **XR Inställningar**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Ladda ner och öppna exempelprojektet Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

Öppna **Build Settings** (Bygginställningar) genom att välja **Arkiv** > **Build Settings** (Bygginställningar).

I avsnittet **Plattform** väljer du **Universell Windows-plattform**. Ändra **målenheten** till **HoloLens**.

Välj **Switch Platform** (Växla plattform) för att ändra plattformen till **Universell Windows-plattform**. Unity kan uppmana dig att installera UWP-supportkomponenter om de saknas.

![Fönstret Enhetsuppbyggnadsinställningar](./media/get-started-unity-hololens/unity-build-settings.png)

Stäng fönstret **Build Settings** (Bygginställningar).

## <a name="configure-the-account-identifier-and-key"></a>Konfigurera kontoidentifieraren och nyckeln

Gå till `Assets/AzureSpatialAnchors.Examples/Scenes` och öppna scenfilen `AzureSpatialAnchorsBasicDemo.unity` i **projektfönstret.**

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Spara scenen genom att välja **Spara fil** > **.**

## <a name="export-the-hololens-visual-studio-project"></a>Exportera HoloLens Visual Studio-projektet

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Välj **Bygg**. Välj en mapp där HoloLens Visual Studio-projektet ska exporteras i dialogrutan.

När exporten är klar visas en mapp som innehåller det exporterade HoloLens-projektet.

## <a name="deploy-the-hololens-application"></a>Distribuera HoloLens-programmet

Dubbelklicka på **HelloAR U3D.sln** i mappen om du vill öppna projektet i Visual Studio.

Ändra **lösningskonfigurationen** till **release,** ändra **lösningsplattformen** till **x86**och välj **Enhet** från distributionsmålsalternativen.

Om du använder HoloLens 2 använder du **ARM64** som **lösningsplattform**i stället för **x86**.

   ![Visual Studio-konfiguration](./media/get-started-unity-hololens/visual-studio-configuration.png)

Slå på HoloLens-enheten, logga in och anslut enheten till datorn med hjälp av en USB-kabel.

Välj **Felsökning** > **Starta felsökning** för att distribuera din app och börja felsöka.

Följ instruktionerna i appen för att placera och återkalla en fästpunkt.

I Visual Studio stoppar du appen genom att välja **Stoppa felsökning** eller Skift+F5.

## <a name="troubleshooting"></a>Felsökning

### <a name="unity-20193"></a>Enhet 2019.3

På grund av att förändringar bryts stöds för närvarande inte Unity 2019.3. Använd Unity 2019.1 eller 2019.2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Självstudiekurs: Dela rumsliga ankare mellan enheter](../tutorials/tutorial-share-anchors-across-devices.md)
