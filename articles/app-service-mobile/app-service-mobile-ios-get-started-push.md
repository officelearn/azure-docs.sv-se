---
title: Lägg till Push-meddelanden till iOS-App med Azure Mobile Apps
description: Lär dig hur du använder Azure Mobile Apps för att skicka push-meddelanden till iOS-app.
services: app-service\mobile
documentationcenter: ios
manager: crdun
editor: ''
author: conceptdev
ms.assetid: fa503833-d23e-4925-8d93-341bb3fbab7d
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/10/2016
ms.author: crdun
ms.openlocfilehash: b84990d053d92b9408e119b8aba28c8b9f8b046e
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42819006"
---
# <a name="add-push-notifications-to-your-ios-app"></a>Lägg till Push-meddelanden till din iOS-App

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Översikt

I den här självstudien lägger du till push-meddelanden till den [iOS Snabbstart] projekt så att ett push-meddelande skickas till enheten varje gång en post infogas.

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
[iOS Snabbstart]: app-service-mobile-ios-get-started.md
