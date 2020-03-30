---
title: Standardanvändarbehörigheter - Azure Active Directory | Microsoft-dokument
description: Lär dig mer om de olika användarbehörigheter som är tillgängliga i Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: ajburnle
ms.reviewer: vincesm
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 227230f2d6f46fae27e2cec69d99390f5054c7db
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366247"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Vilka är standardanvändarbehörigheterna i Azure Active Directory?
Alla användare beviljas en uppsättning standardbehörigheter i Azure Active Directory (AD Azure). En användares åtkomst består av typen av användare, deras [rolltilldelningar](active-directory-users-assign-role-azure-portal.md)och deras ägarskap för enskilda objekt. Den här artikeln beskriver dessa standardbehörigheter och innehåller en jämförelse av standardinställningar för medlem och gäst. Standardanvändarbehörigheterna kan bara ändras i användarinställningar i Azure AD.

## <a name="member-and-guest-users"></a>Medlems- och gästanvändare
Vilken uppsättning standardbehörigheter som tas emot beror på om användaren är en inbyggd medlem i klienten (medlemsanvändaren) eller om användaren överförs från en annan katalog som en B2B-samarbetsgäst (gästanvändare). Se [Vad är Azure AD B2B-samarbete?](../b2b/what-is-b2b.md)
* Medlemsanvändare kan registrera program, hantera sina egna profilbilder och mobila telefonnummer, ändra sina egna lösenord och bjuda in B2B gäster. Dessutom kan användare läsa all kataloginformation (med några få undantag). 
* Gästanvändare har begränsade katalogbehörigheter. Till exempel kan gästanvändare kan inte bläddra i information från klienten utanför sin egen profilinformation. Däremot kan en gästanvändare hämta information om en annan användare genom att ange användarens huvudnamn eller objekt-ID. En gästanvändare kan läsa egenskaper för grupper som de tillhör, inklusive gruppmedlemskap, oavsett **behörigheterna för gästanvändare är begränsad** inställning. En gäst kan inte visa information om andra klientobjekt.

Standardbehörigheter för gäster är begränsade som standard. Gäster kan läggas till i administratörsroller som beviljar dem fullständig läs- och skrivbehörighet i rollen. Det finns en ytterligare begränsning, möjligheten för gäster att bjuda in andra gäster. Om du ställer in **Gäster kan bjuda in** på **Nej** kan gäster inte bjuda in andra gäster. Se [Delegera inbjudningar till B2B-samarbete](../b2b/delegate-invitations.md) för att lära dig hur. Om du vill ge gästanvändare samma behörigheter som medlemsanvändare som standard anger du **Gästanvändarnas behörigheter begränsas** till **Nej**. Den här inställningen ger alla behörigheter för medlemsanvändare till gästanvändare som standard och gör att gäster kan läggas till i administrativa roller.

## <a name="compare-member-and-guest-default-permissions"></a>Jämför standardbehörigheter för medlemmar och gästanvändare

