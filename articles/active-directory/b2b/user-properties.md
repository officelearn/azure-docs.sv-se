---
title: Egenskaperna för en B2B gästen användare – Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B-gäst användaregenskaper och tillstånd före och efter inlösning av inbjudan
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d4466e4ac7a4e818da6332254e3094eccbaf2b4
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59257611"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Egenskaper för en användare för Azure Active Directory B2B-samarbete

Den här artikeln beskriver egenskaper och tillstånd för användarobjektet i Azure Active Directory (Azure AD) B2B gäst före och efter inlösning av inbjudan. Azure AD business-to-business (B2B) samarbete-användare är en användare med UserType = gäst. Den här gästanvändare vanligtvis från en partnerorganisation och har begränsad behörighet i katalogen bjuder in som standard.

Beroende på behov i organisationen som bjuder in kan en Azure AD B2B-användare vara i något av följande konto tillstånd:

- Tillstånd 1: Homed i en extern instans av Azure AD och representeras som en gästanvändare i organisationen som bjuder in. I det här fallet loggar B2B-användaren in med ett Azure AD-konto som tillhör den inbjudna klienten. Om partnerorganisationen inte använder Azure AD, skapas fortfarande gästanvändare i Azure AD. Kraven är att de lösa in sin inbjudan och Azure AD verifierar sin e-postadress. Den här ordningen kallas även en innehavare för just-in-time-(åtkomst JIT) eller en ”viral” innehavare.

- Tillstånd 2: Homed i ett Microsoft- eller ett annat konto, vilket representeras som gästanvändare i organisationen värden. I det här fallet gästanvändare loggar in med ett Microsoft-konto eller ett socialt konto (google.com eller liknande). Inbjudna användarens identitet skapas som ett Microsoft-konto i den organisationen som bjuder in directory under erbjudandet inlösen.

- Tillstånd 3: Homed i värden organisationens lokala Active Directory och synkroniseras med värden organisationens Azure AD. Du kan använda Azure AD Connect för att synkronisera partnerkonton till molnet som Azure AD B2B-användare med UserType = gäst. Se [bevilja hanteras lokalt partneråtkomst till molnresurser](hybrid-on-premises-to-cloud.md).

- Tillstånd 4: I värden organisationens Azure AD med UserType = Gäst och autentiseringsuppgifter som värd-organisation hanterar.

  ![Diagram som illustrerar fyra användarstatus](media/user-properties/redemption-diagram.png)


Nu ska vi se vad en Azure AD B2B-användare ser ut i Azure AD.

### <a name="before-invitation-redemption"></a>Innan du inlösning av inbjudan

Tillstånd 1 och tillstånd 2-konton är resultatet av att bjuda in gästanvändare för att samarbeta med hjälp av gäst användarnas egna autentiseringsuppgifter. När inbjudan skickas först till gästanvändaren, skapas ett konto i din katalog. Det här kontot har inte några autentiseringsuppgifter som är associerade med den eftersom autentiseringen utförs med gästanvändarens identitetsprovider. Den **källa** egenskapen för gästanvändarkontot i din katalog är inställd **Invited användaren**. 

