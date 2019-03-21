---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 563c2bd561328561d30acee6910b70d53ef64c6b
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305332"
---
## <a name="set-up-your-device"></a>Konfigurera enheten

### <a name="set-up-an-android-device"></a>Konfigurera en Android-enhet

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Konfigurera en iOS-enhet

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>Konfigurera konto ID och nyckel

I fönstret **Projekt** går du till `Assets/AzureSpatialAnchorsPlugin/Examples` och öppnar scenfilen `AzureSpatialAnchorsLocalSharedDemo.unity`.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

I den **Inspector** fönstret, ange den `Sharing Anchors Service url` (från ASP.NET-webbappen Azure-distribution) som värde för `Base Sharing Url`genom att byta ut `index.html` med `api/anchors`. Det bör se ut så här: `https://<app_name>.azurewebsites.net/api/anchors`.

Spara scenen genom att välja **Arkiv** > **Spara**.

## <a name="to-deploy-the-app-to-an-android-device"></a>Distribuera appen till en Android-enhet

Logga in på din Android-enhet och ansluta den till datorn med hjälp av en USB-kabel.

Öppna **Build Settings** (Bygginställningar) genom att välja **Arkiv** > **Build Settings** (Bygginställningar).

Under **scener i skapa**, markera kryssrutan bredvid den `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` scen och rensa markeringarna från alla andra scener.

Se till att **exportera projekt** inte är markerade. Välj **skapa och köra**. Du uppmanas att spara din `.apk` fil. Du kan välja ett namn för den.

Följ instruktionerna i appen. Du kan välja **Skapa & resurs fästpunkt** eller **hitta delade ankare**. Det första alternativet kan du skapa en fästpunkt som kan finnas senare på samma enhet eller på ett annat. Det andra alternativet, kan om du redan har kört appen, på samma enhet eller på ett annat du hitta tidigare delade fästpunkter.

## <a name="to-deploy-the-app-to-an-ios-device"></a>Distribuera appen till en iOS-enhet

Öppna **Build Settings** (Bygginställningar) genom att välja **Arkiv** > **Build Settings** (Bygginställningar).

Under **scener i skapa**, markera kryssrutan bredvid den `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` scen och rensa markeringarna från alla andra scener.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Följ instruktionerna i appen. Du kan välja **Skapa & resurs fästpunkt** eller **hitta delade ankare**. Det första alternativet kan du skapa en fästpunkt som kan finnas senare på samma enhet eller på ett annat. Det andra alternativet, kan om du redan har kört appen, på samma enhet eller på ett annat du hitta tidigare delade fästpunkter.

Stoppa appen i Xcode genom att välja **stoppa**.
