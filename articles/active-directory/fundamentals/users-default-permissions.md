---
title: Standard användarbehörigheter - Azure Active Directory | Microsoft Docs
description: Läs mer om de olika användarbehörigheterna som är tillgängliga i Azure Active Directory.
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
ms.openlocfilehash: 99ceb72930d756926bbc73508b20fbde8ea31324
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561609"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Vad är standardbehörigheterna för användare i Azure Active Directory?
Alla användare beviljas en uppsättning standardbehörigheter i Azure Active Directory (AD Azure). En användares åtkomst består av typen av användare, deras [roll tilldelningar](active-directory-users-assign-role-azure-portal.md)och deras ägarskap för enskilda objekt. Den här artikeln beskriver dessa standardbehörigheter och innehåller en jämförelse av standardinställningar för medlem och gäst. Standard användar behörigheter kan bara ändras i användar inställningar i Azure AD.

## <a name="member-and-guest-users"></a>Medlems- och gästanvändare
Uppsättningen standard behörigheter som tas emot beror på om användaren är en ursprunglig medlem i klienten (medlems användare) eller om användaren förs över från en annan katalog som ett B2B-samarbets gäst (gäst användare). Se [Vad är Azure AD B2B-samarbete?](../b2b/what-is-b2b.md) mer information om att lägga till gäst användare.
* Medlemsanvändare kan registrera program, hantera sina egna profilbilder och mobila telefonnummer, ändra sina egna lösenord och bjuda in B2B gäster. Dessutom kan användare läsa all kataloginformation (med några få undantag). 
* Gäst användare har begränsad katalog behörighet. Till exempel kan gästanvändare kan inte bläddra i information från klienten utanför sin egen profilinformation. Däremot kan en gästanvändare hämta information om en annan användare genom att ange användarens huvudnamn eller objekt-ID. En gäst användare kan läsa egenskaper för grupper som de tillhör, inklusive grupp medlemskap, oavsett vilka **gäst användares behörigheter är begränsade** . En gäst kan inte Visa information om andra klient objekt.

Standardbehörigheter för gäster är begränsade som standard. Gäster kan läggas till i administratörsroller som beviljar dem fullständig läs- och skrivbehörighet i rollen. Det finns en ytterligare begränsning, möjligheten för gäster att bjuda in andra gäster. Om du ställer in **Gäster kan bjuda in** på **Nej** kan gäster inte bjuda in andra gäster. Se [Delegera inbjudningar till B2B-samarbete](../b2b/delegate-invitations.md) för att lära dig hur. Om du vill ge gästanvändare samma behörigheter som medlemsanvändare som standard anger du **Gästanvändarnas behörigheter begränsas** till **Nej**. Den här inställningen ger alla behörigheter för medlemsanvändare till gästanvändare som standard och gör att gäster kan läggas till i administrativa roller.

## <a name="compare-member-and-guest-default-permissions"></a>Jämför standardbehörigheter för medlemmar och gästanvändare

