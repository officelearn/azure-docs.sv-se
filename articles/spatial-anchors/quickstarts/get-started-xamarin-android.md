---
title: Snabb start – skapa en Xamarin Android-app med moln rums ankare | Microsoft Docs
description: I den här snabb starten får du lära dig hur du skapar en Android-app med Xamarin med hjälp av spatiala ankare.
author: craigktreasure
manager: virivera
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/22/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 0958961f1e66be130dd2be816f6002dd34465dc6
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68931660"
---
# <a name="quickstart-create-a-xamarin-android-app-with-azure-spatial-anchors"></a>Snabbstart: Skapa en Xamarin Android-app med avstånds ankare för Azure

Den här snabb starten beskriver hur du skapar en Android-app med Xamarin med hjälp av [Azure spatiala ankare](../overview.md). Azure Spatial Anchors är en plattformsoberoende utvecklartjänst som du kan använda för att skapa upplevelser med mixad verklighet med hjälp av objekt som bevarar sin plats mellan enheter över tid. När du är klar har du en Android-app som kan spara och återkalla en spatial fäst punkt.

Du lär dig följande:

> [!div class="checklist"]
> * Skapa ett Spatial Anchors-konto
> * Konfigurera kontoidentifierare och kontonyckel för Spatial Anchors
> * Distribuera och köra på en Android-enhet

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Kontrollera att du har följande så att du kan utföra den här snabbstarten:
- En Windows-eller macOS-dator:
  - Om du använder Windows:
    - En uppdaterad version av <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019 16.2 +</a>.
    - <a href="https://git-scm.com/download/win" target="_blank">Git för Windows</a>
  - Om du använder macOS:
    - En uppdaterad version av <a href="https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019" target="_blank">Visual Studio för Mac 8.1 +</a>.
    - <a href="https://git-scm.com/download/mac" target="_blank">Git för macOS</a>
- Den senaste versionen av Xamarin. Android är installerad och körs på valfri plattform. En guide för att installera Xamarin. Android finns i installations guiderna för [Xamarin. Android](https://docs.microsoft.com/xamarin/android/get-started/installation/index) .
- En <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">utvecklaraktiverad</a> och <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore-kapabel</a> Android-enhet.
  - Ytterligare enhets driv rutiner kan krävas för att datorn ska kunna kommunicera med din Android-enhet. Mer information finns [här](https://developer.android.com/studio/run/device.html).
- Din app måste vara mål ARCore **1,8**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Öppna exempelprojektet

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Öppna `Xamarin/SampleXamarin.sln` i Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Konfigurera kontoidentifierare och nyckel

Nästa steg är att konfigurera appen så att den använder din konto identifierare och konto nyckeln. Du kopierade dem till en text redigerare när du [ställer in resursen för spatiala ankare](#create-a-spatial-anchors-resource).

Öppna `Xamarin/SampleXamarin.Common/AccountDetails.cs`.

Leta upp fältet `SpatialAnchorsAccountKey` och ersätt `Set me` med kontonyckeln.

Leta upp fältet `SpatialAnchorsAccountId` och ersätt `Set me` med kontoidentifieraren.

## <a name="deploy-the-app-to-your-android-device"></a>Distribuera appen till din Android-enhet

Starta Android-enheten, logga in och Anslut den till datorn med en USB-kabel.

Ställ in startprojektet på **SampleXamarin. Android**, ändra **lösningens konfiguration** till **version**och välj den enhet som du vill distribuera till i list rutan enhets väljare.

# <a name="windowstabdeploy-windows"></a>[Windows](#tab/deploy-windows)

![Visual Studio-konfiguration](./media/get-started-xamarin-android/visual-studio-windows-configuration.png)

Välj **Felsök** > **Starta fel sökning** för att distribuera och starta din app.

# <a name="macostabdeploy-macos"></a>[macOS](#tab/deploy-macos)

![Visual Studio-konfiguration](./media/get-started-xamarin-android/visual-studio-macos-configuration.jpg)

Välj **Kör** > **starta utan fel sökning** för att distribuera och starta din app.

---

I appen väljer du **Basic** för att köra demonstrationen och följer anvisningarna för att placera och återkalla en fäst punkt.

> ![Skärm bild](./media/get-started-xamarin-android/screenshot-1.jpg)
> 1![skärmdump](./media/get-started-xamarin-android/screenshot-2.jpg)
> 2![, skärm bild 3](./media/get-started-xamarin-android/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Självstudier: Dela Spatial Anchors mellan olika enheter](../tutorials/tutorial-share-anchors-across-devices.md)
