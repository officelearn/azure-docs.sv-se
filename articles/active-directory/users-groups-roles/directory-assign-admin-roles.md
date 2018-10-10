---
title: Behörigheter för administratör i Azure Active Directory | Microsoft Docs
description: En administratörsroll kan lägga till användare, tilldela administrativa roller, återställa användarlösenord, hantera användarlicenser eller hantera domäner.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 09/25/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: f54f13c6b7bb75644e95e62f13d6427e07c64037
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2018
ms.locfileid: "48884600"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Behörigheter för administratör i Azure Active Directory

Med Azure Active Directory (Azure AD) kan ange du separata administratörer att hantera olika funktioner. Administratörer kan utses i Azure AD-portalen för att utföra åtgärder som att lägga till eller ändra användare, tilldela administrativa roller, återställa användarlösenord, hantera användarlicenser och hantera domännamn.

Globala administratörer har åtkomst till alla administrativa funktioner. Som standard tilldelas den person som registrerar sig för en Azure-prenumeration rollen som Global administratör för katalogen. Endast globala administratörer kan delegera administratörsroller.

## <a name="assign-or-remove-administrator-roles"></a>Tilldela eller ta bort administratörsroller

Om du vill lära dig mer om att tilldela administrativa roller till en användare i Azure Active Directory, se [visa och tilldela administratörsroller i Azure Active Directory](directory-manage-roles-portal.md).

## <a name="available-roles"></a>Tillgängliga roller

Följande administratörsroller är tillgängliga:

