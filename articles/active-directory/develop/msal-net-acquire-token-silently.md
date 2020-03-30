---
title: Hämta en token från cacheminnet (MSAL.NET)
titleSuffix: Microsoft identity platform
description: Lär dig hur du hämtar en åtkomsttoken tyst (från tokencachen) med hjälp av Microsoft Authentication Library for .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 90189a1d7fd6421b7a24940e8c6ed615fa0df6d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084830"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Hämta en token från tokencachen med MSAL.NET

När du hämtar en åtkomsttoken med Microsoft Authentication Library for .NET (MSAL.NET) cachelagras token. När programmet behöver en token bör `AcquireTokenSilent` den först anropa metoden för att verifiera om en godtagbar token finns i cacheminnet. I många fall är det möjligt att skaffa en annan token med fler scope baserat på en token i cacheminnet. Det är också möjligt att uppdatera en token när den närmar sig förfallodatum (eftersom tokencachen också innehåller en uppdateringstoken).

Det rekommenderade mönstret `AcquireTokenSilent` är att anropa metoden först.  Om `AcquireTokenSilent` misslyckas, sedan skaffa en token med andra metoder.

I följande exempel försöker programmet först hämta en token från tokencachen.  Om `MsalUiRequiredException` ett undantag genereras hämtar programmet en token interaktivt. 

```csharp
AuthenticationResult result = null;
var accounts = await app.GetAccountsAsync();

try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
 // A MsalUiRequiredException happened on AcquireTokenSilent.
 // This indicates you need to call AcquireTokenInteractive to acquire a token
 System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

 try
 {
    result = await app.AcquireTokenInteractive(scopes)
          .ExecuteAsync();
 }
 catch (MsalException msalex)
 {
    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
 }
}
catch (Exception ex)
{
 ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
 return;
}

if (result != null)
{
 string accessToken = result.AccessToken;
 // Use the token
}
```