**Område** | **Användarbehörigheter för medlem** | **Användarbehörigheter för gäst**
------------ | --------- | ----------
Användare och kontakter | Läsa alla offentliga egenskaper om användare och kontakter<br>Bjuda in gäster<br>Ändra eget lösenord<br>Hantera eget mobilnummer<br>Hantera eget foto<br>Ogiltigförklara egna uppdateringstokens | Läsa egna egenskaper<br>Läs visningsnamn, e-post, inloggningsnamn, foto, användarnamn och användartypsegenskaper för andra användare och kontakter<br>Ändra eget lösenord
Grupper | Skapa säkerhetsgrupper<br>Skapa Office 365-grupper<br>Läsa alla icke-dolda egenskaper i grupper<br>Läsa icke-dolda gruppmeddelanden<br>Läsa dolda Office 365-gruppmedlemskap för domänansluten grupp<br>Hantera egenskaper, ägarskap och medlemskap i grupper som användaren äger<br>Lägga till gäster i egna grupper<br>Hantera inställningar för dynamiskt medlemskap<br>Ta bort egna grupper<br>Återställa grupper som ägs av Office 365 | Läsa alla icke-dolda egenskaper i grupper<br>Läsa icke-dolda gruppmeddelanden<br>Läsa dolda Office 365-gruppmedlemskap för domänansluten grupp<br>Hantera egna grupper<br>Lägga till gäster i egna grupper (om detta är tillåtet)<br>Ta bort egna grupper<br>Återställa grupper som ägs av Office 365<br>Läs egenskaper för grupper som de tillhör, inklusive medlemskap.
Program | Registrera (Skapa) ett nytt program<br>Skrivskyddade egenskaper för registrerade program och företagsprogram<br>Hantera egenskaper för program, tilldelningar och autentiseringsuppgifter för egna program<br>Skapa eller ta bort programlösenord för användare<br>Ta bort egna program<br>Återställ egna program | Skrivskyddade egenskaper för registrerade program och företagsprogram<br>Hantera egenskaper för program, tilldelningar och autentiseringsuppgifter för egna program<br>Ta bort egna program<br>Återställ egna program
Enheter | Läs alla enhetsegenskaper<br>Läs alla egenskaper för egna enheter<br> | Inga behörigheter<br>Ta bort egna enheter<br>
Katalog | Läs all företagsinformation<br>Läsa alla domäner<br>Läsa alla partnerkontrakt | Läsa visningsnamn och verifierade domäner
Roller och omfattningar | Läsa alla administrativa roller och medlemskap<br>Läsa alla egenskaper och medlemskap i administrativa enheter | Inga behörigheter 
Prenumerationer | Läsa alla prenumerationer<br>Aktivera tjänsteplanmedlem | Inga behörigheter
Principer | Läs alla principegenskaper<br>Hantera alla egenskaper för egna principer | Inga behörigheter

## <a name="to-restrict-the-default-permissions-for-member-users"></a>Så här begränsar du standardbehörigheter för medlemsanvändare

Standardbehörigheter för medlemmsanvändare kan begränsas på följande sätt.