* **[Programadministratör](#application-administrator)**: användare i den här rollen kan skapa och hantera alla aspekter av företagsprogram och programregistreringar proxyinställningarna för programmet. Den här rollen ger även möjlighet att godkänna delegerade behörigheter och programbehörigheter exklusive Microsoft Graph och Azure AD Graph. Medlemmar i den här rollen har inte lagts som ägare när du skapar nya programregistreringar eller företagsprogram.

* **[Programutvecklare](#application-developer)**: användare i den här rollen kan skapa programregistreringar när den ”användare kan registrera program” är inställt på Nej. Den här rollen kan också medlemmar måste samtycka på sina egna räkning när den ”användare kan godkänna att appar får åtkomst till företagets data å deras vägnar” är inställt på Nej. Medlemmar i den här rollen har lagts till som ägare när du skapar nya programregistreringar eller företagsprogram.

* **[Faktureringsadministratör](#billing-administrator)**: gör inköp, hanterar prenumerationer, hanterar supportärenden och övervakar tjänstehälsa.

* **[Molnprogramadministratör](#cloud-application-administrator)**: användare i den här rollen har samma behörigheter som rollen programadministratör exklusive möjligheten att hantera programproxy. Den här rollen ger möjlighet att skapa och hantera alla aspekter av företagsprogram och programregistreringar. Den här rollen ger även möjlighet att godkänna delegerade behörigheter och programbehörigheter exklusive Microsoft Graph och Azure AD Graph. Medlemmar i den här rollen har inte lagts som ägare när du skapar nya programregistreringar eller företagsprogram.

* **[Molnenhetsadministratör](#cloud-device-administrator)**: användare i den här rollen kan aktivera, inaktivera, och ta bort enheter i Azure AD och Läs Windows 10 BitLocker-nycklar (om sådan finns) i Azure-portalen. Rollen ger inte behörighet att hantera andra egenskaper på enheten.

* **[Efterlevnadsadministratör](#compliance-administrator)**: användare med den här rollen har hanteringsbehörigheter inom Office 365 Security & Compliance Center och administrationscentret för Exchange. Mer information på [om Office 365-administratörsroller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Administratör för villkorsstyrd åtkomst](#conditional-access-administrator)**: användare med den här rollen har behörighet att hantera inställningar för villkorlig åtkomst i Azure Active Directory.
  > [!NOTE]
  > Om du vill distribuera Exchange ActiveSync villkorsstyrd åtkomstprincip i Azure, måste användaren också vara en Global administratör.
  
* **[Enhetsadministratörer](#device-administrators)**: den här rollen är tilldelas endast som en ytterligare lokal administratör i [Enhetsinställningar](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Användare med den här rollen blir administratörer för den lokala datorn på alla Windows 10-enheter som är anslutna till Azure Active Directory. De har inte behörighet att hantera enheters objekt i Azure Active Directory. 

* **[Katalogläsare](#directory-readers)**: det här är en äldre roll som ska tilldelas till program som inte stöder den [godkänna Framework](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Det ska inte tilldelas alla användare.

* **[Konton för katalogsynkronisering](#directory-synchronization-accounts)**: Använd inte. Den här rollen är tilldelas automatiskt till Azure AD Connect-tjänsten och inte avsedd eller stöd för annan användning.

* **[Katalogskrivare](#directory-writers)**: det här är en äldre roll som ska tilldelas till program som inte stöder den [godkänna Framework](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Det ska inte tilldelas alla användare.

* **[Dynamics 365-tjänstadministratör / CRM-tjänstadministratör](#dynamics-365-service-administrator)**: användare med den här rollen har globala behörigheter inom Microsoft Dynamics 365 Online när tjänsten finns närvarande, liksom möjlighet att hantera supportbegäranden och Kontrollera tjänstens hälsotillstånd. Mer information på [använda administratörsrollen för att hantera din klient](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

* **[Exchange-tjänstadministratör](#exchange-service-administrator)**: användare med den här rollen har globala behörigheter inom Microsoft Exchange Online när tjänsten finns. Mer information på [om Office 365-administratörsroller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Global administratör / företagsadministratör](#company-administrator)**: användare med den här rollen har åtkomst till alla administrativa funktioner i Azure Active Directory, samt tjänster som använder Azure Active Directory-identiteter som Exchange Online SharePoint Online och Skype för företag Online. Den person som registrerar sig för Azure Active Directory-klient blir global administratör. Endast globala administratörer kan tilldela andra administratörsroller. Det kan finnas mer än en global administratör i företaget. Globala administratörer kan återställa lösenordet för alla användare och alla andra administratörer.

  > [!NOTE]
  > I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras rollen som ”företagets administratör”. Det är ”Global administratör” i den [Azure-portalen](https://portal.azure.com).
  >
  >

* **[Gäst bjuder in](#guest-inviter)**: användare i den här rollen kan hantera inbjudningar för Azure Active Directory B2B gäst när den **medlemmar kan bjuda in** användaren är inställt på Nej. Mer information om B2B-samarbete i [om Azure AD B2B-samarbete](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Den omfattar inte några andra behörigheter.

* **[Information Protection-administratör](#information-protection-administrator)**: användare med den här rollen har alla behörigheter i Azure Information Protection-tjänsten. Den här rollen kan konfigurera etiketter för Azure Information Protection-principen, hantera skyddsmallar och aktivera skydd. Den här rollen ger inte några behörigheter i Identity Protection Center, Privileged Identity Management, övervaka Office 365 Service Health eller Office 365 säkerhets- och Efterlevnadscenter.

* **[Intune-tjänstadministratör](#intune-service-administrator)**: användare med den här rollen har globala behörigheter inom Microsoft Intune Online när tjänsten finns. Dessutom innehåller den här rollen möjlighet att hantera användare och enheter för att associera principer, samt skapa och hantera grupper. Mer information på [rollbaserad administrationskontroll (RBAC) med Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control)

* **[Licensadministratör](#license-administrator)**: användare i den här rollen kan lägga till, ta bort, och uppdatera licenstilldelningar på användare, grupper (med gruppbaserad licensiering) och hantera användningsplats på användare. Rollen ger inte möjlighet att köpa eller hantera prenumerationer, skapa eller hantera grupper, eller skapa eller hantera användarna, förutom användningsplatsen.

* **[Message Center läsare](#message-center-reader)**: användare i den här rollen kan övervaka meddelanden och rådgivande hälsotillstånd uppdateringar i [Office 365-meddelandecentret](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) för organisationen på konfigurerade tjänster, till exempel Exchange, Intune, och Microsoft Teams. Meddelandecenter läsarna få veckovis e-sammandrag av inlägg, uppdateringar, och kan dela center meddelandeposter i Office 365. I Azure AD har användare som tilldelats den här rollen endast läsåtkomst på Azure AD-tjänster, till exempel användare och grupper. 

* **[Partner nivå 1-Support](#partner-tier1-support)**: Använd inte. Den här rollen har gjorts inaktuell och kommer att tas bort från Azure AD i framtiden. Den här rollen är avsedd att användas av ett litet antal Microsoft återförsäljning-partner och är inte avsedd för allmänt bruk.

* **[Partner – nivå 2-stöd](#partner-tier2-support)**: Använd inte. Den här rollen har gjorts inaktuell och kommer att tas bort från Azure AD i framtiden. Den här rollen är avsedd att användas av ett litet antal Microsoft återförsäljning-partner och är inte avsedd för allmänt bruk.

* **[Lösenordsadministratör / Supportavdelningsadministratör](#helpdesk-administrator)**: användare med den här rollen kan ändra lösenord, ogiltigförklara uppdateringstoken, hanterar tjänstbegäranden och övervakar tjänstehälsa. Ogiltigförklara en uppdateringstoken Tvingar användaren att logga in igen. Helpdeks-administratörer kan återställa lösenord och ogiltigförklara uppdaterings-tokens för andra användare som är icke-administratörer eller medlemmar i följande roller:
  * Katalogläsare
  * Gäst bjuder in
  * Supportavdelningsadministratör
  * Meddelandecenterläsare
  * Rapportläsare
  
  > [!NOTE]
  > I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras rollen som ”Supportavdelningsadministratör”. Det är ”Lösenordsadministratör” i den [Azure-portalen](https://portal.azure.com/).
  >
  
* **[Power BI-tjänstadministratör](#power-bi-service-administrator)**: användare med den här rollen har globala behörigheter inom Microsoft Power BI när tjänsten finns närvarande, liksom möjlighet att hantera supportbegäranden och kontrollera tjänstens hälsotillstånd. Mer information på [förstå administratörsrollen för Power BI](https://docs.microsoft.com/power-bi/service-admin-role).

* **[Privilegierad Rolladministratör](#privileged-role-administrator)**: användare med den här rollen kan hantera rolltilldelningar i Azure Active Directory, liksom i Azure AD Privileged Identity Management. Den här rollen kan dessutom hantera alla aspekter av Privileged Identity Management.

* **[Rapporterar läsare](#reports-reader)**: användare med den här rollen kan visa användning reporting data och instrumentpanelen rapporter i Administrationscenter för Office 365 och införande kontexten pack i Power BI. Dessutom rollen ger åtkomst till loggar in rapporter och aktivitet i Azure AD och data som returneras av Microsoft Graph rapporterings-API. En användare som tilldelats rollen rapportläsare kan komma åt endast relevanta användnings- och användningsstatistik. De har inte någon administratörsbehörighet för att konfigurera inställningar eller åtkomst till produktspecifika Administrationscenter som Exchange. 

* **[Säkerhetsadministratör](#security-administrator)**: användare med den här rollen har alla skrivskyddade behörigheter för rollen säkerhetsläsare, plus möjligheten att hantera konfigurationen för säkerhetsrelaterade tjänster: Azure Active Directory Identity Protection, Azure Information Protection och Office 365-säkerhet- och Efterlevnadscenter. Mer information om behörigheter för Office 365 finns på [behörigheter i Office 365 säkerhets- och Efterlevnadscenter](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).
  
  | I | Kan göra |
  | --- | --- |
  | Identity Protection Center  |<ul><li>Alla behörigheter för rollen Säkerhetsläsare.<li>Dessutom möjlighet att utföra alla IPC åtgärder förutom för återställning av lösenord. |
  | Privileged Identity Management |<ul><li>Alla behörigheter för rollen Säkerhetsläsare.<li>**Det går inte att** hantera rollmedlemskap för Azure AD eller inställningar. |
  | <p>Övervakare för Office 365 Service Health</p><p>Säkerhets- och efterlevnadscenter för Office 365 |<ul><li>Alla behörigheter för rollen Säkerhetsläsare.<li>Kan konfigurera alla inställningar i funktionen Advanced Threat Protection (skydd för skadlig kod och virus, skadliga URL-config, URL: en spårning osv.). |
  
* **[Säkerhetsläsare](#security-reader)**: användare med den här rollen har global skrivskyddad åtkomst, inklusive all information i Azure Active Directory, Identity Protection, Privileged Identity Management, samt möjlighet att läsa Azure Active Directory logga in rapporter och granskningsloggar. Rollen ger även skrivskyddad behörighet i Office 365 säkerhets- och Efterlevnadscenter. Mer information om behörigheter för Office 365 finns på [behörigheter i Office 365 säkerhets- och Efterlevnadscenter](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

  | I | Kan göra |
  | --- | --- |
  | Identity Protection Center  |Läsa alla säkerhetsrapporter och inställningsinformation för säkerhetsfunktioner<ul><li>Skydd mot skräppost<li>Kryptering<li>Skydd mot dataförlust<li>Mot skadlig kod<li>Avancerat skydd<li>Mot nätfiske<li>Mailflow regler |
  | Privileged Identity Management |<p>Har skrivskyddad åtkomst till all information som visas i Azure AD PIM: principer och rapporter för Azure AD-rolltilldelningar security granskar och i framtiden skrivskyddad åtkomst till principdata och rapporter för scenarier utöver Azure AD-rolltilldelning.<p>**Det går inte att** registrera dig för Azure AD PIM eller göra några ändringar. I PIM'S portal eller via PowerShell kan någon i den här rollen aktivera ytterligare roller (till exempel Global administratör eller privilegierad Rolladministratör), om användaren är en kandidat för dessa. |
  | <p>Övervakare för Office 365 Service Health</p><p>Säkerhets- och efterlevnadscenter för Office 365</p> |<ul><li>Läsa och hantera aviseringar<li>Läs in säkerhetsprinciper<li>Läsa hotinformation, Cloud App Discovery och karantän sökning och undersök<li>Läsa alla rapporter |

* **[Tjänstesupportsadministratören](#service-support-administrator)**: användare med den här rollen kan öppna supportbegäranden med Microsoft för Azure och Office 365-tjänster och visa tjänstinstrumentpanelen och meddelandecenter i datacentret i Azure-portalen och Office 365-administrationsportalen. Mer information på [om Office 365-administratörsroller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[SharePoint-tjänstadministratör](#sharepoint-service-administrator)**: användare med den här rollen har globala behörigheter inom Microsoft SharePoint Online när tjänsten finns närvarande, liksom möjlighet att hantera supportbegäranden och kontrollera tjänstens hälsotillstånd. Mer information på [om Office 365-administratörsroller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Skype för företag – / Lync-tjänstadministratör](#lync-service-administrator)**: användare med den här rollen har globala behörigheter inom Microsoft Skype för företag när tjänsten finns närvarande, samt hantera Skype-specifika användarattribut i Azure Active Katalogen. Den här rollen ger dessutom möjlighet att hantera supportbegäranden och kontrollera tjänstens hälsotillstånd och komma åt Teams och Skype för Business Admin Center. Kontot måste också ha licens för team eller teamen PowerShell-cmdlet: ar kan köras. Mer information på [om the Skype för företag-administratörsroll](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) och team licensinformation på [Skype för företag och Microsoft Teams-tillägg-licensiering](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

  > [!NOTE]
  > I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras rollen som ”Lync-tjänstadministratör”. Det är ”Skype för företag tjänstadministratör” i den [Azure-portalen](https://portal.azure.com/).
  >
  >

* **[Teams kommunikation administratör](#teams-communications-administrator)**: användare i den här rollen kan hantera aspekter av Microsoft Teams-arbetsbelastningen som rör röst- och telefoni. Detta inkluderar hanteringsverktygen för tilldelning av telefon, röst- och möte principer och fullständig åtkomst till anropet analytics verktygsuppsättningen.

* **[Teams kommunikation supporttekniker](#teams-communications-support-engineer)**: användare i den här rollen kan felsöka kommunikationsproblem i Microsoft Teams och Skype för företag med hjälp av användaren anropa felsökningsverktyg i Microsoft Teams och Skype för Administrationscenter för företag. Användare i den här rollen kan visa fullständiga anropet registrera information för alla deltagare som ingår.

* **[Teams kommunikation Support Specialist](#teams-communications-support-specialist)**: användare i den här rollen kan felsöka kommunikationsproblem i Microsoft Teams och Skype för företag med hjälp av användaren anropa felsökningsverktyg i Microsoft Teams och Skype för Administrationscenter för företag. Användare i den här rollen kan bara se användarinformation i anropet för den specifika användaren har sökt.

* **[Team tjänstadministratör](#teams-service-administrator)**: användare i den här rollen kan hantera alla aspekter av Microsoft Teams-arbetsbelastning via Microsoft Teams och Skype för företag-administrationscentret och respektive PowerShell-moduler. Detta inkluderar bland andra delar alla hanteringsverktyg som är relaterade till telefoni, meddelanden, möten och team själva. Den här rollen ger även möjlighet att hantera Office 365-grupper.

* **[Användarkontoadministratören](#user-account-administrator)**: användare med den här rollen kan skapa användare och hantera alla aspekter av användare med vissa begränsningar (se nedan). Dessutom kan kan användare med den här rollen skapa och hantera alla grupper. Den här rollen inkluderar även möjligheten att skapa och hantera användarvyer, hantera supportbegäranden och kontrollera tjänstens hälsotillstånd.

  | | |
  | --- | --- |
  |Allmänna behörigheter|<p>Skapa användare och grupper</p><p>Skapa och hantera användarvyer</p><p>Hantera supportärenden för Office|
  |<p>På alla användare, inklusive alla administratörer</p>|<p>Hantera licenser</p><p>Hantera alla användaregenskaper utom User Principal Name</p>
  |Endast på användare som är icke-administratörer eller begränsade administrativa roller i något av följande:<ul><li>Katalogläsare<li>Gäst bjuder in<li>Supportavdelningsadministratör<li>Meddelandecenterläsare<li>Rapportläsare<li>Användarkonto-administratör|<p>Ta bort och återställning</p><p>Inaktivera och aktivera</p><p>Ogiltigförklara uppdatera token</p><p>Hantera egenskaper för alla användare, inklusive användarens huvudnamn</p><p>Återställa lösenord</p><p>Uppdatera (FIDO) enhetsnycklar</p>

I följande tabeller beskrivs de särskilda behörigheterna i Azure Active Directory för varje roll. Vissa roller kan ha ytterligare behörigheter i Microsoft-tjänster utanför Azure Active Directory.

### <a name="application-administrator"></a>Programadministratör
Kan skapa och hantera alla aspekter av appregistreringar och enterprise-appar.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft.aad.Directory/Applications/Audience/Update | Uppdatera applications.audience-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/Authentication/Update | Uppdatera applications.authentication-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/Basic/Update | Uppdatera grundläggande egenskaper för program i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/Create | Skapa program i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/Credentials/Update | Uppdatera applications.credentials-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/delete | Ta bort program i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/owners/Update | Uppdatera applications.owners-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/permissions/Update | Uppdatera applications.permissions-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/policies/Update | Uppdatera applications.policies-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Skapa appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/read | Läsa appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Uppdatera appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Ta bort appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Läsa policies.applicationConfiguration-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Uppdatera policies.applicationConfiguration-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Skapa principer i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Ta bort principer i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Läsa policies.applicationConfiguration-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Uppdatera policies.applicationConfiguration-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Läsa policies.applicationConfiguration-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Uppdatera grundläggande egenskaper för servicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Skapa servicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Ta bort servicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Uppdatera servicePrincipals.appRoleAssignedTo-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Uppdatera servicePrincipals.appRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Uppdatera servicePrincipals.owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Uppdatera servicePrincipals.policies-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Hantera licenser för användare i Azure Active Directory. |
| microsoft.aad.reports/allEntities/read | Läsa Azure AD-rapporter. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="application-developer"></a>Programutvecklare
Kan skapa programregistreringar oberoende av den ”användare kan registrera program' inställningen.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | Skapa program i Azure Active Directory. Skaparen läggs till som första ägare och det skapade objektet räknas mot Skaparens kvot på 250 skapade objekt. |
| microsoft.aad.directory/appRoleAssignments/createAsOwner | Skapa appRoleAssignments i Azure Active Directory. Skaparen läggs till som första ägare och det skapade objektet räknas mot Skaparens kvot på 250 skapade objekt. |
| microsoft.aad.directory/oAuth2PermissionGrants/createAsOwner | Skapa oAuth2PermissionGrants i Azure Active Directory. Skaparen läggs till som första ägare och det skapade objektet räknas mot Skaparens kvot på 250 skapade objekt. |
| microsoft.aad.directory/servicePrincipals/createAsOwner | Skapa servicePrincipals i Azure Active Directory. Skaparen läggs till som första ägare och det skapade objektet räknas mot Skaparens kvot på 250 skapade objekt. |

### <a name="billing-administrator"></a>Faktureringsadministratör
Kan utföra vanliga faktureringsrelaterade uppgifter som uppdatering av betalningsinformation.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft.aad.Directory/Organization/Basic/Update | Uppdatera grundläggande egenskaper på organisation i Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/update | Uppdatera organization.trustedCAsForPasswordlessAuth-egenskapen i Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Hantera alla aspekter av Azure Access-tjänsten. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| microsoft.commerce.billing/allEntities/allTasks | Hantera alla aspekter av Office 365-fakturering. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="cloud-application-administrator"></a>Molnprogramadministratör
Kan skapa och hantera alla aspekter av appregistreringar och enterprise-appar förutom App Proxy.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft.aad.Directory/Applications/Audience/Update | Uppdatera applications.audience-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/Authentication/Update | Uppdatera applications.authentication-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/Basic/Update | Uppdatera grundläggande egenskaper för program i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/Create | Skapa program i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/Credentials/Update | Uppdatera applications.credentials-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/delete | Ta bort program i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/owners/Update | Uppdatera applications.owners-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/permissions/Update | Uppdatera applications.permissions-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/policies/Update | Uppdatera applications.policies-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Skapa appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Uppdatera appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Ta bort appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Skapa principer i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Läsa policies.applicationConfiguration-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Uppdatera policies.applicationConfiguration-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Ta bort principer i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Läsa policies.applicationConfiguration-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Uppdatera policies.applicationConfiguration-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Läsa policies.applicationConfiguration-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Uppdatera servicePrincipals.appRoleAssignedTo-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Uppdatera servicePrincipals.appRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Uppdatera grundläggande egenskaper för servicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Skapa servicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Ta bort servicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Uppdatera servicePrincipals.owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Uppdatera servicePrincipals.policies-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Hantera licenser för användare i Azure Active Directory. |
| microsoft.aad.reports/allEntities/read | Läsa Azure AD-rapporter. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="cloud-device-administrator"></a>Molnenhetsadministratör
Fullständig åtkomst för att hantera enheter i Azure AD.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft.aad.Directory/Devices/delete | Ta bort enheter i Azure Active Directory. |
| Microsoft.aad.Directory/Devices/disable | Inaktivera enheter i Azure Active Directory. |
| Microsoft.aad.Directory/Devices/enable | Konfigurera enheter i Azure Active Directory. |
| microsoft.aad.reports/allEntities/read | Läsa Azure AD-rapporter. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |

### <a name="company-administrator"></a>Företagsadministratör
Kan hantera alla aspekter av Azure AD och Microsoft-tjänster som använde Azure AD-identiteter.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från rollen.
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | Skapa och ta bort administrativeUnits och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/applications/allProperties/allTasks | Skapa och ta bort program, och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/allProperties/allTasks | Skapa och ta bort appRoleAssignments och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/contacts/allProperties/allTasks | Skapa och ta bort kontakter och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/contracts/allProperties/allTasks | Skapa och ta bort kontrakt, och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/devices/allProperties/allTasks | Skapa och ta bort enheter och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/allProperties/allTasks | Skapa och ta bort directoryRoles, och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/directoryRoleTemplates/allProperties/allTasks | Skapa och ta bort directoryRoleTemplates och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/domains/allProperties/allTasks | Skapa och ta bort domäner och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/groups/allProperties/allTasks | Skapa och ta bort grupper och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/groupSettings/allProperties/allTasks | Skapa och ta bort groupSettings, och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/allProperties/allTasks | Skapa och ta bort groupSettingTemplates, och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/loginTenantBranding/allProperties/allTasks | Skapa och ta bort loginTenantBranding, och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/allProperties/allTasks | Skapa och ta bort oAuth2PermissionGrants, och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/organization/allProperties/allTasks | Skapa och ta bort organisation, och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/policies/allProperties/allTasks | Skapa och ta bort principer och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | Skapa och ta bort roleAssignments och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Skapa och ta bort roleDefinitions och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/scopedRoleMemberships/allProperties/allTasks | Skapa och ta bort scopedRoleMemberships och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/serviceAction/activateService | Kan utföra åtgärden Activateservice service i Azure Active Directory |
| microsoft.aad.directory/serviceAction/disableDirectoryFeature | Kan utföra åtgärden Disabledirectoryfeature service i Azure Active Directory |
| microsoft.aad.directory/serviceAction/enableDirectoryFeature | Kan utföra åtgärden Enabledirectoryfeature service i Azure Active Directory |
| microsoft.aad.directory/serviceAction/getAvailableExtentionProperties | Kan utföra åtgärden Getavailableextentionproperties service i Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/allProperties/allTasks | Skapa och ta bort servicePrincipals och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/allProperties/allTasks | Skapa och ta bort subscribedSkus och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/users/allProperties/allTasks | Skapa och ta bort användare, och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Utföra alla åtgärder i Azure AD Connect. |
| microsoft.aad.identityProtection/allEntities/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera standardegenskaper i microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Läsa alla resurser i microsoft.aad.privilegedIdentityManagement. |
| microsoft.aad.reports/allEntities/allTasks | Läsa och konfigurera Azure AD-rapporter. |
| microsoft.azure.accessService/allEntities/allTasks | Hantera alla aspekter av Azure Access-tjänsten. |
| microsoft.azure.informationProtection/allEntities/allTasks | Hantera alla aspekter av Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| microsoft.commerce.billing/allEntities/allTasks | Hantera alla aspekter av Office 365-fakturering. |
| microsoft.intune/allEntities/allTasks | Hantera alla aspekter av Intune. |
| Microsoft.Office365.complianceManager/allEntities/allTasks | Hantera alla aspekter av Office 365-efterlevnad Manager |
| Microsoft.Office365.Exchange/allEntities/allTasks | Hantera alla aspekter av Exchange Online. |
| Microsoft.Office365.lockbox/allEntities/allTasks | Hantera alla aspekter av Office 365 Customer Lockbox |
| microsoft.powerApps.powerBI/allEntities/allTasks | Kan hantera alla aspekter av Power BI. |
| Microsoft.Office365.protectionCenter/allEntities/allTasks | Hantera alla aspekter av Office 365-Säkerhetscenter. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| Microsoft.Office365.SharePoint/allEntities/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera standardegenskaper i microsoft.office365.sharepoint. |
| Microsoft.Office365.skypeForBusiness/allEntities/allTasks | Hantera alla aspekter av Skype för företag Online. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Hantera alla aspekter av Dynamics 365. |

### <a name="compliance-administrator"></a>Efterlevnadsadministratör
Kan läsa och hantera efterlevnadskonfiguration och rapporter i Azure AD och Office 365.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Hantera alla aspekter av Azure Access-tjänsten. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| Microsoft.Office365.complianceManager/allEntities/allTasks | Hantera alla aspekter av Office 365-efterlevnad Manager |
| Microsoft.Office365.Exchange/allEntities/allTasks | Hantera alla aspekter av Exchange Online. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| Microsoft.Office365.SharePoint/allEntities/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera standardegenskaper i microsoft.office365.sharepoint. |
| Microsoft.Office365.skypeForBusiness/allEntities/allTasks | Hantera alla aspekter av Skype för företag Online. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="conditional-access-administrator"></a>Administratör för villkorsstyrd åtkomst
Kan hantera funktioner för villkorsstyrd åtkomst.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/policies/conditionalAccess/basic/read | Läsa policies.conditionalAccess-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/basic/update | Uppdatera policies.conditionalAccess-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/create | Skapa principer i Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/delete | Ta bort principer i Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/read | Läsa policies.conditionalAccess-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/update | Uppdatera policies.conditionalAccess-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/policiesAppliedTo/read | Läsa policies.conditionalAccess-egenskapen i Azure Active Directory. |

### <a name="crm-service-administrator"></a>Administratör för CRM-tjänst
Kan hantera alla aspekter av Dynamics 365-produkten.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Hantera alla aspekter av Azure Access-tjänsten. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Hantera alla aspekter av Dynamics 365. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="customer-lockbox-access-approver"></a>Godkännare av åtkomst till Customer LockBox
Kan godkänna förfrågningar till Microsoft Support om att få åtkomst till kundens organisationsdata.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Hantera alla aspekter av Azure Access-tjänsten. |
| Microsoft.Office365.lockbox/allEntities/allTasks | Hantera alla aspekter av Office 365 Customer Lockbox |

### <a name="device-administrators"></a>Enhetsadministratörer
Medlemmar i den här rollen har lagts till i gruppen lokala administratörer på Azure AD-anslutna enheter.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/groupSettings/basic/read | Läsa grundläggande egenskaper för groupSettings i Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Läsa grundläggande egenskaper för groupSettingTemplates i Azure Active Directory. |

### <a name="directory-readers"></a>Katalogläsare
Kan läsa grundläggande kataloginformation. För att bevilja åtkomst till program inte avsedd för användare.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från rollen.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/basic/read | Läs grundläggande egenskaper på administrativeUnits i Azure Active Directory. |
| microsoft.aad.directory/administrativeUnits/members/read | Läs administrativeUnits.members-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/Audience/Read | Läsa applications.audience-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/Authentication/Read | Läsa applications.authentication-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/Basic/Read | Läsa grundläggande egenskaper för program i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/Credentials/Read | Läsa applications.credentials-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/owners/Read | Läsa applications.owners-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/permissions/Read | Läsa applications.permissions-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/policies/Read | Läsa applications.policies-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/Basic/Read | Läs grundläggande egenskaper för kontakter i Azure Active Directory. |
| microsoft.aad.directory/contacts/memberOf/read | Läsa contacts.memberOf-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/contracts/Basic/Read | Läsa grundläggande egenskaper för kontrakt i Azure Active Directory. |
| Microsoft.aad.Directory/Devices/Basic/Read | Läs grundläggande egenskaper för enheter i Azure Active Directory. |
| microsoft.aad.directory/devices/memberOf/read | Läsa devices.memberOf-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/read | Läsa devices.registeredOwners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/read | Läsa devices.registeredUsers-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/basic/read | Läsa grundläggande egenskaper för directoryRoles i Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/eligibleMembers/read | Läsa directoryRoles.eligibleMembers-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/members/read | Läsa directoryRoles.members-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Domains/Basic/Read | Läs grundläggande egenskaper för domäner i Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/read | Läsa groups.appRoleAssignments-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Basic/Read | Läsa grundläggande egenskaper för grupper i Azure Active Directory. |
| microsoft.aad.directory/groups/memberOf/read | Läsa groups.memberOf-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/groups/members/Read | Läsa groups.members-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/groups/owners/Read | Läsa groups.owners-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Settings/Read | Läsa groups.settings-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/groupSettings/basic/read | Läsa grundläggande egenskaper för groupSettings i Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Läsa grundläggande egenskaper för groupSettingTemplates i Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/basic/read | Läsa grundläggande egenskaper för oAuth2PermissionGrants i Azure Active Directory. |
| Microsoft.aad.Directory/Organization/Basic/Read | Läsa grundläggande egenskaper för organisation i Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/read | Läsa organization.trustedCAsForPasswordlessAuth-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/roleAssignments/basic/read | Läsa grundläggande egenskaper för rolltilldelningar i Azure Active Directory. |
| microsoft.aad.directory/roleDefinitions/basic/read | Läsa grundläggande egenskaper för roleDefinitions i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Läsa servicePrincipals.appRoleAssignedTo-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Läsa servicePrincipals.appRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/read | Läsa grundläggande egenskaper för servicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Läsa servicePrincipals.memberOf-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Läsa servicePrincipals.oAuth2PermissionGrants-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Läsa servicePrincipals.ownedObjects-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Läsa servicePrincipals.owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Läsa servicePrincipals.policies-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/basic/read | Läsa grundläggande egenskaper för subscribedSkus i Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/read | Läsa users.appRoleAssignments-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Basic/Read | Läsa grundläggande egenskaper för användare i Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Läsa users.directReports-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/invitedBy/read | Läsa users.invitedBy-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/invitedUsers/read | Läsa users.invitedUsers-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Manager/Read | Läsa users.manager-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Läsa users.memberOf-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Läsa users.oAuth2PermissionGrants-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Läsa users.ownedDevices-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Läsa users.ownedObjects-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Läsa users.registeredDevices-egenskapen i Azure Active Directory. |

### <a name="directory-synchronization-accounts"></a>Konton för katalogen katalogsynkronisering
Endast används av Azure AD Connect-tjänsten.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från rollen.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/organization/dirSync/update | Uppdatera organization.dirSync-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/policies/Create | Skapa principer i Azure Active Directory. |
| Microsoft.aad.Directory/policies/delete | Ta bort principer i Azure Active Directory. |
| Microsoft.aad.Directory/policies/Basic/Read | Läs grundläggande egenskaper för principer i Azure Active Directory. |
| Microsoft.aad.Directory/policies/Basic/Update | Uppdatera grundläggande egenskaper för principer i Azure Active Directory. |
| Microsoft.aad.Directory/policies/owners/Read | Läsa policies.owners-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/policies/owners/Update | Uppdatera policies.owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/policies/policiesAppliedTo/read | Läsa policies.policiesAppliedTo-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Läsa servicePrincipals.appRoleAssignedTo-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Uppdatera servicePrincipals.appRoleAssignedTo-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Läsa servicePrincipals.appRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Uppdatera servicePrincipals.appRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/read | Läsa grundläggande egenskaper för servicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Uppdatera grundläggande egenskaper för servicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Skapa servicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Läsa servicePrincipals.memberOf-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Läsa servicePrincipals.oAuth2PermissionGrants-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Läsa servicePrincipals.owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Uppdatera servicePrincipals.owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Läsa servicePrincipals.ownedObjects-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Läsa servicePrincipals.policies-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Uppdatera servicePrincipals.policies-egenskapen i Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Utföra alla åtgärder i Azure AD Connect. |

### <a name="directory-writers"></a>Katalogskrivare
Kan läsa och skriva grundläggande kataloginformation. För att bevilja åtkomst till program inte avsedd för användare.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft.aad.Directory/groups/Create | Skapa grupper i Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Skapa grupper i Azure Active Directory. Skaparen läggs till som första ägare och det skapade objektet räknas mot Skaparens kvot på 250 skapade objekt. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Uppdatera groups.appRoleAssignments-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Basic/Update | Uppdatera grundläggande egenskaper för grupper i Azure Active Directory. |
| Microsoft.aad.Directory/groups/members/Update | Uppdatera groups.members-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/groups/owners/Update | Uppdatera groups.owners-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Settings/Update | Uppdatera groups.settings-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/groupSettings/basic/update | Uppdatera grundläggande egenskaper för groupSettings i Azure Active Directory. |
| microsoft.aad.directory/groupSettings/create | Skapa groupSettings i Azure Active Directory. |
| microsoft.aad.directory/groupSettings/delete | Ta bort groupSettings i Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Uppdatera users.appRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Hantera licenser för användare i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Basic/Update | Uppdatera grundläggande egenskaper för användare i Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidera alla tokens för användaruppdatering i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Manager/Update | Uppdatera users.manager-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Uppdatera users.userPrincipalName-egenskapen i Azure Active Directory. |

### <a name="exchange-service-administrator"></a>Exchange Service-administratör
Kan hantera alla aspekter av Exchange-produkten.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Hantera alla aspekter av Azure Access-tjänsten. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| Microsoft.Office365.Exchange/allEntities/allTasks | Hantera alla aspekter av Exchange Online. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="guest-inviter"></a>Gäst bjuder in
Kan bjuda in gästanvändare oberoende av inställningen medlemmar kan bjuda in gäster.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från rollen.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/users/appRoleAssignments/read | Läsa users.appRoleAssignments-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Basic/Read | Läsa grundläggande egenskaper för användare i Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Läsa users.directReports-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/invitedBy/read | Läsa users.invitedBy-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/inviteGuest | Bjud in gästanvändare i Azure Active Directory. |
| microsoft.aad.directory/users/invitedUsers/read | Läsa users.invitedUsers-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Manager/Read | Läsa users.manager-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Läsa users.memberOf-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Läsa users.oAuth2PermissionGrants-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Läsa users.ownedDevices-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Läsa users.ownedObjects-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Läsa users.registeredDevices-egenskapen i Azure Active Directory. |

### <a name="helpdesk-administrator"></a>Supportavdelningsadministratör
Kan återställa lösenord för icke-administratörer och Helpdeks-administratörer.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidera alla tokens för användaruppdatering i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Password/Update | Uppdatera lösenord för alla användare i Azure Active Directory. Se onlinedokumentationen för mer information. |
| microsoft.azure.accessService/allEntities/allTasks | Hantera alla aspekter av Azure Access-tjänsten. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="information-protection-administrator"></a>Information Protection-administratör
Kan hantera alla aspekter av Azure Information Protection-produkten.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Hantera alla aspekter av Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="intune-service-administrator"></a>Administratör för Intune-tjänsten
Kan hantera alla aspekter av Intune-produkten.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft.aad.Directory/Contacts/Basic/Update | Uppdatera grundläggande egenskaper för kontakter i Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/Create | Skapa kontakter i Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/delete | Ta bort kontakter i Azure Active Directory. |
| Microsoft.aad.Directory/Devices/Basic/Update | Uppdatera grundläggande egenskaper för enheter i Azure Active Directory. |
| Microsoft.aad.Directory/Devices/Create | Skapa enheter i Azure Active Directory. |
| Microsoft.aad.Directory/Devices/delete | Ta bort enheter i Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/update | Uppdatera devices.registeredOwners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/update | Uppdatera devices.registeredUsers-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Uppdatera groups.appRoleAssignments-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Basic/Update | Uppdatera grundläggande egenskaper för grupper i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Create | Skapa grupper i Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Skapa grupper i Azure Active Directory. Skaparen läggs till som första ägare och det skapade objektet räknas mot Skaparens kvot på 250 skapade objekt. |
| Microsoft.aad.Directory/groups/delete | Ta bort grupper i Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Läsa groups.hiddenMembers-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/groups/members/Update | Uppdatera groups.members-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/groups/owners/Update | Uppdatera groups.owners-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Restore | Återställa grupper i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Settings/Update | Uppdatera groups.settings-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Uppdatera users.appRoleAssignments-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Basic/Update | Uppdatera grundläggande egenskaper för användare i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Manager/Update | Uppdatera users.manager-egenskapen i Azure Active Directory. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| microsoft.intune/allEntities/allTasks | Hantera alla aspekter av Intune. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="license-administrator"></a>Licensadministratör
Kan hantera produktlicenser på användare och grupper.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/users/assignLicense | Hantera licenser för användare i Azure Active Directory. |
| microsoft.aad.directory/users/usageLocation/update | Uppdatera users.usageLocation-egenskapen i Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Hantera alla aspekter av Azure Access-tjänsten. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |

### <a name="lync-service-administrator"></a>Lync-tjänstadministratör
Kan hantera alla aspekter av Skype för Business-produkten.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Hantera alla aspekter av Azure Access-tjänsten. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| Microsoft.Office365.skypeForBusiness/allEntities/allTasks | Hantera alla aspekter av Skype för företag Online. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="message-center-reader"></a>Meddelandecenterläsare
Kan endast läsa meddelanden och uppdateringar avseende sin organisation i meddelandecenter för Office 365. 

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.accessmessagecenter/allEntities/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera standardegenskaper i meddelandecenter. |
| microsoft.azure.accessService/allEntities/allTasks | Hantera alla aspekter av Azure Access-tjänsten. |

### <a name="partner-tier1-support"></a>Partnersupport, nivå 1
Använd inte – inte avsett för allmänt bruk.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft.aad.Directory/Contacts/Basic/Update | Uppdatera grundläggande egenskaper för kontakter i Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/Create | Skapa kontakter i Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/delete | Ta bort kontakter i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Create | Skapa grupper i Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Skapa grupper i Azure Active Directory. Skaparen läggs till som första ägare och det skapade objektet räknas mot Skaparens kvot på 250 skapade objekt. |
| Microsoft.aad.Directory/groups/members/Update | Uppdatera groups.members-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/groups/owners/Update | Uppdatera groups.owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Uppdatera users.appRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Hantera licenser för användare i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Basic/Update | Uppdatera grundläggande egenskaper för användare i Azure Active Directory. |
| Microsoft.aad.Directory/Users/delete | Ta bort användare i Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidera alla tokens för användaruppdatering i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Manager/Update | Uppdatera users.manager-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Password/Update | Uppdatera lösenord för alla användare i Azure Active Directory. Se onlinedokumentationen för mer information. |
| Microsoft.aad.Directory/Users/Restore | Återställa borttagna användare i Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Uppdatera users.userPrincipalName-egenskapen i Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Hantera alla aspekter av Azure Access-tjänsten. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="partner-tier2-support"></a>Partnersupport, nivå 2
Använd inte – inte avsett för allmänt bruk.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft.aad.Directory/Contacts/Basic/Update | Uppdatera grundläggande egenskaper för kontakter i Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/Create | Skapa kontakter i Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/delete | Ta bort kontakter i Azure Active Directory. |
| microsoft.aad.directory/domains/allTasks | Skapa och ta bort domäner och läsa och uppdatera standardegenskaper i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Create | Skapa grupper i Azure Active Directory. |
| Microsoft.aad.Directory/groups/delete | Ta bort grupper i Azure Active Directory. |
| Microsoft.aad.Directory/groups/members/Update | Uppdatera groups.members-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Restore | Återställa grupper i Azure Active Directory. |
| Microsoft.aad.Directory/Organization/Basic/Update | Uppdatera grundläggande egenskaper på organisation i Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/update | Uppdatera organization.trustedCAsForPasswordlessAuth-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Uppdatera users.appRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Hantera licenser för användare i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Basic/Update | Uppdatera grundläggande egenskaper för användare i Azure Active Directory. |
| Microsoft.aad.Directory/Users/delete | Ta bort användare i Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidera alla tokens för användaruppdatering i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Manager/Update | Uppdatera users.manager-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Password/Update | Uppdatera lösenord för alla användare i Azure Active Directory. Se onlinedokumentationen för mer information. |
| Microsoft.aad.Directory/Users/Restore | Återställa borttagna användare i Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Uppdatera users.userPrincipalName-egenskapen i Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Hantera alla aspekter av Azure Access-tjänsten. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="power-bi-service-administrator"></a>Power BI-tjänstadministratör
Kan hantera alla aspekter av Power BI-produkten.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Hantera alla aspekter av Azure Access-tjänsten. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Kan hantera alla aspekter av Power BI. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="privileged-role-administrator"></a>Privilegierad rolladministratör
Kan hantera rolltilldelningar i Azure AD och alla aspekter av Privileged Identity Management.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/directoryRoles/update | Uppdatera directoryRoles i Azure Active Directory. |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera standardegenskaper i microsoft.aad.privilegedIdentityManagement. |

### <a name="reports-reader"></a>Rapportläsare
Kan läsa inloggnings- och granskningsrapporter.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.reports/allEntities/read | Läsa Azure AD-rapporter. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| Microsoft.Office365.usageReports/allEntities/Read | Läs Office 365-användningsrapporter. |

### <a name="security-administrator"></a>Säkerhetsadministratör
Kan läsa säkerhetsinformation och rapporter och hantera konfigurationen i Azure AD och Office 365.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft.aad.Directory/Applications/policies/Update | Uppdatera applications.policies-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/policies/Basic/Update | Uppdatera grundläggande egenskaper för principer i Azure Active Directory. |
| Microsoft.aad.Directory/policies/Create | Skapa principer i Azure Active Directory. |
| Microsoft.aad.Directory/policies/delete | Ta bort principer i Azure Active Directory. |
| Microsoft.aad.Directory/policies/owners/Update | Uppdatera policies.owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Uppdatera servicePrincipals.policies-egenskapen i Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Läsa alla resurser i microsoft.aad.identityProtection. |
| microsoft.aad.identityProtection/allEntities/update | Uppdatera alla resurser i microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Läsa alla resurser i microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.accessService/allEntities/allTasks | Hantera alla aspekter av Azure Access-tjänsten. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| Microsoft.Office365.protectionCenter/allEntities/Read | Läsa alla aspekter av Office 365-säkerhetscenter. |
| Microsoft.Office365.protectionCenter/allEntities/Update | Uppdatera alla resurser i microsoft.office365.protectionCenter. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |

### <a name="security-reader"></a>Säkerhetsläsare
Kan läsa säkerhetsinformation och rapporter i Azure AD och Office 365.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.identityProtection/allEntities/read | Läsa alla resurser i microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Läsa alla resurser i microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.accessService/allEntities/allTasks | Hantera alla aspekter av Azure Access-tjänsten. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| Microsoft.Office365.protectionCenter/allEntities/Read | Läsa alla aspekter av Office 365-säkerhetscenter. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |

### <a name="service-support-administrator"></a>Tjänstesupport-administratör
Kan läsa information om Service Health och hantera supportärenden.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Hantera alla aspekter av Azure Access-tjänsten. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="sharepoint-service-administrator"></a>SharePoint-tjänstadministratör
Kan hantera alla aspekter av SharePoint-tjänsten.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Hantera alla aspekter av Azure Access-tjänsten. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| Microsoft.Office365.SharePoint/allEntities/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera standardegenskaper i microsoft.office365.sharepoint. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="teams-communications-administrator"></a>Teams kommunikation administratör
Kan hantera anropa och möten funktioner i Microsoft Teams-tjänst.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft.aad.Directory/policies/Basic/Read | Läs grundläggande egenskaper för principer i Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Hantera alla aspekter av Azure Access-tjänsten. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |
| Microsoft.Office365.usageReports/allEntities/Read | Läs Office 365-användningsrapporter. |

### <a name="teams-communications-support-engineer"></a>Teams kommunikation supporttekniker
Kan felsöka problem med kommunikation i grupper med hjälp av avancerade verktyg.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft.aad.Directory/policies/Basic/Read | Läs grundläggande egenskaper för principer i Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Hantera alla aspekter av Azure Access-tjänsten. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |

### <a name="teams-communications-support-specialist"></a>Teams kommunikation Support Specialist
Kan felsöka problem med kommunikation i Teams med hjälp av grundläggande verktyg.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft.aad.Directory/policies/Basic/Read | Läs grundläggande egenskaper för principer i Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Hantera alla aspekter av Azure Access-tjänsten. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |

### <a name="teams-service-administrator"></a>Team-tjänstadministratör
Hantera Microsoft Teams-tjänsten.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/groups/hiddenMembers/read | Läsa groups.hiddenMembers-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/policies/Basic/Read | Läs grundläggande egenskaper för principer i Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Hantera alla aspekter av Azure Access-tjänsten. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |
| Microsoft.Office365.usageReports/allEntities/Read | Läs Office 365-användningsrapporter. |

### <a name="user-account-administrator"></a>Användarkonto-administratör
Kan hantera alla aspekter av användare och grupper, inklusive återställning av lösenord för begränsade administratörer.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/appRoleAssignments/create | Skapa appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Ta bort appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Uppdatera appRoleAssignments i Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/Basic/Update | Uppdatera grundläggande egenskaper för kontakter i Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/Create | Skapa kontakter i Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/delete | Ta bort kontakter i Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Uppdatera groups.appRoleAssignments-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Basic/Update | Uppdatera grundläggande egenskaper för grupper i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Create | Skapa grupper i Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Skapa grupper i Azure Active Directory. Skaparen läggs till som första ägare och det skapade objektet räknas mot Skaparens kvot på 250 skapade objekt. |
| Microsoft.aad.Directory/groups/delete | Ta bort grupper i Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Läsa groups.hiddenMembers-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/groups/members/Update | Uppdatera groups.members-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/groups/owners/Update | Uppdatera groups.owners-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Restore | Återställa grupper i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Settings/Update | Uppdatera groups.settings-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Uppdatera users.appRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Hantera licenser för användare i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Basic/Update | Uppdatera grundläggande egenskaper för användare i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Create | Skapa användare i Azure Active Directory. |
| Microsoft.aad.Directory/Users/delete | Ta bort användare i Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidera alla tokens för användaruppdatering i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Manager/Update | Uppdatera users.manager-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Password/Update | Uppdatera lösenord för alla användare i Azure Active Directory. Se onlinedokumentationen för mer information. |
| Microsoft.aad.Directory/Users/Restore | Återställa borttagna användare i Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Uppdatera users.userPrincipalName-egenskapen i Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Hantera alla aspekter av Azure Access-tjänsten. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |


## <a name="deprecated-roles"></a>Föråldrad roller

Följande roller ska inte användas. De har blivit inaktuella och tas bort från Azure AD i framtiden.

* Ad hoc-licensadministratör
* Enhetskoppling
* Enhetshanterare
* Användare av enheter
* E-postverifierad användarskapare
* Postlådeadministratör
* Arbetsplatsenhetskoppling

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du tilldelar en användare som administratör för en Azure-prenumeration finns [hantera åtkomst med RBAC och Azure portal](../../role-based-access-control/role-assignments-portal.md)
* Mer information om hur resursåtkomsten hanteras i Microsoft Azure finns i [Förstå resursåtkomst i Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Mer information om hur Azure Active Directory relaterar till din Azure-prenumeration finns i [Hur Azure-prenumerationer är associerade med Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
