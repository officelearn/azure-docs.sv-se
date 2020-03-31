---
title: Planera en Azure Active Directory Access Panel-distribution
description: Vägledning om hur du distribuerar Åtkomstpanelen för Azure Active Directory
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2019
ms.author: baselden
ms.reviewer: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04d8b6c6d40aa81bf56baed59f90417f2147fa56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897072"
---
# <a name="plan-an-azure-active-directory-access-panel-deployment"></a>Planera en Azure Active Directory Access Panel-distribution

Åtkomstpanelen för Azure Active Directory (Azure AD) är en webbaserad portal som hjälper till att sänka supportkostnaderna, öka produktiviteten och säkerheten och minska användarnas frustration. Systemet innehåller detaljerad rapportering som spårar när du öppnar systemet och meddelar administratörer om missbruk eller missbruk.

Genom att använda Azure AD Access Panel kan du:

* Upptäck och få tillgång till alla företagets Azure AD-anslutna resurser, till exempel program
* Begära åtkomst till nya appar och grupper
* Hantera åtkomst till dessa resurser för andra
* Hantera återställning av lösenord för självbetjäning och azure multifaktorautentiseringsinställningar
* Hantera sina enheter

Det gör det också möjligt för administratörer att hantera:

* Användarvillkor
* Organisationer
* Åtkomstgranskningar


## <a name="benefits-of-azure-ad-access-panel-integration"></a>Fördelar med integrering av Azure AD Access Panel

Azure AD Access Panel gynnar företag på följande sätt:

**Ger intuitiv användarupplevelse:** Åtkomstpanelen ger dig en enda plattform för alla dina Azure single sign-on (SSO)-anslutna program. Du har en enhetlig portal för att hitta befintliga inställningar och nya funktioner, till exempel grupphantering och återställning av lösenord för självbetjäning, när de läggs till. Den intuitiva upplevelsen gör det möjligt för användare att återgå till arbetet snabbare och bli mer produktiva, samtidigt som de minskar sin frustration.

**Ökar produktiviteten**: Alla användarprogram på åtkomstpanelen har SSO aktiverat. Om du aktiverar SSO i företagsprogram och Office 365 skapas en överlägsen inloggningsupplevelse genom att ytterligare inloggningsuppmaningar minskas eller elimineras. Åtkomstpanelen använder självbetjäning och dynamiskt medlemskap och förbättrar den övergripande säkerheten för ditt identitetssystem. Det gör man genom att se till att rätt personer hanterar åtkomsten till programmen. Åtkomstpanelen fungerar som en sammanhängande målsida där du snabbt kan hitta resurser och fortsätta arbetet.

**Hanterar kostnad:** Aktivera åtkomstpanelen med Azure AD kan hjälpa till med avyttringen av lokala infrastrukturer. Det minskar supportkostnaderna genom att ge dig en konsekvent portal för att hitta alla dina appar, begära åtkomst till resurser och hantera konton.

**Ökar flexibiliteten och säkerheten:** Åtkomstpanelen ger dig tillgång till den säkerhet och flexibilitet som en molnplattform tillhandahåller. Administratörer kan enkelt ändra inställningar till program och resurser och kan tillgodose nya säkerhetskrav utan att påverka användarna.

**Möjliggör robust granskning och användningsspårning**: Granskning och användningsspårning för alla användarfunktioner meddelar dig när användare använder sina resurser och ser till att du kan bedöma säkerheten.

### <a name="licensing-considerations"></a>Tillståndsöverväganden

Åtkomstpanelen är gratis och kräver inga licenser att använda på en grundläggande nivå. Antalet objekt i katalogen och de ytterligare funktioner som du vill distribuera kan dock kräva ytterligare licenser. Några vanliga Azure AD-scenarier som har licenskrav är följande säkerhetsfunktioner:

* [Azure multifaktorautentisering](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works)

* [Gruppbaserat medlemskap](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)

