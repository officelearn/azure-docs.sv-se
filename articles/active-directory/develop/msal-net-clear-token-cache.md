---
title: Rensa token-cachen med Microsoft Authentication Library för .NET – Azure
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
ms.openlocfilehash: 1cee6443db0b019f79a80cf5b7c0e2a7a50240f2
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532656"
---
# <a name="clear-the-token-cache-using-msalnet"></a>Rensa token-cachen med MSAL.NET

När du [hämtar en](msal-acquire-cache-tokens.md) åtkomsttoken med Microsoft Authentication Library för .net (MSAL.net) cachelagras token. När programmet behöver en token ska det först anropa `AcquireTokenSilent` metoden för att kontrol lera om en acceptabel token finns i cacheminnet. 

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
