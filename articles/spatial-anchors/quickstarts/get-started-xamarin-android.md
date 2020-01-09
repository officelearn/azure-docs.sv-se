---
title: 'Snabb start: skapa en Xamarin Android-app'
description: I den här snabb starten får du lära dig hur du skapar en Android-app med Xamarin med hjälp av spatiala ankare.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/22/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 94906d645ec38a24d54536ee8aa93e7418c8dc35
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75465173"
---
# <a name="quickstart-create-a-xamarin-android-app-with-azure-spatial-anchors"></a>Snabb start: skapa en Xamarin Android-app med Azures spatiala ankare

Den här snabb starten beskriver hur du skapar en Android-app med Xamarin med hjälp av [Azure spatiala ankare](../overview.md). Azure Spatial Anchors är en plattformsoberoende utvecklartjänst som du kan använda för att skapa Mixed Reality-upplevelser med hjälp av objekt som bevarar sin plats mellan enheter över tid. När du är klar har du en Android-app som kan spara och återkalla en spatial fäst punkt.

Du lär dig följande:

> [!div class="checklist"]
> * Skapa ett Spatial Anchors-konto
> * Konfigurera konto-ID och kontonyckel för Spatial Anchors
> * Distribuera och köra på en Android-enhet

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

Kontrollera att du har följande för genomföra den här snabbstarten:
- En Windows-eller macOS-dator:
  - Om du använder Windows:
    - En uppdaterad version av <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019 16.2 +</a>.
    - <a href="https://git-scm.com/download/win" target="_blank">Git för Windows</a>.
    - <a href="https://git-lfs.github.com/">Git-LF</a>.
  - Om du använder macOS:
    - En uppdaterad version av <a href="https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019" target="_blank">Visual Studio för Mac 8.1 +</a>.
    - <a href="https://git-scm.com/download/mac" target="_blank">Git för MacOS</a>.
    - <a href="https://git-lfs.github.com/">Git-LF</a>.
- Den senaste versionen av Xamarin. Android är installerad och körs på valfri plattform. En guide för att installera Xamarin. Android finns i installations guiderna för [Xamarin. Android](https://docs.microsoft.com/xamarin/android/get-started/installation/index) .
- En <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">utvecklaraktiverad</a> och <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore-kapabel</a> Android-enhet.
  - Ytterligare enhets driv rutiner kan krävas för att datorn ska kunna kommunicera med din Android-enhet. Mer information finns [här](https://developer.android.com/studio/run/device.html).
- Din app måste vara mål ARCore **1,8**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Öppna exempelprojektet

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Öppna `Xamarin/SampleXamarin.sln` i Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Konfigurera konto-ID och -nyckel

Nästa steg är att konfigurera appen så att den använder din konto identifierare och konto nyckeln. Du kopierade dem till en text redigerare när du [ställer in resursen för spatiala ankare](#create-a-spatial-anchors-resource).

Öppna `Xamarin/SampleXamarin.Common/AccountDetails.cs`.

Leta upp fältet `SpatialAnchorsAccountKey` och ersätt `Set me` med kontonyckeln.

Leta upp fältet `SpatialAnchorsAccountId` och ersätt `Set me` med kontoidentifieraren.

## <a name="deploy-the-app-to-your-android-device"></a>Distribuera appen till din Android-enhet

Starta Android-enheten, logga in och Anslut den till datorn med en USB-kabel.

Ställ in startprojektet på **SampleXamarin. Android**, ändra **lösningens konfiguration** till **version**och välj den enhet som du vill distribuera till i list rutan enhets väljare.

# <a name="windowstabdeploy-windows"></a>[Windows](#tab/deploy-windows)

![Visual Studio-konfiguration](./media/get-started-xamarin-android/visual-studio-windows-configuration.png)

Välj **felsök** > **Starta fel sökning** för att distribuera och starta din app.

# <a name="macostabdeploy-macos"></a>[macOS](#tab/deploy-macos)

![Visual Studio-konfiguration](./media/get-started-xamarin-android/visual-studio-macos-configuration.jpg)

Välj **kör** > **starta utan fel sökning** för att distribuera och starta din app.

---

I appen väljer du **Basic** för att köra demonstrationen och följer anvisningarna för att placera och återkalla en fäst punkt.

> ![skärm bild 1](./media/get-started-xamarin-android/screenshot-1.jpg)
> ![skärm bild 2](./media/get-started-xamarin-android/screenshot-2.jpg)
> ![skärm bild 3](./media/get-started-xamarin-android/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Självstudie: dela spatiala ankare mellan enheter](../tutorials/tutorial-share-anchors-across-devices.md)