![Skärmbild som visar användaregenskaper före erbjudandet inlösen](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Efter inlösning av inbjudan

När gästanvändaren har accepterat inbjudan, den **källa** egenskapen uppdateras baserat på gästanvändarens identitetsprovider.

För gästanvändare i tillstånd 1, den **källa** är **externa Azure Active Directory**.

![Tillstånd 1-gästanvändare när erbjudandet inlösen](media/user-properties/after-redemption-state1.png)

För gästanvändare i läge 2, den **källa** är **Account**.

![Tillstånd 2-gästanvändare när erbjudandet inlösen](media/user-properties/after-redemption-state2.png)

För gästanvändare i tillståndet 3 och 4 tillstånd, den **källa** är inställd på **Azure Active Directory** eller **Windows Server Active Directory**, enligt beskrivningen i nästa avsnitt.

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Viktiga egenskaper för användaren för Azure AD B2B-samarbete
### <a name="usertype"></a>UserType
Den här egenskapen anger förhållandet för användaren som ska värd-innehavare. Den här egenskapen kan ha två värden:
- Medlem: Det här värdet anger en medarbetare i organisationen för värden och en användare i organisationens löneuppgifter. Den här användaren till exempel förväntar sig att ha åtkomst till interna webbplatser. Den här användaren anses inte vara en externa medarbetaren.

- Guest: Det här värdet anger en användare som inte anses vara interna för företag, till exempel en externa medarbetare, partner eller kund. Användaren är inte förväntas ta emot en VD interna PM eller ta emot företagets fördelar, till exempel.

  > [!NOTE]
  > UserType har ingen relation till hur användaren loggar in, katalogrollen för användare och så vidare. Den här egenskapen kan du bara anger användarens relation till värd-organisation och kan organisationen för att genomdriva principer som är beroende av den här egenskapen.

### <a name="source"></a>Källa
Den här egenskapen anger hur användaren loggar in.

- Inbjudna användare: Den här användaren har bjudits men ännu har in inte inbjudan.

- Externa Active Directory: Den här användaren är homed i en extern organisation och autentiserar med hjälp av en Azure AD-konto som hör till den andra organisationen. Den här typen av inloggning motsvarar läge 1.

- Microsoft-konto: Den här användaren är homed i ett Microsoft-konto och autentiserar med hjälp av ett Microsoft-konto. Den här typen av inloggning motsvarar läge 2.

- Windows Server Active Directory: Den här användaren är inloggad från en lokal Active Directory som hör till den här organisationen. Den här typen av inloggning motsvarar tillstånd 3.

- Azure Active Directory: Den här användaren autentiseras med hjälp av en Azure AD-konto som tillhör den här organisationen. Den här typen av inloggning motsvarar tillstånd 4.
  > [!NOTE]
  > Käll- och UserType är oberoende egenskaper. Ett värde för källan innebär inte ett visst värde för UserType.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Kan Azure AD B2B-användare läggas till som medlemmar i stället för gäster?
Vanligtvis är en Azure AD B2B-användare och gästanvändare synonyma. Därför kan en Azure AD B2B-samarbete användare läggs till som en användare med UserType = Gäst som standard. Men i vissa fall kan ingår partnerorganisationen i en större organisation som värd organisationen också tillhör. I så, fall kanske värd organisationen vill behandla användare i partnerorganisationen som medlemmar i stället för gäster. Använd API: er för Azure AD B2B inbjudan Manager för att lägga till eller bjuda in användare från partnerorganisationen till värd-organisation som medlem.

## <a name="filter-for-guest-users-in-the-directory"></a>Filter för gästanvändare i katalogen

![Skärmbild som visar filtret för gästanvändare](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>Konvertera UserType
Det är möjligt att konvertera UserType från medlem till gäst och vice versa med hjälp av PowerShell. Men representerar egenskapen UserType användarens relation till organisationen. Därför bör du ändra den här egenskapen endast om relationen användarens organisation ändringar. Om relationen mellan användaren ändrar användarens huvudnamn (UPN) Ändra? Ska du fortsätta att få åtkomst till samma resurser? Tilldelas en postlåda? Vi rekommenderar inte ändra UserType med hjälp av PowerShell som en atomisk aktivitet. Även om den här egenskapen blir inte kan ändras med hjälp av PowerShell, rekommenderar vi inte tar ett beroende på det här värdet.

## <a name="remove-guest-user-limitations"></a>Ta bort begränsningar för gäst-användare
Det kan finnas fall där du vill ge dina gästanvändare högre privilegier. Du kan lägga till en gästanvändare till valfri roll och även ta bort standardbegränsningar för gäst-användare i katalogen för att ge en användare samma behörighet som medlemmar.

Det är möjligt att Stäng av standardbegränsningarna så att en gästanvändare i företagets katalog har samma behörigheter som en medlemsanvändare.

![Skärmbild som visar de externa användarna alternativet i användarinställningarna](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Kan jag visa gästanvändare i den globala adresslistan Exchange?
Ja. Gästen objekt visas inte i din organisations globala adresslistan som standard, men du kan använda Azure Active Directory PowerShell för att göra dem synliga. Mer information finns i **kan jag göra gäst objekt visas i den globala adresslistan?** i [hantera gäståtkomst i Office 365-grupper](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups?redirectSourcePath=%252fen-us%252farticle%252fmanage-guest-access-in-office-365-groups-9de497a9-2f5c-43d6-ae18-767f2e6fe6e0&view=o365-worldwide#faq). 

## <a name="next-steps"></a>Nästa steg

* [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
* [Användartoken för B2B-samarbete](user-token.md)
* [B2B-samarbete mappning av användaranspråk](claims-mapping.md)
