---
title: Lägg till push-meddelanden i Xamarin.Android-appen
description: Lär dig hur du använder Azure App Service och Azure Notification Hubs för att skicka push-meddelanden till din Xamarin.Android-app.
ms.assetid: 6f7e8517-e532-4559-9b07-874115f4c65b
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 5657be0dbaeb46f8f899a9b4a2f8ba9b4fe9ebaa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249313"
---
# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Lägg till push-meddelanden i Xamarin.Android-appen

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Översikt

I den här självstudien lägger du till push-meddelanden i [snabbstartsprojektet Xamarin.Android](app-service-mobile-windows-store-dotnet-get-started.md) så att ett push-meddelande skickas till enheten varje gång en post infogas.

Om du inte använder det hämtade snabbstartsserverprojektet behöver du tilläggspaketet för push-meddelande. Mer information finns i guiden [Arbeta med .NET-server för server för server för Azure Mobile Apps.](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)

## <a name="prerequisites"></a>Krav

Den här självstudien kräver installationen:

* Ett aktivt Google-konto. Du kan registrera dig för ett Google-konto på [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).
* [Google Cloud Messaging-klientkomponent](https://components.xamarin.com/view/GCMClient/).

## <a name="configure-a-notification-hub"></a><a name="configure-hub"></a>Konfigurera en meddelandehubb

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="enable-firebase-cloud-messaging"></a><a id="register"></a>Aktivera Firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-azure-to-send-push-requests"></a>Konfigurera Azure för att skicka push-begäranden

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a><a id="update-server"></a>Uppdatera serverprojektet för att skicka push-meddelanden

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-the-client-project-for-push-notifications"></a><a id="configure-app"></a>Konfigurera klientprojektet för push-meddelanden

[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a name="add-push-notifications-code-to-your-app"></a><a id="add-push"></a>Lägga till push-meddelanden i appen

[!INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test-push-notifications-in-your-app"></a><a name="test"></a>Testa push-meddelanden i appen

Du kan testa appen med hjälp av en virtuell enhet i emulatorn. Det finns ytterligare konfigurationssteg som krävs när du kör på en emulator.

1. Den virtuella enheten måste ha Google API:er inställda som mål i AVD-hanteraren (Android Virtual Device).

    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. Lägg till ett Google-konto på Android-enheten genom att klicka på**Lägg till konto****för** >  **appar** > och följ sedan anvisningarna.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. Kör todolist-appen som tidigare och infoga ett nytt todo-objekt. Den här gången visas en meddelandeikon i meddelandefältet. Du kan öppna meddelandelådan för att visa hela texten i meddelandet.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)

<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: https://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: https://components.xamarin.com/view/azure-mobile-services/
