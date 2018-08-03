---
title: Tilldela administratörsroller i Azure Active Directory | Microsoft Docs
description: En administratörsroll kan lägga till användare, tilldela administrativa roller, återställa användarlösenord, hantera användarlicenser eller hantera domäner. En användare som är tilldelad en administratörsroll har samma behörigheter i alla molntjänster som din organisation prenumererar.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 07/25/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: f5d53b75b8cc47fc8405a334ae9af32faa67a439
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39481061"
---
# <a name="assigning-administrator-roles-in-azure-active-directory"></a>Tilldela administratörsroller i Azure Active Directory

Med Azure Active Directory (Azure AD) kan ange du separata administratörer att hantera olika funktioner. Administratörer kan utses i Azure AD-portalen för att utföra åtgärder som att lägga till eller ändra användare, tilldela administrativa roller, återställa användarlösenord, hantera användarlicenser och hantera domännamn.

## <a name="details-about-the-global-administrator-role"></a>Information om rollen global administratör

Globala administratörer har åtkomst till alla administrativa funktioner. Som standard tilldelas den person som registrerar sig för en Azure-prenumeration rollen som global administratör för katalogen. Endast globala administratörer kan tilldela andra administratörsroller.

## <a name="assign-or-remove-administrator-roles"></a>Tilldela eller ta bort administratörsroller

