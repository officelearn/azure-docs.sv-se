---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: bfeb8bddf5fe3b4a76e662aed6c5a07439d2f1cf
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57908646"
---
## <a name="set-up-your-device"></a>Konfigurera enheten

### <a name="set-up-an-android-device"></a>Konfigurera en Android-enhet

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Konfigurera en iOS-enhet

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Konfigurera kontoidentifierare och nyckel

I fönstret **Projekt** går du till `Assets/AzureSpatialAnchorsPlugin/Examples` och öppnar scenfilen `AzureSpatialAnchorsLocalSharedDemo.unity`.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

I fönstret **Inspector** anger du `Sharing Anchors Service url` (från din Azure-distribution av ASP.NET-webbapp) som värde för `Base Sharing Url`. Ersätt `index.html` med `api/anchors`. Det bör se ut så här: `https://<app_name>.azurewebsites.net/api/anchors`.

Spara scenen genom att välja **Arkiv** -> **Spara**.

## <a name="to-deploy-to-an-android-device"></a>Distribuera till en Android-enhet

Logga in på din Android-enhet och ansluta den till datorn via en USB-kabel.

Öppna **Build Settings** (Bygginställningar) genom att välja **Arkiv** -> **Build Settings** (Bygginställningar).

Under **Scenes In Build** (Scener i bygget) placerar du en kryssmarkering intill scenen `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` och rensar kryssmarkeringarna från alla andra scener.

Se till att kryssrutan **Exportera projekt** inte har en kryssmarkering. Klicka på **Skapa och kör**. Du uppmanas att spara din `.apk`-fil. Du kan välja ett valfritt namn för den.

Följ instruktionerna i appen. Du kan välja **Create & Share Anchor** (Skapa och dela fästpunkt) eller **Locate Shared Anchor** (Hitta delad fästpunkt). Med det första alternativet kan du skapa en fästpunkt som senare kan hittas på samma enhet eller en annan. Med det andra alternativet, om du tidigare har kört appen antingen på samma enhet eller en annan, kan du hitta fästpunkter som delats tidigare.

## <a name="to-deploy-to-an-ios-device"></a>Distribuera till en iOS-enhet

Öppna **Build Settings** (Bygginställningar) genom att välja **Arkiv** -> **Build Settings** (Bygginställningar).

Under **Scenes In Build** (Scener i bygget) placerar du en kryssmarkering intill scenen `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` och rensar kryssmarkeringarna från alla andra scener.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Följ instruktionerna i appen. Du kan välja **Create & Share Anchor** (Skapa och dela fästpunkt) eller **Locate Shared Anchor** (Hitta delad fästpunkt). Med det första alternativet kan du skapa en fästpunkt som senare kan hittas på samma enhet eller en annan. Med det andra alternativet, om du tidigare har kört appen antingen på samma enhet eller en annan, kan du hitta fästpunkter som delats tidigare.

I Xcode stoppar du appen genom att trycka på **Stoppa**.
