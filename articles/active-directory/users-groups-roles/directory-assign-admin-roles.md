---
title: Administratören rollbeskrivningar och behörigheter – Azure Active Directory | Microsoft Docs
description: En administratörsroll kan lägga till användare, tilldela administrativa roller, återställa användarlösenord, hantera användarlicenser eller hantera domäner.
services: active-directory
author: curtand
manager: mtillman
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/12/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f06446cb6af1fa145e5fcec41cc85a1452af207a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57839261"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Behörigheter för administratör i Azure Active Directory

Med Azure Active Directory (Azure AD) kan ange du begränsade administratörer att hantera identitet uppgifter i mindre privilegierade roller. Administratörer kan tilldelas för sådant som att lägga till eller ändra användare, tilldela administrativa roller, återställa användarlösenord, hantera användarlicenser och hantera domännamn. Standard användarbehörigheter kan ändras endast i användarinställningarna i Azure AD.

Globala administratörer har åtkomst till alla administrativa funktioner. Som standard tilldelas den person som registrerar sig för en Azure-prenumeration rollen som Global administratör för katalogen. Endast globala administratörer och Privilegierade Rolladministratörer kan delegera administratörsroller. För att minska risken för företaget rekommenderar vi att du tilldela den här rollen till endast ett fåtal personer i företaget.


## <a name="assign-or-remove-administrator-roles"></a>Tilldela eller ta bort administratörsroller

Om du vill lära dig mer om att tilldela administrativa roller till en användare i Azure Active Directory, se [visa och tilldela administratörsroller i Azure Active Directory](directory-manage-roles-portal.md).

## <a name="available-roles"></a>Tillgängliga roller

Följande administratörsroller är tillgängliga:

