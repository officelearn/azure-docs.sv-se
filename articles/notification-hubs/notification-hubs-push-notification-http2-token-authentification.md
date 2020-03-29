---
title: Tokenbaserad (HTTP/2) autentisering för APNS i Azure Notification Hubs | Microsoft-dokument
description: Lär dig hur du använder den nya tokenautentiseringen för APNS.
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
ms.openlocfilehash: 448b5c38371024c2eae900f4f87b343ee0a3b36a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263820"
---
# <a name="token-based-http2-authentication-for-apns"></a>Tokenbaserad (HTTP/2) autentisering för APNS

## <a name="overview"></a>Översikt

I den här artikeln beskrivs hur du använder det nya APNS HTTP/2-protokollet med tokenbaserad autentisering.

De viktigaste fördelarna med att använda det nya protokollet är:

* Tokengenerering är relativt enkel (jämfört med certifikat)
* Inga fler utgångsdatum – du har kontroll över dina autentiseringstoken och återkallande av dem
* Nyttolaster kan nu vara upp till 4 KB
* Synkron feedback
* Du är på Apples senaste protokoll – certifikat använder fortfarande det binära protokollet, som är markerat för utfasning

Med hjälp av denna nya mekanism kan utföras i två steg:

* Hämta nödvändig information från Apples utvecklarkontoportal.
* Konfigurera meddelandehubben med den nya informationen.

Meddelandehubbar är nu inställda på att använda det nya autentiseringssystemet med APNS.

Observera att om du migrerade från att använda certifikatuppgifter för APNS, överskrivningar tokenegenskaperna ditt certifikat i vårt system, men ditt program fortsätter att ta emot meddelanden sömlöst.

## <a name="obtaining-authentication-information-from-apple"></a>Hämta autentiseringsinformation från Apple

Om du vill aktivera tokenbaserad autentisering behöver du följande egenskaper från ditt Apple Developer-konto:

### <a name="key-identifier"></a>Nyckelidentifierare

Nyckelidentifieraren kan erhållas från sidan **Nycklar** under **Certifikat, identifierare & profiler**i ditt Apple Developer-konto:

![](./media/notification-hubs-push-notification-http2-token-authentification/keys.png)

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier-and-application-name"></a>Programidentifierare och programnamn

Programnamnet och identifieraren är också tillgängliga på sidan **Certifikat, identifierare & profiler** i utvecklarkontot:

![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

### <a name="configure-via-the-net-sdk-or-the-azure-portal"></a>Konfigurera via .NET SDK eller Azure-portalen

Du kan konfigurera hubben så att den använder tokenbaserad autentisering med hjälp av vår [senaste klient SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs)eller i Azure-portalen. Om du vill aktivera tokenbaserad autentisering i portalen loggar du in på Azure-portalen och går till panelen Inställningar för > **Apple (APNS).** Välj **Token** från egenskapen **Autentiseringsläge** för att uppdatera hubben med alla relevanta tokenegenskaper.

![Konfigurera token](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Ange de egenskaper som du har hämtat från ditt Apple Developer-konto.
* Välj programläge (**Produktion** eller **Sandbox**).
* Klicka på **knappen Spara** om du vill uppdatera APNS-autentiseringsuppgifterna.

Tokenbaserade autentiseringsuppgifter består av följande fält:

* **Nyckel-ID:** Identifierare för den privata nyckel som genereras i Apples utvecklarportal; till exempel `2USFGKSKLT`.
* **Team-ID:** Kallas även "Prefix" eller "Appprefix". Detta är identifieraren för organisationen i Apples utvecklarportal. till exempel `S4V3D7CHJR`.
* **Bunt-ID:** Kallas även "App-ID". Detta är buntidentifieraren för programmet. till exempel `com.microsoft.nhubsample2019`. Observera att du kan använda en nyckel för många appar. Det här värdet `apns-topic` mappas till HTTP-huvudet när du skickar ett meddelande och används för att rikta det specifika programmet.
* **Token**: Kallas även "Nyckel" eller "Privat nyckel". Detta erhålls från .p8-filen som genereras på Apples utvecklarportal. Nyckeln måste ha APNS aktiverat (som väljs på Apple Developer-portalen när du genererar nyckeln). Värdet måste ha PEM-sidhuvudet/sidfoten borttagen från det när du anger det till NH Portal/API.
* **Slutpunkt:** Det här är en växlingsknapp i portalbladet Notification Hubs och ett strängfält i API:et. Giltiga värden `https://api.push.apple.com` `https://api.sandbox.push.apple.com`är eller . Meddelandehubbar använder det här värdet för antingen produktions- eller sandlådemiljön för att skicka meddelanden. Detta måste `aps-environment` matcha berättigandet i appen, annars matchar inte APNS-enhetstoken som genereras miljön och meddelandena kan inte skickas.

Här är ett kodexempel som illustrerar rätt användning:

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

* [Skapa en Azure-meddelandehubb i Azure-portalen](create-notification-hub-portal.md)
* [Konfigurera en meddelandehubb i Azure-portalen](create-notification-hub-portal.md)
