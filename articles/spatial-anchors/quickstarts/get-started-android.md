---
title: 'Snabbstart: Skapa en Android-app'
description: I den här snabbstarten får du lära dig hur du skapar en Android-app med hjälp av Spatial Anchors.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 0501c8bb1d71c6cff6033fc937cda019c8890056
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "75376468"
---
# <a name="quickstart-create-an-android-app-with-azure-spatial-anchors"></a>Snabbstart: Skapa en Android-app med Azure Spatial Anchors

I den här snabbstarten beskrivs hur du skapar en Android-app med hjälp av [Azure Spatial Anchors](../overview.md) i antingen Java eller C++/NDK. Azure Spatial Anchors är en plattformsoberoende utvecklartjänst som du kan använda för att skapa upplevelser med mixad verklighet med hjälp av objekt som bevarar sin plats mellan enheter över tid. När du är klar har du en ARCore Android-app som kan spara och återkalla en spatial fästpunkt.

Du lär dig följande:

> [!div class="checklist"]
> * Skapa ett Spatial Anchors-konto
> * Konfigurera konto-ID och kontonyckel för Spatial Anchors
> * Distribuera och köra på en Android-enhet

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

Kontrollera att du har följande så att du kan utföra den här snabbstarten:

- En Windows- eller macOS-maskin med <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4+</a>.
  - Om du kör på Windows behöver du också <a href="https://git-scm.com/download/win" target="_blank">Git för Windows</a> och <a href="https://git-lfs.github.com/">Git LFS</a>.
  - Om du kör på macOS, få Git installerat via HomeBrew. Ange följande kommando i en enda `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`rad i terminalen: . Kör sedan `brew install git` `brew install git-lfs`och .
  - För att skapa NDK-exemplet måste du också installera NDK och CMake 3.6 eller större SDK-verktyg i Android Studio.
- En <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">utvecklaraktiverad</a> och <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore-kapabel</a> Android-enhet.
  - Ytterligare drivrutiner kan krävas för att datorn ska kunna kommunicera med din Android-enhet. Se [här](https://developer.android.com/studio/run/device.html) för ytterligare information och instruktioner.
- Appen måste inriktas på ARCore **1.11.0**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Öppna exempelprojektet

# <a name="java"></a>[Java](#tab/openproject-java)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Ladda `arcore_c_api.h` ner [härifrån](https://raw.githubusercontent.com/google-ar/arcore-android-sdk/v1.11.0/libraries/include/arcore_c_api.h) och `Android\NDK\libraries\include`placera den i .

Från den nyligen klonade databasen initierar du undermoduler genom att köra följande kommando:

```console
git submodule update --init --recursive
```

---

Öppna Android Studio.

# <a name="java"></a>[Java](#tab/openproject-java)

Välj **Öppna ett befintligt Android Studio-projekt** och välj det projekt som finns i `Android/Java/`.

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

Välj **Öppna ett befintligt Android Studio-projekt** och välj det projekt som finns i `Android/NDK/`.

---

## <a name="configure-account-identifier-and-key"></a>Konfigurera konto-ID och -nyckel

Nästa steg är att konfigurera appen så att den använder kontoidentifieraren och kontonyckeln. Du kopierade dem till en textredigerare när [du konfigurerade resursen Spatial Anchors](#create-a-spatial-anchors-resource).

# <a name="java"></a>[Java](#tab/openproject-java)

Öppna `Android/Java/app/src/main/java/com/microsoft/sampleandroid/AzureSpatialAnchorsManager.java`.

Leta upp fältet `SpatialAnchorsAccountKey` och ersätt `Set me` med kontonyckeln.

Leta upp fältet `SpatialAnchorsAccountId` och ersätt `Set me` med kontoidentifieraren.

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

Öppna `Android/NDK/app/src/main/cpp/AzureSpatialAnchorsApplication.cpp`.

Leta upp fältet `SpatialAnchorsAccountKey` och ersätt `Set me` med kontonyckeln.

Leta upp fältet `SpatialAnchorsAccountId` och ersätt `Set me` med kontoidentifieraren.

---

## <a name="deploy-the-app-to-your-android-device"></a>Distribuera appen till din Android-enhet

Slå på Android-enheten, logga in och anslut den till datorn via en USB-kabel.

Välj **Kör** från Android Studio-verktygsfältet.

![Android Studio – distribuera och kör](./media/get-started-android/android-studio-deploy-run.png)

Välj Android-enheten i dialogrutan **Select Deployment Target** (Välj distributionsmål) och välj **OK** för att köra appen på Android-enheten.

Följ instruktionerna i appen för att placera och återkalla en fästpunkt.

Stoppa appen genom att välja **Stoppa** från Android Studio-verktygsfältet.

![Android Studio – stoppa](./media/get-started-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Självstudiekurs: Dela rumsliga ankare mellan enheter](../tutorials/tutorial-share-anchors-across-devices.md)
