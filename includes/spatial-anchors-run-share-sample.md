---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: b46a2b18309851bbe2934980137a53d2de6f6efc
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "67135353"
---
## <a name="set-up-your-device-in-unity"></a>Konfigurera din enhet i Unity

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

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

Under **kulisserna i skapa**, se till att alla scener har en bock bredvid dem.

Se till att **exportera projekt** inte är markerade. Välj **skapa och köra**. Du uppmanas att spara din `.apk` fil. Du kan välja ett namn för den.

När appen startar i den **väljer en Demo** dialogrutan Använd pilarna vänster eller höger för att välja den **LocalShare** alternativet och tryck på **Gå!** . Följ instruktionerna i appen. Du kan välja **Skapa & resurs fästpunkt** eller **hitta delade ankare**.

Det första scenariot kan du skapa en fästpunkt som kan finnas senare på samma enhet eller på ett annat.
I det andra scenariot, kan om du redan har kört appen, på samma enhet eller på ett annat du hitta tidigare delade fästpunkter. När du väljer ditt scenario hjälper appen dig med ytterligare instruktioner runt vad du gör. Du kommer till exempel bli ombedd att flytta din enhet att samla in miljöinformation. Vid ett senare tillfälle du placera en fästpunkt i hela världen, vänta tills den är att spara och så vidare.

### <a name="deploy-to-an-ios-device"></a>Distribuera till en iOS-enhet

Öppna **Build Settings** (Bygginställningar) genom att välja **Arkiv** > **Build Settings** (Bygginställningar).

Under **kulisserna i skapa**, se till att alla scener har en bock bredvid dem.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

När appen startar i den **väljer en Demo** dialogrutan Använd pilarna vänster eller höger för att välja den **LocalShare** alternativet och tryck på **Gå!** . Följ instruktionerna i appen. Du kan välja **Skapa & resurs fästpunkt** eller **hitta delade ankare**.

Det första scenariot kan du skapa en fästpunkt som kan finnas senare på samma enhet eller på ett annat.
I det andra scenariot, kan om du redan har kört appen, på samma enhet eller på ett annat du hitta tidigare delade fästpunkter. När du väljer ditt scenario hjälper appen dig med ytterligare instruktioner runt vad du gör. Du kommer till exempel bli ombedd att flytta din enhet att samla in miljöinformation. Vid ett senare tillfälle du placera en fästpunkt i hela världen, vänta tills den är att spara och så vidare.

Stoppa appen i Xcode genom att välja **stoppa**.