* [Självåterställning av lösenord](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)

* [Identitetsskydd för Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

Se den [fullständiga licensieringsguiden för Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites-for-deploying-the-azure-ad-access-panel"></a>Förutsättningar för distribution av Azure AD Access Panel

Slutför följande förutsättningar innan du påbörjar det här projektet:

* [Integrera program SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-sso-deployment)

* [Hantera Azure AD-användar- och gruppinfrastruktur](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)

## <a name="plan-the-azure-ad-access-panel-deployment"></a>Planera distributionen av Azure AD Access-panelen

I följande tabell beskrivs de viktigaste användningsfallen för en distribution av Åtkomstpanelen:

| Område| Beskrivning |
| - | - |
| Åtkomst| Access Panel-portalen är tillgänglig från företagsenheter och personliga enheter i företagsnätverket. |
|Åtkomst | Access Panel-portalen är tillgänglig från företagsenheter utanför företagsnätverket. |
| Granskning| Användningsdata hämtas till företagssystem minst var 29:e dag. |
| Styrning| Livscykeln för användartilldelningarna till Azure AD-anslutna program och grupper definieras och övervakas. |
| Säkerhet| Åtkomsten till resurser styrs via användar- och grupptilldelningar. Endast behöriga användare kan hantera resursåtkomst. |
| Prestanda| Åtkomsttilldelningsspridningstidslinjer dokumenteras och övervakas. |
| Användarupplevelse| Användarna är medvetna om åtkomstpanelens funktioner och hur de ska användas.|
| Användarupplevelse| Användare kan hantera sin åtkomst till program och grupper.|
| Användarupplevelse| Användare kan hantera sina konton. |
| Användarupplevelse| Användarna är medvetna om webbläsarkompatibiliteten. |
| Support| Användare kan hitta stöd för problem med Åtkomstpanelen. |

### <a name="best-practices-for-deploying-the-azure-ad-access-panel"></a>Metodtips för distribution av Azure AD Access Panel

Funktionen på åtkomstpanelen kan aktiveras gradvis. Vi rekommenderar följande distributionsordning:

1. Mina appar
   * Startprogram
   * Apphantering med självbetjäning
   * Microsoft Office 365-integrering

1. Identifiering av självbetjäningsappar
   * Återställning av lösenord för självbetjäning
   * Inställningar för multifaktorautentisering
   * Enhetshantering
   * Användningsvillkor
   * Hantera organisationer

1. Mina grupper
   * Självbetjäning, grupphantering
1. Åtkomstgranskningar
   * Hantering av åtkomstgranskning

Från och med Mina appar introducerar användare till portalen som en vanlig plats för åtkomst till resurser. Tillägget av självbetjäningsprogramidentifiering bygger på upplevelsen Mina appar. Mina grupper och åtkomstgranskningar bygger på självbetjäningsfunktionerna.

### <a name="plan-configurations-for-the-azure-ad-access-panel"></a>Planera konfigurationer för Azure AD Access-panelen

I följande tabell visas flera viktiga konfigurationer på Åtkomstpanelen och de typiska värden som du kan använda:

| Konfiguration| Typiska värden |
| - | - |
| Bestäm pilotgrupperna| Identifiera azure AD-säkerhetsgruppen som ska användas och se till att alla pilotmedlemmar är en del av gruppen. |
| Bestäm vilken grupp eller vilka grupper som ska aktiveras för produktion.| Identifiera Azure AD-säkerhetsgrupperna eller Active Directory-grupperna som synkroniserats med Azure AD och som ska användas. Se till att alla pilotmedlemmar är en del av gruppen. |
| Tillåt användare att använda SSO för vissa typer av program| Federerad SSO, OAuth, Lösenord SSO, App Proxy |
| Tillåt användare att använda återställning av lösenord för självbetjäning | Ja |
| Tillåt användare att använda multifaktorautentisering| Ja |
| Tillåt användare att använda grupphantering med självbetjäning för vissa typer av grupper| Säkerhetsgrupper, Office 365-grupper |
| Tillåt användare att använda självbetjäningsapphantering| Ja |
| Tillåt användare att använda åtkomstgranskningar| Ja |

### <a name="plan-consent-strategy"></a>Planera samtyckesstrategi

Användare eller administratörer måste samtycka till alla program användarvillkor och sekretesspolicyer. Om möjligt, med tanke på dina affärsregler, använd administratörsmedgivande, vilket ger användarna en bättre upplevelse.

Om du vill använda administratörsmedgivande måste du vara en global administratör för organisationen och programmen måste vara antingen:

* Registrerad i din organisation

* Registrerad i en annan Azure AD-organisation och har tidigare godkänts av minst en användare

Mer information finns i [Konfigurera hur slutanvändare samtycker till ett program i Azure Active Directory](configure-user-consent.md).

### <a name="engage-the-right-stakeholders"></a>Engagera rätt intressenter

När teknikprojekt misslyckas gör de det vanligtvis på grund av felaktiga förväntningar på påverkan, resultat och ansvar. För att undvika dessa fallgropar, [se till att du engagerar rätt intressenter](../fundamentals/active-directory-deployment-plans.md) och att intressentrollerna i projektet är väl förstådda.

### <a name="plan-communications"></a>Planera kommunikation

Kommunikation är avgörande för att en ny tjänst ska lyckas. Proaktivt informera dina användare hur och när deras erfarenhet kommer att förändras och hur man får support om det behövs.

Även om åtkomstpanelen vanligtvis inte skapar användarproblem är det viktigt att förbereda. Skapa guider och en lista över alla resurser för supportpersonalen innan du startar.

#### <a name="communications-templates"></a>Kommunikationsmallar

Microsoft tillhandahåller [anpassningsbara mallar för e-postmeddelanden och annan kommunikation](https://aka.ms/APTemplates) för Åtkomstpanelen. Du kan anpassa dessa tillgångar för användning i andra kommunikationskanaler efter behov för din företagskultur.

## <a name="plan-your-sso-configuration"></a>Planera din SSO-konfiguration

När en användare loggar in på ett program går de igenom en autentiseringsprocess och måste bevisa vilka de är. Utan SSO lagras ett lösenord i programmet och användaren måste känna till lösenordet. Med SSO skickas användarnas autentiseringsuppgifter till programmet, så att de inte behöver ange lösenord igen för varje program.

För att starta program i Mina appar måste SSO vara aktiverat.

Azure AD stöder tre olika sätt att aktivera [enkel inloggning i program:](what-is-single-sign-on.md)

* **Federerad enkel inloggning** 
    * Aktiverar ett program för att omdirigera till Azure AD för användarautentisering, i stället för att fråga efter ett lösenord. 
    * Stöds för program som använder protokoll, till exempel SAML 2.0, WS-Federation eller OpenID Connect, och är det rikaste läget för enkel inloggning.

* **Lösenordsbaserad enkel inloggning** 
    * Aktiverar säker lagring av programlösenord och uppspelning med hjälp av ett webbläsartillägg eller en mobilapp. 
    * Drar nytta av den befintliga inloggningsprocessen som tillhandahålls av programmet, men gör det möjligt för en administratör att hantera lösenorden. Användaren behöver inte känna till lösenordet.

* **Befintlig enkel inloggning** 
    * Gör det möjligt för Azure AD att dra nytta av alla befintliga enskilda inloggningar som har konfigurerats för programmet.
    * Gör att dessa program kan länkas till Office 365- eller Azure AD Access Panel-portalerna. 
    * Aktiverar ytterligare rapportering i Azure AD när programmen startas där. 
    * Inkluderar att använda Azure Application Proxy och det länkade enkel inloggningsläget.

Lär dig hur du konfigurerar ett programs [SSO-läge](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)här: Enkel inloggning till program i Azure Active Directory .

För bästa möjliga upplevelse med sidan Mina appar, börja med integreringen av molnprogram som är tillgängliga för federerade SSO. Federerad SSO tillåter användare att ha en konsekvent en klick upplevelse över sin app lansera ytor och tenderar att vara mer robust i konfigurationskontroll.

Använd Federerad SSO med Azure AD (OpenID Connect/SAML) när ett program stöder det, i stället för lösenordsbaserad SSO och ADFS.

Mer information om hur du distribuerar och konfigurerar SaaS-program finns i [SaaS SSO-distributionsplanen](https://aka.ms/deploymentplans/sso).

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>Planera att distribuera webbläsartillägget Mina appar

När användare loggar in på lösenordsbaserade SSO-program måste de installera och använda det säkra inloggningstillägget Mina appar. Tillägget kör ett skript som överför lösenordet till programmets inloggningsformulär. Användare uppmanas att installera tillägget när de först startar det lösenordsbaserade SSO-programmet. Mer information om tillägget finns i den här dokumentationen om [hur du installerar webbläsartillägget för Åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/application-access-panel-extension-problem-installing?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav).

Om du måste integrera lösenordsbaserade SSO-program bör du definiera en mekanism för att distribuera tillägget i stor skala med [webbläsare som stöds](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction). Alternativen är:

* [Grupprincip för Internet Explorer](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)

* [Configuration Manager för Internet Explorer](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

* [Användardriven nedladdning och konfiguration för Chrome, Firefox, Microsoft Edge eller IE](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Läs mer: [Konfigurera lösenord enkel inloggning](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery).

Användare som inte använder lösenordsbaserade SSO-program drar också nytta av tillägget. Dessa fördelar inkluderar möjligheten att starta en app från sökfältet, hitta åtkomst till nyligen använda program och ha en länk till sidan Mina appar.

Här är vad användaren kommer att se när du startar ett lösenordsbaserat SSO-program för första gången:

![Skärmbild av installationsskärmen för webbläsartillägg för Mina appar ](media/access-panel-deployment-plan/ap-dp-sign-in.png)

#### <a name="plan-for-mobile-access"></a>Planera för mobil åtkomst

En webbläsare som skyddas med Intune-principen (Microsoft Edge eller Intune Managed Browser) är nödvändig för mobila användare som startar lösenordsbaserade SSO-program. En principskyddad webbläsare gör det möjligt att överföra lösenordet som sparats för programmet. Microsoft Edge eller den hanterade webbläsaren innehåller en uppsättning webbdataskyddsfunktioner. Du kan också använda Microsoft Edge för företagsscenarier på iOS- och Android-enheter. Microsoft Edge stöder samma hanteringsscenarier som Intune Managed Browser och förbättrar användarupplevelsen. Läs mer: [Hantera webbåtkomst med hjälp av en Microsoft Intune-principskyddad webbläsare](https://docs.microsoft.com/intune/app-configuration-managed-browser).

## <a name="plan-your-my-apps-deployment"></a>Planera distributionen av mina appar

Grunden för Åtkomstpanelen är programstartprogrammet My Apps, [https://myapps.microsoft.com](https://myapps.microsoft.com/)som användarna får tillgång till på . På sidorna Mina appar får användarna en enda plats att börja sitt arbete och komma åt sina nödvändiga program. Här hittar användarna en lista över alla program de har enkel inloggning åtkomst till. 

![En skärmbild av apppanelen](media/access-panel-deployment-plan/ap-dp-app-launcher.png)

Samma program visas i startprogrammet för Office 365 när användare använder Office 365-portalen.

Planera i vilken ordning du ska lägga till program i startprogrammet För Mina appar och bestäm om du ska distribuera dem gradvis eller allt på en gång. Det gör du genom att skapa ett programlager med en lista över typ av autentisering och eventuella befintliga SSO-integreringar för varje program.

#### <a name="add-applications-to-the-my-apps-panel"></a>Lägga till program på panelen Mina appar

Alla Azure AD SSO-aktiverade program kan läggas till i startprogrammet För Mina appar. Andra program läggs till med hjälp av alternativet Länkad SSO. Du kan konfigurera en programpanel som länkar till URL:en för ditt befintliga webbprogram. Med länkad SSO kan du börja dirigera användare till my apps-portalen utan att migrera alla program till Azure AD SSO. Du kan gradvis flytta till Azure AD SSO-konfigurerade program utan att störa användarnas upplevelse.

#### <a name="use-my-apps-collections"></a>Använda mina appar-samlingar

Som standard visas alla program tillsammans på en enda sida. Men du kan använda samlingar för att gruppera relaterade program och presentera dem på en separat flik, vilket gör dem lättare att hitta. Du kan till exempel använda samlingar för att skapa logiska grupperingar av program för specifika jobbroller, uppgifter, projekt och så vidare. Mer information finns i [Så här använder du Mina appar-samlingar för att anpassa användaråtkomstpaneler](access-panel-collections.md). 

#### <a name="plan-whether-to-use-my-apps-or-an-existing-portal"></a>Planera om mina appar eller en befintlig portal ska användas

Användarna kanske redan har ett annat program eller en annan portal än Mina appar. Om så är fallet, besluta om att stödja båda portalerna eller bara använda en.

Om en befintlig portal redan används som utgångspunkt för användare kan du integrera Mina appar med hjälp av användaråtkomstadresser. Användaråtkomstadresser fungerar som direkta länkar till de program som är tillgängliga i portalen Mina appar. Dessa webbadresser kan bäddas in på valfri befintlig webbplats. När en användare väljer länken öppnas programmet från portalen Mina appar.

Du hittar url-egenskapen för användaråtkomst i egenskapen **Egenskaper** för programmet i Azure-portalen.

![En skärmbild av apppanelen](media/access-panel-deployment-plan/ap-dp-user-access-url.png)


## <a name="plan-self-service-application-discovery-and-access"></a>Planera identifiering och åtkomst till självbetjäningsprogram

När en kärnuppsättning program har distribuerats till en användares sida Mina appar bör du aktivera självbetjäningsapphanteringsfunktioner. Identifiering av självbetjäningsappar gör det möjligt för användare att:

* Hitta nya appar att lägga till i mina appar. 
* Lägg till valfria appar som de kanske inte vet att de behöver under installationen.

Arbetsflöden för godkännande är tillgängliga för explicit godkännande för åtkomst till program. Användare som är godkännare får aviseringar i My Apps-portalen när begäran om åtkomst till programmet väntar.

## <a name="plan-self-service-group-membership"></a>Planera självbetjäningsgruppmedlemskap 

Du kan göra det möjligt för användare att skapa och hantera sina egna säkerhetsgrupper eller Office 365-grupper i Azure AD. Gruppens ägare kan godkänna eller neka medlemsförfrågningar och delegera kontroll över gruppmedlemskap. Grupphanteringsfunktioner för självbetjäning är inte tillgängliga för e-postaktiverade säkerhetsgrupper eller distributionslistor.

Om du vill planera för gruppmedlemskap med självbetjäning bestämmer du om du tillåter alla användare i organisationen att skapa och hantera grupper eller bara en delmängd användare. Om du tillåter en delmängd av användarna måste du skapa en grupp som dessa personer läggs till i. Mer information om hur du aktiverar dessa scenarier finns i Konfigurera grupphantering med [självbetjäning i Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management)

## <a name="plan-reporting-and-auditing"></a>Planera rapportering och revision

Azure AD innehåller [rapporter som erbjuder tekniska och affärsmässiga insikter](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/). Arbeta med dina företag och tekniska programägare för att ta över ägandet av dessa rapporter och att konsumera dem regelbundet. Följande tabell innehåller några exempel på typiska rapporteringsscenarier.

|   | Hantera risk| Öka produktiviteten| Styrning och efterlevnad |
|  - |- | - | - |
| Rapporttyper|  Programbehörigheter och användning| Kontotableringsaktivitet| Granska vem som har åtkomst till programmen |
| Potentiella åtgärder| Tillgång till revision. återkalla behörigheter| Åtgärda alla etableringsfel| Återkalla åtkomst |

Azure AD behåller de flesta granskningsdata i 30 dagar. Data är tillgängliga via Azure Admin Portal eller API för dig att ladda ner till dina analyssystem.

#### <a name="auditing"></a>Granskning

Granskningsloggar för programåtkomst är tillgängliga i 30 dagar. Om säkerhetsgranskning inom företaget kräver längre kvarhållning måste loggarna exporteras till ett SIEM-verktyg (Security Information Event and Management), till exempel Splunk eller ArcSight.

För granskning, rapportering och säkerhetskopiering av katastrofåterställning dokumenterar du den hämtningsfrekvens som krävs, vad målsystemet är och vem som är ansvarig för att hantera varje säkerhetskopiering. Du kanske inte behöver separata gransknings- och rapporteringssäkerhetskopior. Säkerhetskopiering av katastrofåterställning bör vara en separat entitet.

## <a name="deploy-applications-to-users-my-apps-panel"></a>Distribuera program till användarnas panelen Mina appar

När ett program har konfigurerats för SSO tilldelas grupper åtkomst. Användare i de tilldelade grupperna har åtkomst och de ser programmet i startprogrammet Mina program och Startprogrammet för Office 365.

Se [Tilldela användare och grupper till ett program i Active Directory](methods-for-assigning-users-and-groups.md).

Om du under testningen eller distributionen vill lägga till grupperna men ännu inte tillåter att programmen visas i Mina appar läser [du Dölj ett program från användarens upplevelse i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

### <a name="deploy-microsoft-office-365-applications-to-my-apps"></a>Distribuera Microsoft Office 365-program till Mina program

För Office 365-program får användarna en kopia av Office baserat på licenser som tilldelats dem. En förutsättning för åtkomst till Office-program är att användarna tilldelas rätt licenser som är knutna till Office-programmen. När du tilldelar en användare en licens ser de automatiskt de program som är kopplade till licensen på sidan Mina appar och i startprogrammet för Office 365.

Om du vill dölja en uppsättning Office-program från användare finns det ett alternativ för att dölja appar från My Apps-portalen, samtidigt som du tillåter åtkomst från Office 365-portalen. Hitta dessa inställningar i delen användarinställningar i programmet. Läs mer: [Dölj ett program från användarens upplevelse i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

![En skärmbild av hur du konfigurerar hur du döljer program](media/access-panel-deployment-plan/ap-dp-o365-portal.png)

### <a name="deploy-application-self-service-capabilities"></a>Distribuera självbetjäningsfunktioner för program

Självbetjäningsprogramåtkomst gör det möjligt för användare att själv upptäcka och begära åtkomst till program. Användarna har friheten att komma åt de appar de behöver utan att gå igenom en IT-grupp varje gång för att begära åtkomst. När en användare begär åtkomst och godkänns, antingen automatiskt eller manuellt av en appägare, läggs de till i en grupp i backend. Rapportering är aktiverat för vem som har begärt, godkänt eller tagit bort åtkomst, och det ger dig kontroll över hanteringen av de tilldelade rollerna.

Du kan delegera godkännande av begäranden om programåtkomst till affärsgodkännanden. Företagsgodkännaren kan ange lösenord för appåtkomst från företagsgodkännarens sida Mina appar.

Läs mer: [Så här använder du självbetjäningstillgång](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to).

![En skärmbild av konfigurationen av självbetjäningsprogramhantering](media/access-panel-deployment-plan/ap-dp-salesforce.png)

## <a name="validate-your-deployment"></a>Verifiera distributionen

Se till att distributionen av åtkomstpanelen har testats noggrant och att en återställningsplan finns på plats.

Följande tester bör utföras med både företagsägda enheter och personliga enheter. Dessa testfall bör också återspegla dina affärsanvändningsfall. Följande är några fall baserat på exempel affärskrav i det här dokumentet och på typiska tekniska scenarier. Lägg till andra som är specifika för dina behov.

#### <a name="application-sso-access-test-case-examples"></a>Exempel på åtkomsttest för program:


| Affärsfall| Förväntat resultat |
| - | -|
| Användaren loggar in på portalen Mina appar| Användaren kan logga in och se sina program |
| Användaren startar ett federerat SSO-program| Användaren loggas automatiskt in i programmet |
| Användaren startar ett lösenord SSO-program för första gången| Användaren måste installera tillägget Mina appar |
| Användaren startar ett lösenord SSO-program en efterföljande gång| Användaren loggas automatiskt in i programmet |
| Användaren startar ett program från Office 365 Portal| Användaren loggas automatiskt in i programmet |
| Användaren startar en app från den hanterade webbläsaren| Användaren loggas automatiskt in i programmet |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Testformulär för självbetjäningsfunktioner för programfunktioner


| Affärsfall| Förväntat resultat |
| - | - |
| Användaren kan hantera medlemskap i programmet| Användaren kan lägga till/ta bort medlemmar som har åtkomst till appen |
| Användaren kan redigera programmet| Användaren kan redigera programmets beskrivning och autentiseringsuppgifter för lösenord SSO-program |

### <a name="rollback-steps"></a>Steg för återställning

Det är viktigt att planera vad du ska göra om distributionen inte går som planerat. Om SSO-konfigurationen misslyckas under distributionen måste du förstå hur du [felsöker SSO-problem](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sso) och minskar påverkan för användarna. Under extrema omständigheter kan du behöva [återställa SSO](../manage-apps/plan-sso-deployment.md).


## <a name="manage-your-implementation"></a>Hantera implementeringen

Du bör använda den minst privilegierade rollen för att utföra en obligatorisk uppgift i Azure Active Directory. [Granska de olika roller som är tillgängliga](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) och välj rätt för att lösa dina behov för varje persona för det här programmet. Vissa roller kan behöva tillämpas tillfälligt och tas bort när distributionen har slutförts.

| Profiler| Roller| Azure AD-roll  |
| - | -| -|
| Helpdesk-administratör| Nivå 1-stöd| Inget |
| Identitetsadministratör| Konfigurera och felsöka när problem påverkar Azure AD| Global administratör |
| Programadministratör| Användarens attestation i programmet, konfiguration för användare med behörigheter| Inget |
| Infrastrukturadministratörer| Certifiera rollover ägare| Global administratör |
| Företagare/intressent| Användarens attestation i programmet, konfiguration för användare med behörigheter| Inget |

Du kan använda [Privilegierad identitetshantering](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) för att hantera dina roller för att tillhandahålla ytterligare granskning, kontroll och åtkomstgranskning för användare med katalogbehörigheter.

### <a name="troubleshoot-access-panel-issues"></a>Felsöka problem på åtkomstpanelen

Skapa felsökningsguider för supportorganisationen med vanliga scenarier, som pekar på Microsoft-dokumentation i sina lösningar. Du kanske vill skapa stödlinjer för de nivåer som används av organisationen.

Se de här felsökningsguiderna som referens:

[Program visas inte](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-application-not-appearing)

[Oväntade program visas](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-unexpected-application)

[Användaren kan inte logga in på åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-web-sign-in-problem)

[Problem med att använda självbetjäningstillgång](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-self-service-access)

[Problem med webbläsartillägget](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-access-panel-browser-extension)

## <a name="next-steps"></a>Nästa steg

[Planera en distribution av Azure Multi-Factor Authentication](https://aka.ms/deploymentplans/mfa)
