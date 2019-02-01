---
title: Bästa praxis för säker administratörsåtkomst - Azure Active Directory | Microsoft Docs
description: Se till att din organisations administrativa åtkomst och admin-konton är säkra. Systemarkitekter och IT-proffs som konfigurerar Azure AD, Azure och Microsoft Online Services.
services: active-directory
keywords: ''
author: curtand
manager: mtillman
ms.author: curtand
ms.date: 01/31/2019
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.custom: it-pro
ms.reviewer: martincoetzer; MarkMorow
ms.openlocfilehash: 327ea929b5825abb448cb13b1bd0cbfa5681521c
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55512507"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Skydda privilegierad åtkomst för hybrid- och distributioner i Azure AD

Säkerheten för de flesta eller samtliga företagstillgångar i modern organisation beror på integriteten hos de Privilegierade konton som administrerar och hanterar IT-system. Skadliga aktörer ofta inklusive cyberangripare riktar sig mot administratörskonton och andra element för privilegierad åtkomst att snabbt få tillgång till känsliga data och system med hjälp av stöldattacker av autentiseringsuppgifter. Cloud finns services, skydd och svar i gemensamma ansvaret för molntjänstleverantören och kunden. Mer information om de senaste hoten till slutpunkter och molnet finns i den [Microsoft Security Intelligence Report](https://www.microsoft.com/security/operations/security-intelligence-report). Den här artikeln kan hjälpa dig att utveckla en handlingsplan mot kunskapsluckor mellan dina aktuella planer och de riktlinjer som beskrivs här.

> [!NOTE] 
> Microsoft strävar efter att de högsta nivåerna av förtroende, transparens, standardefterlevnad och regelefterlevnad. Läs mer om hur Microsofts team för global incidenthantering minimera effekterna av att attacker mot molntjänster och hur säkerhet är inbyggt i Microsoft-produkter och molntjänster på [Microsoft Trust Center - Security](https://www.microsoft.com/trustcenter/security)och mål för Microsoft-efterlevnad på [Microsoft Trust Center - efterlevnad](https://www.microsoft.com/trustcenter/compliance).

<!--## Risk management, incident response, and recovery preparation

A cyber-attack, if successful, can shut down operations not just for a few hours, but in some cases for days or even weeks. The collateral damage, such as legal ramifications, information leaks, and media coverage, could potentially continue for years. To ensure effective company-wide risk containment, cybersecurity and IT pros must align their response and recovery processes. To reduce the risk of business disruption due to a cyber-attack, industry experts recommend you do the following:

* As part of your risk management operations, establish a crisis management team for your organization that is responsible for managing all types of business disruptions.

* Compare your current risk mitigations, incident response, and recovery plan with industry best practices for managing a business disruption before, during, and after a cyber-attack.

* Develop and implement a roadmap for closing the gaps between your current plans and the best practices described in this document.


## Securing privileged access for hybrid and cloud deployments

does the article really start here?-->
För de flesta organisationer beror säkerheten för företagstillgångar på integriteten hos de Privilegierade konton som administrerar och hanterar IT-system. Cyberangripare fokuserar på privilegierad åtkomst till infrastruktursystem (till exempel Active Directory och Azure Active Directory) för att få åtkomst till en organisations känsliga data. 

Traditionella metoder som fokuserar på att skydda ingångs- och avsluta punkterna i ett nätverk som primär säkerhetsperimeter är mindre effektiva på grund av ökning av användningen av SaaS-appar och personliga enheter på Internet. Ersättningar för nätverkets säkerhetsperimeter i ett komplext modernt företag är kontroller för autentisering och auktorisering i en organisations identitetslager. 

Privilegierade administrativa konton har effektivt hand av den här nya ”säkerhetsperimetern”. Det är viktigt att skydda privilegierad åtkomst, oavsett om miljön är på plats, i molnet eller lokala och värdbaserade molntjänster. Skydda administrativ åtkomst från envisa angripare måste du utföra en fullständig och Intelligent metod för att isolera din organisations systemen från risker. 

Skydda privilegierad åtkomst kräver ändringar
* Processer och administrativ praxis kunskapshantering
* Tekniska komponenter som värdförsvar, kontoskydd och Identitetshantering

Det här dokumentet fokuserar främst på skapar en översikt över att skydda identiteter och åtkomst till som hanteras eller rapporterades i Azure AD, Microsoft Azure, Office 365 och andra molntjänster. För organisationer som har lokala administrativa konton, se anvisningar för både lokalt och hybrid privilegierad åtkomst hanteras från Active Directory på [skydda privilegierad åtkomst](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access). 

> [!NOTE] 
> Riktlinjerna i den här artikeln gäller främst funktioner i Azure Active Directory som ingår i Azure Active Directory Premium-planer P1 och P2. Azure Active Directory Premium P2 ingår i EMS E5 suite och Microsoft 365 E5 suite. Dessa riktlinjer förutsätter att din organisation redan har Azure AD Premium P2-licenser för dina användare. Om du inte har dessa licenser, kanske vissa av riktlinjer som inte gäller för din organisation. I den här artikeln är termen global administratör (eller global administratör) också synonyma med ”företagets administratör” eller ”Innehavaradministratör”.

## <a name="develop-a-roadmap"></a>Utveckla en översikt 

Microsoft rekommenderar att du utvecklar och följer en översikt över för att skydda privilegierad åtkomst från cyberhot angripare. Du kan alltid ändra ditt översikten för att hantera dina befintliga funktioner och särskilda krav i din organisation. Varje steg av översikten bör öka kostnaderna och svårigheten för motståndare att angripa privilegierad åtkomst till dina lokala, moln och hybrid-tillgångar. Microsoft rekommenderar följande fyra översikten faser: Det här rekommenderas översikten scheman de mest effektiva och snabbaste implementeringarna först, baserat på Microsofts upplevelser med cyberattackkedjan incident och svaret implementering. Tidslinjerna för den här översikten är ungefärliga.

![Faser i översikten med tiden rader](./media/directory-admin-roles-secure/roadmap-timeline.png)

* Steg 1 (24 – 48 timmar): Kritiska objekt som vi rekommenderar att du gör direkt

* Steg 2 (2 – 4 veckor): Minimera de mest använda attackteknikerna

* Steg 3 (1 – 3 månader): Skapa synlighet och skapa fullständig kontroll av administratörsaktivitet

* Steg 4 (sex månader och senare): Fortsätta skapa försvar för att ytterligare skydda din säkerhet-plattform

Översikt över ramverket har utformats för att maximera användningen av Microsoft-tekniker som du kanske redan har distribuerat. Du kan också dra nytta av viktiga aktuella och kommande säkerhetstekniker och integrera säkerhetsverktyg från andra leverantörer som du redan har distribuerat eller planerar distribution. 

## <a name="stage-1-critical-items-that-we-recommend-you-do-right-away"></a>Steg 1: Kritiska objekt som vi rekommenderar att du gör direkt

![Fas 1](./media/directory-admin-roles-secure/stage-one.png)

Steg 1 i översikten fokuserar på viktiga uppgifter som är snabb och enkel att implementera. Vi rekommenderar att du gör dessa några objekt direkt inom de första 24 – 48 timmarna att se till att en grundläggande nivå av säker privilegierad åtkomst. Det här steget av säkerhetsöversikten över säker privilegierad åtkomst innehåller följande åtgärder:

### <a name="general-preparation"></a>Allmän förberedelse

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Aktivera Azure AD Privileged Identity Management

Om du inte redan aktiverat Azure AD Privileged Identity Management (PIM) kan du göra det i din produktionsklient. När du har aktiverat Privileged Identity Management kan du ett meddelande om e-postmeddelanden för privilegierad åtkomst rollen ändringar. Dessa meddelanden ge en tidig varning när ytterligare användare läggs till i mycket Privilegierade roller i din katalog.

Azure AD Privileged Identity Management ingår i Azure AD Premium P2- eller EMS E5. Dessa lösningar hjälper dig att skydda åtkomst till program och resurser i den lokala miljön och i molnet. Om du inte redan har Azure AD Premium P2- eller EMS E5 och vill utvärdera flera av de funktioner som refereras till i den här översikten, registrera dig för den [Enterprise Mobility + Security kostnadsfri 90-dagars utvärderingsversion](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial). Använd dessa utvärderingsversioner licens för att prova Azure AD Privileged Identity Management och Azure AD Identity Protection för att övervaka aktiviteten med Azure AD avancerad säkerhetsrapportering, granskning och aviseringar.

När du har aktiverat Azure AD Privileged Identity Management:

1. Logga in på den [Azure-portalen](https://portal.azure.com/) med ett konto som är en global administratör för din produktionsklient.

2. För att välja den klient där du vill använda Privileged Identity Management, väljer du ditt användarnamn i det övre högra hörnet i Azure Portal.

3. Välj **alla tjänster** och filtrera listan för **Azure AD Privileged Identity Management**.

4. Öppna Privileged Identity Management från den **alla tjänster** listan och fästa den på instrumentpanelen.

Den första personen som använder Azure AD Privileged Identity Management i din klient tilldelas automatiskt den **säkerhetsadministratör** och **privilegierad rolladministratör** roller i klienten. Endast privilegierade rolladministratörer kan hantera rolltilldelningar för Azure AD-katalog för användare. När du lägger till Azure AD Privileged Identity Management, visas som dessutom säkerhetsguiden som vägleder dig genom den första upplevelsen för identifiering och tilldelning. Du kan avsluta guiden utan att göra några ytterligare just nu. 

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Identifiera och klassificera konton som är mycket Privilegierade roller 

Visa användare som ingår i directory rollerna Global administratör, privilegierad rolladministratör, administratör i Exchange Online och SharePoint Online-administratör när du har aktiverat på Azure AD Privileged Identity Management. Om du inte har Azure AD PIM i din klientorganisation, kan du använda den [PowerShell API](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0). Börja med rollen som global administratör när den här rollen är generisk: en användare som har tilldelats den här administratörsrollen har samma behörigheter i alla molntjänster som din organisation prenumererar, oavsett om de har tilldelats den här rollen i Office 365-portalen , Azure portal, eller genom att använda Azure AD-modulen för Microsoft PowerShell. 

Ta bort alla konton som inte längre behövs i dessa roller. Kategorisera sedan de återstående konton som tilldelas till administrativa roller:

* Tilldelas individuellt till administrativa användare och kan också användas för icke-administrativa syften (till exempel personliga e-post)
* Individuellt tilldelade till administrativa användare och avsedda för administrativa syften
* Delas mellan flera användare
* För glas åtkomst vid akutfall scenarier
* För automatiserade skript
* För externa användare

#### <a name="define-at-least-two-emergency-access-accounts"></a>Definiera minst två konton för åtkomst vid akutfall 

Se till att du inte får i en situation där de kan oavsiktligt utelåst från administrationen av Azure AD-klienten på grund av att det inte går att logga in eller aktivera en befintlig enskilda användares konto som administratör. Exempelvis om organisationen är federerat till en lokal identitetsprovider vara som identitetsprovider otillgänglig så att användare inte kan logga in lokalt. Du kan minimera effekten av oavsiktliga bristande administrativ åtkomst genom att lagra två eller flera konton för åtkomst vid akutfall i din klient.

För åtkomst vid akutfall hjälpa organisationer begränsa privilegierad åtkomst i en befintlig Azure Active Directory-miljö. Dessa konton är mycket Privilegierade och tilldelas inte till enskilda individer. För åtkomst vid akutfall är begränsade till nödfall för ”Bryt om” scenarion där normala administrativa konton inte kan användas. Organisationer måste i syfte att kontrollera och minska nödfall Kontoanvändning till endast den tid som det är nödvändigt. 

Utvärdera de konton som är tilldelade eller berättigad för rollen som global administratör. Om du inte ser någon endast molnbaserade användarkonton med den *. onmicrosoft.com-domän (avsett för ”Bryt om” åtkomst vid akutfall), skapa dem. Mer information finns i [hantera åtkomst vid akutfall administratörskonton i Azure AD](directory-emergency-access.md).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Aktivera multifaktorautentisering och registrera alla andra med höga privilegier enanvändarläge icke-federerade administratörskonton 

Kräv Azure Multi-Factor Authentication (MFA) vid inloggning för alla enskilda användare som tilldelas permanent till en eller flera av Azure AD-administratörsroller: Global administratör, privilegierad rolladministratör, administratör i Exchange Online och SharePoint Online-administratör. Använd guiden för att aktivera [Multi-Factor Authentication (MFA) för dina administratörskonton](../authentication/howto-mfa-userstates.md) och se till att alla användare har registrerat hos [ https://aka.ms/mfasetup ](https://aka.ms/mfasetup). Mer information finns under steg 2 och 3 i guiden [skydda åtkomsten till data och tjänster i Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e). 

## <a name="stage-2-mitigate-the-most-frequently-used-attack-techniques"></a>Steg 2: Minimera de mest använda attackteknikerna

![Steg 2](./media/directory-admin-roles-secure/stage-two.png)

Steg 2 i översikten fokuserar på att undvika de ofta används attackteknikerna för stöld och missbruk och kan implementeras på cirka 2 – 4 veckor. Det här steget av säkerhetsöversikten över säker privilegierad åtkomst innehåller följande åtgärder.

### <a name="general-preparation"></a>Allmän förberedelse

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>Utför en förteckning över tjänster, ägare och administratörer

Ökade bring-your-own device (BYOD) och arbete från startsidan principer och tillväxt av trådlös anslutning i företag är det viktigt att du övervakar som ansluter till nätverket. En granskningslogg för effektiva säkerhetsgrupper visar ofta enheter, program och program som körs på nätverket som inte stöds av IT-avdelningen, och därför potentiellt osäkert. Mer information finns i [Azure säkerhetshantering och översikt över](../../security/security-management-and-monitoring-overview.md). Se till att du inkluderar alla av följande uppgifter i din inventering. 

* Identifiera de användare som har administrativa roller och tjänster där de kan hantera.
* Använda Azure AD PIM för att ta reda på vilka användare i din organisation som har administratörsåtkomst till Azure AD, inklusive ytterligare roller utöver de som anges i steg 1.
* Utöver de roller som definierats i Azure AD, Office 365 som levereras med en uppsättning administrativa roller som du kan tilldela till användare i din organisation. Varje administratörsroll mappas till vanliga företagsfunktioner, och ger personer i din organisation behörighet att utföra specifika uppgifter i administrationscentret för Office 365. Använda administrationscentret för Office för att ta reda på vilka användare i din organisation som har administratörsåtkomst till Office 365, inklusive via roller som inte hanteras i Azure AD. Mer information finns i [om Office 365-administratörsroller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) och [rekommenderade säkerhetsmetoder för Office 365](https://support.office.com/article/Security-best-practices-for-Office-365-9295e396-e53d-49b9-ae9b-0b5828cdedc3).
* Utför inventeringen i andra tjänster som din organisation använder, till exempel Azure, Intune eller Dynamics 365.
* Kontrollera att dina administratörskonton (konton som används för administration, inte bara dagliga användarkonton) ha fungerande e-postadresser som är kopplade till dem och har registrerat dig för Azure MFA, eller använda MFA lokalt.
* Fråga om deras motivering för administrativ åtkomst.
* Ta bort administratörsåtkomst för de enskilda användare och tjänster som inte behövs.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Identifiera Microsoft-konton i administrativa roller som måste stängas för att arbets- eller skolkonton 

Ibland kan återanvända första globala administratörer för en organisation autentiseringsuppgifterna för deras befintliga Microsoft-konto när de började använda Azure AD. Dessa Microsoft-konton ska ersättas av enskilda konton för molnbaserad eller synkroniserade. 

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Se till att separata användarkonton och e-post vidarebefordran för globala administratörskonton 

Globala administratörskonton får inte ha personliga e-postadresser eftersom personliga e-postkonton regelbundet phished av cyberhot angripare. För att avgränsa internetrisker (nätfiskeattacker, oavsiktlig webbsurfning) från administrativa privilegier, skapar du dedikerade konton för varje användare med administratörsbehörighet. 

Om du inte redan har gjort det, kan du skapa separata konton för användare att utföra aktiviteter för global administratör, och kontrollera att de inte oavsiktligt öppna e-postmeddelanden eller köra program som är associerad med deras administratörskonton. Tänk på dessa konton har sin e-post som vidarebefordras till en aktiv postlåda.  

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Kontrollera att lösenorden för administrativa konton nyligen har ändrat

Kontrollera att alla användare har loggat in på sina administrativa konton och ändra sina lösenord på minst en gång under de senaste 90 dagarna. Kontrollera också som alla delade konton där flera användare känna till lösenordet har fått sina lösenord som nyligen ändrats.

#### <a name="turn-on-password-hash-synchronization"></a>Aktivera synkronisering av lösenordshash

Synkronisering av lösenordshash är en funktion som används för att synkronisera hashvärden av användarlösenord från en lokal Active Directory-instans för en molnbaserad Azure AD-instans. Även om du vill använda federation med Active Directory Federation Services (AD FS) eller andra identitetsleverantörer, kan du också konfigurera synkronisering av lösenordshash som en säkerhetskopia i fall din lokala infrastruktur, till exempel AD eller AD FS-servrar misslyckas eller blir tillfälligt otillgänglig. Detta gör det möjligt för användare att logga in på tjänsten med hjälp av samma lösenord som de använder för att logga in på sina lokala AD-instans. Det kan också, Identity Protection att identifiera avslöjade autentiseringsuppgifter genom att jämföra dessa hashvärden för lösenord med lösenord som är kända för äventyras, om en användare har, med sitt samma e-postadress och lösenord för andra tjänster som inte är anslutna till Azure AD.  Mer information finns i [implementera lösenordshashsynkronisering med Azure AD Connect-synkronisering](../hybrid/how-to-connect-password-hash-synchronization.md).

#### <a name="require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users"></a>Kräv multifaktorautentisering (MFA) för användare i alla Privilegierade roller samt exponerade användare

Azure AD rekommenderar att du kräver multifaktorautentisering (MFA) för alla användare, administratörer och andra användare som skulle ha en betydande inverkan om deras konto har komprometterats (till exempel finansiella införlivande). Detta minskar risken för angrepp på grund av ett olämpligt lösenord.

Aktivera:

* [MFA för konton med hög exponering](../authentication/multi-factor-authentication-security-best-practices.md) , till exempel konton för verkställande införlivande i en organisation 
* [MFA för varje administratörskonto som är associerade med en enskild användare](../authentication/howto-mfa-userstates.md) för andra anslutet SaaS-appar 
* MFA för alla administratörer för Microsoft SaaS-appar, inklusive administratörer i roller hanteras i Exchange Online och Office-portalen

Om du använder Windows Hello för företag, kan MFA krav uppfyllas med hjälp av Windows Hello tecknet i upplevelse. Mer information finns i [Windows Hello](https://docs.microsoft.com/windows/uwp/security/microsoft-passport). 

#### <a name="configure-identity-protection"></a>Konfigurera Identity Protection 

Azure AD Identity Protection är en algoritm-baserad övervakning och rapportering verktyg som du kan använda för att identifiera potentiella problem som påverkar organisationens identiteter. Du kan konfigurera automatiska svar till de identifierade misstänkta aktiviteterna och vidta lämpliga åtgärder du kan åtgärda detta. Mer information finns i [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>Hämta din Office 365 skyddar Score (om du använder Office 365)

Skydda poäng lista ut vilka Office 365-tjänster du använder (till exempel OneDrive, SharePoint och Exchange) och sedan tittar på dina inställningar och aktiviteter och jämför dem med en baslinje som upprättats av Microsoft. Du får en poäng utifrån hur justerade du är med metodtips för säkerhet. Vem som helst som har administratörsbehörighet (global administratör eller en anpassad administratörsroll) för en Office 365 Business Premium eller Enterprise-prenumeration kan komma åt den säkra poängen på [ https://securescore.office.com ](https://securescore.office.com/).

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>Läser du igenom informationen för Office 365 säkerhets- och (om du använder Office 365)

Den [planera för säkerhet och efterlevnad](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) beskriver tillvägagångssättet för hur Office 365-kund bör konfigurera Office 365 och utnyttja andra EMS-funktioner. Sedan kan granska steg 3 – 6 på hur du [skydda åtkomsten till data och tjänster i Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) och anvisningar om hur du [övervaka säkerhet och efterlevnad i Office 365](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6).


#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>Konfigurera aktivitetsövervakning för Office 365 (om du använder Office 365)

Du kan övervaka hur personer i din organisation använder Office 365-tjänster, så att du kan identifiera de användare som har ett administratörskonto och som inte behöver Office 365 har åtkomst till på grund av inte loggar in på dessa portaler. Mer information finns i [aktivitetsrapporter för hybridgranskning i Office 365-Administrationscenter](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263).

#### <a name="establish-incidentemergency-response-plan-owners"></a>Upprätta incident/emergency response plan ägare

Utföra incidenthantering effektivt är ett komplext företag. Därför kan kräver upprätta en lyckad incidenthantering funktion betydande planering och resurser. Det är viktigt att du kontinuerligt övervaka cyberattacker och upprätta procedurer för att prioritera hantering av incidenter. Effektiva metoder för att samla in, analysera och rapportera data är viktigt att skapa relationer och för att upprätta kommunikation med andra interna grupper och planera ägare. Mer information finns i [Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717). 

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Säker lokala Privilegierade administrativa konton om du inte redan har gjort

Om din Azure Active Directory-klient är synkroniserad med den lokala Active Directory, och följ sedan riktlinjerna i [Security privilegierad åtkomst översikt](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): Steg 1. Detta innefattar att skapa separata konton för användare som behöver genomföra lokala administrativa uppgifter, distribuera arbetsstationer för privilegierad åtkomst för Active Directory-administratörer och skapa unika lösenord lokal administratör för arbetsstationer och servrar.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Ytterligare åtgärder för organisationer som hanterar åtkomst till Azure

#### <a name="complete-an-inventory-of-subscriptions"></a>Slutför en förteckning över prenumerationer

Använda Enterprise portal och Azure portal för att identifiera de prenumerationer i din organisation som är värdar för program i produktion. 

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Ta bort Microsoft-konton från administratörsroller

Microsoft-konton från andra program, t.ex Xbox Live och Outlook bör inte användas som administratörskonton för organisationsprenumerationer. Ta bort Administratörsstatus från alla Microsoft-konton och Ersätt med Active Directory (till exempel chris@contoso.com) arbets- eller skolkonton.

#### <a name="monitor-azure-activity"></a>Övervaka Azure

Azure-aktivitetsloggen innehåller en historik över händelser på prenumerationsnivå i Azure. Den innehåller information om som skapas, uppdateras och tagit bort vilka resurser, och när de här händelserna inträffade. Mer information finns i [granska och ta emot meddelanden om viktiga åtgärder i din Azure-prenumeration](../../azure-monitor/platform/quick-audit-notify-action-subscription.md).


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Ytterligare åtgärder för organisationer som hanterar åtkomst till andra molnappar via Azure AD 

#### <a name="configure-conditional-access-policies"></a>Konfigurera principer för villkorlig åtkomst

Förbered principer för villkorlig åtkomst för både lokala och molnbaserade program. Om du har användare arbetsplatsanslutna enheter kan få mer information från [hur du konfigurerar lokal villkorlig åtkomst med hjälp av Azure Active Directory device registration](../active-directory-device-registration-on-premises-setup.md).


## <a name="stage-3-build-visibility-and-take-full-control-of-admin-activity"></a>Steg 3: Skapa synlighet och få fullständig kontroll av administratörsaktivitet

![Steg 3](./media/directory-admin-roles-secure/stage-three.png)

Steg 3 bygger på åtgärder från steg 2 och har utformats för att kunna implementeras på cirka 1 – 3 månader. Det här steget av säkerhetsöversikten över säker privilegierad åtkomst innehåller följande komponenter.

### <a name="general-preparation"></a>Allmän förberedelse

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Slutför en åtkomstgranskning av användarna i administratörsroller

Fler företagsanvändare få privilegierad åtkomst via molntjänster, vilket kan leda till en ökande ohanterade plattform. Detta omfattar även användare blir globala administratörer för Office 365, Azure-prenumerationsadministratörer och användare som har administratörsåtkomst till virtuella datorer eller via SaaS-appar. Organisationer bör däremot har alla anställda, särskilt administratörer hanterar dagliga affärstransaktioner som användare utan privilegier och bara vidta administratörsrättigheter efter behov. Eftersom antalet användare i adminroller kanske har blivit sedan första gången, granskar fullständig åtkomst för att identifiera och verifiera varje användare som kan användas för att aktivera administratörsprivilegier. 

Gör följande:

* Avgöra vilka användare som Azure AD-administratörer, aktivera på begäran, just-in-time-administratörsåtkomst och rollbaserad säkerhetskontroller.
* Konvertera användare som har inga Rensa motivering för privilegierad administratörsåtkomst till en annan roll (om ingen berättigad roll, ta bort dem).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Fortsätta distribution av starkare autentisering för alla användare 

Kräv C-suite chefer på hög nivå chefer, kritiska IT och personal för säkerhet och andra högexponerade användare har moderna, stark autentisering, till exempel Azure MFA- eller Windows Hello. 

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Använda dedikerade arbetsstationer för administration för Azure AD

Angripare kan försöka Privilegierade målkonton att få åtkomst till organisationens data och system så att de kan störa integritet och verifiering av data med hjälp av skadlig kod som ändrar programmet funktionaliteten eller snoops administratören att ange autentiseringsuppgifter. Arbetsstationer för privilegierad åtkomst (Paw) ger ett dedikerat operativsystem för känsliga uppgifter som är skyddat mot internetattacker och hotvektorer. Att separera känsliga uppgifter och konton från dagligen använda arbetsstationer och enheter infrastrukturen mot nätfiskeattacker, program och OS-sårbarheter, olika personifiering attacker och stöldattacker av autentiseringsuppgifter, till exempel tangenttryckning loggning, Pass-the-Hash och Pass-The-Ticket. Du kan minska risken att administratörer anger administratörsautentiseringsuppgifter utom på en Skrivbordsmiljö som har varit härdade genom att distribuera arbetsstationer för privilegierad åtkomst. Mer information finns i [arbetsstationer för privilegierad åtkomst](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations).

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Rekommendationerna från National Institute of Standards och teknik för hantering av incidenter 

Innehåller riktlinjer för incident hantering, särskilt för att analysera incident-relaterade data och bestämma lämpliga svaret på varje incident National Institute Standards and Technology (NIST). Mer information finns i [The (NIST) datorn Security Incident handbok för hantering av (SP 800-61, Revision 2)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Implementera Privileged Identity Management (PIM) för JIT ytterligare administrativa roller

Azure Active Directory, använder [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) kapaciteten. Tidsbegränsad aktivering av Privilegierade roller fungerar genom att:

* Aktivera admin-behörighet för att utföra en viss uppgift
* Använda MFA under aktiveringen
* Använd aviseringar för att meddela administratörer om out-of-band-ändringar
* Användarna kan behålla viss behörighet i en förinställd tidsperiod
* Tillåt säkerhetsadministratörer att upptäcka alla Privilegierade identiteter, visa granskningsrapporter och skapa åtkomstgranskningar för att identifiera varje användare som kan användas för att aktivera admin-behörighet

Om du redan använder Azure AD Privileged Identity Management kan du justera tidsramar för Tidsbundna privilegier efter behov (till exempel underhållsfönster).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Fastställa exponeringen för lösenordsbaserad inloggning protokoll (om du använder Exchange Online)

Tidigare antas protokoll att kombinationer av användarnamn/lösenord har bäddats in i enheter, e-postkonton, telefoner och så vidare. Men nu med risk för cyberattacker i molnet, rekommenderar vi att du identifiera alla potentiella användare som, om sina autentiseringsuppgifter har komprometterats kan vara oåterkalleligt i organisationen och exkluderar dem från att kunna logga in på sin e-post via användarnamn / lösenordet genom att implementera starka autentiseringskrav och villkorlig åtkomst. 

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>Slutför en utvärdering för granskning av roller för Office 365-roller (om du använder Office 365)

Utvärdera om alla administratörer användare är i rätt rollerna (ta bort och omtilldela enligt den här utvärderingen).

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>Granska den security incident management-metod som används i Office 365 och jämför med din organisation

Du kan hämta den här rapporten från [Security Incident Management i Microsoft Office 365](https://www.microsoft.com/download/details.aspx?id=54302).

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Fortsätt att skydda lokala Privilegierade administrativa konton

Om Azure Active Directory är ansluten till en lokal Active Directory, och följ sedan riktlinjerna i den [Security privilegierad åtkomst översikt](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): Steg 2. Detta inkluderar distribuera arbetsstationer för privilegierad åtkomst för alla administratörer, som kräver MFA, med bara tillräckligt administratör för domänkontrollanter, vilket minskar risken för angrepp på domäner, distribuera ATA för identifiering av attack.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Ytterligare åtgärder för organisationer som hanterar åtkomst till Azure

#### <a name="establish-integrated-monitoring"></a>Upprätta integrerad övervakning

Den [Azure Security Center](../../security-center/security-center-intro.md) tillhandahåller integrerad säkerhet övervaka och hantera principer för dina Azure-prenumerationer, hjälper dig att identifiera hot som kan annars oupptäckta och fungerar med ett vittomfattande ekosystem med säkerhet lösningar.

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Inventera dina Privilegierade konton inom virtuella värddatorer

I de flesta fall behöver du inte ge användare obegränsad behörighet till alla Azure-prenumerationer eller resurser. Du kan använda Azure AD-administratörsroller för att hålla isär uppgifter i din organisation och bevilja endast mängden åtkomst till användare som behöver att utföra specifika arbetsuppgifter. Till exempel använda Azure AD-administratörsroller för att låta en administratör hantera virtuella datorer i en prenumeration, medan en annan kan hantera SQL-databaser inom samma prenumeration. Mer information finns i [Kom igång med rollbaserad åtkomstkontroll i Azure-portalen](../../role-based-access-control/overview.md).

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Implementera PIM för Azure AD-administratörsroller

Använda Privileged identity Management med Azure AD-administratörsroller för att hantera, kontrollera och övervaka åtkomst till Azure-resurser. Använda PIM skyddar Privilegierade konton från cyberattacker genom att sänka exponeringstiden för privilegier och öka översyn i deras användning via rapporter och aviseringar. Mer information finns i [hantera Rollbaserad åtkomstkontroll till Azure-resurser med Privileged Identity Management](../../role-based-access-control/pim-azure-resource.md).

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Använda Azure log integration för att skicka relevant Azure loggar till din SIEM-system 

Azure-loggintegrering kan du integrera raw loggar från dina Azure-resurser till din organisations befintliga säkerhetsinformation och händelsehantering (SIEM) system. [Azure-loggintegrering](../../security/security-azure-log-integration-overview.md) samlar in Windows-händelser från Windows Loggboken och Azure-resurser från Azure-aktivitetsloggar och aviseringar i Azure Security Center Azure diagnostikloggar. 


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Ytterligare åtgärder för organisationer som hanterar åtkomst till andra molnappar via Azure AD

#### <a name="implement-user-provisioning-for-connected-apps"></a>Implementera användaretablering för anslutna appar

Azure AD kan du automatisera skapandet, underhållet och borttagningen av användaridentiteter i molnprogram (SaaS), till exempel Dropbox, Salesforce, ServiceNow och så vidare. Mer information finns i [automatisera användaretablering och avetablering för SaaS-program med Azure AD](../manage-apps/user-provisioning.md).

#### <a name="integrate-information-protection"></a>Integrera informationsskydd

MCAS kan du undersöka filer och ange principer baserat på klassificeringsetiketter för Azure Information Protection, vilket ger bättre synlighet och kontroll över dina data i molnet. Söka och klassificera filer i molnet och installera Azure information protection-etiketter. Mer information finns i [integrering med Azure Information Protection](https://docs.microsoft.com/cloud-app-security/azip-integration).

#### <a name="configure-conditional-access"></a>Konfigurera villkorlig åtkomst

Konfigurera villkorlig åtkomst baserat på en grupp, plats och programmets känslighet för [SaaS-appar](https://azure.microsoft.com/overview/what-is-saas/) och Azure AD-anslutna appar. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Övervaka aktivitet i anslutna molnappar

För att säkerställa användarnas åtkomst är skyddad i anslutna program samt, rekommenderar vi att du utnyttjar [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). Detta skyddar enterprise-åtkomst för molnappar, förutom att skydda dina administratörskonton genom att:

* Utöka synlighet och kontroll i molnappar
* Skapa principer för åtkomst, aktiviteter och delning av data
* Automatiskt identifiera riskfyllda aktiviteter, onormalt beteende och hot
* Förhindra att data läcker ut
* Minimera risker och automatiserade threat skydd och principtillämpning

Cloud App Securitys SIEM-agenten integrerar Cloud App Security med din SIEM-server för att aktivera centraliserad övervakning av Office 365-aviseringar och aktiviteter. Körs på servern och hämtar aviseringar och aktiviteter från Cloud App Security och skickar dem till SIEM-servern. Mer information finns i [SIEM-integrering](https://docs.microsoft.com/cloud-app-security/siem).

## <a name="stage-4-continue-building-defenses-to-a-more-proactive-security-posture"></a>Steg 4: Fortsätta skapa försvar för en mer proaktiv säkerhetsposition


![Steg 4](./media/directory-admin-roles-secure/stage-four.png)

Steg 4 i översikten bygger på synlighet från steg 3 och har utformats för att kunna implementeras i sex månader och mycket mer. Du har slutfört en översikt över-hjälper dig att utveckla strong privilegierad åtkomst skydd mot potentiella attacker som är kända och tillgängliga idag. Tyvärr säkerhetshot ständigt utvecklas och ändras, så vi rekommenderar att du ser säkerhet som en pågående process som fokuserar på att höja kostnaden och minska frekvensen av lyckade angrepp på din miljö.

Att skydda privilegierad åtkomst är ett viktigt första steg för att upprätta säkerhetsgarantier för företagstillgångar i en modern organisation, men det är inte den enda delen av ett fullständigt säkerhetsprogram vilket skulle inkludera element som princip, åtgärder, information Ange pågående säkerhetsgarantier säkerhet, servrar, program, datorer, enheter, molninfrastruktur och andra komponenter. 

Utöver att hantera dina konton för privilegierad åtkomst, rekommenderar vi att du läser följande kontinuerligt:

* Se till att administratörer gör sin dagliga verksamhet som användare utan privilegier.
* Endast beviljar privilegierad åtkomst vid behov och ta bort den efteråt (just-in-time).
* Behålla och granska aktivitetsrapporter som är relaterade till Privilegierade konton.

Mer information om hur du bygger en översikt för fullständig säkerhet finns i [Microsoft IT arkitektur molnresurser](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources). Mer information om Microsoft-tjänster som hjälper till med något av dessa ämnen Kontakta din Microsoft-representant eller gå [skapa kritiska cyberhot försvar för att skydda ditt företag](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx).

Den här pågående slutfasen i översikten säker privilegierad åtkomst innehåller följande komponenter.

### <a name="general-preparation"></a>Allmän förberedelse

#### <a name="review-admin-roles-in-azure-active-directory"></a>Granska administratörsroller i Azure Active Directory 

Avgöra om aktuella inbyggda Azure AD-administratörsroller är fortfarande uppdaterade och se till att användare finns bara i de roller och delegeringar som de behöver för motsvarande behörigheter. Du kan ange separata administratörer att hantera olika funktioner med Azure AD. Mer information finns i [Tilldela administratörsroller i Azure Active Directory](directory-assign-admin-roles.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Granska användare som har administration av Azure AD-anslutna enheter

Mer information finns i [hur du konfigurerar hybrid Azure Active Directory-anslutna enheter](../device-management-hybrid-azuread-joined-devices-setup.md).

#### <a name="review-members-of-built-in-office-365-admin-roleshttpssupportofficecomarticleabout-office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d"></a>Granska medlemmar i [inbyggda Office 365-administratörsroller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)
Om du använder Office 365.
‎
#### <a name="validate-incident-response-plan"></a>Verifiera incidentåtgärdsplan

För att förbättra din plan, rekommenderar Microsoft att du regelbundet kontrollera att din plan fungerar som förväntat:

* Gå igenom din befintliga vägkarta för att se vad inte uppfylldes
* Baserat på postmortem analysen, ändra befintliga eller definiera nya Metodtips
* Se till att dina uppdaterade incidentåtgärdsplan och bästa praxis ska distribueras i hela organisationen


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Ytterligare åtgärder för organisationer som hanterar åtkomst till Azure 

Bestäm om du behöver [överföra ägarskap för en Azure-prenumeration till ett annat konto](../../billing/billing-subscription-transfer.md).
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>”Bryt om”: vad du gör i nödfall

![Varning](./media/directory-admin-roles-secure/emergency.jpeg)

1. Meddela viktiga chefer och security införlivande med relevant information om incidenten.

2. Granska dina attack spelbok. 

3. Komma åt din ”Bryt om” konto användarnamn/lösenord kombination att logga in på Azure AD. 

4. Få hjälp från Microsoft av [att öppna en supportförfrågan för Azure](../../azure-supportability/how-to-create-azure-support-request.md).

5. Titta på den [Azure AD-inloggningsrapporter](../reports-monitoring/overview-reports.md). Det kan finnas en fördröjning mellan en händelse inträffar och när det ingår i rapporten.

6. För hybridmiljöer om externa och din AD FS-servern inte är tillgänglig kan du behöva växla tillfälligt från federerad autentisering du använder lösenordets hash-synkronisering. Detta återställer domän federation tillbaka till hanterad autentisering tills AD FS-servern blir tillgänglig.

7. Övervaka e-post för privilegierade konton.

8. Se till att spara säkerhetskopior av relevanta loggfiler för potentiella kriminaltekniska och juridiska undersökningar.

Läs mer om hur Microsoft Office 365 hanterar säkerhetsincidenter [Security Incident Management i Microsoft Office 365](https://aka.ms/Office365SIM).

## <a name="faq-common-questions-we-receive-regarding-securing-privileged-access"></a>VANLIGA FRÅGOR OCH SVAR: Vanliga frågor rörande att säkra privilegierad åtkomst  


**F:** Vad gör jag om jag inte har implementerats ännu alla komponenter för säker åtkomst?

**Svar:** Definiera minst två glas kontot, tilldela MFA till Privilegierade konton och separata användarkonton från globala administratörskonton.


**F:** Vad är det främsta problem som måste åtgärdas först efter ett intrång?

**Svar:** Var noga med att du kräver den starkaste autentiseringen för högexponerade enskilda användare.


**F:** Vad händer om våra Privilegierade administratörer har inaktiverats?

**Svar:** Skapa en Global administratör-konto som är alltid hålls uppdaterade.


**F:** Vad händer om det finns endast en global administratör vänster och de inte går att nå? 

**Svar:** Använd ett av dina glas konton för att få omedelbar privilegierad åtkomst.


**F:** Hur kan jag skydda administratörer i min organisation?

**Svar:** Finns administratörer i organisationen alltid göra sin dagliga verksamhet som ”utan privilegier” standardanvändare.
 

**F:** Vad är bästa praxis för att skapa administratörskonton i Azure AD?

**Svar:** Reservera privilegierad åtkomst för specifika administrativa uppgifter.


**F:** Vilka verktyg finns för att minska beständiga administratörsåtkomst?

**Svar:** Roller för Privileged Identity Management (PIM) och Azure AD-administratör.


**F:** Vad är Microsoft position på Synkronisera administratörskonton till Azure AD?

**Svar:** Nivå 0-administratörskonton (inklusive konton, grupper och andra resurser som har direkt eller indirekt administrativ kontroll av AD-skogen, domäner eller domänkontrollanter och alla tillgångar) används endast för lokala AD-konton och är vanligtvis inte synkroniseras för Azure AD för molnet. 


**F:** Hur håller vi administratörer från att tilldela slumpmässiga administratörsåtkomst i portalen?

**Svar:** Använda icke-privilegierade konton för alla användare och de flesta administratörer. Börja med att utveckla ett fotavtryck för organisationen för att ta reda på vilken bör vara privilegierad några administratörskonton. Och Övervakare som nyligen skapats administrativa användare.


## <a name="next-steps"></a>Nästa steg

* [Microsoft Trust Center för produkten säkerhet](https://www.microsoft.com/trustcenter/security) – säkerhetsfunktionerna i Microsoft cloud produkter och tjänster

* [Microsoft Trust Center - efterlevnad](https://www.microsoft.com/trustcenter/compliance/complianceofferings) – Microsofts omfattande uppsättningen efterlevnadserbjudanden för cloud services

* [Vägledning om hur du utför en riskbedömning](https://www.microsoft.com/trustcenter/guidance/risk-assessment) – Hantera säkerhet och efterlevnad kraven för Microsofts molntjänster

### <a name="other-ms-online-services"></a>Andra onlinetjänster för MS 

* [Microsoft Intune Security](https://www.microsoft.com/trustcenter/security/intune-security) – Intune ger hantering av mobila enheter, hantering av mobila program och funktioner för Datorhantering från molnet.

* [Microsoft Dynamics 365-säkerhet](https://www.microsoft.com/trustcenter/security/dynamics365-security) – Dynamics 365 är Microsofts molnbaserade lösning som förenar customer relationship management (CRM) och Företagsresursplanering (Företagsresursplanering)-funktioner.

 
