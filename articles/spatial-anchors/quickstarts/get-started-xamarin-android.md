---
title: 'Snabbstart: Skapa en Xamarin Android-app'
description: I den här snabbstarten får du lära dig hur du skapar en Android-app med Xamarin med spatialankare.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/22/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 94906d645ec38a24d54536ee8aa93e7418c8dc35
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "75465173"
---
# <a name="quickstart-create-a-xamarin-android-app-with-azure-spatial-anchors"></a>Snabbstart: Skapa en Xamarin Android-app med Azure Spatial Anchors

Den här snabbstarten beskriver hur du skapar en Android-app med Xamarin med [Azure Spatial Anchors](../overview.md). Azure Spatial Anchors är en plattformsoberoende utvecklartjänst som du kan använda för att skapa upplevelser med mixad verklighet med hjälp av objekt som bevarar sin plats mellan enheter över tid. När du är klar har du en Android-app som kan spara och återkalla ett rumsligt ankare.

Du lär dig följande:

> [!div class="checklist"]
> * Skapa ett Spatial Anchors-konto
> * Konfigurera konto-ID och kontonyckel för Spatial Anchors
> * Distribuera och köra på en Android-enhet

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

Kontrollera att du har följande så att du kan utföra den här snabbstarten:
- En Windows- eller macOS-dator:
  - Om du använder Windows:
    - En uppdaterad version av <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019 16.2+</a>.
    - <a href="https://git-scm.com/download/win" target="_blank">Git för Windows</a>.
    - <a href="https://git-lfs.github.com/">Git AKU</a>.
  - Om du använder macOS:
    - En uppdaterad version av <a href="https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019" target="_blank">Visual Studio för Mac 8.1+</a>.
    - <a href="https://git-scm.com/download/mac" target="_blank">Git för macOS</a>.
    - <a href="https://git-lfs.github.com/">Git AKU</a>.
- Den senaste versionen av Xamarin.Android installeras och körs på din plattform val. En guide till installation av Xamarin.Android finns i installationsguiderna för [Xamarin.Android.](https://docs.microsoft.com/xamarin/android/get-started/installation/index)
- En <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">utvecklaraktiverad</a> och <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore-kapabel</a> Android-enhet.
  - Ytterligare drivrutiner kan krävas för att datorn ska kunna kommunicera med din Android-enhet. Mer information finns [här](https://developer.android.com/studio/run/device.html).
- Appen måste inriktas på ARCore **1.8**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Öppna exempelprojektet

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Öppna `Xamarin/SampleXamarin.sln` i Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Konfigurera konto-ID och -nyckel

Nästa steg är att konfigurera appen så att den använder kontoidentifieraren och kontonyckeln. Du kopierade dem till en textredigerare när [du konfigurerade resursen Spatial Anchors](#create-a-spatial-anchors-resource).

Öppna `Xamarin/SampleXamarin.Common/AccountDetails.cs`.

Leta upp fältet `SpatialAnchorsAccountKey` och ersätt `Set me` med kontonyckeln.

Leta upp fältet `SpatialAnchorsAccountId` och ersätt `Set me` med kontoidentifieraren.

## <a name="deploy-the-app-to-your-android-device"></a>Distribuera appen till din Android-enhet

Slå på Android-enheten, logga in och anslut den till datorn med en USB-kabel.

Ställ in startprojektet på **SampleXamarin.Android**, ändra **lösningskonfigurationen** till **Release**och välj den enhet som du vill distribuera till i listrutan enhetsväljare.

# <a name="windows"></a>[Windows](#tab/deploy-windows)

![Visual Studio-konfiguration](./media/get-started-xamarin-android/visual-studio-windows-configuration.png)

Välj **Felsökning** > **Starta felsökning för** att distribuera och starta din app.

# <a name="macos"></a>[Macos](#tab/deploy-macos)

![Visual Studio-konfiguration](./media/get-started-xamarin-android/visual-studio-macos-configuration.jpg)

Välj **Kör** > **start utan felsökning för** att distribuera och starta appen.

---

I appen väljer du **Grundläggande** för att köra demon och följ instruktionerna för att placera och återkalla ett ankare.

> ![Skärmdump 1](./media/get-started-xamarin-android/screenshot-1.jpg)
> ![Skärmdump](./media/get-started-xamarin-android/screenshot-2.jpg)
> ![2 Skärmdump 3](./media/get-started-xamarin-android/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Självstudiekurs: Dela rumsliga ankare mellan enheter](../tutorials/tutorial-share-anchors-across-devices.md)
