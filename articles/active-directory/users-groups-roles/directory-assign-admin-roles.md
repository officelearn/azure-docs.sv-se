---
title: Beskrivningar och behörigheter för administratörsroll – Azure AD | Microsoft-dokument
description: En administratörsroll kan lägga till användare, tilldela administrativa roller, återställa användarlösenord, hantera användarlicenser eller hantera domäner.
services: active-directory
author: curtand
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: reference
ms.date: 04/07/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: e097173712693754baab99912301c98ee336f64f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877923"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Behörigheter för administratörsrollen i Azure Active Directory

Med Azure Active Directory (Azure AD) kan du ange begränsade administratörer för att hantera identitetsuppgifter i mindre privilegierade roller. Administratörer kan tilldelas för sådana ändamål som att lägga till eller ändra användare, tilldela administrativa roller, återställa användarlösenord, hantera användarlicenser och hantera domännamn. Standardanvändarbehörigheterna kan bara ändras i användarinställningar i Azure AD.

## <a name="limit-use-of-global-administrator"></a>Begränsa användningen av global administratör

Användare som har tilldelats rollen Global administratör kan läsa och ändra alla administrativa inställningar i din Azure AD-organisation. Som standard tilldelas den person som registrerar sig för en Azure-prenumeration rollen Global administratör för Azure AD-organisationen. Endast globala administratörer och administratörer för privilegierade roller kan delegera administratörsroller. För att minska risken för ditt företag rekommenderar vi att du tilldelar den här rollen till minsta möjliga personer i organisationen.

Vi rekommenderar att du tilldelar den här rollen till färre än fem personer i organisationen. Om du har tilldelat fler än fem administratörer rollen Global administratör i organisationen kan du minska användningen av den.

### <a name="find-the-role-you-need"></a>Hitta den roll du behöver

Om det är frustrerande för dig att hitta den roll du behöver utanför en lista med många roller kan Azure AD visa delmängder av rollerna baserat på rollkategorier. Kolla in vårt nya **typfilter** för [Azure AD-roller och administratörer](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) för att bara visa dig rollerna i den valda typen.

### <a name="a-role-exists-now-that-didnt-exist-when-you-assigned-the-global-administrator-role"></a>Det finns en roll som inte fanns när du tilldelade rollen Global administratör

