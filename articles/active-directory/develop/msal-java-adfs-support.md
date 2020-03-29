---
title: AD FS-stöd (MSAL för Java)
titleSuffix: Microsoft identity platform
description: Lär dig mer om AD FS-stöd (Active Directory Federation Services) i Microsoft Authentication Library for Java (MSAL4j).
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/21/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 3d834a8d1524595304c22fed9897094622dfd93f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696222"
---
# <a name="active-directory-federation-services-support-in-msal-for-java"></a>Stöd för Active Directory Federation Services i MSAL för Java

Med AD FS (Active Directory Federation Services) i Windows Server kan du lägga till OpenID Connect- och OAuth 2.0-baserad autentisering och auktorisering i din MSAL-app (Microsoft Authentication Library for Java). När den har integrerats kan din app autentisera användare i AD FS, federerade via Azure AD. Mer information om scenarier finns i [AD FS-scenarier för utvecklare](/windows-server/identity/ad-fs/ad-fs-development).

En app som använder MSAL för Java kommer att prata med Azure Active Directory (Azure AD), som sedan matas till AD FS.

MSAL för Java ansluter till Azure AD, som loggar in användare som hanteras i Azure AD (hanterade användare) eller användare som hanteras av en annan identitetsprovider som AD FS (federerade användare). MSAL för Java vet inte att en användare är federerad. Det talar helt enkelt till Azure AD.

Den [myndighet](msal-client-application-configuration.md#authority) som du använder i det här fallet är den vanliga myndigheten (myndighetsvärdnamn + klient, allmänning eller organisationer).

## <a name="acquire-a-token-interactively-for-a-federated-user"></a>Skaffa en token interaktivt för en federerad användare

När du `ConfidentialClientApplication.AcquireToken()` `PublicClientApplication.AcquireToken()` ringer `AuthorizationCodeParameters` `DeviceCodeParameters`eller med eller är användarupplevelsen vanligtvis:

1. Användaren anger sitt konto-ID.
2. Azure AD visar kortfattat "Ta dig till organisationens sida" och användaren omdirigeras till inloggningssidan för identitetsleverantören. Inloggningssidan anpassas vanligtvis med organisationens logotyp.

Ad FS-versioner som stöds i det här federerade scenariot är:
- Active Directory Federation Services FS v2
- Active Directory Federation Services v3 (Windows Server 2012 R2)
- Active Directory Federation Services v4 (AD FS 2016)

## <a name="acquire-a-token-via-username-and-password"></a>Skaffa en token via användarnamn och lösenord

När du skaffar `ConfidentialClientApplication.AcquireToken()` `PublicClientApplication.AcquireToken()` en `IntegratedWindowsAuthenticationParameters` `UsernamePasswordParameters`token med eller med eller får MSAL för Java identitetsleverantören att kontakta baserat på användarnamnet. MSAL för Java får en [SAML 1.1 token](reference-saml-tokens.md) token från identitetsprovidern, som den sedan tillhandahåller till Azure AD som returnerar JWT (JWT).

## <a name="next-steps"></a>Nästa steg

Det federerade ärendet finns [i Konfigurera Azure Active Directory-inloggningsbeteende för ett program med hjälp av en home realm discovery-princip](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal)