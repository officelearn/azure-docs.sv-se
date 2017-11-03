---
title: "Egenskaper för en Azure Active Directory B2B-samarbete användare | Microsoft Docs"
description: "Azure Active Directory B2B-samarbete användaregenskaper kan konfigureras"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/25/2017
ms.author: sasubram
ms.openlocfilehash: 6e49cb202ed03bf50fb9ca34d34924cda434829c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Egenskaper för en användare för Azure Active Directory B2B-samarbete

En Azure Active Directory (AD Azure) business-to-business (B2B) samarbete användare är en användare med UserType = gäst. Gästanvändaren normalt är från en organisation och har begränsad behörighet i katalogen bjuda in som standard.

En Azure AD B2B-samarbete användare kan vara i något av följande konto tillstånd beroende på bjuda in organisationens behov:

- Tillstånd 1 ”: Multihomed” i en extern instans av Azure AD och representeras som gästanvändare i bjuda in organisationen. I det här fallet loggar B2B-användare in med ett konto i Azure AD som hör till de inbjudna. Om partnerorganisationen inte använder Azure AD kan skapas fortfarande gästanvändaren i Azure AD. Kraven är att de lösa sina inbjudan och Azure AD verifierar sin e-postadress. Den här ordningen kallas även en just-in-time (JIT) innehavare eller ”viral” innehavare.

- Tillstånd 2 ”: Multihomed” i ett Microsoft-konto och representeras som gästanvändare i organisationen värden. I det här fallet loggar gästanvändaren in med ett Microsoft-konto. Inbjudna användarens sociala identitet (google.com eller liknande), som inte är ett Microsoft-konto skapas som ett Microsoft-konto under erbjudande åtgärd.

- Tillstånd 3: Homed i värden organisationens lokala Active Directory och synkroniserats med värden organisationens Azure AD. Under den här versionen, måste du använda PowerShell för att manuellt ändra UserType för dessa användare i molnet.

- Tillstånd 4: Homed i värden organisationens Azure AD med UserType = Gäst och autentiseringsuppgifter som värd-organisation hanterar.

  ![Visa den bjuder in initialer](media/active-directory-b2b-user-properties/redemption-diagram.png)


Nu ska vi se hur en användare med Azure AD B2B-samarbete i läge 1 som ser ut i Azure AD.

### <a name="before-invitation-redemption"></a>Innan du inbjudan inlösning

