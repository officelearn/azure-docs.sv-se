---
title: Lägg till push-meddelanden till din Android-app med Mobile Apps | Microsoft Docs
description: Lär dig hur du använder Mobile Apps för att skicka push-meddelanden till din Android-app.
services: app-service\mobile
documentationcenter: android
manager: crdun
editor: ''
author: conceptdev
ms.assetid: 9058ed6d-e871-4179-86af-0092d0ca09d3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 11/17/2017
ms.author: crdun
ms.openlocfilehash: 9e9f7aba49c53a1a6fcc611ed771f266eb49c883
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38547183"
---
# <a name="add-push-notifications-to-your-android-app"></a>Lägg till push-meddelanden till din Android-app
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Översikt
I den här självstudien lägger du till push-meddelanden till den [Snabbstart för Android] projekt så att ett push-meddelande skickas till enheten varje gång en post infogas.

Om du inte använder serverprojekt hämtade Snabbstart måste tilläggspaket för push-meddelande. Mer information finns i [arbeta med SDK för .NET-serverdelen för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande:

* En IDE, beroende på ditt projekt backend-server:

  * [Android Studio](https://developer.android.com/sdk/index.html) om den här appen har en Node.js-serverdel.
  * [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) eller senare om den här appen har en Microsoft .NET-serverdel.
* Android 2.3 eller senare, Google Repository revision 27 eller senare och Google Play Services 9.0.2 eller senare för Firebase Cloud Messaging.
* Slutför den [Snabbstart för Android].

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Skapa ett projekt som har stöd för Firebase Cloud Messaging
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Konfigurera en meddelandehubb
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Konfigurera Azure för att skicka push-meddelanden
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>Aktivera push-meddelanden för serverprojektet
[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>Lägg till push-meddelanden i appen
I det här avsnittet ska uppdatera du din Android-app i klienten för att hantera push-meddelanden.

### <a name="verify-android-sdk-version"></a>Kontrollera Android SDK-version
[!INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

Nästa steg är att installera Google Play-tjänster. Firebase Cloud Messaging har vissa lägsta nivå krav med API för utveckling och testning, som den **minSdkVersion** -egenskapen i manifestet måste följa.

Om du testar med en äldre enhet läser [Lägg till Firebase till din Android-projekt] att avgöra hur låg du kan ange det här värdet och ställa in korrekt.

### <a name="add-firebase-cloud-messaging-to-the-project"></a>Lägg till Firebase Cloud Messaging i projektet
[!INCLUDE [Add Firebase Cloud Messaging](../../includes/app-service-mobile-add-firebase-cloud-messaging.md)]

### <a name="add-code"></a>Lägg till kod
[!INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>Testa appen mot den publicerade mobiltjänsten
Du kan testa appen genom att koppla en Android-telefon med en USB-kabel direkt eller genom att använda en virtuell enhet i emulatorn.

## <a name="next-steps"></a>Nästa steg
Nu när du har slutfört den här självstudien, Överväg fortsätter in på någon av följande självstudiekurser:

* [Lägg till autentisering i din Android-app](app-service-mobile-android-get-started-users.md).
  Lär dig hur du lägger till autentisering till snabbstartsprojektet todolist på Android med en identitetsprovider som stöds.
* [Aktivera synkronisering offline för din Android-app](app-service-mobile-android-get-started-offline-data.md).
  Lär dig hur du lägger till offlinestöd i appen med hjälp av en Mobile Apps-serverdel. Med offlinesynkronisering kan användare kan interagera med en mobil app&mdash;visa, lägga till eller ändra data&mdash;även om det inte finns någon nätverksanslutning.

<!-- URLs -->
[Snabbstart för Android]: app-service-mobile-android-get-started.md
[Lägg till Firebase till din Android-projekt]:https://firebase.google.com/docs/android/setup
