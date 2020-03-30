---
title: AD FS-stöd i MSAL.NET | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om AD FS-stöd (Active Directory Federation Services) i Microsoft Authentication Library for .NET (MSAL.NET).
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
ms.openlocfilehash: 15af18177cea217612a4d5276d130abe02d339f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77160768"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>Stöd för Active Directory Federation Services i MSAL.NET
Med AD FS (Active Directory Federation Services) i Windows Server kan du lägga till OpenID Connect- och OAuth 2.0-baserad autentisering och auktorisering till program som du utvecklar. Dessa program kan sedan autentisera användare direkt mot AD FS. Mer information finns i [AD FS-scenarier för utvecklare](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios).

Microsoft Authentication Library for .NET (MSAL.NET) stöder två scenarier för autentisering mot AD FS:

- MSAL.NET samtal med Azure Active Directory, som i sig är *federerade* med AD FS.
- MSAL.NET samtal **direkt** till en ADFS-myndighet. Detta stöds endast från AD FS 2019 och uppåt. Ett av de scenarier som detta belyser är [Azure Stack-stöd](https://azure.microsoft.com/overview/azure-stack/)


## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>MSAL ansluter till Azure AD, som är federerat med AD FS
MSAL.NET stöder anslutning till Azure AD, som loggar in hanterade användare (användare som hanteras i Azure AD) eller federerade användare (användare som hanteras av en annan identitetsprovider som AD FS). MSAL.NET vet inte om det faktum att användarna är federerade. Vad det anlangar, det talar till Azure AD.

Den [myndighet](msal-client-application-configuration.md#authority) som du använder i det här fallet är den vanliga myndigheten (myndighetsvärdnamn + klient, allmänning eller organisationer).

### <a name="acquiring-a-token-interactively"></a>Skaffa en token interaktivt
När du `AcquireTokenInteractive` anropar metoden är användarupplevelsen vanligtvis:

1. Användaren anger sitt konto-ID.
2. Azure AD visar kortfattat meddelandet "Ta dig till organisationens sida".
3. Användaren omdirigeras till inloggningssidan för identitetsleverantören. Inloggningssidan anpassas vanligtvis med organisationens logotyp.

AD FS-versioner som stöds i det här federerade scenariot är AD FS v2, AD FS v3 (Windows Server 2012 R2) och AD FS v4 (AD FS 2016).

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>Skaffa en token med AcquireTokenByIntegratedAuthentication eller AcquireTokenByUsernamePassword
När du skaffar `AcquireTokenByIntegratedAuthentication` `AcquireTokenByUsernamePassword` en token med hjälp av metoderna eller får MSAL.NET identitetsleverantören att kontakta baserat på användarnamnet.  MSAL.NET får en [SAML 1.1-token](reference-saml-tokens.md) efter att ha kontaktat identitetsprovidern.  MSAL.NET tillhandahåller sedan SAML-token till Azure AD som ett användarpåstående (liknande [flödet för räkning)](msal-authentication-flows.md#on-behalf-of)för att få tillbaka en JWT.

## <a name="msal-connects-directly-to-ad-fs"></a>MSAL ansluter direkt till AD FS
MSAL.NET stöder anslutning till AD FS 2019, som är Open ID Connect-kompatibel och förstår PKCE och omfattningar. Det här stödet kräver att en Service [Pack-KB 4490481](https://support.microsoft.com/en-us/help/4490481/windows-10-update-kb4490481) tillämpas på Windows Server. När du ansluter direkt till AD FS liknar den myndighet som du `https://mysite.contoso.com/adfs/`vill använda för att skapa programmet .

För närvarande finns det inga planer på att stödja en direkt anslutning till:

- AD FS 16, eftersom det inte stöder PKCE och fortfarande använder resurser, inte omfattning
- AD FS v2, som inte är OIDC-kompatibel.

 Om du behöver stödja scenarier som kräver en direkt anslutning till AD FS 2016 använder du den senaste versionen av [Azure Active Directory Authentication Library](../azuread-dev/active-directory-authentication-libraries.md#microsoft-supported-client-libraries). När du har uppgraderat ditt lokala system till AD FS 2019 kan du använda MSAL.NET.

## <a name="next-steps"></a>Nästa steg

Det federerade ärendet finns [i Konfigurera Azure Active Directory-inloggningsbeteende för ett program med hjälp av en home realm discovery-princip](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal)
