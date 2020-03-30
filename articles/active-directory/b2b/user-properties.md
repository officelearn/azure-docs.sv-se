---
title: Egenskaper för en B2B-gästanvändare – Azure Active Directory | Microsoft-dokument
description: Azure Active Directory B2B gästanvändaregenskaper och -tillstånd före och efter inlösen av inbjudan
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40f5002e361653614c966dc43301afa83eb7b200
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050799"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Egenskaper för en Azure Active Directory B2B-samarbetsanvändare

I den här artikeln beskrivs egenskaperna och tillstånden för B2B-gästanvändarobjektet i Azure Active Directory (Azure AD) före och efter inlösen av inbjudan. En Azure AD business-to-business (B2B) samarbetsanvändare är en användare med UserType = Gäst. Den här gästanvändaren kommer vanligtvis från en partnerorganisation och har begränsade privilegier i den inbjudande katalogen som standard.

Beroende på den inbjudande organisationens behov kan en Azure AD B2B-samarbetsanvändare vara i något av följande kontotillstånd:

- Tillstånd 1: Homed i en extern instans av Azure AD och representeras som gästanvändare i den inbjudande organisationen. I det här fallet loggar B2B-användaren in med hjälp av ett Azure AD-konto som tillhör den inbjudna klienten. Om partnerorganisationen inte använder Azure AD skapas fortfarande gästanvändaren i Azure AD. Kraven är att de löser in sin inbjudan och Azure AD verifierar sin e-postadress. Detta arrangemang kallas också en just-in-time (JIT) hyresrätt eller en "viral" hyresrätt.

   > [!IMPORTANT]
   > **Från och med den 31 mars 2021**stöder Microsoft inte längre inlösen av inbjudningar genom att skapa ohanterade Azure AD-konton och klienter för B2B-samarbetsscenarier. Som förberedelse uppmuntrar vi kunder att välja [e-post engångskodautentisering.](one-time-passcode.md) Vi välkomnar din feedback på den här offentliga förhandsversionen och är glada över att skapa ännu fler sätt att samarbeta.

- Tillstånd 2: Hemtrevs i ett Microsoft-konto eller annat konto och representeras som gästanvändare i värdorganisationen. I det här fallet loggar gästanvändaren in med ett Microsoft-konto eller ett socialt konto (google.com eller liknande). Den inbjudna användarens identitet skapas som ett Microsoft-konto i den inbjudande organisationens katalog under inlösen av erbjudandet.

- Tillstånd 3: Homed i värdorganisationens lokala Active Directory och synkroniseras med värdorganisationens Azure AD. Du kan använda Azure AD Connect för att synkronisera partnerkontona till molnet som Azure AD B2B-användare med UserType = Gäst. Se [Bevilja lokalt hanterade partnerkonton åtkomst till molnresurser](hybrid-on-premises-to-cloud.md).

- Tillstånd 4: Homed i värdorganisationens Azure AD med UserType = Gäst och autentiseringsuppgifter som värdorganisationen hanterar.

  ![Diagram som visar de fyra användartillstånden](media/user-properties/redemption-diagram.png)


Nu ska vi se hur en Azure AD B2B-samarbetsanvändare ser ut i Azure AD.

### <a name="before-invitation-redemption"></a>Före inlösen av inbjudan

Tillstånd 1- och tillstånd 2-konton är resultatet av att bjuda in gästanvändare att samarbeta med hjälp av gästanvändarnas egna autentiseringsuppgifter. När inbjudan skickas till gästanvändaren skapas ett konto i katalogen. Det här kontot har inga associerade autentiseringsuppgifter eftersom autentisering utförs av gästanvändarens identitetsleverantör. Egenskapen **Source** för gästanvändarkontot i katalogen är inställd **på Inbjuden användare**. 

