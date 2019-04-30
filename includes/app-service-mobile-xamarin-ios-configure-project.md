---
ms.openlocfilehash: a69df0cc9ea14a2c9fa172c77663afb1d6861f9b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097549"
---
#### <a name="configure-the-ios-project-in-xamarin-studio"></a>Konfigurera iOS-projekt i Xamarin Studio
1. Öppna i Xamarin.Studio, **Info.plist**, och uppdatera den **paketidentifieraren** med paket-ID som du skapade tidigare med din nya app-ID.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)
2. Rulla ned till **bakgrundslägen**. Välj den **aktivera bakgrundslägen** box och **Remote notifications** box.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)
3. Dubbelklicka på ditt projekt i lösningen panelen för att öppna **Projektalternativ**.
4. Under **skapa**, Välj **iOS paket signering**, och markera den motsvarande identitet och etableringsprofilen du just ställt in för det här projektet.

   ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

   Detta säkerställer att projektet använder den nya profilen för kodsignering. Läs den officiella Xamarin device provisioning-dokumentation, [Xamarin Device Provisioning].

#### <a name="configure-the-ios-project-in-visual-studio"></a>Konfigurera iOS-projektet i Visual Studio
1. Högerklicka på projektet i Visual Studio och klicka sedan på **egenskaper**.
2. I egenskapssidorna klickar du på den **iOS-program** fliken och uppdatera den **identifierare** med det ID som du skapade tidigare.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)
3. I den **iOS paket signering** fliken, markera den motsvarande identitet och en etableringsprofil som du precis har konfigurerat för det här projektet.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    Detta säkerställer att projektet använder den nya profilen för kodsignering. Läs den officiella Xamarin device provisioning-dokumentation, [Xamarin Device Provisioning].
4. Dubbelklicka på filen Info.plist för att öppna den och sedan aktivera **RemoteNotifications** under **bakgrundslägen**.

[Xamarin Device Provisioning]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/