Om du vill lära dig mer om att tilldela administrativa roller till en användare i Azure Active Directory, se [tilldela en användare till administratörsroller i Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="available-roles"></a>Tillgängliga roller

Följande administratörsroller är tillgängliga:

* **[Programadministratör](#application-administrator)**: användare i den här rollen kan skapa och hantera alla aspekter av företagsprogram och programregistreringar proxyinställningarna för programmet. Den här rollen ger även möjlighet att godkänna delegerade behörigheter och programbehörigheter exklusive Microsoft Graph och Azure AD Graph. Medlemmar i den här rollen har inte lagts som ägare när du skapar nya programregistreringar eller företagsprogram.

* **[Programutvecklare](#application-developer)**: användare i den här rollen kan skapa programregistreringar när den ”användare kan registrera program” är inställt på Nej. Den här rollen kan också medlemmar måste samtycka på sina egna räkning när den ”användare kan godkänna att appar får åtkomst till företagets data å deras vägnar” är inställt på Nej. Medlemmar i den här rollen har lagts till som ägare när du skapar nya programregistreringar eller företagsprogram.

* **[Faktureringsadministratör](#billing-administrator)**: gör inköp, hanterar prenumerationer, hanterar supportärenden och övervakar tjänstehälsa.

* **[Molnprogramadministratör](#cloud-application-administrator)**: användare i den här rollen har samma behörigheter som rollen programadministratör exklusive möjligheten att hantera programproxy. Den här rollen ger möjlighet att skapa och hantera alla aspekter av företagsprogram och programregistreringar. Den här rollen ger även möjlighet att godkänna delegerade behörigheter och programbehörigheter exklusive Microsoft Graph och Azure AD Graph. Medlemmar i den här rollen har inte lagts som ägare när du skapar nya programregistreringar eller företagsprogram.

* **[Efterlevnadsadministratör](#compliance-administrator)**: användare med den här rollen har hanteringsbehörigheter inom Office 365 Security & Compliance Center och administrationscentret för Exchange. Mer information på [om Office 365-administratörsroller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Administratör för villkorsstyrd åtkomst](#conditional-access-administrator)**: användare med den här rollen har behörighet att hantera inställningar för villkorlig åtkomst i Azure Active Directory.
  > [!NOTE]
  > Om du vill distribuera Exchange ActiveSync villkorsstyrd åtkomstprincip i Azure, måste användaren också vara en Global administratör.
  
* **[Enhetsadministratörer](#device-administrators)**: den här rollen är tilldelas endast som en ytterligare lokal administratör i [Enhetsinställningar](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Användare med den här rollen blir administratörer för den lokala datorn på alla Windows 10-enheter som är anslutna till Azure Active Directory. De har inte behörighet att hantera enheters objekt i Azure Active Directory. 

* **[Katalogläsare](#directory-readers)**: det här är en äldre roll som ska tilldelas till program som inte stöder den [godkänna Framework](../develop/active-directory-integrating-applications.md). Det ska inte tilldelas alla användare.

* **[Konton för katalogsynkronisering](#directory-synchronization-accounts)**: Använd inte. Den här rollen är tilldelas automatiskt till Azure AD Connect-tjänsten och inte avsedd eller stöd för annan användning.

* **[Katalogskrivare](#directory-writers)**: det här är en äldre roll som ska tilldelas till program som inte stöder den [godkänna Framework](../develop/active-directory-integrating-applications.md). Det ska inte tilldelas alla användare.

* **[Dynamics 365-tjänstadministratör / CRM-tjänstadministratör](#dynamics-365-service-administrator)**: användare med den här rollen har globala behörigheter inom Microsoft Dynamics 365 Online när tjänsten finns närvarande, liksom möjlighet att hantera supportbegäranden och Kontrollera tjänstens hälsotillstånd. Mer information på [använda administratörsrollen för att hantera din klient](https://docs.microsoft.com/en-us/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

* **[Exchange-tjänstadministratör](#exchange-service-administrator)**: användare med den här rollen har globala behörigheter inom Microsoft Exchange Online när tjänsten finns. Mer information på [om Office 365-administratörsroller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Global administratör / företagsadministratör](#company-administrator)**: användare med den här rollen har åtkomst till alla administrativa funktioner i Azure Active Directory, samt tjänster som använder Azure Active Directory-identiteter som Exchange Online SharePoint Online och Skype för företag Online. Den person som registrerar sig för Azure Active Directory-klient blir global administratör. Endast globala administratörer kan tilldela andra administratörsroller. Det kan finnas mer än en global administratör i företaget. Globala administratörer kan återställa lösenordet för alla användare och alla andra administratörer.

  > [!NOTE]
  > I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras rollen som ”företagets administratör”. Det är ”Global administratör” i den [Azure-portalen](https://portal.azure.com).
  >
  >

* **[Gäst bjuder in](#guest-inviter)**: användare i den här rollen kan hantera inbjudningar för Azure Active Directory B2B gäst när den **medlemmar kan bjuda in** användaren är inställt på Nej. Mer information om B2B-samarbete i [om Azure AD B2B-samarbete](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Den omfattar inte några andra behörigheter.

* **[Information Protection-administratör](#information-protection-administrator)**: användare med den här rollen har alla behörigheter i Azure Information Protection-tjänsten. Den här rollen kan konfigurera etiketter för Azure Information Protection-principen, hantera skyddsmallar och aktivera skydd. Den här rollen ger inte några behörigheter i Identity Protection Center, Privileged Identity Management, övervaka Office 365 Service Health eller Office 365 säkerhets- och Efterlevnadscenter.

* **[Intune-tjänstadministratör](#intune-service-administrator)**: användare med den här rollen har globala behörigheter inom Microsoft Intune Online när tjänsten finns. Dessutom innehåller den här rollen möjlighet att hantera användare och enheter för att associera principer, samt skapa och hantera grupper. Mer information på [rollbaserad administrationskontroll (RBAC) med Microsoft Intune](https://docs.microsoft.com/en-us/intune/role-based-access-control)

* **[Message Center läsare](#message-center-reader)**: användare i den här rollen kan övervaka meddelanden och rådgivande hälsotillstånd uppdateringar i [Office 365-meddelandecentret](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) för organisationen på konfigurerade tjänster, till exempel Exchange, Intune och Microsoft Teams. Meddelandecenter läsarna få veckovis e-sammandrag av inlägg, uppdateringar, och kan dela center meddelandeposter i Office 365. I Azure AD har användare som tilldelats den här rollen endast läsåtkomst på Azure AD-tjänster, till exempel användare och grupper. 

* **[Partner nivå 1-Support](#partner-tier1-support)**: Använd inte. Den här rollen har gjorts inaktuell och kommer att tas bort från Azure AD i framtiden. Den här rollen är avsedd att användas av ett litet antal Microsoft återförsäljning-partner och är inte avsedd för allmänt bruk.

* **[Partner – nivå 2-stöd](#partner-tier2-support)**: Använd inte. Den här rollen har gjorts inaktuell och kommer att tas bort från Azure AD i framtiden. Den här rollen är avsedd att användas av ett litet antal Microsoft återförsäljning-partner och är inte avsedd för allmänt bruk.

* **[Lösenordsadministratör / Supportavdelningsadministratör](#helpdesk-administrator)**: användare med den här rollen kan ändra lösenord, hanterar tjänstbegäranden och övervakar tjänstehälsa. Supportadministratörer kan endast ändra lösenord för användare och andra supportadministratörer. 

  > [!NOTE]
  > I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras rollen som ”Supportavdelningsadministratör”. Det är ”Lösenordsadministratör” i den [Azure-portalen](https://portal.azure.com/).
  >
  >
  
* **[Power BI-tjänstadministratör](#power-bi-service-administrator)**: användare med den här rollen har globala behörigheter inom Microsoft Power BI när tjänsten finns närvarande, liksom möjlighet att hantera supportbegäranden och kontrollera tjänstens hälsotillstånd. Mer information på [förstå administratörsrollen för Power BI](https://docs.microsoft.com/en-us/power-bi/service-admin-role).

* **[Privilegierad Rolladministratör](#privileged-role-administrator)**: användare med den här rollen kan hantera rolltilldelningar i Azure Active Directory, liksom i Azure AD Privileged Identity Management. Den här rollen kan dessutom hantera alla aspekter av Privileged Identity Management.

* **[Rapporterar läsare](#reports-reader)**: användare med den här rollen kan visa användning reporting data och instrumentpanelen rapporter i Administrationscenter för Office 365 och införande kontexten hanteringspaket i Power BI. Dessutom rollen ger åtkomst till inloggnings-rapporter och aktivitet i Azure AD och data som returneras av Microsoft Graph rapporterings-API. En användare som tilldelats rollen rapportläsare kan komma åt endast relevanta användnings- och användningsstatistik. De har inte någon administratörsbehörighet för att konfigurera inställningar eller åtkomst produkten specifika administrationscentret som Exchange. 

* **[Säkerhetsadministratör](#security-administrator)**: användare med den här rollen har alla skrivskyddade behörigheter för rollen säkerhetsläsare, plus möjligheten att hantera konfigurationen för säkerhetsrelaterade tjänster: Azure Active Directory Identity Protection, Azure Information Protection, Privileged Identity Management, och Office 365 säkerhets- och Efterlevnadscenter. Mer information om behörigheter för Office 365 finns på [behörigheter i Office 365 säkerhets- och Efterlevnadscenter](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).
  
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

* **[Skype för företag – / Lync-tjänstadministratör](#lync-service-administrator)**: användare med den här rollen har globala behörigheter inom Microsoft Skype för företag när tjänsten finns närvarande, samt hantera Skype-specifika användarattribut i Azure Active Katalogen. Den här rollen ger dessutom möjlighet att hantera supportbegäranden och kontrollera tjänstens hälsotillstånd. Mer information på [om the Skype för företag-administratörsroll](https://support.office.com/en-us/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5).

  > [!NOTE]
  > I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras rollen som ”Lync-tjänstadministratör”. Det är ”Skype för företag tjänstadministratör” i den [Azure-portalen](https://portal.azure.com/).
  >
  >

* **[Användarkontoadministratören](#user-account-administrator)**: användarna med den här rollen kan skapa och hantera alla aspekter av användare och grupper. Dessutom innehåller den här rollen möjlighet att hantera supportbegäranden och kontrollera tjänstens hälsotillstånd. Vissa begränsningar gäller. Den här rollen tillåter exempelvis inte att ta bort en global administratör. Användarkontoadministratörer kan ändra lösenord för användare, supportadministratörer och andra användaradministratörer endast.

| Kan göra | Det går inte att göra |
| --- | --- |
| <p>Visa företaget och användaren information</p><p>Hantera supportärenden för Office</p><p>Ändra lösenord för användare, supportadministratörer och andra användaradministratörer endast</p><p>Skapa och hantera användarvyer</p><p>Skapa, redigera och ta bort användare och grupper och hantera användarlicenser, med begränsningar. Han eller hon kan inte ta bort en global administratör eller skapa andra administratörer.</p> |<p>Utföra åtgärder för fakturering och inköp för Office-produkter</p><p>Hantera domäner</p><p>Hantera företagsinformation</p><p>Delegera administrativa roller till andra</p><p>Använda katalogsynkronisering</p><p>Aktivera eller inaktivera multifaktorautentisering</p><p>Visa granskningsloggar</p> |

## <a name="deprecated-roles"></a>Föråldrad roller

Följande roller ska inte användas. De har inaktuella och tas bort från Azure AD i framtiden.

* Ad hoc-licensadministratör
* Enhetskoppling
* Enhetshanterare
* Användare av enheter
* E-postverifierad användarskapare
* Postlådeadministratör
* Arbetsplatsenhetskoppling


### <a name="to-add-a-colleague-as-a-global-administrator"></a>Att lägga till en kollega som global administratör

1. Logga in på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com) med ett konto som är en global administratör eller Privilegierade roller för klientkatalog.

   ![Öppna azure AD-administratörscenter](./media/directory-assign-admin-roles/active-directory-admin-center.png)

2. Välj **användare**.

3. Sök efter den användare som du vill utse som global administratör och öppna bladet för den användaren.

4. På användarbladet väljer **katalogroll**.
 
5. På bladet directory roll väljer du den **Global administratör** roll, och spara.

## <a name="detailed-azure-active-directory-permissions"></a>Detaljerad Azure Active Directory-behörigheter
I följande tabeller beskrivs de särskilda behörigheterna i Azure Active Directory för varje roll. Vissa roller, som Global administratör kan ha ytterligare behörigheter i Microsoft services outide av Azure Active Directory.


### <a name="application-administrator"></a>Programadministratör
Kan skapa och hantera alla aspekter av appregistreringar och enterprise-appar.

  > [!NOTE]
  > Den här rollen ärver ytterligare behörigheter från den [användarrollen](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/Application/Create | Skapa program i Azure Active Directory. |
| microsoft.aad.directory/Application/Delete | Ta bort program i Azure Active Directory. |
| microsoft.aad.directory/Application/Update | Uppdatera standardegenskaper för program i Azure Active Directory. |
| microsoft.aad.directory/Application/Update/DefaultPolicy | Uppdatera Applications.DefaultPolicy-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Application/Update/Owners | Uppdatera Applications.Owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Create | Skapa AppRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Delete | Ta bort AppRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Update | Uppdatera standardegenskaper på AppRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/Policy/Create | Skapa principer i Azure Active Directory. |
| microsoft.aad.directory/Policy/Delete | Ta bort principer i Azure Active Directory. |
| microsoft.aad.directory/Policy/Update | Uppdatera standardegenskaper för principer i Azure Active Directory. |
| microsoft.aad.directory/Policy/Update/Owners | Uppdatera Policies.Owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllNoDirectory | Kan ge samtycke åt alla användare till alla resurser förutom Azure Active Directory (Azure AD Graph och Microsoft Graph). |
| microsoft.aad.directory/ServicePrincipal/Create | Skapa ServicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Delete | Ta bort ServicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update | Uppdatera standardegenskaper för ServicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | Uppdatera ServicePrincipals.AppRoleAssignedTo-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | Uppdatera ServicePrincipals.AppRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Uppdatera ServicePrincipals.DefaultPolicy-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | Uppdatera ServicePrincipals.Owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Hantera licenser för användare i Azure Active Directory. |
| microsoft.aad.reports/AllEntities/Read | Läsa Azure AD-rapporter. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Läsa och konfigurera Office 365 Service Health. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Skapa och hantera Office 365-supportbegäranden. |

### <a name="application-developer"></a>Programutvecklare
Kan skapa programregistreringar oberoende av den **användare kan registrera program** inställningen.

  > [!NOTE]
  > Den här rollen ärver ytterligare behörigheter från den [användarrollen](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/Application/CreateAsOwner | Skapa program i Azure Active Directory. Skaparen läggs till som första ägare och det skapade objektet räknas mot Skaparens kvot på 250 skapade objekt. |
| microsoft.aad.directory/AppRoleAssignment/CreateAsOwner | Skapa AppRoleAssignments i Azure Active Directory. Skaparen läggs till som första ägare och det skapade objektet räknas mot Skaparens kvot på 250 skapade objekt. |
| microsoft.aad.directory/OAuth2PermissionGrant/CreateAsOwner | Skapa OAuth2PermissionGrants i Azure Active Directory. Skaparen läggs till som första ägare och det skapade objektet räknas mot Skaparens kvot på 250 skapade objekt. |
| microsoft.aad.directory/ServicePrincipal/CreateAsOwner | Skapa ServicePrincipals i Azure Active Directory. Skaparen läggs till som första ägare och det skapade objektet räknas mot Skaparens kvot på 250 skapade objekt. |

### <a name="billing-administrator"></a>Faktureringsadministratör
Kan utföra vanliga faktureringsrelaterade uppgifter som uppdatering av betalningsinformation.

  > [!NOTE]
  > Den här rollen ärver ytterligare behörigheter från den [användarrollen](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Se Rollbeskrivning ovan för mer information.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Läs Organizations.TrustedCAsForPasswordlessAuth-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Organization/Update | Uppdatera standardegenskaper för organisationer i Azure Active Directory. |
| microsoft.aad.directory/Organization/Update/TrustedCAsForPasswordlessAuth | Uppdatera Organizations.TrustedCAsForPasswordlessAuth-egenskapen i Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Skapa och ta bort alla resurser och läsa och uppdatera standardegenskaper i Azure Access Control. |
| microsoft.aad.billing/AllEntities/AllActions | Hantera alla aspekter av Office 365-fakturering. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Läsa och konfigurera Office 365 Service Health. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Skapa och hantera Office 365-supportbegäranden. |

### <a name="cloud-application-administrator"></a>Molnprogramadministratör
Kan skapa och hantera alla aspekter av appregistreringar och enterprise-appar förutom App Proxy.

  > [!NOTE]
  > Den här rollen ärver ytterligare behörigheter från den [användarrollen](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/Application/Create | Skapa program i Azure Active Directory. |
| microsoft.aad.directory/Application/Delete | Ta bort program i Azure Active Directory. |
| microsoft.aad.directory/Application/Update | Uppdatera standardegenskaper för program i Azure Active Directory. |
| microsoft.aad.directory/Application/Update/DefaultPolicy | Uppdatera Applications.DefaultPolicy-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Application/Update/Owners | Uppdatera Applications.Owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Create | Skapa AppRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Delete | Ta bort AppRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Update | Uppdatera standardegenskaper på AppRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/Policy/Create | Skapa principer i Azure Active Directory. |
| microsoft.aad.directory/Policy/Delete | Ta bort principer i Azure Active Directory. |
| microsoft.aad.directory/Policy/Update | Uppdatera standardegenskaper för principer i Azure Active Directory. |
| microsoft.aad.directory/Policy/Update/Owners | Uppdatera Policies.Owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllNoDirectory | Kan ge samtycke åt alla användare till alla resurser förutom Azure Active Directory (Azure AD Graph och Microsoft Graph). |
| microsoft.aad.directory/ServicePrincipal/Create | Skapa ServicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Delete | Ta bort ServicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update | Uppdatera standardegenskaper för ServicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | Uppdatera ServicePrincipals.AppRoleAssignedTo-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | Uppdatera ServicePrincipals.AppRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Uppdatera ServicePrincipals.DefaultPolicy-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | Uppdatera ServicePrincipals.Owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Hantera licenser för användare i Azure Active Directory. |
| microsoft.aad.reports/AllEntities/Read | Läsa Azure AD-rapporter. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Läsa och konfigurera Office 365 Service Health. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Skapa och hantera Office 365-supportbegäranden. |

### <a name="company-administrator"></a>Företagsadministratör
Kan hantera alla aspekter av Azure AD och Microsoft-tjänster som använde Azure AD-identiteter. I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras rollen som ”företagets administratör”. Det är ”Global administratör” i den [Azure-portalen](https://portal.azure.com).

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Se Rollbeskrivning ovan för mer information.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/AdministrativeUnit/AllActions/AllProperties | Skapa och ta bort AdministrativeUnits och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/Application/AllActions/AllProperties | Skapa och ta bort program och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/AllActions/AllProperties | Skapa och ta bort AppRoleAssignments och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/CollaborationSpace/AllActions/AllProperties | Skapa och ta bort CollaborationSpaces och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/Contact/AllActions/AllProperties | Skapa och ta bort kontakter och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/Device/AllActions/AllProperties | Skapa och ta bort enheter och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/DirectoryRole/AllActions/AllProperties | Skapa och ta bort DirectoryRoles och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/DirectoryRoleTemplate/AllActions/AllProperties | Skapa och ta bort DirectoryRoleTemplates och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/DirectorySetting/AllActions/AllProperties | Skapa och ta bort DirectorySettings och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/DirectorySettingTemplate/AllActions/AllProperties | Skapa och ta bort DirectorySettingTemplates och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/Domain/AllActions/AllProperties | Skapa och ta bort domäner och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/Group/AllActions/AllProperties | Skapa och ta bort grupper och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/LoginTenantBranding/AllActions/AllProperties | Skapa och ta bort LoginTenantBrandings och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/OAuth2PermissionGrant/AllActions/AllProperties | Skapa och ta bort OAuth2PermissionGrants och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/Policy/AllActions/AllProperties | Skapa och ta bort principer och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllWithDirectory | Kan ge samtycke åt alla användare till alla resurser, inklusive Azure Active Directory (Azure AD Graph och Microsoft Graph). |
| microsoft.aad.directory/ServicePrincipal/AllActions/AllProperties | Skapa och ta bort ServicePrincipals och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/Organization/AllActions/AllProperties | Skapa och ta bort Organizations och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/User/AllActions/AllProperties | Skapa och ta bort användare och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.aadconnect/AllEntities/AllActions | Skapa och ta bort alla resurser och läs och uppdatera standardegenskaperna i microsoft.aad.aadconnect. |
| microsoft.aad.accessservice/AllEntities/AllActions | Skapa och ta bort alla resurser och läsa och uppdatera standardegenskaper i Azure Access Control. |
| microsoft.aad.billing/AllEntities/AllActions | Hantera alla aspekter av Office 365-fakturering. |
| microsoft.aad.compliance/AllEntities/AllActions | Skapa och ta bort alla resurser och läsa och uppdatera standardegenskaper efter Efterlevnadscenter. |
| microsoft.aad.directorysync/AllEntities/AllActions | Utföra alla åtgärder i Azure AD Connect. |
| microsoft.aad.lockbox/AllEntities/AllActions | Hantera alla aspekter av Lockbox-tjänsten. |
| microsoft.aad.privilegedrolemanagement/AllEntities/AllActions | Hantera alla aspekter av tjänsten för hantering av privilegierad roll. |
| microsoft.aad.reports/AllEntities/AllActions | Läsa och konfigurera Azure AD-rapporter. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Läsa och konfigurera Office 365 Service Health. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Skapa och hantera Office 365-supportbegäranden. |
| microsoft.crm/AllEntities/AllActions | Hantera alla aspekter av Dynamics 365. |
| microsoft.exchange/AllEntities/AllActions | Hantera alla aspekter av Exchange Online. |
| microsoft.aad.informationprotection/AllEntities/AllActions | Hantera alla aspekter av Information Protection. |
| microsoft.intune/AllEntities/AllActions | Hantera alla aspekter av Intune. |
| microsoft.powerbi/AllEntities/AllActions | Kan hantera alla aspekter av Power BI. |
| microsoft.protectioncenter/AllEntities/AllActions | Hantera Office 365-säkerhetscenter. |
| microsoft.sharepoint/AllEntities/AllActions | Hantera SharePoint Online. |
| microsoft.skypeforbusiness/AllEntities/AllActions | Hantera Skype för företag – Online. |

### <a name="compliance-administrator"></a>Efterlevnadsadministratör
Kan läsa och hantera efterlevnadskonfiguration och rapporter i Azure AD och Office 365.

  > [!NOTE]
  > Den här rollen ärver ytterligare behörigheter från den [användarrollen](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Se Rollbeskrivning ovan för mer information.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Skapa och ta bort alla resurser och läsa och uppdatera standardegenskaper i Azure Access Control. |
| microsoft.aad.compliance/AllEntities/AllActions | Skapa och ta bort alla resurser och läsa och uppdatera standardegenskaper efter Efterlevnadscenter. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Läsa och konfigurera Office 365 Service Health. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Skapa och hantera Office 365-supportbegäranden. |
| microsoft.exchange/Compliance/AllActions | Hantera efterlevnad i Exchange Online. |
| microsoft.sharepoint/Compliance/AllActions | Hantera efterlevnad i SharePoint Online. |
| microsoft.skypeforbusiness/Compliance/AllActions | Hantera efterlevnad i Skype för företag – Online. |

### <a name="conditional-access-administrator"></a>Administratör för villkorsstyrd åtkomst
Kan hantera funktioner för villkorsstyrd åtkomst.

  > [!NOTE]
  > Den här rollen ärver ytterligare behörigheter från den [användarrollen](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/ConditionalAccessPolicy/Create | Skapa ConditionalAccessPolicys i Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Delete | Ta bort ConditionalAccessPolicys i Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Read | Läsa standardegenskaper för ConditionalAccessPolicys i Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Read/Owners | Läs ConditionalAccessPolicys.Owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Read/PolicyAppliedTo | Läs ConditionalAccessPolicys.PolicyAppliedTo-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Update | Uppdatera standardegenskaper för ConditionalAccessPolicys i Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Update/Owners | Uppdatera ConditionalAccessPolicys.Owners-egenskapen i Azure Active Directory. |

### <a name="device-administrators"></a>Enhetsadministratörer

Användare med den här rollen blir administratörer för den lokala datorn på alla Windows 10-enheter som är anslutna till Azure Active Directory. De har inte behörighet att hantera enhetsobjekt i Azure Active Directory.

  > [!NOTE]
  > Den här rollen ärver ytterligare behörigheter från den [användarrollen](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

### <a name="directory-readers"></a>Katalogläsare
Kan läsa grundläggande kataloginformation. För att bevilja åtkomst till program.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/AdministrativeUnit/Read | Läsa standardegenskaperna på AdministrativeUnits i Azure Active Directory. |
| microsoft.aad.directory/AdministrativeUnit/Read/Members | Läs AdministrativeUnits.Members-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Application/Read | Läsa standardegenskaper för program i Azure Active Directory. |
| microsoft.aad.directory/Application/Read/Owners | Läsa Applications.Owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/CollaborationSpace/Read | Läsa standardegenskaper på CollaborationSpaces i Azure Active Directory. |
| microsoft.aad.directory/CollaborationSpace/Read/Owners | Läsa CollaborationSpaces.Owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Contact/Read | Läsa standardegenskaper för kontakter i Azure Active Directory. |
| microsoft.aad.directory/Contact/Read/MemberOf | Läsa Contacts.MemberOf-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Device/Read | Läsa standardegenskaper för enheter i Azure Active Directory. |
| microsoft.aad.directory/Device/Read/MemberOf | Läsa Devices.MemberOf-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Device/Read/RegisteredOwners | Läsa Devices.RegisteredOwners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Device/Read/RegisteredUsers | Läsa Devices.RegisteredUsers-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/DirectoryRole/Read | Läsa standardegenskaper för DirectoryRoles i Azure Active Directory. |
| microsoft.aad.directory/DirectoryRole/Read/EligibleMembers | Läsa DirectoryRoles.EligibleMembers-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/DirectoryRole/Read/Members | Läsa DirectoryRoles.Members-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/DirectorySetting/Read | Läsa standardegenskaper för DirectorySettings i Azure Active Directory. |
| microsoft.aad.directory/DirectorySettingTemplate/Read | Läsa standardegenskaper för DirectorySettingTemplates i Azure Active Directory. |
| microsoft.aad.directory/Domain/Read | Läsa standardegenskaper för domäner i Azure Active Directory. |
| microsoft.aad.directory/Group/Read | Läsa standardegenskaper för grupper i Azure Active Directory. |
| microsoft.aad.directory/Group/Read/AppRoleAssignments | Läsa Groups.AppRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Group/Read/MemberOf | Läsa Groups.MemberOf-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Group/Read/Members | Läsa Groups.Members-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Group/Read/Owners | Läsa Groups.Owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Group/Read/Settings | Läsa Groups.Settings-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/OAuth2PermissionGrant/Read | Läsa standardegenskaper för OAuth2PermissionGrants i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read | Läsa standardegenskaper för ServicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignedTo | Läsa ServicePrincipals.AppRoleAssignedTo-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignments | Läsa ServicePrincipals.AppRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/DefaultPolicy | Läsa ServicePrincipals.DefaultPolicy-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/MemberOf | Läsa ServicePrincipals.MemberOf-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/OAuth2PermissionGrants | Läsa ServicePrincipals.OAuth2PermissionGrants-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/Owners | Läsa ServicePrincipals.Owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/OwnedObjects | Läsa ServicePrincipals.OwnedObjects-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Organization/Read | Läsa standardegenskaper för organisationer i Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Läs Organizations.TrustedCAsForPasswordlessAuth-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/Read | Läsa standardegenskaper för användare i Azure Active Directory. |
| microsoft.aad.directory/User/Read/AppRoleAssignments | Läsa Users.AppRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/Read/DirectReports | Läsa Users.DirectReports-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/Read/InvitedBy | Läsa Users.InvitedBy-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/Read/InvitedUsers | Läsa Users.InvitedUsers-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/Read/Manager | Läsa Users.Manager-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/Read/MemberOf | Läsa Users.MemberOf-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/Read/OAuth2PermissionGrants | Läsa Users.OAuth2PermissionGrants-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/Read/OwnedDevices | Läsa Users.OwnedDevices-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/Read/OwnedObjects | Läsa Users.OwnedObjects-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/Read/RegisteredDevices | Läsa Users.RegisteredDevices-egenskapen i Azure Active Directory. |

### <a name="directory-synchronization-accounts"></a>Konton för katalogen katalogsynkronisering
Endast används av Azure AD Connect-tjänsten.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/Policy/Create | Skapa principer i Azure Active Directory. |
| microsoft.aad.directory/Policy/Delete | Ta bort principer i Azure Active Directory. |
| microsoft.aad.directory/Policy/Read | Läsa standardegenskaper för principer i Azure Active Directory. |
| microsoft.aad.directory/Policy/Read/Owners | Läsa Policies.Owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Policy/Read/PolicyAppliedTo | Läsa Policies.PolicyAppliedTo-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Policy/Update | Uppdatera standardegenskaper för principer i Azure Active Directory. |
| microsoft.aad.directory/Policy/Update/Owners | Uppdatera Policies.Owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Create | Skapa ServicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read | Läsa standardegenskaper för ServicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignedTo | Läsa ServicePrincipals.AppRoleAssignedTo-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignments | Läsa ServicePrincipals.AppRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/DefaultPolicy | Läsa ServicePrincipals.DefaultPolicy-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/MemberOf | Läsa ServicePrincipals.MemberOf-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/OAuth2PermissionGrants | Läsa ServicePrincipals.OAuth2PermissionGrants-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/Owners | Läsa ServicePrincipals.Owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/OwnedObjects | Läsa ServicePrincipals.OwnedObjects-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update | Uppdatera standardegenskaper för ServicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | Uppdatera ServicePrincipals.AppRoleAssignedTo-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | Uppdatera ServicePrincipals.AppRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Uppdatera ServicePrincipals.DefaultPolicy-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | Uppdatera ServicePrincipals.Owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Organization/Update/DirSync | Uppdatera Organizations.DirSync-egenskapen i Azure Active Directory. |
| microsoft.aad.directorysync/AllEntities/AllActions | Utföra alla åtgärder i Azure AD Connect. |

### <a name="directory-writer"></a>Directory-skrivare
Kan läsa och skriva grundläggande kataloginformation. För att bevilja åtkomst till program

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/DirectorySetting/Create | Skapa DirectorySettings i Azure Active Directory. |
| microsoft.aad.directory/DirectorySetting/Delete | Ta bort DirectorySettings i Azure Active Directory. |
| microsoft.aad.directory/DirectorySetting/Update | Uppdatera standardegenskaper för DirectorySettings i Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Skapa grupper i Azure Active Directory. |
| microsoft.aad.directory/Group/CreateAsOwner | Skapa grupper i Azure Active Directory. Skaparen läggs till som första ägare och det skapade objektet räknas mot Skaparens kvot på 250 skapade objekt. |
| microsoft.aad.directory/Group/Read | Läsa standardegenskaper för grupper i Azure Active Directory. |
| microsoft.aad.directory/Group/Update | Uppdatera standardegenskaper för grupper i Azure Active Directory. |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | Uppdatera Groups.AppRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Uppdatera Groups.Members-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Owners | Uppdatera Groups.Owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Settings | Uppdatera Groups.Settings-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Läs Organizations.TrustedCAsForPasswordlessAuth-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Hantera licenser för användare i Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Invalidera alla tokens för användaruppdatering i Azure Active Directory. |
| microsoft.aad.directory/User/Update | Uppdatera standardegenskaper för användare i Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Uppdatera Users.AppRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Uppdatera Users.Manager-egenskapen i Azure Active Directory. |

### <a name="dynamics-365-service-administrator"></a>Dynamics 365-tjänstadministratör
Kan hantera alla aspekter av Dynamics 365-produkten.

  > [!NOTE]
  > Den här rollen ärver ytterligare behörigheter från den [användarrollen](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Se Rollbeskrivning ovan för mer information.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Läs Organizations.TrustedCAsForPasswordlessAuth-egenskapen i Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Skapa och ta bort alla resurser och läsa och uppdatera standardegenskaper i Azure Access Control. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Läsa och konfigurera Office 365 Service Health. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Skapa och hantera Office 365-supportbegäranden. |
| microsoft.crm/AllEntities/AllActions | Hantera alla aspekter av Dynamics 365. |

### <a name="exchange-service-administrator"></a>Exchange Service-administratör
Kan hantera alla aspekter av Exchange-produkten.

  > [!NOTE]
  > Den här rollen ärver ytterligare behörigheter från den [användarrollen](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Se Rollbeskrivning ovan för mer information.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Skapa och ta bort alla resurser och läsa och uppdatera standardegenskaper i Azure Access Control. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Skapa och hantera Office 365-supportbegäranden. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Läsa och konfigurera Office 365 Service Health. |
| microsoft.exchange/AllEntities/AllActions | Hantera alla aspekter av Exchange Online. |

### <a name="guest-inviter"></a>Gäst bjuder in
Kan bjuda in gästanvändare oberoende av den **medlemmar kan bjuda in gäster** inställningen.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från rollen gäst.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/User/InviteGuest | Bjud in gästanvändare i Azure Active Directory. |
| microsoft.aad.directory/User/Read | Läsa standardegenskaper för användare i Azure Active Directory. |
| microsoft.aad.directory/User/Read/AppRoleAssignments | Läsa Users.AppRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/Read/DirectReports | Läsa Users.DirectReports-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/Read/InvitedBy | Läsa Users.InvitedBy-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/Read/InvitedUsers | Läsa Users.InvitedUsers-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/Read/Manager | Läsa Users.Manager-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/Read/MemberOf | Läsa Users.MemberOf-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/Read/OAuth2PermissionGrants | Läsa Users.OAuth2PermissionGrants-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/Read/OwnedDevices | Läsa Users.OwnedDevices-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/Read/OwnedObjects | Läsa Users.OwnedObjects-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/Read/RegisteredDevices | Läsa Users.RegisteredDevices-egenskapen i Azure Active Directory. |

### <a name="helpdesk-administrator"></a>Supportavdelningsadministratör
Kan återställa lösenord för icke-administratörer och Helpdeks-administratörer.

  > [!NOTE]
  > Den här rollen ärver ytterligare behörigheter från den [användarrollen](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Läs Organizations.TrustedCAsForPasswordlessAuth-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Invalidera alla tokens för användaruppdatering i Azure Active Directory. |
| microsoft.aad.directory/User/Update/PasswordHelpdeskScope | Uppdatera lösenord för begränsade administratörer och andra supportadministratörer i Azure Active Directory. Se onlinedokumentationen för mer information. |
| microsoft.aad.accessservice/AllEntities/AllActions | Skapa och ta bort alla resurser och läsa och uppdatera standardegenskaper i Azure Access Control. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Skapa och hantera Office 365-supportbegäranden. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Läsa och konfigurera Office 365 Service Health. |

### <a name="information-protection-administrator"></a>Information Protection-administratör
Kan hantera alla aspekter av Azure Information Protection-produkten.

  > [!NOTE]
  > Den här rollen ärver ytterligare behörigheter från den [användarrollen](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Se Rollbeskrivning ovan för mer information.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/Group/Read | Läsa standardegenskaper för grupper i Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Läs Organizations.TrustedCAsForPasswordlessAuth-egenskapen i Azure Active Directory. |
| microsoft.aad.informationprotection/AllEntities/AllActions | Hantera alla aspekter av Information Protection. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Läsa och konfigurera Office 365 Service Health. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Skapa och hantera Office 365-supportbegäranden. |

### <a name="intune-service-administrator"></a>Administratör för Intune-tjänsten
Kan hantera alla aspekter av Intune-produkten.

  > [!NOTE]
  > Den här rollen ärver ytterligare behörigheter från den [användarrollen](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Se Rollbeskrivning ovan för mer information.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/Contact/Create | Skapa kontakter i Azure Active Directory. |
| microsoft.aad.directory/Contact/Delete | Ta bort kontakter i Azure Active Directory. |
| microsoft.aad.directory/Contact/Update | Uppdatera standardegenskaper för kontakter i Azure Active Directory. |
| microsoft.aad.directory/Device/Create | Skapa enheter i Azure Active Directory. |
| microsoft.aad.directory/Device/Delete | Ta bort enheter i Azure Active Directory. |
| microsoft.aad.directory/Device/Update | Uppdatera standardegenskaper för enheter i Azure Active Directory. |
| microsoft.aad.directory/Device/Update/RegisteredOwners | Uppdatera Devices.RegisteredOwners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Device/Update/RegisteredUsers | Uppdatera Devices.RegisteredUsers-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Skapa grupper i Azure Active Directory. |
| microsoft.aad.directory/Group/CreateAsOwner | Skapa grupper i Azure Active Directory. Skaparen läggs till som första ägare och det skapade objektet räknas mot Skaparens kvot på 250 skapade objekt. |
| microsoft.aad.directory/Group/Delete | Ta bort grupper i Azure Active Directory. |
| microsoft.aad.directory/Group/Read | Läsa standardegenskaper för grupper i Azure Active Directory. |
| microsoft.aad.directory/Group/Read/HiddenMembers | Läsa Groups.HiddenMembers-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Group/Restore | Återställa grupper i Azure Active Directory. |
| microsoft.aad.directory/Group/Update | Uppdatera standardegenskaper för grupper i Azure Active Directory. |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | Uppdatera Groups.AppRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Uppdatera Groups.Members-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Owners | Uppdatera Groups.Owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Settings | Uppdatera Groups.Settings-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Läs Organizations.TrustedCAsForPasswordlessAuth-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/Update | Uppdatera standardegenskaper för användare i Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Uppdatera Users.AppRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Uppdatera Users.Manager-egenskapen i Azure Active Directory. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Skapa och hantera Office 365-supportbegäranden. |
| microsoft.intune/AllEntities/AllActions | Hantera alla aspekter av Intune. |

### <a name="lync-service-administrator"></a>Lync-tjänstadministratör
Kan hantera alla aspekter av Skype för Business-produkten.

  > [!NOTE]
  > Den här rollen ärver ytterligare behörigheter från den [användarrollen](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Se Rollbeskrivning ovan för mer information.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Skapa och ta bort alla resurser och läsa och uppdatera standardegenskaper i Azure Access Control. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Läsa och konfigurera Office 365 Service Health. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Skapa och hantera Office 365-supportbegäranden. |
| microsoft.skypeforbusiness/AllEntities/AllActions | Hantera Skype för företag – Online. |

### <a name="message-center-reader"></a>Meddelandecenterläsare
Kan endast läsa meddelanden och uppdateringar avseende sin organisation i meddelandecenter för Office 365. 

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Se Rollbeskrivning ovan för mer information.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/Group/Read | Läsa standardegenskaper för grupper i Azure Active Directory. |
| microsoft.aad.accessmessagecenter/AllEntities/AllActions | Skapa och ta bort alla resurser och läsa och uppdatera standardegenskaper i meddelandecenter. |
| microsoft.aad.accessservice/AllEntities/AllActions | Skapa och ta bort alla resurser och läsa och uppdatera standardegenskaper i Azure Access Control. |

### <a name="partner-tier1-support"></a>Partnersupport, nivå 1
Använd inte – inte avsett för allmänt bruk.

  > [!NOTE]
  > Den här rollen ärver ytterligare behörigheter från den [användarrollen](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Se Rollbeskrivning ovan för mer information.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Skapa och ta bort alla resurser och läsa och uppdatera standardegenskaper i Azure Access Control. |
| microsoft.aad.directory/Contact/Create | Skapa kontakter i Azure Active Directory. |
| microsoft.aad.directory/Contact/Delete | Ta bort kontakter i Azure Active Directory. |
| microsoft.aad.directory/Contact/Update | Uppdatera standardegenskaper för kontakter i Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Skapa grupper i Azure Active Directory. |
| microsoft.aad.directory/Group/CreateAsOwner | Skapa grupper i Azure Active Directory. Skaparen läggs till som första ägare och det skapade objektet räknas mot Skaparens kvot på 250 skapade objekt. |
| microsoft.aad.directory/Group/Read | Läsa standardegenskaper för grupper i Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Uppdatera Groups.Members-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Owners | Uppdatera Groups.Owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Läs Organizations.TrustedCAsForPasswordlessAuth-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Hantera licenser för användare i Azure Active Directory. |
| microsoft.aad.directory/User/Delete | Ta bort användare i Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Invalidera alla tokens för användaruppdatering i Azure Active Directory. |
| microsoft.aad.directory/User/Restore | Återställa borttagna användare i Azure Active Directory. |
| microsoft.aad.directory/User/Update | Uppdatera standardegenskaper för användare i Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Uppdatera Users.AppRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Uppdatera Users.Manager-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/Update/PasswordUserScope | Uppdatera lösenord för icke-administratörer i Azure Active Directory. Se onlinedokumentationen för mer information. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Läsa och konfigurera Office 365 Service Health. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Skapa och hantera Office 365-supportbegäranden. |

### <a name="partner-tier2-support"></a>Partnersupport, nivå 2
Använd inte – inte avsett för allmänt bruk.

  > [!NOTE]
  > Den här rollen ärver ytterligare behörigheter från den [användarrollen](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Se Rollbeskrivning ovan för mer information.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Skapa och ta bort alla resurser och läsa och uppdatera standardegenskaper i Azure Access Control. |
| microsoft.aad.directory/Contact/Create | Skapa kontakter i Azure Active Directory. |
| microsoft.aad.directory/Contact/Delete | Ta bort kontakter i Azure Active Directory. |
| microsoft.aad.directory/Contact/Update | Uppdatera standardegenskaper för kontakter i Azure Active Directory. |
| microsoft.aad.directory/Domain/AllActions | Skapa och ta bort domäner och läsa och uppdatera standardegenskaper i Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Skapa grupper i Azure Active Directory. |
| microsoft.aad.directory/Group/Delete | Ta bort grupper i Azure Active Directory. |
| microsoft.aad.directory/Group/Read | Läsa standardegenskaper för grupper i Azure Active Directory. |
| microsoft.aad.directory/Group/Restore | Återställa grupper i Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Uppdatera Groups.Members-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Läs Organizations.TrustedCAsForPasswordlessAuth-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Organization/Update | Uppdatera standardegenskaper för organisationer i Azure Active Directory. |
| microsoft.aad.directory/Organization/Update/TrustedCAsForPasswordlessAuth | Uppdatera Organizations.TrustedCAsForPasswordlessAuth-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Hantera licenser för användare i Azure Active Directory. |
| microsoft.aad.directory/User/Delete | Ta bort användare i Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Invalidera alla tokens för användaruppdatering i Azure Active Directory. |
| microsoft.aad.directory/User/Restore | Återställa borttagna användare i Azure Active Directory. |
| microsoft.aad.directory/User/Update | Uppdatera standardegenskaper för användare i Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Uppdatera Users.AppRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Uppdatera Users.Manager-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/Update/Password | Uppdatera lösenord för alla användare i Azure Active Directory. Se onlinedokumentationen för mer information. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Läsa och konfigurera Office 365 Service Health. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Skapa och hantera Office 365-supportbegäranden. |

### <a name="power-bi-service-administrator"></a>Power BI-tjänstadministratör
Kan hantera alla aspekter av Power BI-produkten.

  > [!NOTE]
  > Den här rollen ärver ytterligare behörigheter från den [användarrollen](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Se Rollbeskrivning ovan för mer information.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Läs Organizations.TrustedCAsForPasswordlessAuth-egenskapen i Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Skapa och ta bort alla resurser och läsa och uppdatera standardegenskaper i Azure Access Control. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Läsa och konfigurera Office 365 Service Health. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Skapa och hantera Office 365-supportbegäranden. |
| microsoft.powerbi/AllEntities/AllActions | Kan hantera alla aspekter av Power BI. |

### <a name="privileged-role-administrator"></a>Privilegierad rolladministratör
Kan hantera rolltilldelningar i Azure AD

  > [!NOTE]
  > Den här rollen ärver ytterligare behörigheter från den [användarrollen](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Se Rollbeskrivning ovan för mer information.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/DirectoryRole/Update | Uppdatera standardegenskaper för DirectoryRoles i Azure Active Directory. |
| microsoft.aad.privilegedrolemanagement/AllEntities/AllActions | Hantera alla aspekter av tjänsten för hantering av privilegierad roll. |

### <a name="reports-reader"></a>Rapportläsare
Kan läsa inloggnings- och granskningsrapporter.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Se Rollbeskrivning ovan för mer information.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.reports/AllEntities/Read | Läsa Azure AD-rapporter. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Läsa och konfigurera Office 365 Service Health. |
| Microsoft.Office365.usagereports/AllEntities/Read | Läs Office 365-användningsrapporter. |

### <a name="security-administrator"></a>Säkerhetsadministratör
Kan läsa säkerhetsinformation och rapporter

  > [!NOTE]
  > Den här rollen ärver ytterligare behörigheter från den [användarrollen](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Se Rollbeskrivning ovan för mer information.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/Application/Update/DefaultPolicy | Uppdatera Applications.DefaultPolicy-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Policy/Create | Skapa principer i Azure Active Directory. |
| microsoft.aad.directory/Policy/Delete | Ta bort principer i Azure Active Directory. |
| microsoft.aad.directory/Policy/Update | Uppdatera standardegenskaper för principer i Azure Active Directory. |
| microsoft.aad.directory/Policy/Update/Owners | Uppdatera Policies.Owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Uppdatera ServicePrincipals.DefaultPolicy-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Läs Organizations.TrustedCAsForPasswordlessAuth-egenskapen i Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Skapa och ta bort alla resurser och läsa och uppdatera standardegenskaper i Azure Access Control. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Läsa och konfigurera Office 365 Service Health. |
| microsoft.aad.privilegedrolemanagement/AllEntities/Read | Läsa alla aspekter av Privileged Identity Management. |
| microsoft.protectioncenter/AllEntities/Read | Läsa alla aspekter av Office 365-säkerhetscenter. |
| microsoft.protectioncenter/AllEntities/Update | Hantera Office 365-säkerhetscenter. |

### <a name="security-reader"></a>Säkerhetsläsare
Kan läsa säkerhetsinformation och rapporter i Azure AD och Office 365.

  > [!NOTE]
  > Den här rollen ärver behörigheterna från katalogläsarrollen.
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Se Rollbeskrivning ovan för mer information.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Läs Organizations.TrustedCAsForPasswordlessAuth-egenskapen i Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Skapa och ta bort alla resurser och läsa och uppdatera standardegenskaper i Azure Access Control. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Läsa och konfigurera Office 365 Service Health. |
| microsoft.privilegedidentitymanagement/AllEntities/Read | Läsa alla aspekter av Privileged Identity Management. |
| microsoft.protectioncenter/AllEntities/Read | Läsa alla aspekter av Office 365-säkerhetscenter. |

### <a name="service-support-administrator"></a>Tjänstesupport-administratör
Kan läsa information om Service Health och hantera supportärenden.

  > [!NOTE]
  > Den här rollen ärver ytterligare behörigheter från den [användarrollen](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Se Rollbeskrivning ovan för mer information.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Läsa Organizations.TrustedCAsForPasswordlessAuth-egenskapen i Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Skapa och ta bort alla resurser och läsa och uppdatera standardegenskaper i Azure Access Control. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Skapa och hantera Office 365-supportbegäranden. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Läsa och konfigurera Office 365 Service Health. |

### <a name="sharepoint-service-administrator"></a>SharePoint-tjänstadministratör
Kan hantera alla aspekter av SharePoint-tjänsten.

  > [!NOTE]
  > Den här rollen ärver ytterligare behörigheter från den [användarrollen](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Se Rollbeskrivning ovan för mer information.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Skapa och ta bort alla resurser och läsa och uppdatera standardegenskaper i Azure Access Control. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Läsa och konfigurera Office 365 Service Health. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Skapa och hantera Office 365-supportbegäranden. |
| microsoft.sharepoint/AllEntities/AllActions | Hantera SharePoint Online. |

### <a name="user-account-administrator"></a>Användarkonto-administratör
Kan hantera alla aspekter av användare och grupper

  > [!NOTE]
  > Den här rollen ärver ytterligare behörigheter från den [användarrollen](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/AppRoleAssignment/Create | Skapa AppRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Delete | Ta bort AppRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Update | Uppdatera standardegenskaper på AppRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/Contact/Create | Skapa kontakter i Azure Active Directory. |
| microsoft.aad.directory/Contact/Delete | Ta bort kontakter i Azure Active Directory. |
| microsoft.aad.directory/Contact/Update | Uppdatera standardegenskaper för kontakter i Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Skapa grupper i Azure Active Directory. |
| microsoft.aad.directory/Group/CreateAsOwner | Skapa grupper i Azure Active Directory. Skaparen läggs till som första ägare och det skapade objektet räknas mot Skaparens kvot på 250 skapade objekt. |
| microsoft.aad.directory/Group/Delete | Ta bort grupper i Azure Active Directory. |
| microsoft.aad.directory/Group/Read | Läsa standardegenskaper för grupper i Azure Active Directory. |
| microsoft.aad.directory/Group/Read/HiddenMembers | Läsa Groups.HiddenMembers-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Group/Restore | Återställa grupper i Azure Active Directory. |
| microsoft.aad.directory/Group/Update | Uppdatera standardegenskaper för grupper i Azure Active Directory. |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | Uppdatera Groups.AppRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Uppdatera Groups.Members-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Owners | Uppdatera Groups.Owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Settings | Uppdatera Groups.Settings-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Läsa Organizations.TrustedCAsForPasswordlessAuth-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Hantera licenser för användare i Azure Active Directory. |
| microsoft.aad.directory/User/Create | Skapa användare i Azure Active Directory. |
| microsoft.aad.directory/User/Delete | Ta bort användare i Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Ogiltigförklaras alla tokens för användaruppdatering i Azure Active Directory. |
| microsoft.aad.directory/User/Restore | Återställa borttagna användare i Azure Active Directory. |
| microsoft.aad.directory/User/Update | Uppdatera standardegenskaper för användare i Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Uppdatera Users.AppRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Uppdatera Users.Manager-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/User/Update/PasswordUserAcctAdminScope | Uppdatera lösenord för begränsade administratörer, supportadministratörer och andra användarkonto administratörer i Azure Active Directory. Se onlinedokumentationen för mer information. |
| microsoft.aad.accessservice/AllEntities/AllActions | Skapa och ta bort alla resurser och läsa och uppdatera standardegenskaper i Azure Access Control. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Skapa och hantera Office 365-supportbegäranden. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Läsa och konfigurera Office 365 Service Health. |

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du ändrar administratörer för en Azure-prenumeration finns i [Lägga till eller ändra Azure-administratörsroller](../../billing/billing-add-change-azure-subscription-administrator.md)
* Mer information om hur resursåtkomsten hanteras i Microsoft Azure finns i [Förstå resursåtkomst i Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Mer information om hur Azure Active Directory relaterar till din Azure-prenumeration finns i [Hur Azure-prenumerationer är associerade med Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
