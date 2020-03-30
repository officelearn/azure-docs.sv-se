---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a69df0cc9ea14a2c9fa172c77663afb1d6861f9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187843"
---
#### <a name="configure-the-ios-project-in-xamarin-studio"></a>Konfigurera iOS-projektet i Xamarin Studio
1. Öppna **Info.plist**i Xamarin.Studio och uppdatera **buntidentifieraren** med det paket-ID som du skapade tidigare med ditt nya app-ID.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)
2. Bläddra ned till **bakgrundslägen**. Markera rutan **Aktivera bakgrundslägen** och rutan **Fjärrmeddelanden.**

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)
3. Dubbelklicka på projektet på lösningspanelen för att öppna **Projektalternativ**.
4. Under **Skapa**väljer du **iOS Bundle Signing**och väljer motsvarande identitets- och etableringsprofil som du just har ställt in för det här projektet.

   ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

   Detta säkerställer att projektet använder den nya profilen för kodsignering. Information om tillhandahållande av [Xamarin-enhet]finns i Xamarin Device Provisioning .

#### <a name="configure-the-ios-project-in-visual-studio"></a>Konfigurera iOS-projektet i Visual Studio
1. Högerklicka på projektet i Visual Studio och klicka sedan på **Egenskaper**.
2. På egenskapssidorna klickar du på fliken **iOS-program** och uppdaterar **identifieraren** med det ID som du skapade tidigare.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)
3. På fliken **signering av iOS-paket** väljer du motsvarande identitets- och etableringsprofil som du just har ställt in för det här projektet.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    Detta säkerställer att projektet använder den nya profilen för kodsignering. Information om tillhandahållande av [Xamarin-enhet]finns i Xamarin Device Provisioning .
4. Dubbelklicka på Info.plist för att öppna den och aktivera sedan **RemoteNotifications** under **Bakgrundslägen**.

[Xamarin-enhetsetablering]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/
