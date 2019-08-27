---
title: Beskrivningar och behörigheter för administratörs roll – Azure Active Directory | Microsoft Docs
description: En administratörs roll kan lägga till användare, tilldela administrativa roller, återställa användar lösen ord, hantera användar licenser eller hantera domäner.
services: active-directory
author: curtand
manager: mtillman
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/23/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a228c0d349fd45f34923a64ef99dcfba50c4c548
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034994"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Administratörs roll behörigheter i Azure Active Directory

Med hjälp av Azure Active Directory (Azure AD) kan du ange begränsade administratörer för att hantera identitets uppgifter i mindre privilegierade roller. Administratörer kan tilldelas till sådana orsaker som att lägga till eller ändra användare, tilldela administrativa roller, återställa användar lösen ord, hantera användar licenser och hantera domän namn. Standard användar behörigheter kan bara ändras i användar inställningar i Azure AD.

## <a name="limit-the-use-of-global-administrator"></a>Begränsa användningen av global administratör

Användare som har tilldelats rollen som global administratör kan läsa och ändra alla administrativa inställningar i din Azure AD-organisation. Som standard tilldelas den person som registrerar sig för en Azure-prenumeration rollen som global administratör för Azure AD-organisationen. Endast globala administratörer och privilegierade roll administratörer kan delegera administratörs roller. För att minska risken för verksamheten rekommenderar vi att du tilldelar den här rollen till det minsta möjliga antalet personer i din organisation.

Som bästa praxis rekommenderar vi att du tilldelar rollen till färre än 5 personer i din organisation. Om du har fler än fem användare som har tilldelats rollen global administratör i din organisation kan du använda följande metoder för att minska användningen.

### <a name="find-the-role-you-need"></a>Hitta den roll du behöver

Om det är frustrerande att hitta den roll du behöver från en lista över många roller, kan Azure AD förse dig med kortare listor baserat på roll kategorier. Kolla in vårt nya **typ** filter för [Azure AD-roller och-administratörer](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) för att visa att du bara visar rollerna i den valda typen.

### <a name="a-role-exists-now-that-didnt-exist-when-you-assigned-the-global-administrator-role"></a>En roll finns nu som inte fanns när du tilldelade rollen som global administratör

