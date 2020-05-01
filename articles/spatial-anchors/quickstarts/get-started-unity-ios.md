---
title: 'Snabb start: skapa en Unity iOS-app'
description: I den här snabbstarten lär du dig att skapa en iOS-app med Unity med hjälp av Spatial Anchors.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 37a82c4001dd42a4cfbbb9dabec29f28359afd75
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "79240592"
---
# <a name="quickstart-create-a-unity-ios-app-with-azure-spatial-anchors"></a>Snabb start: skapa en enhets-iOS-app med avstånds ankare för Azure

Den här snabb starten beskriver hur du skapar en Unity iOS-app med hjälp av [Azure spatiala ankare](../overview.md). Azure Spatial Anchors är en plattformsoberoende utvecklartjänst som du kan använda för att skapa upplevelser med mixad verklighet med hjälp av objekt som bevarar sin plats mellan enheter över tid. När du är klar har du en ARKit iOS-app som skapats med Unity och som kan spara och återkalla en spatial fästpunkt.

Du lär dig följande:

> [!div class="checklist"]
> * Skapa ett Spatial Anchors-konto
> * Förbereda Unity-bygginställningar
> * Konfigurera konto-ID och kontonyckel för Spatial Anchors
> * Exportera Xcode-projektet
> * Distribuera och köra på en iOS-enhet

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

Kontrollera att du har följande så att du kan utföra den här snabbstarten:

- En macOS-dator med enhet <a href="https://unity3d.com/get-unity/download" target="_blank">2019,1 eller 2019,2</a>, den senaste versionen av <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a>och <a href="https://cocoapods.org" target="_blank">CocoaPods</a> installerad.
- Git installerat via HomeBrew. Ange följande kommando i en enskild rad i terminalen: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Kör `brew install git` sedan och `brew install git-lfs`.
- En utvecklaraktiverad <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">ARKit-kompatibel</a> iOS-enhet.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Ladda ned och öppna exempel projektet Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [iOS Unity Build Settings](../../../includes/spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Konfigurera konto-ID och -nyckel

I fönstret **Projekt** går du till `Assets/AzureSpatialAnchors.Examples/Scenes` och öppnar scenfilen `AzureSpatialAnchorsBasicDemo.unity`.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Spara scenen genom att välja **Arkiv** -> **Spara**.

## <a name="export-the-xcode-project"></a>Exportera Xcode-projektet

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

[!INCLUDE [Configure Xcode](../../../includes/spatial-anchors-unity-ios-xcode.md)]

Följ instruktionerna i appen för att placera och återkalla en fästpunkt.

När du är färdig stoppar du appen genom att trycka på **stoppa** i Xcode.

## <a name="troubleshooting"></a>Felsökning

### <a name="rendering-issues"></a>Åter givnings problem

Om du inte ser kameran som bakgrund när du kör appen (om du till exempel ser en tom, blå eller annan textur) behöver du förmodligen återimportera tillgångarna i Unity. Stoppa appen. På den översta menyn i enhets listan väljer du **till gångar-> åter importera alla**. Kör sedan appen igen.

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>CocoaPods problem på macOS Catalina (10,15)

Om du nyligen har uppdaterat till macOS Catalina (10,15) och hade CocoaPods installerat i förväg, kan CocoaPods vara i ett trasigt tillstånd och inte konfigurera dina poddar- `.xcworkspace` och projektfiler korrekt. För att lösa det här problemet måste du installera om CocoaPods genom att köra följande kommandon:

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

### <a name="unity-20193"></a>Unity 2019,3

På grund av avbrytande ändringar stöds inte Unity 2019,3 för närvarande. Använd Unity 2019,1 eller 2019,2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Självstudie: dela spatiala ankare mellan enheter](../tutorials/tutorial-share-anchors-across-devices.md)
