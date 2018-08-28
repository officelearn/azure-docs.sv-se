---
title: Var lagrar Azure AD identitetsdata för europeiska kunder? | Microsoft Docs
description: Läs mer om var Microsoft Azure Active Directory lagrar identitetsrelaterade data för europeiska kunder.
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
ms.topic: overview
ms.date: 05/17/2018
ms.custom: it-pro
ms.openlocfilehash: 0baa499f56fa6c074ac1c0f604e74f9e7adb5502
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "42022870"
---
# <a name="where-does-microsoft-azure-active-directory-azure-ad-store-identity-data-for-european-customers"></a>Var lagrar Microsoft Azure Active Directory (Azure AD) identitetsdata för europeiska kunder?
Azure AD hjälper dig att hantera användaridentiteter och att skapa intelligensdrivna åtkomstprinciper som hjälper dig att skydda din organisations resurser. Identitetsdata lagras på en plats som baseras på den adress som din organisationen uppgav när du registrerade dig för tjänsten. Till exempel när du registrerar dig för en prenumeration på Office 365 eller Azure. Specifik information om var dina identitetsdata lagras finns i avsnittet [Var finns dina data?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) på Microsoft Trust Center.

De flesta Azure AD-relaterade europeiska identitetsdata lagras på europeiska datacenter, men det finns fem användarrelaterade attribut som vanligtvis lagras på datacenter i USA. Dessa attribut är GivenName, Surname, userPrincipalName, Domain och PasswordHash. Attributet PasswordHash kan vara ett undantag och lagras inte alltid i USA om någon använder en lokal, federerad autentiseringsmetod som hindrar PasswordHash-värdet från att synkronisera med Azure AD. Det finns också vissa operativa, tjänstspecifika data som krävs för normal Azure AD-drift som lagras i USA och som inte innehåller några personuppgifter.

## <a name="data-stored-outside-of-european-datacenters-for-european-customers"></a>Data som lagras utanför europeiska datacenter för europeiska kunder

Merparten av alla Azure AD-relaterade europeiska identitetsdata, för organisationer med europeiska adresser, lagras på europeiska datacenter. Exempel på Azure AD-data som inte lagras på europeiska datacenter är:

- **Identitetsrelaterade attribut**

    Följande identitetsrelaterade attribut replikeras till USA:

    - GivenName
    - Efternamn
    - userPrincipalName
    - Domain
    - PasswordHash
    - SourceAnchor
    - AccountEnabled
    - PasswordPolicies
    - StrongAuthenticationRequirement
    - ApplicationPassword
    - PUID

- **Microsoft Azure Multi-Factor authentication (MFA) och lösenordsåterställning via självbetjäning (SSPR) i Azure AD**
    
    MFA lagrar alla användardata i vila på europeiska datacenter. Men vissa MFA-tjänstspecifika data lagras i USA, inklusive:
    
    - Tvåfaktorautentisering och relaterade personliga data kan lagras i USA om du använder MFA eller SSPR.
        - All tvåfaktorsautentisering via telefonsamtal eller SMS kan genomföras av amerikanska operatörer.
        - Push-meddelanden med Microsoft Authenticator-appen kräver meddelanden från tillverkarens meddelandetjänst (Apple eller Google), som kan finnas utanför Europa.
        - OATH-koder verifieras alltid i USA. 
    - Vissa MFA- och SSPR-loggar lagras i USA i 30 dagar, oavsett autentiseringstyp.

- **Microsoft Azure Active Directory B2C (Azure AD B2C)**

    Azure AD B2C lagrar alla användardata i vila på europeiska datacenter. Arbetsloggar (där personliga data har tagits bort) lagras dock på den plats som personen kommer åt tjänsterna från. Om en B2C-användare exempelvis kommer åt tjänsten i USA, så lagras arbetsloggarna i USA. Dessutom lagras all information om principkonfigurationer som inte innehåller personliga data endast i USA. Mer information om principkonfigurationer finns i artikeln om [inbyggda principer i Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies).

- **Microsoft Azure Active Directory B2B (Azure AD B2B)** 
    
    Azure AD B2B lagrar alla användardata i vila på europeiska datacenter. Dock lagrar B2B icke-personliga metadata i tabeller på datacenter i USA. Den här tabellen innehåller fält som redeemUrl, invitationTicket, resource tenant Id, InviteRedirectUrl och InviterAppId.

- **Microsoft Azure Active Directory Domain Services (Azure AD DS)**

    Azure AD DS lagrar användardata på samma plats som kundens valda Azure Virtual Network. Om nätverket finns utanför Europa replikeras och lagras alltså dessa data utanför Europa.

- **Tjänster och appar som integrerar med Azure AD**

    Tjänster och appar som integrerar med Azure AD har åtkomst till identitetsdata. Utvärdera varje tjänst och app för att se hur identitetsdata bearbetas av den specifika tjänsten och appen och avgör om de uppfyller ditt företags datalagringskrav.

    Läs mer om Microsoft-tjänsternas datahemvist i avsnittet [Var finns dina data?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) på Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg
Mer information om funktionerna som beskrivs ovan finns i artiklarna nedan.
- [Kom igång med Azure Active Directory](get-started-azure-ad.md)
- [Vad är Multi-Factor Authentication?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
- [Lösenordsåterställning via självbetjäning i Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)
- [Vad är Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Vad är Azure AD B2B-samarbete?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
