---
title: Lägg till push-meddelanden i Xamarin.Android-appen | Microsoft Docs
description: Lär dig hur du använder Azure App Service och Azure Notification Hubs för att skicka push-meddelanden i Xamarin.Android-appen
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 6f7e8517-e532-4559-9b07-874115f4c65b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: cff0845b555f25fce438f3389e1f97cda0450bc3
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447132"
---
# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Lägg till push-meddelanden i Xamarin.Android-appen

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center investerar i nya och integrerade tjänster som är centrala för utveckling av mobilappar. Utvecklare kan använda **skapa**, **Test** och **fördela** tjänster för att konfigurera pipeline för kontinuerlig integrering och leverans. När appen har distribuerats, utvecklare kan övervaka status och användningen av sin app med hjälp av den **Analytics** och **diagnostik** services och interagera med användare som använder den **Push** tjänsten. Utvecklare kan även utnyttja **Auth** att autentisera användarna och **Data** -tjänsten för att bevara och synkronisera AppData i molnet. Kolla in [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-android-get-started-push) idag.
>

## <a name="overview"></a>Översikt

I den här självstudien lägger du till push-meddelanden till den [Xamarin.Android snabbstarten](app-service-mobile-windows-store-dotnet-get-started.md) projekt så att ett push-meddelande skickas till enheten varje gång en post infogas.

Om du inte använder det nedladdade snabbstartsprojektet på en server måste tilläggspaket för push-meddelande. Mer information finns i den [arbeta med SDK för .NET-serverdelen för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) guide.

## <a name="prerequisites"></a>Förutsättningar

Den här självstudien kräver installationen:

* Ett aktivt Google-konto. Du kan registrera dig för ett Google-konto på [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).
* [Google Cloud Messaging-klientkomponent](https://components.xamarin.com/view/GCMClient/).

## <a name="configure-hub"></a>Konfigurera en Notification Hub

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Aktivera Firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-azure-to-send-push-requests"></a>Konfigurera Azure för att skicka push-begäranden

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a id="update-server"></a>Uppdatera serverprojektet för att skicka push-meddelanden

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a id="configure-app"></a>Konfigurera klientprojektet för push-meddelanden

[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a id="add-push"></a>Lägg till kod för push-meddelanden till din app

[!INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>Test-push-meddelanden i din app

Du kan testa appen med hjälp av en virtuell enhet i emulatorn. Det finns ytterligare konfigurationssteg som krävs vid körning på en emulator.

1. Den virtuella enheten måste ha Google APIs som mål i hanteraren för Android Virtual Device (AVD).

    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. Lägg till ett Google-konto till Android-enhet genom att klicka på **appar** > **inställningar** > **Lägg till konto**, följer sedan anvisningarna.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. Kör appen todolist som innan och infoga en ny att göra-objekt. Den här tiden kan visas en meddelandeikon i meddelandefältet. Du kan öppna lådan meddelande om du vill visa den fullständiga texten i meddelandet.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)

<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: https://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: https://components.xamarin.com/view/azure-mobile-services/
