---
title: Azure Active Directory-autentiseringsprotokoll | Microsoft Docs
description: En översikt över autentiseringsprotokollet som stöds av Azure Active Directory (AD)
documentationcenter: dev-center-name
author: CelesteDG
services: active-directory
manager: mtillman
editor: ''
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 71b12b848b1be309823bac46a219c0bb25e26092
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
ms.locfileid: "34156525"
---
# <a name="azure-active-directory-authentication-protocols"></a>Azure Active Directory-autentiseringsprotokoll
Azure Active Directory (AD Azure) stöder flera av de vanligaste protokollen för autentisering och auktorisering. I det här avsnittet beskrivs protokoll som stöds och deras implementering i Azure AD. Avsnitten ingår en granskning av anspråkstyper med stöd, en introduktion till användning av federationsmetadata, detaljerad OAuth 2.0. och referensdokumentationen för SAML 2.0-protokollet och instruktioner för felsökning.

## <a name="authentication-protocols-articles-and-reference"></a>Autentiseringsprotokoll artiklar och referens
* [Viktig Information om signering nyckeln förnyelse i Azure AD](active-directory-signing-key-rollover.md) – Lär dig mer om Azure AD-signering nyckelförnyelse takt ändringar du kan göra att uppdatera nyckeln automatiskt och beskrivning för hur du uppdaterar de vanligaste scenarierna för programmet.
* [Token och anspråkstyper stöds](active-directory-token-and-claims.md) – Lär dig mer om anspråk i de token som Azure AD utfärdar.
* [Federationsmetadata](active-directory-federation-metadata.md) – Lär dig hur du hittar och tolka metadata dokument som genererar Azure AD.
* [OAuth 2.0 i Azure AD](active-directory-protocols-oauth-code.md) – Lär dig mer om implementering av OAuth 2.0 i Azure AD.
* [OpenID Connect 1.0](active-directory-protocols-openid-connect-code.md) – Lär dig hur du använder OAuth 2.0 genom en auktorisering-protokollet för autentisering.
* [Tjänst-till-anrop med klientens autentiseringsuppgifter](active-directory-protocols-oauth-service-to-service.md) – Lär dig hur du använder bevilja för OAuth 2.0-klientautentiseringsuppgifter för tjänst-till-anrop.
* [Tjänst-till-anrop med On-Behalf-Of flöde](active-directory-protocols-oauth-on-behalf-of.md) – Lär dig att använda OAuth 2.0 On-Behalf-Of flödet för tjänst-till-anrop.
* [Referens för SAML-protokollet](active-directory-saml-protocol-reference.md) – Lär dig mer om profiler för enkel inloggning och enkel Sign-out SAML Azure AD.

## <a name="see-also"></a>Se även
[Azure Active Directory-Guide för utvecklare](active-directory-developers-guide.md)

[Active Directory-kodexempel](active-directory-code-samples.md)
