---
title: Omfång för v1.0-program (Microsoft Authentication Library) | Azure
description: Läs mer om omfång för ett v1.0-program med Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44aad6b2fab7e0ab2ff11d8469782b001b1f4d18
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545901"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Omfång för ett webb-API som tar emot v1.0 token

OAuth2-behörigheter är behörighetsomfattningar som en Azure AD för utvecklare (v1.0) API (resurs) webbprogrammet exponerar för klientprogram. De här behörighetsomfattningarna kan ges klientprogram under medgivande. Se avsnittet `oauth2Permissions` i den [Azure Active Directory application manifest referens](reference-app-manifest.md#manifest-reference).

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Sökomfång för att begära åtkomst till specifika OAuth2-behörigheter i ett v1.0-program
Om du vill hämta token för specifika omfång i ett v1.0-program (till exempel i Azure AD graph, vilket är https://graph.windows.net), måste du skapa omfång genom att sammanfoga en önskad resurs-ID med en önskad OAuth2-behörighet för den resursen.

Till exempel till åtkomst åt användaren ett v1.0 webb-API där ID-URI: n är `ResourceId`:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Om du vill läsa och skriva MSAL.NET Azure Active Directory med hjälp av Azure AD graph API (https://graph.windows.net/), skulle du skapa en lista med omfattningar som i följande:

```csharp
string ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.windows.net/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Om du vill skriva området för Azure Resource Manager-API (https://management.core.windows.net/), du måste begära följande omfattningen (Observera två snedstreck):

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Du måste använda två snedstreck eftersom Azure Resource Manager API: et förväntar sig ett snedstreck i sin publik-begäran (aud) och det finns ett snedstreck att separera API-namn från omfånget.

Den logik som används av Azure AD är följande:

- För ADAL (v1.0) slutpunkten med en v1.0 åtkomsttoken (endast möjliga), aud = resurs
- För MSAL (Microsoft identity-plattformen (v2.0) slutpunkt) frågar en åtkomsttoken för en resurs som tar emot v2.0 token, aud = resurs. AppId
- För MSAL (v2.0-slutpunkt) frågar en åtkomsttoken för en resurs som tar emot en token för åtkomst av v1.0 (vilket är fallet ovan), tolkar målgrupp från det begärda omfånget genom att ta allt innan det senaste snedstrecket och använda det som resurs-ID i Azure AD. Därför om https://database.windows.net förväntar sig en målgrupp ”https://database.windows.net/”, måste du begära en omfattning ”https://database.windows.net//.default”. Se även GitHub problemet [#747: Resurs-url avslutande snedstreck utelämnas, vilket orsakade sql auth fel](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

## <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Sökomfång för att begära åtkomst till alla behörigheter för ett v1.0-program
Lägg till ”.default” till app-ID-URI för API: et om du vill hämta en token för alla statiska omfång i ett v1.0-program:

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

```javascript
var ResourceId = "someAppIDURI";
var scopes = [ ResourceId + "/.default"];
```

## <a name="scopes-to-request-for-client-credential-flow--daemon-app"></a>Sökomfång för att begära för klienten autentiseringsuppgifter flow / daemon-app
När det gäller klientflödet autentiseringsuppgifter, omfattning att skicka också skulle vara `/.default`. Detta talar om att Azure AD: ”alla appnivå behörigheter som administratören har godkänt att i program-registreringen.