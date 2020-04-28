---
title: Standard användar behörigheter – Azure Active Directory | Microsoft Docs
description: Lär dig mer om de olika användar behörigheter som är tillgängliga i Azure Active Directory.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80366247"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Vilka är standard användar behörigheterna i Azure Active Directory?
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
Subscriptions | Läsa alla prenumerationer<br>Aktivera tjänsteplanmedlem | Inga behörigheter
Principer | Läs alla principegenskaper<br>Hantera alla egenskaper för egna principer | Inga behörigheter

## <a name="to-restrict-the-default-permissions-for-member-users"></a>Så här begränsar du standardbehörigheter för medlemsanvändare

Standardbehörigheter för medlemmsanvändare kan begränsas på följande sätt.

Behörighet | Förklaring av inställning
---------- | ------------
Användare kan registrera program | Om du ställer in det här alternativet på Nej förhindras användare från att skapa program registreringar. Möjligheten kan sedan ges tillbaka till specifika individer genom att lägga till dem i rollen programutvecklare.
Tillåt att användare ansluter arbets-eller skol konton med LinkedIn | Om du ställer in det här alternativet på Nej förhindras användare från att ansluta sina arbets-eller skol konton till deras LinkedIn-konto. Mer information finns i [LinkedIn konto anslutningar data delning och medgivande](https://docs.microsoft.com/azure/active-directory/users-groups-roles/linkedin-user-consent).
Möjlighet att skapa säkerhetsgrupper | När den här inställningen är inställd på Nej kan användare inte skapa säkerhetsgrupper. Globala administratörer och användar administratörer kan fortfarande skapa säkerhets grupper. Se [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](../users-groups-roles/groups-settings-cmdlets.md) för att lära dig hur du ska gå tillväga.
Får skapa Office 365-grupper | Om det här alternativet är Nej får användare inte skapa Office 365-grupper. Om det här alternativet är Vissa får en uppsättning användare skapa Office 365-grupper. Globala administratörer och användar administratörer kommer fortfarande att kunna skapa Office 365-grupper. Se [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](../users-groups-roles/groups-settings-cmdlets.md) för att lära dig hur du ska gå tillväga.
Begränsa åtkomst till Azure AD-administrationsportalen | Om du ställer in det här alternativet på Nej kan icke-administratörer använda Azure AD-administrationskonsolen för att läsa och hantera Azure AD-resurser. Ja, begränsar alla icke-administratörer från att komma åt Azure AD-data i administrations portalen. Viktigt att Observera: den här inställningen begränsar inte åtkomsten till Azure AD-data med hjälp av PowerShell eller andra klienter, till exempel Visual Studio. När det är inställt på Ja, för att ge en särskild icke-administratör möjlighet att använda Azure AD-administrationskonsolen tilldelar du en administrativ roll som katalog läsar rollen. Med den här rollen kan du läsa grundläggande katalog information som medlems användare har som standard (gäster och tjänstens huvud namn).
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
| Microsoft. Directory/program/mål grupp/uppdatering | Uppdatera program. Audience-egenskapen i Azure Active Directory. |
| Microsoft. Directory/program/autentisering/uppdatering | Uppdatera program. Authentication-egenskapen i Azure Active Directory. |
| Microsoft. Directory/Applications/Basic/Update | Uppdatera grundläggande egenskaper för program i Azure Active Directory. |
| Microsoft. Directory/program/autentiseringsuppgifter/uppdatera | Uppdatera program. credentials-egenskapen i Azure Active Directory. |
| Microsoft. Directory/Applications/Delete | Ta bort program i Azure Active Directory. |
| Microsoft. Directory/program/ägare/uppdatering | Uppdatera program. Owners-egenskapen i Azure Active Directory. |
| Microsoft. Directory/program/behörigheter/uppdatera | Uppdatera program. Permissions-egenskapen i Azure Active Directory. |
| Microsoft. Directory/Applications/policies/Update | Uppdatera Applications. policys-egenskapen i Azure Active Directory. |
| Microsoft. Directory/program/Återställ | Återställa program i Azure Active Directory. |

#### <a name="owned-enterprise-applications"></a>Ägda företags program
Användare kan utföra följande åtgärder på ägda företags program. Ett företags program består av tjänstens huvud namn, en eller flera program principer, och ibland ett program objekt i samma klient organisation som tjänstens huvud namn.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. Directory/auditLogs/allProperties/Read | Läs alla egenskaper (inklusive privilegierade egenskaper) på auditLogs i Azure Active Directory. |
| Microsoft. Directory/policies/Basic/Update | Uppdatera grundläggande egenskaper för principer i Azure Active Directory. |
| Microsoft. Directory/principer/Delete | Ta bort principer i Azure Active Directory. |
| Microsoft. Directory/principer/ägare/uppdatering | Uppdatera policys. Owners-egenskapen i Azure Active Directory. |
| Microsoft. Directory/Service princip ALS/appRoleAssignedTo/Update | Uppdatera service princip ALS. appRoleAssignedTo-egenskapen i Azure Active Directory. |
| Microsoft. Directory/Service princip ALS/appRoleAssignments/Update | Uppdatera users. appRoleAssignments-egenskapen i Azure Active Directory. |
| Microsoft. Directory/Service princip ALS/målgrupp/uppdatera | Uppdatera service princip ALS. Audience-egenskapen i Azure Active Directory. |
| Microsoft. Directory/Service princip ALS/Authentication/Update | Uppdatera service princip ALS. Authentication-egenskapen i Azure Active Directory. |
| Microsoft. Directory/Service princip ALS/Basic/Update | Uppdatera grundläggande egenskaper för service princip ALS i Azure Active Directory. |
| Microsoft. Directory/Service princip ALS/credentials/Update | Uppdatera service princip ALS. credentials-egenskapen i Azure Active Directory. |
| Microsoft. Directory/Service princip ALS/Delete | Ta bort service princip ALS i Azure Active Directory. |
| Microsoft. Directory/Service princip ALS/Owners/Update | Uppdatera service princip ALS. Owners-egenskapen i Azure Active Directory. |
| Microsoft. Directory/Service princip ALS/Permissions/Update | Uppdatera service princip ALS. Permissions-egenskapen i Azure Active Directory. |
| Microsoft. Directory/Service princip ALS/policies/Update | Uppdatera service princip ALS. policys-egenskapen i Azure Active Directory. |
| Microsoft. Directory/signInReports/allProperties/Read | Läs alla egenskaper (inklusive privilegierade egenskaper) på signInReports i Azure Active Directory. |

#### <a name="owned-devices"></a>Ägda enheter
Användare kan utföra följande åtgärder på ägda enheter.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. Directory/Devices/bitLockerRecoveryKeys/Read | Läsa Devices. bitLockerRecoveryKeys-egenskapen i Azure Active Directory. |
| Microsoft. Directory/enheter/inaktivera | Inaktivera enheter i Azure Active Directory. |

#### <a name="owned-groups"></a>Ägda grupper
Användare kan utföra följande åtgärder i ägda grupper.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. Directory/Groups/appRoleAssignments/Update | Uppdatera Groups. appRoleAssignments-egenskapen i Azure Active Directory. |
| Microsoft. Directory/grupper/Basic/Update | Uppdatera grundläggande egenskaper för grupper i Azure Active Directory. |
| Microsoft. Directory/grupper/ta bort | Ta bort grupper i Azure Active Directory. |
| Microsoft. Directory/Groups/dynamicMembershipRule/Update | Uppdatera Groups. dynamicMembershipRule-egenskapen i Azure Active Directory. |
| Microsoft. Directory/grupper/medlemmar/uppdatera | Uppdatera Groups. members-egenskapen i Azure Active Directory. |
| Microsoft. Directory/grupper/ägare/uppdatera | Uppdatera Groups. Owners-egenskapen i Azure Active Directory. |
| Microsoft. Directory/grupper/Återställ | Återställ grupper i Azure Active Directory. |
| Microsoft. Directory/grupper/inställningar/uppdatera | Uppdatera Groups. Settings-egenskapen i Azure Active Directory. |

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du tilldelar administratörs roller för Azure AD finns i [tilldela en användare till administratörs roller i Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
* Mer information om hur resursåtkomsten hanteras i Microsoft Azure finns i [Förstå resursåtkomst i Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Mer information om hur Azure Active Directory relaterar till din Azure-prenumeration finns i [Hur Azure-prenumerationer är associerade med Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Hantera användare](add-users-azure-active-directory.md)
