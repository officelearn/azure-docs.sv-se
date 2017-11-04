---
title: "Autentisering för tokenbaserad (HTTP/2) för APNS i Azure Notification Hubs | Microsoft Docs"
description: "Det här avsnittet beskriver hur man utnyttjar den nya token autentiseringen för APNS"
services: notification-hubs
documentationcenter: .net
author: kpiteira
manager: erikre
editor: 
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 05/17/2017
ms.author: kapiteir
ms.openlocfilehash: 5a21bcd9f12fc3f96b17a556ba15526c35ababe2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="token-based-http2-authentication-for-apns"></a>Autentisering för tokenbaserad (HTTP/2) för APNS
## <a name="overview"></a>Översikt
Den här artikeln beskrivs hur du använder det nya APN HTTP/2-protokollet med token autentisering.

De främsta fördelarna med att använda det nya protokollet inkluderar:
-   Token generering är relativt enkel (jämfört med certifikat)
-   Ingen mer förfallodatum – har du kontroll över dina autentiseringstoken och deras återkallelse
-   Nyttolaster kan nu vara upp till 4 KB
- Synkron feedback
-   Du är på Apples senaste protokollet – certifikat fortfarande använda binär-protokollet, vilken är markerad för utfasning

Med den här nya funktionen kan du göra i två steg i ett par minuter:
1.  Hämta nödvändig information från Apple Developer-kontoportalen
2.  Konfigurera din meddelandehubb med den nya informationen

Notification Hubs är nu klar att använda det nya autentiseringssystemet med APNS. 

Observera att om du migrerat från med hjälp av autentiseringsuppgifter för certifikat för APNS:
- egenskaperna token skriva över ditt certifikat i vårt system
- men programmet fortsätter att ta emot meddelanden sömlöst.

## <a name="obtaining-authentication-information-from-apple"></a>Hämta autentiseringsuppgifter från Apple
Om du vill aktivera tokenbaserad autentisering behöver du följande egenskaper från Apple Developer-konto:
### <a name="key-identifier"></a>Nyckel-ID
Nyckel-ID kan hämtas från sidan ”nycklar” i Apple Developer-konto

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier--application-name"></a>Program-ID och programnamn
Programnamnet är tillgängliga via sidan App-ID: N i Developer-konto. 
![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

Program-ID är tillgängligt via informationssidan medlemskap i kontot för utvecklare.
![](./media/notification-hubs-push-notification-http2-token-authentification/app-id.png)


### <a name="authentication-token"></a>Token för autentisering
Du kan hämta token för autentisering när du generera en token för ditt program. Mer information om hur du skapar den här variabeln finns i [Apples utvecklardokumentation](http://help.apple.com/xcode/mac/current/#/dev11b059073?sub=dev1eb5dfe65).

## <a name="configuring-your-notification-hub-to-use-token-based-authentication"></a>Konfigurera meddelandehubben för att använda tokenbaserad autentisering
### <a name="configure-via-the-azure-portal"></a>Konfigurera via Azure portal
Om du vill aktivera token autentisering i portalen, logga in på Azure portal och gå till din Meddelandehubb > Notification Services > APN-panelen. 

Det finns en ny egenskap – *autentiseringsläge*. När Token kan du uppdatera din hubb med alla relevanta token egenskaper.

![](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

- Ange egenskaper som du hämtade från Apple developer-konto 
- Välj din programläge (produktion eller Sandbox) 
- Klicka på Spara för att uppdatera APNS-autentiseringsuppgifterna. 

### <a name="configure-via-management-api-rest"></a>Konfigurera via Management API (REST)

Du kan använda vår [management API: er](https://msdn.microsoft.com/library/azure/dn495827.aspx) att uppdatera meddelandehubben för att använda token-baserad autentisering.
Beroende på om du konfigurerar programmet är en Sandbox eller produktion app (anges i Apple Developer-konto), använder du någon av motsvarande slutpunkter:

- Sandboxslutpunkt: [https://api.development.push.apple.com:443-3-enhet](https://api.development.push.apple.com:443/3/device)
- Produktion slutpunkt: [https://api.push.apple.com:443-3-enhet](https://api.push.apple.com:443/3/device)

> [!IMPORTANT]
> Tokenbaserad autentisering kräver en API-version: **2017-04 eller senare**.
> 
> 

Här är ett exempel på en PUT-förfrågan för att uppdatera en hubb med tokenbaserad autentisering:


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
        

### <a name="configure-via-the-net-sdk"></a>Konfigurera via .NET SDK
Du kan konfigurera din hubb för att använda token autentisering med hjälp av vår [senaste klient-SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/1.0.8). 

Här är ett kodexempel illustrerar korrekt användning:


        NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
        string token = "YOUR TOKEN HERE";
        string keyId = "YOUR KEY ID HERE";
        string appName = "YOUR APP NAME HERE";
        string appId = "YOUR APP ID HERE";
        NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
        desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
        desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
        nm.UpdateNotificationHubAsync(desc);

## <a name="reverting-to-using-certificate-based-authentication"></a>Återgå till att använda certifikatbaserad autentisering
Du kan återställa när som helst till med hjälp av certifikatbaserad autentisering genom att använda alla föregående metod och skickar certifikatet i stället för egenskaperna token. Åtgärden skriver över tidigare lagrade autentiseringsuppgifter.
