---
title: Metodtips för säker administratörsåtkomst – Azure AD | Microsoft-dokument
description: Kontrollera att organisationens administrativa åtkomst och administratörskonton är säkra. För systemarkitekter och IT-proffs som konfigurerar Azure AD, Azure och Microsoft Online Services.
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
ms.openlocfilehash: 1c46facb2d43137175730bf04fea0efec9c1ecbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266278"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Skydda privilegierad åtkomst för hybrid- och molndistributioner i Azure AD

Säkerheten för de flesta eller alla affärstillgångar i den moderna organisationen beror på integriteten hos de privilegierade konton som administrerar och hanterar IT-system. Skadliga aktörer, inklusive cyberangripare, riktar sig ofta till administratörskonton och andra element med privilegierad åtkomst för att snabbt få tillgång till känsliga data och system med hjälp av stöldattacker. För molntjänster är förebyggande och respons molntjänstleverantörens och kundens gemensamma ansvar. Mer information om de senaste hoten mot slutpunkter och molnet finns i [Microsoft Security Intelligence Report](https://www.microsoft.com/security/operations/security-intelligence-report). Den här artikeln kan hjälpa dig att utveckla en färdplan för att minska klyftorna mellan dina nuvarande planer och den vägledning som beskrivs här.

> [!NOTE]
> Microsoft strävar efter högsta möjliga förtroende, transparens, standardkonformance och regelefterlevnad. Läs mer om hur Microsofts globala incidenthanteringsteam minskar effekterna av attacker mot molntjänster och hur säkerhet är inbyggt i Microsofts affärsprodukter och molntjänster på [Microsoft Trust Center – Säkerhets-](https://www.microsoft.com/trustcenter/security) och Microsoft-efterlevnadsmål på Microsoft Trust Center - [Compliance](https://www.microsoft.com/trustcenter/compliance).

<!--## Risk management, incident response, and recovery preparation

A cyber-attack, if successful, can shut down operations not just for a few hours, but in some cases for days or even weeks. The collateral damage, such as legal ramifications, information leaks, and media coverage, could potentially continue for years. To ensure effective company-wide risk containment, cybersecurity and IT pros must align their response and recovery processes. To reduce the risk of business disruption due to a cyber-attack, industry experts recommend you do the following:

* As part of your risk management operations, establish a crisis management team for your organization that is responsible for managing all types of business disruptions.

* Compare your current risk mitigations, incident response, and recovery plan with industry best practices for managing a business disruption before, during, and after a cyber-attack.

* Develop and implement a roadmap for closing the gaps between your current plans and the best practices described in this document.


## Securing privileged access for hybrid and cloud deployments

does the article really start here?-->
För de flesta organisationer beror säkerheten för affärstillgångar på integriteten för de privilegierade konton som administrerar och hanterar IT-system. Cyberangripare fokuserar på privilegierad åtkomst till infrastruktursystem (till exempel Active Directory och Azure Active Directory) för att få åtkomst till en organisations känsliga data. 

Traditionella metoder som fokuserar på att säkra in- och utgångspunkterna i ett nätverk som den primära säkerhetsperimetern är mindre effektiva på grund av den ökade användningen av SaaS-appar och personliga enheter på Internet. Ersättningar för nätverkets säkerhetsperimeter i ett komplext modernt företag är autentiserings- och auktoriseringskontroller i en organisations identitetslager.

Privilegierade administrativa konton har effektivt kontroll över den här nya "säkerhetsperimetern". Det är viktigt att skydda privilegierad åtkomst, oavsett om miljön är lokal, molnig eller hybrid lokalt och molnbaserat. För att skydda administrativ åtkomst mot beslutsamma motståndare måste du ha en fullständig och genomtänkt metod för att isolera organisationens system från risker. 

För att skydda privilegierad åtkomst krävs ändringar i

* Processer, administrativ praxis och kunskapshantering
* Tekniska komponenter som värdförsvar, kontoskydd och identitetshantering

Det här dokumentet fokuserar främst på att skapa en översikt för att skydda identiteter och åtkomst som hanteras eller rapporteras i Azure AD, Microsoft Azure, Office 365 och andra molntjänster. För organisationer som har lokala administrativa konton finns i vägledningen för lokal och hybridbevilad åtkomst som hanteras från Active Directory vid [Skydda privilegierad åtkomst](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access). 

> [!NOTE] 
> Vägledningen i den här artikeln refererar främst till funktioner i Azure Active Directory som ingår i Azure Active Directory Premium-abonnemang P1 och P2. Azure Active Directory Premium P2 ingår i EMS E5-paketet och Microsoft 365 E5-paketet. Den här vägledningen förutsätter att din organisation redan har Azure AD Premium P2-licenser som köpts för dina användare. Om du inte har dessa licenser kanske en del av vägledningen inte gäller för din organisation. I den här artikeln är termen global administratör (eller global administratör) synonymt med "företagsadministratör" eller "klientadministratör".

## <a name="develop-a-roadmap"></a>Utveckla en färdplan 

Microsoft rekommenderar att du utvecklar och följer en färdplan för att skydda privilegierad åtkomst mot cyberattacker. Du kan alltid justera översikten så att den passar dina befintliga funktioner och specifika krav inom organisationen. Varje steg i färdplanen bör öka kostnaderna och svårigheten för motståndare att attackera privilegierad åtkomst för dina lokala, moln- och hybridtillgångar. Microsoft rekommenderar följande fyra översiktsstadier: Den här rekommenderade färdplanen schemalägger de mest effektiva och snabbaste implementeringarna först, baserat på Microsofts erfarenheter av implementering av incidenter och svar på cyber-attack. Tidslinjerna för den här färdplanen är ungefärliga.

![Faser i färdplanen med tidsgränser](./media/directory-admin-roles-secure/roadmap-timeline.png)

* Steg 1 (24-48 timmar): Kritiska föremål som vi rekommenderar att du gör direkt

* Steg 2 (2-4 veckor): Mildra de vanligaste attackteknikerna

* Steg 3 (1-3 månader): Skapa synlighet och bygga full kontroll över admin-aktivitet

* Steg 4 (sex månader och därefter): Fortsätt att bygga försvar för att ytterligare härda din säkerhetsplattform

Det här ramverket för översikt är utformat för att maximera användningen av Microsoft-teknik som du kanske redan har distribuerat. Du kan också dra nytta av viktiga aktuella och kommande säkerhetstekniker och integrera säkerhetsverktyg från andra leverantörer som du redan har distribuerat eller överväger att distribuera. 

## <a name="stage-1-critical-items-that-we-recommend-you-do-right-away"></a>Steg 1: Kritiska objekt som vi rekommenderar att du gör direkt

![Steg 1 Kritiska objekt att göra först](./media/directory-admin-roles-secure/stage-one.png)

Steg 1 i färdplanen är inriktad på kritiska uppgifter som är snabba och enkla att genomföra. Vi rekommenderar att du gör dessa få objekt direkt inom de första 24-48 timmarna för att säkerställa en grundläggande nivå av säker privilegierad åtkomst. I det här steget i färdplanen för skyddad privilegierad åtkomst ingår följande åtgärder:

### <a name="general-preparation"></a>Allmänna förberedelser

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Aktivera Azure AD-privilegierad identitetshantering

Om du inte redan har aktiverat Azure AD Privileged Identity Management (PIM) gör du det i din produktionsklientorganisation. När du har aktiverat Privilegierad identitetshantering får du e-postmeddelanden om privilegierade rolländringar. Dessa meddelanden ger tidig varning när ytterligare användare läggs till i mycket privilegierade roller i katalogen.

Azure AD Privileged Identity Management ingår i Azure AD Premium P2 eller EMS E5. Dessa lösningar hjälper dig att skydda åtkomsten till program och resurser i den lokala miljön och in i molnet. Om du inte redan har Azure AD Premium P2 eller EMS E5 och vill utvärdera fler av de funktioner som refereras i den här översikten registrerar du dig för [den kostnadsfria 90-dagars provperioden](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial)för Enterprise Mobility + Security . Använd dessa licenstestversioner för att prova Azure AD Privileged Identity Management och Azure AD Identity Protection för att övervaka aktivitet med hjälp av Azure AD avancerad säkerhetsrapportering, granskning och aviseringar.

När du har aktiverat Azure AD Privileged Identity Management:

1. Logga in på [Azure-portalen](https://portal.azure.com/) med ett konto som är en global administratör för din produktionsklient.

2. Om du vill välja den klient där du vill använda Privilegierad identitetshantering väljer du ditt användarnamn i det övre högra hörnet av Azure-portalen.

3. På Azure-portalmenyn väljer du **Alla tjänster** och filtrerar listan för Azure **AD Privileged Identity Management**.

4. Öppna Privilegierad identitetshantering från listan **Alla tjänster** och fäst den på instrumentpanelen.

Den första personen som använder Azure AD Privileged Identity Management i din klient tilldelas automatiskt **administratörsrollerna säkerhetsadministratör** och **privilegierad roll** i klienten. Endast privilegierade rolladministratörer kan hantera Azure AD-katalogrolltilldelningar för användare. När du har lagt till Azure AD Privileged Identity Management visas också säkerhetsguiden som hjälper dig genom den första identifierings- och tilldelningsupplevelsen. Du kan avsluta guiden utan att göra några ytterligare ändringar just nu. 

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Identifiera och kategorisera konton som har hög behörighet 

När du har aktiverat Azure AD Privileged Identity Management kan du visa de användare som finns i katalogrollerna Global administratör, Administratör för privilegierad roll, Exchange Online-administratör och SharePoint Online-administratör. Om du inte har Azure AD PIM i din klientorganisation kan du använda [PowerShell-API:et](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0). Börja med den globala administratörsrollen eftersom den här rollen är allmän: en användare som tilldelas den här administratörsrollen har samma behörigheter för alla molntjänster som din organisation har prenumererat på, oavsett om de har tilldelats den här rollen i Microsoft 365 administrationscenter, Azure-portalen eller genom att använda Azure AD-modulen för Microsoft PowerShell. 

Ta bort alla konton som inte längre behövs i dessa roller. Kategorisera sedan de återstående konton som har tilldelats administratörsroller:

* Individuellt tilldelade till administrativa användare, och kan också användas för icke-administrativa ändamål (till exempel personlig e-post)
* Individuellt tilldelade till administrativa användare och endast avsedda för administrativa ändamål
* Delas mellan flera användare
* För nödåtkomstscenarier för inbrott
* För automatiserade skript
* För externa användare

#### <a name="define-at-least-two-emergency-access-accounts"></a>Definiera minst två konton för nödåtkomst 

Se till att du inte hamnar i en situation där de oavsiktligt kan låsas ute från administrationen av din Azure AD-klient på grund av en oförmåga att logga in eller aktivera en befintlig enskild användares konto som administratör. Om organisationen till exempel är federerad med en lokal identitetsprovider kan den identitetsprovidern vara otillgänglig så att användarna inte kan logga in lokalt. Du kan minska effekten av oavsiktlig brist på administrativ åtkomst genom att lagra två eller flera konton för nödåtkomst i din klientorganisation.

Konton för nödåtkomst hjälper organisationer att begränsa privilegierad åtkomst i en befintlig Azure Active Directory-miljö. Dessa konton är mycket privilegierade och tilldelas inte till specifika personer. Konton för nödåtkomst är begränsade till nödsituationer för "break glass"-scenarier där normala administrativa konton inte kan användas. Organisationer måste se till att syftet med att kontrollera och minska nödkontots användning till endast den tid som det är nödvändigt.

Utvärdera de konton som har tilldelats eller är berättigade till den globala administratörsrollen. Om du inte såg några molnkonton med hjälp av domänen *.onmicrosoft.com (avsedd för nödåtkomst för "krossat glas") skapar du dem. Mer information finns [i Hantera administrativa konton för nödåtkomst i Azure AD](directory-emergency-access.md).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Aktivera multifaktorautentisering och registrera alla andra högprivilegierade icke-federerade administratörskonton för en användare

Kräv Azure Multi-Factor Authentication (MFA) vid inloggning för alla enskilda användare som permanent tilldelas en eller flera av Azure AD-administratörsrollerna: Global administratör, Administratör för privilegierad roll, Exchange Online-administratör och SharePoint Online-administratör. Använd guiden för att aktivera [MFA (Multifaktor Authentication) för dina administratörskonton](../authentication/howto-mfa-userstates.md) och se till att alla dessa användare har registrerat sig på [https://aka.ms/mfasetup](https://aka.ms/mfasetup). Mer information finns under steg 2 och steg 3 i guiden [Skydda åtkomsten till data och tjänster i Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e). 

## <a name="stage-2-mitigate-the-most-frequently-used-attack-techniques"></a>Steg 2: Mildra de vanligaste attackteknikerna

![Steg 2 Mildra attacker som används ofta](./media/directory-admin-roles-secure/stage-two.png)

Steg 2 i färdplanen fokuserar på att minska de mest använda attackteknikerna för stöld och missbruk och kan implementeras inom cirka 2-4 veckor. Den här fasen i färdplanen för skyddad privilegierad åtkomst innehåller följande åtgärder.

### <a name="general-preparation"></a>Allmänna förberedelser

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>Gör en inventering av tjänster, ägare och administratörer

Med ökningen av byod-principer (bring-your-own-device) och policyer för arbete från hemmet och tillväxten av trådlös anslutning i företag är det viktigt att du övervakar vem som ansluter till nätverket. En effektiv säkerhetsgranskning visar ofta enheter, program och program som körs i nätverket som inte stöds av IT och därför eventuellt inte är säkra. Mer information finns i [Översikt över Azure-säkerhetshantering och övervakning](../../security/fundamentals/management-monitoring-overview.md). Se till att du inkluderar alla följande uppgifter i lagerprocessen. 

* Identifiera de användare som har administrativa roller och de tjänster där de kan hantera.
* Använd Azure AD PIM för att ta reda på vilka användare i organisationen som har administratörsåtkomst till Azure AD, inklusive ytterligare roller utöver de som anges i steg 1.
* Utöver de roller som definierats i Azure AD levereras Office 365 med en uppsättning administratörsroller som du kan tilldela användare i organisationen. Varje administratörsroll mappar till vanliga affärsfunktioner och ger personer i organisationen behörighet att utföra specifika uppgifter i [Microsoft 365-administrationscentret](https://admin.microsoft.com). Använd administrationscentret för Microsoft 365 för att ta reda på vilka användare i organisationen som har administratörsåtkomst till Office 365, inklusive via roller som inte hanteras i Azure AD. Mer information finns i [Om office 365-administratörsroller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) och [metodtips för säkerhet för Office 365](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center).
* Utför lagret i andra tjänster som din organisation förlitar sig på, till exempel Azure, Intune eller Dynamics 365.
* Se till att dina administratörskonton (konton som används för administration, inte bara användarnas dagliga konton) har fungerande e-postadresser kopplade till dem och har registrerat sig för Azure MFA eller använda MFA lokalt.
* Be användarna om deras affärsmotivering för administrativ åtkomst.
* Ta bort administratörsåtkomst för de personer och tjänster som inte behöver den.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Identifiera Microsoft-konton i administrativa roller som måste växlas till arbets- eller skolkonton

Ibland återanvänder de första globala administratörerna för en organisation sina befintliga Microsoft-kontouppgifter när de började använda Azure AD. Dessa Microsoft-konton bör ersättas av enskilda molnbaserade eller synkroniserade konton. 

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Se till att separata användarkonton och vidarebefordran av e-post för globala administratörskonton 

Globala administratörskonton bör inte ha personliga e-postadresser, eftersom personliga e-postkonton regelbundet phished av cyberangripare. Skapa dedikerade konton för varje användare med administratörsbehörighet för att hjälpa till att separera internetrisker (nätfiskeattacker, oavsiktlig surfning) från administrativa privilegier. 

Om du inte redan har gjort det skapar du separata konton för användare att utföra globala administratörsuppgifter för att se till att de inte oavsiktligt öppnar e-postmeddelanden eller kör program som är associerade med deras administratörskonton. Se till att dessa konton har sin e-post vidarebefordras till en fungerande postlåda.  

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Se till att lösenorden för administrativa konton nyligen har ändrats

Se till att alla användare har loggat in på sina administrativa konton och ändrat sina lösenord minst en gång under de senaste 90 dagarna. Se också till att alla delade konton där flera användare vet att lösenordet har fått sina lösenord ändrade nyligen.

#### <a name="turn-on-password-hash-synchronization"></a>Aktivera synkronisering av lösenordsh hash

Synkronisering av lösenord hash-funktioner är en funktion som används för att synkronisera hashar av användarlösenordshã¤nder från en lokal Active Directory-instans till en molnbaserad Azure AD-instans. Även om du bestämmer dig för att använda federation med AD FS (Active Directory Federation Services) eller andra identitetsleverantörer kan du eventuellt ställa in synkronisering av lösenordshÃ¤nder som en säkerhetskopia om din lokala infrastruktur som AD- eller ADFS-servrar misslyckas eller inte är tillfälligt otillgängliga. Detta gör det möjligt för användare att logga in på tjänsten med samma lösenord som de använder för att logga in på sin lokala AD-instans. Dessutom tillåter det Identity Protection att identifiera komprometterade autentiseringsuppgifter genom att jämföra dessa lösenord hashar med lösenord som är kända för att äventyras, om en användare har utnyttjat samma e-postadress och lösenord på andra tjänster som inte är anslutna till Azure AD.  Mer information finns i [Implementera synkronisering av lösenordsh hash med Azure AD Connect-synkronisering](../hybrid/how-to-connect-password-hash-synchronization.md).

#### <a name="require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users"></a>Kräv MFA (Multi factor Authentication) för användare i alla privilegierade roller samt exponerade användare

Azure AD rekommenderar att du behöver MFA (Multi Factor Authentication) för alla dina användare, inklusive administratörer och alla andra användare som skulle ha en betydande inverkan om deras konto komprometterades (till exempel ekonomiansvariga). Detta minskar risken för en attack på grund av ett komprometterat lösenord.

Slå på:

* [MFA använder principer](../authentication/howto-mfa-getstarted.md) för villkorlig åtkomst för alla användare i organisationen.

Om du använder Windows Hello för företag kan MFA-kravet uppfyllas med hjälp av inloggningsupplevelsen för Windows Hello. Mer information finns i [Windows Hello](https://docs.microsoft.com/windows/uwp/security/microsoft-passport). 

#### <a name="configure-identity-protection"></a>Konfigurera identitetsskydd 

Azure AD Identity Protection är ett algoritmbaserat övervaknings- och rapporteringsverktyg som du kan använda för att identifiera potentiella sårbarheter som påverkar organisationens identiteter. Du kan konfigurera automatiska svar på de identifierade misstänkta aktiviteterna och vidta lämpliga åtgärder för att lösa dem. Mer information finns i [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>Skaffa ditt säkra Office 365-resultat (om du använder Office 365)

Säker poäng visar vilka Office 365-tjänster du använder (som OneDrive, SharePoint och Exchange) och tittar sedan på dina inställningar och aktiviteter och jämför dem med en baslinje som upprättats av Microsoft. Du får en poäng baserat på hur anpassad du är med bästa säkerhetsmetoder. Alla som har administratörsbehörighet (global administratör eller en anpassad administratörsroll) för en [https://securescore.office.com](https://securescore.office.com/)Office 365 Business Premium- eller Enterprise-prenumeration kan komma åt secure score på .

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>Granska säkerhets- och efterlevnadsvägledningen för Office 365 (om du använder Office 365)

[I abonnemanget för säkerhet och efterlevnad](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) beskrivs metoden för hur en Office 365-kund ska konfigurera Office 365 och utnyttja andra EMS-funktioner. Gå sedan igenom steg 3-6 om hur du [skyddar åtkomsten till data och tjänster i Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) och guiden för hur du [övervakar säkerhet och efterlevnad i Office 365](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6).

#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>Konfigurera Aktivitetsövervakning för Office 365 (om office 365)

Du kan övervaka hur personer i organisationen använder Office 365-tjänster, så att du kan identifiera användare som har ett administrativt konto och som kanske inte behöver Office 365-åtkomst på grund av att de inte loggar in på dessa portaler. Mer information finns [i Aktivitetsrapporter i administrationscentret för Microsoft 365](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263).

#### <a name="establish-incidentemergency-response-plan-owners"></a>Upprätta ägare av incident-/beredskapsplan

Att utföra incidentinsatser effektivt är ett komplext företag. Därför kräver upprättandet av en framgångsrik incidenthanteringskapacitet omfattande planering och resurser. Det är viktigt att du kontinuerligt övervakar för cyberattacker och upprättar rutiner för att prioritera hanteringen av incidenter. Effektiva metoder för att samla in, analysera och rapportera data är avgörande för att bygga relationer och för att etablera kommunikation med andra interna grupper och planera ägare. Mer information finns i [Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717). 

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Säkra lokala privilegierade administrativa konton, om de inte redan har gjorts

Om din Azure Active Directory-klient synkroniseras med lokal Active Directory följer du anvisningarna i Översikt över åtkomst för [privilegierad säkerhet:](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)Steg 1. Detta inkluderar att skapa separata administratörskonton för användare som behöver utföra lokala administrativa uppgifter, distribuera arbetsstationer för privilegierad åtkomst för Active Directory-administratörer och skapa unika lokala administratörslösenord för arbetsstationer och Servrar.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Ytterligare steg för organisationer som hanterar åtkomst till Azure

#### <a name="complete-an-inventory-of-subscriptions"></a>Slutföra en inventering av prenumerationer

Använd Enterprise-portalen och Azure-portalen för att identifiera prenumerationer i organisationen som är värd för produktionsprogram.

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Ta bort Microsoft-konton från administratörsroller

Microsoft-konton från andra program, till exempel Xbox, Live och Outlook, bör inte användas som administratörskonton för organisationsprenumerationer. Ta bort administratörsstatus från alla Microsoft-konton och chris@contoso.comersätt med Azure Active Directory (till exempel ) arbets- eller skolkonton.

#### <a name="monitor-azure-activity"></a>Övervaka Azure-aktivitet

Azure-aktivitetsloggen innehåller historik över händelser på prenumerationsnivå i Azure. Den innehåller information om vem som skapade, uppdaterade och tog bort vilka resurser och när dessa händelser inträffade. Mer information finns i [Granska och ta emot meddelanden om viktiga åtgärder i din Azure-prenumeration](../../azure-monitor/platform/quick-audit-notify-action-subscription.md).

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Ytterligare steg för organisationer som hanterar åtkomst till andra molnappar via Azure AD

#### <a name="configure-conditional-access-policies"></a>Konfigurera principer för villkorlig åtkomst

Förbered principer för villkorlig åtkomst för lokala och molnbaserade program. Om du har användare på arbetsplatsen sammanfogade enheter kan du få mer information från [Konfigurera lokal villkorlig åtkomst med hjälp av Registrering av Azure Active Directory-enhet](../active-directory-device-registration-on-premises-setup.md).


## <a name="stage-3-build-visibility-and-take-full-control-of-admin-activity"></a>Steg 3: Skapa synlighet och ta full kontroll över administratörsaktiviteten

![Steg 3 tar kontroll över admin-aktivitet](./media/directory-admin-roles-secure/stage-three.png)

Steg 3 bygger på mildrande åtgärder från steg 2 och är utformad för att implementeras i cirka 1-3 månader. Det här steget i översikten säker privilegierad åtkomst innehåller följande komponenter.

### <a name="general-preparation"></a>Allmänna förberedelser

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Slutföra en åtkomstgranskning av användare i administratörsroller

Fler företagsanvändare får privilegierad åtkomst via molntjänster, vilket kan leda till en ökande ohanterlig plattform. Detta inkluderar användare som blir globala administratörer för Office 365, Azure-prenumerationsadministratörer och användare som har administratörsåtkomst till virtuella datorer eller via SaaS-appar. I stället bör organisationer ha alla anställda, särskilt administratörer, hantera dagliga affärstransaktioner som oprivilegierade användare och endast ta på sig administratörsrättigheter efter behov. Eftersom antalet användare i administratörsroller kan ha ökat sedan den första användningen, slutför du åtkomstgranskningar för att identifiera och bekräfta alla användare som är berättigade att aktivera administratörsbehörighet. 

Gör följande:

* Ta reda på vilka användare som är Azure AD-administratörer, aktivera administratörsåtkomst på begäran, just-in-time och rollbaserade säkerhetskontroller.
* Konvertera användare som inte har någon tydlig motivering för administratörsbehörighet till en annan roll (om ingen kvalificerad roll tar du bort dem).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Fortsätta distributionen av starkare autentisering för alla användare 

Kräv att chefer i C-suite, chefer på hög nivå, kritisk IT- och säkerhetspersonal och andra högexponerade användare har modern, stark autentisering, till exempel Azure MFA eller Windows Hello. 

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Använda dedikerade arbetsstationer för administration för Azure AD

Angripare kan försöka rikta in sig på privilegierade konton för att få åtkomst till en organisations data och system så att de kan störa datas integritet och äkthet, genom skadlig kod som ändrar programlogiken eller snokar administratören som anger en autentiseringsuppgifter. Arbetsstationer för privilegierad åtkomst (PAW, Privileged Access Workstation) ger tillgång till ett dedikerat operativsystem för känsliga uppgifter som är skyddat mot Internetattacker och hotvektorer. Genom att hålla känsliga uppgifter och konton åtskilda från de vanliga arbetsstationerna för daglig användning kan du effektivt skydda infrastrukturen mot nätfiskeattacker, attacker mot sårbarheter i program och operativsystem, olika typer av personifieringsattacker och attacker som försöker stjäla autentiseringsuppgifter, t.ex. loggning av tangenttryckningar, Pass-The-Hash och Pass-The-Ticket. Genom att distribuera arbetsstationer för privilegierad åtkomst kan du minska risken för att administratörer anger administratörsautentiseringsuppgifter utom i en skrivbordsmiljö som har härdats. Mer information finns i [Arbetsstationer för privilegierad åtkomst](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations).

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Granska Nationella institutet för standarder och teknik rekommendationer för hantering av incidenter 

National Institute of Standards and Technology 's (NIST) ger riktlinjer för incidenthantering, särskilt för att analysera incidentrelaterade data och fastställa lämpliga svar på varje incident. Mer information finns i [(NIST) guide för hantering av datorsäkerhetsincidenter (SP 800-61, Revision 2)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Implementera PIM (Privileged Identity Management) för JIT till ytterligare administrativa roller

Använd Azure AD [Privileged Identity Management-funktionen](../privileged-identity-management/pim-configure.md) för Azure Active Directory. Tidsbegränsad aktivering av privilegierade roller fungerar genom att du kan:

* Aktivera administratörsbehörighet för att utföra en viss uppgift
* Framtvinga MFA under aktiveringsprocessen
* Använd aviseringar för att informera administratörer om out-of-band-ändringar
* Gör det möjligt för användare att behålla vissa privilegier under en förkonfigurerad tid
* Tillåt säkerhetsadministratörer att identifiera alla privilegierade identiteter, visa granskningsrapporter och skapa åtkomstgranskningar för att identifiera alla användare som är berättigade att aktivera administratörsbehörighet

Om du redan använder Azure AD Privileged Identity Management justerar du tidsramarna för tidsbundna privilegier efter behov (till exempel underhållsfönster).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Fastställ exponering för lösenordsbaserade inloggningsprotokoll (om du använder Exchange Online)

Tidigare antog protokoll att kombinationer av användarnamn/lösenord var inbäddade i enheter, e-postkonton, telefoner och så vidare. Men nu med risken för cyberattacker i molnet rekommenderar vi att du identifierar alla potentiella användare som, om deras autentiseringsuppgifter komprometterades, kan vara katastrofala för organisationen och utesluta dem från att kunna logga in på sin e-post via användarnamn /lösenord genom att implementera starka autentiseringskrav och villkorlig åtkomst. Du kan blockera [äldre autentisering med villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/block-legacy-authentication). Läs information om [hur du blockerar grundläggande autentisering](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online) via Exchange online. 

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>Slutför en granskningsutvärdering av roller för Office 365-roller (om office 365 använders)

Utvärdera om alla administratörer användare är i rätt roller (ta bort och tilldela enligt denna bedömning).

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>Granska metoden för hantering av säkerhetsincidenter som används i Office 365 och jämför med din egen organisation

Du kan hämta den här rapporten från [Security Incident Management i Microsoft Office 365](https://www.microsoft.com/download/details.aspx?id=54302).

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Fortsätt att skydda lokala privilegierade administrativa konton

Om din Azure Active Directory är ansluten till lokal Active Directory följer du anvisningarna i [översikten för åtkomst till säkerhet privilegierad](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)åtkomst: Steg 2. Detta inkluderar distribution av arbetsstationer för privilegierad åtkomst för alla administratörer, som kräver MFA, med just enough admin för DC-underhåll, sänka attackytan för domäner, distribuera ATA för attackidentifiering.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Ytterligare steg för organisationer som hanterar åtkomst till Azure

#### <a name="establish-integrated-monitoring"></a>Upprätta integrerad övervakning

[Azure Security Center](../../security-center/security-center-intro.md) tillhandahåller integrerad säkerhetsövervakning och principhantering i dina Azure-prenumerationer, hjälper till att identifiera hot som annars kan gå obemärkt förbi och fungerar med ett brett ekosystem av säkerhetslösningar.

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Inventera dina privilegierade konton i värdbaserade virtuella datorer

I de flesta fall behöver du inte ge användarna obegränsad behörighet till alla dina Azure-prenumerationer eller resurser. Du kan använda Azure AD-administratörsroller för att segregera uppgifter inom din organisation och endast bevilja åtkomst till användare som behöver utföra specifika jobb. Använd till exempel Azure AD-administratörsroller för att låta en administratör endast hantera virtuella datorer i en prenumeration, medan en annan kan hantera SQL-databaser i samma prenumeration. Mer information finns [i Komma igång med rollbaserad åtkomstkontroll i Azure-portalen](../../role-based-access-control/overview.md).

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Implementera PIM för Azure AD-administratörsroller

Använd Privilegierad identitetshantering med Azure AD-administratörsroller för att hantera, kontrollera och övervaka åtkomst till Azure-resurser. Genom att använda PIM skyddas privilegierade konton från cyberattacker genom att minska exponeringstiden för privilegier och öka din insyn i deras användning genom rapporter och aviseringar. Mer information finns i [Hantera RBAC-åtkomst till Azure-resurser med privilegierad identitetshantering](../../role-based-access-control/pim-azure-resource.md).

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Använda Azure-loggintegreringar för att skicka relevanta Azure-loggar till dina SIEM-system 

Med Azure-loggintegrering kan du integrera råloggar från dina Azure-resurser till organisationens befintliga SIEM-system (Security Information and Event Management). [Azure-loggintegrering](../../security/fundamentals/azure-log-integration-overview.md) samlar in Windows-händelser från Windows Loggboken loggar och Azure-resurser från Azure Activity Logs, Azure Security Center-aviseringar och Azure Diagnostic loggar. 


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Ytterligare steg för organisationer som hanterar åtkomst till andra molnappar via Azure AD

#### <a name="implement-user-provisioning-for-connected-apps"></a>Implementera användaretablering för anslutna appar

Med Azure AD kan du automatisera skapande, underhåll och borttagning av användaridentiteter i molnprogram (SaaS) som Dropbox, Salesforce, ServiceNow och så vidare. Mer information finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure AD](../app-provisioning/user-provisioning.md).

#### <a name="integrate-information-protection"></a>Integrera informationsskydd

MCAS kan du undersöka filer och ange principer baserat på Azure Information Protection klassificeringsetiketter, vilket möjliggör större synlighet och kontroll av dina data i molnet. Skanna och klassificera filer i molnet och tillämpa Azure-informationsskyddsetiketter. Mer information finns i [Azure Information Protection-integrering](https://docs.microsoft.com/cloud-app-security/azip-integration).

#### <a name="configure-conditional-access"></a>Konfigurera villkorlig åtkomst

Konfigurera villkorlig åtkomst baserat på en grupp, plats och programkänslighet för [SaaS-appar](https://azure.microsoft.com/overview/what-is-saas/) och Azure AD-anslutna appar. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Övervaka aktivitet i anslutna molnappar

För att säkerställa att användarnas åtkomst är skyddad även i anslutna program rekommenderar vi att du utnyttjar [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). Detta säkerställer företagets åtkomst till molnappar, förutom att skydda dina administratörskonton, genom att du kan:

* Utöka synlighet och kontroll till molnappar
* Skapa principer för åtkomst, aktiviteter och datadelning
* Identifiera automatiskt riskfyllda aktiviteter, onormala beteenden och hot
* Förhindra dataläckage
* Minimera risk- och automatiserade hotförebyggande och policykontroll

Cloud App Security SIEM-agenten integrerar Cloud App Security med din SIEM-server för att möjliggöra centraliserad övervakning av Office 365-aviseringar och aktiviteter. Den körs på din server och hämtar aviseringar och aktiviteter från Cloud App Security och strömmar dem till SIEM-servern. Mer information finns i [SIEM-integrering](https://docs.microsoft.com/cloud-app-security/siem).

## <a name="stage-4-continue-building-defenses-to-a-more-proactive-security-posture"></a>Steg 4: Fortsätt att bygga försvar till en mer proaktiv säkerhetsposition

![Steg 4 anta en proaktiv säkerhetsposition](./media/directory-admin-roles-secure/stage-four.png)

Steg 4 i färdplanen bygger på synligheten från steg 3 och är utformad för att genomföras inom sex månader och därefter. Genom att slutföra en översikt kan du utveckla ett starkt privilegierat åtkomstskydd från potentiella attacker som för närvarande är kända och tillgängliga idag. Tyvärr utvecklas och skiftar säkerhetshoten hela tiden, så vi rekommenderar att du ser säkerhet som en pågående process som fokuserar på att öka kostnaderna och minska framgången för motståndare som riktar sig till din miljö.

Att skydda privilegierad åtkomst är ett viktigt första steg för att upprätta säkerhetsgarantier för affärstillgångar i en modern organisation, men det är inte den enda delen av ett komplett säkerhetsprogram som skulle innehålla element, till exempel princip, åtgärder, information säkerhet, servrar, program, datorer, enheter, molninfrastruktur och andra komponenter ger löpande säkerhetsgarantier. 

Förutom att hantera dina konton för privilegierad åtkomst rekommenderar vi att du kontinuerligt granskar följande:

* Se till att administratörer gör sin dagliga verksamhet som oprivilegierade användare.
* Bevilja endast privilegierad åtkomst när det behövs och ta bort den efteråt (just-in-time).
* Behåll och granska granskningsaktivitet som rör privilegierade konton.

Mer information om hur du skapar en fullständig säkerhetsfärdplan finns i [Microsofts moln-IT-arkitekturresurser](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources). Mer information om hur du anlitar Microsoft-tjänster för att hjälpa till med något av dessa ämnen kontaktar du din Microsoft-representant eller läser [Skapa kritiska cyberförsvar för att skydda ditt företag](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx).

Den här sista pågående fasen av färdplanen för skyddad privilegierad åtkomst innehåller följande komponenter.

### <a name="general-preparation"></a>Allmänna förberedelser

#### <a name="review-admin-roles-in-azure-active-directory"></a>Granska administratörsroller i Azure Active Directory 

Ta reda på om aktuella inbyggda Azure AD-administratörsroller fortfarande är uppdaterade och se till att användarna bara finns i de roller och delegeringar som de behöver för motsvarande behörigheter. Med Azure AD kan du ange separata administratörer för att hantera olika funktioner. Mer information finns i [Tilldela administratörsroller i Azure Active Directory](directory-assign-admin-roles.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Granska användare som har administration av Azure AD-anslutna enheter

Mer information finns i [Så här konfigurerar du hybrid-Azure Active Directory-anslutna enheter](../device-management-hybrid-azuread-joined-devices-setup.md).

#### <a name="review-members-of-built-in-office-365-admin-roles"></a>Granska medlemmar [i inbyggda office 365-administratörsroller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)
Om du använder Office 365.
‎
#### <a name="validate-incident-response-plan"></a>Validera incidenthanteringsplan

För att förbättra ditt abonnemang rekommenderar Microsoft att du regelbundet validerar att ditt abonnemang fungerar som förväntat:

* Gå igenom din befintliga färdplan för att se vad som missades
* Baserat på postmortemanalysen, revidera befintliga eller definiera nya bästa metoder
* Se till att din uppdaterade incidenthanteringsplan och bästa praxis distribueras i hela organisationen


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Ytterligare steg för organisationer som hanterar åtkomst till Azure 

Ta reda på om du behöver [överföra ägarskapet för en Azure-prenumeration till ett annat konto](../../cost-management-billing/manage/billing-subscription-transfer.md).
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Break glass": vad man ska göra i en nödsituation

![Konton för nödkrossglasåtkomst](./media/directory-admin-roles-secure/emergency.jpeg)

1. Meddela nyckelchefer och ordningsvakter med relevant information om händelsen.

2. Gå igenom din attackspelbok. 

3. Få tillgång till din kombination av användarnamn/lösenord för att logga in på Azure AD. 

4. Få hjälp från Microsoft genom [att öppna en Azure-supportbegäran](../../azure-portal/supportability/how-to-create-azure-support-request.md).

5. Titta på [Azure AD-inloggningsrapporterna](../reports-monitoring/overview-reports.md). Det kan finnas en fördröjning mellan en händelse som inträffar och när den tas med i rapporten.

6. Om federerade och AD FS-servern inte är tillgänglig för hybridmiljöer kan du behöva tillfälligt byta från federerad autentisering för att använda synkronisering av lösenord hash. Detta återställer domänfederationen tillbaka till hanterad autentisering tills AD FS-servern blir tillgänglig.

7. Övervaka e-post för privilegierade konton.

8. Se till att du sparar säkerhetskopior av relevanta loggar för potentiella kriminaltekniska och juridiska undersökningar.

Mer information om hur Microsoft Office 365 hanterar säkerhetsincidenter finns [i Security Incident Management i Microsoft Office 365](https://aka.ms/Office365SIM).

## <a name="faq-common-questions-we-receive-regarding-securing-privileged-access"></a>Vanliga frågor: Vanliga frågor vi får om att skydda privilegierad åtkomst  

**F:** Vad gör jag om jag inte har implementerat några säkra åtkomstkomponenter ännu?

**Svar:** Definiera minst två break-glass-konto, tilldela MFA till dina privilegierade administratörskonton och separata användarkonton från globala administratörskonton.

**F:** Efter ett brott, vad är den viktigaste frågan som måste åtgärdas först?

**Svar:** Se till att du behöver den starkaste autentiseringen för högexponerade individer.

**F:** Vad händer om våra privilegierade administratörer har inaktiverats?

**Svar:** Skapa ett globalt administratörskonto som alltid hålls uppdaterat.

**F:** Vad händer om det bara finns en global administratör kvar och de inte kan nås? 

**Svar:** Använd ett av dina glasb inbrottskonton för att få omedelbar privilegierad åtkomst.

**F:** Hur kan jag skydda administratörer inom min organisation?

**Svar:** Har administratörer alltid göra sin dagliga verksamhet som standard "oprivilegierade" användare.

**F:** Vilka är de bästa metoderna för att skapa administratörskonton i Azure AD?

**Svar:** Reservera privilegierad åtkomst för specifika administratörsuppgifter.

**F:** Vilka verktyg finns för att minska beständig administratörsåtkomst?

**Svar:** Pim-administratörsroller (Privileged Identity Management) och Azure AD.

**F:** Vad är Microsofts position när det gäller att synkronisera administratörskonton till Azure AD?

**Svar:** Nivå 0-administratörskonton (inklusive konton, grupper och andra tillgångar som har direkt eller indirekt administrativ kontroll över AD-skogen, domäner eller domänkontrollanter och alla tillgångar) används endast för lokala AD-konton och synkroniseras vanligtvis inte för Azure AD för molnet.

**F:** Hur hindrar vi administratörer från att tilldela slumpmässig administratörsåtkomst i portalen?

**Svar:** Använd konton som inte är privilegierade för alla användare och de flesta administratörer. Börja med att utveckla ett fotavtryck för organisationen för att avgöra vilka få administratörskonton som ska privilegiera. Och övervaka för nyligen skapade administrativa användare.

## <a name="next-steps"></a>Nästa steg

* [Microsoft Trust Center for Product Security](https://www.microsoft.com/trustcenter/security) – Säkerhetsfunktioner för Microsofts molnprodukter och -tjänster

* [Microsoft Trust Center – Efterlevnad](https://www.microsoft.com/trustcenter/compliance/complianceofferings) – Microsofts omfattande uppsättning efterlevnadserbjudanden för molntjänster

* [Vägledning om hur du utför en riskbedömning](https://www.microsoft.com/trustcenter/guidance/risk-assessment) – Hantera säkerhets- och efterlevnadskrav för Microsofts molntjänster

### <a name="other-microsoft-online-services"></a>Andra Microsoft Online-tjänster

* [Microsoft Intune Security](https://www.microsoft.com/trustcenter/security/intune-security) – Intune tillhandahåller funktioner för hantering av mobila enheter, hantering av mobila program och pc-hantering från molnet.

* [Microsoft Dynamics 365 security](https://www.microsoft.com/trustcenter/security/dynamics365-security) – Dynamics 365 är den Microsoft-molnbaserade lösning som förenar funktionerna för hantering av kundrelationer (CRM) och ERP-funktioner (Enterprise Resource Planning).
