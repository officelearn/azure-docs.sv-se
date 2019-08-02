---
title: Identity-datalagring för Europeiska kunder – Azure Active Directory | Microsoft Docs
description: Läs mer om där Azure Active Directory lagrar identitetsrelaterade data för dess europeiska kunder.
services: active-directory
author: msaburnley
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/04/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33704c3392726ccd12ca0fd74f26465a54697e11
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68722136"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Identity-datalagring för Europeiska kunder i Azure Active Directory
Identitets data lagras av Azure AD på en geografisk plats baserat på den adress som tillhandahålls av din organisation när du prenumererar på en Microsoft Online-tjänst, till exempel Office 365 och Azure. Information om var dina identitets data lagras, kan du använda avsnittet var finns [dina data?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) i Microsoft säkerhets Center.

För kunder som tillhandahöll en adress i Europa, behåller Azure AD de flesta identitets data inom de europeiska data centren. Det här dokumentet innehåller information om alla data som lagras utanför Europa av Azure AD-tjänster.

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure Multi-Factor Authentication (MFA)
    
- All tvåfaktorautentisering som använder telefonsamtal eller SMS härstammar från amerikanska data Center och dirigeras även av globala leverantörer.
- Push-meddelanden med hjälp av Microsoft Authenticator-appen kommer från amerikanska data Center. Dessutom kan enhetens leverantörsspecifika tjänster också komma i spel och dessa tjänster kanske inte finns utanför Europa.
- OATH-koder verifieras alltid i USA. 

Mer information om vilka användar uppgifter som samlas in av Azure Multi-Factor Authentication Server (MFA Server) och molnbaserad Azure MFA finns i [användar data insamling för Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting-datacollection).

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Azure AD B2C princip konfigurations data och nyckel behållare lagras i amerikanska data Center. Dessa innehåller inte några användares personliga data. Mer information om princip konfiguration finns [i Azure Active Directory B2C: Inbyggda principer](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) -artikeln.

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Azure AD B2B lagrar inbjudningar med Lös in länk-och omdirigerings-URL-information i amerikanska data Center. Dessutom lagras e-postadresserna till användare som avbryter prenumerationen på att ta emot B2B-inbjudningar också i amerikanska data Center.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory Domain Services (Azure AD DS)

Azure AD DS lagrar användardata på samma plats som kundens valda Azure Virtual Network. Om nätverket finns utanför Europa replikeras och lagras alltså dessa data utanför Europa.

## <a name="other-considerations"></a>Annat att tänka på

Tjänster och program som integreras med Azure AD har åtkomst till identitets data. Utvärdera varje tjänst och program som du använder för att avgöra hur identitets data bearbetas av den specifika tjänsten och programmet och om de uppfyller företagets data lagrings krav.

Läs mer om Microsoft-tjänsternas datahemvist i avsnittet [Var finns dina data?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) på Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg
Mer information om de funktioner och funktioner som beskrivs ovan finns i följande artiklar:
- [Vad är Multi-Factor Authentication?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Lösenordsåterställning via självbetjäning i Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [Vad är Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [Vad är Azure AD B2B-samarbete?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