Det är möjligt att en roll eller roller har lagts till i Azure AD som ger mer detaljerade behörigheter som inte var ett alternativ när du förhöjde vissa användare till global administratör. Med tiden lanserar vi ytterligare roller som utför uppgifter som bara rollen global administratör kan göra tidigare. Du kan se dessa återspeglas i följande [tillgängliga roller](#available-roles).

## <a name="assign-or-remove-administrator-roles"></a>Tilldela eller ta bort administratörsroller

Mer information om hur du tilldelar administrativa roller till en användare i Azure Active Directory finns [i Visa och tilldela administratörsroller i Azure Active Directory](directory-manage-roles-portal.md).

## <a name="available-roles"></a>Tillgängliga roller

Följande administratörsroller är tillgängliga:

### <a name="application-administrator"></a>[Programadministratör](#application-administrator-permissions)

Användare i den här rollen kan skapa och hantera alla aspekter av företagsprogram, programregistreringar och programproxyinställningar. Observera att användare som tilldelats den här rollen inte läggs till som ägare när nya programregistreringar eller företagsprogram skapas.

Programadministratörer kan hantera programautentiseringsuppgifter som gör att de kan personifiera programmet. Så användare som tilldelats den här rollen kan hantera programautentiseringsuppgifter för endast de program som antingen inte har tilldelats några Azure AD-roller eller de som endast har tilldelats följande administratörsroller:
* Programadministratör
* Programutvecklare
* Molnprogramadministratör
* Katalogläsare

Om ett program tilldelas någon annan roll som inte nämns ovan kan programadministratören inte hantera autentiseringsuppgifter för det programmet. 
 
Den här rollen ger också möjlighet att _godkänna_ delegerade behörigheter och programbehörigheter, med undantag för behörigheter på Microsoft Graph API.

> [!IMPORTANT]
> Det här undantaget innebär att du fortfarande kan samtycka till behörigheter för _andra_ appar (t.ex. appar eller appar från tredje part som du har registrerat), men inte till behörigheter för Azure AD själv. Du kan fortfarande _begära_ dessa behörigheter som en del av appregistreringen, men _om du beviljar_ (dvs. medgivande till) kräver dessa behörigheter en Azure AD-administratör. Det innebär att en obehörig användare inte enkelt kan höja sina behörigheter, till exempel genom att skapa och samtycka till en app som kan skriva till hela katalogen och genom appens behörigheter höja sig till att bli en global administratör.

### <a name="application-developer"></a>[Programutvecklare](#application-developer-permissions)

Användare i den här rollen kan skapa programregistreringar när inställningen "Användare kan registrera program" är inställd på Nr. Den här rollen ger också behörighet att medgivande för ens egen räkning när inställningen "Användare kan samtycka till appar som använder företagsdata för deras räkning" är inställd på Nej. Användare som tilldelats den här rollen läggs till som ägare när nya programregistreringar eller företagsprogram skapas.

### <a name="authentication-administrator"></a>[Administratör för autentisering](#authentication-administrator-permissions)

Rollen Autentiseringsadministratör är för närvarande i offentlig förhandsversion. Användare med den här rollen kan ange eller återställa autentiseringsuppgifter som inte är lösenord och kan uppdatera lösenord för alla användare. Autentiseringsadministratörer kan kräva att användare registrerar sig mot befintliga icke-lösenordsautentiseringsuppgifter (till exempel MFA eller FIDO) och **återkallar kom ihåg MFA på enheten**, som frågar efter MFA vid nästa inloggning av användare som inte är administratörer eller endast tilldelat följande roller:

* Administratör för autentisering
* Katalogläsare
* Gäst inbjudna
* Läsare av Meddelandecenter
* Rapporter Reader

> [!IMPORTANT]
> Användare med den här rollen kan ändra autentiseringsuppgifter för personer som kan ha åtkomst till känslig eller privat information eller kritisk konfiguration i och utanför Azure Active Directory. Om du ändrar en användares autentiseringsuppgifter kan det innebära att användarens identitet och behörigheter kan antas. Ett exempel:
>
>- Programregistrering och företagsprogramägare, som kan hantera autentiseringsuppgifter för appar som de äger. Dessa appar kan ha privilegierade behörigheter i Azure AD och på andra ställen som inte beviljas autentiseringsadministratörer. Genom den här sökvägen kan en autentiseringsadministratör ta på sig identiteten för en programägare och sedan ta på sig identiteten för ett privilegierat program genom att uppdatera autentiseringsuppgifterna för programmet.
>- Azure-prenumerationsägare, som kan ha åtkomst till känslig eller privat information eller kritisk konfiguration i Azure.
>- Säkerhetsgrupp och Office 365-gruppägare, som kan hantera gruppmedlemskap. Dessa grupper kan bevilja åtkomst till känslig eller privat information eller kritisk konfiguration i Azure AD och på andra ställen.
>- Administratörer i andra tjänster utanför Azure AD som Exchange Online, Office Security and Compliance Center och personalsystem.
>- Icke-administratörer som chefer, juridiska ombud och personalanställda som kan ha tillgång till känslig eller privat information.

### <a name="azure-devops-administrator"></a>[Azure DevOps-administratör](#azure-devops-administrator-permissions)

Användare med den här rollen kan hantera Azure DevOps-principen för att begränsa nya Azure DevOps-organisationer till en uppsättning konfigurerbara användare eller grupper. Användare i den här rollen kan hantera den här principen via alla Azure DevOps-organisationer som har stöd för företagets Azure AD-organisation.

Alla Azure DevOps-principer för företag kan hanteras av användare i den här rollen.

### <a name="azure-information-protection-administrator"></a>[Administratör för azure-informationsskydd](#azure-information-protection-administrator-permissions)

Användare med den här rollen har alla behörigheter i Azure Information Protection-tjänsten. Med den här rollen kan du konfigurera etiketter för Azure Information Protection-principen, hantera skyddsmallar och aktivera skydd. Den här rollen ger inte behörigheter i Identity Protection Center, Privileged Identity Management, Monitor Office 365 Service Health eller Office 365 Security & Compliance Center.

### <a name="b2c-user-flow-administrator"></a>[B2C-administratör för användarflöde](#b2c-user-flow-administrator-permissions)

Användare med den här rollen kan skapa och hantera B2C-användarflöden (kallas även "inbyggda" principer) i Azure-portalen.Genom att skapa eller redigera användarflöden kan dessa användare ändra html/CSS/javascript-innehållet i användarupplevelsen, ändra MFA-krav per användarflöde, ändra anspråk i token och justera sessionsinställningar för alla principer i klienten. Å andra sidan innehåller den här rollen inte möjligheten att granska användardata eller göra ändringar i attributen som ingår i klientschemat.Ändringar i Identity Experience Framework -principer (kallas även anpassade) ligger också utanför den här rollens omfattning.

### <a name="b2c-user-flow-attribute-administrator"></a>[Administratör för användarflödesattribut för B2C](#b2c-user-flow-attribute-administrator-permissions)

Användare med den här rollen lägger till eller tar bort anpassade attribut som är tillgängliga för alla användarflöden i klienten.Användare med den här rollen kan därför ändra eller lägga till nya element i slutanvändarschemat och påverka beteendet för alla användarflöden och indirekt resultera i ändringar av vilka data som kan begäras av slutanvändare och slutligen skickas som anspråk till program.Den här rollen kan inte redigera användarflöden.

### <a name="b2c-ief-keyset-administrator"></a>[B2C IEF Keyset Administratör](#b2c-ief-keyset-administrator-permissions)

Användaren kan skapa och hantera principnycklar och hemligheter för tokenkryptering, tokensignaturer och anspråk på kryptering/dekryptering.Genom att lägga till nya nycklar i befintliga nyckelbehållare kan den här begränsade administratören rulla hemligheter efter behov utan att påverka befintliga program.Den här användaren kan se det fullständiga innehållet i dessa hemligheter och deras utgångsdatum även efter att de har skapats.

> [!IMPORTANT]
> Detta är en känslig roll.Rollen nyckeluppsättningsadministratör bör granskas noggrant och tilldelas med försiktighet under förproduktion och produktion.

### <a name="b2c-ief-policy-administrator"></a>[B2C IEF-principadministratör](#b2c-ief-policy-administrator-permissions)

Användare i den här rollen har möjlighet att skapa, läsa, uppdatera och ta bort alla anpassade principer i Azure AD B2C och har därför full kontroll över Identity Experience Framework i relevant Azure AD B2C-klientorganisation. Genom att redigera principer kan den här användaren upprätta direkt federation med externa identitetsleverantörer, ändra katalogschemat, ändra allt användarvänt innehåll (HTML, CSS, JavaScript), ändra kraven för att slutföra en autentisering, skapa nya användare, skicka användardata till externa system, inklusive fullständiga migreringar, och redigera all användarinformation inklusive känsliga fält som lösenord och telefonnummer. Omvänt kan den här rollen inte ändra krypteringsnycklarna eller redigera hemligheterna som används för federationen i klienten.

> [!IMPORTANT]
> B2 IEF-principadministratören är en mycket känslig roll som bör tilldelas på en mycket begränsad basis för klienter i produktionen.Aktiviteter som utförs av dessa användare bör granskas noggrant, särskilt för hyresgäster i produktionen.

### <a name="billing-administrator"></a>[Faktureringsadministratör](#billing-administrator-permissions)

Gör inköp, hanterar prenumerationer, hanterar supportärenden och övervakar tjänstens hälsa.

### <a name="cloud-application-administrator"></a>[Molnprogramadministratör](#cloud-application-administrator-permissions)

Användare i den här rollen har samma behörigheter som rollen Programadministratör, exklusive möjligheten att hantera programproxy. Den här rollen ger möjlighet att skapa och hantera alla aspekter av företagsansökningar och programregistreringar. Den här rollen ger också möjlighet att godkänna delegerade behörigheter och programbehörigheter exklusive Microsoft Graph API. Användare som tilldelats den här rollen läggs inte till som ägare när nya programregistreringar eller företagsprogram skapas.

Cloud Application Administrators kan hantera programautentiseringsuppgifter som gör att de kan personifiera programmet. Så användare som tilldelats den här rollen kan hantera programautentiseringsuppgifter för endast de program som antingen inte har tilldelats några Azure AD-roller eller de som endast har tilldelats följande administratörsroller:
* Programutvecklare
* Molnprogramadministratör
* Katalogläsare

Om ett program har tilldelats någon annan roll som inte nämns ovan kan Cloud Application Administrator inte hantera autentiseringsuppgifter för det programmet.

### <a name="cloud-device-administrator"></a>[Administratör för molnenheter](#cloud-device-administrator-permissions)

Användare i den här rollen kan aktivera, inaktivera och ta bort enheter i Azure AD och läsa Windows 10 BitLocker-nycklar (om sådana finns) i Azure-portalen. Rollen ger inte behörighet att hantera andra egenskaper på enheten.

### <a name="compliance-administrator"></a>[Efterlevnadsadministratör](#compliance-administrator-permissions)

Användare med den här rollen har behörighet att hantera efterlevnadsrelaterade funktioner i Microsoft 365 compliance center, Microsoft 365 admincenter, Azure och Office 365 Security & Compliance Center. Tilldelade kan också hantera alla funktioner i Administrationscentret för Exchange och Teams & Administrationscenter för Skype för företag och skapa supportärenden för Azure och Microsoft 365. Mer information finns på [Om Office 365-administratörsroller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

I | Kan göra
----- | ----------
[Efterlevnadscenter för Microsoft 365](https://protection.office.com) | Skydda och hantera organisationens data för Microsoft 365-tjänster<br>Hantera efterlevnadsaviseringar
[Compliance Manager (Efterlevnadshanteraren)](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Spåra, tilldela och verifiera organisationens efterlevnadsaktiviteter för regelefterlevnad
[Säkerhetsorganisationscenter för Office 365 &](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Hantera datastyrning<br>Utföra juridisk utredning och datautredning<br>Hantera begäran om registrerad datauppgift<br><br>Den här rollen har samma behörigheter som [rollgruppen för efterlevnadsadministratör](https://docs.microsoft.com/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) i Office 365 Security & Compliance Center rollbaserad åtkomstkontroll.
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Visa alla Intune-granskningsdata
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Har skrivskyddade behörigheter och kan hantera aviseringar<br>Kan skapa och ändra filprinciper och tillåta filstyrningsåtgärder<br>Kan visa alla inbyggda rapporter under Datahantering

### <a name="compliance-data-administrator"></a>[Efterlevnadsdataadministratör](#compliance-data-administrator-permissions)

Användare med den här rollen har behörighet att spåra data i Microsoft 365 compliance center, Microsoft 365 admin center och Azure. Användare kan också spåra efterlevnadsdata i Administrationscentret för Exchange, Compliance Manager och Teams & administrationscenter för Skype för företag och skapa supportärenden för Azure och Microsoft 365.

I | Kan göra
----- | ----------
[Efterlevnadscenter för Microsoft 365](https://protection.office.com) | Övervaka efterlevnadsrelaterade policyer för Microsoft 365-tjänster<br>Hantera efterlevnadsaviseringar
[Compliance Manager (Efterlevnadshanteraren)](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Spåra, tilldela och verifiera organisationens efterlevnadsaktiviteter för regelefterlevnad
[Säkerhetsorganisationscenter för Office 365 &](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Hantera datastyrning<br>Utföra juridisk utredning och datautredning<br>Hantera begäran om registrerad datauppgift<br><br>Den här rollen har samma behörigheter som [rollgruppen för efterlevnadsdataadministratör](https://docs.microsoft.com/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) i Office 365 Security & Compliance Center rollbaserad åtkomstkontroll.
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Visa alla Intune-granskningsdata
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Har skrivskyddade behörigheter och kan hantera aviseringar<br>Kan skapa och ändra filprinciper och tillåta filstyrningsåtgärder<br>Kan visa alla inbyggda rapporter under Datahantering

### <a name="conditional-access-administrator"></a>[Administratör för villkorlig åtkomst](#conditional-access-administrator-permissions)

Användare med den här rollen har möjlighet att hantera Azure Active Directory Villkorlig åtkomstinställningar.
> [!NOTE]
> Om du vill distribuera Exchange ActiveSync-principen för villkorlig åtkomst i Azure måste användaren också vara global administratör.

### <a name="customer-lockbox-access-approver"></a>[Godkännande av kundlåsboxåtkomst](#customer-lockbox-access-approver-permissions)

Hanterar [Customer Lockbox-förfrågningar](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests) i din organisation. De får e-postmeddelanden för Customer Lockbox-begäranden och kan godkänna och neka begäranden från administrationscentret för Microsoft 365. De kan också aktivera eller inaktivera funktionen Customer Lockbox. Endast globala administratörer kan återställa lösenorden för personer som tilldelats den här rollen.

### <a name="desktop-analytics-administrator"></a>[Administratör för skrivbordsanalys](#desktop-analytics-administrator-permissions)


Användare i den här rollen kan hantera desktop analytics- och Office-anpassningstjänster & policytjänster. För Desktop Analytics omfattar detta möjligheten att visa tillgångsinventering, skapa distributionsplaner, visa distribution och hälsostatus. För tjänsten Anpassning av Office & princip gör den här rollen det möjligt för användare att hantera Office-principer.

### <a name="device-administrator"></a>[Enhetsadministratör](#device-administrators-permissions)

Den här rollen är endast tillgänglig för tilldelning som ytterligare en lokal administratör i [Enhetsinställningar](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Användare med den här rollen blir lokala datoradministratörer på alla Windows 10-enheter som är anslutna till Azure Active Directory. De har inte möjlighet att hantera enhetsobjekt i Azure Active Directory.

### <a name="directory-readers"></a>[Katalogläsare](#directory-readers-permissions)

Användare i den här rollen kan läsa grundläggande kataloginformation. Denna roll bör användas för:
* Om du beviljar en viss uppsättning gästanvändare läsbehörighet i stället för att ge den till alla gästanvändare.
* Att bevilja en viss uppsättning användare som inte är administratörer åtkomst till Azure-portalen när "Begränsa åtkomsten till Azure AD-portalen till endast administratörer" är inställd på "Ja".
* Ge tjänstens huvudnamn åtkomst till katalog där Directory.Read.All inte är ett alternativ.

### <a name="directory-synchronization-accounts"></a>[Konton för katalogsynkronisering](#directory-synchronization-accounts-permissions)

Använd inte. Den här rollen tilldelas automatiskt till Azure AD Connect-tjänsten och är inte avsedd eller stöds för någon annan användning.

### <a name="directory-writers"></a>[Katalog författare](#directory-writers-permissions)

Det här är en äldre roll som ska tilldelas program som inte stöder [ramverket för medgivande](../develop/quickstart-register-app.md). Det bör inte tilldelas några användare.

### <a name="dynamics-365-administrator--crm-administrator"></a>[Dynamics 365 administratör / CRM-administratör](#crm-service-administrator-permissions)

Användare med den här rollen har globala behörigheter inom Microsoft Dynamics 365 Online, när tjänsten finns, samt möjligheten att hantera supportärenden och övervaka tjänstens hälsa. Mer information finns i [Använd rollen tjänstadministratör för att hantera din klientorganisation](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

> [!NOTE]
> I Microsoft Graph API och Azure AD PowerShell identifieras den här rollen som "Dynamics 365 Service Administrator". Det är "Dynamics 365 Administrator" i [Azure-portalen](https://portal.azure.com).

### <a name="exchange-administrator"></a>[Exchange-administratör](#exchange-service-administrator-permissions)

Användare med den här rollen har globala behörigheter inom Microsoft Exchange Online när tjänsten finns. Har också möjlighet att skapa och hantera alla Office 365-grupper, hantera supportärenden och övervaka tjänstens hälsotillstånd. Mer information finns på [Om office 365-administratörsroller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> I Microsoft Graph API och Azure AD PowerShell identifieras den här rollen som "Exchange Service Administrator". Det är "Exchange Administrator" i [Azure-portalen](https://portal.azure.com). Det är "Exchange Online-administratör" i [Administrationscentret för Exchange](https://go.microsoft.com/fwlink/p/?LinkID=529144).

### <a name="external-identity-provider-administrator"></a>[Administratör för extern identitetsprovider](#external-identity-provider-administrator-permissions)

Den här administratören hanterar federationen mellan Azure Active Directory-klienter och externa identitetsleverantörer.Med den här rollen kan användare lägga till nya identitetsleverantörer och konfigurera alla tillgängliga inställningar (t.ex. autentiseringssökväg, tjänst-ID, tilldelade nyckelbehållare).Den här användaren kan göra det möjligt för klienten att lita på autentiseringar från externa identitetsleverantörer.Den resulterande effekten på slutanvändarens upplevelser beror på vilken typ av klientorganisation:

* Azure Active Directory-klienter för anställda och partner: Tillägget av en federation (t.ex. med Gmail) påverkar omedelbart alla gäst inbjudningar som ännu inte har lösts in. Se [Lägga till Google som identitetsleverantör för B2B-gästanvändare](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).
* Azure Active Directory B2C-klienter: Tillägget av en federation (till exempel med Facebook eller med en annan Azure AD-organisation) påverkar inte direkt slutanvändarflöden förrän identitetsprovidern läggs till som ett alternativ i ett användarflöde (kallas även en inbyggd princip). Se [Konfigurera ett Microsoft-konto som en identitetsleverantör](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app) för ett exempel.För att ändra användarflöden krävs den begränsade rollen för "B2C User Flow Administrator".

### <a name="global-administrator--company-administrator"></a>[Global administratör / Företagsadministratör](#company-administrator-permissions)

Användare med den här rollen har åtkomst till alla administrativa funktioner i Azure Active Directory, samt tjänster som använder Azure Active Directory-identiteter som Microsoft 365 security center, Microsoft 365 compliance center, Exchange Online, SharePoint Online och Skype för företag – Online. Den person som registrerar sig för Azure Active Directory-klienten blir en global administratör. Det kan finnas fler än en global administratör på företaget. Globala administratörer kan återställa lösenordet för alla användare och alla andra administratörer.

> [!NOTE]
> I Microsoft Graph API och Azure AD PowerShell identifieras den här rollen som "Företagsadministratör". Det är "Global administratör" i [Azure-portalen](https://portal.azure.com).
>
>

### <a name="global-reader"></a>[Global läsare](#global-reader-permissions)

Användare i den här rollen kan läsa inställningar och administrativ information för Microsoft 365-tjänster men kan inte vidta hanteringsåtgärder. Global läsare är den skrivskyddade motsvarigheten till global administratör. Tilldela global läsare i stället för Global administratör för planering, granskningar eller undersökningar. Använd Global läsare i kombination med andra begränsade administratörsroller som Exchange Administrator för att göra det enklare att få jobbet gjort utan att tilldela rollen Global administratör. Den globala läsaren fungerar med administrationscentret för Microsoft 365, administrationscenter för Exchange, administrationscenter för Team, Säkerhetscenter, Efterlevnadscenter, Azure AD-administrationscenter och administrationscenter för Enhetshantering.

> [!NOTE]
> Global läsarroll har några begränsningar just nu -
>
>- Administrationscenter för SharePoint - Administrationscenter för SharePoint stöder inte rollen Global reader. Du ser inte "SharePoint" i den vänstra rutan under Administrationscenter i [Microsoft 365 admin center](https://admin.microsoft.com/Adminportal/Home#/homepage).
>- [Administrationscenter för OneDrive](https://admin.onedrive.com/) – Administrationscentret för OneDrive stöder inte rollen Global reader.
>- [Azure AD-portal -](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) Global läsare kan inte läsa etableringsläget för en företagsapp.
>- [M365 admin center](https://admin.microsoft.com/Adminportal/Home#/homepage) - Global läsare kan inte läsa kund lockbox förfrågningar. Fliken **Kundlåsboxbegäranr** hittar du inte under **Support** i den vänstra rutan i Administrationscenter för M365.
>- [M365 Security Center](https://security.microsoft.com/homepage) - Global läsare kan inte läsa känslighet och retention etiketter. Flikarna **Känslighetsetiketter,** **Kvarhållningsetiketter**och **Etikettanalys** hittar du inte i den vänstra rutan i säkerhetscentret M365.
>- [Office Security & Compliance Center](https://sip.protection.office.com/homepage) – Global läsare kan inte läsa SCC-granskningsloggar, göra innehållssökning eller se Säker poäng.
>- [Administrationscenter för team](https://admin.teams.microsoft.com) – Global läsare kan inte läsa **Teams livscykel,** **Analytics & rapporter,** **IP-telefonenhetshantering** och **appkatalog**.
>- [PAM (Privileged Access Management)](https://docs.microsoft.com/office365/securitycompliance/privileged-access-management-overview) stöder inte rollen Global reader.
>- [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/what-is-information-protection) - Global läsare stöds endast [för central rapportering](https://docs.microsoft.com/azure/information-protection/reports-aip) och när din Azure AD-organisation inte finns på den [enhetliga märkningsplattformen](https://docs.microsoft.com/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform).
>
> Dessa funktioner är för närvarande under utveckling.
>

### <a name="groups-administrator"></a>[Gruppadministratör](#groups-administrator-permissions)

Användare i den här rollen kan skapa/hantera grupper och dess inställningar som namngivnings- och förfalloprinciper. Det är viktigt att förstå att tilldela en användare till den här rollen ger dem möjlighet att hantera alla grupper i klienten över olika arbetsbelastningar som Teams, SharePoint, Yammer utöver Outlook. Även användaren kommer att kunna hantera de olika gruppinställningarna i olika administratörsportaler som Microsoft Admin Center, Azure-portalen, samt arbetsbelastningsspecifika sådana som Teams och SharePoint Admin Centers.

### <a name="guest-inviter"></a>[Gäst inbjudna](#guest-inviter-permissions)

Användare i den här rollen kan hantera Azure Active Directory B2B gästanvändare inbjudningar när **medlemmarna kan bjuda in** användarinställning är inställd på Nr. Mer information om B2B-samarbete på [Om Azure AD B2B-samarbete](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Den innehåller inga andra behörigheter.

### <a name="helpdesk-administrator"></a>[Administratör för helpdesk](#helpdesk-administrator-permissions)

Användare med den här rollen kan ändra lösenord, ogiltigförklara uppdateringstoken, hantera tjänstbegäranden och övervaka tjänstens hälsotillstånd. Om du ogiltigförklarar en uppdateringstoken tvingas användaren att logga in igen. Helpdesk-administratörer kan återställa lösenord och ogiltigförklara uppdateringstoken för andra användare som inte är administratörer eller endast tilldelat följande roller:

* Katalogläsare
* Gäst inbjudna
* Administratör för helpdesk
* Läsare av Meddelandecenter
* Rapporter Reader

> [!IMPORTANT]
> Användare med den här rollen kan ändra lösenord för personer som kan ha åtkomst till känslig eller privat information eller kritisk konfiguration i och utanför Azure Active Directory. Att ändra lösenordet för en användare kan innebära möjligheten att anta att användarens identitet och behörigheter. Ett exempel:
>
>- Programregistrering och företagsprogramägare, som kan hantera autentiseringsuppgifter för appar som de äger. Dessa appar kan ha privilegierade behörigheter i Azure AD och på andra ställen som inte har beviljats Helpdesk-administratörer. Genom den här sökvägen kan en helpdeskadministratör ta på sig identiteten för en programägare och sedan ta på sig identiteten för ett privilegierat program genom att uppdatera autentiseringsuppgifterna för programmet.
>- Azure-prenumerationsägare, som kan ha åtkomst till känslig eller privat information eller kritisk konfiguration i Azure.
>- Säkerhetsgrupp och Office 365-gruppägare, som kan hantera gruppmedlemskap. Dessa grupper kan bevilja åtkomst till känslig eller privat information eller kritisk konfiguration i Azure AD och på andra ställen.
>- Administratörer i andra tjänster utanför Azure AD som Exchange Online, Office Security and Compliance Center och personalsystem.
>- Icke-administratörer som chefer, juridiska ombud och personalanställda som kan ha tillgång till känslig eller privat information.

Det går att delegera administrativa behörigheter över undergrupper av användare och tillämpa principer på en delmängd användare med [administrativa enheter (nu i offentlig förhandsversion).](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units)

Den här rollen kallades tidigare "Lösenordsadministratör" i [Azure-portalen](https://portal.azure.com/). Namnet "Helpdesk Administrator" i Azure AD matchar nu namnet i Azure AD PowerShell och Microsoft Graph API.

### <a name="intune-administrator"></a>[Intune-administratör](#intune-service-administrator-permissions)

Användare med den här rollen har globala behörigheter inom Microsoft Intune Online när tjänsten finns. Dessutom innehåller den här rollen möjligheten att hantera användare och enheter för att associera principer samt skapa och hantera grupper. Mer information om [rollbaserad administrationskontroll (RBAC) med Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control).

Den här rollen kan skapa och hantera alla säkerhetsgrupper. Intune Admin har dock inte administratörsrättigheter över Office-grupper. Det innebär att administratören inte kan uppdatera ägare eller medlemskap för alla Office-grupper i klienten. Han/hon kan dock hantera den Office-grupp som han skapar som kommer som en del av hans/hennes slutanvändarbehörighet. Så alla Office-grupper (inte säkerhetsgrupp) som han/ hon skapar bör räknas mot hans/hennes kvot på 250.

> [!NOTE]
> I Microsoft Graph API och Azure AD PowerShell identifieras den här rollen som "Intune Service Administrator". Det är "Intune Administrator" i [Azure-portalen](https://portal.azure.com).

### <a name="kaizala-administrator"></a>[Kaizala Administratör](#kaizala-administrator-permissions)

Användare med den här rollen har globala behörigheter för att hantera inställningar i Microsoft Kaizala, när tjänsten finns, samt möjligheten att hantera supportärenden och övervaka tjänstens hälsa. Dessutom kan användaren komma åt rapporter som rör antagande & användning av Kaizala av organisationens medlemmar och affärsrapporter som genereras med hjälp av Kaizala-åtgärderna.

### <a name="license-administrator"></a>[Licensadministratör](#license-administrator-permissions)

Användare i den här rollen kan lägga till, ta bort och uppdatera licenstilldelningar för användare, grupper (med gruppbaserad licensiering) och hantera användningsplatsen för användare. Rollen ger inte möjlighet att köpa eller hantera prenumerationer, skapa eller hantera grupper eller skapa eller hantera användare utanför användningsplatsen. Den här rollen har ingen åtkomst till att visa, skapa eller hantera supportärenden.

### <a name="message-center-privacy-reader"></a>[Sekretessläsare för Message Center](#message-center-privacy-reader-permissions)

Användare i den här rollen kan övervaka alla meddelanden i Meddelandecenter, inklusive datasekretessmeddelanden. Sekretessläsare i Message Center får e-postmeddelanden, inklusive de som är relaterade till datasekretess, och de kan avsluta prenumerationen med hjälp av Inställningar för Message Center. Endast den globala administratören och Message Center Privacy Reader kan läsa datasekretessmeddelanden. Dessutom innehåller den här rollen möjligheten att visa grupper, domäner och prenumerationer. Den här rollen har ingen behörighet att visa, skapa eller hantera tjänstbegäranden.

### <a name="message-center-reader"></a>[Läsare av Meddelandecenter](#message-center-reader-permissions)

Användare i den här rollen kan övervaka meddelanden och rådgivande hälsouppdateringar i [Office 365 Message Center](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) för sin organisation på konfigurerade tjänster som Exchange, Intune och Microsoft Teams. Message Center-läsare får veckovisa e-postsammanfattningar av inlägg, uppdateringar och kan dela inlägg i meddelandecenter i Office 365. I Azure AD har användare som tilldelats den här rollen endast skrivskyddad åtkomst på Azure AD-tjänster som användare och grupper. Den här rollen har ingen åtkomst till att visa, skapa eller hantera supportärenden.

### <a name="office-apps-administrator"></a>[Office Apps-administratör](#office-apps-administrator-permissions)

Användare i den här rollen kan hantera Molninställningarna för Office 365-appar. Detta inkluderar hantering av molnprinciper, hämtningshantering för självbetjäning och möjligheten att visa Office-appar relaterade rapporter. Den här rollen ger dessutom möjlighet att hantera supportärenden och övervaka tjänstens hälsotillstånd i huvudadministrationscentret. Användare som tilldelats den här rollen kan också hantera kommunikation av nya funktioner i Office-appar. 

### <a name="partner-tier1-support"></a>[Support för partnernivå1](#partner-tier1-support-permissions)

Använd inte. Den här rollen har tagits bort och kommer att tas bort från Azure AD i framtiden. Den här rollen är avsedd att användas av ett litet antal Microsoft-återförsäljningspartner och är inte avsedd för allmänt bruk.

### <a name="partner-tier2-support"></a>[Support för partnernivå2](#partner-tier2-support-permissions)

Använd inte. Den här rollen har tagits bort och kommer att tas bort från Azure AD i framtiden. Den här rollen är avsedd att användas av ett litet antal Microsoft-återförsäljningspartner och är inte avsedd för allmänt bruk.

### <a name="password-administrator"></a>[Lösenordsadministratör](#password-administrator-permissions)

Användare med den här rollen har begränsad förmåga att hantera lösenord. Den här rollen ger inte möjlighet att hantera tjänstbegäranden eller övervaka tjänstens hälsotillstånd. Lösenordsadministratörer kan återställa lösenord för andra användare som inte är administratörer eller medlemmar i följande roller:

* Katalogläsare
* Gäst inbjudna
* Lösenordsadministratör

### <a name="power-bi-administrator"></a>[Power BI-administratör](#power-bi-service-administrator-permissions)

Användare med den här rollen har globala behörigheter inom Microsoft Power BI, när tjänsten finns, samt möjligheten att hantera supportärenden och övervaka tjänstens hälsa. Mer information på [Understanding the Power BI admin role](https://docs.microsoft.com/power-bi/service-admin-role).

> [!NOTE]
> I Microsoft Graph API och Azure AD PowerShell identifieras den här rollen som "Power BI Service Administrator". Det är "Power BI Administrator" i [Azure-portalen](https://portal.azure.com).

### <a name="power-platform-administrator"></a>[Administratör för Power Platform](#power-platform-administrator-permissions)

Användare i den här rollen kan skapa och hantera alla aspekter av miljöer, PowerApps, Flows, Data Loss Prevention policies. Dessutom har användare med den här rollen möjlighet att hantera supportärenden och övervaka tjänstens hälsa.

### <a name="privileged-authentication-administrator"></a>[Privilegierad autentiseringsadministratör](#privileged-authentication-administrator-permissions)

Användare med den här rollen kan ange eller återställa autentiseringsuppgifter för icke-lösenord för alla användare, inklusive globala administratörer, och kan uppdatera lösenord för alla användare. Privilegierade autentiseringsadministratörer kan tvinga användare att registrera sig mot befintliga icke-lösenordsautentiseringsuppgifter (t.ex.

### <a name="privileged-role-administrator"></a>[Administratör för privilegierad roll](#privileged-role-administrator-permissions)

Användare med den här rollen kan hantera rolltilldelningar i Azure Active Directory, liksom i Azure AD Privileged Identity Management. Dessutom tillåter den här rollen hantering av alla aspekter av privilegierad identitetshantering och administrativa enheter.

> [!IMPORTANT]
> Den här rollen ger möjlighet att hantera tilldelningar för alla Azure AD-roller, inklusive den globala administratörsrollen. Den här rollen innehåller inte några andra privilegierade förmågor i Azure AD som att skapa eller uppdatera användare. Användare som tilldelats den här rollen kan dock ge sig själva eller andra ytterligare privilegier genom att tilldela ytterligare roller.

### <a name="reports-reader"></a>[Rapporter Reader](#reports-reader-permissions)

Användare med den här rollen kan visa användningsrapporteringsdata och instrumentpanelen för rapporter i Microsoft 365 admincenter och implementeringskontextpaketet i Power BI. Dessutom ger rollen åtkomst till inloggningsrapporter och aktivitet i Azure AD och data som returneras av Microsoft Graph-rapporterings-API:et. En användare som tilldelats rollen Reports Reader kan endast komma åt relevanta användnings- och införandemått. De har inga administratörsbehörighet för att konfigurera inställningar eller komma åt de produktspecifika administrationscenter som Exchange. Den här rollen har ingen åtkomst till att visa, skapa eller hantera supportärenden.

### <a name="search-administrator"></a>[Sök administratör](#search-administrator-permissions)

Användare i den här rollen har fullständig åtkomst till alla hanteringsfunktioner för Microsoft Search i administrationscentret för Microsoft 365. Sökadministratörer kan delegera rollerna Sökadministratörer och Sökredigerare till användare och skapa och hantera innehåll, till exempel bokmärken, Q&Som och platser. Dessutom kan dessa användare visa meddelandecentret, övervaka tjänstens hälsotillstånd och skapa tjänstbegäranden.

### <a name="search-editor"></a>[Sökredigerare](#search-editor-permissions)

Användare i den här rollen kan skapa, hantera och ta bort innehåll för Microsoft Search i Microsoft 365-administrationscentret, inklusive bokmärken, Q&Som och platser.

### <a name="security-administrator"></a>[Säkerhetsadministratör](#security-administrator-permissions)

Användare med den här rollen har behörighet att hantera säkerhetsrelaterade funktioner i Microsoft 365-säkerhetscentret, Azure Active Directory Identity Protection, Azure Information Protection och Office 365 Security & Compliance Center. Mer information om Office 365-behörigheter finns på [Behörigheter i Office 365 Security & Compliance Center](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

I | Kan göra
--- | ---
[Microsoft 365 säkerhetscenter](https://protection.office.com) | Övervaka säkerhetsrelaterade policyer för Microsoft 365-tjänster<br>Hantera säkerhetshot och aviseringar<br>Visa rapporter
Centrum för identitetsskydd | Alla behörigheter för rollen Säkerhetsläsare<br>Dessutom kan du utföra alla identity protection center-åtgärder utom för att återställa lösenord
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Alla behörigheter för rollen Säkerhetsläsare<br>**Det går inte** att hantera Azure AD-rolltilldelningar eller inställningar
[Säkerhetsorganisationscenter för Office 365 &](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Hantera säkerhetsprinciper<br>Visa, undersöka och svara på säkerhetshot<br>Visa rapporter
Azure Advanced Threat Protection | Övervaka och svara på misstänkt säkerhetsaktivitet
Windows Defender ATP och EDR | Tilldela roller<br>Hantera maskingrupper<br>Konfigurera identifiering av slutpunktshot och automatisk reparation<br>Visa, undersöka och svara på aviseringar
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Visar information om användare, enheter, registrering, konfiguration och program<br>Det går inte att göra ändringar i Intune
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Lägga till administratörer, lägga till principer och inställningar, ladda upp loggar och utföra styrningsåtgärder
[Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Kan visa säkerhetsprinciper, visa säkerhetstillstånd, redigera säkerhetsprinciper, visa aviseringar och rekommendationer, avvisa aviseringar och rekommendationer
[Hälsohälsa för Office 365-tjänster](https://docs.microsoft.com/office365/enterprise/view-service-health) | Visa hälsotillståndet för Office 365-tjänster

### <a name="security-operator"></a>[Säkerhetsoperatör](#security-operator-permissions)

Användare med den här rollen kan hantera aviseringar och ha global skrivskyddad åtkomst på säkerhetsrelaterade funktioner, inklusive all information i Microsoft 365-säkerhetscenter, Azure Active Directory, Identity Protection, Privileged Identity Management och Office 365 Security & Compliance Center. Mer information om Office 365-behörigheter finns på [Behörigheter i Office 365 Security & Compliance Center](https://docs.microsoft.com/office365/securitycompliance/permissions-in-the-security-and-compliance-center).

I | Kan göra
--- | ---
[Microsoft 365 säkerhetscenter](https://protection.office.com) | Alla behörigheter för rollen Säkerhetsläsare<br>Visa, undersöka och svara på aviseringar om säkerhetshot
Centrum för identitetsskydd | Alla behörigheter för rollen Säkerhetsläsare<br>Dessutom kan du utföra alla identity protection center-åtgärder utom för att återställa lösenord
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Alla behörigheter för rollen Säkerhetsläsare
[Säkerhetsorganisationscenter för Office 365 &](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Alla behörigheter för rollen Säkerhetsläsare<br>Visa, undersöka och svara på säkerhetsaviseringar
Windows Defender ATP och EDR | Alla behörigheter för rollen Säkerhetsläsare<br>Visa, undersöka och svara på säkerhetsaviseringar
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Alla behörigheter för rollen Säkerhetsläsare
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Alla behörigheter för rollen Säkerhetsläsare
[Hälsohälsa för Office 365-tjänster](https://docs.microsoft.com/office365/enterprise/view-service-health) | Visa hälsotillståndet för Office 365-tjänster

### <a name="security-reader"></a>[Säkerhetsläsare](#security-reader-permissions)

Användare med den här rollen har global skrivskyddad åtkomst på säkerhetsrelaterade funktioner, inklusive all information i Microsoft 365-säkerhetscenter, Azure Active Directory, Identity Protection, Privileged Identity Management, samt möjligheten att läsa Azure Active Directory-inloggningsrapporter och granskningsloggar och i Office 365 Security & Compliance Center. Mer information om Office 365-behörigheter finns på [Behörigheter i Office 365 Security & Compliance Center](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

I | Kan göra
--- | ---
[Microsoft 365 säkerhetscenter](https://protection.office.com) | Visa säkerhetsrelaterade policyer för Microsoft 365-tjänster<br>Visa säkerhetshot och aviseringar<br>Visa rapporter
Centrum för identitetsskydd | Läs all säkerhetsrapporter och all inställningsinformation för säkerhetsfunktioner<br><ul><li>Anti-spam<li>Kryptering<li>Förebyggande av dataförlust<li>Anti-malware<li>Advanced Threat Protection<li>Anti-phishing<li>Regler för e-postflöde
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Har skrivskyddad åtkomst till all information som visas i Azure AD Privileged Identity Management: Principer och rapporter för Azure AD-rolltilldelningar och säkerhetsgranskningar.<br>**Det går inte** att registrera dig för Azure AD Privileged Identity Management eller göra några ändringar i den. I portalen Privilegierad identitetshantering eller via PowerShell kan någon i den här rollen aktivera ytterligare roller (till exempel Global admin eller Privilegierad rolladministratör), om användaren är berättigad till dem.
[Säkerhetsorganisationscenter för Office 365 &](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Visa säkerhetsprinciper<br>Visa och undersöka säkerhetshot<br>Visa rapporter
Windows Defender ATP och EDR | Visa och undersöka aviseringar. När du aktiverar rollbaserad åtkomstkontroll i Windows Defender ATP förlorar användare med skrivskyddade behörigheter som azure AD Security-läsaren åtkomst tills de tilldelas en Windows Defender ATP-roll.
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Visar information om användare, enhet, registrering, konfiguration och programmet. Kan inte göra ändringar i Intune.
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Har skrivskyddade behörigheter och kan hantera aviseringar
[Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Kan visa rekommendationer och aviseringar, visa säkerhetsprinciper, visa säkerhetstillstånd, men kan inte göra ändringar
[Hälsohälsa för Office 365-tjänster](https://docs.microsoft.com/office365/enterprise/view-service-health) | Visa hälsotillståndet för Office 365-tjänster

### <a name="service-support-administrator"></a>[Administratör för servicesupport](#service-support-administrator-permissions)

Användare med den här rollen kan öppna supportbegäranden med Microsoft för Azure- och Office 365-tjänster och visa tjänstinstrumentpanelen och meddelandecentret i [Azure-portalen](https://portal.azure.com) och [Microsoft 365-administrationscentret](https://admin.microsoft.com). Mer information på [Om administratörsroller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> Tidigare kallades den här rollen "Tjänstadministratör" i [Azure Portal](https://portal.azure.com) och [Microsoft 365 admincenter](https://admin.microsoft.com). Vi har bytt namn till "Service Support Administrator" för att anpassa sig till det exsiting namnet i Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell.

### <a name="sharepoint-administrator"></a>[SharePoint-administratör](#sharepoint-service-administrator-permissions)

Användare med den här rollen har globala behörigheter i Microsoft SharePoint Online, när tjänsten finns, samt möjligheten att skapa och hantera alla Office 365-grupper, hantera supportärenden och övervaka tjänstens hälsotillstånd. Mer information på [Om administratörsroller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> I Microsoft Graph API och Azure AD PowerShell identifieras den här rollen som "SharePoint-tjänstadministratör". Det är "SharePoint Administrator" i [Azure-portalen](https://portal.azure.com).

### <a name="skype-for-business--lync-administrator"></a>[Skype för företag / Lync-administratör](#lync-service-administrator-permissions)

Användare med den här rollen har globala behörigheter i Microsoft Skype för företag, när tjänsten finns, samt hanterar Skype-specifika användarattribut i Azure Active Directory. Dessutom ger den här rollen möjlighet att hantera supportärenden och övervaka tjänstens hälsotillstånd och att komma åt administrationscentret för team och Skype för företag. Kontot måste också vara licensierat för Teams eller så kan det inte köra Teams PowerShell-cmdlets. Mer information finns i [Om administratörsrollen för Skype för företag](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) och licensinformation om Teams i [tilläggslicensiering för Skype för företag och Microsoft Teams](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

> [!NOTE]
> I Microsoft Graph API och Azure AD PowerShell identifieras den här rollen som "Lync-tjänstadministratör". Det är "Skype för företag-administratör" i [Azure-portalen](https://portal.azure.com/).

### <a name="teams-communications-administrator"></a>[Team Kommunikationsadministratör](#teams-communications-administrator-permissions)

Användare i den här rollen kan hantera aspekter av Microsoft Teams-arbetsbelastningen som är relaterad till rösttelefoni & telefoni. Detta inkluderar hanteringsverktygen för telefonnummertilldelning, röst- och mötesprinciper och fullständig åtkomst till verktygen för samtalsanalys.

### <a name="teams-communications-support-engineer"></a>[Supporttekniker för team](#teams-communications-support-engineer-permissions)

Användare i den här rollen kan felsöka kommunikationsproblem i Microsoft Teams & Skype för företag med hjälp av felsökningsverktygen för användarsamtal i administrationscentret för Microsoft Teams & Skype för företag. Användare i den här rollen kan visa fullständig samtalspostinformation för alla inblandade deltagare. Den här rollen har ingen åtkomst till att visa, skapa eller hantera supportärenden.

### <a name="teams-communications-support-specialist"></a>[Team Kommunikationssupport Specialist](#teams-communications-support-specialist-permissions)

Användare i den här rollen kan felsöka kommunikationsproblem i Microsoft Teams & Skype för företag med hjälp av felsökningsverktygen för användarsamtal i administrationscentret för Microsoft Teams & Skype för företag. Användare i den här rollen kan bara visa användarinformation i anropet för den specifika användare som de har tittat upp. Den här rollen har ingen åtkomst till att visa, skapa eller hantera supportärenden.

### <a name="teams-service-administrator"></a>[Administratör för teamtjänsten](#teams-service-administrator-permissions)

Användare i den här rollen kan hantera alla aspekter av Microsoft Teams-arbetsbelastningen via Microsoft Teams & administrationscentret för Skype för företag och respektive PowerShell-moduler. Detta inkluderar bland annat alla hanteringsverktyg relaterade till telefoni, meddelanden, möten och teamen själva. Den här rollen ger dessutom möjlighet att skapa och hantera alla Office 365-grupper, hantera supportärenden och övervaka tjänstens hälsotillstånd.

### <a name="user-administrator"></a>[Användaradministratör](#user-administrator-permissions)

Användare med den här rollen kan skapa användare och hantera alla aspekter av användare med vissa begränsningar (se nedan) och kan uppdatera principer för förfallodatum för lösenord. Dessutom kan användare med den här rollen skapa och hantera alla grupper. Den här rollen omfattar också möjligheten att skapa och hantera användarvyer, hantera supportärenden och övervaka tjänstens hälsa. Användaradministratörer har inte behörighet att hantera vissa användaregenskaper för användare i de flesta administratörsroller. Användare med den här rollen har inga fel för att hantera MFA. Rollerna som är undantag från den här begränsningen visas i följande tabell.

| | |
| --- | --- |
|Allmänna behörigheter|<p>Skapa användare och grupper</p><p>Skapa och hantera användarvyer</p><p>Hantera Office-supportbiljetter<p>Uppdatera principer för förfallodatum för lösenord|
|<p>På alla användare, inklusive alla administratörer</p>|<p>Hantera licenser</p><p>Hantera alla användaregenskaper utom användarens huvudnamn</p>
|Endast för användare som inte är administratörer eller i någon av följande begränsade administratörsroller:<ul><li>Katalogläsare<li>Gäst inbjudna<li>Administratör för helpdesk<li>Läsare av Meddelandecenter<li>Rapporter Reader<li>Användaradministratör|<p>Ta bort och återställa</p><p>Inaktivera och aktivera</p><p>Ogiltigförklara uppdateringstoken</p><p>Hantera alla användaregenskaper, inklusive användarnamn</p><p>Återställa lösenord</p><p>Uppdatera (FIDO)-enhetsnycklar</p>|

> [!IMPORTANT]
> Användare med den här rollen kan ändra lösenord för personer som kan ha åtkomst till känslig eller privat information eller kritisk konfiguration i och utanför Azure Active Directory. Att ändra lösenordet för en användare kan innebära möjligheten att anta att användarens identitet och behörigheter. Ett exempel:
>
>- Programregistrering och företagsprogramägare, som kan hantera autentiseringsuppgifter för appar som de äger. Dessa appar kan ha privilegierade behörigheter i Azure AD och på andra ställen som inte beviljas användaradministratörer. Genom den här sökvägen kan en användaradministratör ta på sig identiteten för en programägare och sedan ta på sig identiteten för ett privilegierat program genom att uppdatera autentiseringsuppgifterna för programmet.
>- Azure-prenumerationsägare, som kan ha åtkomst till känslig eller privat information eller kritisk konfiguration i Azure.
>- Säkerhetsgrupp och Office 365-gruppägare, som kan hantera gruppmedlemskap. Dessa grupper kan bevilja åtkomst till känslig eller privat information eller kritisk konfiguration i Azure AD och på andra ställen.
>- Administratörer i andra tjänster utanför Azure AD som Exchange Online, Office Security and Compliance Center och personalsystem.
>- Icke-administratörer som chefer, juridiska ombud och personalanställda som kan ha tillgång till känslig eller privat information.

## <a name="role-permissions"></a>Rollbehörigheter

I följande tabeller beskrivs de specifika behörigheter i Azure Active Directory som ges till varje roll. Vissa roller kan ha ytterligare behörigheter i Microsoft-tjänster utanför Azure Active Directory.

### <a name="application-administrator-permissions"></a>Behörigheter för programadministratör

Kan skapa och hantera alla aspekter av appregistreringar och företagsappar.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/Application/appProxyAuthentication/update | Uppdatera egenskaper för appproxyautentisering på tjänstobjektnamn i Azure Active Directory. |
| microsoft.directory/Application/appProxyUrlSettings/update | Uppdatera interna och externa URLS för programproxy i Azure Active Directory. |
| microsoft.directory/applications/applicationProxy/read microsoft.directory/applications/applicationProxy/read microsoft.directory/applications/applicationProxy/read microsoft. | Läs alla appproxyegenskaper. |
| microsoft.directory/applications/applicationProxy/update microsoft.directory/applications/applicationProxy/update microsoft.directory/applications/applicationProxy/update microsoft. | Uppdatera alla egenskaper för App Proxy. |
| microsoft.directory/applications/audience/update | Uppdatera egenskapen applications.audience i Azure Active Directory. |
| microsoft.directory/program/autentisering/uppdatering | Uppdatera egenskapen applications.authentication i Azure Active Directory. |
| microsoft.directory/applications/basic/update | Uppdatera grundläggande egenskaper för program i Azure Active Directory. |
| microsoft.directory/applications/create | Skapa program i Azure Active Directory. |
| microsoft.directory/program/autentiseringsuppgifter/uppdatering | Uppdatera egenskapen applications.credentials i Azure Active Directory. |
| microsoft.directory/program/delete | Ta bort program i Azure Active Directory. |
| microsoft.directory/applications/owners/update | Uppdatera egenskapen applications.owners i Azure Active Directory. |
| microsoft.directory/program/behörigheter/uppdatering | Uppdatera egenskapen applications.permissions i Azure Active Directory. |
| microsoft.directory/applications/policies/update | Uppdatera egenskapen applications.policies i Azure Active Directory. |
| microsoft.directory/appRoleAssignments/create | Skapa appRoleAstilldelningar i Azure Active Directory. |
| microsoft.directory/appRoleAssignments/read microsoft.directory/appRoleAssignments/read microsoft.directory/appRoleAssignments/read microsoft. | Läs appRoleAstilldelningar i Azure Active Directory. |
| microsoft.directory/appRoleAssignments/update | Uppdatera appRoleAstilldelningar i Azure Active Directory. |
| microsoft.directory/appRoleAssignments/delete microsoft.directory/appRoleAssignments/delete microsoft.directory/appRoleAssignments/delete microsoft. | Ta bort appRoleAstilldelningar i Azure Active Directory. |
| microsoft.directory/auditLogs/allProperties/read | Läs alla egenskaper (inklusive privilegierade egenskaper) på auditLogs i Azure Active Directory. |
| microsoft.directory/connectorGroups/everything/read | Läs egenskaperna för programproxykopplingskopplingsgrupp i Azure Active Directory. |
| microsoft.directory/connectorGroups/everything/update | Uppdatera alla egenskaper för programproxykopplingsgrupp i Azure Active Directory. |
| microsoft.directory/connectorGroups/create | Skapa programproxykopplingsgrupper i Azure Active Directory. |
| microsoft.directory/connectorGroups/delete | Ta bort programproxykopplingsgrupper i Azure Active Directory. |
| microsoft.directory/connectors/everything/read | Läs alla programproxyanslutningsegenskaper i Azure Active Directory. |
| microsoft.directory/connectors/create | Skapa programproxyanslutningar i Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/basic/read | Läs egenskapen policies.applicationConfiguration i Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/basic/update | Uppdatera egenskapen policies.applicationConfiguration i Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/create | Skapa principer i Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/delete microsoft.directory/policies/applicationConfiguration/delete microsoft.directory/policies/applicationConfiguration/delete microsoft. | Ta bort principer i Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/owners/read | Läs egenskapen policies.applicationConfiguration i Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/owners/update | Uppdatera egenskapen policies.applicationConfiguration i Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/policyAppliedTo/read | Läs egenskapen policies.applicationConfiguration i Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Uppdatera servicePrincipals.appRoleAssignedTo-egenskap i Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAstilldelningar/uppdatering | Uppdatera servicePrincipals.appRoleAssignments egenskap i Azure Active Directory. |
| microsoft.directory/servicePrincipals/publik/uppdatering | Uppdatera egenskapen ServicePrincipals.audience i Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Uppdatera egenskapen ServicePrincipals.authentication i Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Uppdatera grundläggande egenskaper för servicePrincipals i Azure Active Directory. |
| microsoft.directory/servicePrincipals/create | Skapa servicePrincipals i Azure Active Directory. |
| microsoft.directory/servicePrincipals/autentiseringsuppgifter/uppdatering | Uppdatera egenskapen servicePrincipals.credentials i Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | Ta bort servicePrinciper i Azure Active Directory. |
| microsoft.directory/servicePrincipals/ägare/uppdatering | Uppdatera servicePrincipals.owners-egenskapen i Azure Active Directory. |
| microsoft.directory/servicePrincipals/behörigheter/uppdatering | Uppdatera egenskapen servicePrincipals.permissions i Azure Active Directory. |
| microsoft.directory/servicePrincipals/principer/uppdatering | Uppdatera egenskapen servicePrincipals.policies i Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read microsoft.directory/signInReports/allProperties/read microsoft.directory/signInReports/allProperties/read microsoft. | Läs alla egenskaper (inklusive privilegierade egenskaper) vid inloggningar i Azure Active Directory. |
| microsoft.azure.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbiljetter. |
| microsoft.office365.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbiljetter. |

### <a name="application-developer-permissions"></a>Behörigheter för programutvecklare

Kan skapa programregistreringar oberoende av inställningen "Användare kan registrera program".

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/applications/createAsOwner | Skapa program i Azure Active Directory. Skaparen läggs till som den första ägaren och det skapade objektet räknas mot skaparens 250 skapade objektkvot. |
| microsoft.directory/appRoleAssignments/createAsOwner | Skapa appRoleAstilldelningar i Azure Active Directory. Skaparen läggs till som den första ägaren och det skapade objektet räknas mot skaparens 250 skapade objektkvot. |
| microsoft.directory/oAuth2PermissionGrants/createAsOwner | Skapa oAuth2PermissionGrants i Azure Active Directory. Skaparen läggs till som den första ägaren och det skapade objektet räknas mot skaparens 250 skapade objektkvot. |
| microsoft.directory/servicePrincipals/createAsOwner | Skapa servicePrincipals i Azure Active Directory. Skaparen läggs till som den första ägaren och det skapade objektet räknas mot skaparens 250 skapade objektkvot. |

### <a name="authentication-administrator-permissions"></a>Behörigheter för autentiseringsadministratör

Tillåts visa, ange och återställa information om autentiseringsmetod för alla användare som inte är administratörer.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/users/invalidateAllRefreshTokens | Ogiltigförklara alla användaruppdateringstoken i Azure Active Directory. |
| microsoft.directory/users/strongAuthentication/update microsoft.directory/users/strongAuthentication/update microsoft.directory/users/strongAuthentication/update microsoft. | Uppdatera starka autentiseringsegenskaper som MFA-autentiseringsuppgifter. |
| microsoft.azure.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbiljetter. |
| microsoft.office365.webPortal/alla enheter/grundläggande/läs | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbiljetter. |
| microsoft.directory/users/password/update | Uppdatera lösenord för alla användare i Office 365-organisationen. Mer information finns i onlinedokumentationen. |

### <a name="azure-devops-administrator-permissions"></a>Azure DevOps-administratörsbehörigheter

Kan hantera Azure DevOps organisationsprincip och inställningar.

> [!NOTE]
> Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i [rollbeskrivning](#azure-devops-administrator) ovan.
>
>

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.devOps/allEntities/allTasks | Läs och konfigurera Azure DevOps. |

### <a name="azure-information-protection-administrator-permissions"></a>Azure Information Protection Administrator behörigheter

Kan hantera alla aspekter av Azure Information Protection-tjänsten.

> [!NOTE]
> Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i [rollbeskrivning](#) ovan.
>
>

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Hantera alla aspekter av Azure Information Protection. |
| microsoft.azure.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbiljetter. |
| microsoft.office365.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbiljetter. |

### <a name="b2c-user-flow-administrator-permissions"></a>B2C Administratörsbehörighet för användarflöde

Skapa och hantera alla aspekter av användarflöden.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.b2c/userFlows/allTasks | Läs och konfigurera användarflöden i Azure Active Directory B2C. |

### <a name="b2c-user-flow-attribute-administrator-permissions"></a>Administratörsbehörigheter för användarflödesattribut för B2C

Skapa och hantera attributschemat som är tillgängligt för alla användarflöden.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.b2c/userAttributes/allTasks | Läs och konfigurera användarattribut i Azure Active Directory B2C. |

### <a name="b2c-ief-keyset-administrator-permissions"></a>B2C IEF Keyset Administratörsbehörigheter

Hantera hemligheter för federation och kryptering i Identity Experience Framework.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/keySets/allTasks | Läs och konfigurera nyckeluppsättningar i Azure Active Directory B2C. |

### <a name="b2c-ief-policy-administrator-permissions"></a>Behörigheter för B2C IEF-principadministratör

Skapa och hantera ramprinciper för förtroende i Identity Experience Framework.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/policies/allTasks | Läs och konfigurera anpassade principer i Azure Active Directory B2C. |

### <a name="billing-administrator-permissions"></a>Behörigheter för faktureringsadministratör

Kan utföra vanliga faktureringsrelaterade uppgifter som att uppdatera betalningsinformation.

> [!NOTE]
> Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollbeskrivning ovan.
>
>

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/organization/basic/update | Uppdatera grundläggande egenskaper för organisation i Azure Active Directory. |
| microsoft.azure.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbiljetter. |
| microsoft.commerce.billing/alla enheter/allaTasker | Hantera alla aspekter av Office 365-fakturering. |
| microsoft.office365.webPortal/alla enheter/grundläggande/läs | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbiljetter. |

### <a name="cloud-application-administrator-permissions"></a>Behörigheter för molnprogramadministratör

Kan skapa och hantera alla aspekter av appregistreringar och företagsappar utom App Proxy.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/applications/audience/update | Uppdatera egenskapen applications.audience i Azure Active Directory. |
| microsoft.directory/program/autentisering/uppdatering | Uppdatera egenskapen applications.authentication i Azure Active Directory. |
| microsoft.directory/applications/basic/update | Uppdatera grundläggande egenskaper för program i Azure Active Directory. |
| microsoft.directory/applications/create | Skapa program i Azure Active Directory. |
| microsoft.directory/program/autentiseringsuppgifter/uppdatering | Uppdatera egenskapen applications.credentials i Azure Active Directory. |
| microsoft.directory/program/delete | Ta bort program i Azure Active Directory. |
| microsoft.directory/applications/owners/update | Uppdatera egenskapen applications.owners i Azure Active Directory. |
| microsoft.directory/program/behörigheter/uppdatering | Uppdatera egenskapen applications.permissions i Azure Active Directory. |
| microsoft.directory/applications/policies/update | Uppdatera egenskapen applications.policies i Azure Active Directory. |
| microsoft.directory/appRoleAssignments/create | Skapa appRoleAstilldelningar i Azure Active Directory. |
| microsoft.directory/appRoleAssignments/update | Uppdatera appRoleAstilldelningar i Azure Active Directory. |
| microsoft.directory/appRoleAssignments/delete microsoft.directory/appRoleAssignments/delete microsoft.directory/appRoleAssignments/delete microsoft. | Ta bort appRoleAstilldelningar i Azure Active Directory. |
| microsoft.directory/auditLogs/allProperties/read | Läs alla egenskaper (inklusive privilegierade egenskaper) på auditLogs i Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/create | Skapa principer i Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/basic/read | Läs egenskapen policies.applicationConfiguration i Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/basic/update | Uppdatera egenskapen policies.applicationConfiguration i Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/delete microsoft.directory/policies/applicationConfiguration/delete microsoft.directory/policies/applicationConfiguration/delete microsoft. | Ta bort principer i Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/owners/read | Läs egenskapen policies.applicationConfiguration i Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/owners/update | Uppdatera egenskapen policies.applicationConfiguration i Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/policyAppliedTo/read | Läs egenskapen policies.applicationConfiguration i Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Uppdatera servicePrincipals.appRoleAssignedTo-egenskap i Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAstilldelningar/uppdatering | Uppdatera servicePrincipals.appRoleAssignments egenskap i Azure Active Directory. |
| microsoft.directory/servicePrincipals/publik/uppdatering | Uppdatera egenskapen ServicePrincipals.audience i Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Uppdatera egenskapen ServicePrincipals.authentication i Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Uppdatera grundläggande egenskaper för servicePrincipals i Azure Active Directory. |
| microsoft.directory/servicePrincipals/create | Skapa servicePrincipals i Azure Active Directory. |
| microsoft.directory/servicePrincipals/autentiseringsuppgifter/uppdatering | Uppdatera egenskapen servicePrincipals.credentials i Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | Ta bort servicePrinciper i Azure Active Directory. |
| microsoft.directory/servicePrincipals/ägare/uppdatering | Uppdatera servicePrincipals.owners-egenskapen i Azure Active Directory. |
| microsoft.directory/servicePrincipals/behörigheter/uppdatering | Uppdatera egenskapen servicePrincipals.permissions i Azure Active Directory. |
| microsoft.directory/servicePrincipals/principer/uppdatering | Uppdatera egenskapen servicePrincipals.policies i Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read microsoft.directory/signInReports/allProperties/read microsoft.directory/signInReports/allProperties/read microsoft. | Läs alla egenskaper (inklusive privilegierade egenskaper) vid inloggningar i Azure Active Directory. |
| microsoft.azure.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbiljetter. |
| microsoft.office365.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbiljetter. |

### <a name="cloud-device-administrator-permissions"></a>Behörigheter för molnenhetsadministratör

Fullständig åtkomst för att hantera enheter i Azure AD.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Läs alla egenskaper (inklusive privilegierade egenskaper) på auditLogs i Azure Active Directory. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read microsoft.directory/devices/bitLockerRecoveryKeys/read microsoft.directory/devices/bitLockerRecoveryKeys/read microsoft. | Läs egenskapen devices.bitLockerRecoveryKeys i Azure Active Directory. |
| microsoft.directory/devices/delete microsoft.directory/devices/delete microsoft.directory/devices/delete microsoft. | Ta bort enheter i Azure Active Directory. |
| microsoft.directory/devices/disable | Inaktivera enheter i Azure Active Directory. |
| microsoft.directory/devices/enable | Aktivera enheter i Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read microsoft.directory/signInReports/allProperties/read microsoft.directory/signInReports/allProperties/read microsoft. | Läs alla egenskaper (inklusive privilegierade egenskaper) vid inloggningar i Azure Active Directory. |
| microsoft.azure.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Azure Service Health. |
| microsoft.office365.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Office 365 Service Health. |

### <a name="company-administrator-permissions"></a>Behörigheter för företagsadministratör

Kan hantera alla aspekter av Azure AD och Microsoft-tjänster som använder Azure AD-identiteter. Den här rollen kallas även rollen Global administratör. 

> [!NOTE]
> Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollbeskrivning ovan.
>
>

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Skapa och ta bort alla resurser och läs och uppdatera standardegenskaper i microsoft.aad.cloudAppSecurity. |
| microsoft.directory/administrativeUnits/allProperties/allTasks | Skapa och ta bort administrationUnits och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.directory/applications/allProperties/allTasks | Skapa och ta bort program och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.directory/appRoleAssignments/allProperties/allTasks | Skapa och ta bort appRoleAstilldelningar och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.directory/auditLogs/allProperties/read | Läs alla egenskaper (inklusive privilegierade egenskaper) på auditLogs i Azure Active Directory. |
| microsoft.directory/contacts/allProperties/allTasks | Skapa och ta bort kontakter och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.directory/contracts/allProperties/allTasks | Skapa och ta bort kontrakt och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.directory/devices/allProperties/allTasks | Skapa och ta bort enheter och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.directory/directoryRoles/allProperties/allTasks | Skapa och ta bort katalogRoller och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.directory/directoryRoleTemplates/allProperties/allTasks | Skapa och ta bort katalogRoleTemplates och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.directory/domains/allProperties/allTasks | Skapa och ta bort domäner och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.directory/groups/allProperties/allTasks | Skapa och ta bort grupper och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.directory/groupSettings/allProperties/allTasks | Skapa och ta bort groupSettings och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.directory/groupSettingTemplates/allProperties/allTasks | Skapa och ta bort groupSettingTemplates och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.directory/loginTenantBranding/allProperties/allTasks | Skapa och ta bort loginTenantBranding och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Skapa och ta bort oAuth2PermissionGrants och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.directory/organization/allProperties/allTasks | Skapa och ta bort organisation och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.directory/policies/allProperties/allTasks | Skapa och ta bort principer och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.directory/roleAssignments/allProperties/allTasks | Skapa och ta bort rollTilldelningar och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.directory/roleDefinitions/allProperties/allTasks | Skapa och ta bort roleDefinitioner och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.directory/scopedRoleMemberships/allProperties/allTasks | Skapa och ta bort scopedRoleMemberships och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.directory/serviceAction/activateService | Kan utföra åtgärden Aktivera tjänst i Azure Active Directory |
| microsoft.directory/serviceAction/disableDirectoryFeature | Kan utföra åtgärden Disabledirectoryfeature-tjänst i Azure Active Directory |
| microsoft.directory/serviceAction/enableDirectoryFeature | Kan utföra tjänsten Enabledirectoryfeature i Azure Active Directory |
| microsoft.directory/serviceAction/getAvailableExtentionProperties | Kan utföra tjänsten Getavailableextentionproperties i Azure Active Directory |
| microsoft.directory/servicePrincipals/allProperties/allTasks | Skapa och ta bort servicePrincipaler och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read microsoft.directory/signInReports/allProperties/read microsoft.directory/signInReports/allProperties/read microsoft. | Läs alla egenskaper (inklusive privilegierade egenskaper) vid inloggningar i Azure Active Directory. |
| microsoft.directory/subscribedSkus/allProperties/allTasks | Skapa och ta bort subscribedSkus och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.directory/users/allProperties/allTasks | Skapa och ta bort användare och läs och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.directorySync/alla enheter/allaTasks | Utför alla åtgärder i Azure AD Connect. |
| microsoft.aad.identityProtection/allEntities/allTasks | Skapa och ta bort alla resurser och läs och uppdatera standardegenskaper i microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/alla enheter/läsa | Läs alla resurser i microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Läs alla resurser i microsoft.azure.advancedThreatProtection. |
| microsoft.azure.informationProtection/allEntities/allTasks | Hantera alla aspekter av Azure Information Protection. |
| microsoft.azure.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbiljetter. |
| microsoft.commerce.billing/alla enheter/allaTasker | Hantera alla aspekter av Office 365-fakturering. |
| microsoft.intune/allEntities/allTasks | Hantera alla aspekter av Intune. |
| microsoft.office365.complianceManager/alla enheter/allaTasks | Hantera alla aspekter av Efterlevnadshanteraren för Office 365 |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Hantera alla aspekter av Desktop Analytics. |
| microsoft.office365.exchange/alla enheter/allaTasker | Hantera alla aspekter av Exchange Online. |
| microsoft.office365.lockbox/alla enheter/allaTasker | Hantera alla aspekter av Office 365 Customer Lockbox |
| microsoft.office365.messageCenter/messages/read | Läs meddelanden i microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Läs securityMessages i microsoft.office365.messageCenter. |
| microsoft.office365.protectionCenter/alla enheter/allaTasker | Hantera alla aspekter av Office 365 Protection Center. |
| microsoft.office365.securityComplianceCenter/alla enheter/allaTasks | Skapa och ta bort alla resurser och läs och uppdatera standardegenskaper i microsoft.office365.securityComplianceCenter. |
| microsoft.office365.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.sharepoint/alla enheter/allaTasker | Skapa och ta bort alla resurser och läs och uppdatera standardegenskaper i microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/alla enheter/allaTasks | Hantera alla aspekter av Skype för företag – Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbiljetter. |
| microsoft.office365.usageReports/alla entiteter/läsa | Läs användningsrapporter för Office 365. |
| microsoft.office365.webPortal/alla enheter/grundläggande/läs | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Hantera alla aspekter av Dynamics 365. |
| microsoft.powerApps.powerBI/alla enheter/allaTasker | Hantera alla aspekter av Power BI. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Läs alla resurser i microsoft.windows.defenderAdvancedThreatProtection. |

### <a name="compliance-administrator-permissions"></a>Behörigheter för efterlevnadsadministratör

Kan läsa och hantera efterlevnadskonfiguration och rapporter i Azure AD och Office 365.

> [!NOTE]
> Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollbeskrivning ovan.
>
>

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbiljetter. |
| microsoft.office365.webPortal/alla enheter/grundläggande/läs | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.complianceManager/alla enheter/allaTasks | Hantera alla aspekter av Efterlevnadshanteraren för Office 365 |
| microsoft.office365.exchange/alla enheter/allaTasker | Hantera alla aspekter av Exchange Online. |
| microsoft.office365.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.sharepoint/alla enheter/allaTasker | Skapa och ta bort alla resurser och läs och uppdatera standardegenskaper i microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/alla enheter/allaTasks | Hantera alla aspekter av Skype för företag – Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbiljetter. |

### <a name="compliance-data-administrator-permissions"></a>Behörigheter för efterlevnadsdataadministratör

Skapar och hanterar efterlevnadsinnehåll.

> [!NOTE]
> Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollbeskrivning ovan.
>
>

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Läs och konfigurera Microsoft Cloud App Security. |
| microsoft.azure.informationProtection/allEntities/allTasks | Hantera alla aspekter av Azure Information Protection. |
| microsoft.azure.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbiljetter. |
| microsoft.office365.webPortal/alla enheter/grundläggande/läs | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.complianceManager/alla enheter/allaTasks | Hantera alla aspekter av Efterlevnadshanteraren för Office 365 |
| microsoft.office365.exchange/alla enheter/allaTasker | Hantera alla aspekter av Exchange Online. |
| microsoft.office365.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.sharepoint/alla enheter/allaTasker | Skapa och ta bort alla resurser och läs och uppdatera standardegenskaper i microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/alla enheter/allaTasks | Hantera alla aspekter av Skype för företag – Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbiljetter. |

### <a name="conditional-access-administrator-permissions"></a>Administratörsbehörigheter för villkorlig åtkomst

Kan hantera funktioner för villkorlig åtkomst.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/policies/conditionalAccess/basic/read | Läs principer.conditionalAccess-egenskapen i Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/basic/update | Uppdatera principer.conditionalAccess-egenskapen i Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/create | Skapa principer i Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/delete | Ta bort principer i Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/owners/read | Läs principer.conditionalAccess-egenskapen i Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/owners/update | Uppdatera principer.conditionalAccess-egenskapen i Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/policiesAppliedTo/read | Läs principer.conditionalAccess-egenskapen i Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/tenantDefault/update | Uppdatera principer.conditionalAccess-egenskapen i Azure Active Directory. |

### <a name="crm-service-administrator-permissions"></a>Behörigheter för CRM-tjänstadministratör

Kan hantera alla aspekter av Dynamics 365-produkten.

> [!NOTE]
> Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollbeskrivning ovan.
>
>

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbiljetter. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Hantera alla aspekter av Dynamics 365. |
| microsoft.office365.webPortal/alla enheter/grundläggande/läs | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbiljetter. |

### <a name="customer-lockbox-access-approver-permissions"></a>Behörigheter för Customer LockBox Access Approveer

Kan godkänna Microsofts supportbegäranden om åtkomst till kundorganisationsdata.

> [!NOTE]
> Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollbeskrivning ovan.
>
>

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.office365.webPortal/alla enheter/grundläggande/läs | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.lockbox/alla enheter/allaTasker | Hantera alla aspekter av Office 365 Customer Lockbox |

### <a name="desktop-analytics-administrator-permissions"></a>Administratörsbehörigheter för Skrivbordsanalys

Kan hantera desktop analytics- och Office-anpassningstjänster & policytjänster. För Desktop Analytics omfattar detta möjligheten att visa tillgångsinventering, skapa distributionsplaner, visa distribution och hälsostatus. För tjänsten Anpassning av Office & princip gör den här rollen det möjligt för användare att hantera Office-principer.

> [!NOTE]
> Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollbeskrivning ovan.
>
>

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbiljetter. |
| microsoft.office365.webPortal/alla enheter/grundläggande/läs | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Hantera alla aspekter av Desktop Analytics. |
| microsoft.office365.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbiljetter. |

### <a name="device-administrators-permissions"></a>Behörigheter för enhetsadministratörer

Användare som tilldelats den här rollen läggs till i den lokala administratörsgruppen på Azure AD-anslutna enheter.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/groupSettings/basic/read | Läs grundläggande egenskaper för groupSettings i Azure Active Directory. |
| microsoft.directory/groupSettingTemplates/basic/read | Läs grundläggande egenskaper på groupSettingTemplates i Azure Active Directory. |

### <a name="directory-readers-permissions"></a>Behörigheter för katalogläsare
Kan läsa grundläggande kataloginformation. För att bevilja åtkomst till program, inte avsedd för användare.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/administrativeUnits/basic/read | Läs grundläggande egenskaper på administrationUnits i Azure Active Directory. |
| microsoft.directory/administrativeUnits/members/read | Läs egenskapen administrativeUnits.members i Azure Active Directory. |
| microsoft.directory/applications/basic/read | Läs grundläggande egenskaper för program i Azure Active Directory. |
| microsoft.directory/applications/owners/read microsoft.directory/applications/owners/read microsoft.directory/applications/owners/read microsoft. | Läs egenskapen applications.owners i Azure Active Directory. |
| microsoft.directory/applications/policies/read | Läs egenskapen applications.policies i Azure Active Directory. |
| microsoft.directory/contacts/basic/read | Läs grundläggande egenskaper för kontakter i Azure Active Directory. |
| microsoft.directory/contacts/memberOf/read microsoft.directory/contacts/memberOf/read microsoft.directory/contacts/memberOf/read microsoft. | Läs contacts.memberOf-egenskap i Azure Active Directory. |
| microsoft.directory/contracts/basic/read | Läs grundläggande egenskaper för kontrakt i Azure Active Directory. |
| microsoft.directory/devices/basic/read | Läs grundläggande egenskaper på enheter i Azure Active Directory. |
| microsoft.directory/devices/memberOf/read microsoft.directory/devices/memberOf/read microsoft.directory/devices/memberOf/read microsoft. | Läs devices.memberOf-egenskap i Azure Active Directory. |
| microsoft.directory/devices/registeredOwners/read microsoft.directory/devices/registeredOwners/read microsoft.directory/devices/registeredOwners/read microsoft. | Läs devices.registeredOwners-egenskapen i Azure Active Directory. |
| microsoft.directory/devices/registeredUsers/read | Läs devices.registeredAnvändare-egenskapen i Azure Active Directory. |
| microsoft.directory/directoryRoller/grundläggande/läs | Läs grundläggande egenskaper på katalogroller i Azure Active Directory. |
| microsoft.directory/directoryRoles/eligibleMembers/read microsoft.directory/directoryRoles/eligibleMembers/read microsoft.directory/directoryRoles/eligibleMembers/read microsoft. | Läs egenskapen DirectoryRoles.eligibleMembers i Azure Active Directory. |
| microsoft.directory/directoryRoller/medlemmar/läsa | Läs egenskapen DirectoryRoles.members i Azure Active Directory. |
| microsoft.directory/domains/basic/read | Läs grundläggande egenskaper för domäner i Azure Active Directory. |
| microsoft.directory/groups/appRoleAssignments/read microsoft.directory/groups/appRoleAssignments/read microsoft.directory/groups/appRoleAssignments/read microsoft. | Läs egenskapen groups.appRoleAssignments i Azure Active Directory. |
| microsoft.directory/groups/basic/read | Läs grundläggande egenskaper för grupper i Azure Active Directory. |
| microsoft.directory/groups/memberOf/read | Läs groups.memberOf-egenskap i Azure Active Directory. |
| microsoft.directory/groups/members/read | Egenskapen Read groups.members i Azure Active Directory. |
| microsoft.directory/groups/owners/read | Egenskapen Read groups.owners i Azure Active Directory. |
| microsoft.directory/groups/settings/read microsoft.directory/groups/settings/read microsoft.directory/groups/settings/read microsoft. | Läs egenskapen groups.settings i Azure Active Directory. |
| microsoft.directory/groupSettings/basic/read | Läs grundläggande egenskaper för groupSettings i Azure Active Directory. |
| microsoft.directory/groupSettingTemplates/basic/read | Läs grundläggande egenskaper på groupSettingTemplates i Azure Active Directory. |
| microsoft.directory/oAuth2PermissionGrants/basic/read microsoft.directory/oAuth2PermissionGrants/basic/read microsoft.directory/oAuth2PermissionGrants/basic/read microsoft. | Läs grundläggande egenskaper på oAuth2PermissionGrants i Azure Active Directory. |
| microsoft.directory/organization/basic/read | Läs grundläggande egenskaper för organisation i Azure Active Directory. |
| microsoft.directory/organization/trustedCAsForPasswordlessAuth/read microsoft.directory/organization/trustedCAsForPasswordlessAuth/read microsoft.directory/organization/trustedCAsForPasswordlessAuth/read microsoft. | Läs egenskapen organization.trustedCAsForPasswordlessAuth i Azure Active Directory. |
| microsoft.directory/roleAssignments/basic/read | Läs grundläggande egenskaper för rollTilldelningar i Azure Active Directory. |
| microsoft.directory/roleDefinitioner/basic/read | Läs grundläggande egenskaper för rollDefinitioner i Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Läs servicePrincipals.appRoleAssignedTo-egenskap i Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAstilldelningar/läs | Läs servicePrincipals.appRoleAssignments egenskap i Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/read | Läs grundläggande egenskaper för servicePrincipals i Azure Active Directory. |
| microsoft.directory/servicePrincipals/memberOf/read | Läs servicePrincipals.memberOf-egenskap i Azure Active Directory. |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Läs servicePrincipals.oAuth2PermissionGrants egenskap i Azure Active Directory. |
| microsoft.directory/servicePrincipals/ownedObjects/read microsoft.directory/servicePrincipals/ownedObjects/read microsoft.directory/servicePrincipals/ownedObjects/read microsoft. | Läs servicePrincipals.ownedObjects-egenskap i Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/read | Läs servicePrincipals.owners-egenskapen i Azure Active Directory. |
| microsoft.directory/servicePrinciper/principer/läsa | Läs egenskapen servicePrincipals.policies i Azure Active Directory. |
| microsoft.directory/subscribedSkus/basic/read | Läs grundläggande egenskaper på subscribedSkus i Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/read microsoft.directory/users/appRoleAssignments/read microsoft.directory/users/appRoleAssignments/read microsoft. | Läs egenskapen users.appRoleAssignments i Azure Active Directory. |
| microsoft.directory/users/basic/read | Läs grundläggande egenskaper för användare i Azure Active Directory. |
| microsoft.directory/users/directReports/read | Läs egenskapen users.directReports i Azure Active Directory. |
| microsoft.directory/users/manager/read | Läs egenskapen users.manager i Azure Active Directory. |
| microsoft.directory/users/memberOf/read microsoft.directory/users/memberOf/read microsoft.directory/users/memberOf/read microsoft. | Läs egenskapen users.memberOf i Azure Active Directory. |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read microsoft.directory/users/oAuth2PermissionGrants/basic/read microsoft.directory/users/oAuth2PermissionGrants/basic/read microsoft. | Läs egenskapen users.oAuth2PermissionGrants i Azure Active Directory. |
| microsoft.directory/users/ownedDevices/read | Läs egenskapen users.ownedDevices i Azure Active Directory. |
| microsoft.directory/users/ownedObjects/read microsoft.directory/users/ownedObjects/read microsoft.directory/users/ownedObjects/read microsoft. | Läs egenskapen users.ownedObjects i Azure Active Directory. |
| microsoft.directory/users/registeredDevices/read microsoft.directory/users/registeredDevices/read microsoft.directory/users/registeredDevices/read microsoft. | Läs egenskapen users.registeredDevices i Azure Active Directory. |

### <a name="directory-synchronization-accounts-permissions"></a>Behörigheter för katalogsynkroniseringskonton

Används endast av Azure AD Connect-tjänsten.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/organization/dirSync/update | Uppdatera egenskapen organization.dirSync i Azure Active Directory. |
| microsoft.directory/policies/create | Skapa principer i Azure Active Directory. |
| microsoft.directory/policies/delete | Ta bort principer i Azure Active Directory. |
| microsoft.directory/policies/basic/read | Läs grundläggande egenskaper för principer i Azure Active Directory. |
| microsoft.directory/policies/basic/update | Uppdatera grundläggande egenskaper för principer i Azure Active Directory. |
| microsoft.directory/policies/owners/read | Egenskapen Read policies.owners i Azure Active Directory. |
| microsoft.directory/policies/owners/update | Egenskapen Update policies.owners i Azure Active Directory. |
| microsoft.directory/policies/policiesAppliedTo/read | Läs policies.policiesAppliedTo-egenskap i Azure Active Directory. |
| microsoft.directory/policies/tenantDefault/update | Uppdatera egenskapen policies.tenantDefault i Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Läs servicePrincipals.appRoleAssignedTo-egenskap i Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Uppdatera servicePrincipals.appRoleAssignedTo-egenskap i Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAstilldelningar/läs | Läs servicePrincipals.appRoleAssignments egenskap i Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAstilldelningar/uppdatering | Uppdatera servicePrincipals.appRoleAssignments egenskap i Azure Active Directory. |
| microsoft.directory/servicePrincipals/publik/uppdatering | Uppdatera egenskapen ServicePrincipals.audience i Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Uppdatera egenskapen ServicePrincipals.authentication i Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/read | Läs grundläggande egenskaper för servicePrincipals i Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Uppdatera grundläggande egenskaper för servicePrincipals i Azure Active Directory. |
| microsoft.directory/servicePrincipals/create | Skapa servicePrincipals i Azure Active Directory. |
| microsoft.directory/servicePrincipals/autentiseringsuppgifter/uppdatering | Uppdatera egenskapen servicePrincipals.credentials i Azure Active Directory. |
| microsoft.directory/servicePrincipals/memberOf/read | Läs servicePrincipals.memberOf-egenskap i Azure Active Directory. |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Läs servicePrincipals.oAuth2PermissionGrants egenskap i Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/read | Läs servicePrincipals.owners-egenskapen i Azure Active Directory. |
| microsoft.directory/servicePrincipals/ägare/uppdatering | Uppdatera servicePrincipals.owners-egenskapen i Azure Active Directory. |
| microsoft.directory/servicePrincipals/ownedObjects/read microsoft.directory/servicePrincipals/ownedObjects/read microsoft.directory/servicePrincipals/ownedObjects/read microsoft. | Läs servicePrincipals.ownedObjects-egenskap i Azure Active Directory. |
| microsoft.directory/servicePrincipals/behörigheter/uppdatering | Uppdatera egenskapen servicePrincipals.permissions i Azure Active Directory. |
| microsoft.directory/servicePrinciper/principer/läsa | Läs egenskapen servicePrincipals.policies i Azure Active Directory. |
| microsoft.directory/servicePrincipals/principer/uppdatering | Uppdatera egenskapen servicePrincipals.policies i Azure Active Directory. |
| microsoft.directorySync/alla enheter/allaTasks | Utför alla åtgärder i Azure AD Connect. |

### <a name="directory-writers-permissions"></a>Behörigheter för katalogförfattare

Kan läsa & skriva grundläggande kataloginformation. För att bevilja åtkomst till program, inte avsedd för användare.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/grupper/skapa | Skapa grupper i Azure Active Directory. |
| microsoft.directory/groups/createAsOwner | Skapa grupper i Azure Active Directory. Skaparen läggs till som den första ägaren och det skapade objektet räknas mot skaparens 250 skapade objektkvot. |
| microsoft.directory/groups/appRoleAssignments/update microsoft.directory/groups/appRoleAssignments/update microsoft.directory/groups/appRoleAssignments/update microsoft. | Uppdatera egenskapen groups.appRoleAssignments i Azure Active Directory. |
| microsoft.directory/groups/basic/update | Uppdatera grundläggande egenskaper för grupper i Azure Active Directory. |
| microsoft.directory/groups/members/update | Egenskapen Update groups.members i Azure Active Directory. |
| microsoft.directory/groups/owners/update | Egenskapen Update groups.owners i Azure Active Directory. |
| microsoft.directory/groups/settings/update | Egenskapen Update groups.settings i Azure Active Directory. |
| microsoft.directory/groupSettings/basic/update | Uppdatera grundläggande egenskaper för groupSettings i Azure Active Directory. |
| microsoft.directory/groupSettings/create | Skapa groupSettings i Azure Active Directory. |
| microsoft.directory/groupSettings/delete | Ta bort groupSettings i Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update microsoft.directory/users/appRoleAssignments/update microsoft.directory/users/appRoleAssignments/update microsoft. | Uppdatera egenskapen users.appRoleAssignments i Azure Active Directory. |
| microsoft.directory/users/assignLicense microsoft.directory/users/assignLicense microsoft.directory/users/assignLicense microsoft. | Hantera licenser för användare i Azure Active Directory. |
| microsoft.directory/users/basic/update | Uppdatera grundläggande egenskaper för användare i Azure Active Directory. |
| microsoft.directory/users/invalidateAllRefreshTokens | Ogiltigförklara alla användaruppdateringstoken i Azure Active Directory. |
| microsoft.directory/users/manager/update | Uppdatera egenskapen users.manager i Azure Active Directory. |
| microsoft.directory/users/userPrincipalName/update | Uppdatera egenskapen users.userPrincipalName i Azure Active Directory. |

### <a name="exchange-service-administrator-permissions"></a>Behörigheter för Exchange-tjänstadministratör

Kan hantera alla aspekter av Exchange-produkten.

> [!NOTE]
> Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollbeskrivning ovan.
>
>

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbiljetter. |
| microsoft.directory/groups/unified/appRoleAssignments/update microsoft.directory/groups/unified/appRoleAssignments/update microsoft.directory/groups/unified/appRoleAssignments/update microsoft. | Uppdatera groups.unified-egenskap i Azure Active Directory. |
| microsoft.directory/groups/unified/basic/update microsoft.directory/groups/unified/basic/update microsoft.directory/groups/unified/basic/update microsoft. | Uppdatera grundläggande egenskaper för Office 365-grupper. |
| microsoft.directory/groups/unified/create | Skapa Office 365-grupper. |
| microsoft.directory/groups/unified/delete microsoft.directory/groups/unified/delete microsoft.directory/groups/unified/delete microsoft. | Ta bort Office 365-grupper. |
| microsoft.directory/groups/unified/members/update | Uppdatera medlemskap i Office 365-grupper. |
| microsoft.directory/groups/unified/owners/update | Uppdatera ägarskapet för Office 365-grupper. |
| microsoft.office365.exchange/alla enheter/allaTasker | Hantera alla aspekter av Exchange Online. |
| microsoft.office365.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbiljetter. |
| microsoft.office365.usageReports/alla entiteter/läsa | Läs användningsrapporter för Office 365. |
| microsoft.office365.webPortal/alla enheter/grundläggande/läs | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |

### <a name="external-identity-provider-administrator-permissions"></a>Administratörsbehörigheter för extern identitetsprovider

Konfigurera identitetsleverantörer för användning i direkt federation.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.b2c/identityProviders/allTasks | Läs och konfigurera identitetsleverantörer i Azure Active Directory B2C. |

### <a name="global-reader-permissions"></a>Behörigheter för global läsare
Kan läsa allt som en global administratör kan, men inte redigera något.

> [!NOTE]
> Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i [rollbeskrivning](#global-reader) ovan.
>
>

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.commerce.billing/alla enheter/läsa    | Läs alla aspekter av Office 365-fakturering. |
| microsoft.directory/administrativeUnits/basic/read    | Läs grundläggande egenskaper på administrationUnits i Azure Active Directory. |
| microsoft.directory/administrativeUnits/members/read    | Läs egenskapen administrativeUnits.members i Azure Active Directory. |
| microsoft.directory/applications/basic/read    | Läs grundläggande egenskaper för program i Azure Active Directory. |
| microsoft.directory/applications/owners/read microsoft.directory/applications/owners/read microsoft.directory/applications/owners/read microsoft.    | Läs egenskapen applications.owners i Azure Active Directory. |
| microsoft.directory/applications/policies/read    | Läs egenskapen applications.policies i Azure Active Directory. |
| microsoft.directory/contacts/basic/read    | Läs grundläggande egenskaper för kontakter i Azure Active Directory. |
| microsoft.directory/contacts/memberOf/read microsoft.directory/contacts/memberOf/read microsoft.directory/contacts/memberOf/read microsoft.    | Läs contacts.memberOf-egenskap i Azure Active Directory. |
| microsoft.directory/contracts/basic/read    | Läs grundläggande egenskaper för kontrakt i Azure Active Directory. |
| microsoft.directory/devices/basic/read    | Läs grundläggande egenskaper på enheter i Azure Active Directory. |
| microsoft.directory/devices/memberOf/read microsoft.directory/devices/memberOf/read microsoft.directory/devices/memberOf/read microsoft.    | Läs devices.memberOf-egenskap i Azure Active Directory. |
| microsoft.directory/devices/registeredOwners/read microsoft.directory/devices/registeredOwners/read microsoft.directory/devices/registeredOwners/read microsoft.    | Läs devices.registeredOwners-egenskapen i Azure Active Directory. |
| microsoft.directory/devices/registeredUsers/read    | Läs devices.registeredAnvändare-egenskapen i Azure Active Directory. |
| microsoft.directory/directoryRoller/grundläggande/läs    | Läs grundläggande egenskaper på katalogroller i Azure Active Directory. |
| microsoft.directory/directoryRoles/eligibleMembers/read microsoft.directory/directoryRoles/eligibleMembers/read microsoft.directory/directoryRoles/eligibleMembers/read microsoft.    | Läs egenskapen DirectoryRoles.eligibleMembers i Azure Active Directory. |
| microsoft.directory/directoryRoller/medlemmar/läsa    | Läs egenskapen DirectoryRoles.members i Azure Active Directory. |
| microsoft.directory/domains/basic/read    | Läs grundläggande egenskaper för domäner i Azure Active Directory. |
| microsoft.directory/groups/appRoleAssignments/read microsoft.directory/groups/appRoleAssignments/read microsoft.directory/groups/appRoleAssignments/read microsoft.    | Läs egenskapen groups.appRoleAssignments i Azure Active Directory. |
| microsoft.directory/groups/basic/read    | Läs grundläggande egenskaper för grupper i Azure Active Directory. |
| microsoft.directory/groups/hiddenMembers/read microsoft.directory/groups/hiddenMembers/read microsoft.directory/groups/hiddenMembers/read microsoft.    | Läs egenskapen groups.hiddenMembers i Azure Active Directory. |
| microsoft.directory/groups/memberOf/read    | Läs groups.memberOf-egenskap i Azure Active Directory. |
| microsoft.directory/groups/members/read    | Egenskapen Read groups.members i Azure Active Directory. |
| microsoft.directory/groups/owners/read    | Egenskapen Read groups.owners i Azure Active Directory. |
| microsoft.directory/groups/settings/read microsoft.directory/groups/settings/read microsoft.directory/groups/settings/read microsoft.    | Läs egenskapen groups.settings i Azure Active Directory. |
| microsoft.directory/groupSettings/basic/read    | Läs grundläggande egenskaper för groupSettings i Azure Active Directory. |
| microsoft.directory/groupSettingTemplates/basic/read    | Läs grundläggande egenskaper på groupSettingTemplates i Azure Active Directory. |
| microsoft.directory/oAuth2PermissionGrants/basic/read microsoft.directory/oAuth2PermissionGrants/basic/read microsoft.directory/oAuth2PermissionGrants/basic/read microsoft.    | Läs grundläggande egenskaper på oAuth2PermissionGrants i Azure Active Directory. |
| microsoft.directory/organization/basic/read    | Läs grundläggande egenskaper för organisation i Azure Active Directory. |
| microsoft.directory/organization/trustedCAsForPasswordlessAuth/read microsoft.directory/organization/trustedCAsForPasswordlessAuth/read microsoft.directory/organization/trustedCAsForPasswordlessAuth/read microsoft.    | Läs egenskapen organization.trustedCAsForPasswordlessAuth i Azure Active Directory. |
| microsoft.directory/policies/standard/read    | Läs standardprinciper i Azure Active Directory. |
| microsoft.directory/roleAssignments/basic/read    | Läs grundläggande egenskaper för rollTilldelningar i Azure Active Directory. |
| microsoft.directory/roleDefinitioner/basic/read    | Läs grundläggande egenskaper för rollDefinitioner i Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/read    | Läs servicePrincipals.appRoleAssignedTo-egenskap i Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAstilldelningar/läs    | Läs servicePrincipals.appRoleAssignments egenskap i Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/read    | Läs grundläggande egenskaper för servicePrincipals i Azure Active Directory. |
| microsoft.directory/servicePrincipals/memberOf/read    | Läs servicePrincipals.memberOf-egenskap i Azure Active Directory. |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/basic/read    | Läs servicePrincipals.oAuth2PermissionGrants egenskap i Azure Active Directory. |
| microsoft.directory/servicePrincipals/ownedObjects/read microsoft.directory/servicePrincipals/ownedObjects/read microsoft.directory/servicePrincipals/ownedObjects/read microsoft.    | Läs servicePrincipals.ownedObjects-egenskap i Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/read    | Läs servicePrincipals.owners-egenskapen i Azure Active Directory. |
| microsoft.directory/servicePrinciper/principer/läsa    | Läs egenskapen servicePrincipals.policies i Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read microsoft.directory/signInReports/allProperties/read microsoft.directory/signInReports/allProperties/read microsoft.    | Läs alla egenskaper (inklusive privilegierade egenskaper) vid inloggningar i Azure Active Directory. |
| microsoft.directory/subscribedSkus/basic/read    | Läs grundläggande egenskaper på subscribedSkus i Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/read microsoft.directory/users/appRoleAssignments/read microsoft.directory/users/appRoleAssignments/read microsoft.    | Läs egenskapen users.appRoleAssignments i Azure Active Directory. |
| microsoft.directory/users/basic/read    | Läs grundläggande egenskaper för användare i Azure Active Directory. |
| microsoft.directory/users/directReports/read    | Läs egenskapen users.directReports i Azure Active Directory. |
| microsoft.directory/users/manager/read    | Läs egenskapen users.manager i Azure Active Directory. |
| microsoft.directory/users/memberOf/read microsoft.directory/users/memberOf/read microsoft.directory/users/memberOf/read microsoft.    | Läs egenskapen users.memberOf i Azure Active Directory. |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read microsoft.directory/users/oAuth2PermissionGrants/basic/read microsoft.directory/users/oAuth2PermissionGrants/basic/read microsoft.    | Läs egenskapen users.oAuth2PermissionGrants i Azure Active Directory. |
| microsoft.directory/users/ownedDevices/read    | Läs egenskapen users.ownedDevices i Azure Active Directory. |
| microsoft.directory/users/ownedObjects/read microsoft.directory/users/ownedObjects/read microsoft.directory/users/ownedObjects/read microsoft.    | Läs egenskapen users.ownedObjects i Azure Active Directory. |
| microsoft.directory/users/registeredDevices/read microsoft.directory/users/registeredDevices/read microsoft.directory/users/registeredDevices/read microsoft.    | Läs egenskapen users.registeredDevices i Azure Active Directory. |
| microsoft.directory/users/strongAuthentication/read microsoft.directory/users/strongAuthentication/read microsoft.directory/users/strongAuthentication/read microsoft.    | Läs starka autentiseringsegenskaper som MFA-autentiseringsinformation. |
| microsoft.office365.exchange/alla enheter/läsa    | Läs alla aspekter av Exchange Online. |
| microsoft.office365.messageCenter/messages/read    | Läs meddelanden i microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read    | Läs securityMessages i microsoft.office365.messageCenter. |
| microsoft.office365.protectionCenter/alla enheter/läsa    | Läs alla aspekter av Office 365 Protection Center. |
| microsoft.office365.securityComplianceCenter/alla enheter/läsa    | Läs alla standardegenskaper i microsoft.office365.securityComplianceCenter. |
| microsoft.office365.usageReports/alla entiteter/läsa    | Läs användningsrapporter för Office 365. |
| microsoft.office365.webPortal/alla enheter/standard/läsa    | Läs standardegenskaper för alla resurser i microsoft.office365.webPortal. |

### <a name="groups-administrator-permissions"></a>Behörigheter för grupperadministratör
Kan hantera alla aspekter av grupper och gruppinställningar som namngivning och principer för förfallodatum.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/groups/basic/read | Läs standardegenskaper för grupper i Azure Active Directory.  |
| microsoft.directory/groups/basic/update | Uppdatera grundläggande egenskaper för grupper i Azure Active Directory. |
| microsoft.directory/grupper/skapa | Skapa grupper i Azure Active Directory. |
| microsoft.directory/groups/createAsOwner | Skapa grupper i Azure Active Directory. Skaparen läggs till som den första ägaren och det skapade objektet räknas mot skaparens 250 skapade objektkvot. |
| microsoft.directory/groups/delete | Ta bort grupper i Azure Active Directory. |
| microsoft.directory/groups/hiddenMembers/read microsoft.directory/groups/hiddenMembers/read microsoft.directory/groups/hiddenMembers/read microsoft. | Läs egenskapen groups.hiddenMembers i Azure Active Directory. |
| microsoft.directory/groups/members/update | Egenskapen Update groups.members i Azure Active Directory. |
| microsoft.directory/groups/owners/update | Egenskapen Update groups.owners i Azure Active Directory. |
| microsoft.directory/groups/restore | Återställ grupper i Azure Active Directory. |
| microsoft.directory/groups/settings/update | Egenskapen Update groups.settings i Azure Active Directory. |
| microsoft.azure.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbiljetter. |
| microsoft.office365.messageCenter/messages/read | Läs meddelanden i microsoft.office365.messageCenter. |
| microsoft.office365.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbiljetter. |

### <a name="guest-inviter-permissions"></a>Behörigheter för gästtr inbjudna
Kan bjuda in gästanvändare oberoende av inställningen "medlemmar kan bjuda in gäster".

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/users/appRoleAssignments/read microsoft.directory/users/appRoleAssignments/read microsoft.directory/users/appRoleAssignments/read microsoft. | Läs egenskapen users.appRoleAssignments i Azure Active Directory. |
| microsoft.directory/users/basic/read | Läs grundläggande egenskaper för användare i Azure Active Directory. |
| microsoft.directory/users/directReports/read | Läs egenskapen users.directReports i Azure Active Directory. |
| microsoft.directory/users/inviteGuest microsoft.directory/users/inviteGuest microsoft.directory/users/inviteGuest microsoft. | Bjud in gästanvändare i Azure Active Directory. |
| microsoft.directory/users/manager/read | Läs egenskapen users.manager i Azure Active Directory. |
| microsoft.directory/users/memberOf/read microsoft.directory/users/memberOf/read microsoft.directory/users/memberOf/read microsoft. | Läs egenskapen users.memberOf i Azure Active Directory. |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read microsoft.directory/users/oAuth2PermissionGrants/basic/read microsoft.directory/users/oAuth2PermissionGrants/basic/read microsoft. | Läs egenskapen users.oAuth2PermissionGrants i Azure Active Directory. |
| microsoft.directory/users/ownedDevices/read | Läs egenskapen users.ownedDevices i Azure Active Directory. |
| microsoft.directory/users/ownedObjects/read microsoft.directory/users/ownedObjects/read microsoft.directory/users/ownedObjects/read microsoft. | Läs egenskapen users.ownedObjects i Azure Active Directory. |
| microsoft.directory/users/registeredDevices/read microsoft.directory/users/registeredDevices/read microsoft.directory/users/registeredDevices/read microsoft. | Läs egenskapen users.registeredDevices i Azure Active Directory. |

### <a name="helpdesk-administrator-permissions"></a>Administratörsbehörigheter för Helpdesk

Kan återställa lösenord för icke-administratörer och helpdeskadministratörer.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read microsoft.directory/devices/bitLockerRecoveryKeys/read microsoft.directory/devices/bitLockerRecoveryKeys/read microsoft. | Läs egenskapen devices.bitLockerRecoveryKeys i Azure Active Directory. |
| microsoft.directory/users/invalidateAllRefreshTokens | Ogiltigförklara alla användaruppdateringstoken i Azure Active Directory. |
| microsoft.directory/users/password/update | Uppdatera lösenord för alla användare i Azure Active Directory. Mer information finns i onlinedokumentationen. |
| microsoft.azure.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbiljetter. |
| microsoft.office365.webPortal/alla enheter/grundläggande/läs | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbiljetter. |

### <a name="intune-service-administrator-permissions"></a>Behörigheter för Intune-tjänstadministratör

Kan hantera alla aspekter av Intune-produkten.

> [!NOTE]
> Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollbeskrivning ovan.
>
>

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/contacts/basic/update | Uppdatera grundläggande egenskaper för kontakter i Azure Active Directory. |
| microsoft.directory/kontakter/skapa | Skapa kontakter i Azure Active Directory. |
| microsoft.directory/kontakter/ta bort | Ta bort kontakter i Azure Active Directory. |
| microsoft.directory/devices/basic/update | Uppdatera grundläggande egenskaper på enheter i Azure Active Directory. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read microsoft.directory/devices/bitLockerRecoveryKeys/read microsoft.directory/devices/bitLockerRecoveryKeys/read microsoft. | Läs egenskapen devices.bitLockerRecoveryKeys i Azure Active Directory. |
| microsoft.directory/devices/create | Skapa enheter i Azure Active Directory. |
| microsoft.directory/devices/delete microsoft.directory/devices/delete microsoft.directory/devices/delete microsoft. | Ta bort enheter i Azure Active Directory. |
| microsoft.directory/devices/registeredOwners/update | Uppdatera egenskapen devices.registeredOwners i Azure Active Directory. |
| microsoft.directory/devices/registeredUsers/update | Uppdatera egenskapen devices.registeredUsers i Azure Active Directory. |
| microsoft.directory/groups/appRoleAssignments/update microsoft.directory/groups/appRoleAssignments/update microsoft.directory/groups/appRoleAssignments/update microsoft. | Uppdatera egenskapen groups.appRoleAssignments i Azure Active Directory. |
| microsoft.directory/groups/basic/update | Uppdatera grundläggande egenskaper för grupper i Azure Active Directory. |
| microsoft.directory/grupper/skapa | Skapa grupper i Azure Active Directory. |
| microsoft.directory/groups/createAsOwner | Skapa grupper i Azure Active Directory. Skaparen läggs till som den första ägaren och det skapade objektet räknas mot skaparens 250 skapade objektkvot. |
| microsoft.directory/groups/delete | Ta bort grupper i Azure Active Directory. |
| microsoft.directory/groups/hiddenMembers/read microsoft.directory/groups/hiddenMembers/read microsoft.directory/groups/hiddenMembers/read microsoft. | Läs egenskapen groups.hiddenMembers i Azure Active Directory. |
| microsoft.directory/groups/members/update | Egenskapen Update groups.members i Azure Active Directory. |
| microsoft.directory/groups/owners/update | Egenskapen Update groups.owners i Azure Active Directory. |
| microsoft.directory/groups/restore | Återställ grupper i Azure Active Directory. |
| microsoft.directory/groups/settings/update | Egenskapen Update groups.settings i Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update microsoft.directory/users/appRoleAssignments/update microsoft.directory/users/appRoleAssignments/update microsoft. | Uppdatera egenskapen users.appRoleAssignments i Azure Active Directory. |
| microsoft.directory/users/basic/update | Uppdatera grundläggande egenskaper för användare i Azure Active Directory. |
| microsoft.directory/users/manager/update | Uppdatera egenskapen users.manager i Azure Active Directory. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbiljetter. |
| microsoft.intune/allEntities/allTasks | Hantera alla aspekter av Intune. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbiljetter. |
| microsoft.office365.webPortal/alla enheter/grundläggande/läs | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |

### <a name="kaizala-administrator-permissions"></a>Kaizala Administratörsbehörigheter

Kan hantera inställningar för Microsoft Kaizala.

> [!NOTE]
> Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollbeskrivning ovan.
>
>

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.office365.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbiljetter. |
| microsoft.office365.webPortal/alla enheter/grundläggande/läs | Läs administrationscentret för Office 365. |

### <a name="license-administrator-permissions"></a>Behörigheter för licensadministratör

Kan hantera produktlicenser för användare och grupper.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/users/assignLicense microsoft.directory/users/assignLicense microsoft.directory/users/assignLicense microsoft. | Hantera licenser för användare i Azure Active Directory. |
| microsoft.directory/users/usageLocation/update | Uppdatera egenskapen users.usageLocation i Azure Active Directory. |
| microsoft.azure.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Azure Service Health. |
| microsoft.office365.webPortal/alla enheter/grundläggande/läs | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Office 365 Service Health. |

### <a name="lync-service-administrator-permissions"></a>Behörigheter för Lync-tjänstadministratör

Kan hantera alla aspekter av Skype för företag-produkten.

> [!NOTE]
> Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollbeskrivning ovan.
>
>

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbiljetter. |
| microsoft.office365.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.skypeForBusiness/alla enheter/allaTasks | Hantera alla aspekter av Skype för företag – Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbiljetter. |
| microsoft.office365.usageReports/alla entiteter/läsa    | Läs användningsrapporter för Office 365. |
| microsoft.office365.webPortal/alla enheter/grundläggande/läs | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |


### <a name="message-center-privacy-reader-permissions"></a>Sekretessläsningsbehörigheter för Message Center

Kan läsa Inlägg i Message Center, datasekretessmeddelanden, grupper, domäner och prenumerationer.

> [!NOTE]
> Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollbeskrivning ovan.
>
>

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.office365.webPortal/alla enheter/grundläggande/läs | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.messageCenter/messages/read | Läs meddelanden i microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Läs securityMessages i microsoft.office365.messageCenter. |

### <a name="message-center-reader-permissions"></a>Behörigheter för Message Center Reader
Kan bara läsa meddelanden och uppdateringar för organisationen i Office 365 Message Center. 

> [!NOTE]
> Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollbeskrivning ovan.
>
>

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.office365.webPortal/alla enheter/grundläggande/läs | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.messageCenter/messages/read | Läs meddelanden i microsoft.office365.messageCenter. |

### <a name="office-apps-administrator-permissions"></a>Administratörsbehörigheter för Office Apps
Kan hantera Office-apps molntjänster, inklusive hantering av principer och inställningar, och hantera möjligheten att välja, avmarkera och publicera "nyheter" funktionsinnehåll på slutanvändarens enheter.

> [!NOTE]
> Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollbeskrivning ovan.
>
>

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbiljetter. |
| microsoft.office365.messageCenter/messages/read | Läs meddelanden i microsoft.office365.messageCenter. |
| microsoft.office365.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbiljetter. |
| microsoft.office365.userCommunication/allEntities/allTasks | Läs och uppdatera Synligheten för nya meddelanden. |
| microsoft.office365.webPortal/alla enheter/grundläggande/läs | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |

### <a name="partner-tier1-support-permissions"></a>Supportbehörigheter för partnernivå1

Använd inte - inte avsedd för allmänt bruk.

> [!NOTE]
> Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollbeskrivning ovan.
>
>

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/contacts/basic/update | Uppdatera grundläggande egenskaper för kontakter i Azure Active Directory. |
| microsoft.directory/kontakter/skapa | Skapa kontakter i Azure Active Directory. |
| microsoft.directory/kontakter/ta bort | Ta bort kontakter i Azure Active Directory. |
| microsoft.directory/grupper/skapa | Skapa grupper i Azure Active Directory. |
| microsoft.directory/groups/createAsOwner | Skapa grupper i Azure Active Directory. Skaparen läggs till som den första ägaren och det skapade objektet räknas mot skaparens 250 skapade objektkvot. |
| microsoft.directory/groups/members/update | Egenskapen Update groups.members i Azure Active Directory. |
| microsoft.directory/groups/owners/update | Egenskapen Update groups.owners i Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update microsoft.directory/users/appRoleAssignments/update microsoft.directory/users/appRoleAssignments/update microsoft. | Uppdatera egenskapen users.appRoleAssignments i Azure Active Directory. |
| microsoft.directory/users/assignLicense microsoft.directory/users/assignLicense microsoft.directory/users/assignLicense microsoft. | Hantera licenser för användare i Azure Active Directory. |
| microsoft.directory/users/basic/update | Uppdatera grundläggande egenskaper för användare i Azure Active Directory. |
| microsoft.directory/users/delete | Ta bort användare i Azure Active Directory. |
| microsoft.directory/users/invalidateAllRefreshTokens | Ogiltigförklara alla användaruppdateringstoken i Azure Active Directory. |
| microsoft.directory/users/manager/update | Uppdatera egenskapen users.manager i Azure Active Directory. |
| microsoft.directory/users/password/update | Uppdatera lösenord för alla användare i Azure Active Directory. Mer information finns i onlinedokumentationen. |
| microsoft.directory/users/restore | Återställ borttagna användare i Azure Active Directory. |
| microsoft.directory/users/userPrincipalName/update | Uppdatera egenskapen users.userPrincipalName i Azure Active Directory. |
| microsoft.azure.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbiljetter. |
| microsoft.office365.webPortal/alla enheter/grundläggande/läs | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbiljetter. |

### <a name="partner-tier2-support-permissions"></a>Behörigheter för partnernivå2-support

Använd inte - inte avsedd för allmänt bruk.

> [!NOTE]
> Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollbeskrivning ovan.
>
>

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/contacts/basic/update | Uppdatera grundläggande egenskaper för kontakter i Azure Active Directory. |
| microsoft.directory/kontakter/skapa | Skapa kontakter i Azure Active Directory. |
| microsoft.directory/kontakter/ta bort | Ta bort kontakter i Azure Active Directory. |
| microsoft.directory/domains/allTasks | Skapa och ta bort domäner och läs och uppdatera standardegenskaper i Azure Active Directory. |
| microsoft.directory/grupper/skapa | Skapa grupper i Azure Active Directory. |
| microsoft.directory/groups/delete | Ta bort grupper i Azure Active Directory. |
| microsoft.directory/groups/members/update | Egenskapen Update groups.members i Azure Active Directory. |
| microsoft.directory/groups/restore | Återställ grupper i Azure Active Directory. |
| microsoft.directory/organization/basic/update | Uppdatera grundläggande egenskaper för organisation i Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update microsoft.directory/users/appRoleAssignments/update microsoft.directory/users/appRoleAssignments/update microsoft. | Uppdatera egenskapen users.appRoleAssignments i Azure Active Directory. |
| microsoft.directory/users/assignLicense microsoft.directory/users/assignLicense microsoft.directory/users/assignLicense microsoft. | Hantera licenser för användare i Azure Active Directory. |
| microsoft.directory/users/basic/update | Uppdatera grundläggande egenskaper för användare i Azure Active Directory. |
| microsoft.directory/users/delete | Ta bort användare i Azure Active Directory. |
| microsoft.directory/users/invalidateAllRefreshTokens | Ogiltigförklara alla användaruppdateringstoken i Azure Active Directory. |
| microsoft.directory/users/manager/update | Uppdatera egenskapen users.manager i Azure Active Directory. |
| microsoft.directory/users/password/update | Uppdatera lösenord för alla användare i Azure Active Directory. Mer information finns i onlinedokumentationen. |
| microsoft.directory/users/restore | Återställ borttagna användare i Azure Active Directory. |
| microsoft.directory/users/userPrincipalName/update | Uppdatera egenskapen users.userPrincipalName i Azure Active Directory. |
| microsoft.azure.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbiljetter. |
| microsoft.office365.webPortal/alla enheter/grundläggande/läs | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbiljetter. |

### <a name="password-administrator-permissions"></a>Behörigheter för lösenordsadministratör

Kan återställa lösenord för icke-administratörer och lösenordsadministratörer.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/users/password/update | Uppdatera lösenord för alla användare i Azure Active Directory. Mer information finns i onlinedokumentationen. |
| microsoft.office365.webPortal/alla enheter/grundläggande/läs | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |

### <a name="power-bi-service-administrator-permissions"></a>Behörigheter för Power BI-tjänstadministratör

Kan hantera alla aspekter av Power BI-produkten.

> [!NOTE]
> Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollbeskrivning ovan.
>
>
| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbiljetter. |
| microsoft.powerApps.powerBI/alla enheter/allaTasker | Hantera alla aspekter av Power BI. |
| microsoft.office365.webPortal/alla enheter/grundläggande/läs | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbiljetter. |


### <a name="power-platform-administrator-permissions"></a>Behörigheter för Power Platform-administratör

Kan skapa och hantera alla aspekter av Microsoft Dynamics 365, PowerApps och Microsoft Flow. 

> [!NOTE]
> Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollbeskrivning ovan.
>
>
| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbiljetter. |
| microsoft.dynamics365/allEntities/allTasks | Hantera alla aspekter av Dynamics 365. |
| microsoft.flow/allEntities/allTasks microsoft.flow/allEntities/allTasks microsoft.flow/allEntities/allTasks microsoft. | Hantera alla aspekter av Microsoft Flow. |
| microsoft.powerApps/alla enheter/allaTasks | Hantera alla aspekter av PowerApps. |
| microsoft.office365.webPortal/alla enheter/grundläggande/läs | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbiljetter. |

### <a name="privileged-authentication-administrator-permissions"></a>Behörigheter för privilegierad autentiseringsadministratör

Får visa, ange och återställa information om autentiseringsmetod för alla användare (admin eller icke-admin).

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/users/invalidateAllRefreshTokens | Ogiltigförklara alla användaruppdateringstoken i Azure Active Directory. |
| microsoft.directory/users/strongAuthentication/update microsoft.directory/users/strongAuthentication/update microsoft.directory/users/strongAuthentication/update microsoft. | Uppdatera starka autentiseringsegenskaper som MFA-autentiseringsuppgifter. |
| microsoft.azure.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbiljetter. |
| microsoft.office365.webPortal/alla enheter/grundläggande/läs | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbiljetter. |
| microsoft.directory/users/password/update | Uppdatera lösenord för alla användare i Office 365-organisationen. Mer information finns i onlinedokumentationen. |

### <a name="privileged-role-administrator-permissions"></a>Behörigheter för privilegierad rolladministratör

Kan hantera rolltilldelningar i Azure AD och alla aspekter av privilegierad identitetshantering.

> [!NOTE]
> Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollbeskrivning ovan.
>
>

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Skapa och ta bort alla resurser och läs och uppdatera standardegenskaper i microsoft.aad.privilegedIdentityManagement. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/allTasks | Läs och konfigurera servicePrincipals.appRoleAssignedTo-egenskap i Azure Active Directory. |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/allTasks microsoft.directory/servicePrincipals/oAuth2PermissionGrants/allTasks microsoft.directory/servicePrincipals/oAuth2PermissionGrants/allTasks microsoft. | Läs och konfigurera egenskapen servicePrincipals.oAuth2PermissionGrants i Azure Active Directory. |
| microsoft.directory/administrativeUnits/allProperties/allTasks | Skapa och hantera administrativa enheter (inklusive medlemmar) |
| microsoft.directory/roleAssignments/allProperties/allTasks | Skapa och hantera rolltilldelningar. |
| microsoft.directory/roleDefinitions/allProperties/allTasks | Skapa och hantera rolldefinitioner. |

### <a name="reports-reader-permissions"></a>Behörigheter för rapporterläsare

Kan läsa inloggnings- och granskningsrapporter.

> [!NOTE]
> Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollbeskrivning ovan.
>
>

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Läs alla egenskaper (inklusive privilegierade egenskaper) på auditLogs i Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read microsoft.directory/signInReports/allProperties/read microsoft.directory/signInReports/allProperties/read microsoft. | Läs alla egenskaper (inklusive privilegierade egenskaper) vid inloggningar i Azure Active Directory. |
| microsoft.azure.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Azure Service Health. |
| microsoft.office365.usageReports/alla entiteter/läsa | Läs användningsrapporter för Office 365. |

### <a name="search-administrator-permissions"></a>Behörigheter för sökadministratör

Kan skapa och hantera alla aspekter av Microsoft Search-inställningar.

> [!NOTE]
> Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollbeskrivning ovan.
>
>

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Läs meddelanden i microsoft.office365.messageCenter. |
| microsoft.office365.search/allEntities/allProperties/allTasks | Skapa och ta bort alla resurser och läs och uppdatera alla egenskaper i microsoft.office365.search. |
| microsoft.office365.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbiljetter. |
| microsoft.office365.webPortal/alla enheter/grundläggande/läs | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |

### <a name="search-editor-permissions"></a>Behörigheter för sökredigerare

Kan skapa och hantera redaktionellt innehåll som bokmärken, Q och As, platser, planlösning.

> [!NOTE]
> Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollbeskrivning ovan.
>
>

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Läs meddelanden i microsoft.office365.messageCenter. |
| microsoft.office365.search/content/allProperties/allTasks | Skapa och ta bort innehåll och läs och uppdatera alla egenskaper i microsoft.office365.search. |

### <a name="security-administrator-permissions"></a>Behörigheter för säkerhetsadministratör

Kan läsa säkerhetsinformation och rapporter och hantera konfiguration i Azure AD och Office 365.

> [!NOTE]
> Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollbeskrivning ovan.
>
>

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/applications/policies/update | Uppdatera egenskapen applications.policies i Azure Active Directory. |
| microsoft.directory/auditLogs/allProperties/read | Läs alla egenskaper (inklusive privilegierade egenskaper) på auditLogs i Azure Active Directory. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read microsoft.directory/devices/bitLockerRecoveryKeys/read microsoft.directory/devices/bitLockerRecoveryKeys/read microsoft. | Läs egenskapen devices.bitLockerRecoveryKeys i Azure Active Directory. |
| microsoft.directory/policies/basic/update | Uppdatera grundläggande egenskaper för principer i Azure Active Directory. |
| microsoft.directory/policies/create | Skapa principer i Azure Active Directory. |
| microsoft.directory/policies/delete | Ta bort principer i Azure Active Directory. |
| microsoft.directory/policies/owners/update | Egenskapen Update policies.owners i Azure Active Directory. |
| microsoft.directory/policies/tenantDefault/update | Uppdatera egenskapen policies.tenantDefault i Azure Active Directory. |
| microsoft.directory/servicePrincipals/principer/uppdatering | Uppdatera egenskapen servicePrincipals.policies i Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read microsoft.directory/signInReports/allProperties/read microsoft.directory/signInReports/allProperties/read microsoft. | Läs alla egenskaper (inklusive privilegierade egenskaper) vid inloggningar i Azure Active Directory. |
| microsoft.aad.identityProtection/allentities/läs | Läs alla resurser i microsoft.aad.identityProtection. |
| microsoft.aad.identitySkydd/alla enheter/uppdatering | Uppdatera alla resurser i microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/alla enheter/läsa | Läs alla resurser i microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Azure Service Health. |
| microsoft.office365.webPortal/alla enheter/grundläggande/läs | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.protectionCenter/alla enheter/läsa | Läs alla aspekter av Office 365 Protection Center. |
| microsoft.office365.protectionCenter/alla enheter/uppdatering | Uppdatera alla resurser i microsoft.office365.protectionCenter. |
| microsoft.office365.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Office 365 Service Health. |

### <a name="security-operator-permissions"></a>Behörigheter för säkerhetsoperator

Skapar och hanterar säkerhetshändelser.

> [!NOTE]
> Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollbeskrivning ovan.
>
>

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Läs och konfigurera Microsoft Cloud App Security. |
| microsoft.aad.identityProtection/allentities/läs | Läs alla resurser i microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/alla enheter/läsa | Läs alla resurser i microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Läs och konfigurera Azure AD Advanced Threat Protection. |
| microsoft.intune/allEntities/allTasks | Hantera alla aspekter av Intune. |
| microsoft.office365.securityComplianceCenter/alla enheter/allaTasks | Läs och konfigurera Security & Compliance Center. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Läs och konfigurera Windows Defender Advanced Threat Protection. |

### <a name="security-reader-permissions"></a>Behörigheter för säkerhetsläsare

Kan läsa säkerhetsinformation och rapporter i Azure AD och Office 365.

> [!NOTE]
> Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollbeskrivning ovan.
>
>

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Läs alla egenskaper (inklusive privilegierade egenskaper) på auditLogs i Azure Active Directory. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read microsoft.directory/devices/bitLockerRecoveryKeys/read microsoft.directory/devices/bitLockerRecoveryKeys/read microsoft. | Läs egenskapen devices.bitLockerRecoveryKeys i Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/basic/read | Läs principer.conditionalAccess-egenskapen i Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read microsoft.directory/signInReports/allProperties/read microsoft.directory/signInReports/allProperties/read microsoft. | Läs alla egenskaper (inklusive privilegierade egenskaper) vid inloggningar i Azure Active Directory. |
| microsoft.aad.identityProtection/allentities/läs | Läs alla resurser i microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/alla enheter/läsa | Läs alla resurser i microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Azure Service Health. |
| microsoft.office365.webPortal/alla enheter/grundläggande/läs | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.protectionCenter/alla enheter/läsa | Läs alla aspekter av Office 365 Protection Center. |
| microsoft.office365.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Office 365 Service Health. |

### <a name="service-support-administrator-permissions"></a>Administratörsbehörigheter för tjänstsupport

Kan läsa hälsoinformation och hantera supportärenden.

> [!NOTE]
> Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollbeskrivning ovan.
>
>

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbiljetter. |
| microsoft.office365.webPortal/alla enheter/grundläggande/läs | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbiljetter. |

### <a name="sharepoint-service-administrator-permissions"></a>Administratörsbehörigheter för SharePoint-tjänst

Kan hantera alla aspekter av SharePoint-tjänsten.

> [!NOTE]
> Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollbeskrivning ovan.
>
>

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbiljetter. |
| microsoft.directory/groups/unified/appRoleAssignments/update microsoft.directory/groups/unified/appRoleAssignments/update microsoft.directory/groups/unified/appRoleAssignments/update microsoft. | Uppdatera groups.unified-egenskap i Azure Active Directory. |
| microsoft.directory/groups/unified/basic/update microsoft.directory/groups/unified/basic/update microsoft.directory/groups/unified/basic/update microsoft. | Uppdatera grundläggande egenskaper för Office 365-grupper. |
| microsoft.directory/groups/unified/create | Skapa Office 365-grupper. |
| microsoft.directory/groups/unified/delete microsoft.directory/groups/unified/delete microsoft.directory/groups/unified/delete microsoft. | Ta bort Office 365-grupper. |
| microsoft.directory/groups/unified/members/update | Uppdatera medlemskap i Office 365-grupper. |
| microsoft.directory/groups/unified/owners/update | Uppdatera ägarskapet för Office 365-grupper. |
| microsoft.office365.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.sharepoint/alla enheter/allaTasker | Skapa och ta bort alla resurser och läs och uppdatera standardegenskaper i microsoft.office365.sharepoint. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbiljetter. |
| microsoft.office365.usageReports/alla entiteter/läsa    | Läs användningsrapporter för Office 365. |
| microsoft.office365.webPortal/alla enheter/grundläggande/läs | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |

### <a name="teams-communications-administrator-permissions"></a>Behörigheter för teamkommunikationsadministratör

Kan hantera samtals- och mötesfunktioner i Microsoft Teams-tjänsten.

> [!NOTE]
> Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollbeskrivning ovan.
>
>

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbiljetter. |
| microsoft.office365.webPortal/alla enheter/grundläggande/läs | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbiljetter. |
| microsoft.office365.usageReports/alla entiteter/läsa | Läs användningsrapporter för Office 365. |

### <a name="teams-communications-support-engineer-permissions"></a>Behörigheter för teamkommunikationssupporttekniker

Kan felsöka kommunikationsproblem inom Teams med hjälp av avancerade verktyg.

> [!NOTE]
> Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollbeskrivning ovan.
>
>

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Azure Service Health. |
| microsoft.office365.webPortal/alla enheter/grundläggande/läs | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Office 365 Service Health. |

### <a name="teams-communications-support-specialist-permissions"></a>Behörigheter för teamkommunikationssupport

Kan felsöka kommunikationsproblem inom Teams med hjälp av grundläggande verktyg.

> [!NOTE]
> Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollbeskrivning ovan.
>
>

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Azure Service Health. |
| microsoft.office365.webPortal/alla enheter/grundläggande/läs | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Office 365 Service Health. |

### <a name="teams-service-administrator-permissions"></a>Behörigheter för Team-tjänst

Kan hantera Tjänsten Microsoft Teams.

> [!NOTE]
> Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollbeskrivning ovan.
>
>

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/groups/hiddenMembers/read microsoft.directory/groups/hiddenMembers/read microsoft.directory/groups/hiddenMembers/read microsoft. | Läs egenskapen groups.hiddenMembers i Azure Active Directory. |
| microsoft.directory/groups/unified/appRoleAssignments/update microsoft.directory/groups/unified/appRoleAssignments/update microsoft.directory/groups/unified/appRoleAssignments/update microsoft. | Uppdatera groups.unified-egenskap i Azure Active Directory. |
| microsoft.directory/groups/unified/basic/update microsoft.directory/groups/unified/basic/update microsoft.directory/groups/unified/basic/update microsoft. | Uppdatera grundläggande egenskaper för Office 365-grupper. |
| microsoft.directory/groups/unified/create | Skapa Office 365-grupper. |
| microsoft.directory/groups/unified/delete microsoft.directory/groups/unified/delete microsoft.directory/groups/unified/delete microsoft. | Ta bort Office 365-grupper. |
| microsoft.directory/groups/unified/members/update | Uppdatera medlemskap i Office 365-grupper. |
| microsoft.directory/groups/unified/owners/update | Uppdatera ägarskapet för Office 365-grupper. |
| microsoft.azure.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbiljetter. |
| microsoft.office365.webPortal/alla enheter/grundläggande/läs | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbiljetter. |
| microsoft.office365.usageReports/alla entiteter/läsa | Läs användningsrapporter för Office 365. |

### <a name="user-administrator-permissions"></a>Behörigheter för användaradministratör
Kan hantera alla aspekter av användare och grupper, inklusive återställning av lösenord för begränsade administratörer.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/appRoleAssignments/create | Skapa appRoleAstilldelningar i Azure Active Directory. |
| microsoft.directory/appRoleAssignments/delete microsoft.directory/appRoleAssignments/delete microsoft.directory/appRoleAssignments/delete microsoft. | Ta bort appRoleAstilldelningar i Azure Active Directory. |
| microsoft.directory/appRoleAssignments/update | Uppdatera appRoleAstilldelningar i Azure Active Directory. |
| microsoft.directory/contacts/basic/update | Uppdatera grundläggande egenskaper för kontakter i Azure Active Directory. |
| microsoft.directory/kontakter/skapa | Skapa kontakter i Azure Active Directory. |
| microsoft.directory/kontakter/ta bort | Ta bort kontakter i Azure Active Directory. |
| microsoft.directory/groups/appRoleAssignments/update microsoft.directory/groups/appRoleAssignments/update microsoft.directory/groups/appRoleAssignments/update microsoft. | Uppdatera egenskapen groups.appRoleAssignments i Azure Active Directory. |
| microsoft.directory/groups/basic/update | Uppdatera grundläggande egenskaper för grupper i Azure Active Directory. |
| microsoft.directory/grupper/skapa | Skapa grupper i Azure Active Directory. |
| microsoft.directory/groups/createAsOwner | Skapa grupper i Azure Active Directory. Skaparen läggs till som den första ägaren och det skapade objektet räknas mot skaparens 250 skapade objektkvot. |
| microsoft.directory/groups/delete | Ta bort grupper i Azure Active Directory. |
| microsoft.directory/groups/hiddenMembers/read microsoft.directory/groups/hiddenMembers/read microsoft.directory/groups/hiddenMembers/read microsoft. | Läs egenskapen groups.hiddenMembers i Azure Active Directory. |
| microsoft.directory/groups/members/update | Egenskapen Update groups.members i Azure Active Directory. |
| microsoft.directory/groups/owners/update | Egenskapen Update groups.owners i Azure Active Directory. |
| microsoft.directory/groups/restore | Återställ grupper i Azure Active Directory. |
| microsoft.directory/groups/settings/update | Egenskapen Update groups.settings i Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update microsoft.directory/users/appRoleAssignments/update microsoft.directory/users/appRoleAssignments/update microsoft. | Uppdatera egenskapen users.appRoleAssignments i Azure Active Directory. |
| microsoft.directory/users/assignLicense microsoft.directory/users/assignLicense microsoft.directory/users/assignLicense microsoft. | Hantera licenser för användare i Azure Active Directory. |
| microsoft.directory/users/basic/update | Uppdatera grundläggande egenskaper för användare i Azure Active Directory. |
| microsoft.directory/users/create | Skapa användare i Azure Active Directory. |
| microsoft.directory/users/delete | Ta bort användare i Azure Active Directory. |
| microsoft.directory/users/invalidateAllRefreshTokens | Ogiltigförklara alla användaruppdateringstoken i Azure Active Directory. |
| microsoft.directory/users/manager/update | Uppdatera egenskapen users.manager i Azure Active Directory. |
| microsoft.directory/users/password/update | Uppdatera lösenord för alla användare i Azure Active Directory. Mer information finns i onlinedokumentationen. |
| microsoft.directory/users/restore | Återställ borttagna användare i Azure Active Directory. |
| microsoft.directory/users/userPrincipalName/update | Uppdatera egenskapen users.userPrincipalName i Azure Active Directory. |
| microsoft.azure.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportbiljetter. |
| microsoft.office365.webPortal/alla enheter/grundläggande/läs | Läs grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/alla enheter/allaTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbiljetter. |

## <a name="role-template-ids"></a>Rollmall-ID:er

Rollmall-ID:er används huvudsakligen av Microsoft Graph API- eller PowerShell-användare.

Diagram displayName | Visningsnamn för Azure Portal | katalogRoleTemplateId
----------------- | ------------------------- | -------------------------
Programadministratör | Programadministratör | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
Programutvecklare | Programutvecklare | CF1C38E5-3621-4004-A7CB-879624DCED7C
Administratör för autentisering | Administratör för autentisering | c4e39bd9-1100-46d3-8c65-fb160da0071f
Azure DevOps-administratör | Azure DevOps-administratör | e3973bdf-4987-49ae-837a-ba8e231c7286
Administratör för azure-informationsskydd | Administratör för Azure-informationsskydd | 7495fdc4-34c4-4d15-a289-98788ce399fd
B2C-administratör för användarflöde | B2C-administratör för användarflöde | 6e591065-9bad-43ed-90f3-e9424366d2f0
Administratör för användarflödesattribut för B2C | Administratör för användarflödesattribut för B2C | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
B2C IEF Keyset Administratör | B2C IEF Keyset Administratör | aaf43236-0c0d-4d5f-883a-6955382ac081
B2C IEF-principadministratör | B2C IEF-principadministratör | 3edaf663-341e-4475-9f94-5c398ef6c070
Faktureringsadministratör | Faktureringsadministratör | b0f54661-2d74-4c50-afa3-1ec803f12efe
Molnprogramadministratör | Administratör för molnprogram | 158c047a-c907-4556-b7ef-446551a6b5f7
Administratör för molnenheter | Administratör för molnenheter | 7698a772-787b-4ac8-901f-60d6b08affd2
Företagsadministratör | Global administratör | 62e90394-69f5-4237-9190-012177145e10
Efterlevnadsadministratör | Administratör för efterlevnad | 17315797-102d-40b4-93e0-432062caca18
Efterlevnadsdataadministratör | Administratör för efterlevnadsdata | e6d1a23a-da11-4be4-9570-befc86d067a7
Administratör för villkorsstyrd åtkomst | Administratör för villkorlig åtkomst | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
Administratör för CRM-tjänster | Dynamics 365-administratör | 44367163-eba1-44c3-98af-f5787879f96a
Godkännande av kundlåsboxåtkomst | Godkännande av kundlåsboxåtkomst | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
Administratör för skrivbordsanalys | Administratör för skrivbordsanalys | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
Enhetsadministratörer | Enhetsadministratörer | 9f06204d-73c1-4d4c-880a-6edb90606fd8
Enhetsanslutning | Enhetskoppling | 9c094953-4995-41c8-84c8-3ebb9b32c93f
Enhetshanterare | Enhetshanterare | 2b499bcd-da44-4968-8aec-78e1674fa64d
Enhetsanvändare | Enhetsanvändare | d405c6df-0af8-4e3b-95e4-4d06e542189e
Katalogläsare | Katalogläsare | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
Konton för katalogsynkronisering | Konton för katalogsynkronisering | d29b2b05-8046-44ba-8758-1e26182fcf32
Katalog författare | Katalogförfattare | 9360feb5-f418-4baa-8175-e2a00bac4301
Administratör för Exchange-tjänsten | Exchange-administratör | 29232cdf-9323-42fd-ade2-1d097af3e4de
Administratör för extern identitetsprovider | Administratör för extern identitetsprovider | be2f45a1-457d-42af-a067-6ec1fa63bc45
Global läsare | Global läsare | f2ef992c-3afb-46b9-b7cf-a126ee74c451
Gruppadministratör | Gruppadministratör | fdd7a751-b60b-444a-984c-02652fe8fa1c 
Gäst inbjudna | Gäst inbjudna | 95e79109-95c0-4d8e-aee3-d01accf2d47b
Administratör för helpdesk | Helpdesk-administratör | 729827e3-9c14-49f7-bb1b-9608f156bbb8
Administratör för Intune-tjänsten | Intune-administratör | 3a2c62db-5318-420d-8d74-23affee5d9d5
Kaizala Administratör | Kaizala administratör | 74ef975b-6605-40af-a5d2-b9539d836353
Licensadministratör | Licensadministratör | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Administratör för Lync-tjänsten | Skype for Business-administratör | 75941009-915a-4869-abe7-691bff18279e
Sekretessläsare för Message Center | Sekretessläsare för message center | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
Läsare av Meddelandecenter | Läsare av meddelandecenter | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
Office Apps-administratör | Office-programadministratör | 2b745bdf-0803-4d80-aa65-822c4493daac
Support för partnernivå1 | Support för partnernivå1 | 4ba39ca4-527c-499a-b93d-d9b492c50246
Support för partnernivå2 | Support för partnernivå2 | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
Lösenordsadministratör | Lösenordsadministratör | 966707d0-3269-4727-9be2-8c3a10f19b9d
Power BI-tjänstadministratör | Power BI-administratör | a9ea8996-122f-4c74-9520-8edcd192826c
Administratör för Power Platform | Administratör för Power Platform | 11648597-926c-4cf3-9c36-bcebb0ba8dcc
Privilegierad autentiseringsadministratör | Privilegierad autentiseringsadministratör | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
Administratör för privilegierad roll | Administratör för privilegierad roll | e8611ab8-c189-46e8-94e1-60213ab1f814
Rapporter Reader | Rapporter läsare | 4a5d8f65-41da-4de4-8968-e035b65339cf
Sök administratör | Sök administratör | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
Sökredigerare | Sökredigerare | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
Säkerhetsadministratör | Säkerhetsadministratör | 194ae4cb-b126-40b2-bd5b-6091b380977d
Säkerhetsoperatör | Säkerhetsoperatör | 5f222b1-57c3-48ba-8ad5-d4759f1fde6f
Säkerhetsläsare | Säkerhetsläsare | 5d6b6bb7-de71-4623-b4af-96380a352509
Administratör för servicesupport | Tjänstsupportadministratör | f023fd81-a637-4b56-95fd-791ac0226033
Administratör för SharePoint-tjänsten | SharePoint-administratör | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Team Kommunikationsadministratör | Team Kommunikationsadministratör | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Supporttekniker för team | Supporttekniker för team | f70938a0-fc10-4177-9e90-2178f8765737
Team Kommunikationssupport Specialist | Team Kommunikationssupport Specialist | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Administratör för teamtjänsten | Administratör för teamtjänsten | 69091246-20e8-4a56-aa4d-066075b2a7a8
Användare | Användare | a0b1b346-4d3e-4e8b-98f8-753987be4970
Administratör för användarkonto | Användaradministratör | fe930be7-5e62-47db-91af-98c3a49a38b1
Anslutning till arbetsplatsenheter | Anslutning till arbetsplatsenheter | c34f683f-4d5a-4403-affd-6615e00e3a7f

## <a name="deprecated-roles"></a>Inaktuella roller

Följande roller bör inte användas. De har tagits bort och kommer att tas bort från Azure AD i framtiden.

* AdHoc-licensadministratör
* Enhetsanslutning
* Enhetshanterare
* Enhetsanvändare
* E-postverifierade användare skapare
* Administratör för postlåda
* Anslutning till arbetsplatsenheter

## <a name="roles-not-shown-in-the-portal"></a>Roller som inte visas i portalen

Alla roller som returneras av PowerShell- eller MS Graph API visas inte i Azure-portalen. I följande tabell ordnas dessa skillnader.

API-namn | Namn på Azure-portal | Anteckningar
-------- | ------------------- | -------------
Företagsadministratör | Global administratör | [Namnet ändrats för bättre tydlighet](directory-assign-admin-roles.md#role-template-ids)
Administratör för CRM-tjänster | Dynamics 365-administratör | [Återspeglar nuvarande produktvarumärke](directory-assign-admin-roles.md#role-template-ids)
Enhetsanslutning | Inaktuell | [Dokumentation för inaktuella roller](directory-assign-admin-roles.md#deprecated-roles)
Enhetshanterare | Inaktuell | [Dokumentation för inaktuella roller](directory-assign-admin-roles.md#deprecated-roles)
Enhetsanvändare | Inaktuell | [Dokumentation för inaktuella roller](directory-assign-admin-roles.md#deprecated-roles)
Konton för katalogsynkronisering | Visas inte eftersom det inte bör användas | [Dokumentation för katalogsynkroniseringskonton](directory-assign-admin-roles.md#directory-synchronization-accounts)
Katalog författare | Visas inte eftersom det inte bör användas | [Dokumentation för katalogförfattare](directory-assign-admin-roles.md#directory-writers)
Gästanvändare | Visas inte eftersom det inte kan användas  | Ej tillämpligt
Administratör för Lync-tjänsten | Skype for Business-administratör | [Återspeglar nuvarande produktvarumärke](directory-assign-admin-roles.md#role-template-ids)
Support för partnernivå 1 | Visas inte eftersom det inte bör användas | [Supportdokumentation för partnernivå1](directory-assign-admin-roles.md#partner-tier1-support)
Support för partnernivå 2 | Visas inte eftersom det inte bör användas | [Dokumentation för support på partnernivå2](directory-assign-admin-roles.md#partner-tier2-support)
Skrivaradministratör | Pågående arbete | Pågående arbete
Skrivartekniker | Pågående arbete | Pågående arbete
Begränsad gästanvändare | Visas inte eftersom det inte kan användas | Ej tillämpligt
Användare | Visas inte eftersom det inte kan användas | Ej tillämpligt
Anslutning till arbetsplatsenheter | Inaktuell | [Dokumentation för inaktuella roller](directory-assign-admin-roles.md#deprecated-roles)

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du tilldelar en användare som administratör för en Azure-prenumeration finns i [Hantera åtkomst med Azure-roller (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)
* Mer information om hur resursåtkomst styrs i Microsoft Azure finns i [Förstå de olika rollerna](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Mer information om hur Azure Active Directory relaterar till din Azure-prenumeration finns i [Hur Azure-prenumerationer är associerade med Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