Det är möjligt att en roll eller roller har lagts till i Azure AD och ger mer detaljerade behörigheter som inte var ett alternativ när du utökade vissa användare till global administratör. Med tiden, tar vi upp ytterligare roller som utför uppgifter som bara den globala administratörs rollen kan utföra före. Du kan se dessa återspeglade i följande [tillgängliga roller](#available-roles).

## <a name="assign-or-remove-administrator-roles"></a>Tilldela eller ta bort administratörs roller

Information om hur du tilldelar administrativa roller till en användare i Azure Active Directory finns i [Visa och tilldela administratörs roller i Azure Active Directory](directory-manage-roles-portal.md).

## <a name="available-roles"></a>Tillgängliga roller

Följande administratörs roller är tillgängliga:

* **[Program administratör](#application-administrator)** : Användare med den här rollen kan skapa och hantera alla aspekter av företags program, program registreringar och programproxy-inställningar. Den här rollen ger också möjlighet att godkänna delegerade behörigheter och program behörigheter exklusive Microsoft Graph och Azure AD Graph. Användare som har tilldelats den här rollen läggs inte till som ägare när de skapar nya program registreringar eller företags program.

  <b>Viktigt</b>: Den här rollen ger möjlighet att hantera programautentiseringsuppgifter. Användare som har tilldelats den här rollen kan lägga till autentiseringsuppgifter i ett program och använda dessa autentiseringsuppgifter för att personifiera programmets identitet. Om programmets identitet har beviljats åtkomst till Azure Active Directory, till exempel möjligheten att skapa eller uppdatera användare eller andra objekt, kan en användare som tilldelats rollen utföra dessa åtgärder samtidigt som programmet personifieras. Den här möjligheten att personifiera programmets identitet kan vara en höjning av privilegier över vad användaren kan göra via sina roll tilldelningar i Azure AD. Det är viktigt att förstå att tilldelning av en användare till rollen program administratör ger dem möjlighet att personifiera ett programs identitet.

* **[](#application-developer)** Programutvecklare: Användare med den här rollen kan skapa program registreringar när inställningen "användare kan registrera program" är inställd på Nej. Den här rollen beviljar också behörighet för ett eget ställe när inställningen "användare kan godkänna att appar som har åtkomst till företags information för deras räkning" är inställd på Nej. Användare som har tilldelats rollen läggs till som ägare när de skapar nya program registreringar eller företags program.

* **[Administratör för autentisering](#authentication-administrator)** : Användare med den här rollen kan ange eller återställa autentiseringsuppgifter för icke-lösenord och kan uppdatera lösen ord för alla användare. Autentiserings administratörer kan kräva att användarna registrerar sig igen mot befintliga autentiseringsuppgifter för icke-lösenord (till exempel MFA eller FIDO) och återkalla åtkomst till **MFA på enheten**, vilket efterfrågar MFA vid nästa inloggning av användare som inte är administratörer eller tilldelas följande roller endast:
  * Autentiseringsadministratör
  * Katalogläsare
  * Gästinbjudare
  * Meddelandecenterläsare
  * Rapportläsare

  Rollen som administratör för autentisering är för närvarande en offentlig för hands version. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

  <b>Viktigt</b>: Användare med den här rollen kan ändra autentiseringsuppgifter för personer som kan ha åtkomst till känslig eller privat information eller kritisk konfiguration i och utanför Azure Active Directory. Att ändra autentiseringsuppgifterna för en användare kan innebära att du förutsätter att användarens identitet och behörigheter. Exempel:

  * Program registrering och företags program ägare, som kan hantera autentiseringsuppgifter för appar som de äger. De här apparna kan ha privilegierade behörigheter i Azure AD och inte beviljas till autentiserings administratörer. Genom den här sökvägen kan en administratörs administratör kunna ta hänsyn till identiteten för en program ägare och sedan ytterligare anta identiteten för ett privilegierat program genom att uppdatera autentiseringsuppgifterna för programmet.
  * Azure-Prenumerationens ägare, som kan ha åtkomst till känslig eller privat information eller kritisk konfiguration i Azure.
  * Säkerhets grupp och Office 365-grupp ägare, som kan hantera grupp medlemskap. Dessa grupper kan ge åtkomst till känslig eller privat information eller kritisk konfiguration i Azure AD och någon annan stans.
  * Administratörer i andra tjänster utanför Azure AD, till exempel Exchange Online, Office Security och Compliance Center och personal system.
  * Icke-administratörer som chefer, juridiska konsulter och personal anställda som kan ha till gång till känslig eller privat information.

* **[Azure information Protection administratör](#azure-information-protection-administrator)** : Användare med den här rollen har alla behörigheter i Azure Information Protections tjänsten. Med den här rollen kan du konfigurera etiketter för Azure Information Protection principen, hantera skydds mallar och aktivera skydd. Den här rollen beviljar inte några behörigheter i identitets skydds centret, Privileged Identity Management, övervaka Office 365 Service Health eller Office 365 Säkerhets-och efterlevnadscenter.

* **[B2C användar flödes administratör](#b2c-user-flow-administrator)** : Användare med den här rollen kan skapa och hantera B2C Användarflöden (aka "inbyggda" principer) i Azure Portal. Genom att skapa eller redigera användar flöden kan dessa användare ändra HTML/CSS/JavaScript-innehåll i användar upplevelsen, ändra MFA-krav per användar flöde, ändra anspråk i token och justera sessionsinställningar för alla principer i klienten. Den här rollen omfattar å andra sidan inte möjligheten att granska användar data eller göra ändringar i de attribut som ingår i klient organisations schemat. Ändringar av aka-principer (Identity Experience Framework) är också utanför den här rollens omfång.

* **[B2C-administratör för användar flöde](#b2c-user-flow-attribute-administrator)** : Användare med den här rollen lägger till eller tar bort anpassade attribut som är tillgängliga för alla användar flöden i klienten. Det innebär att användare med den här rollen kan ändra eller lägga till nya element i slutanvändarens schema och påverka beteendet för alla användar flöden och indirekt leda till ändringar i vilka data som kan tillfrågas slutanvändare och slutligen skickas som anspråk till program. Den här rollen kan inte redigera användar flöden.

* **[B2C IEF Keys-administratör](#b2c-ief-keyset-administrator)** :    Användaren kan skapa och hantera princip nycklar och hemligheter för token-kryptering, token-signaturer och anspråks kryptering/dekryptering. Genom att lägga till nya nycklar i befintliga nyckel behållare kan denna begränsade administratör förnya hemligheter vid behov utan att påverka befintliga program. Den här användaren kan se det fullständiga innehållet i dessa hemligheter och deras förfallo datum även efter att de har skapats.
    
  <b>Viktigt:</b> det här är en känslig roll. Administratörs rollen för nyckel uppsättningen bör granskas noga och tilldelas med försiktighet under för produktion och produktion.

* **[B2C IEF-princip administratör](#b2c-ief-policy-administrator)** : Användare med den här rollen kan skapa, läsa, uppdatera och ta bort alla anpassade principer i Azure AD B2C och har därför fullständig kontroll över identitets Miljös ramverket i den relevanta Azure AD B2C klienten. Genom att redigera principer kan den här användaren upprätta direkt Federation med externa identitets leverantörer, ändra katalog schema, ändra allt innehåll som riktas mot användare (HTML, CSS, Java Script), ändra kraven för att slutföra en autentisering, skapa nya användare, skicka användar data till externa system, inklusive fullständiga migreringar, och redigera all användar information, inklusive känsliga fält som lösen ord och telefonnummer. Den här rollen kan däremot inte ändra krypterings nycklarna eller redigera hemligheterna som används för federationen i klienten.

  <b>Viktigt:</b> IEF princip administratör för B2 är en mycket känslig roll som bör tilldelas till en mycket begränsad grund för klienter i produktion. Aktiviteter av dessa användare bör granskas noga, särskilt för innehavare i produktion.

* **[Fakturerings administratör](#billing-administrator)** : Gör inköp, hanterar prenumerationer, hanterar support ärenden och övervakar tjänstens hälsa.

* **[Moln program administratör](#cloud-application-administrator)** : Användare i den här rollen har samma behörigheter som program administratörs rollen, förutom möjligheten att hantera programproxyn. Den här rollen ger möjlighet att skapa och hantera alla aspekter av företags program och program registreringar. Den här rollen ger också möjlighet att godkänna delegerade behörigheter och program behörigheter exklusive Microsoft Graph och Azure AD Graph. Användare som har tilldelats den här rollen läggs inte till som ägare när de skapar nya program registreringar eller företags program.

  <b>Viktigt</b>: Den här rollen ger möjlighet att hantera programautentiseringsuppgifter. Användare som har tilldelats den här rollen kan lägga till autentiseringsuppgifter i ett program och använda dessa autentiseringsuppgifter för att personifiera programmets identitet. Om programmets identitet har beviljats åtkomst till Azure Active Directory, till exempel möjligheten att skapa eller uppdatera användare eller andra objekt, kan en användare som tilldelats rollen utföra dessa åtgärder samtidigt som programmet personifieras. Den här möjligheten att personifiera programmets identitet kan vara en höjning av privilegier över vad användaren kan göra via sina roll tilldelningar i Azure AD. Det är viktigt att förstå att tilldela en användare till rollen som moln program administratör och ger dem möjlighet att personifiera ett programs identitet.

* **[Moln enhets administratör](#cloud-device-administrator)** : Användare med den här rollen kan aktivera, inaktivera och ta bort enheter i Azure AD och läsa Windows 10 BitLocker-nycklar (om sådana finns) i Azure Portal. Rollen ger inte behörighet att hantera andra egenskaper på enheten.

* **[Administratör för efterlevnad](#compliance-administrator)** : Användare med den här rollen har behörighet att hantera kompatibla funktioner i Microsoft 365 Compliance Center, Microsoft 365 administrations Center, Azure och Office 365 Säkerhets-och efterlevnadscenter. Tilldelningar kan också hantera alla funktioner i administrations centret för Exchange och team & Skype för företag-administratörer och skapa support biljetter för Azure och Microsoft 365. Mer information finns i [om Office 365-administratörs roller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

  I | Kan göra
  ----- | ----------
  [Microsoft 365 Compliance Center](https://protection.office.com) | Skydda och hantera din organisations data mellan Microsoft 365 tjänster<br>Hantera aviseringar om efterlevnad
  [Compliance Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Spåra, tilldela och verifiera organisationens uppgifter för regelefterlevnad
  [Office 365 Säkerhets-och efterlevnadscenter](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Hantera data styrning<br>Utföra juridisk information och data undersökning<br>Hantera begäran om data subjekt
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Visa alla gransknings data i Intune
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Har Läs behörighet och kan hantera aviseringar<br>Kan skapa och ändra fil principer och tillåta fil styrnings åtgärder<br> Kan visa alla inbyggda rapporter under Datahantering

* **[Administratör för kompatibilitets data](#compliance-data-administrator)** : Användare med den här rollen har behörighet att spåra data i Microsoft 365 Compliance Center, Microsoft 365 administrations Center och Azure. Användare kan också spåra efterlevnadsprinciper i administrations centret för Exchange, Compliance Manager och Teams & Skype för företag administrations Center och skapa support biljetter för Azure och Microsoft 365.

  I | Kan göra
  ----- | ----------
  [Microsoft 365 Compliance Center](https://protection.office.com) | Övervaka efterlevnadsprinciper relaterade principer i Microsoft 365 Services<br>Hantera aviseringar om efterlevnad
  [Compliance Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Spåra, tilldela och verifiera organisationens uppgifter för regelefterlevnad
  [Office 365 Säkerhets-och efterlevnadscenter](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Hantera data styrning<br>Utföra juridisk information och data undersökning<br>Hantera begäran om data subjekt
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Visa alla gransknings data i Intune
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Har Läs behörighet och kan hantera aviseringar<br>Kan skapa och ändra fil principer och tillåta fil styrnings åtgärder<br> Kan visa alla inbyggda rapporter under Datahantering

* **[Administratör för villkorlig åtkomst](#conditional-access-administrator)** : Användare med den här rollen kan hantera inställningar för Azure Active Directory villkorlig åtkomst.
  > [!NOTE]
  > Om du vill distribuera principen för villkorlig åtkomst för Exchange ActiveSync i Azure måste användaren också vara global administratör.
  
* **[Customer lockbox åtkomst god kännare](#customer-lockbox-access-approver)** : Hanterar [Customer lockbox begär Anden](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests) i din organisation. De får e-postaviseringar för Customer Lockbox begär Anden och kan godkänna och Neka förfrågningar från Microsoft 365 administrations Center. De kan också aktivera eller inaktivera funktionen för Customer Lockbox. Endast globala administratörer kan återställa lösen orden för personer som har tilldelats den här rollen.
  <!--  This was announced in August of 2018. https://techcommunity.microsoft.com/t5/Security-Privacy-and-Compliance/Customer-Lockbox-Approver-Role-Now-Available/ba-p/223393-->

* **[Administratör för Skriv bords analys](#desktop-analytics-administrator)** : Användare med den här rollen kan hantera Desktop Analytics-och Office-anpassning & princip tjänster. För Skriv bords analys omfattar detta möjlighet att visa till gångs lager, skapa distributions planer, Visa distribution och hälso status. Den här rollen ger användare möjlighet att hantera Office-principer för tjänsten för anpassning av Office-&.

* **[Enhets administratör](#device-administrators)** : Den här rollen är endast tillgänglig för tilldelning som en ytterligare lokal administratör i [enhets inställningar](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Användare med den här rollen blir lokala dator administratörer på alla Windows 10-enheter som är anslutna till Azure Active Directory. De har inte möjlighet att hantera enheters objekt i Azure Active Directory. 

* **[Katalog läsare](#directory-readers)** : Det här är en roll som endast ska tilldelas till äldre program som inte stöder [medgivande ramverket](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Tilldela den inte till användare.

* **[Konton för Directory-synkronisering](#directory-synchronization-accounts)** : Använd inte. Den här rollen tilldelas automatiskt till den Azure AD Connect tjänsten och är inte avsedd eller stöds inte för någon annan användning.

* **[Katalog skrivare](#directory-writers)** : Det här är en äldre roll som ska tilldelas till program som inte stöder [medgivande ramverket](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). De ska inte tilldelas till några användare.

* **[Dynamics 365-administratör/CRM-administratör](#crm-service-administrator)** : Användare med den här rollen har globala behörigheter inom Microsoft Dynamics 365 online när tjänsten finns, samt möjligheten att hantera support biljetter och övervaka tjänstens hälsa. Mer information finns i [använda rollen tjänst administratör för att hantera din klient](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).
  > [!NOTE] 
  > I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras den här rollen som "Dynamics 365 service Administrator". Det är "Dynamics 365-administratör" i [Azure Portal](https://portal.azure.com).

* **[Exchange-administratör](#exchange-service-administrator)** : Användare med den här rollen har globala behörigheter inom Microsoft Exchange Online när tjänsten finns. Har också möjlighet att skapa och hantera alla Office 365-grupper, hantera support biljetter och övervaka tjänstens hälsa. Mer information [om administratörs roller för Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras den här rollen som "Exchange Service Administrator". Det är "Exchange-administratör" i [Azure Portal](https://portal.azure.com). Det är "Exchange Online administratörs" i [administrations centret för Exchange](https://go.microsoft.com/fwlink/p/?LinkID=529144). 

* **[Administratör för extern identitetsprovider](#external-identity-provider-administrator)** : Den här administratören hanterar federationen mellan Azure Active Directory klienter och externa identitets leverantörer. Med den här rollen kan användarna lägga till nya identitets leverantörer och konfigurera alla tillgängliga inställningar (t. ex. autentiserings Sök väg, tjänst-ID, tilldelade nyckel behållare). Den här användaren kan göra det möjligt för klienten att lita på autentiseringar från externa identitets leverantörer. Den resulterande påverkan på slutanvändarens upplevelse beror på typen av klient organisation:
  * Azure Active Directory klienter för anställda och partner: Att lägga till en Federation (t. ex. med Gmail) kommer omedelbart att påverka alla gäst inbjudningar som ännu inte har lösts in. Se [lägga till Google som en identitets leverantör för B2B-gäst användare](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).
  * Azure Active Directory B2C klienter: Att lägga till en Federation (t. ex. med Facebook eller med en annan Azure Active Directory) påverkar inte omedelbart slutanvändarens flöde förrän identitets leverantören har lagts till som ett alternativ i ett användar flöde (aka inbyggd princip). Se [Konfigurera en Microsoft-konto som en identitets leverantör](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app) för ett exempel. Om du vill ändra användar flöden krävs den begränsade rollen "B2C User Flow Administrator".

* **[Global administratör/företags administratör](#company-administrator)** : Användare med den här rollen har åtkomst till alla administrativa funktioner i Azure Active Directory, samt tjänster som använder Azure Active Directory identiteter som Microsoft 365 Security Center, Microsoft 365 Compliance Center, Exchange Online, SharePoint Online och Skype för företag – online. Den person som registrerar sig för den Azure Active Directory klienten blir global administratör. Endast globala administratörer kan tilldela andra administratörs roller. Det kan finnas mer än en global administratör på ditt företag. Globala administratörer kan återställa lösen ordet för alla användare och alla andra administratörer.

  > [!NOTE]
  > I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras rollen som "företags administratör". Det är "global administratör" i [Azure Portal](https://portal.azure.com).
  >
  >

* **[Gäst deltagare](#guest-inviter)** : Användare med den här rollen kan hantera Azure Active Directory B2B-gäst användare om **medlemmar kan bjuda** in användar inställningen till Nej. Mer information om B2B-samarbete vid [Azure AD B2B-samarbete](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Den innehåller inga andra behörigheter.

* **[Supportavdelningen-administratör](#helpdesk-administrator)** : Användare med den här rollen kan ändra lösen ord, ogiltig uppdatera tokens, hantera tjänst begär Anden och övervaka tjänstens hälsa. Om en uppdateringstoken inverifieras tvingar användaren att logga in igen. Administratörer för supportavdelningen kan återställa lösen ord och göra en ogiltig verifiering av uppdateringstoken för andra användare som inte är administratörer eller som tilldelas följande roller:
  * Katalogläsare
  * Gästinbjudare
  * Supportavdelningsadministratör
  * Meddelandecenterläsare
  * Rapportläsare
  
  <b>Viktigt</b>: Användare med den här rollen kan ändra lösen ord för personer som kan ha åtkomst till känslig eller privat information eller kritisk konfiguration i och utanför Azure Active Directory. Att ändra lösen ordet för en användare kan innebära att du antar användarens identitet och behörigheter. Exempel:
  * Program registrering och företags program ägare, som kan hantera autentiseringsuppgifter för appar som de äger. De här apparna kan ha privilegierade behörigheter i Azure AD och beviljas ingen annan support administratör. Med hjälp av den här sökvägen kan IT-administratören kunna ta hänsyn till identiteten för en program ägare och sedan ytterligare utgå från identiteten för ett privilegierat program genom att uppdatera autentiseringsuppgifterna för programmet.
  * Azure-Prenumerationens ägare, som kan ha åtkomst till känslig eller privat information eller kritisk konfiguration i Azure.
  * Säkerhets grupp och Office 365-grupp ägare, som kan hantera grupp medlemskap. Dessa grupper kan ge åtkomst till känslig eller privat information eller kritisk konfiguration i Azure AD och någon annan stans.
  * Administratörer i andra tjänster utanför Azure AD, till exempel Exchange Online, Office Security och Compliance Center och personal system.
  * Icke-administratörer som chefer, juridiska konsulter och personal anställda som kan ha till gång till känslig eller privat information.


  > [!NOTE]
  > Det kan vara möjligt att delegera administrativa behörigheter över del mängder av användare och tillämpa principer på en delmängd användare med [administrativa enheter (förhands granskning)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units).


  > [!NOTE]
  > Den här rollen kallades tidigare för "lösen ords administratör" i [Azure Portal](https://portal.azure.com/). Vi har ändrat namnet till "supportavdelningens administratör" för att matcha namnet i Azure AD PowerShell, Azure AD Graph API och Microsoft Graph API.

* **[Intune-administratör](#intune-service-administrator)** : Användare med den här rollen har globala behörigheter inom Microsoft Intune online när tjänsten finns. Dessutom innehåller den här rollen möjligheten att hantera användare och enheter för att associera principer, samt skapa och hantera grupper. Mer information finns i [rollbaserad administrations kontroll (RBAC) med Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control)
  > [!NOTE]
  > I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras den här rollen som "Intune-tjänsteadministratör". Det är "Intune-administratör" i [Azure Portal](https://portal.azure.com).
  
 * **[Kaizala-administratör](#kaizala-administrator)** : Användare med den här rollen har globala behörigheter för att hantera inställningar inom Microsoft Kaizala när tjänsten finns, samt möjligheten att hantera support biljetter och övervaka tjänstens hälsa.
Dessutom kan användaren komma åt rapporter som är relaterade till antagande & användningen av Kaizala av organisations medlemmar och affärs rapporter som genereras med hjälp av Kaizala-åtgärder. 

* **[Licens administratör](#license-administrator)** : Användare med den här rollen kan lägga till, ta bort och uppdatera licens tilldelningar för användare, grupper (med gruppbaserad licensiering) och hantera användnings platsen för användare. Rollen ger inte möjlighet att köpa eller hantera prenumerationer, skapa eller hantera grupper eller skapa eller hantera användare bortom användnings platsen. Den här rollen har ingen åtkomst för att visa, skapa eller hantera support biljetter.

* **[Sekretess läsare för meddelande Center](#message-center-privacy-reader)** : Användare med den här rollen kan övervaka alla meddelanden i meddelande centret, inklusive data sekretess meddelanden. Meddelande Center sekretess läsare får e-postaviseringar, inklusive de som är relaterade till data sekretess och de kan avbryta prenumerationen med hjälp av meddelande Center inställningar. Endast global administratör och meddelande Center sekretess läsare kan läsa data sekretess meddelanden. Dessutom innehåller den här rollen möjligheten att Visa grupper, domäner och prenumerationer. Den här rollen har ingen behörighet att visa, skapa eller hantera tjänst begär Anden.

* **[Meddelande Center läsare](#message-center-reader)** : Användare i den här rollen kan övervaka meddelanden och råd om hälso uppdateringar i [Office 365 meddelande Center](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) för deras organisation på konfigurerade tjänster som Exchange, Intune och Microsoft Teams. Meddelande Center läsare får veckovis e-postsammandrag av inlägg, uppdateringar och kan dela meddelande Center inlägg i Office 365. I Azure AD kommer användare som tilldelats den här rollen bara att ha skrivskyddad åtkomst till Azure AD-tjänster, till exempel användare och grupper. Den här rollen har ingen åtkomst för att visa, skapa eller hantera support biljetter.

* **[Support för partner 1](#partner-tier1-support)** : Använd inte. Den här rollen är inaktuell och kommer att tas bort från Azure AD i framtiden. Den här rollen är avsedd att användas av ett litet antal Microsoft-partner och är inte avsedd för allmän användning.

* **[Support för partner – nivå 2](#partner-tier2-support)** : Använd inte. Den här rollen är inaktuell och kommer att tas bort från Azure AD i framtiden. Den här rollen är avsedd att användas av ett litet antal Microsoft-partner och är inte avsedd för allmän användning.

* **[Lösen ords administratör](#password-administrator)** : Användare med den här rollen har begränsad möjlighet att hantera lösen ord. Den här rollen ger inte möjlighet att hantera tjänst begär Anden eller övervaka tjänstens hälsa. Lösen ords administratörer kan återställa lösen ord för andra användare som inte är administratörer eller medlemmar i följande roller:
  * Katalogläsare
  * Gästinbjudare
  * Lösenordsadministratör

* **[Power BI administratör](#power-bi-service-administrator)** : Användare med den här rollen har globala behörigheter inom Microsoft Power BI när tjänsten finns, samt möjligheten att hantera support biljetter och övervaka tjänstens hälsa. Mer information finns i [förstå Power BI admin-rollen](https://docs.microsoft.com/power-bi/service-admin-role).
  > [!NOTE]
  > I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras den här rollen som "Power BI tjänst administratör". Det är "Power BI administratör" i [Azure Portal](https://portal.azure.com).

* **[Administratör för privilegie rad autentisering](#privileged-authentication-administrator)** : Användare med den här rollen kan ange eller återställa autentiseringsuppgifter som inte är lösen ord för alla användare, inklusive globala administratörer, och kan uppdatera lösen ord för alla användare. Administratörer med privilegie rad autentisering kan tvinga användare att omregistrera sig mot befintliga autentiseringsuppgifter som inte är lösen ord (t. ex. MFA, FIDO) och återkalla "MFA på enheten" och fråga efter MFA vid nästa inloggning för alla användare.

* **[Privilegie rad roll administratör](#privileged-role-administrator)** : Användare med den här rollen kan hantera roll tilldelningar i Azure Active Directory, samt i Azure AD Privileged Identity Management. Dessutom tillåter den här rollen hantering av alla aspekter av Privileged Identity Management och administrativa enheter.

  <b>Viktigt</b>: Den här rollen ger möjlighet att hantera tilldelningar för alla Azure AD-roller, inklusive rollen global administratör. Den här rollen omfattar inte andra privilegierade förmågor i Azure AD som att skapa eller uppdatera användare. Användare som har tilldelats den här rollen kan dock ge sig själva eller andra ytterligare behörigheter genom att tilldela ytterligare roller.

* **[Rapport läsare](#reports-reader)** : Användare med den här rollen kan visa användnings rapporterings data och instrument panelen rapporter i Microsoft 365 administrations Center och implementerings kontext paketet i Power BI. Dessutom ger rollen åtkomst till inloggnings rapporter och aktiviteter i Azure AD och data som returneras av Microsoft Graph repor ting API. En användare som tilldelats rollen rapport läsare kan bara komma åt relevanta användnings-och implementerings mått. De har inte någon administratörs behörighet för att konfigurera inställningar eller komma åt de produktspecifik administrations Center som Exchange. Den här rollen har ingen åtkomst för att visa, skapa eller hantera support biljetter.

* **[Sök administratör](#search-administrator)** : Användare med den här rollen har fullständig åtkomst till alla funktioner för Microsoft Search-hantering i Microsoft 365 administrations centret. Sök administratörer kan delegera Sök administratörer och Sök redigerings roller till användare och skapa och hantera innehåll, t. ex. bok märken, Q & som och platser. Dessa användare kan dessutom Visa meddelande Center, övervaka tjänstens hälsa och skapa tjänst begär Anden.

* **[Sök redigerare](#search-editor)** : Användare med den här rollen kan skapa, hantera och ta bort innehåll för Microsoft Search i Microsoft 365 administrations Center, inklusive bok märken, Q & som och platser.

* **[Säkerhets administratör](#security-administrator)** : Användare med den här rollen har behörighet att hantera säkerhetsrelaterade funktioner i Microsoft 365 Security Center, Azure Active Directory Identity Protection, Azure Information Protection och Office 365 Säkerhets-och efterlevnadscenter. Mer information om Office 365-behörigheter finns i [behörigheter i Office 365-säkerhets-och Efterlevnadscenter](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).
  
  I | Kan göra
  --- | ---
  [Microsoft 365 Security Center](https://protection.office.com) | Övervaka säkerhetsrelaterade principer i Microsoft 365 Services<br>Hantera säkerhetshot och aviseringar<br>Visa rapporter
  Identity Protection Center | Alla behörigheter för säkerhets läsar rollen<br>Dessutom kan du utföra alla åtgärder för identitets skydds Center förutom att återställa lösen ord
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Alla behörigheter för säkerhets läsar rollen<br>**Det går inte att** hantera roll tilldelningar eller inställningar för Azure AD
  [Office 365 Säkerhets-och efterlevnadscenter](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Hantera säkerhets principer<br>Visa, undersöka och reagera på säkerhetshot<br>Visa rapporter
  Azure Advanced Threat Protection | Övervaka och svara på misstänkt säkerhets aktivitet
  Windows Defender ATP och EDR | Tilldela roller<br>Hantera dator grupper<br>Konfigurera identifiering av slut punkts hot och automatiserad reparation<br>Visa, undersöka och svara på aviseringar
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Visar information om användare, enhet, registrering, konfiguration och program<br>Det går inte att göra ändringar i Intune
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Lägg till administratörer, lägga till principer och inställningar, ladda upp loggar och utföra styrnings åtgärder
  [Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Kan visa säkerhets principer, Visa säkerhets tillstånd, redigera säkerhets principer, Visa aviseringar och rekommendationer, ignorera aviseringar och rekommendationer
  [Office 365 service Health](https://docs.microsoft.com/office365/enterprise/view-service-health) | Visa hälso tillståndet för Office 365-tjänster

* **[Säkerhets operator](#security-operator)** : Användare med den här rollen kan hantera aviseringar och ha global Läs behörighet för säkerhetsrelaterade funktioner, inklusive all information i Microsoft 365 Security Center, Azure Active Directory, identitets skydd, Privileged Identity Management och Office 365 Säkerhets-och efterlevnadscenter. Mer information om Office 365-behörigheter finns i [behörigheter i Office 365-säkerhets-och Efterlevnadscenter](https://docs.microsoft.com/office365/securitycompliance/permissions-in-the-security-and-compliance-center).

  I | Kan göra
  --- | ---
  [Microsoft 365 Security Center](https://protection.office.com) | Alla behörigheter för säkerhets läsar rollen<br>Visa, undersöka och svara på säkerhetshot
  Identity Protection Center | Alla behörigheter för säkerhets läsar rollen<br>Dessutom kan du utföra alla åtgärder för identitets skydds Center förutom att återställa lösen ord
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Alla behörigheter för säkerhets läsar rollen
  [Office 365 Säkerhets-och efterlevnadscenter](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Alla behörigheter för säkerhets läsar rollen<br>Visa, undersöka och svara på säkerhets aviseringar
  Windows Defender ATP och EDR | Alla behörigheter för säkerhets läsar rollen<br>Visa, undersöka och svara på säkerhets aviseringar
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Alla behörigheter för säkerhets läsar rollen
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Alla behörigheter för säkerhets läsar rollen
  [Office 365 service Health](https://docs.microsoft.com/office365/enterprise/view-service-health) | Visa hälso tillståndet för Office 365-tjänster
<!--* **[Security Operator](#security-operator)**: Users with this role can manage alerts and have global read-only access on security-related feature, including all information in Microsoft 365 security center, Azure Active Directory, Identity Protection, Privileged Identity Management.-->

* **[Säkerhets läsare](#security-reader)** : Användare med den här rollen har global Läs behörighet i säkerhetsrelaterade funktioner, inklusive all information i Microsoft 365 Security Center, Azure Active Directory, identitets skydd, Privileged Identity Management, samt möjligheten att läsa Azure Active Katalog inloggnings rapporter och gransknings loggar, och i Office 365 Säkerhets-och efterlevnadscenter. Mer information om Office 365-behörigheter finns i [behörigheter i Office 365-säkerhets-och Efterlevnadscenter](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

  I | Kan göra
  --- | ---
  [Microsoft 365 Security Center](https://protection.office.com) | Visa säkerhetsrelaterade principer i Microsoft 365 Services<br>Visa säkerhetshot och aviseringar<br>Visa rapporter
  Identity Protection Center | Läs alla säkerhets rapporter och inställnings information för säkerhetsfunktioner<br><ul><li>Anti-spam<li>Kryptering<li>Skydd mot data förlust<li>Program mot skadlig kod<li>Avancerat skydd<li>Skydd mot nätfiske<li>Flödes regler för flöden
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Har skrivskyddad åtkomst till all information som finns i Azure AD PIM: Principer och rapporter för roll tilldelningar i Azure AD, säkerhets granskningar och i framtiden Läs åtkomst till princip data och rapporter för scenarier förutom Azure AD-roll tilldelning.<br>**Det går inte att** registrera dig för Azure AD PIM eller göra ändringar i den. I PIM-portalen eller via PowerShell kan någon i den här rollen aktivera ytterligare roller (till exempel global administratör eller privilegierad roll administratör), om användaren är berättigad till dem.
  [Office 365 Säkerhets-och efterlevnadscenter](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Visa säkerhetsprinciper<br>Visa och undersök säkerhetshot<br>Visa rapporter
  Windows Defender ATP och EDR | Visa och undersök aviseringar. När du aktiverar rollbaserad åtkomst kontroll i Windows Defender ATP kan användare med Läs behörighet som rollen Azure AD Security Reader förlora åtkomst tills de tilldelas till en Windows Defender ATP-roll.
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Visar information om användare, enhet, registrering, konfiguration och program. Det går inte att göra ändringar i Intune.
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Har Läs behörighet och kan hantera aviseringar
  [Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Kan visa rekommendationer och aviseringar, Visa säkerhets principer, Visa säkerhets tillstånd, men kan inte göra ändringar
  [Office 365 service Health](https://docs.microsoft.com/office365/enterprise/view-service-health) | Visa hälso tillståndet för Office 365-tjänster

* **[Tjänst support administratör](#service-support-administrator)** : Användare med den här rollen kan öppna support förfrågningar med Microsoft för Azure-och Office 365-tjänster och Visa instrument panelen och meddelande centret i [Azure Portal](https://portal.azure.com) och [Microsoft 365 administrations Center](https://admin.microsoft.com). Mer information [om administratörs roller för Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras den här rollen som "tjänst support administratör". Det är "tjänst administratör" i [Azure Portal](https://portal.azure.com), [Microsoft 365 administrations Center](https://admin.microsoft.com)och Intune-portalen.

* **[SharePoint-administratör](#sharepoint-service-administrator)** : Användare med den här rollen har globala behörigheter inom Microsoft SharePoint Online när tjänsten finns, samt möjligheten att skapa och hantera alla Office 365-grupper, hantera support biljetter och övervaka tjänstens hälsa. Mer information [om administratörs roller för Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras den här rollen som "SharePoint-tjänsteadministratör". Det är "SharePoint-administratör" i [Azure Portal](https://portal.azure.com).

* **[Skype för företag/Lync-administratör](#lync-service-administrator)** : Användare med den här rollen har globala behörigheter inom Microsoft Skype för företag, när tjänsten finns och hantera Skype-specifika användarattribut i Azure Active Directory. Dessutom ger den här rollen möjlighet att hantera support biljetter och övervaka tjänstens hälsa och få åtkomst till team och Skype för Business administrations Center. Kontot måste också vara licensierat för team, eller så kan det inte köra team PowerShell-cmdletar. Mer information [om administrations rollen för Skype för företag](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) och team licens information finns på [Skype för företag och Microsoft Teams tilläggs licensiering](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

  > [!NOTE]
  > I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras rollen som "Lync-tjänsteadministratör". Det är "Skype för företag-administratör" i [Azure Portal](https://portal.azure.com/).

* **[Team administratör](#teams-service-administrator)** : Användare med den här rollen kan hantera alla aspekter av arbets belastningen Microsoft Teams via Microsoft Teams & Skype för Business administrations Center och respektive PowerShell-moduler. Detta omfattar bland annat alla hanterings verktyg som rör telefoni, meddelanden, möten och själva teamet. Den här rollen ger dessutom möjlighet att skapa och hantera alla Office 365-grupper, hantera support biljetter och övervaka tjänstens hälsa.
  > [!NOTE]
  > I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras den här rollen som "team tjänst administratör". Det är "team administratör" i [Azure Portal](https://portal.azure.com).

* **[Team kommunikations administratör](#teams-communications-administrator)** : Användare med den här rollen kan hantera delar av arbets belastningen Microsoft Teams som rör röst & telefoni. Här ingår hanterings verktyg för tilldelning av telefonnummer, röst-och mötes principer och fullständig åtkomst till verktygen för anrops analys.

* **[Support tekniker för Teams kommunikation](#teams-communications-support-engineer)** : Användare i den här rollen kan felsöka kommunikations problem i Microsoft Teams & Skype för företag med hjälp av fel söknings verktygen för användar anrop i Microsoft Teams & Skype för företag administrations Center. Användare med den här rollen kan Visa fullständig information om anrops poster för alla berörda deltagare. Den här rollen har ingen åtkomst för att visa, skapa eller hantera support biljetter.

* **[Support specialist för Teams kommunikation](#teams-communications-support-specialist)** : Användare i den här rollen kan felsöka kommunikations problem i Microsoft Teams & Skype för företag med hjälp av fel söknings verktygen för användar anrop i Microsoft Teams & Skype för företag administrations Center. Användare med den här rollen kan bara Visa användar information i anropet för den aktuella användaren som de har sökt efter. Den här rollen har ingen åtkomst för att visa, skapa eller hantera support biljetter.

* **[Användar administratör](#user-administrator)** : Användare med den här rollen kan skapa användare och hantera alla aspekter av användare med vissa begränsningar (se nedan) och kan uppdatera principer för lösen ordets giltighets tid. Dessutom kan användare med den här rollen skapa och hantera alla grupper. Den här rollen omfattar även möjligheten att skapa och hantera användar visningar, hantera support biljetter och övervaka tjänstens hälsa.

  | | |
  | --- | --- |
  |Allmänna behörigheter|<p>Skapa användare och grupper</p><p>Skapa och hantera användar visningar</p><p>Hantera Office-support biljetter<p>Uppdatera principer för förfallo datum för lösen ord|
  |<p>För alla användare, inklusive alla administratörer</p>|<p>Hantera licenser</p><p>Hantera alla användar egenskaper utom användarens huvud namn</p>
  |Endast på användare som inte är administratörer eller någon av följande begränsade administratörs roller:<ul><li>Katalogläsare<li>Gästinbjudare<li>Supportavdelningsadministratör<li>Meddelandecenterläsare<li>Rapportläsare<li>Användaradministratör|<p>Ta bort och Återställ</p><p>Inaktivera och aktivera</p><p>Invalidera uppdateringstoken</p><p>Hantera alla användar egenskaper inklusive användarens huvud namn</p><p>Återställ lösenord</p><p>Uppdatera (FIDO) enhets nycklar</p>
  
  <b>Viktigt</b>: Användare med den här rollen kan ändra lösen ord för personer som kan ha åtkomst till känslig eller privat information eller kritisk konfiguration i och utanför Azure Active Directory. Att ändra lösen ordet för en användare kan innebära att du antar användarens identitet och behörigheter. Exempel:
  * Program registrering och företags program ägare, som kan hantera autentiseringsuppgifter för appar som de äger. Dessa appar kan ha privilegierade behörigheter i Azure AD och inte beviljas till användar administratörer. Genom den här sökvägen kan en användar administratör kunna ta hänsyn till identiteten för en program ägare och sedan ytterligare anta identiteten för ett privilegierat program genom att uppdatera autentiseringsuppgifterna för programmet.
  * Azure-Prenumerationens ägare, som kan ha åtkomst till känslig eller privat information eller kritisk konfiguration i Azure.
  * Säkerhets grupp och Office 365-grupp ägare, som kan hantera grupp medlemskap. Dessa grupper kan ge åtkomst till känslig eller privat information eller kritisk konfiguration i Azure AD och någon annan stans.
  * Administratörer i andra tjänster utanför Azure AD, till exempel Exchange Online, Office Security och Compliance Center och personal system.
  * Icke-administratörer som chefer, juridiska konsulter och personal anställda som kan ha till gång till känslig eller privat information.

## <a name="role-permissions"></a>Roll behörigheter
I följande tabeller beskrivs de angivna behörigheterna i Azure Active Directory tilldelas varje roll. Vissa roller kan ha ytterligare behörigheter i Microsoft-tjänster utanför Azure Active Directory.

### <a name="application-administrator"></a>Programadministratör
Kan skapa och hantera alla aspekter av registrerings-och företags program.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. AAD. Directory/program/mål grupp/uppdatering | Uppdatera program. Audience-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/program/autentisering/uppdatering | Uppdatera program. Authentication-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Applications/Basic/Update | Uppdatera grundläggande egenskaper för program i Azure Active Directory. |
| Microsoft. AAD. Directory/Applications/skapa | Skapa program i Azure Active Directory. |
| Microsoft. AAD. Directory/program/autentiseringsuppgifter/uppdatera | Uppdatera program. credentials-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Applications/Delete | Ta bort program i Azure Active Directory. |
| Microsoft. AAD. Directory/program/ägare/uppdatera | Uppdatera program. Owners-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/program/behörigheter/uppdatera | Uppdatera program. Permissions-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Applications/policies/Update | Uppdatera Applications. policys-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/appRoleAssignments/Create | Skapa appRoleAssignments i Azure Active Directory. |
| Microsoft. AAD. Directory/appRoleAssignments/Read | Läs appRoleAssignments i Azure Active Directory. |
| Microsoft. AAD. Directory/appRoleAssignments/Update | Uppdatera appRoleAssignments i Azure Active Directory. |
| Microsoft. AAD. Directory/appRoleAssignments/Delete | Ta bort appRoleAssignments i Azure Active Directory. |
| Microsoft. AAD. Directory/auditLogs/allProperties/Read | Läs alla egenskaper (inklusive privilegierade egenskaper) på auditLogs i Azure Active Directory. |
| Microsoft. AAD. Directory/policies/applicationConfiguration/Basic/Read | Läs policies. applicationConfiguration-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/policies/applicationConfiguration/Basic/Update | Uppdatera policys. applicationConfiguration-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/policys/applicationConfiguration/Create | Skapa principer i Azure Active Directory. |
| Microsoft. AAD. Directory/policys/applicationConfiguration/Delete | Ta bort principer i Azure Active Directory. |
| Microsoft. AAD. Directory/policies/applicationConfiguration/Owners/Read | Läs policies. applicationConfiguration-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/policys/applicationConfiguration/Owners/Update | Uppdatera policys. applicationConfiguration-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/policys/applicationConfiguration/policyAppliedTo/Read | Läs policies. applicationConfiguration-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Uppdatera service princip ALS. appRoleAssignedTo-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/appRoleAssignments/Update | Uppdatera service princip ALS. appRoleAssignments-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/Audience/Update | Uppdatera service princip ALS. Audience-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/Authentication/Update | Uppdatera service princip ALS. Authentication-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Uppdatera grundläggande egenskaper för service princip ALS i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/Create | Skapa service princip ALS i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Uppdatera service princip ALS. credentials-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/Delete | Ta bort service princip ALS i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/Owners/Update | Uppdatera service princip ALS. Owners-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/behörigheter/uppdatera | Uppdatera service princip ALS. Permissions-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/policies/Update | Uppdatera service princip ALS. policys-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/signInReports/allProperties/Read | Läs alla egenskaper (inklusive privilegierade egenskaper) på signInReports i Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera support biljetter för Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-support biljetter. |

### <a name="application-developer"></a>Programutvecklare
Kan skapa program registreringar oberoende av inställningen användare kan registrera program.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. AAD. Directory/Applications/createAsOwner | Skapa program i Azure Active Directory. Skaparen läggs till som den första ägaren och det antal skapade objektet mot skapare objekt kvoten 250 har skapats. |
| Microsoft. AAD. Directory/appRoleAssignments/createAsOwner | Skapa appRoleAssignments i Azure Active Directory. Skaparen läggs till som den första ägaren och det antal skapade objektet mot skapare objekt kvoten 250 har skapats. |
| Microsoft. AAD. Directory/oAuth2PermissionGrants/createAsOwner | Skapa oAuth2PermissionGrants i Azure Active Directory. Skaparen läggs till som den första ägaren och det antal skapade objektet mot skapare objekt kvoten 250 har skapats. |
| Microsoft. AAD. Directory/Service princip ALS/createAsOwner | Skapa service princip ALS i Azure Active Directory. Skaparen läggs till som den första ägaren och det antal skapade objektet mot skapare objekt kvoten 250 har skapats. |

### <a name="authentication-administrator"></a>Autentiseringsadministratör
Tillåts Visa, ange och återställa information om autentiseringsinformation för användare som inte är administratörer.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. AAD. Directory/Users/invalidateAllRefreshTokens | Ogiltig verifiering av alla användares uppdateringstoken i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/strongAuthentication/Update | Uppdatera egenskaper för stark autentisering som MFA-autentiseringsinformation. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera support biljetter för Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i Microsoft. Office365. Web-Portal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-support biljetter. |
| Microsoft. AAD. Directory/Users/Password/Update | Uppdatera lösen ord för alla användare i Office 365-organisationen. Mer information finns i onlinedokumentationen. |

### <a name="azure-information-protection-administrator"></a>Azure Information Protection administratör
Kan hantera alla aspekter av tjänsten Azure Information Protection.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i roll beskrivning ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Hantera alla aspekter av Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera support biljetter för Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-support biljetter. |

### <a name="b2c-user-flow-administrator"></a>B2C User Flow Administrator
Skapa och hantera alla aspekter av användar flöden.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.b2c/userFlows/allTasks | Läsa och konfigurera användar flöden i Azure Active Directory B2C. |

### <a name="b2c-user-flow-attribute-administrator"></a>B2C User Flow Attribute Administrator
Skapa och hantera attributet schema tillgängligt för alla användar flöden.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.b2c/userAttributes/allTasks | Läsa och konfigurera användarattribut i Azure Active Directory B2C. |

### <a name="b2c-ief-keyset-administrator"></a>B2C IEF Keyset Administrator
Hantera hemligheter för Federation och kryptering i ramverket för identitets upplevelser.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/keySets/allTasks | Läsa och konfigurera nyckel uppsättningar i Azure Active Directory B2C. |

### <a name="b2c-ief-policy-administrator"></a>B2C IEF Policy Administrator
Skapa och hantera förtroende Ramverks principer i identitets upplevelse ramverket.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/policies/allTasks | Läsa och konfigurera anpassade principer i Azure Active Directory B2C. |

### <a name="billing-administrator"></a>Faktureringsadministratör
Kan utföra vanliga fakturerings aktiviteter, t. ex. uppdatering av betalnings information.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i roll beskrivning ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. AAD. Directory/Organization/Basic/Update | Uppdatera grundläggande egenskaper för organisation i Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera support biljetter för Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Hantera alla aspekter av Office 365-fakturering. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i Microsoft. Office365. Web-Portal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-support biljetter. |


### <a name="cloud-application-administrator"></a>Molnprogramadministratör
Kan skapa och hantera alla aspekter av app-registreringar och företags program förutom App proxy.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. AAD. Directory/program/mål grupp/uppdatering | Uppdatera program. Audience-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/program/autentisering/uppdatering | Uppdatera program. Authentication-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Applications/Basic/Update | Uppdatera grundläggande egenskaper för program i Azure Active Directory. |
| Microsoft. AAD. Directory/Applications/skapa | Skapa program i Azure Active Directory. |
| Microsoft. AAD. Directory/program/autentiseringsuppgifter/uppdatera | Uppdatera program. credentials-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Applications/Delete | Ta bort program i Azure Active Directory. |
| Microsoft. AAD. Directory/program/ägare/uppdatera | Uppdatera program. Owners-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/program/behörigheter/uppdatera | Uppdatera program. Permissions-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Applications/policies/Update | Uppdatera Applications. policys-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/appRoleAssignments/Create | Skapa appRoleAssignments i Azure Active Directory. |
| Microsoft. AAD. Directory/appRoleAssignments/Update | Uppdatera appRoleAssignments i Azure Active Directory. |
| Microsoft. AAD. Directory/appRoleAssignments/Delete | Ta bort appRoleAssignments i Azure Active Directory. |
| Microsoft. AAD. Directory/auditLogs/allProperties/Read | Läs alla egenskaper (inklusive privilegierade egenskaper) på auditLogs i Azure Active Directory. |
| Microsoft. AAD. Directory/policys/applicationConfiguration/Create | Skapa principer i Azure Active Directory. |
| Microsoft. AAD. Directory/policies/applicationConfiguration/Basic/Read | Läs policies. applicationConfiguration-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/policies/applicationConfiguration/Basic/Update | Uppdatera policys. applicationConfiguration-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/policys/applicationConfiguration/Delete | Ta bort principer i Azure Active Directory. |
| Microsoft. AAD. Directory/policies/applicationConfiguration/Owners/Read | Läs policies. applicationConfiguration-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/policys/applicationConfiguration/Owners/Update | Uppdatera policys. applicationConfiguration-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/policys/applicationConfiguration/policyAppliedTo/Read | Läs policies. applicationConfiguration-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Uppdatera service princip ALS. appRoleAssignedTo-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/appRoleAssignments/Update | Uppdatera service princip ALS. appRoleAssignments-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/Audience/Update | Uppdatera service princip ALS. Audience-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/Authentication/Update | Uppdatera service princip ALS. Authentication-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Uppdatera grundläggande egenskaper för service princip ALS i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/Create | Skapa service princip ALS i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Uppdatera service princip ALS. credentials-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/Delete | Ta bort service princip ALS i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/Owners/Update | Uppdatera service princip ALS. Owners-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/behörigheter/uppdatera | Uppdatera service princip ALS. Permissions-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/policies/Update | Uppdatera service princip ALS. policys-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/signInReports/allProperties/Read | Läs alla egenskaper (inklusive privilegierade egenskaper) på signInReports i Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera support biljetter för Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-support biljetter. |

### <a name="cloud-device-administrator"></a>Molnenhetsadministratör
Fullständig åtkomst till hantering av enheter i Azure AD.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. AAD. Directory/auditLogs/allProperties/Read | Läs alla egenskaper (inklusive privilegierade egenskaper) på auditLogs i Azure Active Directory. |
| Microsoft. AAD. Directory/Devices/bitLockerRecoveryKeys/Read | Läsa Devices. bitLockerRecoveryKeys-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Devices/Delete | Ta bort enheter i Azure Active Directory. |
| Microsoft. AAD. Directory/Devices/Disable | Inaktivera enheter i Azure Active Directory. |
| Microsoft. AAD. Directory/Devices/Enable | Aktivera enheter i Azure Active Directory. |
| Microsoft. AAD. Directory/signInReports/allProperties/Read | Läs alla egenskaper (inklusive privilegierade egenskaper) på signInReports i Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läs och konfigurera Office 365 Service Health. |

### <a name="company-administrator"></a>Företagsadministratör
Kan hantera alla aspekter av Azure AD och Microsoft-tjänster som använder Azure AD-identiteter.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i roll beskrivning ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera standard egenskaper i Microsoft. AAD. cloudAppSecurity. |
| Microsoft. AAD. Directory/administrativeUnits/allProperties/allTasks | Skapa och ta bort administrativeUnits och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| Microsoft. AAD. Directory/Applications/allProperties/allTasks | Skapa och ta bort program och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| Microsoft. AAD. Directory/appRoleAssignments/allProperties/allTasks | Skapa och ta bort appRoleAssignments och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| Microsoft. AAD. Directory/auditLogs/allProperties/Read | Läs alla egenskaper (inklusive privilegierade egenskaper) på auditLogs i Azure Active Directory. |
| Microsoft. AAD. Directory/Contacts/allProperties/allTasks | Skapa och ta bort kontakter och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| Microsoft. AAD. Directory/kontrakt/allProperties/allTasks | Skapa och ta bort kontrakt och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| Microsoft. AAD. Directory/Devices/allProperties/allTasks | Skapa och ta bort enheter och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| Microsoft. AAD. Directory/directoryRoles/allProperties/allTasks | Skapa och ta bort directoryRoles och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| Microsoft. AAD. Directory/directoryRoleTemplates/allProperties/allTasks | Skapa och ta bort directoryRoleTemplates och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| Microsoft. AAD. Directory/Domains/allProperties/allTasks | Skapa och ta bort domäner och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| Microsoft. AAD. Directory/Groups/allProperties/allTasks | Skapa och ta bort grupper och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/groupSettings/allProperties/allTasks | Skapa och ta bort groupSettings och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/allProperties/allTasks | Skapa och ta bort groupSettingTemplates och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| Microsoft. AAD. Directory/loginTenantBranding/allProperties/allTasks | Skapa och ta bort loginTenantBranding och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/allProperties/allTasks | Skapa och ta bort oAuth2PermissionGrants och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| Microsoft. AAD. Directory/Organization/allProperties/allTasks | Skapa och ta bort organisation och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| Microsoft. AAD. Directory/policys/allProperties/allTasks | Skapa och ta bort principer och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| Microsoft. AAD. Directory/roleAssignments/allProperties/allTasks | Skapa och ta bort roleAssignments och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Skapa och ta bort roleDefinitions och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| Microsoft. AAD. Directory/scopedRoleMemberships/allProperties/allTasks | Skapa och ta bort scopedRoleMemberships och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| Microsoft. AAD. Directory/serviceAction/activateService | Kan utföra Activateservice-tjänstens åtgärd i Azure Active Directory |
| Microsoft. AAD. Directory/serviceAction/disableDirectoryFeature | Kan utföra Disabledirectoryfeature-tjänstens åtgärd i Azure Active Directory |
| microsoft.aad.directory/serviceAction/enableDirectoryFeature | Kan utföra Enabledirectoryfeature-tjänstens åtgärd i Azure Active Directory |
| microsoft.aad.directory/serviceAction/getAvailableExtentionProperties | Kan utföra Getavailableextentionproperties-tjänstens åtgärd i Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/allProperties/allTasks | Skapa och ta bort service princip ALS och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| Microsoft. AAD. Directory/signInReports/allProperties/Read | Läs alla egenskaper (inklusive privilegierade egenskaper) på signInReports i Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/allProperties/allTasks | Skapa och ta bort subscribedSkus och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/allProperties/allTasks | Skapa och ta bort användare och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| Microsoft. AAD. directorySync/upplånade/allTasks | Utföra alla åtgärder i Azure AD Connect. |
| Microsoft. AAD. identityProtection/upplånade/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera standard egenskaper i Microsoft. AAD. identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Läs alla resurser i Microsoft. AAD. privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Läs alla resurser i Microsoft. Azure. advancedThreatProtection. |
| microsoft.azure.informationProtection/allEntities/allTasks | Hantera alla aspekter av Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera support biljetter för Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Hantera alla aspekter av Office 365-fakturering. |
| microsoft.intune/allEntities/allTasks | Hantera alla aspekter av Intune. |
| microsoft.office365.complianceManager/allEntities/allTasks | Hantera alla aspekter av Compliance Manager för Office 365 |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Hantera alla aspekter av Skriv bords analys. |
| microsoft.office365.exchange/allEntities/allTasks | Hantera alla aspekter av Exchange Online. |
| microsoft.office365.lockbox/allEntities/allTasks | Hantera alla aspekter av Office 365 Customer Lockbox |
| microsoft.office365.messageCenter/messages/read | Läs meddelanden i Microsoft. Office365. messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Läs securityMessages i Microsoft. Office365. messageCenter. |
| microsoft.office365.protectionCenter/allEntities/allTasks | Hantera alla aspekter av Office 365 Protection Center. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera standard egenskaper i Microsoft. Office365. securityComplianceCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.sharepoint/allEntities/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera standard egenskaper i Microsoft. Office365. SharePoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Hantera alla aspekter av Skype för företag – online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-support biljetter. |
| microsoft.office365.usageReports/allEntities/read | Läs Office 365-användnings rapporter. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i Microsoft. Office365. Web-Portal. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Hantera alla aspekter av Dynamics 365. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Hantera alla aspekter av Power BI. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Läs alla resurser i Microsoft. Windows. defenderAdvancedThreatProtection. |

### <a name="compliance-administrator"></a>Efterlevnadsadministratör
Kan läsa och hantera kompatibilitets konfiguration och rapporter i Azure AD och Office 365.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i roll beskrivning ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera support biljetter för Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i Microsoft. Office365. Web-Portal. |
| microsoft.office365.complianceManager/allEntities/allTasks | Hantera alla aspekter av Compliance Manager för Office 365 |
| microsoft.office365.exchange/allEntities/allTasks | Hantera alla aspekter av Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.sharepoint/allEntities/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera standard egenskaper i Microsoft. Office365. SharePoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Hantera alla aspekter av Skype för företag – online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-support biljetter. |

### <a name="compliance-data-administrator"></a>Data administratör för efterlevnad
Skapar och hanterar innehåll för efterlevnad.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i roll beskrivning ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Läsa och konfigurera Microsoft Cloud App Security. |
| microsoft.azure.informationProtection/allEntities/allTasks | Hantera alla aspekter av Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera support biljetter för Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i Microsoft. Office365. Web-Portal. |
| microsoft.office365.complianceManager/allEntities/allTasks | Hantera alla aspekter av Compliance Manager för Office 365 |
| microsoft.office365.exchange/allEntities/allTasks | Hantera alla aspekter av Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.sharepoint/allEntities/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera standard egenskaper i Microsoft. Office365. SharePoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Hantera alla aspekter av Skype för företag – online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-support biljetter. |

### <a name="conditional-access-administrator"></a>Administratör för villkorsstyrd åtkomst
Kan hantera funktioner för villkorlig åtkomst.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. AAD. Directory/policies/conditionalAccess/Basic/Read | Läs policies. conditionalAccess-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/policies/conditionalAccess/Basic/Update | Uppdatera policys. conditionalAccess-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/policys/conditionalAccess/Create | Skapa principer i Azure Active Directory. |
| Microsoft. AAD. Directory/policys/conditionalAccess/Delete | Ta bort principer i Azure Active Directory. |
| Microsoft. AAD. Directory/policies/conditionalAccess/Owners/Read | Läs policies. conditionalAccess-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/policys/conditionalAccess/Owners/Update | Uppdatera policys. conditionalAccess-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/policys/conditionalAccess/policiesAppliedTo/Read | Läs policies. conditionalAccess-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/tenantDefault/update | Uppdatera policys. conditionalAccess-egenskapen i Azure Active Directory. |

### <a name="crm-service-administrator"></a>Tjänstadministratör för CRM
Kan hantera alla aspekter av Dynamics 365-produkten.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i roll beskrivning ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera support biljetter för Azure. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Hantera alla aspekter av Dynamics 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i Microsoft. Office365. Web-Portal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-support biljetter. |

### <a name="customer-lockbox-access-approver"></a>Godkännare av åtkomst till Customer LockBox
Kan godkänna Microsofts support förfrågningar för att få åtkomst till kundens organisations data.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i roll beskrivning ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i Microsoft. Office365. Web-Portal. |
| microsoft.office365.lockbox/allEntities/allTasks | Hantera alla aspekter av Office 365 Customer Lockbox |

### <a name="desktop-analytics-administrator"></a>Desktop Analytics-administratör
Kan hantera Desktop Analytics-och Office-anpassning & princip tjänster. För Skriv bords analys omfattar detta möjlighet att visa till gångs lager, skapa distributions planer, Visa distribution och hälso status. Den här rollen ger användare möjlighet att hantera Office-principer för tjänsten för anpassning av Office-&.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i roll beskrivning ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera support biljetter för Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i Microsoft. Office365. Web-Portal. |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Hantera alla aspekter av Skriv bords analys. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-support biljetter. |

### <a name="device-administrators"></a>Enhetsadministratörer
Användare som har tilldelats den här rollen läggs till i den lokala administratörs gruppen på Azure AD-anslutna enheter.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. AAD. Directory/groupSettings/Basic/Read | Läsa grundläggande egenskaper för groupSettings i Azure Active Directory. |
| Microsoft. AAD. Directory/groupSettingTemplates/Basic/Read | Läsa grundläggande egenskaper för groupSettingTemplates i Azure Active Directory. |

### <a name="directory-readers"></a>Katalogläsare
Kan läsa grundläggande katalog information. För att bevilja åtkomst till program, som inte är avsedda för användare.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. AAD. Directory/administrativeUnits/Basic/Read | Läsa grundläggande egenskaper för administrativeUnits i Azure Active Directory. |
| Microsoft. AAD. Directory/administrativeUnits/members/Read | Läs administrativeUnits. members-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/program/Basic/Read | Läsa grundläggande egenskaper för program i Azure Active Directory. |
| Microsoft. AAD. Directory/program/ägare/läsa | Läsa program. Owners-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Applications/policies/Read | Läsa program. policys-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Contacts/Basic/Read | Läsa grundläggande egenskaper för kontakter i Azure Active Directory. |
| Microsoft. AAD. Directory/Contacts/memberOf/läsa | Läsa contacts. memberOf-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/kontrakt/Basic/Read | Läsa grundläggande egenskaper för kontrakt i Azure Active Directory. |
| Microsoft. AAD. Directory/Devices/Basic/Read | Läsa grundläggande egenskaper för enheter i Azure Active Directory. |
| Microsoft. AAD. Directory/Devices/memberOf/Read | Läsa Devices. memberOf-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Devices/registeredOwners/Read | Läsa Devices. registeredOwners-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Devices/registeredUsers/Read | Läsa Devices. registeredUsers-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/directoryRoles/Basic/Read | Läsa grundläggande egenskaper för directoryRoles i Azure Active Directory. |
| Microsoft. AAD. Directory/directoryRoles/eligibleMembers/Read | Läsa directoryRoles. eligibleMembers-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/directoryRoles/members/Read | Läs directoryRoles. members-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Domains/Basic/Read | Läsa grundläggande egenskaper för domäner i Azure Active Directory. |
| Microsoft. AAD. Directory/Groups/appRoleAssignments/Read | Läs Groups. appRoleAssignments-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Groups/Basic/Read | Läsa grundläggande egenskaper för grupper i Azure Active Directory. |
| Microsoft. AAD. Directory/grupper/memberOf/läsa | Läsa Groups. memberOf-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/grupper/medlemmar/läsa | Läsa Groups. members-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Groups/Owners/Read | Läsa Groups. Owners-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Groups/Settings/Read | Läsa Groups. Settings-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/groupSettings/Basic/Read | Läsa grundläggande egenskaper för groupSettings i Azure Active Directory. |
| Microsoft. AAD. Directory/groupSettingTemplates/Basic/Read | Läsa grundläggande egenskaper för groupSettingTemplates i Azure Active Directory. |
| Microsoft. AAD. Directory/oAuth2PermissionGrants/Basic/Read | Läsa grundläggande egenskaper för oAuth2PermissionGrants i Azure Active Directory. |
| Microsoft. AAD. Directory/Organization/Basic/Read | Läsa grundläggande egenskaper för organisation i Azure Active Directory. |
| Microsoft. AAD. Directory/Organization/trustedCAsForPasswordlessAuth/Read | Läs organisation. trustedCAsForPasswordlessAuth-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/roleAssignments/Basic/Read | Läsa grundläggande egenskaper för roleAssignments i Azure Active Directory. |
| Microsoft. AAD. Directory/roleDefinitions/Basic/Read | Läsa grundläggande egenskaper för roleDefinitions i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/appRoleAssignedTo/Read | Läsa service princip ALS. appRoleAssignedTo-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/appRoleAssignments/Read | Läsa service princip ALS. appRoleAssignments-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/Basic/Read | Läsa grundläggande egenskaper för service princip ALS i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/memberOf/läsa | Läs service princip ALS. memberOf-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/oAuth2PermissionGrants/Basic/Read | Läsa service princip ALS. oAuth2PermissionGrants-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/ownedObjects/Read | Läsa service princip ALS. ownedObjects-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/Owners/Read | Läsa service princip ALS. Owners-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/policies/Read | Läs service princip ALS. policys-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/subscribedSkus/Basic/Read | Läsa grundläggande egenskaper för subscribedSkus i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/appRoleAssignments/Read | Läsa users. appRoleAssignments-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/Basic/Read | Läsa grundläggande egenskaper för användare i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/directReports/Read | Läsa users. directReports-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/Manager/Read | Läsa users. Manager-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/memberOf/läsa | Läsa users. memberOf-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/oAuth2PermissionGrants/Basic/Read | Läsa users. oAuth2PermissionGrants-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/ownedDevices/Read | Läsa users. ownedDevices-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Läsa users. ownedObjects-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/registeredDevices/Read | Läsa users. registeredDevices-egenskapen i Azure Active Directory. |

### <a name="directory-synchronization-accounts"></a>Konton för katalog synkronisering
Används endast av Azure AD Connect-tjänsten.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. AAD. Directory/Organization/dirSync/Update | Uppdatera organisation. dirSync-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/policies/Create | Skapa principer i Azure Active Directory. |
| Microsoft. AAD. Directory/policies/Delete | Ta bort principer i Azure Active Directory. |
| Microsoft. AAD. Directory/policies/Basic/Read | Läsa grundläggande egenskaper för principer i Azure Active Directory. |
| Microsoft. AAD. Directory/policies/Basic/Update | Uppdatera grundläggande egenskaper för principer i Azure Active Directory. |
| Microsoft. AAD. Directory/policies/Owners/Read | Läs policies. Owners-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/policys/Owners/Update | Uppdatera policys. Owners-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/policys/policiesAppliedTo/Read | Läs policies. policiesAppliedTo-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/policys/tenantDefault/Update | Uppdatera policys. tenantDefault-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/appRoleAssignedTo/Read | Läsa service princip ALS. appRoleAssignedTo-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Uppdatera service princip ALS. appRoleAssignedTo-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/appRoleAssignments/Read | Läsa service princip ALS. appRoleAssignments-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/appRoleAssignments/Update | Uppdatera service princip ALS. appRoleAssignments-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/Audience/Update | Uppdatera service princip ALS. Audience-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/Authentication/Update | Uppdatera service princip ALS. Authentication-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/Basic/Read | Läsa grundläggande egenskaper för service princip ALS i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Uppdatera grundläggande egenskaper för service princip ALS i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/Create | Skapa service princip ALS i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Uppdatera service princip ALS. credentials-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/memberOf/läsa | Läs service princip ALS. memberOf-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/oAuth2PermissionGrants/Basic/Read | Läsa service princip ALS. oAuth2PermissionGrants-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/Owners/Read | Läsa service princip ALS. Owners-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/Owners/Update | Uppdatera service princip ALS. Owners-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/ownedObjects/Read | Läsa service princip ALS. ownedObjects-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/behörigheter/uppdatera | Uppdatera service princip ALS. Permissions-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/policies/Read | Läs service princip ALS. policys-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/policies/Update | Uppdatera service princip ALS. policys-egenskapen i Azure Active Directory. |
| Microsoft. AAD. directorySync/upplånade/allTasks | Utföra alla åtgärder i Azure AD Connect. |

### <a name="directory-writers"></a>Katalogskrivare
Kan läsa & skriva grundläggande katalog information. För att bevilja åtkomst till program, som inte är avsedda för användare.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. AAD. Directory/grupper/skapa | Skapa grupper i Azure Active Directory. |
| Microsoft. AAD. Directory/Groups/createAsOwner | Skapa grupper i Azure Active Directory. Skaparen läggs till som den första ägaren och det antal skapade objektet mot skapare objekt kvoten 250 har skapats. |
| Microsoft. AAD. Directory/Groups/appRoleAssignments/Update | Uppdatera Groups. appRoleAssignments-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Groups/Basic/Update | Uppdatera grundläggande egenskaper för grupper i Azure Active Directory. |
| Microsoft. AAD. Directory/grupper/medlemmar/uppdatera | Uppdatera Groups. members-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/grupper/ägare/uppdatera | Uppdatera Groups. Owners-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/grupper/inställningar/uppdatera | Uppdatera Groups. Settings-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/groupSettings/Basic/Update | Uppdatera grundläggande egenskaper för groupSettings i Azure Active Directory. |
| Microsoft. AAD. Directory/groupSettings/Create | Skapa groupSettings i Azure Active Directory. |
| Microsoft. AAD. Directory/groupSettings/Delete | Ta bort groupSettings i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/appRoleAssignments/Update | Uppdatera users. appRoleAssignments-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/assignLicense | Hantera licenser för användare i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/Basic/Update | Uppdatera grundläggande egenskaper för användare i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/invalidateAllRefreshTokens | Ogiltig verifiering av alla användares uppdateringstoken i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/Manager/Update | Uppdatera users. Manager-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/userPrincipalName/Update | Uppdatera users. userPrincipalName-egenskapen i Azure Active Directory. |

### <a name="exchange-service-administrator"></a>Exchange Service-administratör
Kan hantera alla aspekter av Exchange-produkten.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i roll beskrivning ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. AAD. Directory/Groups/Unified/appRoleAssignments/Update | Uppdatera Groups. Unified-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Groups/Unified/Basic/Update | Uppdatera grundläggande egenskaper för Office 365-grupper. |
| Microsoft. AAD. Directory/Groups/Unified/Create | Skapa Office 365-grupper. |
| Microsoft. AAD. Directory/Groups/Unified/Delete | Ta bort Office 365-grupper. |
| Microsoft. AAD. Directory/Groups/Unified/members/Update | Uppdatera medlemskap för Office 365-grupper. |
| Microsoft. AAD. Directory/Groups/Unified/Owners/Update | Uppdatera ägarskap för Office 365-grupper. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera support biljetter för Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i Microsoft. Office365. Web-Portal. |
| microsoft.office365.exchange/allEntities/allTasks | Hantera alla aspekter av Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-support biljetter. |

### <a name="external-identity-provider-administrator"></a>Administratör för extern identitetsprovider
Konfigurera identitets leverantörer för användning i direkt Federation.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. AAD. B2C/Identityprovider/allTasks | Läsa och konfigurera identitets leverantörer i Azure Active Directory B2C. |

### <a name="guest-inviter"></a>Gästinbjudare
Kan bjuda in gäst användare oberoende av inställningen medlemmar kan bjuda in gäster.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. AAD. Directory/Users/appRoleAssignments/Read | Läsa users. appRoleAssignments-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/Basic/Read | Läsa grundläggande egenskaper för användare i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/directReports/Read | Läsa users. directReports-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/inviteGuest | Bjud in gäst användare i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/Manager/Read | Läsa users. Manager-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/memberOf/läsa | Läsa users. memberOf-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/oAuth2PermissionGrants/Basic/Read | Läsa users. oAuth2PermissionGrants-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/ownedDevices/Read | Läsa users. ownedDevices-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Läsa users. ownedObjects-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/registeredDevices/Read | Läsa users. registeredDevices-egenskapen i Azure Active Directory. |

### <a name="helpdesk-administrator"></a>Supportavdelningsadministratör
Kan återställa lösen ord för administratörer som inte har administratörer och supportavdelningen.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. AAD. Directory/Devices/bitLockerRecoveryKeys/Read | Läsa Devices. bitLockerRecoveryKeys-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/invalidateAllRefreshTokens | Ogiltig verifiering av alla användares uppdateringstoken i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/Password/Update | Uppdatera lösen ord för alla användare i Azure Active Directory. Mer information finns i onlinedokumentationen. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera support biljetter för Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i Microsoft. Office365. Web-Portal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-support biljetter. |

### <a name="intune-service-administrator"></a>Administratör för Intune-tjänsten
Kan hantera alla aspekter av Intune-produkten.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i roll beskrivning ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. AAD. Directory/Contacts/Basic/Update | Uppdatera grundläggande egenskaper för kontakter i Azure Active Directory. |
| Microsoft. AAD. Directory/Contacts/Create | Skapa kontakter i Azure Active Directory. |
| Microsoft. AAD. Directory/Contacts/Delete | Ta bort kontakter i Azure Active Directory. |
| Microsoft. AAD. Directory/Devices/Basic/Update | Uppdatera grundläggande egenskaper för enheter i Azure Active Directory. |
| Microsoft. AAD. Directory/Devices/bitLockerRecoveryKeys/Read | Läsa Devices. bitLockerRecoveryKeys-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Devices/Create | Skapa enheter i Azure Active Directory. |
| Microsoft. AAD. Directory/Devices/Delete | Ta bort enheter i Azure Active Directory. |
| Microsoft. AAD. Directory/Devices/registeredOwners/Update | Uppdatera Devices. registeredOwners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/update | Uppdatera Devices. registeredUsers-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Groups/appRoleAssignments/Update | Uppdatera Groups. appRoleAssignments-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Groups/Basic/Update | Uppdatera grundläggande egenskaper för grupper i Azure Active Directory. |
| Microsoft. AAD. Directory/grupper/skapa | Skapa grupper i Azure Active Directory. |
| Microsoft. AAD. Directory/Groups/createAsOwner | Skapa grupper i Azure Active Directory. Skaparen läggs till som den första ägaren och det antal skapade objektet mot skapare objekt kvoten 250 har skapats. |
| Microsoft. AAD. Directory/Groups/Delete | Ta bort grupper i Azure Active Directory. |
| Microsoft. AAD. Directory/Groups/hiddenMembers/Read | Läs Groups. hiddenMembers-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/grupper/medlemmar/uppdatera | Uppdatera Groups. members-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/grupper/ägare/uppdatera | Uppdatera Groups. Owners-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Groups/Restore | Återställ grupper i Azure Active Directory. |
| Microsoft. AAD. Directory/grupper/inställningar/uppdatera | Uppdatera Groups. Settings-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/appRoleAssignments/Update | Uppdatera users. appRoleAssignments-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/Basic/Update | Uppdatera grundläggande egenskaper för användare i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/Manager/Update | Uppdatera users. Manager-egenskapen i Azure Active Directory. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera support biljetter för Azure. |
| microsoft.intune/allEntities/allTasks | Hantera alla aspekter av Intune. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-support biljetter. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i Microsoft. Office365. Web-Portal. |

### <a name="kaizala-administrator"></a>Kaizala-administratör
Kan hantera inställningar för Microsoft-Kaizala.  

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i roll beskrivning ovan.
  >
  >  
  
| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-support biljetter. |
| microsoft.office365.webPortal/allEntities/basic/read | Läs Office 365 administrations Center. |

### <a name="license-administrator"></a>Licensadministratör
Kan hantera produkt licenser för användare och grupper.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. AAD. Directory/Users/assignLicense | Hantera licenser för användare i Azure Active Directory. |
| microsoft.aad.directory/users/usageLocation/update | Uppdatera users. usageLocation-egenskapen i Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i Microsoft. Office365. Web-Portal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läs och konfigurera Office 365 Service Health. |

### <a name="lync-service-administrator"></a>Lync-tjänstadministratör
Kan hantera alla aspekter av Skype för företag-produkten.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i roll beskrivning ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera support biljetter för Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i Microsoft. Office365. Web-Portal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Hantera alla aspekter av Skype för företag – online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-support biljetter. |

### <a name="message-center-privacy-reader"></a>Meddelande Center sekretess läsare
Kan läsa meddelande Center inlägg, data integritets meddelanden, grupper, domäner och prenumerationer.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i roll beskrivning ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i Microsoft. Office365. Web-Portal. |
| microsoft.office365.messageCenter/messages/read | Läs meddelanden i Microsoft. Office365. messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Läs securityMessages i Microsoft. Office365. messageCenter. |

### <a name="message-center-reader"></a>Meddelandecenterläsare
Kan endast läsa meddelanden och uppdateringar för sin organisation i meddelande Center för Office 365. 

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i roll beskrivning ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i Microsoft. Office365. Web-Portal. |
| microsoft.office365.messageCenter/messages/read | Läs meddelanden i Microsoft. Office365. messageCenter. |

### <a name="partner-tier1-support"></a>Partnersupport, nivå 1
Använd inte – inte avsedd för allmän användning.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i roll beskrivning ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. AAD. Directory/Contacts/Basic/Update | Uppdatera grundläggande egenskaper för kontakter i Azure Active Directory. |
| Microsoft. AAD. Directory/Contacts/Create | Skapa kontakter i Azure Active Directory. |
| Microsoft. AAD. Directory/Contacts/Delete | Ta bort kontakter i Azure Active Directory. |
| Microsoft. AAD. Directory/grupper/skapa | Skapa grupper i Azure Active Directory. |
| Microsoft. AAD. Directory/Groups/createAsOwner | Skapa grupper i Azure Active Directory. Skaparen läggs till som den första ägaren och det antal skapade objektet mot skapare objekt kvoten 250 har skapats. |
| Microsoft. AAD. Directory/grupper/medlemmar/uppdatera | Uppdatera Groups. members-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/grupper/ägare/uppdatera | Uppdatera Groups. Owners-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/appRoleAssignments/Update | Uppdatera users. appRoleAssignments-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/assignLicense | Hantera licenser för användare i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/Basic/Update | Uppdatera grundläggande egenskaper för användare i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/Delete | Ta bort användare i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/invalidateAllRefreshTokens | Ogiltig verifiering av alla användares uppdateringstoken i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/Manager/Update | Uppdatera users. Manager-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/Password/Update | Uppdatera lösen ord för alla användare i Azure Active Directory. Mer information finns i onlinedokumentationen. |
| Microsoft. AAD. Directory/Users/Restore | Återställ borttagna användare i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/userPrincipalName/Update | Uppdatera users. userPrincipalName-egenskapen i Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera support biljetter för Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i Microsoft. Office365. Web-Portal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-support biljetter. |

### <a name="partner-tier2-support"></a>Partnersupport, nivå 2
Använd inte – inte avsedd för allmän användning.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i roll beskrivning ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. AAD. Directory/Contacts/Basic/Update | Uppdatera grundläggande egenskaper för kontakter i Azure Active Directory. |
| Microsoft. AAD. Directory/Contacts/Create | Skapa kontakter i Azure Active Directory. |
| Microsoft. AAD. Directory/Contacts/Delete | Ta bort kontakter i Azure Active Directory. |
| Microsoft. AAD. Directory/Domains/allTasks | Skapa och ta bort domäner och läsa och uppdatera standard egenskaper i Azure Active Directory. |
| Microsoft. AAD. Directory/grupper/skapa | Skapa grupper i Azure Active Directory. |
| Microsoft. AAD. Directory/Groups/Delete | Ta bort grupper i Azure Active Directory. |
| Microsoft. AAD. Directory/grupper/medlemmar/uppdatera | Uppdatera Groups. members-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Groups/Restore | Återställ grupper i Azure Active Directory. |
| Microsoft. AAD. Directory/Organization/Basic/Update | Uppdatera grundläggande egenskaper för organisation i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/appRoleAssignments/Update | Uppdatera users. appRoleAssignments-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/assignLicense | Hantera licenser för användare i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/Basic/Update | Uppdatera grundläggande egenskaper för användare i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/Delete | Ta bort användare i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/invalidateAllRefreshTokens | Ogiltig verifiering av alla användares uppdateringstoken i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/Manager/Update | Uppdatera users. Manager-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/Password/Update | Uppdatera lösen ord för alla användare i Azure Active Directory. Mer information finns i onlinedokumentationen. |
| Microsoft. AAD. Directory/Users/Restore | Återställ borttagna användare i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/userPrincipalName/Update | Uppdatera users. userPrincipalName-egenskapen i Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera support biljetter för Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i Microsoft. Office365. Web-Portal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-support biljetter. |

### <a name="password-administrator"></a>Lösenordsadministratör
Kan återställa lösen ord för icke-administratörer och lösen ords administratörer.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. AAD. Directory/Users/Password/Update | Uppdatera lösen ord för alla användare i Azure Active Directory. Mer information finns i onlinedokumentationen. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i Microsoft. Office365. Web-Portal. |

### <a name="power-bi-service-administrator"></a>Power BI-tjänstadministratör
Kan hantera alla aspekter av den Power BI produkten.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i roll beskrivning ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera support biljetter för Azure. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Hantera alla aspekter av Power BI. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i Microsoft. Office365. Web-Portal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-support biljetter. |

### <a name="privileged-authentication-administrator"></a>Privilegierad autentiseringsadministratör
Tillåts Visa, ange och återställa information om autentiseringsmetod för alla användare (administratörer eller icke-administratörer).

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. AAD. Directory/Users/invalidateAllRefreshTokens | Ogiltig verifiering av alla användares uppdateringstoken i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/strongAuthentication/Update | Uppdatera egenskaper för stark autentisering som MFA-autentiseringsinformation. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera support biljetter för Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i Microsoft. Office365. Web-Portal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-support biljetter. |
| Microsoft. AAD. Directory/Users/Password/Update | Uppdatera lösen ord för alla användare i Office 365-organisationen. Mer information finns i onlinedokumentationen. |
### <a name="privileged-role-administrator"></a>Privilegierad rolladministratör
Kan hantera roll tilldelningar i Azure AD och alla aspekter av Privileged Identity Management.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i roll beskrivning ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera standard egenskaper i Microsoft. AAD. privilegedIdentityManagement. |
| Microsoft. AAD. Directory/Service princip ALS/appRoleAssignedTo/allTasks | Läsa och konfigurera service princip ALS. appRoleAssignedTo-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/allTasks | Läsa och konfigurera service princip ALS. oAuth2PermissionGrants-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/administrativeUnits/allProperties/allTasks | Skapa och hantera administrativa enheter (inklusive medlemmar) |
| Microsoft. AAD. Directory/roleAssignments/allProperties/allTasks | Skapa och hantera roll tilldelningar. |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Skapa och hantera roll definitioner. |

### <a name="reports-reader"></a>Rapportläsare
Kan läsa inloggnings-och gransknings rapporter.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i roll beskrivning ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. AAD. Directory/auditLogs/allProperties/Read | Läs alla egenskaper (inklusive privilegierade egenskaper) på auditLogs i Azure Active Directory. |
| Microsoft. AAD. Directory/signInReports/allProperties/Read | Läs alla egenskaper (inklusive privilegierade egenskaper) på signInReports i Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.office365.usageReports/allEntities/read | Läs Office 365-användnings rapporter. |

### <a name="search-administrator"></a>Sök administratör
Kan skapa och hantera alla aspekter av Microsofts Sök inställningar.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i roll beskrivning ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Läs meddelanden i Microsoft. Office365. messageCenter. |
| microsoft.office365.search/allEntities/allProperties/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera alla egenskaper i Microsoft. Office365. search. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-support biljetter. |
| microsoft.office365.usageReports/allEntities/read | Läs Office 365-användnings rapporter. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i Microsoft. Office365. Web-Portal. |

### <a name="search-editor"></a>Sök redigerare
Kan skapa och hantera redaktionellt innehåll, till exempel bok märken, Q och as, locations, floorplan.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i roll beskrivning ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Läs meddelanden i Microsoft. Office365. messageCenter. |
| microsoft.office365.search/content/allProperties/allTasks | Skapa och ta bort innehåll och läsa och uppdatera alla egenskaper i Microsoft. Office365. search. |
| microsoft.office365.usageReports/allEntities/read | Läs Office 365-användnings rapporter. |

### <a name="security-administrator"></a>Säkerhetsadministratör
Kan läsa säkerhets information och rapporter och hantera konfigurationen i Azure AD och Office 365.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i roll beskrivning ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. AAD. Directory/Applications/policies/Update | Uppdatera Applications. policys-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/auditLogs/allProperties/Read | Läs alla egenskaper (inklusive privilegierade egenskaper) på auditLogs i Azure Active Directory. |
| Microsoft. AAD. Directory/Devices/bitLockerRecoveryKeys/Read | Läsa Devices. bitLockerRecoveryKeys-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/policies/Basic/Update | Uppdatera grundläggande egenskaper för principer i Azure Active Directory. |
| Microsoft. AAD. Directory/policies/Create | Skapa principer i Azure Active Directory. |
| Microsoft. AAD. Directory/policies/Delete | Ta bort principer i Azure Active Directory. |
| Microsoft. AAD. Directory/policys/Owners/Update | Uppdatera policys. Owners-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/policys/tenantDefault/Update | Uppdatera policys. tenantDefault-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Service princip ALS/policies/Update | Uppdatera service princip ALS. policys-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/signInReports/allProperties/Read | Läs alla egenskaper (inklusive privilegierade egenskaper) på signInReports i Azure Active Directory. |
| Microsoft. AAD. identityProtection/upplånare/Läs | Läs alla resurser i Microsoft. AAD. identityProtection. |
| microsoft.aad.identityProtection/allEntities/update | Uppdatera alla resurser i Microsoft. AAD. identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Läs alla resurser i Microsoft. AAD. privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i Microsoft. Office365. Web-Portal. |
| microsoft.office365.protectionCenter/allEntities/read | Läs alla aspekter av Office 365 Protection Center. |
| microsoft.office365.protectionCenter/allEntities/update | Uppdatera alla resurser i Microsoft. Office365. protectionCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läs och konfigurera Office 365 Service Health. |

### <a name="security-operator"></a>Säkerhets operatör
Skapar och hanterar säkerhets händelser.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i roll beskrivning ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Läsa och konfigurera Microsoft Cloud App Security. |
| Microsoft. AAD. identityProtection/upplånare/Läs | Läs alla resurser i Microsoft. AAD. identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Läs alla resurser i Microsoft. AAD. privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Läsa och konfigurera Azure AD Advanced Threat Protection. |
| microsoft.intune/allEntities/allTasks | Hantera alla aspekter av Intune. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Läsa och konfigurera Säkerhets-och efterlevnadscenter. |
| microsoft.office365.usageReports/allEntities/read | Läs Office 365-användnings rapporter. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Läsa och konfigurera Windows Defender Avancerat skydd. |

### <a name="security-reader"></a>Säkerhetsläsare
Kan läsa säkerhets information och rapporter i Azure AD och Office 365.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i roll beskrivning ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. AAD. Directory/auditLogs/allProperties/Read | Läs alla egenskaper (inklusive privilegierade egenskaper) på auditLogs i Azure Active Directory. |
| Microsoft. AAD. Directory/Devices/bitLockerRecoveryKeys/Read | Läsa Devices. bitLockerRecoveryKeys-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/signInReports/allProperties/Read | Läs alla egenskaper (inklusive privilegierade egenskaper) på signInReports i Azure Active Directory. |
| Microsoft. AAD. identityProtection/upplånare/Läs | Läs alla resurser i Microsoft. AAD. identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Läs alla resurser i Microsoft. AAD. privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i Microsoft. Office365. Web-Portal. |
| microsoft.office365.protectionCenter/allEntities/read | Läs alla aspekter av Office 365 Protection Center. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läs och konfigurera Office 365 Service Health. |

### <a name="service-support-administrator"></a>Tjänstesupport-administratör
Kan läsa information om tjänst hälsa och hantera support ärenden.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i roll beskrivning ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera support biljetter för Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i Microsoft. Office365. Web-Portal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-support biljetter. |

### <a name="sharepoint-service-administrator"></a>SharePoint-tjänstadministratör
Kan hantera alla aspekter av SharePoint-tjänsten.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i roll beskrivning ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. AAD. Directory/Groups/Unified/appRoleAssignments/Update | Uppdatera Groups. Unified-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Groups/Unified/Basic/Update | Uppdatera grundläggande egenskaper för Office 365-grupper. |
| Microsoft. AAD. Directory/Groups/Unified/Create | Skapa Office 365-grupper. |
| Microsoft. AAD. Directory/Groups/Unified/Delete | Ta bort Office 365-grupper. |
| Microsoft. AAD. Directory/Groups/Unified/members/Update | Uppdatera medlemskap för Office 365-grupper. |
| Microsoft. AAD. Directory/Groups/Unified/Owners/Update | Uppdatera ägarskap för Office 365-grupper. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera support biljetter för Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i Microsoft. Office365. Web-Portal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.sharepoint/allEntities/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera standard egenskaper i Microsoft. Office365. SharePoint. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-support biljetter. |

### <a name="teams-communications-administrator"></a>Teams Communications-administratör
Kan hantera uppringnings-och mötes funktioner i Microsoft Teams-tjänsten.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i roll beskrivning ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera support biljetter för Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i Microsoft. Office365. Web-Portal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-support biljetter. |
| microsoft.office365.usageReports/allEntities/read | Läs Office 365-användnings rapporter. |

### <a name="teams-communications-support-engineer"></a>Teams Communications-supporttekniker
Kan felsöka kommunikations problem inom team med hjälp av avancerade verktyg.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i roll beskrivning ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i Microsoft. Office365. Web-Portal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läs och konfigurera Office 365 Service Health. |

### <a name="teams-communications-support-specialist"></a>Teams Communications-supportspecialist
Kan felsöka kommunikations problem inom team med hjälp av grundläggande verktyg.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i roll beskrivning ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i Microsoft. Office365. Web-Portal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läs och konfigurera Office 365 Service Health. |

### <a name="teams-service-administrator"></a>Teams-tjänstadministratör
Kan hantera Microsoft Teams-tjänsten.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i roll beskrivning ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. AAD. Directory/Groups/hiddenMembers/Read | Läs Groups. hiddenMembers-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Groups/Unified/appRoleAssignments/Update | Uppdatera Groups. Unified-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Groups/Unified/Basic/Update | Uppdatera grundläggande egenskaper för Office 365-grupper. |
| Microsoft. AAD. Directory/Groups/Unified/Create | Skapa Office 365-grupper. |
| Microsoft. AAD. Directory/Groups/Unified/Delete | Ta bort Office 365-grupper. |
| Microsoft. AAD. Directory/Groups/Unified/members/Update | Uppdatera medlemskap för Office 365-grupper. |
| Microsoft. AAD. Directory/Groups/Unified/Owners/Update | Uppdatera ägarskap för Office 365-grupper. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera support biljetter för Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i Microsoft. Office365. Web-Portal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-support biljetter. |
| microsoft.office365.usageReports/allEntities/read | Läs Office 365-användnings rapporter. |

### <a name="user-administrator"></a>Användaradministratör
Kan hantera alla aspekter av användare och grupper, inklusive att återställa lösen ord för begränsade administratörer.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. AAD. Directory/appRoleAssignments/Create | Skapa appRoleAssignments i Azure Active Directory. |
| Microsoft. AAD. Directory/appRoleAssignments/Delete | Ta bort appRoleAssignments i Azure Active Directory. |
| Microsoft. AAD. Directory/appRoleAssignments/Update | Uppdatera appRoleAssignments i Azure Active Directory. |
| Microsoft. AAD. Directory/Contacts/Basic/Update | Uppdatera grundläggande egenskaper för kontakter i Azure Active Directory. |
| Microsoft. AAD. Directory/Contacts/Create | Skapa kontakter i Azure Active Directory. |
| Microsoft. AAD. Directory/Contacts/Delete | Ta bort kontakter i Azure Active Directory. |
| Microsoft. AAD. Directory/Groups/appRoleAssignments/Update | Uppdatera Groups. appRoleAssignments-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Groups/Basic/Update | Uppdatera grundläggande egenskaper för grupper i Azure Active Directory. |
| Microsoft. AAD. Directory/grupper/skapa | Skapa grupper i Azure Active Directory. |
| Microsoft. AAD. Directory/Groups/createAsOwner | Skapa grupper i Azure Active Directory. Skaparen läggs till som den första ägaren och det antal skapade objektet mot skapare objekt kvoten 250 har skapats. |
| Microsoft. AAD. Directory/Groups/Delete | Ta bort grupper i Azure Active Directory. |
| Microsoft. AAD. Directory/Groups/hiddenMembers/Read | Läs Groups. hiddenMembers-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/grupper/medlemmar/uppdatera | Uppdatera Groups. members-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/grupper/ägare/uppdatera | Uppdatera Groups. Owners-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Groups/Restore | Återställ grupper i Azure Active Directory. |
| Microsoft. AAD. Directory/grupper/inställningar/uppdatera | Uppdatera Groups. Settings-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/appRoleAssignments/Update | Uppdatera users. appRoleAssignments-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/assignLicense | Hantera licenser för användare i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/Basic/Update | Uppdatera grundläggande egenskaper för användare i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/Create | Skapa användare i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/Delete | Ta bort användare i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/invalidateAllRefreshTokens | Ogiltig verifiering av alla användares uppdateringstoken i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/Manager/Update | Uppdatera users. Manager-egenskapen i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/Password/Update | Uppdatera lösen ord för alla användare i Azure Active Directory. Mer information finns i onlinedokumentationen. |
| Microsoft. AAD. Directory/Users/Restore | Återställ borttagna användare i Azure Active Directory. |
| Microsoft. AAD. Directory/Users/userPrincipalName/Update | Uppdatera users. userPrincipalName-egenskapen i Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera support biljetter för Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i Microsoft. Office365. Web-Portal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läs och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-support biljetter. |

## <a name="role-template-ids"></a>Mall-ID: n

Roll mal len ID används huvudsakligen av Graph API-eller PowerShell-användare.

Graph-displayName | Visnings namn för Azure Portal | directoryRoleTemplateId
----------------- | ------------------------- | -------------------------
Programadministratör | Programadministratör | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
Programutvecklare | Programutvecklare | CF1C38E5-3621-4004-A7CB-879624DCED7C
Autentiseringsadministratör | Autentiseringsadministratör | c4e39bd9-1100-46d3-8c65-fb160da0071f
Azure Information Protection administratör | Azure Information Protection administratör | 7495fdc4-34c4-4d15-a289-98788ce399fd
B2C användar flödes administratör | B2C användar flödes administratör | 6e591065-9bad-43ed-90f3-e9424366d2f0
B2C User Flow Attribute Administrator | B2C User Flow Attribute Administrator | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
B2C IEF Keyset Administrator | B2C IEF Keyset Administrator | aaf43236-0c0d-4d5f-883a-6955382ac081
B2C IEF Policy Administrator | B2C IEF Policy Administrator | 3edaf663-341e-4475-9f94-5c398ef6c070
Faktureringsadministratör | Faktureringsadministratör | b0f54661-2d74-4c50-afa3-1ec803f12efe
Molnprogramadministratör | Molnprogramadministratör | 158c047a-c907-4556-b7ef-446551a6b5f7
Molnenhetsadministratör | Molnenhetsadministratör | 7698a772-787b-4ac8-901f-60d6b08affd2
Företagsadministratör | Global administratör | 62e90394-69f5-4237-9190-012177145e10
Efterlevnadsadministratör | Efterlevnadsadministratör | 17315797-102d-40b4-93e0-432062caca18
Data administratör för efterlevnad | Data administratör för efterlevnad | e6d1a23a-da11-4be4-9570-befc86d067a7
Administratör för villkorsstyrd åtkomst | Administratör av villkorsstyrd åtkomst | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
Tjänstadministratör för CRM | Dynamics 365-administratör | 44367163-eba1-44c3-98af-f5787879f96a
Godkännare av åtkomst till Customer LockBox | Customer Lockbox åtkomst god kännare | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
Desktop Analytics-administratör | Desktop Analytics-administratör | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
Enhetsadministratörer | Enhets administratörer | 9f06204d-73c1-4d4c-880a-6edb90606fd8
Enhets anslutning | Enhets anslutning | 9c094953-4995-41c8-84c8-3ebb9b32c93f
Enhetshanterare | Enhets hanterare | 2b499bcd-da44-4968-8aec-78e1674fa64d
Enhets användare | Enhets användare | d405c6df-0af8-4e3b-95e4-4d06e542189e
Katalogläsare | Katalogläsare | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
Konton för katalog synkronisering | Katalogsynkroniseringskonton | d29b2b05-8046-44ba-8758-1e26182fcf32
Katalogskrivare | Katalogskrivare | 9360feb5-f418-4baa-8175-e2a00bac4301
Exchange Service-administratör | Exchange-administratör | 29232cdf-9323-42fd-ade2-1d097af3e4de
Administratör för extern identitetsprovider | Administratör för extern identitetsprovider | be2f45a1-457d-42af-a067-6ec1fa63bc45
Gästinbjudare | Gästinbjudare | 95e79109-95c0-4d8e-aee3-d01accf2d47b
Supportavdelningsadministratör | Lösenordsadminitratör | 729827e3-9c14-49f7-bb1b-9608f156bbb8
Administratör för Intune-tjänsten | Intune-administratör | 3a2c62db-5318-420d-8d74-23affee5d9d5
Kaizala-administratör | Kaizala-administratör | 74ef975b-6605-40af-a5d2-b9539d836353
Licensadministratör | Licensadministratör | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Lync-tjänstadministratör | Skype for Business-administratör | 75941009-915a-4869-abe7-691bff18279e
Meddelande Center sekretess läsare | Meddelande Center sekretess läsare | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
Meddelandecenterläsare | Meddelandecenterläsare | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
Partnersupport, nivå 1 | Partnersupport, nivå 1 | 4ba39ca4-527c-499a-b93d-d9b492c50246
Partnersupport, nivå 2 | Partnersupport, nivå 2 | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
Lösenordsadministratör | Lösenordsadminitratör | 966707d0-3269-4727-9be2-8c3a10f19b9d
Power BI-tjänstadministratör | Power BI-administratör | a9ea8996-122f-4c74-9520-8edcd192826c
Privilegierad autentiseringsadministratör | Privilegierad autentiseringsadministratör | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
Privilegierad rolladministratör | Administratör för privilegierad roll | e8611ab8-c189-46e8-94e1-60213ab1f814
Rapportläsare | Rapportläsare | 4a5d8f65-41da-4de4-8968-e035b65339cf
Sök administratör | Sök administratör | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
Sök redigerare | Sök redigerare | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
Säkerhetsadministratör | Säkerhetsadministratör | 194ae4cb-b126-40b2-bd5b-6091b380977d
Säkerhets operatör | Säkerhets operatör | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f
Säkerhetsläsare | Säkerhetsläsare | 5d6b6bb7-de71-4623-b4af-96380a352509
Tjänstesupport-administratör | Tjänstadministratör | f023fd81-a637-4b56-95fd-791ac0226033
SharePoint-tjänstadministratör | SharePoint-administratör | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Teams Communications-administratör | Teams Communications-administratör | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Teams Communications-supporttekniker | Teams Communications-supporttekniker | f70938a0-fc10-4177-9e90-2178f8765737
Teams Communications-supportspecialist | Teams Communications-supportspecialist | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Teams-tjänstadministratör | Teams-tjänstadministratör | 69091246-20e8-4a56-aa4d-066075b2a7a8
Användare | Användare | a0b1b346-4d3e-4e8b-98f8-753987be4970
Användarkonto-administratör | Användaradministratör | fe930be7-5e62-47db-91af-98c3a49a38b1
Arbetsplatsenhetskoppling | Anslutning till arbets plats enhet | c34f683f-4d5a-4403-affd-6615e00e3a7f


## <a name="deprecated-roles"></a>Föråldrade roller

Följande roller ska inte användas. De är inaktuella och kommer att tas bort från Azure AD i framtiden.

* AdHoc-licensadministratör
* Enhets anslutning
* Enhetshanterare
* Enhets användare
* Skapare av e-postverifierad användare
* Postlådeadministratör
* Arbetsplatsenhetskoppling

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du tilldelar en användare som administratör för en Azure-prenumeration finns i [Hantera åtkomst med RBAC och Azure Portal](../../role-based-access-control/role-assignments-portal.md)
* Mer information om hur resursåtkomsten hanteras i Microsoft Azure finns i [Förstå resursåtkomst i Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Mer information om hur Azure Active Directory relaterar till din Azure-prenumeration finns i [Hur Azure-prenumerationer är associerade med Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
