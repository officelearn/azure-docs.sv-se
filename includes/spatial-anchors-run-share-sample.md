---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 397a8a9b07b4d7a88d0345399ac4abcc3e738a82
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60681392"
---
## <a name="set-up-your-device"></a>Konfigurera enheten

Öppna projektet i i Unity, den `Unity` mapp.

![Unity-fönstret](./media/spatial-anchors-unity/unity-window.png)

### <a name="set-up-an-android-device"></a>Konfigurera en Android-enhet

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Konfigurera en iOS-enhet

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>Konfigurera konto ID och nyckel

I fönstret **Projekt** går du till `Assets/AzureSpatialAnchorsPlugin/Examples` och öppnar scenfilen `AzureSpatialAnchorsLocalSharedDemo.unity`.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

I den **Inspector** fönstret, ange den `Sharing Anchors Service url` (från ASP.NET-webbappen Azure-distribution) som värde för `Base Sharing Url`genom att byta ut `index.html` med `api/anchors`. Det bör se ut så här: `https://<app_name>.azurewebsites.net/api/anchors`.

Spara scenen genom att välja **Arkiv** > **Spara**.

## <a name="deploy-to-your-device"></a>Distribuera till din nya enhet

### <a name="deploy-to-android-device"></a>Distribuera till Android-enhet

Logga in på din Android-enhet och ansluta den till datorn med hjälp av en USB-kabel.

Öppna **Build Settings** (Bygginställningar) genom att välja **Arkiv** > **Build Settings** (Bygginställningar).

Under **scener i skapa**, markera kryssrutan bredvid den `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` scen och rensa markeringarna från alla andra scener.

Se till att **exportera projekt** inte är markerade. Välj **skapa och köra**. Du uppmanas att spara din `.apk` fil. Du kan välja ett namn för den.

Följ instruktionerna i appen. Du kan välja **Skapa & resurs fästpunkt** eller **hitta delade ankare**. Det första scenariot kan du skapa en fästpunkt som kan finnas senare på samma enhet eller på ett annat. I det andra scenariot, kan om du redan har kört appen, på samma enhet eller på ett annat du hitta tidigare delade fästpunkter. När du väljer ditt scenario hjälper appen dig med ytterligare instruktioner runt vad gör jag. Du kommer till exempel bli ombedd att flytta din enhet för att samla in miljöinformation. Vid ett senare tillfälle kommer du placera en fästpunkt i hela världen, vänta medan den upp och så vidare.

### <a name="deploy-to-an-ios-device"></a>Distribuera till en iOS-enhet

Öppna **Build Settings** (Bygginställningar) genom att välja **Arkiv** > **Build Settings** (Bygginställningar).

Under **scener i skapa**, markera kryssrutan bredvid den `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` scen och rensa markeringarna från alla andra scener.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Följ instruktionerna i appen. Du kan välja **Skapa & resurs fästpunkt** eller **hitta delade ankare**. Det första scenariot kan du skapa en fästpunkt som kan finnas senare på samma enhet eller på ett annat. I det andra scenariot, kan om du redan har kört appen, på samma enhet eller på ett annat du hitta tidigare delade fästpunkter. När du väljer ditt scenario hjälper appen dig med ytterligare instruktioner runt vad gör jag. Du kommer till exempel bli ombedd att flytta din enhet för att samla in miljöinformation. Vid ett senare tillfälle kommer du placera en fästpunkt i hela världen, vänta medan den upp och så vidare.

Stoppa appen i Xcode genom att välja **stoppa**.
