---
title: 'Snabb start: skapa en Android-app'
description: I den här snabbstarten får du lära dig hur du skapar en Android-app med hjälp av Spatial Anchors.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: b7be4e257fc884c655fe380f69b08797a907fbbb
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2020
ms.locfileid: "96022657"
---
# <a name="quickstart-create-an-android-app-with-azure-spatial-anchors"></a>Snabb start: skapa en Android-app med Azures spatiala ankare

I den här snabbstarten beskrivs hur du skapar en Android-app med hjälp av [Azure Spatial Anchors](../overview.md) i antingen Java eller C++/NDK. Azure Spatial Anchors är en plattformsoberoende utvecklartjänst som du kan använda för att skapa upplevelser med mixad verklighet med hjälp av objekt som bevarar sin plats mellan enheter över tid. När du är klar har du en ARCore Android-app som kan spara och återkalla en spatial fästpunkt.

Du lär dig följande:

> [!div class="checklist"]
> * Skapa ett Spatial Anchors-konto
> * Konfigurera konto-ID och kontonyckel för Spatial Anchors
> * Distribuera och köra på en Android-enhet

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Kontrollera att du har följande så att du kan utföra den här snabbstarten:

- En Windows-eller macOS-dator med <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4 +</a>.
  - Om du kör på Windows, behöver du även <a href="https://git-scm.com/download/win" target="_blank">git för Windows</a> och <a href="https://git-lfs.github.com/">git LF</a>.
  - Om du kör på macOS ska du Hämta git installerat via HomeBrew. Ange följande kommando i en enskild rad i terminalen: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` . Kör sedan `brew install git` och `brew install git-lfs` .
  - Om du vill bygga NDK-exemplet måste du också installera NDK-och CMake 3,6 eller fler SDK-verktyg i Android Studio.
- En <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">utvecklaraktiverad</a> och <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore-kapabel</a> Android-enhet.
  - Ytterligare enhets driv rutiner kan krävas för att datorn ska kunna kommunicera med din Android-enhet. Mer information och anvisningar finns [här](https://developer.android.com/studio/run/device.html) .
- Din app måste ha ARCore- **1.11.0** som mål.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Öppna exempelprojektet

# <a name="java"></a>[Java](#tab/openproject-java)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Hämta `arcore_c_api.h` härifrån [here](https://raw.githubusercontent.com/google-ar/arcore-android-sdk/v1.11.0/libraries/include/arcore_c_api.h) och placera den i `Android\NDK\libraries\include` .

Initiera undermoduler genom att köra följande kommando inifrån den nya klonade lagrings platsen:

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

Nästa steg är att konfigurera appen så att den använder din konto identifierare och konto nyckeln. Du kopierade dem till en text redigerare när du [ställer in resursen för spatiala ankare](#create-a-spatial-anchors-resource).

# <a name="java"></a>[Java](#tab/openproject-java)

Öppna `Android/Java/app/src/main/java/com/microsoft/sampleandroid/AzureSpatialAnchorsManager.java`.

Leta upp fältet `SpatialAnchorsAccountKey` och ersätt `Set me` med kontonyckeln.

Leta upp fältet `SpatialAnchorsAccountId` och ersätt `Set me` med kontoidentifieraren.

Leta upp `SpatialAnchorsAccountDomain` fältet och Ersätt `Set me` med konto domänen.

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

Öppna `Android/NDK/app/src/main/cpp/AzureSpatialAnchorsApplication.cpp`.

Leta upp fältet `SpatialAnchorsAccountKey` och ersätt `Set me` med kontonyckeln.

Leta upp fältet `SpatialAnchorsAccountId` och ersätt `Set me` med kontoidentifieraren.

Leta upp `SpatialAnchorsAccountDomain` fältet och Ersätt `Set me` med konto domänen.

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
> [Självstudie: dela spatiala ankare mellan enheter](../tutorials/tutorial-share-anchors-across-devices.md)
