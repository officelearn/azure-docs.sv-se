---
title: Rensa token-cachen med Microsoft Authentication Library för .NET
titleSuffix: Microsoft identity platform
description: Lär dig hur du rensar token-cachen med Microsoft Authentication Library för .NET (MSAL.NET).
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
ms.date: 05/07/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bd48078ec87b085a71ee23006d18917881894bf
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802924"
---
# <a name="clear-the-token-cache-using-msalnet"></a>Rensa token-cachen med MSAL.NET

När du [hämtar en](msal-acquire-cache-tokens.md) åtkomsttoken med Microsoft Authentication Library för .net (MSAL.net) cachelagras token. När programmet behöver en token ska det först anropa `AcquireTokenSilent`-metoden för att kontrol lera om en acceptabel token finns i cacheminnet. 

Att rensa cacheminnet uppnås genom att ta bort kontona från cachen. Detta tar inte bort sessions-cookien som finns i webbläsaren, men.  I följande exempel instansieras ett offentligt klient program, hämtar konton för programmet och tar bort kontona.

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

Läs mer om att förvärva och cachelagra token genom att läsa [Hämta en](msal-acquire-cache-tokens.md)åtkomsttoken.
