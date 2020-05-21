---
title: 'Snabb start: skapa en Xamarin iOS-app'
description: I den här snabb starten får du lära dig hur du skapar en iOS-app med Xamarin med hjälp av spatiala ankare.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/22/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 9c5e36fd062a2579ece4aaa1501243295af72e1b
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649823"
---
# <a name="quickstart-create-a-xamarin-ios-app-with-azure-spatial-anchors"></a>Snabb start: skapa en Xamarin iOS-app med Azure spatial ankare

Den här snabb starten beskriver hur du skapar en iOS-app med Xamarin med hjälp av [Azure spatiala ankare](../overview.md). Azure Spatial Anchors är en plattformsoberoende utvecklartjänst som du kan använda för att skapa upplevelser med mixad verklighet med hjälp av objekt som bevarar sin plats mellan enheter över tid. När du är klar har du en iOS-app som kan spara och återkalla en spatial fäst punkt.

Du lär dig följande:

> [!div class="checklist"]
> * Skapa ett Spatial Anchors-konto
> * Konfigurera konto-ID och kontonyckel för Spatial Anchors
> * Distribuera och köra på en iOS-enhet

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

Kontrollera att du har följande så att du kan utföra den här snabbstarten:
- En Mac som kör macOS med hög Sierra (10,13) eller högre än:
  - Den senaste versionen av Xcode och iOS SDK som installerats från [App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12).
  - En uppdaterad version av <a href="https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019" target="_blank">Visual Studio för Mac 8.1 +</a>.
  - <a href="https://git-scm.com/download/mac" target="_blank">Git för MacOS</a>.
  - <a href="https://git-lfs.github.com/">Git-LF</a>.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Öppna exempelprojektet

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Öppna `Xamarin/SampleXamarin.sln` i Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Konfigurera konto-ID och -nyckel

Nästa steg är att konfigurera appen så att den använder din konto identifierare och konto nyckeln. Du kopierade dem till en text redigerare när du [ställer in resursen för spatiala ankare](#create-a-spatial-anchors-resource).

Öppna `Xamarin/SampleXamarin.Common/AccountDetails.cs`.

Leta upp fältet `SpatialAnchorsAccountKey` och ersätt `Set me` med kontonyckeln.

Leta upp fältet `SpatialAnchorsAccountId` och ersätt `Set me` med kontoidentifieraren.

Leta upp `StartSession()` och Lägg till följande rad och ersätt i din konto domän från tidigare: `this.cloudSession.Configuration.AccountKey = "MyAccountDomain";` .

## <a name="deploy-the-app-to-your-ios-device"></a>Distribuera appen till din iOS-enhet

Starta iOS-enheten, logga in och Anslut den till datorn med en USB-kabel.

Ange att startprojektet ska **SampleXamarin. iOS**, ändra **lösningens konfiguration** till **version**och välj den enhet som du vill distribuera till i list rutan enhets väljare.

![Visual Studio-konfiguration](./media/get-started-xamarin-iOS/visual-studio-macos-configuration.jpg)

Välj **Kör**  >  **starta utan fel sökning** för att distribuera och starta din app.

I appen väljer du **Basic** för att köra demonstrationen och följer anvisningarna för att placera och återkalla en fäst punkt.

> ![Skärm bild 1 ](./media/get-started-xamarin-ios/screenshot-1.jpg)
>  ![ skärmdump 2, ](./media/get-started-xamarin-ios/screenshot-2.jpg)
>  ![ skärm bild 3](./media/get-started-xamarin-ios/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Självstudie: dela spatiala ankare mellan enheter](../tutorials/tutorial-share-anchors-across-devices.md)
