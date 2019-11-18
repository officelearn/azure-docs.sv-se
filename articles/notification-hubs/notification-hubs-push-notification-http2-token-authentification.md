---
title: Token-baserad (HTTP/2) autentisering för APN i Azure Notification Hubs | Microsoft Docs
description: I det här avsnittet beskrivs hur du använder den nya token-autentiseringen för APN
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
ms.date: 11/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/13/2019
ms.openlocfilehash: 56689981d6c85c844fefbec6a4ec4aeb041dbc7f
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111927"
---
# <a name="token-based-http2-authentication-for-apns"></a>Token-baserad (HTTP/2) autentisering för APN

## <a name="overview"></a>Översikt

Den här artikeln förklarar hur du använder det nya APN HTTP/2-protokollet med tokenbaserad autentisering.

De främsta fördelarna med att använda det nya protokollet är:

* Generering av token är relativt enkelt (jämfört med certifikat)
* Inga fler förfallo datum – du får kontroll över dina autentiseringstoken och deras åter kallelse
* Nytto laster kan nu vara upp till 4 KB
* Synkron feedback
* Du är på Apples senaste protokoll – certifikat använder fortfarande det binära protokollet, som är markerat för utfasning

Du kan använda den här nya mekanismen i två steg:

* Hämta nödvändig information från Apple Developer Account-portalen.
* Konfigurera din Notification Hub med den nya informationen.

Notification Hubs har nu ställts in för att använda det nya autentiseringsschemat med APN.

Observera att om du har migrerat från att använda autentiseringsuppgifter för APN, skriver token-egenskaperna över ditt certifikat i vårt system, men programmet fortsätter att ta emot meddelanden sömlöst.

## <a name="obtaining-authentication-information-from-apple"></a>Hämta autentiseringsinformation från Apple

Om du vill aktivera tokenbaserad autentisering behöver du följande egenskaper från ditt Apple Developer-konto:

### <a name="key-identifier"></a>Nyckel identifierare

Nyckel identifieraren kan hämtas från sidan **nycklar** under **certifikat, identifierare & profiler**, i ditt Apple Developer-konto:

![](./media/notification-hubs-push-notification-http2-token-authentification/keys.png)

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier-and-application-name"></a>Program-ID och program namn

Program namnet och identifieraren är också tillgängliga på sidan **certifikat, identifierare & profiler** i Developer-kontot:

![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

### <a name="configure-via-the-net-sdk-or-the-azure-portal"></a>Konfigurera via .NET SDK eller Azure Portal

Du kan konfigurera hubben att använda tokenbaserad autentisering med hjälp av vår [senaste klient-SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs)eller i Azure Portal. Om du vill aktivera tokenbaserad autentisering i portalen loggar du in på Azure Portal och går till **inställningarna > Apple (APNs)** på Notification Hub. Välj **token** från egenskapen **autentiseringsläge** för att uppdatera navet med alla relevanta token-egenskaper.

![Konfigurera token](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Ange de egenskaper du hämtade från ditt Apple Developer-konto.
* Välj program läge (**produktion** eller **sandbox**).
* Klicka på knappen **Spara** för att uppdatera dina APN-autentiseringsuppgifter.

Token-baserade autentiseringsuppgifter består av följande fält:

* **Nyckel-ID**: identifierare för den privata nyckel som genereras i Apple Developer-portalen. till exempel `2USFGKSKLT`.
* **Team-ID**: kallas även prefix eller app-prefix. Detta är identifieraren för organisationen i Apple Developer-portalen. till exempel `S4V3D7CHJR`.
* **Paket-ID**: kallas även "app-ID". Detta är paket-ID: t för programmet. till exempel `com.microsoft.nhubsample2019`. Observera att du kan använda en nyckel för många appar. Det här värdet mappar till `apns-topic` HTTP-huvud när ett meddelande skickas, och används för att rikta det specifika programmet.
* **Token**: kallas även för nyckeln Key eller Private. Detta hämtas från. P8-filen som genereras på Apple Developer-portalen. Nyckeln måste ha APN aktiverat (som väljs på Apple Developer-portalen när nyckeln skapas). Värdet måste ha PEM huvud/sidfot från den när du anger det till NH-portalen/API: et.
* **Slut punkt**: det här är en växling på bladet Notification Hubs Portal och ett sträng fält i API: et. Giltiga värden är `https://api.push.apple.com` eller `https://api.sandbox.push.apple.com`. Notification Hubs använder det här värdet för antingen produktions-eller sandbox-miljön för att skicka meddelanden. Detta måste överensstämma med `aps-environment` rättigheten i appen, annars matchar inte APN-enhetens tokens, och meddelandena kan inte skickas.

Här är ett kod exempel som illustrerar rätt användning:

```csharp
NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
string token = "YOUR PRIVATE KEY HERE";
string keyId = "YOUR KEY ID HERE";
string appName = "YOUR APP NAME HERE";
string appId = "YOUR APP ID HERE";
NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
nm.UpdateNotificationHubAsync(desc);
```

## <a name="next-steps"></a>Nästa steg

* [Skapa en Azure Notification Hub i Azure Portal](create-notification-hub-portal.md)
* [Konfigurera en Notification Hub i Azure Portal](create-notification-hub-portal.md)
