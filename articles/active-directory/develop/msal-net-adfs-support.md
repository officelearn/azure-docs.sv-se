---
title: AD FS stöd i Microsoft Authentication Library för .NET | Azure
description: Läs mer om Active Directory Federation Services (AD FS)-stöd i Microsoft Authentication Library för .NET (MSAL.NET).
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
ms.date: 06/03/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5963c5e83b5af3848edd934328caa1f095bd184
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66676735"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>Stöd för Active Directory Federation Services i MSAL.NET
Active Directory Federation Services (AD FS) i Windows Server kan du lägga till OpenID Connect och OAuth 2.0-baserad autentisering och auktorisering för att program som du utvecklar och de här programmen autentisera användare direkt mot AD FS. Mer information finns i [AD FS-scenarier för utvecklare](/windows-server/identity/ad-fs/overview/ad-fs-scenarios-for-developers).

Microsoft Authentication Library för .NET (MSAL.NET) stöder två scenarier för att autentisera mot AD FS:

- MSAL.NET pratar med Azure Active Directory, som i sin tur är *federerad* med AD FS.
- MSAL.NET föredrag *direkt* till en AD FS-utfärdare, där versionen av AD FS är OpenID Connect kompatibla (med början i AD FS 2019). Ansluta direkt till AD FS kan MSAL.NET att autentisera med appar som körs i [Azure Stack](https://azure.microsoft.com/overview/azure-stack/).

## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>MSAL ansluter till Azure AD, som är federerad med AD FS
MSAL.NET stöder anslutning till Azure AD, som loggar in managed-användare (användare som hanteras i Azure AD) eller med federerade användare (användare som hanteras av en annan identitetsprovider som AD FS). MSAL.NET vet inte om det faktum att användare är externa. Det är fråga kommunicerar med Azure AD.

Den [utfärdare](msal-client-application-configuration.md#authority) du användning i det här fallet är den vanliga utfärdaren (authority värdnamn + klient, vanliga eller organisationer).

### <a name="acquiring-a-token-interactively"></a>Skaffa en token som interaktivt
När du anropar den `AcquireTokenInteractive` metoden användarupplevelsen är vanligtvis:

1. Användaren anger sitt konto-ID.
2. Azure AD visar kort meddelandet ”tar dig till din organisations sidan”.
3. Användaren omdirigeras till inloggningssidan för identitetsprovidern. På inloggningssidan är vanligtvis anpassad med logotypen för organisationen.

AD FS-versioner som stöds i det här federerade scenariot är AD FS v2, AD FS v3 (Windows Server 2012 R2) och AD FS-v4 (AD FS 2016).

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>Skaffa en token med hjälp av AcquireTokenByIntegratedAuthentication eller AcquireTokenByUsernamePassword
När du hämtar en token med den `AcquireTokenByIntegratedAuthentication` eller `AcquireTokenByUsernamePassword` metoder, MSAL.NET hämtar identitetsprovider kontakta baserat på användarnamnet.  MSAL.NET tar emot en [SAML 1.1-token](reference-saml-tokens.md) efter att kontakta identitetsprovidern.  MSAL.NET ger sen SAML-token till Azure AD som en användare försäkran (liknar den [on-behalf-of-flöde](msal-authentication-flows.md#on-behalf-of)) att få tillbaka en JWT.

## <a name="msal-connects-directly-to-ad-fs"></a>MSAL ansluter direkt till AD FS
MSAL.NET stöder anslutning till AD FS 2019 som är öppna ID Connect kompatibla. När du ansluter direkt till AD FS, utfärdare som du vill använda för att skapa ditt program liknar `https://mysite.contoso.com/adfs/`.

Det finns för närvarande inga planer på att stöd för en direkt anslutning till AD FS 2016 eller AD FS v2 (som inte OpenID Connect kompatibla). Om du behöver stöd för scenarier som kräver en direkt anslutning till AD FS 2016 Använd den senaste versionen av [Azure Active Directory Authentication Library](active-directory-authentication-libraries.md#microsoft-supported-client-libraries). När du har uppgraderat din lokala dator till AD FS 2019, kommer du att kunna använda MSAL.NET.
