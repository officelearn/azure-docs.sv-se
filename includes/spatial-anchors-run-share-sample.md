---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: ec8fb6efab126dcf5556a9abfdf58d1fd69d4212
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "72882322"
---
## <a name="android"></a>[Android](#tab/Android)

Java Android-exemplet stöder delning mellan enheter.
Öppna filen `SharedActivity.java` från mappen Samples i Android Studio. Ange URL: en som du fick i föregående steg (från ASP.NET-webbappens Azure-distribution) som `SharingAnchorsServiceUrl` värde för `SharedActivity.java` i filen. Ersätt `index.html` i URL: en med `api/anchors`. Den bör se ut så här `https://<app_name>.azurewebsites.net/api/anchors`:.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="ios"></a>[iOS](#tab/iOS)

Mål-C iOS-exemplet stöder delning mellan enheter.
Öppna filen `SharedDemoViewController.m` i mappen Samples. Ange URL: en som du fick i föregående steg (från ASP.NET-webbappens Azure-distribution) som `SharingAnchorsServiceUrl` värde för `SharedActivity.java` i filen. Ersätt `index.html` i URL: en med `api/anchors`. Den bör se ut så här `https://<app_name>.azurewebsites.net/api/anchors`:.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarin"></a>[Xamarin](#tab/Xamarin)

Både Xamarin Android och iOS-exempel stöder delning mellan enheter.
Öppna filen `AccountDetails.cs` i mappen Samples. Ange URL: en som du fick i föregående steg (från ASP.NET-webbappens Azure-distribution) som `AnchorSharingServiceUrl` värde för `SharedActivity.java` i filen. Ersätt `index.html` i URL: en med `api/anchors`. Den bör se ut så här `https://<app_name>.azurewebsites.net/api/anchors`:.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Konfigurera en Android-enhet

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Konfigurera en iOS-enhet

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>Konfigurera konto identifieraren och nyckeln

I fönstret **Projekt** går du till `Assets/AzureSpatialAnchorsPlugin/Examples` och öppnar scenfilen `AzureSpatialAnchorsLocalSharedDemo.unity`.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

I fönstret **projekt** navigerar du till `Assets\AzureSpatialAnchors.Examples\Resources`. Välj `SpatialAnchorSamplesConfig`. I fönstret **kontrollant** anger du `Sharing Anchors Service url` sedan (från din ASP.NET-webbapp Azure-distribution) som värde `Base Sharing Url`för och ersätter `index.html` med. `api/anchors` Den bör se ut så här `https://<app_name>.azurewebsites.net/api/anchors`:.

Spara scenen genom att välja **Arkiv** > **Spara**.

## <a name="deploy-to-your-device"></a>Distribuera till din nya enhet

### <a name="deploy-to-android-device"></a>Distribuera till Android-enhet

Logga in på din Android-enhet och Anslut den till datorn med hjälp av en USB-kabel.

Öppna **Build Settings** (Bygginställningar) genom att välja **Arkiv** > **Build Settings** (Bygginställningar).

Se till att alla scener har en bock bredvid dem under **scener i build**.

Kontrol lera att **export projekt** inte har en bock markering. Välj **skapa och kör**. Du uppmanas att spara `.apk` filen. Du kan välja ett namn för det.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>Distribuera till en iOS-enhet

Öppna **Build Settings** (Bygginställningar) genom att välja **Arkiv** > **Build Settings** (Bygginställningar).

Se till att alla scener har en bock bredvid dem under **scener i build**.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

Stoppa appen genom att välja **stoppa**i Xcode.