![Innan erbjudandet inlösning](media/active-directory-b2b-user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Efter inbjudan inlösning

![När erbjudandet inlösning](media/active-directory-b2b-user-properties/after-redemption.png)

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Nyckelegenskaperna för användaren för Azure AD B2B-samarbete
### <a name="usertype"></a>UserType
Den här egenskapen anger förhållandet för användare som värd-innehavare. Den här egenskapen kan ha två värden:
- Medlem: Det här värdet anger en anställd hos organisationen värd och en användare i organisationens löneuppgifter. Till exempel förväntas den här användaren har åtkomst till endast är interna platser. Den här användaren kan inte betraktas som en extern deltagare.

- Gäst: Det här värdet anger en användare som inte anses vara interna för företaget, till exempel en extern deltagare, partner, kund eller liknande användare. Användaren kan förväntas ta emot en CEO interna PM eller ta emot fördelar för företaget, till exempel.

  > [!NOTE]
  > UserType har ingen relation till hur användaren loggar in, directory rollen för användar- och så vidare. Den här egenskapen kan du bara anger användarens relation till värd-organisation och kan organisationen för att genomdriva principer som är beroende av den här egenskapen.

### <a name="source"></a>Källa
Den här egenskapen anger hur användaren loggar in.

- Inbjudna användare: Den här användaren har bjudits in men har ännu inte Inlöst en inbjudan.

- Externa Active Directory: Den här användaren homed i en extern organisation och autentiserar med hjälp av en Azure AD-kontot som hör till den andra organisationen. Den här typen av inloggning motsvarar tillstånd 1.

- Microsoft-konto: den här användaren homed i ett Microsoft-konto och autentiserar med hjälp av ett Microsoft-konto. Den här typen av inloggning motsvarar läge 2.

- Windows Server Active Directory: Den här användaren har loggat in från lokala Active Directory som tillhör den här organisationen. Den här typen av inloggning motsvarar tillstånd 3.

- Azure Active Directory: Autentiserar användaren med hjälp av en Azure AD-konto som tillhör den här organisationen. Den här typen av inloggning motsvarar tillstånd 4.
  > [!NOTE]
  > Käll- och UserType är oberoende egenskaper. Ett värde av datakälla innebär inte ett visst värde för UserType.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Kan Azure AD B2B-användare läggas till som medlemmar i stället för gäster?
En Azure AD B2B-användare och gästanvändare normalt synonym. Därför kan en Azure AD B2B-samarbete användare läggs till som en användare med UserType = Gäst som standard. I vissa fall är dock partnerorganisationen medlem i en större organisation som värd organisationen också tillhör. I så fall, kanske värden organisationen vill behandla användare i partnerorganisation som medlemmar i stället för gäster. Använda Azure AD B2B inbjudan Manager API: er för att lägga till eller be en användare från partnerorganisationen till värd-organisation som en medlem.

## <a name="filter-for-guest-users-in-the-directory"></a>Filtret för gästanvändare i katalogen

![Filtrera gästanvändare](media/active-directory-b2b-user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>Konvertera UserType
För närvarande är det möjligt för användare att konvertera UserType från medlem till gäst och vice versa med hjälp av PowerShell. Men egenskapen UserType ska representera användarens relation i organisationen. Därför bör värdet för den här egenskapen bara ändra om relationen för användare i organisationen ändras. Om relationen för användaren ändras bör problem, till exempel om användarens huvudnamn (UPN) ändras kan åtgärdas? Ska användaren fortfarande ha åtkomst till samma resurser? Ska tilldelas en postlåda? Därför rekommenderar vi inte ändra UserType med hjälp av PowerShell som en atomisk aktivitet. Dessutom, om den här egenskapen blir inte ändras med hjälp av PowerShell, rekommenderas inte tar ett beroende på det här värdet.

## <a name="remove-guest-user-limitations"></a>Ta bort gästen användaren begränsningar
Det kan finnas fall där du vill ge användarna gäst högre behörighet. Du kan lägga till en gästanvändaren i någon roll och även ta bort standardbegränsningar gästen användaren i katalogen för att ge en användare samma behörighet som medlemmar.

Det är möjligt att inaktivera standardbegränsningar gästen användaren så att gästanvändare i företagets katalog får samma behörigheter som en medlemsanvändare.

![Ta bort gästen användaren begränsningar](media/active-directory-b2b-user-properties/remove-guest-limitations.png)

## <a name="next-steps"></a>Nästa steg

Läs andra artiklar om Azure AD B2B-samarbete:

* [Vad är Azure AD B2B-samarbete?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Lägger till en B2B-samarbete användare till en roll](active-directory-b2b-add-guest-to-role.md)
* [Delegera B2B-samarbete inbjudningar](active-directory-b2b-delegate-invitations.md)
* [B2B-samarbete användaren granskning och rapportering](active-directory-b2b-auditing-and-reporting.md)
* [Dynamiska grupper och B2B-samarbete](active-directory-b2b-dynamic-groups.md)
* [B2B-samarbete kod och PowerShell-exempel](active-directory-b2b-code-samples.md)
* [Konfigurera SaaS-appar för B2B-samarbete](active-directory-b2b-configure-saas-apps.md)
* [Användartoken för B2B-samarbete](active-directory-b2b-user-token.md)
* [B2B-samarbete användaranspråk mappning](active-directory-b2b-claims-mapping.md)
* [Extern delning av Office 365](active-directory-b2b-o365-external-user.md)
* [Aktuella begränsningar för B2B-samarbete](active-directory-b2b-current-limitations.md)
