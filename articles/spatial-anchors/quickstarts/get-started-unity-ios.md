---
title: 'Snabbstart: Skapa en Unity iOS-app'
description: I den här snabbstarten lär du dig att skapa en iOS-app med Unity med hjälp av Spatial Anchors.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 37a82c4001dd42a4cfbbb9dabec29f28359afd75
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240592"
---
# <a name="quickstart-create-a-unity-ios-app-with-azure-spatial-anchors"></a>Snabbstart: Skapa en Unity iOS-app med Azure Spatial Anchors

Den här snabbstarten beskriver hur du skapar en Unity iOS-app med [Azure Spatial Anchors](../overview.md). Azure Spatial Anchors är en plattformsoberoende utvecklartjänst som du kan använda för att skapa upplevelser med mixad verklighet med hjälp av objekt som bevarar sin plats mellan enheter över tid. När du är klar har du en ARKit iOS-app som skapats med Unity och som kan spara och återkalla en spatial fästpunkt.

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

- En macOS-maskin med <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.1 eller 2019.2</a>, den senaste versionen av <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a>och <a href="https://cocoapods.org" target="_blank">CocoaPods</a> installerade.
- Git installeras via HomeBrew. Ange följande kommando i en enda `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`rad i terminalen: . Kör sedan `brew install git` `brew install git-lfs`och .
- En utvecklaraktiverad <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">ARKit-kompatibel</a> iOS-enhet.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Ladda ner och öppna exempelprojektet Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [iOS Unity Build Settings](../../../includes/spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Konfigurera konto-ID och -nyckel

I fönstret **Projekt** går du till `Assets/AzureSpatialAnchors.Examples/Scenes` och öppnar scenfilen `AzureSpatialAnchorsBasicDemo.unity`.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Spara scenen genom att välja **Spara fil** -> **.**

## <a name="export-the-xcode-project"></a>Exportera Xcode-projektet

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

[!INCLUDE [Configure Xcode](../../../includes/spatial-anchors-unity-ios-xcode.md)]

Följ instruktionerna i appen för att placera och återkalla en fästpunkt.

När du är klar stoppar du appen genom att trycka på **Stoppa** i Xcode.

## <a name="troubleshooting"></a>Felsökning

### <a name="rendering-issues"></a>Återgivningsproblem

Om du inte ser kameran som bakgrund när du kör appen (om du till exempel ser en tom, blå eller annan textur) behöver du förmodligen återimportera tillgångarna i Unity. Stoppa appen. Välj **Tillgångar -> Importera alla**på den övre menyn i Unity. Kör sedan appen igen.

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>CocoaPods frågor om macOS Catalina (10,15)

Om du nyligen uppdaterat till macOS Catalina (10,15) och hade CocoaPods installerat i förväg, kan CocoaPods vara i ett brutet tillstånd och misslyckas med att korrekt konfigurera dina poddar och `.xcworkspace` projektfiler. LÃ¶s problemet genom att installera om CocoaPods genom att köra följande kommandon:

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

### <a name="unity-20193"></a>Enhet 2019.3

På grund av att förändringar bryts stöds för närvarande inte Unity 2019.3. Använd Unity 2019.1 eller 2019.2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Självstudiekurs: Dela rumsliga ankare mellan enheter](../tutorials/tutorial-share-anchors-across-devices.md)
