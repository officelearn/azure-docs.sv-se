---
title: Token-baserad (HTTP/2) autentisering för APN i Azure Notification Hubs | Microsoft Docs
description: I det här avsnittet beskrivs hur du utnyttjar den nya token-autentiseringen för APN
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 02/13/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/13/2019
ms.openlocfilehash: a7fdaae33e28bd543b44c54868324339d1269bc2
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213117"
---
# <a name="token-based-http2-authentication-for-apns"></a>Token-baserad (HTTP/2) autentisering för APN

## <a name="overview"></a>Översikt

Den här artikeln beskriver hur du använder det nya APN HTTP/2-protokollet med tokenbaserad autentisering.

De främsta fördelarna med att använda det nya protokollet är:

* Generering av token är relativt kostnads fritt (jämfört med certifikat)
* Inga fler förfallo datum – du får kontroll över dina autentiseringstoken och deras åter kallelse
* Nytto laster kan nu vara upp till 4 KB
* Synkron feedback
* Du använder Apples senaste protokoll – certifikat använder fortfarande det binära protokollet, som är markerat för utfasning

Du kan använda den här nya mekanismen i två steg om några minuter:

1. Hämta nödvändig information från Apples konto Portal för utvecklare
2. Konfigurera din Notification Hub med den nya informationen

Notification Hubs är nu alla inställda att använda det nya autentiseringsschemat med APN.

Observera att om du har migrerat från att använda certifikat uppgifter för APN:

* token-egenskaperna skriver över ditt certifikat i vårt system.
* men programmet fortsätter att ta emot meddelanden sömlöst.

## <a name="obtaining-authentication-information-from-apple"></a>Hämta autentiseringsinformation från Apple

Om du vill aktivera tokenbaserad autentisering behöver du följande egenskaper från ditt Apple Developer-konto:

### <a name="key-identifier"></a>Nyckelidentifierare

Nyckel identifieraren kan hämtas från sidan "nycklar" i ditt Apple Developer-konto

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier--application-name"></a>Program identifierare & program namn

Program namnet är tillgängligt via sidan app-ID i Developer-kontot.

![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

Program-ID: n är tillgänglig via sidan medlemskaps information i Developer-kontot.

![](./media/notification-hubs-push-notification-http2-token-authentification/app-id.png)

### <a name="authentication-token"></a>Autentiseringstoken

Autentiseringstoken kan hämtas när du har genererat en token för ditt program. Mer information om hur du skapar denna token finns i [Apples dokumentation om utvecklare](https://help.apple.com/xcode/mac/current/#/devdfd3d04a1).

## <a name="configuring-your-notification-hub-to-use-token-based-authentication"></a>Konfigurera Notification Hub för att använda tokenbaserad autentisering

### <a name="configure-via-the-azure-portal"></a>Konfigurera via Azure Portal

Om du vill aktivera tokenbaserad autentisering i portalen loggar du in på Azure Portal och går till Notification Hub-> Notification Services > APN-panel.

Det finns en ny egenskap – *autentiseringsläge*. Genom att välja token kan du uppdatera navet med alla relevanta token-egenskaper.

![](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Ange de egenskaper som du hämtade från ditt Apple Developer-konto
* Välj program läge (produktion eller sandbox)
* Klicka på knappen **Spara** för att uppdatera dina AUTENTISERINGSUPPGIFTER för APN

### <a name="configure-via-management-api-rest"></a>Konfigurera via Management API (REST)

Du kan använda våra [hanterings-API: er](https://msdn.microsoft.com/library/azure/dn495827.aspx) för att uppdatera Notification Hub för att använda tokenbaserad autentisering.
Beroende på om det program som du konfigurerar är en sandbox-eller produktions program (anges i ditt Apple Developer-konto) använder du en av motsvarande slut punkter:

* Sandbox-slutpunkt:[https://api.development.push.apple.com:443/3/device](https://api.development.push.apple.com:443/3/device)
* Produktions slut punkt:[https://api.push.apple.com:443/3/device](https://api.push.apple.com:443/3/device)

> [!IMPORTANT]
> Token-baserad autentisering kräver en API-version av: **2017-04 eller senare**.

Här är ett exempel på en skicka-begäran om att uppdatera en hubb med tokenbaserad autentisering:

    ```text
    PUT https://{namespace}.servicebus.windows.net/{Notification Hub}?api-version=2017-04
      "Properties": {
        "ApnsCredential": {
          "Properties": {
            "KeyId": "<Your Key Id>",
            "Token": "<Your Authentication Token>",
            "AppName": "<Your Application Name>",
            "AppId": "<Your Application Id>",
            "Endpoint":"<Sandbox/Production Endpoint>"
          }
        }
      }
    ```

### <a name="configure-via-the-net-sdk"></a>Konfigurera via .NET SDK

Du kan konfigurera navet att använda token-baserad autentisering med hjälp av vår [senaste klient-SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/1.0.8).

Här är ett kod exempel som illustrerar rätt användning:

```csharp
NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
string token = "YOUR TOKEN HERE";
string keyId = "YOUR KEY ID HERE";
string appName = "YOUR APP NAME HERE";
string appId = "YOUR APP ID HERE";
NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
nm.UpdateNotificationHubAsync(desc);
```

## <a name="reverting-to-using-certificate-based-authentication"></a>Återgå till att använda certifikatbaserad autentisering

Du kan återställa när som helst genom att använda certifikatbaserad autentisering genom att använda en föregående metod och skicka certifikatet i stället för token-egenskaperna. Åtgärden skriver över de tidigare lagrade autentiseringsuppgifterna.
