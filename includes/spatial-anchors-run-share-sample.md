---
author: msftradford
ms.service: spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 9554e04c82299016076f09f85f604af10a94ab4a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185478"
---
## <a name="android"></a>[Android](#tab/Android)

Java Android-exemplet stöder delning mellan enheter.

I Android Studio öppnar du filen *SharedActivity. java* från mappen Samples. 

Ange den URL som du kopierade i föregående steg (från ASP.NET-webbappens Azure-distribution) som värde för `SharingAnchorsServiceUrl` i *SharedActivity. java* -filen. 

Ersätt `index.html` i URL: en med `api/anchors` . Den bör se ut så här: `https://<app_name>.azurewebsites.net/api/anchors` .

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="ios"></a>[iOS](#tab/iOS)

Mål-C iOS-exemplet stöder delning mellan enheter.

Öppna filen *SharedDemoViewController. m* i mappen Samples. 

Ange URL: en som du fick i föregående steg (från ASP.NET-webbappens Azure-distribution) som värde för `SharingAnchorsServiceUrl` i filen *SharedDemoViewController. m* . 

Ersätt `index.html` i URL: en med `api/anchors` . Den bör se ut så här: `https://<app_name>.azurewebsites.net/api/anchors` .

Distribuera appen till enheten. 

När appen har startat väljer du alternativet **Knacka för att starta delad demo** och följer sedan anvisningarna i appen. Du kan välja **Knacka för att hitta ankare med dess ankar nummer** eller **Knacka för att skapa fäst punkt och spara det till tjänsten**.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarin"></a>[Xamarin](#tab/Xamarin)

Både Xamarin Android och iOS-exempel stöder delning mellan enheter.

Öppna filen *AccountDetails.cs* i mappen Samples. 

Ange URL: en som du fick i föregående steg (från ASP.NET-webbappens Azure-distribution) som värde för `AnchorSharingServiceUrl` i *AccountDetails.cs* -filen. 

Ersätt `index.html` i URL: en med `api/anchors` . Den bör se ut så här: `https://<app_name>.azurewebsites.net/api/anchors` .

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Konfigurera en Android-enhet

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Konfigurera en iOS-enhet

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

Gå till i fönstret **projekt** `Assets\AzureSpatialAnchors.Examples\Resources` . 

Välj **SpatialAnchorSamplesConfig**. I fönstret **kontrollant** anger du sedan `Sharing Anchors Service` webb adressen (från din ASP.net-Web App Azure-distribution) som värde för `Base Sharing Url` . Ersätt `index.html` med `api/anchors`. Den bör se ut så här: `https://<app_name>.azurewebsites.net/api/anchors` .

Spara scenen genom att välja **Arkiv**  >  **Spara**.

## <a name="deploy-to-your-device"></a>Distribuera till din nya enhet

### <a name="deploy-to-an-android-device"></a>Distribuera till en Android-enhet

Logga in på din Android-enhet och Anslut den till datorn med hjälp av en USB-kabel.

Öppna **Build Settings** (Bygginställningar) genom att välja **Arkiv** > **Build Settings** (Bygginställningar).

Under **scener i bygge**, se till att varje scen har en bock bredvid sig.

Kontrol lera att **export projektet** inte har någon bock. Välj **skapa och kör**. Du uppmanas att spara din *. apk* -fil. Du kan välja ett namn för det.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>Distribuera till en iOS-enhet

Öppna **Build Settings** (Bygginställningar) genom att välja **Arkiv** > **Build Settings** (Bygginställningar).

Under **scener i bygge**, se till att varje scen har en bock bredvid sig.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

Stoppa appen genom att välja **stoppa** i Xcode.
