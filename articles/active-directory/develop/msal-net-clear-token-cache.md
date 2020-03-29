---
title: Rensa tokencachen (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du rensar tokencachen med hjälp av Microsoft Authentication Library for .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: a10efb5ff0a2c6a3ced3631dfe82c86e3e8a72fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084762"
---
# <a name="clear-the-token-cache-using-msalnet"></a>Rensa tokencachen med MSAL.NET

När du [hämtar en åtkomsttoken](msal-acquire-cache-tokens.md) med Microsoft Authentication Library for .NET (MSAL.NET) cachelagras token. När programmet behöver en token bör `AcquireTokenSilent` den först anropa metoden för att verifiera om en godtagbar token finns i cacheminnet. 

Rensa cacheminnet uppnås genom att ta bort kontona från cacheminnet. Detta tar dock inte bort sessionscookien som finns i webbläsaren.  I följande exempel instansierar ett offentligt klientprogram, hämtar konton för programmet och kontona tas bort.

```csharp
private readonly IPublicClientApplication _app;
private static readonly string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
private static readonly string Authority = string.Format(CultureInfo.InvariantCulture, AadInstance, Tenant);

_app = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(Authority)
                .Build();

var accounts = (await _app.GetAccountsAsync()).ToList();

// clear the cache
while (accounts.Any())
{
   await _app.RemoveAsync(accounts.First());
   accounts = (await _app.GetAccountsAsync()).ToList();
}

```

Om du vill veta mer om att hämta och cachelagring av token läser [du hämta en åtkomsttoken](msal-acquire-cache-tokens.md).
