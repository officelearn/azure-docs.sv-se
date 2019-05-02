---
title: Identity-datalagring för Europeiska kunder – Azure Active Directory | Microsoft Docs
description: Läs mer om där Azure Active Directory lagrar identitetsrelaterade data för dess europeiska kunder.
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/04/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93ac5ef5f03f800a8f90259db3e382b3bc5c5e2c
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64875643"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Identity-datalagring för Europeiska kunder i Azure Active Directory
Identitetsdata lagras av Azure AD i en geografisk plats baserat på den adress som tillhandahålls av din organisation när du prenumererar för Microsoft Online service som Office 365 och Azure. För information om din identitetsdata ska lagras, kan du använda den [var är dina data finns?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) avsnitt i Microsoft Trust Center.

För kunder som tillhandahålls en adress i Europa, ser de flesta av identitetsdata i Europeiska Datacenter till Azure AD. Det här dokumentet innehåller information om alla data som lagras utanför Europa med Azure AD-tjänster.

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure multifaktorautentisering (MFA)
    
- Alla tvåfaktorsautentisering med telefonsamtal eller SMS kommer från datacenter i USA och styrs även av globala leverantörer.
- Push-meddelanden med hjälp av Microsoft Authenticator app som kommer från datacenter. Dessutom kan kan enheten leverantör specifika tjänster förekomma i play och dessa tjänster kanske utanför Europa.
- OATH-koder verifieras alltid i USA. 

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Azure AD B2C-princip konfigurationsdata och nyckelbehållare lagras i datacenter i USA. Dessa innehåller inte några personliga användardata. Mer information om principkonfigurationer, finns det [Azure Active Directory B2C: Inbyggda principer](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) artikeln.

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Azure AD B2B butiker inbjudningar med Lös in länka och omdirigerings-URL-information i datacenter i USA. Dessutom lagras också e-postadress som slutar prenumerera på att ta emot B2B inbjudningar i datacenter i USA.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory Domain Services (Azure AD DS)

Azure AD DS lagrar användardata på samma plats som kundens valda Azure Virtual Network. Om nätverket finns utanför Europa replikeras och lagras alltså dessa data utanför Europa.

## <a name="other-considerations"></a>Annat att tänka på

Tjänster och program som integreras med Azure AD har du åtkomst till identitetsdata. Utvärdera varje tjänst och program som du använder för att fastställa hur identitetsdata bearbetas av den specifika tjänsten och programmet och om de uppfyller företagets krav för datalagring.

Läs mer om Microsoft-tjänsternas datahemvist i avsnittet [Var finns dina data?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) på Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg
Mer information om någon av funktionerna som beskrivs ovan, finns i följande artiklar:
- [Vad är Multi-Factor Authentication?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Lösenordsåterställning via självbetjäning i Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [Vad är Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [Vad är Azure AD B2B-samarbete?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
