---
title: Lägga till push-meddelanden i din Android-app
description: Läs om hur du använder mobilappar för att skicka push-meddelanden till din Android-app.
ms.assetid: 9058ed6d-e871-4179-86af-0092d0ca09d3
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 6fec85c028e992c15fb9503ffb599023e668c58f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459964"
---
# <a name="add-push-notifications-to-your-android-app"></a>Lägga till push-meddelanden i din Android-app

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Översikt

I den här självstudien lägger du till push-meddelanden i [Android-snabbstartsprojektet] så att ett push-meddelande skickas till enheten varje gång en post infogas.

Om du inte använder det hämtade snabbstartsserverprojektet behöver du tilläggspaketet för push-meddelande. Mer information finns i [Arbeta med .NET-server för server för server för server för server för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Krav

Du behöver följande:

* En IDE, beroende på projektets baksida:

  * [Android Studio](https://developer.android.com/sdk/index.html) om den här appen har en Node.js-backdel.
  * [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) eller senare om den här appen har en Microsoft .NET-backdel.
* Android 2.3 eller senare, Google Repository revision 27 eller senare, och Google Play Services 9.0.2 eller senare för Firebase Cloud Messaging.
* Slutför [Snabbstarten]för Android .

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Skapa ett projekt som har stöd för Firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Konfigurera en meddelandehubb

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Konfigurera Azure för att skicka push-meddelanden

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>Aktivera push-meddelanden för serverprojektet

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>Lägg till push-meddelanden i appen

I det här avsnittet uppdaterar du din Android-klient-app för att hantera push-meddelanden.

### <a name="verify-android-sdk-version"></a>Verifiera Android SDK-version

[!INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

Nästa steg är att installera Google Play-tjänster. Firebase Cloud Messaging har vissa minimikrav på API-nivå för utveckling och testning, som egenskapen **minSdkVersion** i manifestet måste överensstämma med.

Om du testar med en äldre enhet läser du [Lägg till Firebase i Ditt Android-projekt] för att avgöra hur lågt du kan ange det här värdet och ställa in det på rätt sätt.

### <a name="add-firebase-cloud-messaging-to-the-project"></a>Lägg till Firebase Cloud Messaging i projektet

[!INCLUDE [Add Firebase Cloud Messaging](../../includes/app-service-mobile-add-firebase-cloud-messaging.md)]

### <a name="add-code"></a>Lägg till kod

[!INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>Testa appen mot den publicerade mobiltjänsten

Du kan testa appen genom att direkt ansluta en Android-telefon med en USB-kabel eller genom att använda en virtuell enhet i emulatorn.

## <a name="next-steps"></a>Nästa steg

Nu när du har slutfört den här självstudien kan du fortsätta till någon av följande självstudier:

* [Lägg till autentisering i din Android-app](app-service-mobile-android-get-started-users.md).
  Lär dig hur du lägger till autentisering i snabbstartsprojektet för todolist på Android med hjälp av en identitetsprovider som stöds.
* [Aktivera offlinesynkronisering för din Android-app](app-service-mobile-android-get-started-offline-data.md).
  Läs om hur du lägger till offlinesupport i appen med hjälp av en backend för mobilappar. Med offlinesynkronisering kan användare&mdash;interagera med en mobilapp&mdash;som visar, lägger till eller ändrar data även när det inte finns någon nätverksanslutning.

<!-- URLs -->
[Snabbstart i Android]: app-service-mobile-android-get-started.md
[Lägg firebase till ditt Android-projekt]:https://firebase.google.com/docs/android/setup