* **[Programadministratör](#application-administrator)**: Användare i den här rollen kan skapa och hantera alla aspekter av företagsprogram och programregistreringar proxyinställningarna för programmet. Den här rollen ger även möjlighet att godkänna delegerade behörigheter och programbehörigheter exklusive Microsoft Graph och Azure AD Graph. Användare som tilldelats den här rollen har inte lagts som ägare när du skapar nya programregistreringar eller företagsprogram.

  <b>Viktiga</b>: Den här rollen ger möjlighet att hantera programmet autentiseringsuppgifter. Användare som är tilldelade den här rollen kan lägga till autentiseringsuppgifter i ett program och använda dessa autentiseringsuppgifter för att personifiera programmets identitet. Om programmets identitet har beviljats åtkomst till Azure Active Directory, till exempel möjligheten att skapa eller uppdatera användare eller andra objekt, kan sedan en användare som tilldelats den här rollen utföra dessa åtgärder medan personifieras programmet. Den här möjligheten att personifiera programmets identitet kan vara en höjning av privilegier över vad användaren kan göra via sin rolltilldelningar i Azure AD. Det är viktigt att förstå att tilldela en användare till rollen programadministratör ger dem möjlighet att personifiera identitet för ett program.

* **[Programutvecklare](#application-developer)**: Användare i den här rollen kan skapa programregistreringar när den ”användare kan registrera program” är inställt på Nej. Den här rollen ger även behörighet att ge samtycke åt det egna när de ”användare kan godkänna att appar får åtkomst till företagets data å deras vägnar” är inställt på Nej. Användare som tilldelats den här rollen har lagts till som ägare när du skapar nya programregistreringar eller företagsprogram.

* **[Administratören Authentication](#authentication-administrator)**: Användare med den här rollen kan ange eller återställa autentiseringsuppgifter för icke-password. Autentisering-administratörer kan kräva att användare kan Omregistrera mot befintliga autentiseringsuppgifter för icke-lösenord (till exempel MFA eller FIDO) och återkalla **MFA sparas på enheten**, som uppmanar för MFA på den nästa inloggningen av användare icke-administratörer eller som har tilldelats följande roller:
  * Autentiseringsadministratör
  * Katalogläsare
  * Gäst bjuder in
  * Meddelandecenterläsare
  * Rapportläsare

  En administratör för autentisering är för närvarande i offentlig förhandsversion. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

  <b>Viktiga</b>: Användare med den här rollen kan ändra autentiseringsuppgifter för personer som kan ha åtkomst till känslig eller privat information eller kritiska konfiguration i och utanför Azure Active Directory. Ändra autentiseringsuppgifterna för en användare kan det innebära att möjlighet att anta användarens identitet och behörigheter. Exempel:

  * Programregistrering och företagsprogram ägare, som kan hantera autentiseringsuppgifterna för appar som de äger. Apparna kan ha privilegierad behörigheter i Azure AD och någon annanstans inte beviljas till administratörer för autentisering. Den här sökvägen som administratör autentisering kanske kan anta identiteten av ett programmets ägare och sedan ytterligare anta identiteten av ett privilegierat program genom att uppdatera autentiseringsuppgifterna för programmet.
  * Azure-prenumerationsägare, vilka kan ha åtkomst till känslig eller privat information eller kritiska konfiguration i Azure.
  * Säkerhetsgrupper och Office 365-gruppen ägare, vilka kan hantera gruppmedlemskap. Dessa grupper kan bevilja åtkomst till känslig eller privat information eller kritiska konfiguration i Azure AD och andra platser.
  * Administratörer i andra tjänster utanför Azure AD som Exchange Online, Office-säkerhet och efterlevnad Center och personal system.
  * Icke-administratörer som chefer, juridiska ombud och Personal anställda som kan ha åtkomst till känslig eller privat information.

* **[Faktureringsadministratör](#billing-administrator)**: Gör inköp, hanterar prenumerationer, hanterar supportbegäranden och övervakar tjänstens hälsotillstånd.

* **[Molnprogramadministratör](#cloud-application-administrator)**: Användare i den här rollen har samma behörigheter som rollen programadministratör exklusive möjligheten att hantera programproxy. Den här rollen ger möjlighet att skapa och hantera alla aspekter av företagsprogram och programregistreringar. Den här rollen ger även möjlighet att godkänna delegerade behörigheter och programbehörigheter exklusive Microsoft Graph och Azure AD Graph. Användare som tilldelats den här rollen har inte lagts som ägare när du skapar nya programregistreringar eller företagsprogram.

  <b>Viktiga</b>: Den här rollen ger möjlighet att hantera programmet autentiseringsuppgifter. Användare som är tilldelade den här rollen kan lägga till autentiseringsuppgifter i ett program och använda dessa autentiseringsuppgifter för att personifiera programmets identitet. Om programmets identitet har beviljats åtkomst till Azure Active Directory, till exempel möjligheten att skapa eller uppdatera användare eller andra objekt, kan sedan en användare som tilldelats den här rollen utföra dessa åtgärder medan personifieras programmet. Den här möjligheten att personifiera programmets identitet kan vara en höjning av privilegier över vad användaren kan göra via sin rolltilldelningar i Azure AD. Det är viktigt att förstå att tilldela en användare till rollen Molnprogramadministratör ger dem möjlighet att personifiera identitet för ett program.

* **[Molnenhetsadministratör](#cloud-device-administrator)**: Användare i den här rollen kan aktivera, inaktivera, och ta bort enheter i Azure AD och Läs Windows 10 BitLocker-nycklar (om sådan finns) i Azure-portalen. Rollen ger inte behörighet att hantera andra egenskaper på enheten.

* **[Efterlevnadsadministratör](#compliance-administrator)**: Användare med den här rollen har behörighet att hantera efterlevnad-relaterade funktioner i Microsoft 365 efterlevnadscenter, Administrationscenter för Microsoft 365, Azure, och Office 365-säkerhet och Efterlevnadscenter. Användare kan också hantera alla funktioner i administrationscentret för Exchange, Teams och Skype för företag-administrationscentret och skapa supportbegäranden för Azure och Microsoft 365. Mer information finns på [om Office 365-administratörsroller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

  I | Kan göra
  ----- | ----------
  [Microsoft 365 efterlevnadscenter](https://protection.microsoft.com) | Skydda och hantera din organisations data över Microsoft 365-tjänster<br>Hantera aviseringar för efterlevnad
  [Compliance Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Spåra, tilldela och verifiera din organisations regelefterlevnad aktiviteter
  [Och Efterlevnadscenter för Office 365-säkerhet](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Hantera datastyrning<br>Utför juridisk information och data undersökning<br>Hantera begäran om ämne
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Visa alla Intune-granskningsdata
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Har skrivskyddad behörighet och kan hantera aviseringar<br>Kan skapa och ändra principer för filer och Tillåt styrningsåtgärder<br> Kan visa de inbyggda rapporterna under datahantering

<!--* **[Compliance Data Administrator](#compliance-data-administrator)**: Users with this role have permissions to protect and track data in the Microsoft 365 compliance center, Microsoft 365 admin center, and Azure. Users can also manage all features within the Exchange admin center, Compliance Manager, and Teams & Skype for Business admin center and create support tickets for Azure and Microsoft 365.

  In | Can do
  ----- | ----------
  [Microsoft 365 compliance center](https://protection.microsoft.com) | Monitor compliance-related policies across Microsoft 365 services<br>Manage compliance alerts
  [Compliance Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Track, assign, and verify your organization's regulatory compliance activities
  [Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Manage data governance<br>Perform legal and data investigation<br>Manage Data Subject Request
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | View all Intune audit data
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Has read-only permissions and can manage alerts<br>Can create and modify file policies and allow file governance actions<br> Can view all the built-in reports under Data Management
-->
* **[Administratör för villkorsstyrd åtkomst](#conditional-access-administrator)**: Användare med den här rollen kan hantera Azure Active Directory-villkorsstyrda åtkomstinställningar.
  > [!NOTE]
  > Om du vill distribuera Exchange ActiveSync villkorsstyrd åtkomstprincip i Azure, måste användaren också vara en Global administratör.
  
* **[Customer Lockbox åtkomst godkännaren](#customer-lockbox-access-approver)**: Hanterar [Customer Lockbox begär](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests) i din organisation. De kan får e-postmeddelanden för Customer Lockbox begäranden och godkänna och Neka förfrågningar från Administrationscenter för Microsoft 365. De kan också aktivera funktionen Customer Lockbox eller inaktivera. Endast globala administratörer kan återställa lösenorden för personer som har tilldelats den här rollen.
  <!--  This was announced in August of 2018. https://techcommunity.microsoft.com/t5/Security-Privacy-and-Compliance/Customer-Lockbox-Approver-Role-Now-Available/ba-p/223393-->

* **[Enhetsadministratörer](#device-administrators)**: Den här rollen är tilldelas endast som en ytterligare lokal administratör i [Enhetsinställningar](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Användare med den här rollen blir administratörer för den lokala datorn på alla Windows 10-enheter som är anslutna till Azure Active Directory. De har inte behörighet att hantera enheters objekt i Azure Active Directory. 

* **[Katalogläsare](#directory-readers)**: Det här är en äldre roll som ska tilldelas till program som inte stöder den [godkänna Framework](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Det ska inte tilldelas alla användare.

* **[Konton för katalogsynkronisering](#directory-synchronization-accounts)**: Använd inte. Den här rollen är tilldelas automatiskt till Azure AD Connect-tjänsten och inte avsedd eller stöd för annan användning.

* **[Katalogskrivare](#directory-writers)**: Det här är en äldre roll som ska tilldelas till program som inte stöder den [godkänna Framework](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Det ska inte tilldelas alla användare.

* **[Dynamics 365-administratör / CRM-administratören](#crm-service-administrator)**: Användare med den här rollen har globala behörigheter inom Microsoft Dynamics 365 Online när tjänsten finns närvarande, liksom möjlighet att hantera supportbegäranden och kontrollera tjänstens hälsotillstånd. Mer information på [använda administratörsrollen för att hantera din klient](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).
  > [!NOTE] 
  > I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras rollen som ”tjänstadministratör för Dynamics 365”. Det är ”Dynamics 365-administratör” i den [Azure-portalen](https://portal.azure.com).

* **[Exchange-administratören](#exchange-service-administrator)**: Användare med den här rollen har globala behörigheter inom Microsoft Exchange Online när tjänsten finns. Har också möjlighet att skapa och hantera alla Office 365-grupper, hantera supportbegäranden och kontrollera tjänstens hälsotillstånd. Mer information på [om Office 365-administratörsroller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras rollen som ”Exchange Service-administratör”. Det är ”Exchange-administratör” i den [Azure-portalen](https://portal.azure.com). Det är ”Exchange Online administratör i fältet” i den [administrationscentret för Exchange](https://go.microsoft.com/fwlink/p/?LinkID=529144). 


* **[Global administratör / företaget administratör](#company-administrator)**: Användare med den här rollen har åtkomst till alla administrativa funktioner i Azure Active Directory, samt tjänster som använder Azure Active Directory-identiteter som Microsoft 365 security center Microsoft 365 efterlevnadscenter Exchange Online, SharePoint Online och Skype för företag Online. Den person som registrerar sig för Azure Active Directory-klient blir global administratör. Endast globala administratörer kan tilldela andra administratörsroller. Det kan finnas mer än en global administratör i företaget. Globala administratörer kan återställa lösenordet för alla användare och alla andra administratörer.

  > [!NOTE]
  > I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras rollen som ”företagets administratör”. Det är ”Global administratör” i den [Azure-portalen](https://portal.azure.com).
  >
  >

* **[Gäst bjuder in](#guest-inviter)**: Användare i den här rollen kan hantera inbjudningar för Azure Active Directory B2B gäst när den **medlemmar kan bjuda in** användaren är inställt på Nej. Mer information om B2B-samarbete i [om Azure AD B2B-samarbete](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Den omfattar inte några andra behörigheter.

* **[Information Protection-administratör](#information-protection-administrator)**: Användare med den här rollen har alla behörigheter i Azure Information Protection-tjänsten. Den här rollen kan konfigurera etiketter för Azure Information Protection-principen, hantera skyddsmallar och aktivera skydd. Den här rollen ger inte några behörigheter i Identity Protection Center, Privileged Identity Management, övervaka Office 365 Service Health eller Office 365 säkerhets- och Efterlevnadscenter.

* **[Intune-administratör](#intune-service-administrator)**: Användare med den här rollen har globala behörigheter inom Microsoft Intune Online när tjänsten finns. Dessutom innehåller den här rollen möjlighet att hantera användare och enheter för att associera principer, samt skapa och hantera grupper. Mer information på [rollbaserad administrationskontroll (RBAC) med Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control)
  > [!NOTE]
  > I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras rollen som ”Intune-tjänstadministratör”. Det är ”Intune-administratör” i den [Azure-portalen](https://portal.azure.com).

* **[Licensadministratör](#license-administrator)**: Användare i den här rollen kan lägga till, ta bort och uppdatera licensiera tilldelningar för användare, grupper (med gruppbaserad licensiering) och hantera användningsplats på användare. Rollen ger inte möjlighet att köpa eller hantera prenumerationer, skapa eller hantera grupper, eller skapa eller hantera användarna, förutom användningsplatsen. Den här rollen har ingen behörighet att visa, skapa eller hantera supportärenden.

* **[Meddelandecenterläsare](#message-center-reader)**: Användare i den här rollen kan övervaka meddelanden och rådgivande hälsotillstånd uppdateringar i [Office 365-meddelandecentret](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) för organisationen på konfigurerade tjänster som Exchange, Intune och Microsoft Teams. Meddelandecenter läsarna få veckovis e-sammandrag av inlägg, uppdateringar, och kan dela center meddelandeposter i Office 365. I Azure AD har användare som tilldelats den här rollen endast läsåtkomst på Azure AD-tjänster, till exempel användare och grupper. Den här rollen har ingen behörighet att visa, skapa eller hantera supportärenden.

* **[Partner nivå 1-Support](#partner-tier1-support)**: Använd inte. Den här rollen har gjorts inaktuell och kommer att tas bort från Azure AD i framtiden. Den här rollen är avsedd att användas av ett litet antal Microsoft återförsäljning-partner och är inte avsedd för allmänt bruk.

* **[Partner – nivå 2-stöd](#partner-tier2-support)**: Använd inte. Den här rollen har gjorts inaktuell och kommer att tas bort från Azure AD i framtiden. Den här rollen är avsedd att användas av ett litet antal Microsoft återförsäljning-partner och är inte avsedd för allmänt bruk.

* **[Lösenordsadministratör / Supportavdelningsadministratör](#helpdesk-administrator)**: Användare med den här rollen kan ändra lösenord, ogiltigförklara uppdateringstoken, hanterar tjänstbegäranden och kontrollera tjänstens hälsotillstånd. Ogiltigförklara en uppdateringstoken Tvingar användaren att logga in igen. Helpdeks-administratörer kan återställa lösenord och ogiltigförklara uppdateringstoken för andra användare som är icke-administratörer eller tilldelats följande roller:
  * Katalogläsare
  * Gäst bjuder in
  * Supportavdelningsadministratör
  * Meddelandecenterläsare
  * Rapportläsare
  
  <b>Viktiga</b>: Användare med den här rollen kan ändra lösenord för personer som kan ha åtkomst till känslig eller privat information eller kritiska konfiguration i och utanför Azure Active Directory. Ändra lösenordet för en användare kan det innebära att möjlighet att anta användarens identitet och behörigheter. Exempel:
  * Programregistrering och företagsprogram ägare, som kan hantera autentiseringsuppgifterna för appar som de äger. Apparna kan ha privilegierad behörigheter i Azure AD och någon annanstans inte beviljas till Supportadministratörer. Den här sökvägen som en Supportavdelningsadministratör kanske kan anta identiteten av ett programmets ägare och sedan ytterligare anta identiteten av ett privilegierat program genom att uppdatera autentiseringsuppgifterna för programmet.
  * Azure-prenumerationsägare, vilka kan ha åtkomst till känslig eller privat information eller kritiska konfiguration i Azure.
  * Säkerhetsgrupper och Office 365-gruppen ägare, vilka kan hantera gruppmedlemskap. Dessa grupper kan bevilja åtkomst till känslig eller privat information eller kritiska konfiguration i Azure AD och andra platser.
  * Administratörer i andra tjänster utanför Azure AD som Exchange Online, Office-säkerhet och efterlevnad Center och personal system.
  * Icke-administratörer som chefer, juridiska ombud och Personal anställda som kan ha åtkomst till känslig eller privat information.

  > [!NOTE]
  > I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras rollen som ”Supportavdelningsadministratör”. Det är ”Lösenordsadministratör” i den [Azure-portalen](https://portal.azure.com/).
  >
  
* **[Power BI-administratör](#power-bi-service-administrator)**: Användare med den här rollen har globala behörigheter inom Microsoft Power BI när tjänsten finns närvarande, liksom möjlighet att hantera supportbegäranden och kontrollera tjänstens hälsotillstånd. Mer information på [förstå administratörsrollen för Power BI](https://docs.microsoft.com/power-bi/service-admin-role).
  > [!NOTE]
  > I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras rollen som ”Power BI-tjänstadministratör”. Det är ”Power BI-administratör” i den [Azure-portalen](https://portal.azure.com).

* **[Privilegierad administratören Authentication](#privileged-authentication-administrator)**: Användare med den här rollen kan ange eller återställa icke-password autentiseringsuppgifter för alla användare, inklusive globala administratörer. Privilegierade autentisering-administratörer kan tvinga användare att registrera mot befintliga icke-password autentiseringsuppgifter (t.ex. MFA, FIDO) och återkalla ”Kom ihåg att MFA på enheten' prompt för MFA vid nästa inloggning för alla användare. Privilegierade autentisering-administratörer kan:
  * Tvinga användare att registrera mot befintliga icke-password autentiseringsuppgifter (t.ex. MFA, FIDO)
  * Återkalla ”Kom ihåg att MFA på enheten' prompt för MFA vid nästa inloggning

* **[Privilegierad Rolladministratör](#privileged-role-administrator)**: Användare med den här rollen kan hantera rolltilldelningar i Azure Active Directory, liksom i Azure AD Privileged Identity Management. Den här rollen kan dessutom hantera alla aspekter av Privileged Identity Management.

  <b>Viktiga</b>: Den här rollen ger dig möjlighet att hantera tilldelningar för alla Azure AD-roller, inklusive den globala administratörsrollen. Den här rollen omfattar inte andra Privilegierade funktioner i Azure AD, som skapar eller uppdaterar användare. Dock kan användare som tilldelats den här rollen bevilja sig själva eller andra ytterligare behörighet genom att tilldela ytterligare roller.

* **[Rapporterar läsare](#reports-reader)**: Användare med den här rollen kan visa användning rapporteringsdata och instrumentpanelen rapporter i Administrationscenter för Office 365 och införande kontexten pack i Power BI. Dessutom rollen ger åtkomst till loggar in rapporter och aktivitet i Azure AD och data som returneras av Microsoft Graph rapporterings-API. En användare som tilldelats rollen rapportläsare kan komma åt endast relevanta användnings- och användningsstatistik. De har inte någon administratörsbehörighet för att konfigurera inställningar eller åtkomst till produktspecifika Administrationscenter som Exchange. Den här rollen har ingen behörighet att visa, skapa eller hantera supportärenden.

* **[Säkerhetsadministratör](#security-administrator)**: Användare med den här rollen har behörighet att hantera säkerhetsrelaterade funktioner i Microsoft 365 Säkerhetscenter, Azure Active Directory Identity Protection, Azure Information Protection och Office 365-säkerhet och Efterlevnadscenter. Mer information om behörigheter för Office 365 finns på [behörigheter i Office 365 säkerhets- och Efterlevnadscenter](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).
  
  I | Kan göra
  --- | ---
  [Microsoft 365 Säkerhetscenter](https://protection.microsoft.com) | Övervaka säkerhetsrelaterade principer i Microsoft 365-tjänster<br>Hantera hot och aviseringar<br>Visa rapporter
  Identity Protection Center  | Alla behörigheter för rollen Säkerhetsläsare<br>Dessutom kan utföra alla åtgärder för Identity Protection Center förutom för återställning av lösenord
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Alla behörigheter för rollen Säkerhetsläsare<br>**Det går inte att** hantera rolltilldelningar i Azure AD eller inställningar
  [Och Efterlevnadscenter för Office 365-säkerhet](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Hantera säkerhetsprinciper<br>Visa, undersöka och svara på säkerhetshot<br>Visa rapporter
  Azure Advanced Threat Protection | Övervaka och svara på misstänkta aktiviteter
  Windows Defender ATP och EDR | Tilldela roller<br>Hantera datorgrupper<br>Konfigurera slutpunkt för identifiering av hot och automatiska reparationer<br>Visa, undersöka och vidta åtgärder vid aviseringar
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Vyer användare, enhet, registrering, konfiguration och programinformation<br>Det går inte att göra ändringar i Intune
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Lägga till administratörer, lägga till principer och inställningar, ladda upp loggar och utföra styrningsåtgärder
  [Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Kan visa säkerhetsprinciper, security tillstånd, redigera säkerhetsprinciper, Visa aviseringar och rekommendationer, avvisa aviseringar och rekommendationer
  [Tjänstehälsa för Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Visa hälsotillståndet för Office 365-tjänster

<!--* **[Security operator](#security-operator)**: Users with this role can manage alerts and have global read-only access on security-related feature, including all information in Microsoft 365 security center, Azure Active Directory, Identity Protection, Privileged Identity Management, as well as the ability to read Azure Active Directory sign-in reports and audit logs, and in Office 365 Security & Compliance Center.

  In | Can do
  --- | ---
  [Microsoft 365 security center](https://protection.microsoft.com) | All permissions of the Security Reader role<br>View, investigate, and respond to security threats alerts
  Identity Protection Center | All permissions of the Security Reader role<br>Additionally, the ability to perform all Identity Protection Center operations except for resetting passwords
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | All permissions of the Security Reader role
  [Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | All permissions of the Security Reader role<br>View, investigate, and respond to security alerts
  Windows Defender ATP and EDR | All permissions of the Security Reader role<br>View, investigate, and respond to security alerts
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | All permissions of the Security Reader role
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | All permissions of the Security Reader role
  [Office 365 service health](https://docs.microsoft.com/office365/enterprise/view-service-health) | View the health of Office 365 services
-->
* **[Security Reader](#security-reader)**: Användare med den här rollen har global skrivskyddad åtkomst på säkerhetsrelaterade funktioner, inklusive all information i Microsoft 365 Säkerhetscenter, Azure Active Directory, Identity Protection, Privileged Identity Management, samt möjlighet att läsa Azure Active Directory-inloggningsrapporter och granskningsloggar, och i Office 365 säkerhets- och Efterlevnadscenter. Mer information om behörigheter för Office 365 finns på [behörigheter i Office 365 säkerhets- och Efterlevnadscenter](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

  I | Kan göra
  --- | ---
  [Microsoft 365 Säkerhetscenter](https://protection.microsoft.com) | Visa säkerhetsrelaterade principer för Microsoft 365-tjänster<br>Visa säkerhetshot och aviseringar<br>Visa rapporter
  Identity Protection Center  | Läsa alla säkerhetsrapporter och inställningsinformation för säkerhetsfunktioner<br><ul><li>Skydd mot skräppost<li>Kryptering<li>Skydd mot dataförlust<li>Mot skadlig kod<li>Advanced Threat Protection<li>Mot nätfiske<li>Mailflow regler
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Har skrivskyddad åtkomst till all information som visas i Azure AD PIM: Principer och rapporter för Azure AD-rolltilldelningar security granskar och i framtiden skrivskyddad åtkomst till principdata och rapporter för scenarier utöver Azure AD-rolltilldelning.<br>**Det går inte att** registrera dig för Azure AD PIM eller göra några ändringar. PIM-portalen eller via PowerShell kan någon i den här rollen aktivera ytterligare roller (till exempel Global administratör eller privilegierad Rolladministratör), om användaren är berättigad till dem.
  [Och Efterlevnadscenter för Office 365-säkerhet](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Visa säkerhetsprinciper<br>Visa och undersöka hot<br>Visa rapporter
  Windows Defender ATP och EDR | Visa och undersöka aviseringar
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Vyer användare, enhet, registrering, konfiguration och information om programmet. Det går inte att göra ändringar i Intune.
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Har skrivskyddad behörighet och kan hantera aviseringar
  [Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Visa rekommendationer och aviseringar, visa IPSec-principer security tillstånd, men det går inte att göra ändringar
  [Tjänstehälsa för Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Visa hälsotillståndet för Office 365-tjänster

* **[Tjänstsupportadministratör](#service-support-administrator)**: Användare med den här rollen kan öppna supportbegäranden med Microsoft för Azure och Office 365-tjänster och visa tjänstinstrumentpanelen och meddelandecenter i Azure-portalen och Office 365-administratörsportalen. Mer information på [om Office 365-administratörsroller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras rollen som ”Tjänstsupportadministratör”. Det är ”tjänstadministratör” i den [Azure-portalen](https://portal.azure.com), administrationsportalen för Office 365 och Intune-portalen.

* **[SharePoint Administrator](#sharepoint-service-administrator)**: Användare med den här rollen har globala behörigheter inom Microsoft SharePoint Online när tjänsten finns närvarande, liksom möjligheten att skapa och hantera alla Office 365-grupper, hantera supportbegäranden och kontrollera tjänstens hälsotillstånd. Mer information på [om Office 365-administratörsroller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras rollen som ”SharePoint-tjänstadministratör”. Det är ”SharePoint-administratör” i den [Azure-portalen](https://portal.azure.com).

* **[Skype för företag – / Lync administratör](#lync-service-administrator)**: Användare med den här rollen har globala behörigheter inom Microsoft Skype för företag när tjänsten finns närvarande, samt hantera Skype-specifika användarattribut i Azure Active Directory. Den här rollen ger dessutom möjlighet att hantera supportbegäranden och kontrollera tjänstens hälsotillstånd och komma åt Teams och Skype för Business Admin Center. Kontot måste också ha licens för team eller teamen PowerShell-cmdlet: ar kan köras. Mer information på [om the Skype för företag-administratörsroll](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) och team licensinformation på [Skype för företag och Microsoft Teams-tillägg-licensiering](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

  > [!NOTE]
  > I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras rollen som ”Lync-tjänstadministratör”. Det är ”Skype för Business Administrator” i den [Azure-portalen](https://portal.azure.com/).

* **[Team administratör](#teams-service-administrator)**: Användare i den här rollen kan hantera alla aspekter av Microsoft Teams-arbetsbelastning via Microsoft Teams och Skype för företag-administrationscentret och respektive PowerShell-moduler. Detta inkluderar bland andra delar alla hanteringsverktyg som är relaterade till telefoni, meddelanden, möten och team själva. Den här rollen ger dessutom möjlighet att skapa och hantera alla Office 365-grupper, hantera supportbegäranden och kontrollera tjänstens hälsotillstånd.
  > [!NOTE]
  > I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras rollen som ”team Service-administratör”. Det är ”team administratör” i den [Azure-portalen](https://portal.azure.com).

* **[Teams kommunikation administratör](#teams-communications-administrator)**: Användare i den här rollen kan hantera aspekter av arbetsbelastningen Microsoft Teams rör röst- och telefoni. Detta inkluderar hanteringsverktygen för tilldelning av telefon, röst- och möte principer och fullständig åtkomst till anropet analytics verktygsuppsättningen.

* **[Teams kommunikation supporttekniker](#teams-communications-support-engineer)**: Användare i den här rollen kan felsöka kommunikationsproblem i Microsoft Teams och Skype för företag med hjälp av användaren anrop felsökningsverktygen i Microsoft Teams och Skype för företag-administrationscentret. Användare i den här rollen kan visa fullständiga anropet registrera information för alla deltagare som ingår. Den här rollen har ingen behörighet att visa, skapa eller hantera supportärenden.

* **[Teams kommunikation Support Specialist](#teams-communications-support-specialist)**: Användare i den här rollen kan felsöka kommunikationsproblem i Microsoft Teams och Skype för företag med hjälp av användaren anrop felsökningsverktygen i Microsoft Teams och Skype för företag-administrationscentret. Användare i den här rollen kan bara se användarinformation i anropet för den specifika användaren har sökt. Den här rollen har ingen behörighet att visa, skapa eller hantera supportärenden.

* **[Användaradministratör](#user-account-administrator)**: Användare med den här rollen kan skapa användare och hantera alla aspekter av användare med vissa begränsningar (se nedan). Dessutom kan kan användare med den här rollen skapa och hantera alla grupper. Den här rollen inkluderar även möjligheten att skapa och hantera användarvyer, hantera supportbegäranden och kontrollera tjänstens hälsotillstånd.

  | | |
  | --- | --- |
  |Allmänna behörigheter|<p>Skapa användare och grupper</p><p>Skapa och hantera användarvyer</p><p>Hantera supportärenden för Office|
  |<p>På alla användare, inklusive alla administratörer</p>|<p>Hantera licenser</p><p>Hantera alla användaregenskaper utom User Principal Name</p>
  |Endast på användare som är icke-administratörer eller begränsade administrativa roller i något av följande:<ul><li>Katalogläsare<li>Gäst bjuder in<li>Supportavdelningsadministratör<li>Meddelandecenterläsare<li>Rapportläsare<li>Användaradministratör|<p>Ta bort och återställning</p><p>Inaktivera och aktivera</p><p>Ogiltigförklara uppdatera token</p><p>Hantera egenskaper för alla användare, inklusive användarens huvudnamn</p><p>Återställa lösenord</p><p>Uppdatera (FIDO) enhetsnycklar</p>
  
  <b>Viktiga</b>: Användare med den här rollen kan ändra lösenord för personer som kan ha åtkomst till känslig eller privat information eller kritiska konfiguration i och utanför Azure Active Directory. Ändra lösenordet för en användare kan det innebära att möjlighet att anta användarens identitet och behörigheter. Exempel:
  * Programregistrering och företagsprogram ägare, som kan hantera autentiseringsuppgifterna för appar som de äger. Apparna kan ha privilegierad behörigheter i Azure AD och någon annanstans inte beviljas till administratörer. Den här sökvägen som en användare med rollen kanske kan anta identiteten av ett programmets ägare och sedan ytterligare anta identiteten av ett privilegierat program genom att uppdatera autentiseringsuppgifterna för programmet.
  * Azure-prenumerationsägare, vilka kan ha åtkomst till känslig eller privat information eller kritiska konfiguration i Azure.
  * Säkerhetsgrupper och Office 365-gruppen ägare, vilka kan hantera gruppmedlemskap. Dessa grupper kan bevilja åtkomst till känslig eller privat information eller kritiska konfiguration i Azure AD och andra platser.
  * Administratörer i andra tjänster utanför Azure AD som Exchange Online, Office-säkerhet och efterlevnad Center och personal system.
  * Icke-administratörer som chefer, juridiska ombud och Personal anställda som kan ha åtkomst till känslig eller privat information.

## <a name="role-permissions"></a>Rollbehörigheter
I följande tabeller beskrivs de särskilda behörigheterna i Azure Active Directory för varje roll. Vissa roller kan ha ytterligare behörigheter i Microsoft-tjänster utanför Azure Active Directory.

### <a name="application-administrator"></a>Programadministratör
Kan skapa och hantera alla aspekter av appregistreringar och enterprise-appar.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft.aad.Directory/Applications/Audience/Update | Uppdatera egenskapen application.audience i Azure Active Directory. |
| microsoft.aad.directory/applications/authentication/update | Uppdatera egenskapen applications.authentication i Azure Active Directory. |
| microsoft.aad.directory/applications/basic/update | Uppdatera grundläggande egenskaper för program i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/Create | Skapa program i Azure Active Directory. |
| microsoft.aad.directory/applications/credentials/update | Uppdatera egenskapen applications.credentials i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/delete | Ta bort program i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/owners/Update | Uppdatera egenskapen applications.owners i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/permissions/Update | Uppdatera egenskapen applications.permissions i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/policies/Update | Uppdatera egenskapen applications.policies i Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Skapa appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/read | Läs appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Uppdatera appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Ta bort appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Läs alla egenskaper (inklusive privilegierade egenskaper) på auditLogs i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Läs egenskapen policies.applicationConfiguration i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Uppdatera egenskapen policies.applicationConfiguration i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Skapa principer i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Ta bort principer i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Läs egenskapen policies.applicationConfiguration i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Uppdatera egenskapen policies.applicationConfiguration i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Läs egenskapen policies.applicationConfiguration i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Uppdatera egenskapen servicePrincipals.appRoleAssignedTo i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Uppdatera egenskapen servicePrincipals.appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Uppdatera egenskapen servicePrincipals.audience i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Uppdatera egenskapen servicePrincipals.authentication i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Uppdatera grundläggande egenskaper för ServicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Skapa servicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Uppdatera egenskapen servicePrincipals.credentials i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Ta bort servicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Uppdatera egenskapen servicePrincipals.owners i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Uppdatera egenskapen servicePrincipals.permissions i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Uppdatera egenskapen servicePrincipals.policies i Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Läsa alla egenskaper (inklusive privilegierade egenskaper) på signInReports i Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbegäranden. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="application-developer"></a>Programutvecklare
Kan skapa programregistreringar oberoende av den ”användare kan registrera program' inställningen.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | Skapa program i Azure Active Directory. Skaparen läggs till som första ägare och det skapade objektet räknas mot Skaparens kvot på 250 skapade objekt. |
| microsoft.aad.directory/appRoleAssignments/createAsOwner | Skapa appRoleAssignments i Azure Active Directory. Skaparen läggs till som första ägare och det skapade objektet räknas mot Skaparens kvot på 250 skapade objekt. |
| microsoft.aad.directory/oAuth2PermissionGrants/createAsOwner | Skapa oAuth2PermissionGrants i Azure Active Directory. Skaparen läggs till som första ägare och det skapade objektet räknas mot Skaparens kvot på 250 skapade objekt. |
| microsoft.aad.directory/servicePrincipals/createAsOwner | Skapa servicePrincipals i Azure Active Directory. Skaparen läggs till som första ägare och det skapade objektet räknas mot Skaparens kvot på 250 skapade objekt. |

### <a name="authentication-administrator"></a>Autentiseringsadministratör
Kan visa, ange och återställa information om autentisering för alla icke-administratörsanvändare.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidera alla tokens för användaruppdatering i Azure Active Directory. |
| microsoft.aad.directory/users/strongAuthentication/update | Uppdatera starka autentiseringsegenskaper som information om MFA-autentiseringsuppgifter. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbegäranden. |
| microsoft.office365.webPortal/allEntities/basic/read | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="billing-administrator"></a>Faktureringsadministratör
Kan utföra vanliga faktureringsrelaterade uppgifter som uppdatering av betalningsinformation.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/organization/basic/update | Uppdatera grundläggande egenskaper om organisation i Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbegäranden. |
| microsoft.commerce.billing/allEntities/allTasks | Hantera alla aspekter av Office 365-fakturering. |
| microsoft.office365.webPortal/allEntities/basic/read | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="desktop-analytics-administrator"></a>Desktop Analytics-administratör
Kan komma åt och hantera skrivbordshanteringsverktyg och tjänster, inklusive Intune.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbegäranden. |
| microsoft.office365.webPortal/allEntities/basic/read | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Hantera alla aspekter av Desktop Analytics. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="cloud-application-administrator"></a>Molnprogramadministratör
Kan skapa och hantera alla aspekter av appregistreringar och enterprise-appar förutom App Proxy.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft.aad.Directory/Applications/Audience/Update | Uppdatera egenskapen application.audience i Azure Active Directory. |
| microsoft.aad.directory/applications/authentication/update | Uppdatera egenskapen applications.authentication i Azure Active Directory. |
| microsoft.aad.directory/applications/basic/update | Uppdatera grundläggande egenskaper för program i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/Create | Skapa program i Azure Active Directory. |
| microsoft.aad.directory/applications/credentials/update | Uppdatera egenskapen applications.credentials i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/delete | Ta bort program i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/owners/Update | Uppdatera egenskapen applications.owners i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/permissions/Update | Uppdatera egenskapen applications.permissions i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/policies/Update | Uppdatera egenskapen applications.policies i Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Skapa appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Uppdatera appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Ta bort appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Läs alla egenskaper (inklusive privilegierade egenskaper) på auditLogs i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Skapa principer i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Läs egenskapen policies.applicationConfiguration i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Uppdatera egenskapen policies.applicationConfiguration i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Ta bort principer i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Läs egenskapen policies.applicationConfiguration i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Uppdatera egenskapen policies.applicationConfiguration i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Läs egenskapen policies.applicationConfiguration i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Uppdatera egenskapen servicePrincipals.appRoleAssignedTo i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Uppdatera egenskapen servicePrincipals.appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Uppdatera egenskapen servicePrincipals.audience i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Uppdatera egenskapen servicePrincipals.authentication i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Uppdatera grundläggande egenskaper för ServicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Skapa servicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Uppdatera egenskapen servicePrincipals.credentials i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Ta bort servicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Uppdatera egenskapen servicePrincipals.owners i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Uppdatera egenskapen servicePrincipals.permissions i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Uppdatera egenskapen servicePrincipals.policies i Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Läsa alla egenskaper (inklusive privilegierade egenskaper) på signInReports i Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbegäranden. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="cloud-device-administrator"></a>Molnenhetsadministratör
Fullständig åtkomst för att hantera enheter i Azure AD.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Läs alla egenskaper (inklusive privilegierade egenskaper) på auditLogs i Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Läs egenskapen devices.bitLockerRecoveryKeys i Azure Active Directory. |
| Microsoft.aad.Directory/Devices/delete | Ta bort enheter i Azure Active Directory. |
| Microsoft.aad.Directory/Devices/disable | Inaktivera enheter i Azure Active Directory. |
| microsoft.aad.directory/devices/enable | Aktivera enheter i Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Läsa alla egenskaper (inklusive privilegierade egenskaper) på signInReports i Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läs och konfigurera Azure Service Health. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |

### <a name="company-administrator"></a>Företagsadministratör
Kan hantera alla aspekter av Azure AD och Microsoft-tjänster som använde Azure AD-identiteter.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Skapa och ta bort alla resurser och läs och uppdatera standardegenskaper i microsoft.aad.cloudAppSecurity. |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | Skapa och ta bort administrativeUnits och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/applications/allProperties/allTasks | Skapa och ta bort program och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/allProperties/allTasks | Skapa och ta bort appRoleAssignments och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Läs alla egenskaper (inklusive privilegierade egenskaper) på auditLogs i Azure Active Directory. |
| microsoft.aad.directory/contacts/allProperties/allTasks | Skapa och ta bort kontakter och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/contracts/allProperties/allTasks | Skapa och ta bort kontrakt och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/devices/allProperties/allTasks | Skapa och ta bort enheter och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/allProperties/allTasks | Skapa och ta bort directoryRoles och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/directoryRoleTemplates/allProperties/allTasks | Skapa och ta bort DirectoryRoleTemplates och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/domains/allProperties/allTasks | Skapa och ta bort domäner och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/groups/allProperties/allTasks | Skapa och ta bort grupper och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/groupSettings/allProperties/allTasks | Skapa och ta bort groupSettings och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/allProperties/allTasks | Skapa och ta bort groupSettingTemplates och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/loginTenantBranding/allProperties/allTasks | Skapa och ta bort loginTenantBranding och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/allProperties/allTasks | Skapa och ta bort oAuth2PermissionGrants och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/organization/allProperties/allTasks | Skapa och ta bort organisation och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/policies/allProperties/allTasks | Skapa och ta bort principer och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | Skapa och ta bort roleAssignments och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Skapa och ta bort roleDefinitions och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/scopedRoleMemberships/allProperties/allTasks | Skapa och ta bort scopedRoleMemberships och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/serviceAction/activateService | Tjänståtgärden Activateservice går att utföra i Azure Active Directory |
| microsoft.aad.directory/serviceAction/disableDirectoryFeature | Tjänståtgärden Disabledirectoryfeature går att utföra i Azure Active Directory |
| microsoft.aad.directory/serviceAction/enableDirectoryFeature | Tjänståtgärden Enabledirectoryfeature går att utföra i Azure Active Directory |
| microsoft.aad.directory/serviceAction/getAvailableExtentionProperties | Tjänståtgärden Getavailableextentionproperties går att utföra i Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/allProperties/allTasks | Skapa och ta bort servicePrincipals och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Läsa alla egenskaper (inklusive privilegierade egenskaper) på signInReports i Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/allProperties/allTasks | Skapa och ta bort subscribedSkus och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/users/allProperties/allTasks | Skapa och ta bort användare och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Utföra alla åtgärder i Azure AD Connect. |
| microsoft.aad.identityProtection/allEntities/allTasks | Skapa och ta bort alla resurser och läs och uppdatera standardegenskaperna i microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Läs alla resurser i microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Läs alla resurser i microsoft.azure.advancedThreatProtection. |
| microsoft.azure.informationProtection/allEntities/allTasks | Hantera alla aspekter av Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbegäranden. |
| microsoft.commerce.billing/allEntities/allTasks | Hantera alla aspekter av Office 365-fakturering. |
| microsoft.intune/allEntities/allTasks | Hantera alla aspekter av Intune. |
| microsoft.office365.complianceManager/allEntities/allTasks | Hantera alla aspekter av Office 365 Efterlevnadshanteraren |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Hantera alla aspekter av Desktop Analytics. |
| microsoft.office365.exchange/allEntities/allTasks | Hantera alla aspekter av Exchange Online. |
| microsoft.office365.lockbox/allEntities/allTasks | Hantera alla aspekter av Office 365 Customer Lockbox |
| microsoft.office365.messageCenter/messages/read | Läs meddelanden i microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Läs securityMessages i microsoft.office365.messageCenter. |
| microsoft.office365.protectionCenter/allEntities/allTasks | Hantera alla aspekter av Office 365 Säkerhetscenter. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Skapa och ta bort alla resurser och läs och uppdatera standardegenskaper i microsoft.office365.securityComplianceCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.sharepoint/allEntities/allTasks | Skapa och ta bort alla resurser och läs och uppdatera standardegenskaperna i microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Hantera alla aspekter av Skype för företag – Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |
| microsoft.office365.usageReports/allEntities/read | Läs Office 365-användningsrapporter. |
| microsoft.office365.webPortal/allEntities/basic/read | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Hantera alla aspekter av Dynamics 365. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Kan hantera alla aspekter av Power BI. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Läs alla resurser i microsoft.windows.defenderAdvancedThreatProtection. |

### <a name="compliance-administrator"></a>Efterlevnadsadministratör
Kan läsa och hantera efterlevnadskonfiguration och rapporter i Azure AD och Office 365.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbegäranden. |
| microsoft.office365.webPortal/allEntities/basic/read | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.complianceManager/allEntities/allTasks | Hantera alla aspekter av Office 365 Efterlevnadshanteraren |
| microsoft.office365.exchange/allEntities/allTasks | Hantera alla aspekter av Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.sharepoint/allEntities/allTasks | Skapa och ta bort alla resurser och läs och uppdatera standardegenskaperna i microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Hantera alla aspekter av Skype för företag – Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="conditional-access-administrator"></a>Administratör för villkorsstyrd åtkomst
Kan hantera funktioner för villkorsstyrd åtkomst.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/policies/conditionalAccess/basic/read | Läs egenskapen policies.conditionalAccess i Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/basic/update | Uppdatera egenskapen policies.conditionalAccess i Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/create | Skapa principer i Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/delete | Ta bort principer i Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/read | Läs egenskapen policies.conditionalAccess i Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/update | Uppdatera egenskapen policies.conditionalAccess i Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/policiesAppliedTo/read | Läs egenskapen policies.conditionalAccess i Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/tenantDefault/update | Uppdatera egenskapen policies.conditionalAccess i Azure Active Directory. |

### <a name="crm-service-administrator"></a>Administratör för CRM-tjänst
Kan hantera alla aspekter av Dynamics 365-produkten.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbegäranden. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Hantera alla aspekter av Dynamics 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="customer-lockbox-access-approver"></a>Godkännare av åtkomst till Customer LockBox
Kan godkänna förfrågningar till Microsoft Support om att få åtkomst till kundens organisationsdata.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.lockbox/allEntities/allTasks | Hantera alla aspekter av Office 365 Customer Lockbox |

### <a name="device-administrators"></a>Enhetsadministratörer
Användare som tilldelats den här rollen har lagts till i gruppen lokala administratörer på Azure AD-anslutna enheter.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/groupSettings/basic/read | Läs grundläggande egenskaper om groupSettings i Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Läs grundläggande egenskaper om groupsettingsTemplates i Azure Active Directory. |

### <a name="directory-readers"></a>Katalogläsare
Kan läsa grundläggande kataloginformation. För att bevilja åtkomst till program inte avsedd för användare.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/basic/read | Läs grundläggande på administrativeUnits i Azure Active Directory. |
| microsoft.aad.directory/administrativeUnits/members/read | Läs egenskapen administrativeUnits.members i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/Basic/Read | Läs grundläggande egenskaper om program i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/owners/Read | Läs egenskapen applications.owners i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/policies/Read | Läs egenskapen applications.policies i Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/Basic/Read | Läs grundläggande egenskaper om kontakter i Azure Active Directory. |
| microsoft.aad.directory/contacts/memberOf/read | Läs egenskapen contacts.memberOf i Azure Active Directory. |
| Microsoft.aad.Directory/contracts/Basic/Read | Läs grundläggande egenskaper om kontrakt i Azure Active Directory. |
| Microsoft.aad.Directory/Devices/Basic/Read | Läs grundläggande egenskaper om enheter i Azure Active Directory. |
| microsoft.aad.directory/devices/memberOf/read | Läs egenskapen devices.memberOf i Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/read | Läs egenskapen devices.registeredOwners i Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/read | Läs egenskapen devices.registeredUsers i Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/basic/read | Läs grundläggande egenskaper om directoryRoles i Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/eligibleMembers/read | Läs egenskapen directoryRoles.eligibleMembers i Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/members/read | Läs egenskapen directoryRoles.members i Azure Active Directory. |
| Microsoft.aad.Directory/Domains/Basic/Read | Läs grundläggande egenskaper om domäner i Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/read | Läs egenskapen groups.appRoleAssignments i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Basic/Read | Läs grundläggande egenskaper om grupper i Azure Active Directory. |
| microsoft.aad.directory/groups/memberOf/read | Läs egenskapen groups.memberOf i Azure Active Directory. |
| Microsoft.aad.Directory/groups/members/Read | Läs egenskapen groups.members i Azure Active Directory. |
| Microsoft.aad.Directory/groups/owners/Read | Läs egenskapen groups.owners i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Settings/Read | Läs egenskapen groups.settings i Azure Active Directory. |
| microsoft.aad.directory/groupSettings/basic/read | Läs grundläggande egenskaper om groupSettings i Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Läs grundläggande egenskaper om groupsettingsTemplates i Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/basic/read | Läs grundläggande egenskaper om oAuth2PermissionGrants i Azure Active Directory. |
| Microsoft.aad.Directory/Organization/Basic/Read | Läs grundläggande egenskaper om organisation i Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/read | Läs egenskapen organization.trustedCAsForPasswordlessAuth i Azure Active Directory. |
| microsoft.aad.directory/roleAssignments/basic/read | Läs grundläggande egenskaper om roleAssignments i Azure Active Directory. |
| microsoft.aad.directory/roleDefinitions/basic/read | Läs grundläggande egenskaper om roleDefinitions i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Läs egenskapen servicePrincipals.appRoleAssignedTo i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Läs egenskapen servicePrincipals.appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/read | Läs grundläggande egenskaper om servicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Läs egenskapen servicePrincipals.memberOf i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Läs egenskapen servicePrincipals.oAuth2PermissionGrants i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Läs servicePrincipals.ownedObjects i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Läs servicePrincipals.owners i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Läs egenskapen servicePrincipals.policies i Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/basic/read | Läs grundläggande egenskaper om subscribedSkus i Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/read | Läs egenskapen users.appRoleAssignments i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Basic/Read | Läs grundläggande egenskaper om användare i Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Läs egenskapen users.directReports i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Manager/Read | Läs egenskapen users.manager i Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Läs egenskapen users.memberOf i Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Läs egenskapen users.oAuth2PermissionGrants i Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Läs users.ownedDevices i Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Läs egenskapen users.ownedObjects i Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Läs egenskapen users.registeredDevices i Azure Active Directory. |

### <a name="directory-synchronization-accounts"></a>Konton för katalogen katalogsynkronisering
Endast används av Azure AD Connect-tjänsten.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/organization/dirSync/update | Uppdatera egenskapen organization.dirSync i Azure Active Directory. |
| Microsoft.aad.Directory/policies/Create | Skapa principer i Azure Active Directory. |
| Microsoft.aad.Directory/policies/delete | Ta bort principer i Azure Active Directory. |
| Microsoft.aad.Directory/policies/Basic/Read | Läs grundläggande egenskaper om principer i Azure Active Directory. |
| Microsoft.aad.Directory/policies/Basic/Update | Uppdatera grundläggande egenskaper för principer i Azure Active Directory. |
| Microsoft.aad.Directory/policies/owners/Read | Läs egenskapen policies.owners i Azure Active Directory. |
| Microsoft.aad.Directory/policies/owners/Update | Uppdatera egenskapen policies.owners i Azure Active Directory. |
| microsoft.aad.directory/policies/policiesAppliedTo/read | Läs egenskapen policies.policiesAppliedTo i Azure Active Directory. |
| microsoft.aad.directory/policies/tenantDefault/update | Uppdatera egenskapen policies.tenantDefault i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Läs egenskapen servicePrincipals.appRoleAssignedTo i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Uppdatera egenskapen servicePrincipals.appRoleAssignedTo i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Läs egenskapen servicePrincipals.appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Uppdatera egenskapen servicePrincipals.appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Uppdatera egenskapen servicePrincipals.audience i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Uppdatera egenskapen servicePrincipals.authentication i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/read | Läs grundläggande egenskaper om servicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Uppdatera grundläggande egenskaper för ServicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Skapa servicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Uppdatera egenskapen servicePrincipals.credentials i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Läs egenskapen servicePrincipals.memberOf i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Läs egenskapen servicePrincipals.oAuth2PermissionGrants i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Läs servicePrincipals.owners i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Uppdatera egenskapen servicePrincipals.owners i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Läs servicePrincipals.ownedObjects i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Uppdatera egenskapen servicePrincipals.permissions i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Läs egenskapen servicePrincipals.policies i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Uppdatera egenskapen servicePrincipals.policies i Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Utföra alla åtgärder i Azure AD Connect. |

### <a name="directory-writers"></a>Katalogskrivare
Kan läsa och skriva grundläggande kataloginformation. För att bevilja åtkomst till program inte avsedd för användare.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft.aad.Directory/groups/Create | Skapa grupper i Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Skapa grupper i Azure Active Directory. Skaparen läggs till som första ägare och det skapade objektet räknas mot Skaparens kvot på 250 skapade objekt. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Uppdatera egenskapen groups.appRoleAssignments i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Basic/Update | Uppdatera grundläggande egenskaper om grupper i Azure Active Directory. |
| Microsoft.aad.Directory/groups/members/Update | Uppdatera egenskapen groups.members i Azure Active Directory. |
| Microsoft.aad.Directory/groups/owners/Update | Uppdatera egenskapen groups.owners i Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Uppdatera egenskapen groups.settings i Azure Active Directory. |
| microsoft.aad.directory/groupSettings/basic/update | Uppdatera grundläggande egenskaper om groupSettings i Azure Active Directory. |
| microsoft.aad.directory/groupSettings/create | Skapa groupSettings i Azure Active Directory. |
| microsoft.aad.directory/groupSettings/delete | Ta bort groupSettings i Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Uppdatera egenskapen users.appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Hantera licenser för användare i Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Uppdatera grundläggande egenskaper för användare i Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidera alla tokens för användaruppdatering i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Manager/Update | Uppdatera egenskapen users.manager i Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Uppdatera egenskapen users.userPrincipalName i Azure Active Directory. |

### <a name="exchange-service-administrator"></a>Exchange Service-administratör
Kan hantera alla aspekter av Exchange-produkten.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Uppdatera egenskapen groups.unified i Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Uppdatera grundläggande egenskaper för Office 365-grupper. |
| microsoft.aad.directory/groups/unified/create | Skapa Office 365-grupper. |
| microsoft.aad.directory/groups/unified/delete | Ta bort Office 365-grupper. |
| microsoft.aad.directory/groups/unified/members/update | Uppdatera medlemskap i Office 365-grupper. |
| microsoft.aad.directory/groups/unified/owners/update | Uppdatera ägarskap för Office 365-grupper. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbegäranden. |
| microsoft.office365.webPortal/allEntities/basic/read | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.exchange/allEntities/allTasks | Hantera alla aspekter av Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="guest-inviter"></a>Gäst bjuder in
Kan bjuda in gästanvändare oberoende av inställningen medlemmar kan bjuda in gäster.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/users/appRoleAssignments/read | Läs egenskapen users.appRoleAssignments i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Basic/Read | Läs grundläggande egenskaper om användare i Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Läs egenskapen users.directReports i Azure Active Directory. |
| microsoft.aad.directory/users/inviteGuest | Bjud in gästanvändare i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Manager/Read | Läs egenskapen users.manager i Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Läs egenskapen users.memberOf i Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Läs egenskapen users.oAuth2PermissionGrants i Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Läs users.ownedDevices i Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Läs egenskapen users.ownedObjects i Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Läs egenskapen users.registeredDevices i Azure Active Directory. |

### <a name="helpdesk-administrator"></a>Supportavdelningsadministratör
Kan återställa lösenord för icke-administratörer och Helpdeks-administratörer.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Läs egenskapen devices.bitLockerRecoveryKeys i Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidera alla tokens för användaruppdatering i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Password/Update | Uppdatera lösenord för alla användare i Azure Active Directory. Se onlinedokumentationen för mer information. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbegäranden. |
| microsoft.office365.webPortal/allEntities/basic/read | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="information-protection-administrator"></a>Information Protection-administratör
Kan hantera alla aspekter av Azure Information Protection-produkten.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Hantera alla aspekter av Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbegäranden. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="intune-service-administrator"></a>Administratör för Intune-tjänsten
Kan hantera alla aspekter av Intune-produkten.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft.aad.Directory/Contacts/Basic/Update | Uppdatera grundläggande egenskaper för kontakter i Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/Create | Skapa kontakter i Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/delete | Ta bort kontakter i Azure Active Directory. |
| Microsoft.aad.Directory/Devices/Basic/Update | Uppdatera grundläggande egenskaper om enheter i Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Läs egenskapen devices.bitLockerRecoveryKeys i Azure Active Directory. |
| Microsoft.aad.Directory/Devices/Create | Skapa enheter i Azure Active Directory. |
| Microsoft.aad.Directory/Devices/delete | Ta bort enheter i Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/update | Uppdatera egenskapen devices.registeredOwners i Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/update | Uppdatera egenskapen devices.registeredUsers i Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Uppdatera egenskapen groups.appRoleAssignments i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Basic/Update | Uppdatera grundläggande egenskaper om grupper i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Create | Skapa grupper i Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Skapa grupper i Azure Active Directory. Skaparen läggs till som första ägare och det skapade objektet räknas mot Skaparens kvot på 250 skapade objekt. |
| Microsoft.aad.Directory/groups/delete | Ta bort grupper i Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Läs egenskapen groups.hiddenMembers i Azure Active Directory. |
| Microsoft.aad.Directory/groups/members/Update | Uppdatera egenskapen groups.members i Azure Active Directory. |
| Microsoft.aad.Directory/groups/owners/Update | Uppdatera egenskapen groups.owners i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Restore | Återställ grupper i Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Uppdatera egenskapen groups.settings i Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Uppdatera egenskapen users.appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Uppdatera grundläggande egenskaper för användare i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Manager/Update | Uppdatera egenskapen users.manager i Azure Active Directory. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbegäranden. |
| microsoft.intune/allEntities/allTasks | Hantera alla aspekter av Intune. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |
| microsoft.office365.webPortal/allEntities/basic/read | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |

### <a name="license-administrator"></a>Licensadministratör
Kan hantera produktlicenser på användare och grupper.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/users/assignLicense | Hantera licenser för användare i Azure Active Directory. |
| microsoft.aad.directory/users/usageLocation/update | Uppdatera egenskapen users.usageLocation i Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läs och konfigurera Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |

### <a name="lync-service-administrator"></a>Lync-tjänstadministratör
Kan hantera alla aspekter av Skype för Business-produkten.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbegäranden. |
| microsoft.office365.webPortal/allEntities/basic/read | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Hantera alla aspekter av Skype för företag – Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="message-center-reader"></a>Meddelandecenterläsare
Kan endast läsa meddelanden och uppdateringar avseende sin organisation i meddelandecenter för Office 365. 

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.messageCenter/messages/read | Läs meddelanden i microsoft.office365.messageCenter. |

### <a name="partner-tier1-support"></a>Partnersupport, nivå 1
Använd inte – inte avsett för allmänt bruk.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft.aad.Directory/Contacts/Basic/Update | Uppdatera grundläggande egenskaper för kontakter i Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/Create | Skapa kontakter i Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/delete | Ta bort kontakter i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Create | Skapa grupper i Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Skapa grupper i Azure Active Directory. Skaparen läggs till som första ägare och det skapade objektet räknas mot Skaparens kvot på 250 skapade objekt. |
| Microsoft.aad.Directory/groups/members/Update | Uppdatera egenskapen groups.members i Azure Active Directory. |
| Microsoft.aad.Directory/groups/owners/Update | Uppdatera egenskapen groups.owners i Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Uppdatera egenskapen users.appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Hantera licenser för användare i Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Uppdatera grundläggande egenskaper för användare i Azure Active Directory. |
| Microsoft.aad.Directory/Users/delete | Ta bort användare i Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidera alla tokens för användaruppdatering i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Manager/Update | Uppdatera egenskapen users.manager i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Password/Update | Uppdatera lösenord för alla användare i Azure Active Directory. Se onlinedokumentationen för mer information. |
| Microsoft.aad.Directory/Users/Restore | Återställa borttagna användare i Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Uppdatera egenskapen users.userPrincipalName i Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbegäranden. |
| microsoft.office365.webPortal/allEntities/basic/read | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="partner-tier2-support"></a>Partnersupport, nivå 2
Använd inte – inte avsett för allmänt bruk.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft.aad.Directory/Contacts/Basic/Update | Uppdatera grundläggande egenskaper för kontakter i Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/Create | Skapa kontakter i Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/delete | Ta bort kontakter i Azure Active Directory. |
| microsoft.aad.directory/domains/allTasks | Skapa och ta bort domäner och läs och uppdatera standardegenskaper i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Create | Skapa grupper i Azure Active Directory. |
| Microsoft.aad.Directory/groups/delete | Ta bort grupper i Azure Active Directory. |
| Microsoft.aad.Directory/groups/members/Update | Uppdatera egenskapen groups.members i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Restore | Återställ grupper i Azure Active Directory. |
| microsoft.aad.directory/organization/basic/update | Uppdatera grundläggande egenskaper om organisation i Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Uppdatera egenskapen users.appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Hantera licenser för användare i Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Uppdatera grundläggande egenskaper för användare i Azure Active Directory. |
| Microsoft.aad.Directory/Users/delete | Ta bort användare i Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidera alla tokens för användaruppdatering i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Manager/Update | Uppdatera egenskapen users.manager i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Password/Update | Uppdatera lösenord för alla användare i Azure Active Directory. Se onlinedokumentationen för mer information. |
| Microsoft.aad.Directory/Users/Restore | Återställa borttagna användare i Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Uppdatera egenskapen users.userPrincipalName i Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbegäranden. |
| microsoft.office365.webPortal/allEntities/basic/read | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="power-bi-service-administrator"></a>Power BI-tjänstadministratör
Kan hantera alla aspekter av Power BI-produkten.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbegäranden. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Kan hantera alla aspekter av Power BI. |
| microsoft.office365.webPortal/allEntities/basic/read | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="privileged-authentication-administrator"></a>Privilegierad autentiseringsadministratör
Har tillåtelse att visa, konfigurera och återställa autentiseringsmetodsinformation för alla användare (administratörer och icke-administratörer).

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidera alla tokens för användaruppdatering i Azure Active Directory. |
| microsoft.aad.directory/users/strongAuthentication/update | Uppdatera starka autentiseringsegenskaper som information om MFA-autentiseringsuppgifter. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbegäranden. |
| microsoft.office365.webPortal/allEntities/basic/read | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="privileged-role-administrator"></a>Privilegierad rolladministratör
Kan hantera rolltilldelningar i Azure AD och alla aspekter av Privileged Identity Management.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/directoryRoles/update | Uppdatera directoryRoles i Azure Active Directory. |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Skapa och ta bort alla resurser och läs och uppdatera standardegenskaperna i microsoft.aad.privilegedIdentityManagement. |

### <a name="reports-reader"></a>Rapportläsare
Kan läsa inloggnings- och granskningsrapporter.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Läs alla egenskaper (inklusive privilegierade egenskaper) på auditLogs i Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Läsa alla egenskaper (inklusive privilegierade egenskaper) på signInReports i Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läs och konfigurera Azure Service Health. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.usageReports/allEntities/read | Läs Office 365-användningsrapporter. |

### <a name="security-administrator"></a>Säkerhetsadministratör
Kan läsa säkerhetsinformation och rapporter och hantera konfigurationen i Azure AD och Office 365.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft.aad.Directory/Applications/policies/Update | Uppdatera egenskapen applications.policies i Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Läs alla egenskaper (inklusive privilegierade egenskaper) på auditLogs i Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Läs egenskapen devices.bitLockerRecoveryKeys i Azure Active Directory. |
| Microsoft.aad.Directory/policies/Basic/Update | Uppdatera grundläggande egenskaper för principer i Azure Active Directory. |
| Microsoft.aad.Directory/policies/Create | Skapa principer i Azure Active Directory. |
| Microsoft.aad.Directory/policies/delete | Ta bort principer i Azure Active Directory. |
| Microsoft.aad.Directory/policies/owners/Update | Uppdatera egenskapen policies.owners i Azure Active Directory. |
| microsoft.aad.directory/policies/tenantDefault/update | Uppdatera egenskapen policies.tenantDefault i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Uppdatera egenskapen servicePrincipals.policies i Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Läsa alla egenskaper (inklusive privilegierade egenskaper) på signInReports i Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Läs alla resurser i microsoft.aad.identityProtection. |
| microsoft.aad.identityProtection/allEntities/update | Uppdatera alla resurser i microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Läs alla resurser i microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läs och konfigurera Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.protectionCenter/allEntities/read | Läsa alla aspekter av Office 365-säkerhetscenter. |
| microsoft.office365.protectionCenter/allEntities/update | Uppdatera alla resurser i microsoft.office365.protectionCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |

### <a name="security-reader"></a>Säkerhetsläsare
Kan läsa säkerhetsinformation och rapporter i Azure AD och Office 365.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Läs alla egenskaper (inklusive privilegierade egenskaper) på auditLogs i Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Läs egenskapen devices.bitLockerRecoveryKeys i Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Läsa alla egenskaper (inklusive privilegierade egenskaper) på signInReports i Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Läs alla resurser i microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Läs alla resurser i microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läs och konfigurera Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.protectionCenter/allEntities/read | Läsa alla aspekter av Office 365-säkerhetscenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |

### <a name="service-support-administrator"></a>Tjänstesupport-administratör
Kan läsa information om Service Health och hantera supportärenden.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbegäranden. |
| microsoft.office365.webPortal/allEntities/basic/read | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="sharepoint-service-administrator"></a>SharePoint-tjänstadministratör
Kan hantera alla aspekter av SharePoint-tjänsten.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Uppdatera egenskapen groups.unified i Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Uppdatera grundläggande egenskaper för Office 365-grupper. |
| microsoft.aad.directory/groups/unified/create | Skapa Office 365-grupper. |
| microsoft.aad.directory/groups/unified/delete | Ta bort Office 365-grupper. |
| microsoft.aad.directory/groups/unified/members/update | Uppdatera medlemskap i Office 365-grupper. |
| microsoft.aad.directory/groups/unified/owners/update | Uppdatera ägarskap för Office 365-grupper. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbegäranden. |
| microsoft.office365.webPortal/allEntities/basic/read | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.sharepoint/allEntities/allTasks | Skapa och ta bort alla resurser och läs och uppdatera standardegenskaperna i microsoft.office365.sharepoint. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="teams-communications-administrator"></a>Teams Communications-administratör
Kan hantera anrops- och mötesfunktioner i Microsoft Teams-tjänsten.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbegäranden. |
| microsoft.office365.webPortal/allEntities/basic/read | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |
| microsoft.office365.usageReports/allEntities/read | Läs Office 365-användningsrapporter. |

### <a name="teams-communications-support-engineer"></a>Teams Communications-supporttekniker
Kan felsöka kommunikationsproblem i grupper med hjälp av avancerade verktyg.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läs och konfigurera Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |

### <a name="teams-communications-support-specialist"></a>Teams Communications-supportspecialist
Kan felsöka kommunikationsproblem i grupper med hjälp av grundläggande verktyg.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läs och konfigurera Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |

### <a name="teams-service-administrator"></a>Teams-tjänstadministratör
Kan hantera Microsoft Teams-tjänsten.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/groups/hiddenMembers/read | Läs egenskapen groups.hiddenMembers i Azure Active Directory. |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Uppdatera egenskapen groups.unified i Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Uppdatera grundläggande egenskaper för Office 365-grupper. |
| microsoft.aad.directory/groups/unified/create | Skapa Office 365-grupper. |
| microsoft.aad.directory/groups/unified/delete | Ta bort Office 365-grupper. |
| microsoft.aad.directory/groups/unified/members/update | Uppdatera medlemskap i Office 365-grupper. |
| microsoft.aad.directory/groups/unified/owners/update | Uppdatera ägarskap för Office 365-grupper. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbegäranden. |
| microsoft.office365.webPortal/allEntities/basic/read | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |
| microsoft.office365.usageReports/allEntities/read | Läs Office 365-användningsrapporter. |

### <a name="user-administrator"></a>Användaradministratör
Kan hantera alla aspekter av användare och grupper, inklusive återställning av lösenord för begränsade administratörer.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/appRoleAssignments/create | Skapa appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Ta bort appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Uppdatera appRoleAssignments i Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/Basic/Update | Uppdatera grundläggande egenskaper för kontakter i Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/Create | Skapa kontakter i Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/delete | Ta bort kontakter i Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Uppdatera egenskapen groups.appRoleAssignments i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Basic/Update | Uppdatera grundläggande egenskaper om grupper i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Create | Skapa grupper i Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Skapa grupper i Azure Active Directory. Skaparen läggs till som första ägare och det skapade objektet räknas mot Skaparens kvot på 250 skapade objekt. |
| Microsoft.aad.Directory/groups/delete | Ta bort grupper i Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Läs egenskapen groups.hiddenMembers i Azure Active Directory. |
| Microsoft.aad.Directory/groups/members/Update | Uppdatera egenskapen groups.members i Azure Active Directory. |
| Microsoft.aad.Directory/groups/owners/Update | Uppdatera egenskapen groups.owners i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Restore | Återställ grupper i Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Uppdatera egenskapen groups.settings i Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Uppdatera egenskapen users.appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Hantera licenser för användare i Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Uppdatera grundläggande egenskaper för användare i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Create | Skapa användare i Azure Active Directory. |
| Microsoft.aad.Directory/Users/delete | Ta bort användare i Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidera alla tokens för användaruppdatering i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Manager/Update | Uppdatera egenskapen users.manager i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Password/Update | Uppdatera lösenord för alla användare i Azure Active Directory. Se onlinedokumentationen för mer information. |
| Microsoft.aad.Directory/Users/Restore | Återställa borttagna användare i Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Uppdatera egenskapen users.userPrincipalName i Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbegäranden. |
| microsoft.office365.webPortal/allEntities/basic/read | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

## <a name="role-template-ids"></a>Mallen för ID: N

Mallen för ID: N används främst av Graph API eller PowerShell-användare.

Graph displayName | Visningsnamn för Azure portal | directoryRoleTemplateId
----------------- | ------------------------- | -------------------------
Programadministratör | Programadministratör | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
Programutvecklare | Programutvecklare | CF1C38E5-3621-4004-A7CB-879624DCED7C
Faktureringsadministratör | Faktureringsadministratör | b0f54661-2d74-4c50-afa3-1ec803f12efe
Desktop Analytics-administratör | Desktop Analytics-administratör | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
Molnprogramadministratör | Molnprogramadministratör | 158c047a-c907-4556-b7ef-446551a6b5f7
Molnenhetsadministratör | Molnenhetsadministratör | 7698a772-787b-4ac8-901f-60d6b08affd2
Företagsadministratör | Global administratör | 62e90394-69f5-4237-9190-012177145e10
Efterlevnadsadministratör | Efterlevnadsadministratör | 17315797-102d-40b4-93e0-432062caca18
Administratör för villkorsstyrd åtkomst | Administratör för villkorsstyrd åtkomst | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
Administratör för CRM-tjänst | Dynamics 365-administratör | 44367163-eba1-44c3-98af-f5787879f96a
Godkännare av åtkomst till Customer LockBox | Customer Lockbox åtkomst godkännaren | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
Enhetsadministratörer | Enhetsadministratörer | 9f06204d-73c1-4d4c-880a-6edb90606fd8
Enhetskoppling | Enhetskoppling | 9c094953-4995-41c8-84c8-3ebb9b32c93f
Enhetshanterare | Enhetshanterare | 2b499bcd-da44-4968-8aec-78e1674fa64d
Användare av enheter | Användare av enheter | d405c6df-0af8-4e3b-95e4-4d06e542189e
Katalogläsare | Katalogläsare | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
Konton för katalogen katalogsynkronisering | Konton för katalogsynkronisering | d29b2b05-8046-44ba-8758-1e26182fcf32
Katalogskrivare | Katalogskrivare | 9360feb5-f418-4baa-8175-e2a00bac4301
Exchange Service-administratör | Exchange-administratör | 29232cdf-9323-42fd-ade2-1d097af3e4de
Gäst bjuder in | Gästinbjudare | 95e79109-95c0-4d8e-aee3-d01accf2d47b
Supportavdelningsadministratör | Lösenordsadminitratör | 729827e3-9c14-49f7-bb1b-9608f156bbb8
Information Protection-administratör | Information Protection-administratör | 7495fdc4-34c4-4d15-a289-98788ce399fd
Administratör för Intune-tjänsten | Intune-administratör | 3a2c62db-5318-420d-8d74-23affee5d9d5
Licensadministratör | Licensadministratör | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Lync-tjänstadministratör | Skype for Business-administratör | 75941009-915a-4869-abe7-691bff18279e
Meddelandecenterläsare | Meddelandecenterläsare | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
Partnersupport, nivå 1 | Partnersupport, nivå 1 | 4ba39ca4-527c-499a-b93d-d9b492c50246
Partnersupport, nivå 2 | Partnersupport, nivå 2 | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
Power BI-tjänstadministratör | Power BI-administratör | a9ea8996-122f-4c74-9520-8edcd192826c
Privilegierad rolladministratör | Privilegierad rolladministratör | e8611ab8-c189-46e8-94e1-60213ab1f814
Rapportläsare | Rapportläsare | 4a5d8f65-41da-4de4-8968-e035b65339cf
Säkerhetsadministratör | Säkerhetsadministratör | 194ae4cb-b126-40b2-bd5b-6091b380977d
Säkerhetsläsare | Säkerhetsläsare | 5d6b6bb7-de71-4623-b4af-96380a352509
Tjänstesupport-administratör | Tjänstadministratör | f023fd81-a637-4b56-95fd-791ac0226033
SharePoint-tjänstadministratör | SharePoint-administratör | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Teams Communications-administratör | Teams Communications-administratör | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Teams Communications-supporttekniker | Teams Communications-supporttekniker | f70938a0-fc10-4177-9e90-2178f8765737
Teams Communications-supportspecialist | Teams Communications-supportspecialist | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Teams-tjänstadministratör | Teams-tjänstadministratör | 69091246-20e8-4a56-aa4d-066075b2a7a8
Användare | Användare | a0b1b346-4d3e-4e8b-98f8-753987be4970
Användarkonto-administratör | Användaradministratör | fe930be7-5e62-47db-91af-98c3a49a38b1
Arbetsplatsenhetskoppling | Arbetsplatsenhetskoppling | c34f683f-4d5a-4403-affd-6615e00e3a7f


## <a name="deprecated-roles"></a>Föråldrad roller

Följande roller ska inte användas. De har blivit inaktuella och tas bort från Azure AD i framtiden.

* AdHoc-licensadministratör
* Enhetskoppling
* Enhetshanterare
* Användare av enheter
* Skapare av e-postverifierad användare
* Postlådeadministratör
* Arbetsplatsenhetskoppling

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du tilldelar en användare som administratör för en Azure-prenumeration finns [hantera åtkomst med RBAC och Azure portal](../../role-based-access-control/role-assignments-portal.md)
* Mer information om hur resursåtkomsten hanteras i Microsoft Azure finns i [Förstå resursåtkomst i Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Mer information om hur Azure Active Directory relaterar till din Azure-prenumeration finns i [Hur Azure-prenumerationer är associerade med Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