**Område** | **Användarbehörigheter för medlem** | **Användarbehörigheter för gäst**
------------ | --------- | ----------
Användare och kontakter | Läsa alla offentliga egenskaper om användare och kontakter<br>Bjuda in gäster<br>Ändra eget lösenord<br>Hantera eget mobilnummer<br>Hantera eget foto<br>Ogiltigförklara egna uppdateringstokens | Läsa egna egenskaper<br>Läsa visnings namn, e-post, inloggnings namn, Foto, User Principal Name och egenskaper för användar typ för andra användare och kontakter<br>Ändra eget lösenord
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
Användare kan registrera program | Om du ställer in det här alternativet på Nej förhindras användare från att skapa program registreringar. Möjligheten kan sedan ges tillbaka till specifika individer genom att lägga till dem i rollen programutvecklare.
Tillåt att användare ansluter arbets-eller skolkonton med LinkedIn | Om du ställer in det här alternativet på Nej förhindras användare från att ansluta sina arbets-eller skol konton till deras LinkedIn-konto. Mer information finns i [LinkedIn konto anslutningar data delning och medgivande](https://docs.microsoft.com/azure/active-directory/users-groups-roles/linkedin-user-consent).
Möjlighet att skapa säkerhetsgrupper | När den här inställningen är inställd på Nej kan användare inte skapa säkerhetsgrupper. Globala administratörer och användar administratörer kan fortfarande skapa säkerhets grupper. Se [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](../users-groups-roles/groups-settings-cmdlets.md) för att lära dig hur du ska gå tillväga.
Får skapa Office 365-grupper | Om det här alternativet är Nej får användare inte skapa Office 365-grupper. Om det här alternativet är Vissa får en uppsättning användare skapa Office 365-grupper. Globala administratörer och användar administratörer kommer fortfarande att kunna skapa Office 365-grupper. Se [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](../users-groups-roles/groups-settings-cmdlets.md) för att lära dig hur du ska gå tillväga.
Begränsa åtkomst till Azure AD-administrationsportalen | Om du ställer in det här alternativet på Ja förhindras användare från att komma åt Azure Active Directory via Azure Portal.
Möjlighet att läsa andra användare | Den här inställningen är endast tillgänglig i PowerShell. Om du ställer in den här flaggan på $false hindras alla icke-administratörer från att läsa användar information från katalogen. Den här flaggan förhindrar inte läsning av användar information i andra Microsoft-tjänster som Exchange Online. Den här inställningen är avsedd för särskilda omständigheter och vi rekommenderar att du inte anger den här flaggan till $false.

## <a name="object-ownership"></a>Objektägarskap

### <a name="application-registration-owner-permissions"></a>Ägarbehörigheter för programregistrering
När en användare registrerar ett program, läggs de automatiskt till som ägare till programmet. De kan hantera metadata för programmet som ägare, till exempel namn och behörigheter för appen. De kan också hantera klient-specifik konfiguration av program, till exempel SSO-konfiguration och användartilldelningar. En ägare kan också lägga till eller ta bort andra ägare. Till skillnad från globala administratörer kan ägare bara hantera program som de äger.

### <a name="enterprise-application-owner-permissions"></a>Behörigheter för företags program ägare
När en användare lägger till ett nytt företags program läggs de automatiskt till som en ägare. Som ägare kan de hantera klientens specifika konfiguration av programmet, till exempel SSO-konfigurationen, etablering och användar tilldelningar. En ägare kan också lägga till eller ta bort andra ägare. Till skillnad från globala administratörer kan ägarna bara hantera de program som de äger.

### <a name="group-owner-permissions"></a>Gruppägares behörigheter
När en användare skapar en grupp, läggs de automatiskt som en ägare till gruppen. Som ägare kan de hantera egenskaper för gruppen, till exempel namnet, samt hantera grupp medlemskap. En ägare kan också lägga till eller ta bort andra ägare. Till skillnad från globala administratörer och användar administratörer kan ägarna bara hantera grupper som de äger. Om du vill tilldela en gruppägare, se [Hantera ägare för en grupp](active-directory-accessmanagement-managing-group-owners.md).

### <a name="ownership-permissions"></a>Ägarskaps behörigheter
I följande tabeller beskrivs de olika behörigheterna i Azure Active Directory medlems användare har över ägda objekt. Användaren har bara dessa behörigheter för objekt som de äger.

#### <a name="owned-application-registrations"></a>Ägda program registreringar
Användare kan utföra följande åtgärder för ägda program registreringar.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. Directory/program/mål grupp/uppdatering | Uppdatera egenskapen application.audience i Azure Active Directory. |
| Microsoft. Directory/program/autentisering/uppdatering | Uppdatera egenskapen applications.authentication i Azure Active Directory. |
| Microsoft. Directory/Applications/Basic/Update | Uppdatera grundläggande egenskaper för program i Azure Active Directory. |
| microsoft.directory/applications/credentials/update | Uppdatera egenskapen applications.credentials i Azure Active Directory. |
| Microsoft. Directory/Applications/Delete | Ta bort program i Azure Active Directory. |
| Microsoft. Directory/program/ägare/uppdatering | Uppdatera egenskapen applications.owners i Azure Active Directory. |
| Microsoft. Directory/program/behörigheter/uppdatera | Uppdatera egenskapen applications.permissions i Azure Active Directory. |
| Microsoft. Directory/Applications/policies/Update | Uppdatera egenskapen applications.policies i Azure Active Directory. |
| Microsoft. Directory/program/Återställ | Återställ program i Azure Active Directory. |

#### <a name="owned-enterprise-applications"></a>Ägda företags program
Användare kan utföra följande åtgärder på ägda företags program. Ett företags program består av tjänstens huvud namn, en eller flera program principer, och ibland ett program objekt i samma klient organisation som tjänstens huvud namn.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. Directory/auditLogs/allProperties/Read | Läs alla egenskaper (inklusive privilegierade egenskaper) på auditLogs i Azure Active Directory. |
| Microsoft. Directory/policies/Basic/Update | Uppdatera grundläggande egenskaper för principer i Azure Active Directory. |
| Microsoft. Directory/principer/Delete | Ta bort principer i Azure Active Directory. |
| Microsoft. Directory/principer/ägare/uppdatering | Uppdatera egenskapen policies.owners i Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Uppdatera egenskapen servicePrincipals.appRoleAssignedTo i Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Uppdatera egenskapen users.appRoleAssignments i Azure Active Directory. |
| microsoft.directory/servicePrincipals/audience/update | Uppdatera egenskapen servicePrincipals.audience i Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Uppdatera egenskapen servicePrincipals.authentication i Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Uppdatera grundläggande egenskaper för ServicePrincipals i Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Uppdatera egenskapen servicePrincipals.credentials i Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | Ta bort servicePrincipals i Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Uppdatera egenskapen servicePrincipals.owners i Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Uppdatera egenskapen servicePrincipals.permissions i Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Uppdatera egenskapen servicePrincipals.policies i Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Läsa alla egenskaper (inklusive privilegierade egenskaper) på signInReports i Azure Active Directory. |

#### <a name="owned-devices"></a>Ägda enheter
Användare kan utföra följande åtgärder på ägda enheter.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. Directory/Devices/bitLockerRecoveryKeys/Read | Läs egenskapen devices.bitLockerRecoveryKeys i Azure Active Directory. |
| Microsoft. Directory/enheter/inaktivera | Inaktivera enheter i Azure Active Directory. |

#### <a name="owned-groups"></a>Ägda grupper
Användare kan utföra följande åtgärder i ägda grupper.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. Directory/Groups/appRoleAssignments/Update | Uppdatera egenskapen groups.appRoleAssignments i Azure Active Directory. |
| Microsoft. Directory/grupper/Basic/Update | Uppdatera grundläggande egenskaper om grupper i Azure Active Directory. |
| Microsoft. Directory/grupper/ta bort | Ta bort grupper i Azure Active Directory. |
| Microsoft. Directory/Groups/dynamicMembershipRule/Update | Uppdatera egenskapen groups.dynamicMembershipRule i Azure Active Directory. |
| Microsoft. Directory/grupper/medlemmar/uppdatera | Uppdatera egenskapen groups.members i Azure Active Directory. |
| Microsoft. Directory/grupper/ägare/uppdatera | Uppdatera egenskapen groups.owners i Azure Active Directory. |
| Microsoft. Directory/grupper/Återställ | Återställ grupper i Azure Active Directory. |
| microsoft.directory/groups/settings/update | Uppdatera egenskapen groups.settings i Azure Active Directory. |

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du tilldelar administratörsroller i Azure AD finns [tilldela en användare till administratörsroller i Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
* Mer information om hur resursåtkomsten hanteras i Microsoft Azure finns i [Förstå resursåtkomst i Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Mer information om hur Azure Active Directory relaterar till din Azure-prenumeration finns i [Hur Azure-prenumerationer är associerade med Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Hantera användare](add-users-azure-active-directory.md)
