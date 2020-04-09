---
title: Autentiseringsprotokoll för Microsoft-identitetsplattform
description: En översikt över de autentiseringsprotokoll som stöds av Microsofts identitetsplattform
author: rwike77
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: 41ea41b4d7c181dad9246653a68c329387ac5381
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884689"
---
# <a name="microsoft-identity-platform-authentication-protocols"></a>Autentiseringsprotokoll för Microsoft-identitetsplattform

Microsofts identitetsplattform stöder flera av de mest använda autentiserings- och auktoriseringsprotokollen. I det här avsnittet beskrivs protokoll som stöds och deras implementering på Microsofts identitetsplattform. Ämnena inkluderade en granskning av anspråkstyper som stöds, en introduktion till användningen av federationsmetadata, detaljerad OAuth 2.0. och SAML 2.0-dokumentation för protokollreferens och ett felsökningsavsnitt.

## <a name="authentication-protocols-articles-and-reference"></a>Artiklar och referens för autentiseringsprotokoll

* [Viktig information om signering av nyckelöverrullning i Microsofts identitetsplattform](active-directory-signing-key-rollover.md) – Läs mer om Microsoft identity-plattformens rollover-kadens för signeringsnyckel, ändringar som du kan göra för att uppdatera nyckeln automatiskt och diskutera hur du uppdaterar de vanligaste programscenarierna.
* [Token- och anspråkstyper som stöds](id-tokens.md) – Lär dig mer om anspråk i de token som Microsoft identity-plattformen utfärdar.
* [OAuth 2.0 i Microsofts identitetsplattform](v2-oauth2-auth-code-flow.md) – Lär dig mer om implementeringen av OAuth 2.0 i Microsofts identitetsplattform.
* [OpenID Connect 1.0](v2-protocols-oidc.md) - Lär dig hur du använder OAuth 2.0, ett auktoriseringsprotokoll, för autentisering.
* [Service till servicesamtal med klientreferenser](v2-oauth2-client-creds-grant-flow.md) – Lär dig hur du använder OAuth 2.0-klientautentiseringsuppgifter bevilja flöde för service till servicesamtal.
* [Service till servicesamtal med on-behalf-Of Flow](v2-oauth2-on-behalf-of-flow.md) - Lär dig hur du använder OAuth 2.0 on-behalf-Of-flöde för service till servicesamtal.
* [SAML Protocol Reference](active-directory-saml-protocol-reference.md) – Lär dig mer om SAML-profilerna för enkel inloggning och enkel inloggning på Microsofts identitetsplattform.

## <a name="see-also"></a>Se även

* [Översikt över Microsofts identitetsplattform](v2-overview.md)
* [Exempel på Active Directory-kod](sample-v2-code.md)
