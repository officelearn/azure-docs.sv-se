---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: ec8fb6efab126dcf5556a9abfdf58d1fd69d4212
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "72882322"
---
## <a name="android"></a>[Android](#tab/Android)

Java-android-exemplet stöder delning mellan enheter.
Öppna filen `SharedActivity.java` från exempelmappen i Android Studio. Ange webbadressen som du fick i föregående steg (från din ASP.NET webbapp Azure-distribution) som värde för `SharingAnchorsServiceUrl` i `SharedActivity.java` filen. Ersätt `index.html` url:en `api/anchors`med . Det bör se `https://<app_name>.azurewebsites.net/api/anchors`ut så här: .

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="ios"></a>[iOS](#tab/iOS)

Objective-C iOS-exemplet stöder delning mellan enheter.
Öppna filen `SharedDemoViewController.m` i exempelmappen. Ange webbadressen som du fick i föregående steg (från din ASP.NET webbapp Azure-distribution) som värde för `SharingAnchorsServiceUrl` i `SharedActivity.java` filen. Ersätt `index.html` url:en `api/anchors`med . Det bör se `https://<app_name>.azurewebsites.net/api/anchors`ut så här: .

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarin"></a>[Xamarin](#tab/Xamarin)

Både Xamarin Android- och iOS-exempel stöder delning mellan enheter.
Öppna filen `AccountDetails.cs` i exempelmappen. Ange webbadressen som du fick i föregående steg (från din ASP.NET webbapp Azure-distribution) som värde för `AnchorSharingServiceUrl` i `SharedActivity.java` filen. Ersätt `index.html` url:en `api/anchors`med . Det bör se `https://<app_name>.azurewebsites.net/api/anchors`ut så här: .

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Konfigurera en Android-enhet

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Konfigurera en iOS-enhet

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>Konfigurera kontoidentifieraren och nyckeln

I fönstret **Projekt** går du till `Assets/AzureSpatialAnchorsPlugin/Examples` och öppnar scenfilen `AzureSpatialAnchorsLocalSharedDemo.unity`.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

Navigera till `Assets\AzureSpatialAnchors.Examples\Resources`i **projektfönstret** . Välj `SpatialAnchorSamplesConfig`. Ange sedan **Inspector** `Sharing Anchors Service url` (från din ASP.NET webbapp Azure-distribution) som värde för `Base Sharing Url`, `index.html` `api/anchors`ersätt med . Det bör se `https://<app_name>.azurewebsites.net/api/anchors`ut så här: .

Spara scenen genom att välja **Spara fil** > **.**

## <a name="deploy-to-your-device"></a>Distribuera till din nya enhet

### <a name="deploy-to-android-device"></a>Distribuera till Android-enhet

Logga in på din Android-enhet och anslut den till datorn med hjälp av en USB-kabel.

Öppna **Build Settings** (Bygginställningar) genom att välja **Arkiv** > **Build Settings** (Bygginställningar).

Under **Scener i build**kontrollerar du att alla scener har en bock bredvid dem.

Kontrollera att **Export project** inte är markerat. Välj **Bygg och kör**. Du uppmanas att spara `.apk` filen. Du kan välja vilket namn som helst för det.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>Distribuera till en iOS-enhet

Öppna **Build Settings** (Bygginställningar) genom att välja **Arkiv** > **Build Settings** (Bygginställningar).

Under **Scener i build**kontrollerar du att alla scener har en bock bredvid dem.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

Stoppa appen i Xcode genom att välja **Stoppa**.
