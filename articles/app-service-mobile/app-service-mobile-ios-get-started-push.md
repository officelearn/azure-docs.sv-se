---
title: Lägg till Push-meddelanden till iOS-App med Azure Mobile Apps
description: Lär dig hur du använder Azure Mobile Apps för att skicka push-meddelanden till iOS-app.
services: app-service\mobile
documentationcenter: ios
manager: crdun
editor: ''
author: elamalani
ms.assetid: fa503833-d23e-4925-8d93-341bb3fbab7d
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 5ab968e88331f888dfcecd2cc30a658b0b0f53ec
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445367"
---
# <a name="add-push-notifications-to-your-ios-app"></a>Lägg till Push-meddelanden till din iOS-App

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center investerar i nya och integrerade tjänster som är centrala för utveckling av mobilappar. Utvecklare kan använda **skapa**, **Test** och **fördela** tjänster för att konfigurera pipeline för kontinuerlig integrering och leverans. När appen har distribuerats, utvecklare kan övervaka status och användningen av sin app med hjälp av den **Analytics** och **diagnostik** services och interagera med användare som använder den **Push** tjänsten. Utvecklare kan även utnyttja **Auth** att autentisera användarna och **Data** -tjänsten för att bevara och synkronisera AppData i molnet. Kolla in [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-ios-get-started-push) idag.
>

## <a name="overview"></a>Översikt

I den här självstudien lägger du till push-meddelanden till den [Snabbstart för iOS] projekt så att ett push-meddelande skickas till enheten varje gång en post infogas.

Om du inte använder det nedladdade snabbstartsprojektet på en server måste tilläggspaket för push-meddelande. Mer information finns i [arbeta med SDK för .NET-serverdelen för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) guide.

Den [stöder inte push-meddelanden i iOS-simulatorn](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html). Du behöver en fysisk iOS-enhet och en [Apple Developer Program medlemskap](https://developer.apple.com/programs/ios/).

## <a name="configure-hub"></a>Konfigurera Meddelandehubben

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Registrera app för push-meddelanden

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Konfigurera Azure för att skicka push-meddelanden

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a id="update-server"></a>Uppdatera serverdel och skicka push-meddelanden

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a id="add-push"></a>Lägg till pushmeddelanden i appen

[!INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a id="test"></a>Test-push-meddelanden

[!INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

## <a id="more"></a>Mer

* Mallar ger dig flexibilitet att skicka plattformsoberoende push-meddelanden och lokaliserade push-meddelanden. [Så här används iOS-klientbiblioteket för Azure Mobile Apps](app-service-mobile-ios-how-to-use-client-library.md#templates) visar hur du registrerar mallar.

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[Snabbstart för iOS]: app-service-mobile-ios-get-started.md
