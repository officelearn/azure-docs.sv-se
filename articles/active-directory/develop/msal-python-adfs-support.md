---
title: Azure AD FS-stöd (MSAL Python)
titleSuffix: Microsoft identity platform
description: Lär dig mer om AD FS-stöd (Active Directory Federation Services) i Microsoft Authentication Library for Python
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/23/2019
ms.author: abpati
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 01d4cb626aabc83117e864b75b49eec63a6c0af0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76699554"
---
# <a name="active-directory-federation-services-support-in-msal-for-python"></a>Stöd för Active Directory Federation Services i MSAL för Python

Med AD FS (Active Directory Federation Services) i Windows Server kan du lägga till OpenID Connect- och OAuth 2.0-baserad autentisering och auktorisering till dina appar med hjälp av Microsoft Authentication Library (MSAL) för Python. Med MSAL för Python-biblioteket kan appen autentisera användare direkt mot AD FS. Mer information om scenarier finns i [AD FS-scenarier för utvecklare](/windows-server/identity/ad-fs/ad-fs-development).

Det finns vanligtvis två sätt att autentisera mot AD FS:

- MSAL Python talar med Azure Active Directory, som i sig är federerade med andra identitetsleverantörer. Federationen sker genom AD FS. MSAL Python ansluter till Azure AD, som loggar in användare som hanteras i Azure AD (hanterade användare) eller användare som hanteras av en annan identitetsprovider som AD FS (federerade användare). MSAL Python vet inte att en användare är federerad. Det talar helt enkelt till Azure AD. Den [myndighet](msal-client-application-configuration.md#authority) som du använder i det här fallet är den vanliga myndigheten (myndighetsvärdnamn + klient, allmänning eller organisationer).
- MSAL Python talar direkt till en AD FS-myndighet. Detta stöds endast av AD FS 2019 och senare.

## <a name="connect-to-active-directory-federated-with-ad-fs"></a>Ansluta till Active Directory som är federerat med AD FS

### <a name="acquire-a-token-interactively-for-a-federated-user"></a>Skaffa en token interaktivt för en federerad användare

Följande gäller oavsett om du ansluter direkt till AD FS (Active Directory Federation Services) eller via Active Directory.

När du `acquire_token_by_authorization_code` `acquire_token_by_device_flow`ringer eller är användarupplevelsen vanligtvis följande:

1. Användaren anger sitt konto-ID.
2. Azure AD visar kortfattat meddelandet "Ta dig till organisationens sida" och användaren omdirigeras till inloggningssidan för identitetsleverantören. Inloggningssidan anpassas vanligtvis med organisationens logotyp.

Ad FS-versioner som stöds i det här federerade scenariot är:
- Active Directory Federation Services FS v2
- Active Directory Federation Services v3 (Windows Server 2012 R2)
- Active Directory Federation Services v4 (AD FS 2016)

### <a name="acquire-a-token-via-username-and-password"></a>Skaffa en token via användarnamn och lösenord

Följande gäller oavsett om du ansluter direkt till AD FS (Active Directory Federation Services) eller via Active Directory.

När du skaffar `acquire_token_by_username_password`en token med får MSAL Python identitetsprovidern att kontakta baserat på användarnamnet. MSAL Python hämtar en [SAML 1.1-token](reference-saml-tokens.md) från identitetsprovidern, som den sedan tillhandahåller till Azure AD som returnerar JWT (JWT).

## <a name="connecting-directly-to-ad-fs"></a>Ansluta direkt till AD FS

När du ansluter katalogen till AD FS kommer den myndighet som du vill använda för att bygga ditt program att vara något i stil med`https://somesite.contoso.com/adfs/`

MSAL Python stöder ADFS 2019.

Den stöder inte en direkt anslutning till ADFS 2016 eller ADFS v2. Om du behöver stödja scenarier som kräver en direkt anslutning till ADFS 2016 använder du den senaste versionen av ADAL Python. När du har uppgraderat ditt lokala system till ADFS 2019 kan du använda MSAL Python.

## <a name="next-steps"></a>Nästa steg

- Det federerade ärendet finns [i Konfigurera Azure Active Directory-inloggningsbeteende för ett program med hjälp av en home realm discovery-princip](../manage-apps/configure-authentication-for-federated-users-portal.md)