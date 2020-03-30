---
title: Scope för v1.0-appar (MSAL) | Azure
description: Lär dig mer om scope för ett v1.0-program med Hjälp av Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/25/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: d5b2ef57af112169fb39e0da7a60b095698ff504
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299838"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Scope för ett webb-API som accepterar v1.0-token

OAuth2-behörigheter är behörighetsscope som en Azure Active Directory (Azure AD) för utvecklare (v1.0) webb-API (resurs) visar klientprogram. Dessa behörighetsscope kan beviljas klientprogram under medgivandet. Se avsnittet `oauth2Permissions` om i [Azure Active Directory-programmanifestreferensen](reference-app-manifest.md#manifest-reference).

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Scope för att begära åtkomst till specifika OAuth2-behörigheter för ett v1.0-program

Om du vill hämta token för specifika scope för ett v1.0-program (till exempel Microsoft Graph API, som är https://graph.microsoft.com), skapar du scope genom att sammanfoga en önskad resursidentifierare med en önskad OAuth2-behörighet för den resursen.

Till exempel, för att komma åt på uppdrag av användaren en v1.0 webb-API där app-ID URI är: `ResourceId`

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Om du vill läsa och skriva med MSAL.NET Azure\/AD med hjälp av Microsoft Graph API (https: /graph.microsoft.com/) måste du skapa en lista över scope som visas i följande exempel:

```csharp
string ResourceId = "https://graph.microsoft.com/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.microsoft.com/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Om du vill skriva det scope som\/motsvarar Azure Resource Manager API (https: /management.core.windows.net/) måste du begära följande omfång (notera de två snedstrecken):

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Du måste använda två snedstreck eftersom Azure Resource Manager API förväntar sig ett snedstreck i sin målgrupp anspråk (aud), och sedan finns det ett snedstreck för att skilja API-namnet från omfånget.

Logiken som används av Azure AD är följande:

- För ADAL-slutpunkt (Azure AD v1.0) med en v1.0-åtkomsttoken (den enda möjliga), aud=resurs
- För MSAL-slutpunkt (Microsoft Identity Platform (v2.0)) som ber en åtkomsttoken för en resurs som accepterar v2.0-token,`aud=resource.AppId`
- För MSAL (v2.0-slutpunkt) som frågar en åtkomsttoken för en resurs som accepterar en v1.0-åtkomsttoken (vilket är fallet ovan), tolkar Azure AD önskad målgrupp från det begärda omfånget genom att ta allt före det sista snedstrecket och använda den som resursidentifierare. Om https:\//database.windows.net förväntar sig en målgrupp på\/"https: /database.windows.net/", måste du därför begära ett\/scope med "https: /database.windows.net//.default". Se även GitHub-problemet [#747: Resurs-url:s avslutande snedstreck utelämnas, vilket orsakade sql auth-fel](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

## <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Scope för att begära åtkomst till alla behörigheter för ett v1.0-program

Om du vill hämta en token för alla statiska scope för ett v1.0-program lägger du till ".default" i API:et för app-ID:

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

```javascript
var ResourceId = "someAppIDURI";
var scopes = [ ResourceId + "/.default"];
```

## <a name="scopes-to-request-for-a-client-credential-flowdaemon-app"></a>Scope för att begära en klientautentiseringsflöde/daemonapp

När det gäller klientautentiseringsflöde skulle omfattningen för att `/.default`passera också vara . Detta talar om för Azure AD: "alla behörigheter på appnivå som administratören har samtyckt till i programregistreringen.
