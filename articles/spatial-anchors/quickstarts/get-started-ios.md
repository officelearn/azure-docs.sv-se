---
title: 'Snabbstart: Skapa en iOS-app'
description: I den här snabbstarten lär du dig att skapa en iOS-app med hjälp av Spatial Anchors.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 6304077a26f5c0ecb91e1ec4936bd79b3d839d95
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79471225"
---
# <a name="quickstart-create-an-ios-app-with-azure-spatial-anchors-in-either-swift-or-objective-c"></a>Snabbstart: Skapa en iOS-app med Azure Spatial Anchors, i antingen Swift eller Objective-C

I den här snabbstarten beskrivs hur du skapar en iOS-app med hjälp av [Azure Spatial Anchors](../overview.md) i antingen Swift eller Objective-C. Azure Spatial Anchors är en plattformsoberoende utvecklartjänst som du kan använda för att skapa upplevelser med mixad verklighet med hjälp av objekt som bevarar sin plats mellan enheter över tid. När du är klar har du en ARKit iOS-app som kan spara och återkalla en spatial fästpunkt.

Du lär dig följande:

> [!div class="checklist"]
> * Skapa ett Spatial Anchors-konto
> * Konfigurera konto-ID och kontonyckel för Spatial Anchors
> * Distribuera och köra på en iOS-enhet

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

Kontrollera att du har följande så att du kan utföra den här snabbstarten:

- En utvecklare aktiverade macOS-maskin med den senaste versionen av <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a> och <a href="https://cocoapods.org" target="_blank">CocoaPods</a> installerad.
- Git installeras via HomeBrew:
  1. Ange följande kommando som en enda `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`rad i terminalen: . 
  1. Kör `brew install git` och `brew install git-lfs`.
  1. Uppdatera din git `git lfs install` config med (för `git lfs install --system` den aktuella användaren) eller (för hela systemet).
- En utvecklaraktiverad <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">ARKit-kompatibel</a> iOS-enhet.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Öppna exempelprojektet

Använd terminalen för att utföra följande åtgärder.

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Installera nödvändiga poddar med hjälp av CocoaPods:

# <a name="swift"></a>[Swift](#tab/openproject-swift)

Navigera till `iOS/Swift/`.

```bash
cd ./iOS/Swift/
```

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

Navigera till `iOS/Objective-C/`.

```bash
cd ./iOS/Objective-C/
```

---

Kör `pod install --repo-update` för att installera CocoaPods för projektet.

Öppna nu `.xcworkspace` i Xcode.

> [!NOTE]
> Se felsökningsstegen [här](#cocoapods-issues-on-macos-catalina-1015) om du har Problem med CocoaPod efter uppgradering till macOS Catalina (10.15).

# <a name="swift"></a>[Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

---

## <a name="configure-account-identifier-and-key"></a>Konfigurera konto-ID och -nyckel

Nästa steg är att konfigurera appen så att den använder kontoidentifieraren och kontonyckeln. Du kopierade dem till en textredigerare när [du konfigurerade resursen Spatial Anchors](#create-a-spatial-anchors-resource).

# <a name="swift"></a>[Swift](#tab/openproject-swift)

Öppna `iOS/Swift/SampleSwift/ViewControllers/BaseViewController.swift`.

Leta upp fältet `spatialAnchorsAccountKey` och ersätt `Set me` med kontonyckeln.

Leta upp fältet `spatialAnchorsAccountId` och ersätt `Set me` med kontoidentifieraren.

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

Öppna `iOS/Objective-C/SampleObjC/BaseViewController.m`.

Leta upp fältet `SpatialAnchorsAccountKey` och ersätt `Set me` med kontonyckeln.

Leta upp fältet `SpatialAnchorsAccountId` och ersätt `Set me` med kontoidentifieraren.

---

## <a name="deploy-the-app-to-your-ios-device"></a>Distribuera appen till din iOS-enhet

Ansluta iOS-enheten till Mac-datorn och ange **det aktiva schemat** till iOS-enheten.

![Välja enheten](./media/get-started-ios/select-device.png)

Välj **Build and then run the current scheme** (Skapa och kör sedan det aktuella schemat).

![Distribuera och köra](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> Om du ser felet `library not found for -lPods-SampleObjC` har du sannolikt öppnat filen `.xcodeproj` i stället för `.xcworkspace`. Öppna `.xcworkspace` och försök igen.

I Xcode stoppar du appen genom att trycka på **Stoppa**.

## <a name="troubleshooting"></a>Felsökning

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>CocoaPods frågor om macOS Catalina (10,15)

Om du nyligen uppdaterat till macOS Catalina (10,15) och hade CocoaPods installerat i förväg, kan CocoaPods vara i ett brutet tillstånd och misslyckas med att korrekt konfigurera dina poddar och `.xcworkspace` projektfiler. LÃ¶s problemet genom att installera om CocoaPods genom att köra följande kommandon:

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

### <a name="app-crashes-when-deploying-to-ios-1031-from-a-personal-provisioning-profiledeveloper-account"></a>Appen kraschar när den distribueras till iOS 10.3.1 från ett personligt etableringsprofil-/utvecklarkonto 

Om du distribuerar din iOS-app på iOS 10.3.1 från ett personligt `Library not loaded: @rpath/ADAL...`etableringsprofil-/utvecklarkonto kan det här felet visas: . 

Så här löser du problemet:

- Använd en etableringsprofil som inte är en profil för personligt team (betalt utvecklarkonto).
- Distribuera din app till en iOS-enhet som kör iOS 13.3 eller tidigare, eller till en som kör betaversionen av iOS 13.4.
- Läs mer om problemet på [Stack Overflow](https://stackoverflow.com/questions/60015309/running-ios-apps-causes-runtime-error-for-frameworks-code-signature-invalid).


[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Självstudiekurs: Dela rumsliga ankare mellan enheter](../tutorials/tutorial-share-anchors-across-devices.md)
