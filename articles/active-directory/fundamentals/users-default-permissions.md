---
title: Standard användarbehörigheter - Azure Active Directory | Microsoft Docs
description: Läs mer om de olika användarbehörigheterna som är tillgängliga i Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: lizross
ms.reviewer: vincesm
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 206e501860691cccc0578a0df4eec2b161b99b4c
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341367"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Vad är standardbehörigheterna för användare i Azure Active Directory?
Alla användare beviljas en uppsättning standardbehörigheter i Azure Active Directory (AD Azure). En användares åtkomst består av typ av användare, deras [rolltilldelningar](active-directory-users-assign-role-azure-portal.md), och deras ägarskap för enskilda objekt. Den här artikeln beskriver dessa standardbehörigheter och innehåller en jämförelse av standardinställningar för medlem och gäst. Standard användarbehörigheter kan ändras endast i användarinställningarna i Azure AD.

## <a name="member-and-guest-users"></a>Medlems- och gästanvändare
Uppsättningen standardbehörigheterna som tagits emot beror på om användaren är medlem intern klient (medlemsanvändare) eller om användaren flyttas från en annan katalog som B2B-samarbete gäst (gästanvändare). Se [vad är Azure AD B2B-samarbete?](../b2b/what-is-b2b.md) för mer information om att lägga till gästanvändare.
* Medlemsanvändare kan registrera program, hantera sina egna profilbilder och mobila telefonnummer, ändra sina egna lösenord och bjuda in B2B gäster. Dessutom kan användare läsa all kataloginformation (med några få undantag). 
* Gästanvändare ha begränsad katalogbehörigheter. Till exempel kan gästanvändare kan inte bläddra i information från klienten utanför sin egen profilinformation. Däremot kan en gästanvändare hämta information om en annan användare genom att ange användarens huvudnamn eller objekt-ID. Gästanvändare kan läsa egenskaper för grupper som de tillhör, inklusive gruppmedlemskap, oavsett den **behörigheterna för gästanvändare är begränsade** inställningen. Gäst kan inte visa information om andra klient-objekt.

Standardbehörigheter för gäster är begränsade som standard. Gäster kan läggas till i administratörsroller som beviljar dem fullständig läs- och skrivbehörighet i rollen. Det finns en ytterligare begränsning, möjligheten för gäster att bjuda in andra gäster. Om du ställer in **Gäster kan bjuda in** på **Nej** kan gäster inte bjuda in andra gäster. Se [Delegera inbjudningar till B2B-samarbete](../b2b/delegate-invitations.md) för att lära dig hur. Om du vill ge gästanvändare samma behörigheter som medlemsanvändare som standard anger du **Gästanvändarnas behörigheter begränsas** till **Nej**. Den här inställningen ger alla behörigheter för medlemsanvändare till gästanvändare som standard och gör att gäster kan läggas till i administrativa roller.

## <a name="compare-member-and-guest-default-permissions"></a>Jämför standardbehörigheter för medlemmar och gästanvändare

