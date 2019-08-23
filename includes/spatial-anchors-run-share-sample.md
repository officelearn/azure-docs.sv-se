---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 1007533df077c58d9e4d57f9e86b035730ea917f
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69903963"
---
## <a name="set-up-your-device-in-unity"></a>Konfigurera enheten i Unity

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Konfigurera en Android-enhet

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Konfigurera en iOS-enhet

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>Konfigurera konto identifieraren och nyckeln

I fönstret **Projekt** går du till `Assets/AzureSpatialAnchorsPlugin/Examples` och öppnar scenfilen `AzureSpatialAnchorsLocalSharedDemo.unity`.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

I fönstret **projekt** navigerar du till `Assets\AzureSpatialAnchors.Examples\Resources`. Välj `SpatialAnchorSamplesConfig`. I fönstret **kontrollant** anger `Sharing Anchors Service url` du sedan (från din ASP.NET-webbapp Azure-distribution) som värde för `Base Sharing Url`och ersätter `index.html` med `api/anchors`. Den bör se ut så här `https://<app_name>.azurewebsites.net/api/anchors`:.

Spara scenen genom att välja **Arkiv** > **Spara**.

## <a name="deploy-to-your-device"></a>Distribuera till din nya enhet

### <a name="deploy-to-android-device"></a>Distribuera till Android-enhet

Logga in på din Android-enhet och Anslut den till datorn med hjälp av en USB-kabel.

Öppna **Build Settings** (Bygginställningar) genom att välja **Arkiv** > **Build Settings** (Bygginställningar).

Se till att alla scener har en bock bredvid dem under **scener i build**.

Kontrol lera att **export projekt** inte har en bock markering. Välj **skapa och kör**. Du uppmanas att spara `.apk` filen. Du kan välja ett namn för det.

När appen startar går du till dialog rutan **Välj en demo** och väljer alternativet **LocalShare** genom att trycka på **gå**till. Följ instruktionerna i appen. Du kan välja **skapa & resurs ankare** eller **hitta delade ankare**.

Med det första scenariot kan du skapa en fäst punkt som kan finnas senare på samma enhet eller på en annan.
Det andra scenariot, om du redan har kört appen, antingen på samma enhet eller på en annan, kan du hitta tidigare delade ankare. När du har valt ditt scenario hjälper appen dig med ytterligare instruktioner kring vad du ska göra. Till exempel uppmanas du att flytta enheten runt till samla in miljö information. Senare kan du placera ett ankare i världen, vänta tills det har sparats och så vidare.

### <a name="deploy-to-an-ios-device"></a>Distribuera till en iOS-enhet

Öppna **Build Settings** (Bygginställningar) genom att välja **Arkiv** > **Build Settings** (Bygginställningar).

Se till att alla scener har en bock bredvid dem under **scener i build**.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

När appen startar går du till dialog rutan **Välj en demo** och väljer alternativet **LocalShare** genom att trycka på **gå**till. Följ instruktionerna i appen. Du kan välja **skapa & resurs ankare** eller **hitta delade ankare**.

Med det första scenariot kan du skapa en fäst punkt som kan finnas senare på samma enhet eller på en annan.
Det andra scenariot, om du redan har kört appen, antingen på samma enhet eller på en annan, kan du hitta tidigare delade ankare. När du har valt ditt scenario hjälper appen dig med ytterligare instruktioner kring vad du ska göra. Till exempel uppmanas du att flytta enheten runt till samla in miljö information. Senare kan du placera ett ankare i världen, vänta tills det har sparats och så vidare.

Stoppa appen genom att välja **stoppa**i Xcode.
