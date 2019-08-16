---
title: Omfattningar för ett v 1.0-program (Microsoft Authentication Library) | Azure
description: Läs om omfattningarna för ett v 1.0-program med hjälp av Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0590614e1c1bc7331246e76fa26a6567a05324e6
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532334"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Omfattningar för ett webb-API som accepterar v 1.0-token

OAuth2-behörigheter är behörighets omfattningar som ett Azure AD for Developers (v 1.0) webb-API (resurs) exponerar för klient program. Dessa behörighets omfattningar kan beviljas till klient program under medgivande. Se avsnittet om `oauth2Permissions` [referens för Azure Active Directory program manifest](reference-app-manifest.md#manifest-reference).

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Omfattningar för att begära åtkomst till specifika OAuth2-behörigheter för ett v 1.0-program
Om du vill hämta token för specifika omfång av ett v 1.0-program (till exempel Azure AD-grafen, som är https:\//Graph.Windows.net), måste du skapa omfattningar genom att sammanfoga ett önskat resurs-ID med önskad OAuth2-behörighet för resursen.

Till exempel för att komma åt användaren för en v 1.0-webb-API där app-ID-URI `ResourceId`: n är:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Om du vill läsa och skriva med MSAL.net Azure Active Directory med hjälp av Azure AD Graph API (https:\//Graph.Windows.net/) skapar du en lista över omfång som i följande avsnitt:

```csharp
string ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.windows.net/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Om du vill skriva det omfång som motsvarar Azure Resource Manager API (https:\//Management.Core.Windows.net/) måste du begära följande omfång (Observera de två snedstrecken):

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Du måste använda två snedstreck eftersom Azure Resource Manager-API: et förväntar sig ett snedstreck i sitt Audience-anspråk (AUD) och det finns ett snedstreck för att avgränsa API-namnet från omfånget.

Den logik som används av Azure AD är följande:

- För ADAL-slutpunkt (v 1.0) med en v 1.0-åtkomsttoken (endast möjlig), AUD = Resource
- För MSAL (Microsoft Identity Platform (v 2.0) slut punkt) som frågar en åtkomsttoken för en resurs som accepterar v 2.0-token, AUD = Resource. Undanta
- För MSAL (v 2.0-slutpunkt) som frågar en åtkomsttoken för en resurs som accepterar en v 1.0-åtkomsttoken (vilket är fallet ovan), parsar Azure AD den önskade mål gruppen från det begärda omfånget genom att ta allt före det sista snedstrecket och använda det som resurs-ID. Om https\/:/Database.Windows.net förväntar sig en mål grupp av "\/https:/Database.Windows.net/" måste du begära ett omfånget "https:\//Database.Windows.net//.default". Se även GitHub problem [#747: Resurs-URL: en avslutande snedstreck utelämnas, vilket orsakade SQL-](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)auth-haveriet.

## <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Omfattningar för att begära åtkomst till alla behörigheter för ett v 1.0-program
Om du vill hämta en token för alla statiska omfattningar i ett v 1.0-program lägger du till ". default" i app-ID-URI: n för API: et:

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

```javascript
var ResourceId = "someAppIDURI";
var scopes = [ ResourceId + "/.default"];
```

## <a name="scopes-to-request-for-client-credential-flow--daemon-app"></a>Omfattningar som ska begäras för Flow/daemon-appen för klientens autentiseringsuppgifter
Om det gäller klientens Credential-flöde, är `/.default`det också den omfattning som passas. Detta säger till Azure AD: "alla behörigheter på program nivå som administratören har samtyckt till i program registreringen.
