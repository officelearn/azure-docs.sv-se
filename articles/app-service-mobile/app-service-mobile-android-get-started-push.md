---
title: Lägg till push-meddelanden till din Android-app med Mobile Apps | Microsoft Docs
description: Lär dig hur du använder Mobile Apps för att skicka push-meddelanden till din Android-app.
services: app-service\mobile
documentationcenter: android
manager: crdun
editor: ''
author: elamalani
ms.assetid: 9058ed6d-e871-4179-86af-0092d0ca09d3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 539859ba73c8a26d6f7e8f25b9e7453d987a52bd
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389015"
---
# <a name="add-push-notifications-to-your-android-app"></a>Lägg till push-meddelanden i din Android-app

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center stöder utveckling av slutpunkt till slutpunkt-tjänster och integrerade tjänster som är centrala för utveckling av mobilappar. Utvecklare kan använda tjänsterna för att **bygga**, **testa** och **distribuera** för att skapa en pipeline för kontinuerlig integrering och leverans. När appen har distribuerats kan utvecklarna övervaka status och användning av appen med hjälp av tjänsterna **Analys** och **Diagnostik**, och kommunicera med användarna via **Push**-tjänsten. Utvecklare kan också dra nytta av **Auth** för att autentisera sina användare och tjänsten **Data** för att spara och synkronisera appdata i molnet.
>
> Om du vill integrera molntjänster i ditt mobilprogram kan du registrera dig med [App Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) i dag.

## <a name="overview"></a>Översikt

I den här självstudien lägger du till push-meddelanden till [Snabb start för Android] projektet för Android så att ett push-meddelande skickas till enheten varje gång en post infogas.

Om du inte använder det nedladdade snabb start Server-projektet behöver du ett tilläggs paket för push-meddelanden. Mer information finns i [arbeta med .NET-Server del Server SDK för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Krav

Du behöver följande:

* En IDE, beroende på ditt projekts backend:

  * [Android Studio](https://developer.android.com/sdk/index.html) om den här appen har en Node. js-Server del.
  * [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) eller senare om den här appen har en Microsoft .NET Server del.
* Android 2,3 eller senare, Google databas revision 27 eller senare och Google Play Services 9.0.2 eller senare för Firebase Cloud Messaging.
* Slutför [Snabb start för Android].

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Skapa ett projekt som har stöd för Firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Konfigurera en meddelandehubb

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Konfigurera Azure för att skicka push-meddelanden

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>Aktivera push-meddelanden för Server projektet

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>Lägga till push-meddelanden för appen

I det här avsnittet uppdaterar du din klient-Android-app för att hantera push-meddelanden.

### <a name="verify-android-sdk-version"></a>Verifiera Android SDK-version

[!INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

Nästa steg är att installera Google Play-tjänster. Firebase Cloud Messaging har vissa krav på minsta API-nivå för utveckling och testning, vilket **minSdkVersion** -egenskapen i manifestet måste följa.

Om du testar med en äldre enhet bör du läsa [Lägg till Firebase i ditt Android-projekt] för att avgöra hur låg du kan ange det här värdet och ange det på lämpligt sätt.

### <a name="add-firebase-cloud-messaging-to-the-project"></a>Lägg till Firebase Cloud Messaging i projektet

[!INCLUDE [Add Firebase Cloud Messaging](../../includes/app-service-mobile-add-firebase-cloud-messaging.md)]

### <a name="add-code"></a>Lägg till kod

[!INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>Testa appen mot den publicerade mobil tjänsten

Du kan testa appen genom att koppla en Android-telefon direkt med en USB-kabel eller genom att använda en virtuell enhet i emulatorn.

## <a name="next-steps"></a>Nästa steg

Nu när du har slutfört den här självstudien kan du fortsätta med någon av följande själv studie kurser:

* [Lägg till autentisering i din Android-app](app-service-mobile-android-get-started-users.md).
  Lär dig hur du lägger till autentisering i ToDoList snabb starts projekt på Android med en identitets leverantör som stöds.
* [Aktivera synkronisering offline för din Android-app](app-service-mobile-android-get-started-offline-data.md).
  Lär dig hur du lägger till offline-stöd i din app med hjälp av en Mobile Apps Server del. Med offlinesynkronisering kan användare interagera med en mobilapp&mdash;Visa, lägga till eller ändra data&mdash;även om det inte finns någon nätverks anslutning.

<!-- URLs -->
[Snabb start för Android]: app-service-mobile-android-get-started.md
[Lägg till Firebase i ditt Android-projekt]: https://firebase.google.com/docs/android/setup
