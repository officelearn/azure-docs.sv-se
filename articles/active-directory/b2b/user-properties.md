---
title: Egenskaper för en användare för Azure Active Directory B2B-samarbete | Microsoft Docs
description: Azure Active Directory B2B-samarbete användaregenskaper kan konfigureras
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/25/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 0cfd7888acf942e4af875c37c2472ff086f9119b
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059643"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Egenskaper för en användare för Azure Active Directory B2B-samarbete

En Azure Active Directory (Azure AD) business-to-business (B2B) samarbete användare är en användare med UserType = gäst. Den här gästanvändare vanligtvis från en partnerorganisation och har begränsad behörighet i katalogen bjuder in som standard.

Beroende på behov i organisationen som bjuder in kan en Azure AD B2B-användare vara i något av följande konto tillstånd:

- Tillstånd 1: Homed i en extern instans av Azure AD och representeras som en gästanvändare i organisationen som bjuder in. I det här fallet loggar B2B-användaren in med ett Azure AD-konto som tillhör den inbjudna klienten. Om partnerorganisationen inte använder Azure AD, skapas fortfarande gästanvändare i Azure AD. Kraven är att de lösa in sin inbjudan och Azure AD verifierar sin e-postadress. Den här ordningen kallas även en innehavare för just-in-time-(åtkomst JIT) eller en ”viral” innehavare.

- Tillstånd 2: Homed i ett Microsoft-konto och representeras som en gästanvändare i organisationen värden. I det här fallet loggar gästanvändare in med ett Microsoft-konto. Inbjudna användarens sociala identitet (google.com eller liknande), som inte är ett Microsoft-konto skapas som ett Microsoft-konto under erbjudandet inlösen.

- Tillstånd 3: Homed i värden organisationens lokala Active Directory och synkroniseras med värden organisationens Azure AD. Under den här versionen måste du manuellt ändra UserType för dessa användare i molnet med hjälp av PowerShell.

- Tillstånd 4: Homed i värden organisations Azure AD med UserType = Gäst och autentiseringsuppgifter som värd-organisation hanterar.

  ![Visa den inbjudaren initialer](media/user-properties/redemption-diagram.png)


Nu ska vi se hur en användare med Azure AD B2B-samarbete i läge 1 som ser ut i Azure AD.

### <a name="before-invitation-redemption"></a>Innan du inlösning av inbjudan

![Före erbjudandet inlösen](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Efter inlösning av inbjudan

![När du har erbjudandet inlösen](media/user-properties/after-redemption.png)

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Viktiga egenskaper för användaren för Azure AD B2B-samarbete
### <a name="usertype"></a>UserType
Den här egenskapen anger förhållandet för användaren som ska värd-innehavare. Den här egenskapen kan ha två värden:
- Medlem: Det här värdet anger en medarbetare i organisationen för värden och en användare i organisationens löneuppgifter. Den här användaren till exempel förväntar sig att ha åtkomst till interna webbplatser. Den här användaren ska inte betraktas som en externa medarbetaren.

- Gäst: Det här värdet anger en användare som inte anses vara interna för företag, till exempel en externa medarbetare, partner, kund eller liknande användare. En sådan användare förväntas inte ta emot en VD interna PM eller ta emot företagets fördelar, till exempel.

  > [!NOTE]
  > UserType har ingen relation till hur användaren loggar in, katalogrollen för användare och så vidare. Den här egenskapen kan du bara anger användarens relation till värd-organisation och kan organisationen för att genomdriva principer som är beroende av den här egenskapen.

### <a name="source"></a>Källa
Den här egenskapen anger hur användaren loggar in.

- Inbjuden användare: Den här användaren har bjudits men ännu har in inte inbjudan.

- Externa Active Directory: Den här användaren är homed i en extern organisation och autentiserar med hjälp av en Azure AD-konto som hör till den andra organisationen. Den här typen av inloggning motsvarar läge 1.

- Microsoft-konto: den här användaren är homed i ett Microsoft-konto och autentiserar med hjälp av ett Microsoft-konto. Den här typen av inloggning motsvarar läge 2.

- Windows Server Active Directory: Den här användaren är inloggad från en lokal Active Directory som hör till den här organisationen. Den här typen av inloggning motsvarar tillstånd 3.

- Azure Active Directory: Den här användaren autentiseras med hjälp av en Azure AD-konto som tillhör den här organisationen. Den här typen av inloggning motsvarar tillstånd 4.
  > [!NOTE]
  > Käll- och UserType är oberoende egenskaper. Ett värde för källan innebär inte ett visst värde för UserType.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Kan Azure AD B2B-användare läggas till som medlemmar i stället för gäster?
Vanligtvis är en Azure AD B2B-användare och gästanvändare synonyma. Därför kan en Azure AD B2B-samarbete användare läggs till som en användare med UserType = Gäst som standard. Men i vissa fall kan ingår partnerorganisationen i en större organisation som värd organisationen också tillhör. I så, fall kanske värd organisationen vill behandla användare i partnerorganisationen som medlemmar i stället för gäster. Använd API: er för Azure AD B2B inbjudan Manager för att lägga till eller bjuda in användare från partnerorganisationen till värd-organisation som medlem.

## <a name="filter-for-guest-users-in-the-directory"></a>Filter för gästanvändare i katalogen

![Filtrera gästanvändare](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>Konvertera UserType
För närvarande är det möjligt för användare att konvertera UserType från medlem till gäst och vice versa med hjälp av PowerShell. Men egenskapen UserType ska representerar användarens relation i organisationen. Därför bör värdet för den här egenskapen bara ändra om ändringar av relationen för användaren i organisationen. Om relationen mellan användaren ändras bör problem, till exempel om användarens huvudnamn (UPN) ändras, åtgärdas? Ska du fortsätta att få åtkomst till samma resurser? Tilldelas en postlåda? Därför rekommenderar vi inte ändra UserType med hjälp av PowerShell som en atomisk aktivitet. Dessutom, om den här egenskapen blir inte kan ändras med hjälp av PowerShell, rekommenderar vi inte tar ett beroende på det här värdet.

## <a name="remove-guest-user-limitations"></a>Ta bort begränsningar för gäst-användare
Det kan finnas fall där du vill ge dina gästanvändare högre privilegier. Du kan lägga till en gästanvändare till valfri roll och även ta bort standardbegränsningar för gäst-användare i katalogen för att ge en användare samma behörighet som medlemmar.

Det är möjligt att inaktivera standardbegränsningar för gäst-användare så att en gästanvändare i företagets katalog får samma behörigheter som en medlemsanvändare.

![Ta bort begränsningar för gäst-användare](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Kan jag visa gästanvändare i den globala adresslistan Exchange?
Ja. Gästen objekt visas inte i din organisations globala adresslistan som standard, men du kan använda Azure Active Directory PowerShell för att göra dem synliga. Mer information finns i **kan jag göra gäst objekt visas i den globala adresslistan?** i [gäståtkomst i Office 365-grupper](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6#PickTab=FAQ). 

## <a name="next-steps"></a>Nästa steg

* [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
* [Användartoken för B2B-samarbete](user-token.md)
* [B2B-samarbete mappning av användaranspråk](claims-mapping.md)
