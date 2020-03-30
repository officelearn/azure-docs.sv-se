---
title: 'Snabbstart: Skapa en Unity Android-app'
description: I den här snabbstarten lär du dig att skapa en Android-app med Unity med hjälp av Spatial Anchors.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 7acff7f0249cdedcebd367fc315be92cafb9ab78
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77615446"
---
# <a name="quickstart-create-a-unity-android-app-with-azure-spatial-anchors"></a>Snabbstart: Skapa en Unity Android-app med Azure Spatial Anchors

Den här snabbstarten beskriver hur du skapar en Unity Android-app med [Azure Spatial Anchors](../overview.md). Azure Spatial Anchors är en plattformsoberoende utvecklartjänst som du kan använda för att skapa upplevelser med mixad verklighet med hjälp av objekt som bevarar sin plats mellan enheter över tid. När du är klar har du en ARCore Android-app som skapats med Unity och som kan spara och återkalla en spatial fästpunkt.

Du lär dig följande:

> [!div class="checklist"]
> * Skapa ett Spatial Anchors-konto
> * Förbereda Unity-bygginställningar
> * Konfigurera konto-ID och kontonyckel för Spatial Anchors
> * Exportera Android Studio-projektet
> * Distribuera och köra på en Android-enhet

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

Kontrollera att du har följande så att du kan utföra den här snabbstarten:

- En Windows- eller macOS-maskin med <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.1 eller 2019.2,</a> inklusive Android Build Support och Android SDK & NDK Tools-modulerna.
  - Om du kör på Windows behöver du också <a href="https://git-scm.com/download/win" target="_blank">Git för Windows</a> och <a href="https://git-lfs.github.com/">Git LFS</a>.
  - Om du kör på macOS, få Git installerat via HomeBrew. Ange följande kommando i en enda `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`rad i terminalen: . Kör sedan `brew install git` `brew install git-lfs`och .
- En <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">utvecklaraktiverad</a> och <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore-kapabel</a> Android-enhet.
  - Ytterligare drivrutiner kan krävas för att datorn ska kunna kommunicera med din Android-enhet. Se [här](https://developer.android.com/studio/run/device.html) för ytterligare information och instruktioner.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Ladda ner och öppna exempelprojektet Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Konfigurera konto-ID och -nyckel

I fönstret **Projekt** går du till `Assets/AzureSpatialAnchors.Examples/Scenes` och öppnar scenfilen `AzureSpatialAnchorsBasicDemo.unity`.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Spara scenen genom att välja **Spara fil** -> **.**

## <a name="export-the-android-studio-project"></a>Exportera Android Studio-projektet

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Välj din enhet i **Kör enhet** och klicka på Skapa **och kör**. Du blir ombedd att `.apk` spara en fil som du kan välja vilket namn som helst för.

Följ instruktionerna i appen för att placera och återkalla en fästpunkt.

## <a name="troubleshooting"></a>Felsökning

### <a name="rendering-issues"></a>Återgivningsproblem

Om du inte ser kameran som bakgrund när du kör appen (om du till exempel ser en tom, blå eller annan textur) behöver du förmodligen återimportera tillgångarna i Unity. Stoppa appen. Från den översta menyn i Unity väljer du **Assets -> Reimport all** (Tillgångar -> Återimportera alla). Kör sedan appen igen.

### <a name="unity-20193"></a>Enhet 2019.3

På grund av att förändringar bryts stöds för närvarande inte Unity 2019.3. Använd Unity 2019.1 eller 2019.2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Självstudiekurs: Dela rumsliga ankare mellan enheter](../tutorials/tutorial-share-anchors-across-devices.md)
