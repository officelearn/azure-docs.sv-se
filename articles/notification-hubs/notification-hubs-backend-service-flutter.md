---
title: Skicka push-meddelanden till Flutter-appar med Azure Notification Hubs via en server dels tjänst | Microsoft Docs
description: Lär dig att skicka meddelanden till Flutter-appar som använder Azure Notification Hubs via en server dels tjänst.
author: mikeparker104
ms.service: notification-hubs
ms.topic: tutorial
ms.date: 07/07/2020
ms.author: miparker
ms.openlocfilehash: 5fa753a6b8b1284c4f8fcd046f74fabcbae3f8fb
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171014"
---
# <a name="tutorial-send-push-notifications-to-flutter-apps-using-azure-notification-hubs-via-a-backend-service"></a>Självstudie: skicka push-meddelanden till Flutter-appar med Azure Notification Hubs via en server dels tjänst  

[![Hämta exempel ](./media/notification-hubs-backend-service-flutter/download.png) Hämta exemplet](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_backend_service)  

> [!div class="op_single_selector"]
>
> * [Xamarin.Forms](notification-hubs-backend-service-xamarin-forms.md)
> * [Flutter](notification-hubs-backend-service-flutter.md)
> * [React Native](notification-hubs-backend-service-react-native.md)

I den här självstudien använder du [Azure Notification Hubs](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-overview) för att skicka meddelanden till ett [Flutter](https://flutter.dev) -program som riktar sig till **Android** och **iOS**.  

[!INCLUDE [Notification Hubs Backend Service Introduction](../../includes/notification-hubs-backend-service-introduction.md)]

Den här självstudien tar dig igenom följande steg:

> [!div class="checklist"]
>
> * [Konfigurera Push-Notification Services och Azure Notification Hubs.](#set-up-push-notification-services-and-azure-notification-hub)
> * [Skapa ett ASP.NET Core webb-API-backend-program.](#create-an-aspnet-core-web-api-backend-application)
> * [Skapa ett Flutter-program mellan plattformar.](#create-a-cross-platform-flutter-application)
> * [Konfigurera det ursprungliga Android-projektet för push-meddelanden.](#configure-the-native-android-project-for-push-notifications)
> * [Konfigurera det ursprungliga iOS-projektet för push-meddelanden.](#configure-the-native-ios-project-for-push-notifications)
> * [Testa lösningen.](#test-the-solution)

## <a name="prerequisites"></a>Krav

För att följa med måste du:

* En [Azure-prenumeration](https://portal.azure.com) där du kan skapa och hantera resurser.
* [Flutter](https://flutter.dev/docs/get-started/install) Toolkit (tillsammans med dess krav).
* [Visual Studio Code](https://code.visualstudio.com) med [Flutter-och dart-plugin](https://flutter.dev/docs/get-started/editor?tab=vscode) -program installerade.
* Möjligheten att köra appen på **Android** -enheter (fysiska enheter eller emuleringsenheter) eller **iOS** (endast fysiska enheter).

För Android måste du ha:

* En utvecklare olåste fysisk enhet eller en emulator *(som kör API 26 och senare med Google Play Services installerat)*.

För iOS måste du ha:

* Ett aktivt [Apple Developer-konto](https://developer.apple.com).
* En fysisk iOS-enhet som är [registrerad på ditt utvecklarverktyg (som](https://help.apple.com/developer-account/#/dev40df0d9fa) *kör iOS 13,0 och senare)*.
* Ett **. p12** - [utvecklingsprojekt](https://help.apple.com/developer-account/#/dev04fd06d56) som är installerade i din **nyckel Ring** så att du kan [köra en app på en fysisk enhet](https://help.apple.com/xcode/mac/current/#/dev5a825a1ca).
* [CocoaPods](https://guides.cocoapods.org/using/getting-started.html#installation) installerat för att hantera biblioteks beroenden.

> [!NOTE]
> IOS-simulatorn har inte stöd för fjärraviseringar och så en fysisk enhet krävs när du utforskar det här exemplet på iOS. Du behöver dock inte köra appen både på **Android** och **iOS** för att kunna slutföra den här självstudien.

Du kan följa stegen i det här exemplet med första principer utan föregående erfarenhet. Du kan dock ha nytta av att ha bekant med följande aspekter.

* [Apple Developer-portalen](https://developer.apple.com)
* [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1)
* [Azure Notification Hubs](notification-hubs-push-notification-overview.md)
* [Google Firebase-konsolen](https://console.firebase.google.com/u/0/)
* [Flutter](https://flutter.dev) och [Dart](https://dart.dev) för plattforms oberoende utveckling
* [Kotlin](https://kotlinlang.org) och [Swift](https://developer.apple.com/swift) för Android och iOS inbyggd utveckling

De angivna stegen är bara för [MacOS](https://developer.apple.com/macos). Det är möjligt att följa med i [Windows](https://www.microsoft.com/windows) genom att hoppa över **iOS** -aspekterna.

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>Konfigurera Push-Notification Services och Azure Notification Hub

I det här avsnittet ställer du in **[Firebase Cloud Messaging (FCM)](https://firebase.google.com/docs/cloud-messaging)** och **[Apple Push-Notification Services (APN)](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)**. Sedan kan du skapa och konfigurera en Notification Hub för att arbeta med dessa tjänster.

[!INCLUDE [Create a Firebase project and enable Firebase Cloud Messaging](../../includes/notification-hubs-common-enable-firebase-cloud-messaging.md)]

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-common-enable-apple-push-notifications.md)]

[!INCLUDE [Create a notification hub](../../includes/notification-hubs-common-create-notification-hub.md)]

[!INCLUDE [Configure your notification hub with APNs information](../../includes/notification-hubs-common-configure-with-apns-information.md)]

[!INCLUDE [Configure your notification hub with FCM information](../../includes/notification-hubs-common-configure-with-fcm-information.md)]

## <a name="create-an-aspnet-core-web-api-backend-application"></a>Skapa ett ASP.NET Core webb-API-backend-program

I det här avsnittet skapar du [ASP.net Core webb-API-](https://dotnet.microsoft.com/apps/aspnet/apis) backend för hantering av [enhets registrering](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration) och sändning av meddelanden till Flutter-mobilappen.

[!INCLUDE [Create an ASP.NET Core Web API backend application](../../includes/notification-hubs-backend-service-web-api.md)]

## <a name="create-a-cross-platform-flutter-application"></a>Skapa ett Flutter program över plattformar

I det här avsnittet skapar du ett [Flutter](https://flutter.dev) mobilt program som implementerar push-meddelanden på plattforms oberoende sätt.

[!INCLUDE [Sample application generic overview](../../includes/notification-hubs-backend-service-sample-app-overview.md)]

[!INCLUDE [Create Flutter application](../../includes/notification-hubs-backend-service-sample-app-flutter.md)]

## <a name="configure-the-native-android-project-for-push-notifications"></a>Konfigurera det interna Android-projektet för push-meddelanden

[!INCLUDE [Configure the native Android project](../../includes/notification-hubs-backend-service-configure-flutter-android.md)]

## <a name="configure-the-native-ios-project-for-push-notifications"></a>Konfigurera det ursprungliga iOS-projektet för push-meddelanden

[!INCLUDE [Configure the native iOS project](../../includes/notification-hubs-backend-service-configure-flutter-ios.md)]

## <a name="test-the-solution"></a>Testa lösningen

Nu kan du testa att skicka meddelanden via backend-tjänsten.

[!INCLUDE [Testing the solution](../../includes/notification-hubs-backend-service-testing.md)]

## <a name="next-steps"></a>Nästa steg

Nu bör du ha en grundläggande Flutter-app som är ansluten till en Notification Hub via en backend-tjänst och kan skicka och ta emot meddelanden.

[!INCLUDE [Next steps](../../includes/notification-hubs-backend-service-next-steps.md)]

## <a name="troubleshooting"></a>Felsökning

[!INCLUDE [Troubleshooting](../../includes/notification-hubs-backend-service-troubleshooting.md)]

## <a name="related-links"></a>Relaterade länkar

* [Översikt över Azure Notification Hubs](notification-hubs-push-notification-overview.md)
* [Installera Flutter på macOS](https://flutter.dev/docs/get-started/install/macos)
* [Installera Flutter i Windows](https://flutter.dev/docs/get-started/install/windows)
* [Notification Hubs SDK för Server dels åtgärder](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
* [Notification Hubs SDK på GitHub](https://github.com/Azure/azure-notificationhubs)
* [Registrera med serverdelen för programmet](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
* [Registreringshantering](notification-hubs-push-notification-registration-management.md)
* [Arbeta med Taggar](notification-hubs-tags-segment-push-message.md)
* [Arbeta med anpassade mallar](notification-hubs-templates-cross-platform-push-messages.md)
