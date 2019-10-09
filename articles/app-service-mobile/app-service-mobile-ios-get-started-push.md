---
title: Lägg till push-meddelanden till iOS-appen med Azure Mobile Apps
description: Lär dig hur du använder Azure Mobile Apps för att skicka push-meddelanden till din iOS-app.
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
ms.openlocfilehash: 7a037cfb411eb3c15f60bb6a8763374d9102bc4e
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027315"
---
# <a name="add-push-notifications-to-your-ios-app"></a>Lägg till push-meddelanden till din iOS-app

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center stöder utveckling av mobila appar från slut punkt till slut punkt och integrerade tjänster. Utvecklare kan använda **bygge**-, **test** -och **distributions** tjänster för att konfigurera kontinuerlig integrering och leverans pipeliner. När appen har distribuerats kan utvecklare övervaka status och användning av appen med hjälp av **analys** -och **diagnos** tjänster och engagera med användare med **push** -tjänsten. Utvecklare kan också utnyttja **auth** för att autentisera sina användare och **data** tjänster för att spara och synkronisera AppData i molnet.
> Om du vill integrera moln tjänster i ditt mobil program kan du registrera dig med App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) idag.

## <a name="overview"></a>Översikt

I den här självstudien lägger du till push-meddelanden i [snabb start för iOS] projektet för iOS så att ett push-meddelande skickas till enheten varje gång en post infogas.

Om du inte använder det hämtade snabb starts Server projektet behöver du ett tilläggs paket för push-meddelanden. Mer information finns i [arbeta med .NET-Server del Server SDK för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) guide.

[IOS-simulatorn stöder inte push-meddelanden](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html). Du behöver en fysisk iOS-enhet och ett [program medlemskap för Apple-utvecklare](https://developer.apple.com/programs/ios/).

## <a name="configure-hub"></a>Konfigurera Notification Hub

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Registrera appen för push-meddelanden

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Konfigurera Azure för att skicka push-meddelanden

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a id="update-server"></a>Uppdatera Server delen för att skicka push-meddelanden

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a id="add-push"></a>Lägg till push-meddelanden i appen

[!INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a id="test"></a>Testa push-meddelanden

[!INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

## <a id="more"></a>Kraftfull

* Mallar ger dig flexibilitet att skicka plattforms oberoende push-meddelanden och lokaliserade push-meddelanden. [Hur du använder klient biblioteket för iOS för Azure Mobile Apps](app-service-mobile-ios-how-to-use-client-library.md#templates) visar hur du registrerar mallar.

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[snabb start för iOS]: app-service-mobile-ios-get-started.md