![Skärmbild som visar användaregenskaper före inlösen av erbjudandet](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Efter inlösen av inbjudan

När gästanvändaren har accepterat inbjudan uppdateras egenskapen **Source** baserat på gästanvändarens identitetsleverantör.

För gästanvändare i tillstånd 1 är **källan** **extern Azure Active Directory**.

![Tillstånd 1 gästanvändare efter inlösen av erbjudandet](media/user-properties/after-redemption-state1.png)

För gästanvändare i tillstånd 2 är **källan** **Microsoft-konto**.

![Tillstånd 2 gästanvändare efter inlösen av erbjudandet](media/user-properties/after-redemption-state2.png)

För gästanvändare i tillstånd 3 och tillstånd 4 är egenskapen **Source** inställd på **Azure Active Directory** eller Windows Server Active **Directory**enligt beskrivningen i nästa avsnitt.

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Viktiga egenskaper för Azure AD B2B-samarbetsanvändaren
### <a name="usertype"></a>UserType
Den här egenskapen anger användarens relation till värdrearen. Den här egenskapen kan ha två värden:
- Medlem: Det här värdet anger en medarbetare i värdorganisationen och en användare i organisationens lönelista. Den här användaren förväntar sig till exempel att ha åtkomst till interna webbplatser. Den här användaren betraktas inte som en extern medarbetare.

- Gäst: Det här värdet anger en användare som inte anses vara intern för företaget, till exempel en extern medarbetare, partner eller kund. En sådan användare förväntas inte få en VD:s interna pm eller få företagsförmåner, till exempel.

  > [!NOTE]
  > UserType har ingen relation till hur användaren loggar in, användarens katalogroll och så vidare. Den här egenskapen anger helt enkelt användarens relation till värdorganisationen och tillåter organisationen att tillämpa principer som är beroende av den här egenskapen.

### <a name="source"></a>Källa
Den här egenskapen anger hur användaren loggar in.

- Inbjuden användare: Den här användaren har bjudits in men har ännu inte löst in en inbjudan.

- Extern Azure Active Directory: Den här användaren är homed i en extern organisation och autentiserar med hjälp av ett Azure AD-konto som tillhör den andra organisationen. Denna typ av inloggning motsvarar stat 1.

- Microsoft-konto: Den här användaren är homed i ett Microsoft-konto och autentiserar med hjälp av ett Microsoft-konto. Denna typ av inloggning motsvarar stat 2.

- Active Directory för Windows Server: Den här användaren är inloggad från lokala Active Directory som tillhör den här organisationen. Denna typ av inloggning motsvarar stat 3.

- Azure Active Directory: Den här användaren autentiserar med hjälp av ett Azure AD-konto som tillhör den här organisationen. Denna typ av inloggning motsvarar stat 4.
  > [!NOTE]
  > Source och UserType är oberoende egenskaper. Ett värde för Källan innebär inte något visst värde för UserType.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Kan Azure AD B2B-användare läggas till som medlemmar i stället för gäster?
Vanligtvis är en Azure AD B2B-användare och gästanvändare synonymt. Därför läggs en Azure AD B2B-samarbetsanvändare till som användare med UserType = Gäst som standard. I vissa fall är dock partnerorganisationen medlem i en större organisation som värdorganisationen också tillhör. I så fall kanske värdorganisationen vill behandla användare i partnerorganisationen som medlemmar i stället för gäster. Använd API:erna för Azure AD B2B Invitation Manager för att lägga till eller bjuda in en användare från partnerorganisationen till värdorganisationen som medlem.

## <a name="filter-for-guest-users-in-the-directory"></a>Filtrera för gästanvändare i katalogen

![Skärmbild som visar filtret för gästanvändare](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>Konvertera UserType
Det är möjligt att konvertera UserType från medlem till gäst och vice versa med hjälp av PowerShell. Egenskapen UserType representerar dock användarens relation till organisationen. Därför bör du bara ändra den här egenskapen om användarens relation till organisationen ändras. Om relationen för användaren ändras, ska användarens huvudnamn (UPN) ändras? Ska användaren fortsätta att ha åtkomst till samma resurser? Ska en postlåda tilldelas? Vi rekommenderar inte att du ändrar UserType genom att använda PowerShell som en atomaktivitet. Om den här egenskapen blir oföränderlig med PowerShell rekommenderar vi inte heller att du tar ett beroende av det här värdet.

## <a name="remove-guest-user-limitations"></a>Ta bort begränsningar för gästanvändare
Det kan finnas fall där du vill ge gästanvändarna högre behörighet. Du kan lägga till en gästanvändare i valfri roll och till och med ta bort standardanvändarbegränsningarna för gästanvändare i katalogen för att ge en användare samma behörighet som medlemmar.

Det är möjligt att stänga av standardbegränsningarna så att en gästanvändare i företagskatalogen har samma behörigheter som medlemsanvändare.

![Skärmbild som visar alternativet Externa användare i användarinställningarna](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Kan jag göra gästanvändare synliga i Exchange Global Address List?
Ja. Som standard visas inte gästobjekt i organisationens globala adresslista, men du kan använda Azure Active Directory PowerShell för att göra dem synliga. Mer information finns **i Kan jag göra gästobjekt synliga i den globala adresslistan?** [Manage guest access in Office 365 Groups](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups?redirectSourcePath=%252fen-us%252farticle%252fmanage-guest-access-in-office-365-groups-9de497a9-2f5c-43d6-ae18-767f2e6fe6e0&view=o365-worldwide#add-guests-to-the-global-address-list) 

## <a name="next-steps"></a>Nästa steg

* [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
* [Användartoken för B2B-samarbete](user-token.md)
* [B2B-mappning av användaranspråk för samarbete](claims-mapping.md)
