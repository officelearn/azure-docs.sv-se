---
title: Identity-datalagring för Europeiska kunder – Azure Active Directory | Microsoft Docs
description: Läs mer om där Azure Active Directory lagrar identitetsrelaterade data för dess europeiska kunder.
services: active-directory
author: eross-msft
manager: mtillman
ms.author: lizross
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/17/2018
ms.custom: it-pro, seodec18
ms.openlocfilehash: 51658fe686e2826618b2044cd969e6b4d463b93f
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063205"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Identity-datalagring för Europeiska kunder i Azure Active Directory
Azure Active Directory (Azure AD) hjälper dig att hantera användaridentiteter och skapa intelligensdrivna åtkomstprinciper som hjälper dig att skydda din organisations resurser. Identitetsdata lagras på en plats som baseras på den adress som din organisationen uppgav när du registrerade dig för tjänsten. Till exempel när du registrerar dig för en prenumeration på Office 365 eller Azure. Specifik information om var dina identitetsdata lagras finns i avsnittet [Var finns dina data?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) på Microsoft Trust Center.

De flesta Azure AD-relaterade europeiska identitetsdata lagras på europeiska datacenter, men det finns fem användarrelaterade attribut som vanligtvis lagras på datacenter i USA. Dessa attribut är GivenName, Surname, userPrincipalName, Domain och PasswordHash. Attributet PasswordHash kan vara ett undantag och lagras inte alltid i USA om någon använder en lokal, federerad autentiseringsmetod som hindrar PasswordHash-värdet från att synkronisera med Azure AD. Det finns också vissa operativa, tjänstspecifika data som krävs för normal Azure AD-drift som lagras i USA och som inte innehåller några personuppgifter.

## <a name="data-stored-outside-of-european-datacenters-for-european-customers"></a>Data som lagras utanför europeiska datacenter för europeiska kunder

Merparten av alla Azure AD-relaterade europeiska identitetsdata, för organisationer med europeiska adresser, lagras på europeiska datacenter. Azure AD-data som lagras i Europeiska datacenter, och också replikeras till datacenter i USA, innehåller:

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

    Azure AD B2C lagrar alla användardata i vila på europeiska datacenter. Arbetsloggar (där personliga data har tagits bort) lagras dock på den plats som personen kommer åt tjänsterna från. Om en B2C-användare exempelvis kommer åt tjänsten i USA, så lagras arbetsloggarna i USA. Dessutom lagras all information om principkonfigurationer som inte innehåller personliga data endast i USA. Mer information om principkonfigurationer, finns det [Azure Active Directory B2C: Inbyggda principer](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) artikeln.

- **Microsoft Azure Active Directory B2B (Azure AD B2B)** 
    
    Azure AD B2B lagrar alla användardata i vila på europeiska datacenter. Dock lagrar B2B icke-personliga metadata i tabeller på datacenter i USA. Den här tabellen innehåller fält som redeemUrl, invitationTicket, resource tenant Id, InviteRedirectUrl och InviterAppId.

- **Microsoft Azure Active Directory Domain Services (Azure AD DS)**

    Azure AD DS lagrar användardata på samma plats som kundens valda Azure Virtual Network. Om nätverket finns utanför Europa replikeras och lagras alltså dessa data utanför Europa.

- **Tjänster och appar som integrerar med Azure AD**

    Tjänster och appar som integrerar med Azure AD har åtkomst till identitetsdata. Utvärdera varje tjänst och app för att se hur identitetsdata bearbetas av den specifika tjänsten och appen och avgör om de uppfyller ditt företags datalagringskrav.

    Läs mer om Microsoft-tjänsternas datahemvist i avsnittet [Var finns dina data?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) på Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg
Mer information om någon av funktionerna som beskrivs ovan, finns i följande artiklar:
- [Vad är Multi-Factor Authentication?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
- [Lösenordsåterställning via självbetjäning i Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)
- [Vad är Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Vad är Azure AD B2B-samarbete?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