**Område** | **Användarbehörigheter för medlem** | **Användarbehörigheter för gäst**
------------ | --------- | ----------
Användare och kontakter | Läsa alla offentliga egenskaper om användare och kontakter<br>Bjuda in gäster<br>Ändra eget lösenord<br>Hantera eget mobilnummer<br>Hantera eget foto<br>Ogiltigförklara egna uppdateringstokens | Läsa egna egenskaper<br>Läsa visningsnamn, e-post, logga in namn, foto, användarens huvudnamn och typ av användaregenskaper för andra användare och kontakter<br>Ändra eget lösenord
Grupper | Skapa säkerhetsgrupper<br>Skapa Office 365-grupper<br>Läsa alla icke-dolda egenskaper i grupper<br>Läsa icke-dolda gruppmeddelanden<br>Läsa dolda Office 365-gruppmedlemskap för domänansluten grupp<br>Hantera egenskaper, ägarskapet och medlemskap i grupper som användaren äger<br>Lägga till gäster i egna grupper<br>Hantera inställningar för dynamiskt medlemskap<br>Ta bort egna grupper<br>Återställa grupper som ägs av Office 365 | Läsa alla icke-dolda egenskaper i grupper<br>Läsa icke-dolda gruppmeddelanden<br>Läsa dolda Office 365-gruppmedlemskap för domänansluten grupp<br>Hantera egna grupper<br>Lägga till gäster i egna grupper (om detta är tillåtet)<br>Ta bort egna grupper<br>Återställa grupper som ägs av Office 365<br>Läsa egenskaper för grupper som de tillhör, inklusive medlemskap.
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
Användare kan registrera program | Ange det här alternativet på Nej förhindrar användare från att skapa programregistreringar. Möjligheten kan sedan få tillbaka till specifika personer genom att lägga till dem till rollen programutvecklare.
Tillåt att användare ansluter arbets-eller skolkonton med LinkedIn | Ange det här alternativet på Nej förhindrar användare från att ansluta sina arbets- eller skolkonto konto med sina LinkedIn-konto. Mer information finns i [LinkedIn konto anslutningar Datadelning och medgivande](https://docs.microsoft.com/azure/active-directory/users-groups-roles/linkedin-user-consent).
Möjlighet att skapa säkerhetsgrupper | När den här inställningen är inställd på Nej kan användare inte skapa säkerhetsgrupper. Globala administratörer och användaradministratörer kan du fortfarande skapa säkerhetsgrupper. Se [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](../users-groups-roles/groups-settings-cmdlets.md) för att lära dig hur du ska gå tillväga.
Får skapa Office 365-grupper | Om det här alternativet är Nej får användare inte skapa Office 365-grupper. Om det här alternativet är Vissa får en uppsättning användare skapa Office 365-grupper. Globala administratörer och användaradministratörer kommer fortfarande att kunna skapa Office 365-grupper. Se [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](../users-groups-roles/groups-settings-cmdlets.md) för att lära dig hur du ska gå tillväga.
Begränsa åtkomst till Azure AD-administrationsportalen | Ange det här alternativet på Ja förhindrar användare från att komma åt Azure Active Directory via Azure portal.
Möjlighet att läsa andra användare | Den här inställningen är endast tillgänglig i PowerShell. $False att ställa in den här flaggan förhindrar alla icke-administratörer från att läsa användarinformation från katalogen. Den här flaggan förhindrar inte att läsa information i andra Microsoft-tjänster som Exchange Online. Den här inställningen är avsedd för särskilda omständigheter och den här flaggan $false rekommenderas inte.

## <a name="object-ownership"></a>Objektägarskap

### <a name="application-registration-owner-permissions"></a>Ägarbehörigheter för programregistrering
När en användare registrerar ett program, läggs de automatiskt till som ägare till programmet. De kan hantera metadata för programmet som ägare, till exempel namn och behörigheter för appen. De kan också hantera klient-specifik konfiguration av program, till exempel SSO-konfiguration och användartilldelningar. En ägare kan också lägga till eller ta bort andra ägare. Till skillnad från globala administratörer kan ägare bara hantera program som de äger.

### <a name="enterprise-application-owner-permissions"></a>Ägarbehörighet för Enterprise-program
När en användare lägger till en ny företagsprogram, läggs de automatiskt som ägare. Ägare kan hantera de klientspecifik konfigurationen av programmet, till exempel SSO konfiguration, etablering och användartilldelningar. En ägare kan också lägga till eller ta bort andra ägare. Till skillnad från globala administratörer kan ägare hantera endast de program som de äger.

### <a name="group-owner-permissions"></a>Gruppägares behörigheter
När en användare skapar en grupp, läggs de automatiskt som en ägare till gruppen. Ägare kan de hantera egenskaperna för en grupp, till exempel namnet, samt hantera gruppmedlemskap. En ägare kan också lägga till eller ta bort andra ägare. Till skillnad från globala administratörer och användaradministratörer kan ägare bara hantera grupper som de äger. Om du vill tilldela en gruppägare, se [Hantera ägare för en grupp](active-directory-accessmanagement-managing-group-owners.md).

### <a name="ownership-permissions"></a>Ägarskap behörigheter
I följande tabeller beskrivs de särskilda behörigheterna i Azure Active Directory användare har över ägda objekt. Användaren har endast dessa behörigheter på objekt som de äger.

#### <a name="owned-application-registrations"></a>Ägda programregistreringar
Användarna kan utföra följande åtgärder för ägda programregistreringar.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/applications/audience/update | Uppdatera applications.audience-egenskapen i Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Uppdatera applications.authentication-egenskapen i Azure Active Directory. |
| microsoft.directory/applications/basic/update | Uppdatera grundläggande egenskaper för program i Azure Active Directory. |
| microsoft.directory/applications/credentials/update | Uppdatera applications.credentials-egenskapen i Azure Active Directory. |
| Microsoft.Directory/Applications/delete | Ta bort program i Azure Active Directory. |
| microsoft.directory/applications/owners/update | Uppdatera applications.owners-egenskapen i Azure Active Directory. |
| microsoft.directory/applications/permissions/update | Uppdatera applications.permissions-egenskapen i Azure Active Directory. |
| microsoft.directory/applications/policies/update | Uppdatera applications.policies-egenskapen i Azure Active Directory. |
| Microsoft.Directory/Applications/Restore | Återställa program i Azure Active Directory. |

#### <a name="owned-enterprise-applications"></a>Ägda företagsprogram
Användarna kan utföra följande åtgärder för ägda företagsprogram. Ett företagsprogram består av tjänstens huvudnamn, en eller flera principer för program och ibland ett programobjekt i samma klientorganisation som tjänstens huvudnamn.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Läsa alla egenskaper (inklusive Privilegierade egenskaper) för auditLogs i Azure Active Directory. |
| microsoft.directory/policies/basic/update | Uppdatera grundläggande egenskaper för principer i Azure Active Directory. |
| Microsoft.Directory/policies/delete | Ta bort principer i Azure Active Directory. |
| Microsoft.Directory/policies/owners/Update | Uppdatera policies.owners-egenskapen i Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Uppdatera servicePrincipals.appRoleAssignedTo-egenskapen i Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Uppdatera users.appRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.directory/servicePrincipals/audience/update | Uppdatera servicePrincipals.audience-egenskapen i Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Uppdatera servicePrincipals.authentication-egenskapen i Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Uppdatera grundläggande egenskaper för servicePrincipals i Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Uppdatera servicePrincipals.credentials-egenskapen i Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | Ta bort servicePrincipals i Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Uppdatera servicePrincipals.owners-egenskapen i Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Uppdatera servicePrincipals.permissions-egenskapen i Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Uppdatera servicePrincipals.policies-egenskapen i Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Läsa alla egenskaper (inklusive Privilegierade egenskaper) för signInReports i Azure Active Directory. |

#### <a name="owned-devices"></a>Företagsägda enheter
Användarna kan utföra följande åtgärder för företagsägda enheter.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Läsa devices.bitLockerRecoveryKeys-egenskapen i Azure Active Directory. |
| Microsoft.Directory/Devices/disable | Inaktivera enheter i Azure Active Directory. |

#### <a name="owned-groups"></a>Ägda grupper
Användarna kan utföra följande åtgärder för ägda grupper.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/groups/appRoleAssignments/update | Uppdatera groups.appRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.directory/groups/basic/update | Uppdatera grundläggande egenskaper för grupper i Azure Active Directory. |
| Microsoft.Directory/groups/delete | Ta bort grupper i Azure Active Directory. |
| microsoft.directory/groups/dynamicMembershipRule/update | Uppdatera groups.dynamicMembershipRule-egenskapen i Azure Active Directory. |
| microsoft.directory/groups/members/update | Uppdatera groups.members-egenskapen i Azure Active Directory. |
| microsoft.directory/groups/owners/update | Uppdatera groups.owners-egenskapen i Azure Active Directory. |
| Microsoft.Directory/groups/Restore | Återställa grupper i Azure Active Directory. |
| microsoft.directory/groups/settings/update | Uppdatera groups.settings-egenskapen i Azure Active Directory. |

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du tilldelar administratörsroller i Azure AD finns [tilldela en användare till administratörsroller i Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
* Mer information om hur resursåtkomsten hanteras i Microsoft Azure finns i [Förstå resursåtkomst i Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Mer information om hur Azure Active Directory relaterar till din Azure-prenumeration finns i [Hur Azure-prenumerationer är associerade med Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Hantera användare](add-users-azure-active-directory.md)
