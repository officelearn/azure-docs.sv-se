---
title: Azure Active Directory autentiseringsprotokoll | Microsoft Docs
description: En översikt över de autentiseringsprotokoll som stöds av Azure Active Directory (AD)
author: rwike77
services: active-directory
manager: CelesteDG
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/27/2017
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: b370736a5e4994651499716e3a923cf59465ca96
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74845424"
---
# <a name="azure-active-directory-authentication-protocols"></a>Azure Active Directory autentiseringsprotokoll

Azure Active Directory (Azure AD) stöder flera av de vanligaste protokollen för autentisering och auktorisering. Ämnena i det här avsnittet beskriver de protokoll som stöds och deras implementering i Azure AD. I avsnitten ingår en granskning av de anspråks typer som stöds, en introduktion till användningen av federationsmetadata, detaljerad OAuth 2,0. och ett fel söknings avsnitt med SAML 2,0-protokoll referens dokumentation.

## <a name="authentication-protocols-articles-and-reference"></a>Artiklar och referens för autentiseringsprotokoll

* [Viktig information om förnyelse av signerings nyckel i Azure AD](active-directory-signing-key-rollover.md) – Läs mer om förnyelse av takt i Azure AD – ändringar som du kan göra för att uppdatera nyckeln automatiskt och diskussion om hur du uppdaterar de vanligaste program scenarierna.
* [Token och anspråks typer som stöds](v1-id-and-access-tokens.md) – Läs mer om anspråk i de tokens som Azure AD utfärdar.
* [Federationsmetadata](azure-ad-federation-metadata.md) – lär dig hur du hittar och tolkar de metadata-dokument som Azure AD genererar.
* [Oauth 2,0 i Azure AD](v1-protocols-oauth-code.md) – lär dig mer om implementeringen av OAuth 2,0 i Azure AD.
* [OpenID Connect 1,0](v1-protocols-openid-connect-code.md) – lär dig hur du använder OAuth 2,0, ett Authorization-protokoll, för autentisering.
* [Tjänst-till-tjänst-anrop med klientautentiseringsuppgifter](v1-oauth2-client-creds-grant-flow.md) – lär dig hur du använder OAuth 2,0-klientens autentiseringsuppgifter för att tilldela tjänst-till-tjänst-anrop.
* [Tjänst-till-tjänst-anrop med on-of-Flow](v1-oauth2-on-behalf-of-flow.md) – lär dig hur du använder OAuth 2,0 på uppdrag av Flow för tjänst-till-tjänst-anrop.
* [Referens för SAML-protokoll](active-directory-saml-protocol-reference.md) – lär dig mer om SAML-profilerna för enkel inloggning och enkel inloggning i Azure AD.

## <a name="see-also"></a>Se också

* [Guide för Azure Active Directory utvecklare](v1-overview.md)
* [Active Directory kod exempel](sample-v1-code.md)
