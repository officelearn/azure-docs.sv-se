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
ms.openlocfilehash: 3185447e4e465fe49849bb4280f2af9bbe852e19
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027698"
---
# <a name="add-push-notifications-to-your-android-app"></a>Lägg till push-meddelanden i din Android-app

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center stöder utveckling av mobila appar från slut punkt till slut punkt och integrerade tjänster. Utvecklare kan använda **bygge**-, **test** -och **distributions** tjänster för att konfigurera kontinuerlig integrering och leverans pipeliner. När appen har distribuerats kan utvecklare övervaka status och användning av appen med hjälp av **analys** -och **diagnos** tjänster och engagera med användare med **push** -tjänsten. Utvecklare kan också utnyttja **auth** för att autentisera sina användare och **data** tjänster för att spara och synkronisera AppData i molnet.
> Om du vill integrera moln tjänster i ditt mobil program kan du registrera dig med App Center [App Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) idag.

## <a name="overview"></a>Översikt

I den här självstudien lägger du till push-meddelanden till [Snabb start för Android] projektet för Android så att ett push-meddelande skickas till enheten varje gång en post infogas.

Om du inte använder det nedladdade snabb start Server-projektet behöver du ett tilläggs paket för push-meddelanden. Mer information finns i [arbeta med .NET-Server del Server SDK för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Förutsättningar

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

## <a name="add-push-notifications-to-your-app"></a>Lägg till push-meddelanden i appen

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
  Lär dig hur du lägger till offline-stöd i din app med hjälp av en Mobile Apps Server del. Med offline-synkronisering kan användare interagera med en mobilapp @ no__t-0viewing, lägga till eller ändra data @ no__t-1even när det inte finns någon nätverks anslutning.

<!-- URLs -->
[Snabb start för Android]: app-service-mobile-android-get-started.md
[Lägg till Firebase i ditt Android-projekt]: https://firebase.google.com/docs/android/setup