Behörighet | Förklaring av inställning
---------- | ------------
Användare kan registrera program | Om du anger det här alternativet till Nej hindras användare från att skapa programregistreringar. Möjligheten kan sedan beviljas tillbaka till specifika personer genom att lägga till dem i rollen Programutvecklare.
Tillåt användare att ansluta arbets- eller skolkonto med LinkedIn | Om du ställer in det här alternativet på Nej hindras användare från att ansluta sitt arbets- eller skolkonto till sitt LinkedIn-konto. Mer information finns i [LinkedIn-kontoanslutningar datadelning och samtycke](https://docs.microsoft.com/azure/active-directory/users-groups-roles/linkedin-user-consent).
Möjlighet att skapa säkerhetsgrupper | När den här inställningen är inställd på Nej kan användare inte skapa säkerhetsgrupper. Globala administratörer och användaradministratörer kan fortfarande skapa säkerhetsgrupper. Se [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](../users-groups-roles/groups-settings-cmdlets.md) för att lära dig hur du ska gå tillväga.
Får skapa Office 365-grupper | Om det här alternativet är Nej får användare inte skapa Office 365-grupper. Om det här alternativet är Vissa får en uppsättning användare skapa Office 365-grupper. Globala administratörer och användaradministratörer kan fortfarande skapa Office 365-grupper. Se [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](../users-groups-roles/groups-settings-cmdlets.md) för att lära dig hur du ska gå tillväga.
Begränsa åtkomst till Azure AD-administrationsportalen | Om du anger det här alternativet till Nej kan icke-administratörer använda Azure AD-administrationsportalen för att läsa och hantera Azure AD-resurser. Ja begränsar alla icke-administratörer från att komma åt alla Azure AD-data i administrationsportalen. Viktigt att notera: den här inställningen begränsar inte åtkomsten till Azure AD-data med PowerShell eller andra klienter som Visual Studio. När den är inställd på Ja, för att ge en viss icke-administratörsanvändare möjlighet att använda Azure AD-administrationsportalen tilldela alla administrativa roller, till exempel rollen Katalogläsare. Den här rollen gör det möjligt att läsa grundläggande kataloginformation, som medlemsanvändare har som standard (gäster och huvudnamn för tjänsten inte).
Möjlighet att läsa andra användare | Den här inställningen är endast tillgänglig i PowerShell. Om du ställer in den här flaggan på $false hindras alla icke-administratörer från att läsa användarinformation från katalogen. Den här flaggan förhindrar inte att du läser användarinformation i andra Microsoft-tjänster, till exempel Exchange Online. Den här inställningen är avsedd för särskilda omständigheter och det rekommenderas inte att ställa in den här flaggan på $false.

## <a name="object-ownership"></a>Objektägarskap

### <a name="application-registration-owner-permissions"></a>Ägarbehörigheter för programregistrering
När en användare registrerar ett program, läggs de automatiskt till som ägare till programmet. De kan hantera metadata för programmet som ägare, till exempel namn och behörigheter för appen. De kan också hantera klient-specifik konfiguration av program, till exempel SSO-konfiguration och användartilldelningar. En ägare kan också lägga till eller ta bort andra ägare. Till skillnad från globala administratörer kan ägare bara hantera program som de äger.

### <a name="enterprise-application-owner-permissions"></a>Ägarbehörigheter för företagsprogram
När en användare lägger till ett nytt företagsprogram läggs de automatiskt till som ägare. Som ägare kan de hantera den klientspecifika konfigurationen av programmet, till exempel SSO-konfigurationen, etableringen och användartilldelningarna. En ägare kan också lägga till eller ta bort andra ägare. Till skillnad från globala administratörer kan ägare bara hantera de program de äger.

### <a name="group-owner-permissions"></a>Gruppägares behörigheter
När en användare skapar en grupp, läggs de automatiskt som en ägare till gruppen. Som ägare kan de hantera egenskaper för gruppen, till exempel namnet, samt hantera gruppmedlemskap. En ägare kan också lägga till eller ta bort andra ägare. Till skillnad från globala administratörer och användaradministratörer kan ägare bara hantera grupper som de äger. Om du vill tilldela en gruppägare, se [Hantera ägare för en grupp](active-directory-accessmanagement-managing-group-owners.md).

### <a name="ownership-permissions"></a>Behörigheter för ägarskap
I följande tabeller beskrivs de specifika behörigheter som finns i Azure Active Directory-medlemsanvändare som har överägda objekt. Användaren har bara dessa behörigheter för objekt som de äger.

#### <a name="owned-application-registrations"></a>Ägda programregistreringar
Användare kan utföra följande åtgärder på ägda programregistreringar.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/applications/audience/update | Uppdatera egenskapen applications.audience i Azure Active Directory. |
| microsoft.directory/program/autentisering/uppdatering | Uppdatera egenskapen applications.authentication i Azure Active Directory. |
| microsoft.directory/applications/basic/update | Uppdatera grundläggande egenskaper för program i Azure Active Directory. |
| microsoft.directory/program/autentiseringsuppgifter/uppdatering | Uppdatera egenskapen applications.credentials i Azure Active Directory. |
| microsoft.directory/program/delete | Ta bort program i Azure Active Directory. |
| microsoft.directory/applications/owners/update | Uppdatera egenskapen applications.owners i Azure Active Directory. |
| microsoft.directory/program/behörigheter/uppdatering | Uppdatera egenskapen applications.permissions i Azure Active Directory. |
| microsoft.directory/applications/policies/update | Uppdatera egenskapen applications.policies i Azure Active Directory. |
| microsoft.directory/applications/restore | Återställa program i Azure Active Directory. |

#### <a name="owned-enterprise-applications"></a>Ägda företagsprogram
Användare kan utföra följande åtgärder på ägda företagsprogram. Ett företagsprogram består av tjänstens huvudnamn, en eller flera programprinciper och ibland ett programobjekt i samma klient som tjänstens huvudnamn.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Läs alla egenskaper (inklusive privilegierade egenskaper) på auditLogs i Azure Active Directory. |
| microsoft.directory/policies/basic/update | Uppdatera grundläggande egenskaper för principer i Azure Active Directory. |
| microsoft.directory/policies/delete | Ta bort principer i Azure Active Directory. |
| microsoft.directory/policies/owners/update | Egenskapen Update policies.owners i Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Uppdatera servicePrincipals.appRoleAssignedTo-egenskap i Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAstilldelningar/uppdatering | Uppdatera egenskapen users.appRoleAssignments i Azure Active Directory. |
| microsoft.directory/servicePrincipals/publik/uppdatering | Uppdatera egenskapen ServicePrincipals.audience i Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Uppdatera egenskapen ServicePrincipals.authentication i Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Uppdatera grundläggande egenskaper för servicePrincipals i Azure Active Directory. |
| microsoft.directory/servicePrincipals/autentiseringsuppgifter/uppdatering | Uppdatera egenskapen servicePrincipals.credentials i Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | Ta bort servicePrinciper i Azure Active Directory. |
| microsoft.directory/servicePrincipals/ägare/uppdatering | Uppdatera servicePrincipals.owners-egenskapen i Azure Active Directory. |
| microsoft.directory/servicePrincipals/behörigheter/uppdatering | Uppdatera egenskapen servicePrincipals.permissions i Azure Active Directory. |
| microsoft.directory/servicePrincipals/principer/uppdatering | Uppdatera egenskapen servicePrincipals.policies i Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read microsoft.directory/signInReports/allProperties/read microsoft.directory/signInReports/allProperties/read microsoft. | Läs alla egenskaper (inklusive privilegierade egenskaper) vid inloggningar i Azure Active Directory. |

#### <a name="owned-devices"></a>Ägda enheter
Användare kan utföra följande åtgärder på ägda enheter.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read microsoft.directory/devices/bitLockerRecoveryKeys/read microsoft.directory/devices/bitLockerRecoveryKeys/read microsoft. | Läs egenskapen devices.bitLockerRecoveryKeys i Azure Active Directory. |
| microsoft.directory/devices/disable | Inaktivera enheter i Azure Active Directory. |

#### <a name="owned-groups"></a>Ägda grupper
Användare kan utföra följande åtgärder på ägda grupper.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/groups/appRoleAssignments/update microsoft.directory/groups/appRoleAssignments/update microsoft.directory/groups/appRoleAssignments/update microsoft. | Uppdatera egenskapen groups.appRoleAssignments i Azure Active Directory. |
| microsoft.directory/groups/basic/update | Uppdatera grundläggande egenskaper för grupper i Azure Active Directory. |
| microsoft.directory/groups/delete | Ta bort grupper i Azure Active Directory. |
| microsoft.directory/groups/dynamicMembershipRule/update microsoft.directory/groups/dynamicMembershipRule/update microsoft.directory/groups/dynamicMembershipRule/update microsoft. | Uppdatera groups.dynamicMembershipRule-egenskapen i Azure Active Directory. |
| microsoft.directory/groups/members/update | Egenskapen Update groups.members i Azure Active Directory. |
| microsoft.directory/groups/owners/update | Egenskapen Update groups.owners i Azure Active Directory. |
| microsoft.directory/groups/restore | Återställ grupper i Azure Active Directory. |
| microsoft.directory/groups/settings/update | Egenskapen Update groups.settings i Azure Active Directory. |

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du tilldelar Azure AD-administratörsroller finns i [Tilldela en användare till administratörsroller i Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
* Mer information om hur resursåtkomsten hanteras i Microsoft Azure finns i [Förstå resursåtkomst i Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Mer information om hur Azure Active Directory relaterar till din Azure-prenumeration finns i [Hur Azure-prenumerationer är associerade med Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Hantera användare](add-users-azure-active-directory.md)
