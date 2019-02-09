---
title: Token-baserad (HTTP/2)-autentisering för APNS i Azure Notification Hubs | Microsoft Docs
description: Det här avsnittet beskrivs hur du använder den nya tokenautentisering för APNS
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: d0d181afd9bb8f1d387ce8e33f50a78dae3a11ec
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55961439"
---
# <a name="token-based-http2-authentication-for-apns"></a>Token-baserad (HTTP/2)-autentisering för APNS

## <a name="overview"></a>Översikt

Den här artikeln beskriver hur du använder det nya APN HTTP/2-protokollet med token formulärbaserad autentisering.

Viktiga fördelar med att använda det nya protokollet är:

* Generering av åtkomsttoken är relativt enkel (jämfört med certifikat)
* Inga fler utgångsdatum – har du kontroll över dina autentiseringstoken och deras återkallning
* Nyttolaster kan nu vara upp till 4 KB
* Synkron feedback
* Du är på Apples senaste protokollet – certifikat fortfarande använda binär-protokollet som har markerats för utfasning

Den här nya mekanismen kan göras i två steg i ett par minuter:

1. Hämta nödvändig information från portalen Apple Developer-konto
2. Konfigurera meddelandehubben med den nya informationen

Meddelandehubbar är nu klart att använda det nya autentiseringssystemet med APNS.

Observera att om du har migrerat från att använda autentiseringsuppgifter för certifikat för APNS:

* token egenskaperna skriva över ditt certifikat i vårt system
* men programmet fortsätter att ta emot meddelanden sömlöst.

## <a name="obtaining-authentication-information-from-apple"></a>Hämta autentiseringsuppgifter från Apple

Om du vill aktivera tokenbaserad autentisering, behöver du följande egenskaper från ditt Apple Developer-konto:

### <a name="key-identifier"></a>Nyckelidentifierare

Nyckelidentifieraren kan hämta från sidan ”nycklar” i ditt Apple Developer-konto

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier--application-name"></a>Program-ID och programnamn

Programnamnet är tillgängligt via sidan App-ID: N i Developer-konto.

![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

Program-ID är tillgängligt via sidan medlemskap i Developer-konto.

![](./media/notification-hubs-push-notification-http2-token-authentification/app-id.png)

### <a name="authentication-token"></a>Autentiseringstoken

Autentiseringstoken kan laddas ned när du har genererat en token för ditt program. Information om hur du skapar den här token i [Apples utvecklardokumentation](http://help.apple.com/xcode/mac/current/#/dev11b059073?sub=dev1eb5dfe65).

## <a name="configuring-your-notification-hub-to-use-token-based-authentication"></a>Konfigurera meddelandehubben för att använda tokenbaserad autentisering

### <a name="configure-via-the-azure-portal"></a>Konfigurera via Azure portal

Om du vill aktivera autentisering med enhetstoken baserat på portalen, logga in på Azure-portalen och gå till din Meddelandehubb > Notification Services > APN-panelen.

Det finns en ny egenskap – *autentiseringsläge*. Att välja Token kan du uppdatera din hubb med alla relevanta token egenskaper.

![](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Ange egenskaper som du hämtade från ditt Apple developer-konto
* Välj din programläge (produktion eller Sandbox)
* Klicka på den **spara** knappen för att uppdatera dina autentiseringsuppgifter för APNS

### <a name="configure-via-management-api-rest"></a>Konfigurera via hanterings-API (REST)

Du kan använda våra [management API: er](https://msdn.microsoft.com/library/azure/dn495827.aspx) att uppdatera din notification hub om du vill använda tokenbaserad autentisering.
Använd någon av de motsvarande slutpunkterna beroende på om programmet som du konfigurerar är ett begränsat läge eller produktion-app (anges i ditt Apple Developer-konto):

* Sandboxslutpunkt: [https://api.development.push.apple.com:443/3/device](https://api.development.push.apple.com:443/3/device)
* Produktion slutpunkt: [https://api.push.apple.com:443/3/device](https://api.push.apple.com:443/3/device)

> [!IMPORTANT]
> Tokenbaserad autentisering kräver API-versionen: **2017-04 eller senare**.

Här är ett exempel på en PUT-begäran för att uppdatera en hubb med tokenbaserad autentisering:

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

Du kan konfigurera din hubb för att använda token autentisering med hjälp av vår [senaste klienten SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/1.0.8).

Här är ett kodexempel som illustrerar rätt användning:

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

## <a name="reverting-to-using-certificate-based-authentication"></a>Återgår till att använda certifikatbaserad autentisering

Du kan återställa när som helst för att använda certifikatbaserad autentisering genom att använda alla föregående metod och skicka certifikatet i stället för token egenskaperna. Åtgärden skriver över tidigare lagrade autentiseringsuppgifter.
