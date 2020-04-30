---
title: Metod tips för säker administratörs åtkomst – Azure AD | Microsoft Docs
description: Se till att organisationens administrativa åtkomst-och administratörs konton är säkra. För system arkitekter och IT-proffs som konfigurerar Azure AD, Azure och Microsoft Online Services.
services: active-directory
keywords: ''
author: curtand
manager: daveba
ms.author: curtand
ms.date: 11/13/2019
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.custom: it-pro
ms.reviewer: martincoetzer; MarkMorow
ms.collection: M365-identity-device-management
ms.openlocfilehash: 512efa959ccb78533845cd1f376318394b5c377b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82129170"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Skydda privilegierad åtkomst för hybrid- och molndistributioner i Azure AD

Säkerheten för de flesta eller alla företags till gångar i den moderna organisationen beror på integriteten hos de privilegierade konton som administrerar och hanterar IT-system. Skadliga aktörer, inklusive cyberhot-angripare, använder ofta administratörs konton och andra delar av privilegie rad åtkomst för att försöka snabbt få åtkomst till känsliga data och system som använder stöld angrepp. För moln tjänster är förebyggande och svar gemensamma ansvar för moln tjänst leverantören och kunden. Mer information om de senaste hoten till slut punkter och molnet finns i [Microsoft Security Intelligence-rapporten](https://www.microsoft.com/security/operations/security-intelligence-report). Den här artikeln hjälper dig att utveckla en översikt över hur du kan stänga luckorna mellan dina aktuella planer och de rikt linjer som beskrivs här.

> [!NOTE]
> Microsoft strävar efter att ha den högsta nivån av förtroende, transparens, standard avvikelse och regelefterlevnad. Lär dig mer om hur Microsoft Global incident Response-teamet minimerar effekterna av attacker mot moln tjänster och hur säkerheten är inbyggd i Microsofts affärs produkter och moln tjänster på [Microsoft Trust Center – säkerhet](https://www.microsoft.com/trustcenter/security) och Microsofts efterlevnadsprinciper på [Microsoft Trust Center – efterlevnad](https://www.microsoft.com/trustcenter/compliance).

<!--## Risk management, incident response, and recovery preparation

A cyber-attack, if successful, can shut down operations not just for a few hours, but in some cases for days or even weeks. The collateral damage, such as legal ramifications, information leaks, and media coverage, could potentially continue for years. To ensure effective company-wide risk containment, cybersecurity and IT pros must align their response and recovery processes. To reduce the risk of business disruption due to a cyber-attack, industry experts recommend you do the following:

* As part of your risk management operations, establish a crisis management team for your organization that is responsible for managing all types of business disruptions.

* Compare your current risk mitigations, incident response, and recovery plan with industry best practices for managing a business disruption before, during, and after a cyber-attack.

* Develop and implement a roadmap for closing the gaps between your current plans and the best practices described in this document.


## Securing privileged access for hybrid and cloud deployments

does the article really start here?-->
För de flesta organisationer är säkerheten för företagets till gångar beroende av integriteten hos de privilegierade konton som administrerar och hanterar IT-system. Cyberhot-angripare fokuserar på privilegie rad åtkomst till infrastruktur system (till exempel Active Directory och Azure Active Directory) för att få åtkomst till en organisations känsliga data. 

Traditionella metoder som fokuserar på att skydda ingångs-och utförsel platser i ett nätverk som primär säkerhetsperimeter är mindre effektiva på grund av ökningen av användningen av SaaS-appar och personliga enheter på Internet. Ersättningar för nätverkets säkerhetsperimeter i ett komplext modernt företag är autentiserings- och auktoriseringskontroller i en organisations identitetslager.

Privilegierade administrativa konton styrs effektivt för den här nya "säkerhetsperimetern". Det är viktigt att skydda privilegie rad åtkomst, oavsett om miljön är lokal, moln eller hybrid lokala och värdbaserade tjänster i molnet. Att skydda administrativ åtkomst mot bestämda angripare kräver att du tar en komplett och genomtänkt metod för att isolera organisationens system från risker. 

För att skydda privilegie rad åtkomst krävs ändringar i

* Processer, administrativa metoder och kunskaps hantering
* Tekniska komponenter, till exempel värd skydd, konto skydd och identitets hantering

Det här dokumentet fokuserar främst på att skapa en översikt för att skydda identiteter och åtkomst som hanteras eller rapporteras i Azure AD, Microsoft Azure, Office 365 och andra moln tjänster. För organisationer som har lokala administratörs konton kan du läsa mer i rikt linjerna för lokal och hybrid privilegie rad åtkomst som hanteras från Active Directory för att [skydda privilegie rad åtkomst](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access). 

> [!NOTE] 
> Vägledningen i den här artikeln handlar främst om funktioner i Azure Active Directory som ingår i Azure Active Directory Premium planerna P1 och P2. Azure Active Directory Premium P2 ingår i EMS E5 Suite och Microsoft 365 E5 Suite. Den här vägledningen förutsätter att din organisation redan har Azure AD Premium P2-licenser som har köpts för dina användare. Om du inte har dessa licenser kanske en del av vägledningen inte gäller för din organisation. Den globala administratören (eller den globala administratören) är också synonym med "företags administratör" eller "innehavaradministratör" i den här artikeln.

## <a name="develop-a-roadmap"></a>Utveckla en översikt 

Microsoft rekommenderar att du utvecklar och följer en översikt för att skydda privilegie rad åtkomst mot cyberhot-attacker. Du kan alltid justera din översikt så att den passar dina befintliga funktioner och särskilda krav i din organisation. Varje steg i översikten bör öka kostnaderna och svårigheten för angripare att attackera privilegie rad åtkomst för dina lokala, molnbaserade och hybrid till gångar. Microsoft rekommenderar följande fyra översikts steg: den rekommenderade översikten schemalägger de mest effektiva och snabbaste implementeringarna först, baserat på Microsofts upplevelser med cyberhot-attackens incident-och svars implementering. Tids linjerna för den här översikten är ungefärliga.

![Faser i översikten med tids rader](./media/directory-admin-roles-secure/roadmap-timeline.png)

* Steg 1 (24-48 timmar): kritiska objekt som vi rekommenderar att du gör omedelbart

* Steg 2 (2-4 veckor): minimera de oftast använda angrepps metoderna

* Steg 3 (1-3 månader): utveckla synlighet och bygga fullständig kontroll över administratörs aktivitet

* Steg 4 (sex månader och senare): Fortsätt att skapa försvar för ytterligare härdning av din säkerhets plattform

Det här översikts ramverket är utformat för att maximera användningen av Microsofts tekniker som du kanske redan har distribuerat. Du kan också dra nytta av viktiga aktuella och kommande säkerhets tekniker och integrera säkerhets verktyg från andra leverantörer som du redan har distribuerat eller som överväger att distribuera. 

## <a name="stage-1-critical-items-that-we-recommend-you-do-right-away"></a>Steg 1: kritiska objekt som vi rekommenderar att du gör omedelbart

![Steg 1 viktiga objekt att göra först](./media/directory-admin-roles-secure/stage-one.png)

Steg 1 i översikten fokuserar på kritiska uppgifter som är snabba och enkla att implementera. Vi rekommenderar att du gör dessa objekt direkt inom de första 24-48 timmarna för att säkerställa en grundläggande nivå av säker privilegie rad åtkomst. Det här steget i den skyddade översikten över privilegie rad åtkomst innehåller följande åtgärder:

### <a name="general-preparation"></a>Allmän förberedelse

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Aktivera Azure AD Privileged Identity Management

Om du inte redan har aktiverat Azure AD Privileged Identity Management (PIM) gör du det i produktions klient organisationen. När du har aktiverat Privileged Identity Management får du e-postaviseringar om roll ändringar för privilegie rad åtkomst. De här meddelandena ger tidig varning när ytterligare användare läggs till i privilegierade roller i din katalog.

Azure AD Privileged Identity Management ingår i Azure AD Premium P2 eller EMS E5. Dessa lösningar hjälper dig att skydda åtkomsten till program och resurser i den lokala miljön och i molnet. Om du inte redan har Azure AD Premium P2 eller EMS E5 och vill utvärdera fler av funktionerna som refereras i den här översikten, registrerar du dig för den [Enterprise Mobility + Security kostnads fria 90-dagars utvärderings versionen](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial). Använd dessa licens test för att prova Azure AD Privileged Identity Management och Azure AD Identity Protection, för att övervaka aktivitet med avancerad säkerhets rapportering, granskning och aviseringar i Azure AD.

När du har aktiverat Azure AD Privileged Identity Management:

1. Logga in på [Azure Portal](https://portal.azure.com/) med ett konto som är en global administratör för din produktions klient.

2. Om du vill välja den klient där du vill använda Privileged Identity Management väljer du ditt användar namn i det övre högra hörnet av Azure Portal.

3. På Azure Portal-menyn väljer du **alla tjänster** och filtrerar listan för **Azure AD Privileged Identity Management**.

4. Öppna Privileged Identity Management från listan **alla tjänster** och fäst den på din instrument panel.

Den första personen som ska använda Azure AD Privileged Identity Management i din klient organisation tilldelas automatiskt rollen **säkerhets administratör** och **administratör för privilegie rad roll** i klienten. Endast privilegierade roll administratörer kan hantera roll tilldelningar för Azure AD-katalog för användare. När du lägger till Azure AD Privileged Identity Management visas dessutom säkerhets guiden som vägleder dig genom den inledande identifierings-och tilldelnings upplevelsen. Du kan avsluta guiden utan att göra några ytterligare ändringar för tillfället. 

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Identifiera och kategorisera konton som har privilegier för hög privilegier 

När du har aktiverat Azure AD Privileged Identity Management kan du Visa de användare som finns i katalog rollerna global administratör, privilegie rad roll administratör, Exchange Online-administratör och SharePoint Online-administratör. Om du inte har Azure AD PIM i din klient kan du använda [PowerShell-API: et](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0). Börja med den globala administratörs rollen eftersom den här rollen är generisk: en användare som har tilldelats den här administratörs rollen har samma behörigheter för alla moln tjänster som din organisation prenumererar på, oavsett om de har tilldelats den här rollen i Microsoft 365 administrations Center, Azure Portal eller med hjälp av Azure AD-modulen för Microsoft PowerShell. 

Ta bort alla konton som inte längre behövs i dessa roller. Kategorisera sedan de återstående konton som har tilldelats administratörs roller:

* Individuellt tilldelad till administrativa användare och kan även användas för icke-administrativa syfte (till exempel personligt e-post)
* Individuellt tilldelad till administrativa användare och endast avsedd för administrativa syfte
* Delas över flera användare
* För situationer med nöd åtkomst till bryta glas
* För automatiserade skript
* För externa användare

#### <a name="define-at-least-two-emergency-access-accounts"></a>Definiera minst två nödfalls åtkomst konton 

Se till att du inte hamnar i en situation där de kan vara oavsiktligt utelåsta från administration av din Azure AD-klient på grund av att det inte går att logga in eller aktivera en befintlig enskild användares konto som administratör. Om organisationen till exempel är federerad till en lokal identitetsprovider, kan identitets leverantören vara otillgänglig så att användare inte kan logga in lokalt. Du kan minska effekten av haveri brist på administrativ åtkomst genom att lagra två eller fler konton för nöd åtkomst i din klient organisation.

Med hjälp av konton för nöd åtkomst kan organisationer begränsa privilegie rad åtkomst i en befintlig Azure Active Directorys miljö. Dessa konton är mycket privilegierade och har inte tilldelats till vissa individer. Konton för nöd åtkomst är begränsade till nödfall för scenarier med "rast glas" där normala administrativa konton inte kan användas. Organisationerna måste säkerställa målet med att kontrol lera och minska nöd kontots användning enbart för den tid för vilken det är nödvändigt.

Utvärdera de konton som är tilldelade eller berättigade till den globala administratörs rollen. Om du inte ser några moln konton som använder *. onmicrosoft.com-domänen (avsedd för "Break glas"-åtkomst till nöd situationer) skapar du dem. Mer information finns i [Hantera administratörs konton för nöd åtkomst i Azure AD](directory-emergency-access.md).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Aktivera Multi-Factor Authentication och registrera alla andra icke-federerade administratörs konton med hög privilegier

Kräv Azure Multi-Factor Authentication (MFA) vid inloggning för alla enskilda användare som permanent tilldelas till en eller flera av administratörs rollerna för Azure AD: global administratör, privilegie rad roll administratör, Exchange Online-administratör och SharePoint Online-administratör. Använd guiden för att aktivera [Multi-Factor Authentication (MFA) för dina administratörs konton](../authentication/howto-mfa-userstates.md) och se till att alla användare har [https://aka.ms/mfasetup](https://aka.ms/mfasetup)registrerat sig på. Mer information finns under steg 2 och steg 3 i guiden [Skydda åtkomsten till data och tjänster i Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e). 

## <a name="stage-2-mitigate-the-most-frequently-used-attack-techniques"></a>Steg 2: minimera de oftast använda angrepps metoderna

![Steg 2 begränsa angrepp som ofta används](./media/directory-admin-roles-secure/stage-two.png)

Steg 2 i översikten fokuserar på att begränsa de mest använda angrepps teknikerna för stöld och missbruk av autentiseringsuppgifter och kan implementeras inom cirka 2-4 veckor. Det här steget i översikten över säkra privilegie rad åtkomst innehåller följande åtgärder.

### <a name="general-preparation"></a>Allmän förberedelse

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>Genomför en inventering av tjänster, ägare och administratörer

Med ökningen i BYOD-och arbets-och arbets plats-principerna och tillväxten av trådlösa anslutningar i företag är det viktigt att du övervakar vem som ansluter till nätverket. En effektiv säkerhets granskning visar ofta enheter, program och program som körs i nätverket och som inte stöds av det, och därför kan det vara säkert. Mer information finns i [Översikt över Azure Security Management och Monitoring](../../security/fundamentals/management-monitoring-overview.md). Se till att du inkluderar alla följande uppgifter i inventerings processen. 

* Identifiera de användare som har administrativa roller och de tjänster som de kan hantera.
* Använd Azure AD PIM för att ta reda på vilka användare i din organisation som har administratörs åtkomst till Azure AD, inklusive ytterligare roller utöver de som anges i steg 1.
* Utöver de roller som definierats i Azure AD levereras Office 365 med en uppsättning administratörs roller som du kan tilldela till användare i din organisation. Varje administratörs roll mappar till vanliga affärs funktioner och ger personer i organisationen behörighet att utföra specifika uppgifter i [Microsoft 365 administrations centret](https://admin.microsoft.com). Använd Microsoft 365 administrations Center för att ta reda på vilka användare i din organisation som har administratörs åtkomst till Office 365, inklusive via roller som inte hanteras i Azure AD. Mer information finns i [om office 365-administratörs roller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) och [rekommenderade säkerhets metoder för Office 365](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center).
* Utför inventeringen i andra tjänster som din organisation använder, till exempel Azure, Intune eller Dynamics 365.
* Se till att dina administratörs konton (konton som används för administrations syfte, inte bara användares dagliga konton) har fungerande e-postadresser kopplade till dem och har registrerat sig för Azure MFA eller Använd MFA lokalt.
* Be användarna om sin affärs motivering för administrativ åtkomst.
* Ta bort administratörs åtkomsten för de individer och tjänster som inte behöver det.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Identifiera Microsoft-konton i administrativa roller som måste växlas till arbets-eller skol konton

Ibland återanvänder de ursprungliga globala administratörerna för en organisation sina befintliga Microsoft-konto autentiseringsuppgifter när de började använda Azure AD. Dessa Microsoft-konton bör ersättas av enskilda molnbaserade eller synkroniserade konton. 

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Se till att separata användar konton och vidarebefordran av e-post för globala administratörs konton 

Globala administratörs konton bör inte ha personliga e-postadresser, eftersom personliga e-postkonton ofta phished av cyberhot-angripare. För att avgränsa Internet risker (nätfiske-attacker, oavsiktlig webb läsning) från administrativa privilegier, skapar du dedikerade konton för varje användare med administratörs behörighet. 

Om du inte redan har gjort det skapar du separata konton för användare som ska kunna utföra globala administrativa uppgifter, för att se till att de inte oavsiktligt öppnar e-postmeddelanden eller kör program som är kopplade till sina administratörs konton. Se till att dessa konton har e-postmeddelandet vidarebefordras till en fungerande post låda.  

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Se till att lösen orden för administrativa konton nyligen har ändrats

Se till att alla användare har loggat in på sina administrativa konton och ändrat sina lösen ord minst en gång under de senaste 90 dagarna. Se också till att alla delade konton i vilka flera användare vet att lösen orden har ändrats nyligen.

#### <a name="turn-on-password-hash-synchronization"></a>Aktivera synkronisering av lösen ords-hash

Hash-synkronisering av lösen ord är en funktion som används för att synkronisera hashar av hashar av användarens lösen ord från en lokal Active Directory-instans till en molnbaserad Azure AD-instans. Även om du bestämmer dig för att använda Federation med Active Directory Federation Services (AD FS) (AD FS) eller andra identitets leverantörer, kan du välja att konfigurera Lösenordssynkronisering som en säkerhets kopia om din lokala infrastruktur, till exempel AD eller ADFS-servrar, inte fungerar eller tillfälligt blir otillgänglig. Detta gör att användarna kan logga in på tjänsten med samma lösen ord som de använder för att logga in på sina lokala AD-instanser. Dessutom tillåter den identitets skydd att identifiera komprometterade autentiseringsuppgifter genom att jämföra dessa lösen ord med lösen ord som är kända för att bli komprometterade, om en användare har utnyttjat sin e-postadress och lösen ord på andra tjänster som inte är anslutna till Azure AD.  Mer information finns i [implementera hash-synkronisering av lösen ord med Azure AD Connect Sync](../hybrid/how-to-connect-password-hash-synchronization.md).

#### <a name="require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users"></a>Kräv Multi-Factor Authentication (MFA) för användare i alla privilegierade roller samt exponerade användare

Azure AD rekommenderar att du kräver Multi-Factor Authentication (MFA) för alla dina användare, inklusive administratörer och andra användare som skulle ha en betydande inverkan om kontot har komprometterats (t. ex. finansiella chefer). Detta minskar risken för angrepp på grund av ett komprometterat lösen ord.

Aktivera:

* [MFA använder principer för villkorlig åtkomst](../authentication/howto-mfa-getstarted.md) för alla användare i din organisation.

Om du använder Windows Hello för företag kan MFA-kravet uppfyllas med hjälp av Windows Hello-inloggningen. Mer information finns i [Windows Hello](https://docs.microsoft.com/windows/uwp/security/microsoft-passport). 

#### <a name="configure-identity-protection"></a>Konfigurera identitets skydd 

Azure AD Identity Protection är ett algoritm-baserat övervaknings-och rapporterings verktyg som du kan använda för att identifiera potentiella sårbarheter som påverkar organisationens identiteter. Du kan konfigurera automatiserade svar på de identifierade misstänkta aktiviteterna och vidta lämpliga åtgärder för att lösa dem. Mer information finns i [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>Skaffa dina Office 365-säkra poäng (om du använder Office 365)

Säkra poäng visar vilka Office 365-tjänster du använder (som OneDrive, SharePoint och Exchange) och tittar sedan på dina inställningar och aktiviteter och jämför dem med en bas linje som upprättats av Microsoft. Du får ett resultat baserat på hur justerat du är med bästa säkerhets praxis. Alla som har administratörs behörighet (global administratör eller en anpassad administratörs roll) för en Office 365 Business Premium-eller Enterprise-prenumeration kan komma [https://securescore.office.com](https://securescore.office.com/)åt säkra poäng på.

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>Granska vägledningen för säkerhet och efterlevnad i Office 365 (om du använder Office 365)

[Planen för säkerhet och efterlevnad](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) beskriver tillvägagångs sättet för hur en Office 365-kund konfigurerar Office 365 och utnyttjar andra EMS-funktioner. Gå sedan igenom steg 3-6 i [Skydda åtkomsten till data och tjänster i office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) och guiden för hur du [övervakar säkerhet och efterlevnad i Office 365](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6).

#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>Konfigurera aktivitets övervakning i Office 365 (om du använder Office 365)

Du kan övervaka hur personer i din organisation använder Office 365-tjänster, så att du kan identifiera användare som har ett administratörs konto och som kanske inte behöver ha Office 365-åtkomst på grund av att de inte loggar in på dessa portaler. Mer information finns [i aktivitets rapporter i Microsoft 365 administrations centret](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263).

#### <a name="establish-incidentemergency-response-plan-owners"></a>Upprätta incidenter/svars plan för nöd situationer

Att utföra incident svar är effektivt ett komplext företag. Därför krävs det avsevärd planering och resurser för att upprätta en lyckad incident svars funktion. Det är viktigt att du kontinuerligt övervakar för cyberhot-attacker och upprättar procedurer för att prioritera hanteringen av incidenter. Effektiva metoder för att samla in, analysera och rapportera data är viktiga för att skapa relationer och upprätta kommunikation med andra interna grupper och planera ägare. Mer information finns i [Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717). 

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Skydda lokala privilegierade administratörs konton, om de inte redan gjorts

Om din Azure Active Directory klient är synkroniserad med lokal Active Directory följer du rikt linjerna i [säkerhets översikten för privilegie rad åtkomst](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): steg 1. Detta omfattar att skapa separata administratörs konton för användare som behöver utföra lokala administrativa uppgifter, distribuera arbets stationer med privilegie rad åtkomst för Active Directory administratörer och skapa unika lokala administratörs lösen ord för arbets stationer och servrar.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Ytterligare steg för organisationer som hanterar åtkomst till Azure

#### <a name="complete-an-inventory-of-subscriptions"></a>Slutför en inventering av prenumerationer

Använd Enterprise Portal och Azure Portal för att identifiera de prenumerationer i organisationen som är värdar för produktions program.

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Ta bort Microsoft-konton från administratörs roller

Microsoft-konton från andra program, till exempel Xbox, Live och Outlook, ska inte användas som administratörs konton för organisations prenumerationer. Ta bort administratörs status från alla Microsoft-konton och Ersätt med Azure Active Directory (till chris@contoso.comexempel) arbets-eller skol konton.

#### <a name="monitor-azure-activity"></a>Övervaka Azure-aktivitet

Azure-aktivitetsloggen innehåller historik över händelser på prenumerationsnivå i Azure. Den innehåller information om vem som skapade, uppdaterat och tagit bort vilka resurser och när dessa händelser inträffade. Mer information finns i [Granska och ta emot meddelanden om viktiga åtgärder i din Azure-prenumeration](../../azure-monitor/platform/quick-audit-notify-action-subscription.md).

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Ytterligare steg för organisationer som hanterar åtkomst till andra molnappar via Azure AD

#### <a name="configure-conditional-access-policies"></a>Konfigurera principer för villkorlig åtkomst

Förbered principer för villkorlig åtkomst för lokala och molnbaserade program. Om du har användare som är anslutna till arbets platsen kan du få mer information om hur du konfigurerar [lokal villkorlig åtkomst med hjälp av Azure Active Directory enhets registrering](../active-directory-device-registration-on-premises-setup.md).


## <a name="stage-3-build-visibility-and-take-full-control-of-admin-activity"></a>Steg 3: skapa synlighet och få fullständig kontroll över administratörs aktiviteten

![Steg 3 ta kontroll över administratörs aktivitet](./media/directory-admin-roles-secure/stage-three.png)

Steg 3 bygger på åtgärder från steg 2 och har utformats för att implementeras på cirka 1-3 månader. Det här steget i den skyddade översikten över privilegie rad åtkomst innehåller följande komponenter.

### <a name="general-preparation"></a>Allmän förberedelse

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Slutför en åtkomst granskning av användare i administratörs roller

Fler företags användare får privilegie rad åtkomst via moln tjänster, vilket kan leda till en ökande ohanterad plattform. Detta inkluderar användare som är globala administratörer för Office 365, Azure-prenumerations administratörer och användare som har administratörs åtkomst till virtuella datorer eller via SaaS-appar. I stället bör organisationer ha alla anställda, särskilt administratörer, hantera dagliga affärs transaktioner som användare utan privilegier och bara ta med administratörs behörighet efter behov. Eftersom antalet användare i administratörs rollerna kan ha vuxit sedan det första införandet, fullständig åtkomst granskningar för att identifiera och bekräfta alla användare som är berättigade att aktivera administratörs behörighet. 

Gör följande:

* Bestäm vilka användare som är Azure AD-administratörer, aktivera administrations åtkomst på begäran, just-in-Time-administratör och rollbaserade säkerhets kontroller.
* Konvertera användare som inte har någon tydlig motivering för administratör privilegie rad åtkomst till en annan roll (om det inte finns någon berättigad roll, ta bort dem).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Fortsätt distributionen av starkare autentisering för alla användare 

Kräv C-Suite-chefer, högnivå ansvariga, viktiga IT-och säkerhets personal och andra användare som är mycket utsatta för att ha modern, stark autentisering, till exempel Azure MFA eller Windows Hello. 

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Använda dedikerade arbets stationer för administration för Azure AD

Angripare kan försöka rikta in sig på privilegierade konton för att få åtkomst till en organisations data och system, så att de kan störa integriteten och äktheten på data genom skadlig kod som ändrar program logiken eller snooping som administratören anger en autentiseringsuppgift. Arbetsstationer för privilegierad åtkomst (PAW, Privileged Access Workstation) ger tillgång till ett dedikerat operativsystem för känsliga uppgifter som är skyddat mot Internetattacker och hotvektorer. Genom att hålla känsliga uppgifter och konton åtskilda från de vanliga arbetsstationerna för daglig användning kan du effektivt skydda infrastrukturen mot nätfiskeattacker, attacker mot sårbarheter i program och operativsystem, olika typer av personifieringsattacker och attacker som försöker stjäla autentiseringsuppgifter, t.ex. loggning av tangenttryckningar, Pass-The-Hash och Pass-The-Ticket. Genom att distribuera arbets stationer med privilegie rad åtkomst kan du minska risken för att administratörer anger autentiseringsuppgifter för administratörer, förutom i en stationär miljö som har härdats. Mer information finns i [arbets stationer med privilegie rad åtkomst](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations).

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Granska National Institute of Standards och teknik rekommendationer för hantering av incidenter 

National Institute of Standards and Technology (NIST) innehåller rikt linjer för incident hantering, särskilt för analys av incident-relaterade data och bestämning av lämpligt svar på varje incident. Mer information finns i [(NIST) dator säkerhets incident hanterings guide (SP 800-61, revision 2)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Implementera Privileged Identity Management (PIM) för JIT till ytterligare administrativa roller

För Azure Active Directory använder du [Azure AD Privileged Identity Managements](../privileged-identity-management/pim-configure.md) funktion. Tidsbegränsad aktivering av privilegierade roller fungerar genom att göra det möjligt att:

* Aktivera administratörs behörighet för att utföra en speciell uppgift
* Tillämpa MFA under aktiverings processen
* Använd aviseringar för att informera administratörer om out-of-band-ändringar
* Gör det möjligt för användare att behålla vissa behörigheter för en förkonfigurerad tids period
* Tillåt säkerhets administratörer att identifiera alla privilegierade identiteter, Visa gransknings rapporter och skapa åtkomst granskningar för att identifiera alla användare som är berättigade att aktivera administratörs behörighet

Om du redan använder Azure AD Privileged Identity Management justerar du tids ramarna för tidsbegränsade privilegier vid behov (till exempel underhålls fönster).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Bestäm exponering för lösenordsbaserade inloggnings protokoll (om du använder Exchange Online)

Tidigare antog protokollen att kombinationer av användar namn/lösen ord är inbäddade i enheter, e-postkonton, telefoner och så vidare. Men nu med risken för cyberhot-attacker i molnet, rekommenderar vi att du identifierar alla potentiella användare som, om deras autentiseringsuppgifter har komprometterats, kan vara oåterkalleliga för organisationen, och undanta dem från att kunna logga in på sin e-post via användar namn/lösen ord genom att implementera starka autentiseringskrav och villkorlig åtkomst. Du kan blockera [äldre autentisering med villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/block-legacy-authentication). Läs informationen om [hur du blockerar grundläggande autentisering](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online) via Exchange Online. 

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>Slutför en gransknings bedömning för roller för Office 365-roller (om du använder Office 365)

Utvärdera om alla administratörer användare har rätt roller (ta bort och omtilldela enligt denna utvärdering).

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>Granska den säkerhets incident hanterings metod som används i Office 365 och jämför med din egen organisation

Du kan hämta den här rapporten från [hantering av säkerhets incidenter i Microsoft Office 365](https://www.microsoft.com/download/details.aspx?id=54302).

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Fortsätt att skydda lokala privilegierade administratörs konton

Om din Azure Active Directory är ansluten till den lokala Active Directory följer du rikt linjerna i [översikten över säkerhets privilegie rad åtkomst](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): steg 2. Detta omfattar distribution av arbets stationer med privilegie rad åtkomst för alla administratörer, vilket kräver MFA, med bara tillräckligt med administratör för DC-underhåll, vilket minskar risken för attacker på domäner, distribuerar ATA för attack identifiering.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Ytterligare steg för organisationer som hanterar åtkomst till Azure

#### <a name="establish-integrated-monitoring"></a>Upprätta integrerad övervakning

[Azure Security Center](../../security-center/security-center-intro.md) tillhandahåller integrerad säkerhetsövervakning och princip hantering i dina Azure-prenumerationer, hjälper till att upptäcka hot som annars kan gå vidare och fungerar med ett brett eko system med säkerhetslösningar.

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Inventera dina privilegierade konton i värd Virtual Machines

I de flesta fall behöver du inte ge användare obegränsade behörigheter till alla dina Azure-prenumerationer eller-resurser. Du kan använda administratörs roller i Azure AD för att åtskilja uppgifter i organisationen och endast bevilja åtkomst till användare som behöver utföra särskilda jobb. Använd till exempel Azure AD-administratörer för att låta en administratör hantera virtuella datorer i en prenumeration, medan en annan kan hantera SQL-databaser inom samma prenumeration. Mer information finns i [Kom igång med rollbaserad Access Control i Azure Portal](../../role-based-access-control/overview.md).

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Implementera PIM för administratörs roller i Azure AD

Använd Privileged Identity Management med administratörs roller i Azure AD för att hantera, kontrol lera och övervaka åtkomst till Azure-resurser. Med hjälp av PIM skyddar du privilegierade konton från cyberhot genom att minska exponerings tiden för privilegier och öka din insyn i användningen genom rapporter och aviseringar. Mer information finns i [hantera RBAC-åtkomst till Azure-resurser med Privileged Identity Management](../../role-based-access-control/pim-azure-resource.md).

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Använd Azures logg integrering för att skicka relevanta Azure-loggar till dina SIEM-system 

Med Azure logg integrering kan du integrera obehandlade loggar från dina Azure-resurser till din organisations befintliga SIEM-system (Security information and Event Management). [Azure logg integrering](../../security/fundamentals/azure-log-integration-overview.md) samlar in Windows-händelser från Windows Loggboken-loggar och Azure-resurser från Azure aktivitets loggar, Azure Security Center aviseringar och Azure-resurs loggar. 


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Ytterligare steg för organisationer som hanterar åtkomst till andra molnappar via Azure AD

#### <a name="implement-user-provisioning-for-connected-apps"></a>Implementera användar etablering för anslutna appar

Med Azure AD kan du automatisera genereringen, underhållet och borttagningen av användar identiteter i Cloud (SaaS)-program som Dropbox, Salesforce, ServiceNow och så vidare. Mer information finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure AD](../app-provisioning/user-provisioning.md).

#### <a name="integrate-information-protection"></a>Integrera informations skydd

Med MCAS kan du undersöka filer och ange principer baserat på Azure Information Protection klassificerings etiketter, vilket ger bättre synlighet och kontroll över dina data i molnet. Genomsök och klassificera filer i molnet och Använd Azure information Protection-etiketter. Mer information finns i [Azure information Protection-integrering](https://docs.microsoft.com/cloud-app-security/azip-integration).

#### <a name="configure-conditional-access"></a>Konfigurera villkorlig åtkomst

Konfigurera villkorlig åtkomst baserat på en grupp, plats och program känslighet för [SaaS-appar](https://azure.microsoft.com/overview/what-is-saas/) och Azure AD-anslutna appar. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Övervaka aktivitet i anslutna molnappar

För att säkerställa att användarnas åtkomst skyddas i anslutna program, rekommenderar vi att du använder [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). Detta skyddar företagets åtkomst till molnappar, förutom att skydda dina administratörs konton genom att göra det möjligt att:

* Utöka synlighet och kontroll över molnappar
* Skapa principer för åtkomst, aktiviteter och data delning
* Identifiera riskfyllda aktiviteter, onormala beteenden och hot automatiskt
* Förhindra data läckage
* Minimera risker och automatiserat skydd av hot och princip tillämpning

Cloud App Security SIEM-agenten integrerar Cloud App Security med SIEM-servern för att aktivera centraliserad övervakning av Office 365-aviseringar och-aktiviteter. Den körs på servern och hämtar aviseringar och aktiviteter från Cloud App Security och strömmar dem till SIEM-servern. Mer information finns i [Siem-integrering](https://docs.microsoft.com/cloud-app-security/siem).

## <a name="stage-4-continue-building-defenses-to-a-more-proactive-security-posture"></a>Steg 4: Fortsätt att skapa försvar för en mer proaktiv säkerhets position

![Steg 4 anta en proaktiv säkerhets position](./media/directory-admin-roles-secure/stage-four.png)

Steg 4 i översikten bygger på visningen av steg 3 och är utformad för att implementeras på sex månader och mer. Genom att slutföra en översikt kan du utveckla starka skyddade åtkomst skydd från potentiella attacker som för närvarande är kända och tillgängliga idag. Tyvärr utvecklas säkerhetshot ständigt och flyttas, så vi rekommenderar att du visar säkerheten som en kontinuerlig process som fokuserar på att öka kostnaderna och minska antalet lyckade angripare mål för din miljö.

Att skydda privilegie rad åtkomst är ett viktigt första steg för att upprätta säkerhets garantier för företags till gångar i en modern organisation, men det är inte den enda delen av ett komplett säkerhets program som innehåller element, till exempel policy, åtgärder, informations säkerhet, servrar, program, datorer, enheter, moln infrastruktur resurser och andra komponenter som ger kontinuerliga säkerhets garantier. 

Förutom att hantera dina privilegierade åtkomst konton rekommenderar vi att du läser igenom följande fort löp ande:

* Se till att administratörerna gör sin dagliga verksamhet som användare utan privilegier.
* Bevilja endast privilegie rad åtkomst vid behov och ta bort den efteråt (just-in-Time).
* Behåll och granska gransknings aktiviteten som rör privilegierade konton.

Mer information om hur du skapar en komplett säkerhets översikt finns i [resurser för IT-arkitektur i Microsoft Cloud](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources). Om du vill ha mer information om hur du engagerar Microsoft-tjänster för att hjälpa till med något av dessa ämnen kontaktar du din Microsoft-representant eller lär [dig att bygga viktiga cyberhot försvar för att skydda ditt företag](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx).

Det här slutliga pågående steget av den säkrade åtkomst planen innehåller följande komponenter.

### <a name="general-preparation"></a>Allmän förberedelse

#### <a name="review-admin-roles-in-azure-active-directory"></a>Granska administratörs roller i Azure Active Directory 

Ta reda på om de aktuella inbyggda administratörs rollerna för Azure AD fortfarande är uppdaterade och se till att användarna bara är i de roller och delegeringar som de behöver för motsvarande behörigheter. Med Azure AD kan du ange att separata administratörer ska kunna hantera olika funktioner. Mer information finns i [tilldela administratörs roller i Azure Active Directory](directory-assign-admin-roles.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Granska användare som har administration av Azure AD-anslutna enheter

Mer information finns i [så här konfigurerar du hybrid Azure Active Directory anslutna enheter](../device-management-hybrid-azuread-joined-devices-setup.md).

#### <a name="review-members-of-built-in-office-365-admin-roles"></a>Granska medlemmar av [inbyggda administratörs roller för Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)
Om du använder Office 365.
‎
#### <a name="validate-incident-response-plan"></a>Verifiera incident svars plan

För att förbättra din plan rekommenderar Microsoft att du regelbundet validerar att din plan fungerar som förväntat:

* Gå igenom din befintliga väg karta för att se vad som missades
* Baserat på Postmortem analys, ändra befintliga eller definiera nya metod tips
* Se till att din uppdaterade incident svars plan och bästa praxis distribueras i organisationen


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Ytterligare steg för organisationer som hanterar åtkomst till Azure 

Avgör om du behöver [överföra ägarskapet för en Azure-prenumeration till ett annat konto](../../cost-management-billing/manage/billing-subscription-transfer.md).
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Bryt glas": vad som händer i nödfall

![Konton för åtkomst till nöd brytar glas](./media/directory-admin-roles-secure/emergency.jpeg)

1. Meddela nyckel hanterare och säkerhets chefer med relevant information om incidenten.

2. Granska din angrepps Spelbok. 

3. Kom åt din "Break glas"-kontots användar namn/lösen ord kombination för att logga in på Azure AD. 

4. Få hjälp från Microsoft genom att [öppna en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md).

5. Titta på [inloggnings rapporterna för Azure AD](../reports-monitoring/overview-reports.md). Det kan finnas en fördröjning mellan en händelse som inträffar och när den tas med i rapporten.

6. Om federerade och din AD FS-server inte är tillgänglig i hybrid miljöer, kan du tillfälligt behöva växla från federerad autentisering till att använda hash-synkronisering av lösen ord. Detta återställer domän federationen tillbaka till hanterad autentisering tills den AD FS servern blir tillgänglig.

7. Övervaka e-post för privilegierade konton.

8. Se till att spara säkerhets kopior av relevanta loggar för potentiella kriminal tekniska och juridisk undersökning.

Mer information om hur Microsoft Office 365 hanterar säkerhets incidenter finns [i hantering av säkerhets incidenter i Microsoft Office 365](https://aka.ms/Office365SIM).

## <a name="faq-common-questions-we-receive-regarding-securing-privileged-access"></a>Vanliga frågor och svar: vanliga frågor vi får om att skydda privilegie rad åtkomst  

**F:** Vad gör jag om jag inte har implementerat några skyddade åtkomst komponenter ännu?

**Svar:** Definiera minst två Break-glas-konto, tilldela MFA till dina privilegierade administratörs konton och skilj användar konton från globala administratörs konton.

**F:** Vad är det viktigaste problemet som måste åtgärdas först efter en överträdelse?

**Svar:** Se till att du kräver den starkaste autentiseringen för mycket utsatta individer.

**F:** Vad händer om våra privilegierade administratörer har inaktiverats?

**Svar:** Skapa ett globalt administratörs konto som alltid hålls uppdaterat.

**F:** Vad händer om det bara finns en global administratör kvar och de inte kan nås? 

**Svar:** Använd ett av dina rast glas konton för att få omedelbar privilegie rad åtkomst.

**F:** Hur kan jag skydda administratörer inom organisationen?

**Svar:** Låt administratörer alltid utföra sin dagliga verksamhet som standard "ej privilegierade" användare.

**F:** Vilka är de bästa metoderna för att skapa administratörs konton i Azure AD?

**Svar:** Reservera privilegie rad åtkomst för vissa administratörs uppgifter.

**F:** Vilka verktyg finns för att minska beständiga administratörs åtkomst?

**Svar:** Privileged Identity Management (PIM) och Azure AD admin-roller.

**F:** Vad är Microsoft-positionen för att synkronisera administratörs konton till Azure AD?

**Svar:** Nivå 0-administratörs konton (inklusive konton, grupper och andra till gångar som har direkt eller indirekt administrativ kontroll över AD-skogen,-domänerna eller domän kontrol Lanterna och alla till gångar) används bara för lokala AD-konton och är vanligt vis inte synkroniserade för Azure AD för molnet.

**F:** Hur skyddar vi administratörer från att tilldela slumpmässig administratörs åtkomst i portalen?

**Svar:** Använd icke-privilegierade konton för alla användare och de flesta administratörer. Börja med att utveckla organisationens organisation för att avgöra vilka få administratörs konton som ska vara privilegierade. Och Övervakare för nyligen skapade administrativa användare.

## <a name="next-steps"></a>Nästa steg

* [Microsoft säkerhets Center för produkt säkerhet](https://www.microsoft.com/trustcenter/security) – säkerhetsfunktioner i Microsofts moln produkter och tjänster

* [Microsoft Trust Center – kompatibilitet](https://www.microsoft.com/trustcenter/compliance/complianceofferings) – Microsofts omfattande uppsättning erbjudanden för regelefterlevnad för moln tjänster

* [Vägledning om hur du utför en riskbedömning](https://www.microsoft.com/trustcenter/guidance/risk-assessment) – hantera krav på säkerhet och efterlevnad för Microsofts moln tjänster

### <a name="other-microsoft-online-services"></a>Andra Microsoft Online Services

* [Microsoft Intune säkerhet](https://www.microsoft.com/trustcenter/security/intune-security) – Intune tillhandahåller hantering av mobila enheter, hantering av mobila program och funktioner för dator hantering från molnet.

* [Microsoft Dynamics 365 Security](https://www.microsoft.com/trustcenter/security/dynamics365-security) – Dynamics 365 är den Microsoft-molnbaserade lösningen som förenar funktionerna för kund Relations hantering (CRM) och Enterprise Resource Planning (ERP).
