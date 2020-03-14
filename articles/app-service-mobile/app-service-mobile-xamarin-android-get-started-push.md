---
title: Lägg till push-meddelanden i Xamarin.Android-appen
description: Lär dig hur du använder Azure App Service och Azure Notification Hubs för att skicka push-meddelanden till din Xamarin. Android-app.
ms.assetid: 6f7e8517-e532-4559-9b07-874115f4c65b
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 5657be0dbaeb46f8f899a9b4a2f8ba9b4fe9ebaa
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79249313"
---
# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Lägg till push-meddelanden i Xamarin.Android-appen

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Översikt

I den här självstudien lägger du till push-meddelanden i [snabb starts projektet Xamarin. Android](app-service-mobile-windows-store-dotnet-get-started.md) så att ett push-meddelande skickas till enheten varje gång en post infogas.

Om du inte använder det hämtade snabb starts Server projektet behöver du ett tilläggs paket för push-meddelanden. Mer information finns i avsnittet [arbeta med .NET-Server delen Server SDK för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) guide.

## <a name="prerequisites"></a>Förutsättningar

I den här självstudien krävs installationen:

* Ett aktivt Google-konto. Du kan registrera dig för ett Google-konto på [accounts.Google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).
* [Google Cloud messaging klient komponent](https://components.xamarin.com/view/GCMClient/).

## <a name="configure-hub"></a>Konfigurera en Notification Hub

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Aktivera Firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-azure-to-send-push-requests"></a>Konfigurera Azure för att skicka push-begäranden

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a id="update-server"></a>Uppdatera Server projektet för att skicka push-meddelanden

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a id="configure-app"></a>Konfigurera klient projektet för push-meddelanden

[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a id="add-push"></a>Lägg till kod för push-meddelanden i din app

[!INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>Testa push-meddelanden i din app

Du kan testa appen genom att använda en virtuell enhet i emulatorn. Det krävs ytterligare konfigurations steg när du kör på en emulator.

1. Den virtuella enheten måste ha Google-API: er angivna som mål i Android Virtual Device (AVD) Manager.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. Lägg till ett Google-konto till Android-enheten genom att klicka på **appar** > **Inställningar** > **Lägg till konto**och följ sedan anvisningarna.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. Kör ToDoList-appen som tidigare och infoga ett nytt att göra-objekt. Den här gången visas en meddelande ikon i meddelande fältet. Du kan öppna meddelande lådan för att visa all meddelande text.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)

<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: https://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: https://components.xamarin.com/view/azure-mobile-services/
