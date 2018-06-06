---
title: Där Azure AD lagrar identitetsdata för Europeiska kunder | Microsoft Docs
description: Lär dig om Microsoft Azure Active Directory lagrar identitetsrelaterade data för dess europeiska kunder.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.author: lizross
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.custom: it-pro
ms.openlocfilehash: 2ae60c620db4fb3d88554ad5653729c7c1325e97
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713498"
---
# <a name="where-does-microsoft-azure-active-directory-azure-ad-store-identity-data-for-european-customers"></a>Om Microsoft Azure Active Directory (AD Azure) lagrar identitetsdata för Europeiska kunder
Azure AD hjälper dig att hantera användaridentiteter och skapa intelligence-driven åtkomstprinciper som att skydda din organisations resurser. Identitetsdata lagras på en plats som har baserat på adressen som din organisation när du prenumererar på tjänsten. Till exempel när du prenumererar på Office 365 eller Azure. Du kan använda mer specifik information om var identitetsdata lagras i [där är dina data finns?](https://www.microsoft.com/en-us/trustcenter/privacy/where-your-data-is-located) delen av Microsoft Trust Center.

De flesta Azure AD-relaterade Europeiska identitetsdata förblir i Europeiska datacenter, finns men det fem användarrelaterade attribut som lagras vanligtvis i USA datacenter. Attributen är GivenName, efternamn, userPrincipalName, domän och PasswordHash. Attributet PasswordHash kan vara ett undantag och lagras inte i USA om någon använder ett lokalt, federerade autentiseringsmetod som stoppar PasswordHash värdet synkroniseras med Azure AD. Det finns också vissa operativa, tjänstspecifika data som krävs för normal Azure AD-åtgärden, som lagras i USA och inte innehåller alla personliga data.

## <a name="data-stored-outside-of-european-datacenters-for-european-customers"></a>Data som lagras utanför Europeiska Datacenter för Europeiska kunder

Den mest Azure AD-relaterade Europeiska identitetsdata för organisationer med Europeiska adresser förblir i Europeiska datacenter. Azure AD-data som inte lagras i Europeiska Datacenter omfattar:

- **Identitetsrelaterade attribut**

    Följande identitetsrelaterade attribut kommer att replikeras till USA:

    - givenName
    - Efternamn
    - userPrincipalName
    - Domän
    - PasswordHash
    - sourceAnchor
    - accountEnabled
    - passwordPolicies
    - StrongAuthenticationRequirement
    - ApplicationPassword
    - PUID

- **Microsoft Azure Multi-Factor authentication (MFA) och Azure AD lösenordsåterställning via självbetjäning (SSPR)**
    
    MFA lagrar alla data i vila i Europeiska datacenter. Dock vissa MFA tjänstspecifika data lagras i USA, inklusive:
    
    - Tvåfaktorsautentisering och dess relaterade personliga data lagras i USA om du använder MFA eller SSPR.
        - Alla tvåfaktorsautentisering med telefonsamtal eller SMS kan utföras av USA leverantörer.
        - Push-meddelanden med Microsoft Authenticator-appen kräver meddelanden från tillverkarens meddelandetjänsten (Apple eller Google), som kan vara utanför Europa.
        - OATH-koder verifieras alltid i USA 
    - Vissa MFA och SSPR-loggfilerna lagras i USA för 30 dagar oavsett autentiseringstyp.

- **Microsoft Azure Active Directory B2C (Azure AD B2C)**

    Azure AD B2C lagrar alla data i vila i Europeiska datacenter. Arbetsloggarna (med dina personliga data bort) men stanna kvar på platsen där personen som har åtkomst till tjänsterna. Till exempel om en B2C-användare får åtkomst till tjänsten i USA, operativa loggar stanna kvar i USA Dessutom lagras alla konfigurationsdata för principen som inte innehåller personuppgifter endast i USA Mer information om principkonfigurationer som finns på [Azure Active Directory B2C: inbyggda principer](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-reference-policies) artikel.

- **Microsoft Azure Active Directory B2B (Azure AD B2B)** 
    
    Azure AD B2B lagrar alla data i vila i Europeiska datacenter. B2B lagrar men dess icke-personlig metadata i tabeller i USA datacenter. Den här tabellen innehåller fälten som redeemUrl, invitationTicket, resurs klient-Id, InviteRedirectUrl och InviterAppId.

- **Microsoft Azure Active Directory Domain Services (Azure AD DS)**

    Azure AD DS lagrar användardata på samma plats som den valda kunden Azure Virtual Network. Så om nätverket utanför Europa data replikeras och lagras utanför Europa.

- **Tjänster och appar som är integrerade med Azure AD**

    Alla tjänster och appar som integreras med Azure AD har åtkomst till identitetsdata. Utvärdera varje tjänst och appen att fastställa hur identitetsdata bearbetas av den specifika service och app, och om de uppfyller företagets krav för datalagring.

    Mer information om Microsoft services data land finns i [där är dina data finns?](https://www.microsoft.com/en-us/trustcenter/privacy/where-your-data-is-located) delen av Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg
Mer information om någon av funktionerna som beskrivs ovan finns i följande artiklar.
- [Kom igång med Azure Active Directory](get-started-azure-ad.md)
- [Vad är Multi-Factor Authentication?](https://docs.microsoft.com/en-us/azure/active-directory/authentication/multi-factor-authentication)
- [Azure AD Självbetjäning för lösenordsåterställning](https://docs.microsoft.com/en-us/azure/active-directory/authentication/active-directory-passwords-overview)
- [Vad är Azure Active Directory B2C?](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-overview)
- [Vad är Azure AD B2B-samarbete?](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/en-us/azure/active-directory-domain-services/active-directory-ds-overview)
