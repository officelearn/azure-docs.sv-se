---
title: Metodtips för att skydda administrativ åtkomst i Azure AD | Microsoft Docs
description: Kontrollera att din organisation åtkomst och admin administratörskonton är säker. För systemarkitekter och IT-proffs som konfigurera Azure AD Azure och Microsoft Online Services.
services: active-directory
keywords: ''
author: curtand
ms.author: curtand
ms.date: 03/09/2018
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.custom: it-pro
ms.reviewer: martincoetzer, MarkMorow
ms.openlocfilehash: 9897a3578ad35376bea81b4821dbc2d84526a736
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Skydda privilegierad åtkomst för hybrid- och distributioner i Azure AD

Säkerheten för de flesta eller alla företagets tillgångar i organisationen moderna beror på integriteten för privilegierade konton som administrera och hantera IT-system. Skadliga aktörer inklusive cyber angripare ofta mål administratörskonton och andra element för privilegierad åtkomst att snabbt komma åt känsliga data och system med hjälp av autentiseringsuppgifter stöld av attacker. Cloud är services, förebygga och svar i molntjänstleverantören och kunden gemensamma ansvar. Mer information om de senaste hoten slutpunkter och molnet finns på [Microsoft Security Intelligence-rapporten](https://www.microsoft.com/security/sir/default.aspx). Den här artikeln kan hjälpa dig att utveckla en plan mot stänger glapp mellan din aktuella planer och de riktlinjer som beskrivs här.

> [!NOTE] 
> Microsoft strävar efter att högsta möjliga förtroende, genomskinlighet, standarder överensstämmelse och regelefterlevnad. Mer information om hur Microsoft-teamet globala incidenter minimera effekterna av attacker mot molntjänster och hur security är inbyggd i Microsoft-produkter och molntjänster i [Microsoft Trust Center - säkerhet](https://www.microsoft.com/en-us/trustcenter/security)och Microsoft efterlevnad mål på [Microsoft Trust Center - kompatibilitet](https://www.microsoft.com/en-us/trustcenter/compliance).

<!--## Risk management, incident response, and recovery preparation

A cyber-attack, if successful, can shut down operations not just for a few hours, but in some cases for days or even weeks. The collateral damage, such as legal ramifications, information leaks, and media coverage, could potentially continue for years. To ensure effective company-wide risk containment, cybersecurity and IT pros must align their response and recovery processes. To reduce the risk of business disruption due to a cyber-attack, industry experts recommend you do the following:

* As part of your risk management operations, establish a crisis management team for your organization that is responsible for managing all types of business disruptions.

* Compare your current risk mitigations, incident response, and recovery plan with industry best practices for managing a business disruption before, during, and after a cyber-attack.

* Develop and implement a roadmap for closing the gaps between your current plans and the best practices described in this document.


## Securing privileged access for hybrid and cloud deployments

does the article really start here?-->
För de flesta organisationer beror säkerheten för företagets tillgångar på integriteten för privilegierade konton som administrera och hantera IT-system. Cyber angripare fokusera på privilegierad åtkomst till infrastruktursystem (till exempel Active Directory och Azure Active Directory) för att få tillgång till känsliga data i en organisation. 

Traditionella metoder som fokuserar på att skydda ingång och utgång punkterna i ett nätverk som säkerhetsinformation perimeternätverket är mindre effektiva på grund av ökade användning av SaaS-appar och personliga enheter på Internet. Fysisk ersätter network security perimeternätverket i en komplex moderna företag är kontroller för autentisering och auktorisering i Identitetslagret för en organisation. 

Privilegierade administrativa konton är effektivt kontroll över den här nya ”säkerhet perimeter”. Det är viktigt att skydda privilegierad åtkomst, oavsett om miljön är lokalt, moln eller hybrid lokala och värdbaserade molntjänster. Skydda administrativ åtkomst mot definieras motståndare kräver att du kan göra en fullständig och Intelligent metod för att isolera organisationens system från risker. 

Skydda privilegierad åtkomst kräver ändringar
* Processer, administrativa metoderna och kunskapshantering
* Tekniska komponenter som värd försvar, konto skydd och Identitetshantering

Det här dokumentet fokuserar främst på att skapa en plan för att skydda identiteter och åtkomst som hanteras eller rapporterades i Azure AD och Microsoft Azure, Office 365 och andra molntjänster. För organisationer som har lokala administrativa konton, se anvisningar för att lokalt och hybrid privilegierad åtkomst hanteras från Active Directory på [skydda privilegierad åtkomst](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access). 

> [!NOTE] 
> Riktlinjerna i den här artikeln gäller främst funktioner i Azure Active Directory som ingår i Azure Active Directory Premium-planer P1 och P2. Azure Active Directory Premium P2 ingår i EMS E5 suite och Microsoft 365 E5 suite. Dessa riktlinjer förutsätter att din organisation redan har Azure AD Premium P2 antal licenser för dina användare. Om du inte har dessa licenser, kanske vissa av riktlinjer som inte gäller för din organisation. I den här artikeln är termen global administratör (eller global administratör) också synonym med ”företagets administratör” eller ”Innehavaradministratör”.

## <a name="develop-a-roadmap"></a>Utveckla en plan 

Microsoft rekommenderar att du utveckla och följa en översikt över om du vill skydda privilegierad åtkomst mot cyber angripare. Du kan alltid ändra din plan för din befintliga funktioner och specifika krav i din organisation. Varje steg i Översikt bör öka kostnaderna och svårigheten för motståndare för angrepp privilegierad åtkomst för lokalt, moln och hybrid tillgångar. Microsoft rekommenderar följande fyra översikt över etapper: Detta bör översikt över scheman mest effektiva och snabbaste implementeringar först baserat på Microsofts upplevelser med cyber attack incident och svar implementering. Tidslinjer för den här vägledningen är ungefärliga.

![Steg i översikt över med gång rader](./media/admin-roles-best-practices/roadmap-timeline.png)

* Steg 1 (24-48 timmar): kritiska objekt som vi rekommenderar att du gör direkt

* Steg 2 (2 – 4 veckor): minimera teknikerna som används mest attack

* Steg 3 (1-3 månader): skapa synlighet och skapa full kontroll över administratörsaktivitet

* Steg 4 (sex månader och senare): fortsätta skapa försvar för att ytterligare skydda din säkerhet plattform

Översikt över ramverket är utformat för att maximera användningen av Microsoft-teknik som du kanske redan har distribuerat. Du kan också dra nytta av viktiga aktuell och kommande säkerhetstekniker och integrera säkerhetsverktyg från andra leverantörer som du redan har distribuerat eller planerar distribution. 

## <a name="stage-1-critical-items-that-we-recommend-you-do-right-away"></a>Steg 1: Kritiska objekt som vi rekommenderar att du gör direkt

![Fas 1](./media/admin-roles-best-practices/stage-one.png)

Steg 1 av översikt fokuserar på viktiga uppgifter som är snabb och enkel att implementera. Vi rekommenderar att du gör dessa några objekt direkt i de första 24-48 timmarna så ett grundläggande säker privilegierad åtkomst. Det här steget av skyddade privilegierad åtkomst översikt innehåller följande åtgärder:

### <a name="general-preparation"></a>Allmän förberedelse

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Aktivera Azure AD Privileged Identity Management

Om du inte redan är på Azure AD Privileged Identity Management (PIM) kan du göra det i produktions-klienten. När du aktiverar Privileged Identity Management kan du får en avisering e-postmeddelanden för privilegierad åtkomst rollen ändringar. Dessa aviseringar ge en tidig varning när fler användare läggs till mycket Privilegierade roller i din katalog.

Azure AD Privileged Identity Management ingår i Azure AD Premium P2- eller EMS E5. Dessa lösningar hjälper dig att skydda åtkomst till program och resurser i lokal miljö och i molnet. Om du inte redan har Azure AD Premium P2- eller EMS E5 och vill utvärdera flera av de funktioner som refereras till i den här vägledningen registrera dig för den [Enterprise Mobility + Security kostnadsfri 90-dagars utvärderingsversion](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial). Använd dessa licens försök för att prova Azure AD Privileged Identity Management och Azure AD Identity Protection för att övervaka aktiviteten med Azure AD avancerad säkerhet reporting, granskning och aviseringar.

När du har aktiverat Azure AD Privileged Identity Management:

1. Logga in på den [Azure-portalen](https://portal.azure.com/) med ett konto som är en global administratör för din klient för produktion.

2. Om du klienten där du vill använda Privileged Identity Management markerar du ditt användarnamn i det övre högra hörnet i Azure-portalen.

3. Välj **alla tjänster** och filtrera listan för **Azure AD Privileged Identity Management**.

4. Öppna Privileged Identity Management från den **alla tjänster** listan och Fäst det på instrumentpanelen.

Den första personen som använder Azure AD Privileged Identity Management i din klient tilldelas automatiskt den **säkerhetsadministratör** och **administratör av Privilegierade roller** roller i klienten. Endast privilegierade rollen administratörer kan hantera rolltilldelningar för Azure AD-katalog med användare. När du lägger till Azure AD Privileged Identity Management, visas som också säkerhetsguiden som vägleder dig genom den inledande identifiering och tilldelning upplevelsen. Du kan avsluta guiden utan att göra några ytterligare just nu. 

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Identifiera och klassificera konton som är mycket Privilegierade roller 

Visa de användare som finns i katalogen roller Global administratör, administratör av Privilegierade roller, administratör i Exchange Online och SharePoint Online-administratör när aktivera Azure AD Privileged Identity Management. Om du inte har Azure AD PIM i din klientorganisation, kan du använda den [PowerShell API](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0). Börja med rollen global administratör som den här rollen är generisk: en användare som har tilldelats den här rollen administratör har samma behörigheter för alla molntjänster som din organisation prenumererar på, oavsett om de har tilldelats den här rollen i Office 365-portalen , Azure portal, eller genom att använda Azure AD-modulen för Microsoft PowerShell. 

Ta bort alla konton som inte längre behövs i rollerna och kategorisera de återstående konton som har tilldelats administratörsroller:

* Tilldelas individuellt till administrativa användare och kan också användas för icke-administrativa syften (till exempel personliga e-post)
* Administrativa användare som tilldelats och separat avsedda för administrativa syften
* Delas mellan flera användare
* Break om nödåtkomst scenarier
* För automatiserade skript
* För externa användare

#### <a name="define-at-least-two-emergency-access-accounts"></a>Definiera minst två konton för nödåtkomst 

Se till att du inte får i en situation där de kan oavsiktligt utestängas från administrationen av Azure AD-klienten på grund av att det inte går att logga in eller aktivera en befintlig enskilda användares konto som administratör. Till exempel om organisationen är federerat till en lokal identitetsleverantör kanske som identitetsprovider inte tillgänglig så att användare inte kan logga in lokalt. Du kan minimera effekten av misstag bristande administrativ åtkomst genom att lagra två eller flera konton för nödåtkomst i din klient.

Konton för nödåtkomst hjälpa organisationer att begränsa privilegierad åtkomst i en befintlig Azure Active Directory-miljö. Dessa konton är mycket Privilegierade och har tilldelats inte till specifika personer. Konton för nödåtkomst är begränsade till nödfall 'nödkonto' scenarier där normala administrativa konton inte kan användas. Organisationer måste kontrollera i syfte att kontrollera och minska användningen av nödfall konton till endast den tid som det är nödvändigt. 

Utvärdera de konton som är tilldelade eller är tillämpliga för rollen global administratör. Om du inte fick någon endast molnbaserad användarkonton med den *. onmicrosoft.com-domän (avsett för ”nödkonto” nödåtkomst), skapa dem. Mer information finns i [hantera nödåtkomst administrativa konton i Azure AD](active-directory-admin-manage-emergency-access-accounts.md).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Aktivera multifaktorautentisering och registrera alla andra mycket Privilegierade användare ofedererad administratörskonton 

Kräver Azure Multi-Factor Authentication (MFA) vid inloggning för enskilda användare som är permanent tilldelade till en eller flera av Azure AD-administratörsroller: Global administratör, administratör av Privilegierade roller, administratör i Exchange Online och SharePoint Online-administratör. Använd guiden för att aktivera [Multi-Factor Authentication (MFA) för dina administratörskonton](authentication/howto-mfa-userstates.md) och se till att alla användare har registrerats på [ https://aka.ms/mfasetup ](https://aka.ms/mfasetup). Mer information finns under steg 2 och 3 i guiden [skydda åtkomst till data och tjänster i Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e). 

## <a name="stage-2-mitigate-the-most-frequently-used-attack-techniques"></a>Steg 2: Minimera teknikerna som används mest attack

![Steg 2](./media/admin-roles-best-practices/stage-two.png)

Steg 2 av översikt fokuserar på att minimera de vanligaste attack teknikerna för stöld av autentiseringsuppgifter och missbruk och är avsedd att implementeras i cirka 2 – 4 veckor. Det här steget av skyddade privilegierad åtkomst översikt innehåller följande åtgärder.

### <a name="general-preparation"></a>Allmän förberedelse

#### <a name="conduct-a-inventory-of-services-owners-and-admins"></a>Utföra en inventering av tjänster, ägare och administratörer

Med ökade bring-your-own device (BYOD) och arbete från startsidan principer och tillväxt trådlös anslutning i företag är det viktigt att övervaka som ansluter till nätverket. En effektiv säkerhetsgranskning visar ofta enheter, program och program som körs på nätverket som inte stöds av IT-avdelningen, och därför kan inte skydda. Mer information finns i [säkerheten i Azure-hantering och övervakning-översikt](../security/security-management-and-monitoring-overview.md). Se till att du inkluderar alla följande uppgifter i din inventering. 

* Identifiera de användare som har administrativa roller och tjänster där de kan hantera.
* Använda Azure AD PIM för att ta reda på vilka användare i din organisation som har administratörsåtkomst till Azure AD, inklusive ytterligare roller utöver de som anges i steg 1.
* Utöver de roller som definierats i Azure AD, levereras Office 365 med en uppsättning administratörsroller som du kan tilldela till användare i din organisation. Varje administratörsroll mappar till vanliga funktioner för företag och ger personer i din organisation har behörighet att utföra särskilda aktiviteter i administrationscentret för Office 365. Använd administrationscentret för Office för att ta reda på vilka användare i din organisation som har administratörsåtkomst till Office 365, inklusive via roller som inte hanteras i Azure AD. Mer information finns i [om Office 365-administratörsroller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) och [rekommenderade säkerhetsmetoder för Office 365](https://support.office.com/article/Security-best-practices-for-Office-365-9295e396-e53d-49b9-ae9b-0b5828cdedc3).
* Utför inventeringen i andra tjänster som din organisation använder, till exempel Azure, Intune eller Dynamics 365.
* Se till att dina administratörskonton (konton som används för administration, inte bara dagliga användarkonton) ha fungerande e-postadresser som är kopplade till dem och har registrerat dig för Azure MFA eller Använd MFA lokala.
* Fråga om deras affärsjustering för administrativ åtkomst.
* Ta bort administratörsåtkomst för de enskilda användare och tjänster som inte behövs.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Identifiera Microsoft-konton i administrativa roller som ska växlas till arbetsplats eller skola konton 

Ibland kan återanvända de första globala administratörerna för en organisation sina befintliga Microsoft-kontouppgifter när de började använda Azure AD. Dessa Microsoft-konton ska ersättas med enskilda molnbaserade eller synkroniserade konton. 

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Se till att separata användarkonton och e-vidarebefordran för globala administratörskonton 

Globala administratörskonton ska inte ha personliga e-postadresser som personliga e-postkonton regelbundet phished av cyber angripare. För att avgränsa internet risker (nätfiskeattacker, oavsiktlig webbsurfning) från administrativa privilegier, skapa dedikerade konton för varje användare med administratörsbehörighet. 

Om du inte redan har gjort det, kan du skapa separata konton för användare att utföra uppgifter för global administratör, se till att de inte oavsiktligt öppna e-postmeddelanden eller köra program som är associerade med deras administratörskonton. Försäkra dig om dessa konton har sin e-post som vidarebefordras till en aktiv postlåda.  

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Kontrollera att lösenorden för administratörskonton nyligen har ändrat

Kontrollera att alla användare har loggat in på sina administrativa konton och ändra sina lösenord på minst en gång under de senaste 90 dagarna. Kontrollera också som en delad konton i vilken flera användare känner till lösenordet har fått sina lösenord nyligen ändrats.

#### <a name="turn-on-password-hash-synchronization"></a>Aktivera synkronisering av lösenords-hash

Synkronisering av lösenords-hash är en funktion som används för att synkronisera hash-värden för användarlösenord från en lokal Active Directory-instans för en molnbaserad Azure AD-instans. Även om du väljer att använda federation med Active Directory Federation Services (AD FS) eller andra identitetsleverantörer, kan du ställa in synkronisering av lösenords-hash som en säkerhetskopia i fall din lokala infrastruktur, till exempel AD eller ADFS-servrar misslyckas eller så blir inte tillgänglig för tillfället. Användare kan logga in till tjänsten med samma lösenord som de använder för att logga in på sina lokala AD-instans. Dessutom kan identitetsskydd att identifiera avslöjade autentiseringsuppgifter genom att jämföra de lösenordshashvärden med kända äventyras, om en användare har utnyttjas sina samma e-postadress och lösenord på andra tjänster som inte är ansluten till Azure AD-lösenord.  Mer information finns i [implementera Lösenordssynkronisering med Azure AD Connect-synkronisering med hash-](./connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md).

#### <a name="require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users"></a>Kräv Multi-Factor authentication (MFA) för användare i alla Privilegierade roller samt exponerade användare

Azure AD rekommenderar att du kräva multifaktorautentisering (MFA) för alla användare, administratörer och andra användare som skulle ha en betydande inverkan om kontot har drabbats (till exempel finansiella polis). Detta minskar risken för angrepp på grund av en komprometterad lösenord.

Aktivera:

* [MFA för hög exponering konton](authentication/multi-factor-authentication-security-best-practices.md) , till exempel konton för verkställande polis i en organisation 
* [MFA för varje administratörskonto som är associerade med en enskild användare](authentication/howto-mfa-userstates.md) för andra anslutna SaaS-appar 
* MFA för alla administratörer för Microsoft SaaS-appar, inklusive administratörer i roller hanteras i Exchange Online och Office-portalen

Om du använder Windows Hello för företag, kan MFA krav uppfyllas med hjälp av Windows Hello tecknet i upplevelsen. Mer information finns i [Windows Hello](https://docs.microsoft.com/windows/uwp/security/microsoft-passport). 

#### <a name="configure-identity-protection"></a>Konfigurera Identity Protection 

Azure AD Identity Protection är en algoritm-baserad övervakning och rapportering av verktyg som du kan använda för att identifiera potentiella problem som påverkar din organisations identiteter. Du kan konfigurera automatiska svar på de identifierade misstänkta aktiviteterna och åtgärda problemet kan lösas. Mer information finns i [Azure Active Directory Identity Protection](active-directory-identityprotection.md).

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>Hämta din Office 365 Secure poäng (om du använder Office 365)

Skydda poäng figurerna reda på vilka Office 365-tjänster du använder (till exempel OneDrive, SharePoint och Exchange) och sedan tittar på dina inställningar och aktiviteter och jämför dem med en baslinje som upprättats av Microsoft. Du får en poäng baserat på hur justerade är regelverk för säkerhet. Alla som har administratörsbehörigheter (global administratör eller en anpassad administratörsroll) för en Office 365 Business Premium eller Enterprise-prenumeration har åtkomst till den säkra poängen på [ https://securescore.office.com ](https://securescore.office.com/).

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>Läser du igenom informationen för Office 365 säkerhet och kompatibilitet (om du använder Office 365)

Den [planera för säkerhet och efterlevnad](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) beskrivs tillvägagångssätt för hur en Office 365-kund ska konfigurera Office 365 och andra funktioner för EMS användas. Sedan granska steg 3-6 på hur du [skydda åtkomst till data och tjänster i Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) och anvisningar om hur du [övervaka säkerhet och efterlevnad i Office 365](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6).


#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>Konfigurera aktivitetsövervakning för Office 365 (om du använder Office 365)

Du kan övervaka hur personer i din organisation använder Office 365-tjänster så att du kan identifiera de användare som har ett administratörskonto och som inte behöver Office 365 har åtkomst till på grund av att inte logga in på dessa portaler. Mer information finns i [aktivitetsrapporter i administrationscentret för Office 365](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263).

#### <a name="establish-incidentemergency-response-plan-owners"></a>Upprätta incident/emergency response plan ägare

Utför incidenter effektivt är en komplex företag. Därför kan kräver upprätta en lyckad incidenter funktion betydande planering och resurser. Det är viktigt att du kontinuerligt övervaka för cyber attacker och fastställa förfaranden för att prioritera hantering av incidenter. Effektiva metoder för att samla in, analysera och rapportera data är viktigt att skapa relationer och för att upprätta kommunikation med andra interna grupper och planera ägare. Mer information finns i [Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717). 

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Säker lokalt Privilegierade administrativa konton om du inte redan har gjort

Om din Azure Active Directory-klient synkroniseras med lokala Active Directory, och följ sedan riktlinjerna i [säkerhet privilegierad åtkomst översikt över](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): steg 1. Det innefattar att skapa separata administratörskonton för användare som behöver utföra administrativa uppgifter för lokalt, distribuera Privileged Access arbetsstationer för Active Directory-administratörerna och skapa unik lokal administratör lösenord för arbetsstationer och -servrar.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Ytterligare åtgärder för organisationer som hanterar åtkomst till Azure

#### <a name="complete-an-inventory-of-subscriptions"></a>Slutföra en förteckning över prenumerationer

Använd Enterprise portal och Azure portal för att identifiera prenumerationer i din organisation som värd för program i produktion. 

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Ta bort Microsoft-konton från administratörsroller

Microsoft-konton från andra program, till exempel Xbox Live och Outlook bör inte användas som administratörskonton för organisationens prenumerationer. Ta bort Administratörsstatus från alla Microsoft-konton och Ersätt med Active Directory (till exempel chris@contoso.com) arbets- eller skolkonton.

#### <a name="monitor-azure-activity"></a>Övervaka Azure aktivitet

Azure-aktivitetsloggen innehåller en historik över prenumerationsnivån händelser i Azure. Den ger information om vem som skapade, uppdateras och tas bort vilka resurser och när de här händelserna inträffade. Mer information finns i [granskning och ta emot meddelanden om viktiga åtgärder i din Azure-prenumeration](../monitoring-and-diagnostics/monitor-quick-audit-notify-action-in-subscription.md).


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Ytterligare åtgärder för organisationer som hanterar åtkomst till andra molnappar via Azure AD 

#### <a name="configure-conditional-access-policies"></a>Konfigurera principer för villkorlig åtkomst

Förbered principer för villkorlig åtkomst för lokala och molnbaserade program. Om du har användare arbetsplatsanslutna enheter kan få mer information från [konfigurerar lokal villkorlig åtkomst med hjälp av Azure Active Directory-enhetsregistrering](active-directory-device-registration-on-premises-setup.md).


## <a name="stage-3-build-visibility-and-take-full-control-of-admin-activity"></a>Steg 3: Skapa synlighet och ha full kontroll över administratörsaktivitet

![Steg 3](./media/admin-roles-best-practices/stage-three.png)

Steg 3 bygger på åtgärder från steg 2 och är utformad för att implementeras i cirka 1-3 månader. Det här steget av skyddade privilegierad åtkomst översikt innehåller följande komponenter.

### <a name="general-preparation"></a>Allmän förberedelse

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Slutföra en åtkomst-granskning av användare i administratörsroller

Fler företagsanvändare få privilegierad åtkomst via molntjänster, vilket kan leda till en ökande ohanterade plattform. Detta omfattar användare blir globala administratörer för Office 365, Azure-prenumerationsadministratörer och användare som har administratörsåtkomst till virtuella datorer eller via SaaS-appar. I stället bör organisationer har alla anställda, särskilt administratörer hanterar dagliga verksamheten transaktioner som icke-privilegierade användare, och bara ta på administratörsrättigheter efter behov. Eftersom antalet användare i administratörsroller kan blivit sedan inledande antogs granskar fullständig åtkomst för att identifiera och verifiera varje användare som är berättigad att aktivera administratörsrättigheter. 

Gör följande:

* Avgöra vilka användare som Azure AD-administratörer, aktivera på begäran, just-in-time-administratörsåtkomst och rollbaserad säkerhetskontroller.
* Konvertera användare har ingen Rensa motivering för privilegierad administratörsåtkomst till en annan roll (om ingen berättigade roll, ta bort dem).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Fortsätta installationen av starkare autentisering för alla användare 

Kräver C suite chefer, övergripande chefer, kritiska IT och säkerhet personal och andra hög exponeras användare har moderna, stark autentisering, till exempel Azure MFA eller Windows Hello. 

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Använda dedicerade arbetsstationer för administration för Azure AD

Angripare kan försöka mål Privilegierade konton för att få åtkomst till företagets data och datorer så att de kan störa integritet och verifiering av data via skadlig kod som ändrar funktionaliteten för programmet eller snoops administratören att ange autentiseringsuppgifter. Privilegierad åtkomst arbetsstationer (PAWs) innehåller ett dedikerat operativsystem känsliga som skyddas från Internet-attacker och hotvektorer. Avgränsa dessa känsliga uppgifter och konton från dagligen använder arbetsstationer och enheter ger mycket starkt skydd från nätfiskeattacker, program och OS säkerhetsrisker, olika personifiering attacker och autentiseringsuppgifter stöld av attacker som tangenttryckning loggning, Pass-the-Hash och Pass-The-Ticket. Du kan minska risken att administratörer ange administratörsautentiseringsuppgifter utom på en Skrivbordsmiljö som har varit härdat genom att distribuera arbetsstationer med privilegierad åtkomst. Mer information finns i [arbetsstationer med privilegierad åtkomst](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations).

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Granska National Institute of Standards och teknik rekommendationer för hantering av incidenter 

Innehåller riktlinjer för hantering av incidenter, särskilt för att analysera incident-relaterad data och bestämma lämpliga svaret på varje incident National Institute Standards and Technology (NIST). Mer information finns i [i (NIST) datorn Incident hantering säkerhetsguiden (SP 800 61, version 2)](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Implementera Privileged Identity Management (PIM) för JIT ytterligare administrativa roller

Azure Active Directory, Använd [Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md) kapaciteten. Tidsbegränsade aktivering av Privilegierade roller fungerar genom att du kan:

* Aktivera admin-privilegier för att utföra en viss uppgift
* Använda Multifaktorautentisering under aktiveringen
* Använd aviseringar för att meddela administratörer om out-of-band-ändringar
* Användarna att behålla vissa behörigheter för en förkonfigurerad tidsperiod
* Tillåt säkerhetsadministratörerna att upptäcka alla Privilegierade identiteter, visa granskningsrapporter och skapa åtkomst omdömen för att identifiera alla användare som är berättigad att aktivera admin-privilegier

Om du använder redan Azure AD Privileged Identity Management kan du justera tidsramar för Tidsbundna privilegier vid behov (till exempel underhållsfönster).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Fastställa exponering lösenordsbaserade inloggning protokoll (om du använder Exchange Online)

Tidigare antas protokoll att kombinationer av användarnamn/lösenord har bäddats in i enheter, e-postkonton, telefoner och så vidare. Men nu med risken för cyber attacker i molnet, rekommenderar vi du identifiera alla potentiella användare som om deras autentiseringsuppgifter skulle komprometteras, kan oåterkalleligt i organisationen och exkluderar dem från att kunna logga in på sin e-post via användarnamn / lösenord genom att implementera kraven för stark autentisering och villkorlig åtkomst. 

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>Slutföra en roller granska bedömning för Office 365 roller (om du använder Office 365)

Utvärdera om alla administratörer användare är i rätt roller (ta bort och omtilldela enligt denna bedömning).

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>Granska den säkerhet incidenthantering metod som används i Office 365 och jämföra med din organisation

Du kan hämta den här rapporten från [säkerhet incidenthantering i Microsoft Office 365](https://www.microsoft.com/download/details.aspx?id=54302).

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Fortsätt att skydda lokala privilegierad administrativa konton

Om Azure Active Directory är ansluten till lokala Active Directory, följer du anvisningarna i den [säkerhet privilegierad åtkomst översikt över](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): i steg 2. Detta inkluderar distribuera arbetsstationer med privilegierad åtkomst för alla administratörer, kräver MFA, med bara tillräckligt administratör för domänkontrollanter, vilket minskar risken för angrepp på domäner, distribuera ATA för identifiering av attacker.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Ytterligare åtgärder för organisationer som hanterar åtkomst till Azure

#### <a name="establish-integrated-monitoring"></a>Upprätta integrerad övervakning

Den [Azure Security Center](../security-center/security-center-intro.md) ger integrerad säkerhet övervaka och hantera principer för dina Azure-prenumerationer, hjälper till att upptäcka hot som annars kanske skulle förbli oupptäckta och fungerar med ett vittomfattande ekosystem med säkerhet lösningar.

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Inventera dina Privilegierade konton värdbaserade virtuella datorer

I de flesta fall behöver du inte ge användare obegränsad behörighet till alla Azure-prenumerationer eller resurser. Du kan använda Azure AD-administratörsroller för att särskilja uppgifter i din organisation och ge bara mängden åtkomst till användare som behöver utföra särskilda jobb. Till exempel använda Azure AD-administratörsroller så att en administratör som hanterar endast virtuella datorer i en prenumeration medan en annan kan hantera SQL-databaser inom samma prenumeration. Mer information finns i [Kom igång med rollbaserad åtkomstkontroll i Azure portal](../role-based-access-control/overview.md).

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Implementera PIM för Azure AD-administratörsroller

Använda Privileged identity Management med administratörsroller i Azure AD för att hantera, styr och övervaka åtkomst till Azure-resurser. Med hjälp av PIM skyddar Privilegierade konton från cyber attacker genom att minska exponeringstid privilegier och öka din insyn i deras användning via rapporter och aviseringar. Mer information finns i [hantera RBAC åtkomst till Azure-resurser med Privileged Identity Management](../role-based-access-control/pim-azure-resource.md).

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Använda Azure logganalys-integrering för att skicka relevanta Azure loggar till din SIEM-system 

Azure logganalys-integrering gör det möjligt att integrera loggarna från Azure-resurser till din organisations befintliga säkerhetsinformation och händelsen Management SIEM ()-system. [Azure logganalys integration](../security/security-azure-log-integration-overview.md) samlar in Windows-händelser från Windows Loggboken och Azure-resurser från Azure aktivitetsloggar, Azure Security Center-aviseringar och Azure diagnostikloggar. 


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Ytterligare åtgärder för organisationer som hanterar åtkomst till andra molnappar via Azure AD

#### <a name="implement-user-provisioning-for-connected-apps"></a>Implementera användaretablering för anslutna appar

Azure AD kan du automatisera skapandet, underhållet och borttagningen av användaridentiteter i molnprogram (SaaS), till exempel Dropbox Salesforce, ServiceNow och så vidare. Mer information finns i [automatisera användaretablering och avetablering för SaaS-program med Azure AD](active-directory-saas-app-provisioning.md).

#### <a name="integrate-information-protection"></a>Integrera informationsskydd

MCAS kan du undersöka filer och ange principer baserat på etiketter för klassificering av Azure Information Protection, aktivera bättre överblick och kontroll över dina data i molnet. Söka och klassificera filer i molnet och installera Azure information protection etiketter. Mer information finns i [Azure Information Protection integration](https://docs.microsoft.com/cloud-app-security/azip-integration).

#### <a name="configure-conditional-access"></a>Konfigurera villkorlig åtkomst

Konfigurera villkorlig åtkomst baserat på en grupp, plats och programmet känslighet för [SaaS-appar](https://azure.microsoft.com/overview/what-is-saas/) och Azure AD anslutna appar. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Övervaka aktiviteten i anslutna molnappar

För att säkerställa att användarnas åtkomst är skyddad i samt anslutna program, rekommenderar vi att du utnyttja [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). Detta skyddar enterprise-åtkomst till molnappar, förutom att skydda dina administratörskonton genom att du kan:

* Utöka synlighet och kontroll av molnappar
* Skapa principer för åtkomst, aktiviteter och delning av data
* Automatiskt identifiera riskfyllda aktiviteter och onormalt beteende hot
* Förhindra dataläckor
* Minimera risken och automatisk hot förebyggande och tvingande principer

Cloud App Security SIEM-agenten integreras Cloud App Security med SIEM-server för att aktivera centraliserad övervakning av Office 365-aviseringar och aktiviteter. Körs på servern och tar emot aviseringar och aktiviteter från Cloud App Security och strömmar dem till SIEM-servern. Mer information finns i [SIEM integrering](https://docs.microsoft.com/cloud-app-security/siem).

## <a name="stage-4-continue-building-defenses-to-a-more-proactive-security-posture"></a>Steg 4: Fortsätt bygga försvar för att förebygga säkerhetstillståndet


![Steg 4](./media/admin-roles-best-practices/stage-four.png)

Steg 4 i Översikt bygger på synlighet från steg 3 och är utformad för att implementeras i sex månader och framåt. Slutför en översikt över-hjälper dig att utveckla strong privilegierad åtkomst skydd mot potentiella attacker som för närvarande tillgänglig idag. Tyvärr säkerhetshot ständigt utvecklas och SKIFT, så vi rekommenderar att du visa säkerhet som en pågående process som fokuserar på att öka kostnaderna och minska frekvensen lyckade motståndare målobjekt för din miljö.

Skydda privilegierad åtkomst är ett viktigt första steg att upprätta säkerhet garantier för företagets tillgångar i en modern organisation, men det är inte endast en del av en fullständig säkerhetsprogrammet som inkluderar element, till exempel princip, åtgärder, information Ange pågående säkerhet garantier säkerhet, servrar, program, datorer, enheter, molninfrastrukturresurs och andra komponenter. 

Utöver att hantera dina Privilegierade konton, rekommenderar vi att du läser följande kontinuerligt:

* Se till att administratörer tänker göra dagliga verksamhet som icke-privilegierade användare.
* Endast bevilja privilegierad åtkomst vid behov och ta bort den efteråt (just-in-time).
* Behåll och granska Granska aktivitet som rör Privilegierade konton.

Mer information om hur du skapar en plan för fullständig säkerhet finns [Microsoft IT arkitektur molnresurser](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources). Mer information om att Microsoft-tjänster för att hjälpa till med någon av dessa avsnitt kontakta Microsofts kundtjänst eller se [skapa kritiska cyber försvar för att skydda företaget](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx).

Den här pågående slutskedet skyddade privilegierad åtkomst översikt innehåller följande komponenter.

### <a name="general-preparation"></a>Allmän förberedelse

#### <a name="review-admin-roles-in-azure-active-directory"></a>Granska administratörsroller i Azure Active Directory 

Avgöra om aktuella inbyggda Azure AD-administratörsroller är fortfarande uppdaterade och kontrollerar att användarna finns bara i roller och delegeringar som de behöver för motsvarande behörigheter. Du kan ange separata administratörer att hantera olika funktioner med Azure AD. Mer information finns i [Tilldela administratörsroller i Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Granska användare som har administration av Azure AD-anslutna enheter

Mer information finns i [hur du konfigurerar hybrid Azure Active Directory-anslutna enheter](device-management-hybrid-azuread-joined-devices-setup.md).

#### <a name="review-members-of-built-in-office-365-admin-roleshttpssupportofficecomarticleabout-office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d"></a>Granska medlemmar i [inbyggda administratörsroller i Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)
Om du använder Office 365.
‎
#### <a name="validate-incident-response-plan"></a>Validera incidentsvarsplanen

För att förbättra din plan rekommenderar Microsoft att du regelbundet kontrollera att planen fungerar som förväntat:

* Gå igenom din befintliga översikt över att se vad uppfylldes
* Baserat på postmortem analys, ändra befintliga eller definiera nya bästa praxis
* Se till att dina uppdaterade incidentsvarsplanen och bästa praxis ska distribueras i organisationen


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Ytterligare åtgärder för organisationer som hanterar åtkomst till Azure 

Bestäm om du behöver [överför ägarskapet för en Azure-prenumeration till ett annat konto](../billing/billing-subscription-transfer.md).
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>”Nödkonto”: vad du gör i nödfall

![Nödsituation](./media/admin-roles-best-practices/emergency.jpeg)

1. Meddela viktiga chefer och säkerhet polis med relevant information om incidenten.

2. Granska dina attack playbook. 

3. Komma åt din ”nödkonto” konto användarnamnet och lösenordet att logga in på Azure AD. 

4. Få hjälp från Microsoft av [öppna ett Azure supportbegäran](../azure-supportability/how-to-create-azure-support-request.md).

5. Titta på den [Azure AD-inloggning rapporter](active-directory-reporting-azure-portal.md). Det kan finnas en fördröjning mellan en händelse inträffar och när det ingår i rapporten.

6. För hybridmiljöer om federerade och din AD FS-servern inte är tillgänglig kan du behöva tillfälligt vill växla från federerad autentisering att använda lösenordets hash-synkronisering. Detta återställer domänfederation tillbaka till hanterade autentisering tills AD FS-servern blir tillgänglig.

7. Övervaka e-post för privilegierade konton.

8. Kontrollera att du sparar säkerhetskopior av relevanta loggfiler för potentiella juridiska och kriminaltekniska undersökningar.

Läs mer om hur Microsoft Office 365 hanterar säkerhetsincidenter [säkerhet incidenthantering i Microsoft Office 365](http://aka.ms/Office365SIM).

## <a name="faq-common-questions-we-receive-regarding-securing-privileged-access"></a>Vanliga frågor och svar: Vanliga frågor vi får om skydda privilegierad åtkomst  


**F:** vad gör jag om jag inte har implementerats alla komponenter för säker åtkomst?

**Svar:** definiera minst två break-om kontot, tilldela MFA till Privilegierade administratörskonton och separata användarkonton från globala administratörskonton.


**F:** efter ett intrång vad är det översta problem som behöver åtgärdas först?

**Svar:** vara säker på att du kräver den starkaste autentiseringen för hög exponeras enskilda användare.


**F:** vad händer om våra Privilegierade administratörer har inaktiverats?

**Svar:** skapa en globala administratörskonto som hålls alltid uppdaterad.


**F:** vad händer om det finns endast en global administratör åt vänster och går inte att nå? 

**Svar:** använda en break om konton för att få omedelbar privilegierad åtkomst.


**F:** hur kan jag skydda administratörer inom organisationen?

**Svar:** har administratörer alltid göra dagliga verksamhet som ”utan privilegier” standardanvändare.
 

**F:** vad är bästa praxis för att skapa administratörskonton i Azure AD?

**Svar:** reservera privilegierad åtkomst för specifika administrativa uppgifter.


**F:** vilka verktyg som finns för att minska beständiga administratörsåtkomst?

**Svar:** Privileged Identity Management (PIM) och Azure AD-administratörsroller.


**F:** vad är Microsoft placering på synkronisering administratörskonton till Azure AD?

**Svar:** nivå 0 administratörskonton (inklusive konton, grupper och andra resurser som har direkt eller indirekt administrativ kontroll av AD-skogen, domäner eller domänkontrollanter och alla tillgångar) används endast för lokala AD-konton och är normalt inte synkroniseras för Azure AD för molnet. 


**F:** hur håller vi administratörer tilldelas slumpmässigt administratörsåtkomst i portal?

**Svar:** använder icke-privilegierade konton för alla användare och de flesta administratörer. Börja med att utveckla en storleken på organisationen för att avgöra vilka bör vara privilegierad några administratörskonton. Och övervaka för nyskapade administrativa användare.


## <a name="next-steps"></a>Nästa steg

* [Microsoft Trust Center för produkten säkerhet](https://www.microsoft.com/en-us/trustcenter/security) – säkerhetsfunktioner i Microsoft cloud produkter och tjänster

* [Microsoft Trust Center - kompatibilitet](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) – Microsofts omfattande uppsättning efterlevnad erbjudanden för molntjänster

* [Anvisningar om hur du utföra en riskbedömning](https://www.microsoft.com/en-us/trustcenter/guidance/risk-assessment) -Hantera säkerhet och efterlevnad kraven för Microsofts molntjänster

### <a name="other-ms-online-services"></a>Andra onlinetjänster MS 

* [Microsoft Intune Security](https://www.microsoft.com/en-us/trustcenter/security/intune-security) – Intune erbjuder hantering av mobila enheter, hantering av mobila program och funktioner för hantering av datorer från molnet.

* [Microsoft Dynamics 365 säkerhet](https://www.microsoft.com/en-us/trustcenter/security/dynamics365-security) – Dynamics 365 är Microsoft molnbaserad lösning som kombinerar customer relationship management (CRM) och resursplanering (ERP)-funktioner.

 
