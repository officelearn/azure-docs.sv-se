---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 8/27/2020
ms.author: rgarcia
ms.openlocfilehash: efd5ff494620d4fab3fb904d9bcf054b57a3290b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91358830"
---
## <a name="android"></a>[Android](#tab/Android)

Java Android-exemplet stöder delning mellan enheter.
Öppna filen `SharedActivity.java` från mappen Samples i Android Studio. Ange URL: en som du fick i föregående steg (från ASP.NET-webbappens Azure-distribution) som värde för `SharingAnchorsServiceUrl` i `SharedActivity.java` filen. Ersätt `index.html` i URL: en med `api/anchors` . Den bör se ut så här: `https://<app_name>.azurewebsites.net/api/anchors` .

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="ios"></a>[iOS](#tab/iOS)

Mål-C iOS-exemplet stöder delning mellan enheter.
Öppna filen `SharedDemoViewController.m` i mappen Samples. Ange URL: en som du fick i föregående steg (från ASP.NET-webbappens Azure-distribution) som värde för `SharingAnchorsServiceUrl` i `SharedDemoViewController.m` filen. Ersätt `index.html` i URL: en med `api/anchors` . Den bör se ut så här: `https://<app_name>.azurewebsites.net/api/anchors` .

Distribuera appen till enheten. När appen har startat väljer du alternativet **Knacka för att starta delad demo** . Följ instruktionerna i appen. Du kan välja **Knacka för att hitta ankare med dess ankar nummer** eller **Knacka för att skapa fäst punkt och spara det till tjänsten**.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarin"></a>[Xamarin](#tab/Xamarin)

Både Xamarin Android och iOS-exempel stöder delning mellan enheter.
Öppna filen `AccountDetails.cs` i mappen Samples. Ange URL: en som du fick i föregående steg (från ASP.NET-webbappens Azure-distribution) som värde för `AnchorSharingServiceUrl` i `AccountDetails.cs` filen. Ersätt `index.html` i URL: en med `api/anchors` . Den bör se ut så här: `https://<app_name>.azurewebsites.net/api/anchors` .

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Konfigurera en Android-enhet

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Konfigurera en iOS-enhet

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

I fönstret **projekt** navigerar du till `Assets\AzureSpatialAnchors.Examples\Resources` . Välj `SpatialAnchorSamplesConfig`. I fönstret **kontrollant** anger du sedan `Sharing Anchors Service url` (från din ASP.NET-webbapp Azure-distribution) som värde för `Base Sharing Url` och ersätter `index.html` med `api/anchors` . Den bör se ut så här: `https://<app_name>.azurewebsites.net/api/anchors` .

Spara scenen genom att välja **Arkiv**  >  **Spara**.

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
