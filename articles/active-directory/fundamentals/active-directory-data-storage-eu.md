---
title: Identitetsdatalagring för europeiska kunder - Azure AD
description: Läs om var Azure Active Directory lagrar identitetsrelaterade data för sina europeiska kunder.
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
ms.openlocfilehash: 7360b11f41cc08c2beb3ffa227e1658881798502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75423006"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Identitetsdatalagring för europeiska kunder i Azure Active Directory
Identitetsdata lagras av Azure AD på en geografisk plats baserat på den adress som tillhandahålls av din organisation när du prenumererar på en Microsoft Online-tjänst som Office 365 och Azure. Om du vill ha information om var dina identitetsdata lagras kan du använda avsnittet [Var finns dina data i](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) Microsoft Trust Center.

För kunder som har angett en adress i Europa behåller Azure AD de flesta identitetsdata i europeiska datacenter. Det här dokumentet innehåller information om alla data som lagras utanför Europa av Azure AD-tjänster.

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure multifaktorautentisering (MFA)
    
- All tvåfaktorsautentisering med telefonsamtal eller SMS kommer från amerikanska datacenter och dirigeras också av globala leverantörer.
- Push-meddelanden med Microsoft Authenticator-appen kommer från amerikanska datacenter. Dessutom kan enhetsleverantörsspecifika tjänster också spela in och dessa tjänster kanske utanför Europa.
- OATH-koder verifieras alltid i USA. 

Mer information om vilken användarinformation som samlas in av Azure Multi-Factor Authentication Server (MFA Server) och molnbaserad Azure MFA finns i [Azure Multi-Factor Authentication user data collection](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting-datacollection).

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Azure AD B2C princip konfigurationsdata och nyckelbehållare lagras i amerikanska datacenter. Dessa innehåller inga användarpersonliga uppgifter. Mer information om principkonfigurationer finns i artikeln om [inbyggda principer i Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies).

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Azure AD B2B lagrar inbjudningar med inlösningslänk och omdirigerar URL-information i amerikanska datacenter. Dessutom lagras även e-postadress för användare som avregistrerar sig från att ta emot B2B-inbjudningar i amerikanska datacenter.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory Domain Services (Azure AD DS)

Azure AD DS lagrar användardata på samma plats som kundens valda Azure Virtual Network. Om nätverket finns utanför Europa replikeras och lagras alltså dessa data utanför Europa.

## <a name="federation-in-microsoft-exchange-server-2013"></a>Federation i Microsoft Exchange Server 2013
    
- Application identifier (AppID) - Ett unikt nummer som genereras av Azure Active Directory-autentiseringssystemet för att identifiera Exchange-organisationer.
- Lista över godkända federerade domäner för program
- Programmets tokensignering av offentlig nyckel 

Mer information om federation på Microsoft Exchange-servern finns i [hjälpartikeln för Federation: Exchange 2013.](https://docs.microsoft.com/exchange/federation-exchange-2013-help)


## <a name="other-considerations"></a>Andra överväganden

Tjänster och program som integreras med Azure AD har åtkomst till identitetsdata. Utvärdera varje tjänst och program som du använder för att avgöra hur identitetsdata bearbetas av den specifika tjänsten och programmet och om de uppfyller företagets datalagringskrav.

Läs mer om Microsoft-tjänsternas datahemvist i avsnittet [Var finns dina data?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) på Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg
Mer information om någon av de funktioner som beskrivs ovan finns i följande artiklar:
- [Vad är Multi-Factor Authentication?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Azure AD-återställning av lösenord för självbetjäning](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [Vad är Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [Vad är Azure AD B2B-samarbete?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
