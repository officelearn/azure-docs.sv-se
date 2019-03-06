---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 5f1e0153b1f919bc9d7e921d2a1b3ae745b2b01f
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753524"
---
## <a name="open-the-sample-project-in-unity"></a>Öppna exempelprojektet i Unity

[!INCLUDE [Clone Sample Repo](spatial-anchors-clone-sample-repository.md)]

## <a name="to-set-up-for-an-android-device"></a>Konfigurera för en Android-enhet

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

## <a name="to-set-up-for-an-ios-device"></a>Konfigurera för en iOS-enhet

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Konfigurera kontoidentifierare och nyckel

I fönstret **Projekt** går du till `Assets/AzureSpatialAnchorsPlugin/Examples` och öppnar scenfilen `AzureSpatialAnchorsLocalSharedDemo.unity`.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

I fönstret **Inspector** anger du `Sharing Anchors Service url` (från din Azure-distribution av ASP.NET-webbapp) som värde för `Base Sharing Url`. Ersätt `index.html` med `api/anchors`. Det bör se ut så här: `https://<app_name>.azurewebsites.net/api/anchors`.

Spara scenen genom att välja **Arkiv** -> **Spara**.

## <a name="to-deploy-to-an-android-device"></a>Distribuera till en Android-enhet

Slå på Android-enheten, logga in och anslut den till datorn via en USB-kabel.

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
