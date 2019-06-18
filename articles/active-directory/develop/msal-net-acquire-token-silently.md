---
title: Hämta en token tyst (Microsoft Authentication Library för .NET) | Azure
description: Lär dig mer om att hämta en åtkomsttoken tyst (från tokencache) med hjälp av Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6331407067a39550d866d7c293a92fac9184b54e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544244"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Skaffa en token från tokens cacheminne med hjälp av MSAL.NET

När du köper en åtkomsttoken med hjälp av Microsoft Authentication Library för .NET (MSAL.NET) cachelagras token. När programmet behöver en token, bör det först anropa den `AcquireTokenSilent` metod för att verifiera om det är en godtagbar token i cacheminnet. I många fall är det möjligt att hämta en annan token med fler områden baserat på en token i cacheminnet. Du kan också uppdatera en token när den får nära förfallodatum (eftersom token cachen innehåller också en uppdateringstoken).

Rekommenderat mönster är att anropa den `AcquireTokenSilent` metoden första.  Om `AcquireTokenSilent` misslyckas kan sedan hämta en token med andra metoder.

I följande exempel försöker programmet först hämta en token från token-cache.  Om en `MsalUiRequiredException` undantagsfel, programmet hämtar en token interaktivt. 

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
 // A MsalUiRequiredException happened on AcquireTokenSilentAsync.
 // This indicates you need to call AcquireTokenAsync to acquire a token
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