---
title: Planera en Azure Active Directory åtkomst panels distribution
description: Vägledning om hur du distribuerar funktioner i Azure AD Access panel
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
ms.date: 08/16/2019
ms.author: baselden
ms.reviewer: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 448af7075297c2b18df1eceaeaac34d5da762489
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576529"
---
# <a name="plan-an-azure-active-directory-access-panel-deployment"></a>Planera en Azure Active Directory åtkomst panels distribution

Den Azure Active Directory åtkomst panelen är en webbaserad portal som gör att du kan sänka support kostnaderna, öka produktiviteten och säkerheten och minska användarnas till gång. Systemet innehåller detaljerad rapportering som spårar när användare kommer åt systemet och meddelar administratörer att missbruka eller missbruk.

På Azure Active Directory åtkomst panelen kan användare:

* Identifiera och få åtkomst till företagets Azure Active Directory anslutna resurser, till exempel program.
* Begär åtkomst till nya appar och grupper eller hantera åtkomst till dessa resurser för andra.
* Hantera inställningar för lösen ords återställning via självbetjäning och Multi-Factor Authentication.
* Hantera sina enheter.

Administratörer kan också hantera:

* Användningsvillkor
* Organisationer
* Åtkomstgranskningar


## <a name="benefits-of-azure-active-directory-access-panel-integration"></a>Fördelar med Azure Active Directory åtkomst panels integrering

Åtkomst panelen för Microsoft Azure Active Directory (Azure AD) fördelar företag på följande sätt:

**Ger intuitiv användar upplevelse**: Att aktivera åtkomst panelen ger användare möjlighet att ha en enda start-pad för alla sina Azure Single-Sign-baserade program. As-funktioner som grupp hantering och lösen ords återställning via självbetjäning läggs till, men användare fortsätter att ha en enhetlig Portal för att hitta dessa inställningar. Den intuitiva upplevelsen gör att användarna kan komma igång snabbare och bli mer produktiva, samtidigt som de minskar sin syn.

**Ökar produktiviteten**: Alla användar program i åtkomst panelen har aktiverat enkel inloggning (SSO). Att aktivera enkel inloggning i företags program och Office 365 ger en överlägsen inloggnings upplevelse för befintliga användare, vilket minskar eller eliminerar ytterligare inloggnings meddelanden. Åtkomst panelen möjliggör självbetjäning och dynamiskt medlemskap och förbättrar den övergripande säkerheten för ditt identitets system genom att se till att rätt personer hanterar åtkomst till programmen. Åtkomst panelen fungerar som en enhetlig landnings sida för en användare för att snabbt hitta resurser och fortsätta arbeta uppgifter.

**Hanterar kostnad**: Att aktivera åtkomst panelen med Azure Active Directory kan tillåta divestment lokala infrastrukturer. Det minskar support kostnaderna genom att göra det möjligt för användare att ha en konsekvent Portal för att hitta alla sina appar, begära åtkomst till resurser och hantera sitt konto.

**Ökar flexibiliteten och säkerheten**: Åtkomst panelen ger dig till gång till den säkerhet och flexibilitet som en moln plattform erbjuder. Administratörer kan enkelt ändra inställningar till program och resurser och hantera nya säkerhets krav utan att påverka användarna.

**Möjliggör robust gransknings-och användnings spårning**: Granskning och användnings spårning för alla slut användar funktioner gör att du vet när användarna använder sina resurser och ser till att du bedömer säkerheten.

### <a name="licensing-considerations"></a>Licens överväganden

Åtkomst panelen är kostnads fri och kräver inga licenser för att använda en grundläggande användare. Antalet objekt i din katalog och de funktioner som du vill distribuera kan dock kräva ytterligare licenser. Vanliga Azure AD-scenarier omfattar följande säkerhetsfunktioner som har licensierings krav.

* [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works)

* [Grupp baserat medlemskap](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)

* [Lösen ords krav för självbetjäning](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)

* [Identitets skydd](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

Se den [fullständiga licens guiden för Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites-for-deploying-the-azure-ad-access-panel"></a>Krav för att distribuera Azure AD åtkomst panel

Följande krav måste vara uppfyllda innan projektet börjar.

* [Integrering av Application SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-sso-deployment)

* [Azure AD-användare och-grupp-infrastruktur](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)

## <a name="plan-the-azure-ad-access-panel-deployment"></a>Planera distribution av Azure AD-åtkomst panel

I följande tabell beskrivs de viktigaste användnings fallen för en åtkomst panels distribution:

| Område| Beskrivning |
| - | - |
| Åtkomst| Åtkomst panels portalen är tillgänglig från företags-och personliga enheter inom företags nätverket. |
|Åtkomst | Åtkomst panels portalen är tillgänglig för företags enheter utanför företags nätverket. |
| Granskning| Användnings data hämtas till företags system minst var 29: e dag. |
| Styrning| Livs cykeln för användar tilldelningar till Azure AD-anslutna program och grupper definieras och övervakas. |
| Säkerhet| Åtkomst till resurser styrs via användar-och grupp tilldelningar. Endast auktoriserade användare kan hantera resurs åtkomst. |
| Prestanda| Tids linjer för distribution av åtkomst tilldelningar dokumenteras och övervakas. |
| Användar upplevelse| Användarna är medvetna om funktionerna i åtkomst panelen och hur de används.|
| Användar upplevelse| Användare kan själv hantera sin åtkomst till program och grupper.|
| Användar upplevelse| Användare kan hantera sina konton. |
| Användar upplevelse| Användarna är medvetna om webbläsarkompatibilitet. |
| Support| Användare kan hitta support för problem med åtkomst panelen. |

### <a name="best-practices-for-deploying-the-azure-ad-access-panel"></a>Metod tips för att distribuera Azure AD-åtkomst panel

Funktionerna i åtkomst panelen kan aktive ras gradvis. Vi rekommenderar följande distributions ordning:

1. Mina appar
   * Starta App
   * Hantering av självbetjänings program
   * Microsoft Office 365-integrering

1. Identifiering av självbetjänings program
   * Återställning av lösenord för självbetjäning
   * Inställningar för Multi-Factor Authentication
   * Enhetshantering
   * Användningsvillkor
   * Hantera organisationer

1. Mina grupper
   * Självbetjäning, grupphantering
1. Åtkomstgranskningar
   * Åtkomst gransknings hantering

Från och med mina appar introducerar användarna till portalen som en gemensam plats för att få åtkomst till resurser. Tillägget av självbetjänings-och program identifiering bygger på mina apps-upplevelser. Mina grupper och åtkomst granskningar bygger på självbetjänings funktionerna.

### <a name="plan-configurations-for-the-azure-ad-access-panel"></a>Planera konfigurationer för Azure AD-åtkomst panel

I följande tabell visas flera viktiga konfigurationer för åtkomst panelen och de typiska värden som du kan använda:

| Konfiguration| Typiska värden |
| - | - |
| Fastställ pilot grupp (er)| Identifiera Azure AD-säkerhetsgruppen som ska användas och se till att alla pilot medlemmar ingår i gruppen. |
| Bestäm vilka grupper eller grupper som ska aktive ras för produktion.| Identifiera de Azure AD-säkerhetsgrupper eller AD-grupper som synkroniserats till Azure AD, som ska användas. Se till att alla pilot medlemmar är en del av gruppen. |
| Tillåt användare att använda enkel inloggning på vilka typer av program| Federerad SSO, OAuth, lösen ords inloggning, App-proxy |
| Tillåt användare att använda självbetjäning för återställning av lösen ord| Ja |
| Tillåt användare att använda Multi-Factor Authentication| Ja |
| Tillåt användare att använda självbetjänings grupp hantering för vilka typer av grupper| Säkerhets grupper, O365-grupper |
| Tillåt användare att använda självbetjänings program hantering| Ja |
| Tillåt användare att använda åtkomst granskningar| Ja |

### <a name="plan-consent-strategy"></a>Planera godkännande strategin

Användare eller administratörer måste godkänna användnings villkoren och sekretess policyn för ett program. Om så är möjligt rekommenderar vi att du använder administratörs medgivande, vilket ger användarna en bättre upplevelse.

Om du vill använda administratörs medgivande måste du vara en global administratör för klienten och programmen måste vara:

* Registrerad i din klient eller

* Registreras i en annan Azure AD-klient och har tidigare godkänts av minst en användare.

Mer information finns i [Konfigurera hur slutanvändare godkänner ett program i Azure Active Directory](configure-user-consent.md).

### <a name="engage-the-right-stakeholders"></a>Engagera rätt intressenter

När teknik projekt inte fungerar, gör de vanligt vis på grund av felmatchade förväntningar på påverkan, resultat och ansvars områden. För att undvika dessa fall GRO par bör [du se till att du är engagerande rätt intressenter](../fundamentals/active-directory-deployment-plans.md) och att från intressenter-rollerna i projektet är väl förstå.

### <a name="plan-communications"></a>Planera kommunikation

Kommunikationen är nödvändig för att en ny tjänst ska lyckas. Informera användarna om hur och när deras upplevelse kommer att ändras och hur du får support om det behövs.

Även om åtkomst panelen inte brukar skapa användar problem är det viktigt att du förbereder. Skapa guider och en lista över alla resurser för support personalen innan du startar.

#### <a name="communications-templates"></a>Kommunikations mallar

Microsoft tillhandahåller [anpassningsbara mallar för e-post och annan kommunikation](https://aka.ms/APTemplates) för åtkomst panelen. Du kan anpassa dessa till gångar för användning i andra kommunikations kanaler efter behov för din företags kultur.

## <a name="plan-your-sso-configuration"></a>Planera SSO-konfigurationen

När en användare loggar in till ett program, går de igenom en autentiseringsprocess och de måste bevisa vem de är. Utan enkel inloggning, ett lösen ord som lagras i programmet och användaren måste känna till det här lösen ordet. Användarens autentiseringsuppgifter för enkel inloggning (SSO) skickas till programmet så att de inte behöver ange lösen orden för varje program igen.

För att starta program i Mina appar måste enkel inloggning (SSO) vara aktive rad för programmen.

Azure AD har stöd för tre olika sätt att aktivera [enkel inloggning till program](what-is-single-sign-on.md):

* **Federerad enkel inloggning** 
    * Gör det möjligt för ett program att omdirigera till Azure AD för användarautentisering i stället för att uppmanas att ange ett lösen ord. 
    * Stöds för program som använder protokoll som SAML 2,0, WS-Federation eller OpenID Connect och är det mest avancerade läget för enkel inloggning.

* **Lösenordsbaserad enkel inloggning** 
    * Möjliggör säker lagring av lösen ord för program och uppspelning med ett webb läsar tillägg eller en mobilapp. 
    * Utnyttjar den befintliga inloggnings processen som tillhandahålls av programmet men gör det möjligt för en administratör att hantera lösen orden. Användaren behöver inte känna till lösen ordet.

* **Befintlig enkel inloggning** 
    * Gör att Azure AD kan utnyttja en befintlig enkel inloggning som har kon figurer ATS för programmet.
    * Gör att dessa program kan länkas till Office 365-eller Azure AD Access panel-portalerna. 
    * Aktiverar ytterligare rapportering i Azure AD när programmen startas där. 
    * Inkluderar användning av Azure Application Proxy och länkat läge för enkel inloggning.

Lär dig hur du konfigurerar ett programs SSO-läge här: [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

För bästa möjliga upplevelse med sidan Mina appar rekommenderar vi att du börjar med att integrera moln program som är tillgängliga för federerad SSO. Federerad SSO gör det möjligt för användare att ha en enhetlig upplevelse med ett klick i appen och är mer robust i konfigurations kontrollen.

Använd federerad enkel inloggning med Azure AD (OpenID Connect/SAML) när ett program stöder det, i stället för lösenordsbaserad SSO och ADFS.

Mer information om hur du distribuerar och konfigurerar SaaS-program finns i distributions [planen SaaS SSO](https://aka.ms/deploymentplans/sso).

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>Planera för att distribuera webb läsar tillägget Mina appar

Om lösenordsbaserade SSO-program görs tillgängliga för slutanvändare måste de installera det säkra inloggnings tillägget Mina appar för att logga in. Tillägget kör ett skript som skickar lösen ordet till programmets inloggnings formulär. Användarna uppmanas att installera tillägget när de först startar det lösenordsbaserade SSO-programmet. Mer information om tillägget finns i vår dokumentation om hur [du installerar åtkomst panelens webb läsar tillägg](https://docs.microsoft.com/azure/active-directory/application-access-panel-extension-problem-installing?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav).

Om du måste integrera lösenordsbaserade SSO-program, bör du definiera en mekanism för att distribuera tillägget i skala med webbläsare som [stöds](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction). Alternativen är:

* [grupprincip för Internet Explorer](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)

* [System Center Configuration Manager (SCCM) för Internet Explorer](https://docs.microsoft.com/sccm/core/clients/deploy/deploy-clients-to-windows-computers)

* [Användar driven nedladdning och konfiguration för Chrome, Firefox, Microsoft Edge eller IE](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Läs mer: [Så här konfigurerar du lösen ord för enkel inloggning](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery).

Användare som inte använder lösenordsbaserade SSO-program drar också nytta av tillägget. De här fördelarna är möjligheten att starta alla appar från Sök fältet, hitta åtkomst till nyligen använda program och att ha en länk till sidan Mina appar.

Så här ser användaren när ett lösenordsbaserade SSO-program startas för första gången:

![Skärm bild av installations skärmen för webb läsar tillägg för Mina appar ](media/access-panel-deployment-plan/ap-dp-sign-in.png)

#### <a name="plan-for-mobile-access"></a>Planera för mobil åtkomst

En webbläsare som skyddas med Intune-principer (Microsoft Edge eller Intune Managed Browser) krävs för att mobila användare ska kunna starta lösenordsbaserade SSO-program. En princip – skyddad webbläsare möjliggör överföring av lösen ordet som sparats för programmet. Microsoft Edge eller Managed Browser innehåller en uppsättning funktioner för webb data skydd. Du kan också använda Microsoft Edge för företags scenarier på iOS-och Android-enheter. Microsoft Edge stöder samma hanterings scenarier som Intune Managed Browser och förbättrar slut användar upplevelsen. Läs mer: [Hantera webb åtkomst med hjälp av en Microsoft Intune-princip – skyddad webbläsare](https://docs.microsoft.com/intune/app-configuration-managed-browser)

## <a name="plan-your-my-apps-deployment"></a>Planera distributionen av mina appar

Grunden för åtkomst panelen är programmet som används för att starta Mina appar, som användare kommer [https://myapps.microsoft.com](https://myapps.microsoft.com/)åt på. Sidorna Mina appar ger användare en enda plats för att starta sitt arbete och få de program som behövs. Här hittar användarna en lista över alla program som de har enkel inloggning till. 

![En skärm bild av panelen appar](media/access-panel-deployment-plan/ap-dp-app-launcher.png)

Samma program kommer att visas i Office 365-appen starta när användarna använder Office 365-portalen.

Planera i vilken ordning du ska lägga till program i Start programmet för Mina appar och om du ska distribuera dem gradvis eller samtidigt. Om du vill göra det skapar du en program inventering som visar typen av autentisering och eventuella befintliga enkel inloggnings-och SSO-integrationer för varje program.

#### <a name="add-applications-to-the-my-apps-panel"></a>Lägg till program i panelen Mina appar

Alla Azure AD SSO-aktiverade program kan läggas till i Start programmet för Mina appar. Andra program läggs till med hjälp av det länkade SSO-alternativet. Du kan konfigurera en program panel som länkar till URL: en för ditt befintliga webb program. Med länkad SSO kan du börja dirigera användare till portalen för Mina appar utan att behöva migrera alla program till Azure Active Directory SSO. Du kan gradvis flytta till Azure AD SSO-konfigurerade program utan att störa användarnas upplevelse.

#### <a name="plan-whether-to-use-my-apps-or-an-existing-portal"></a>Planera om du ska använda Mina appar eller en befintlig Portal

Användarna har kanske redan ett program eller en annan portal än mina appar. I så fall, Bestäm om du vill stödja båda portalerna eller om du bara ska använda en.

Om en befintlig Portal redan används som utgångs punkt för användare kan du integrera mina apps-funktioner med hjälp av "URL för användar åtkomst". URL: er för användar åtkomst fungerar som direkt länkar till de program som är tillgängliga i portalen Mina appar. Dessa URL: er kan bäddas in i en befintlig webbplats. När en användare klickar på länken startas programmet från portalen Mina appar.

Du kan hitta URL-egenskapen för användar åtkomst i avsnittet Egenskaper i programmet i Azure Portal.

![En skärm bild av panelen appar](media/access-panel-deployment-plan/ap-dp-user-access-url.png)


## <a name="plan-self-service-application-discovery-and-access"></a>Planera identifiering och åtkomst av självbetjänings program

När en central uppsättning program har distribuerats till en användares Mina appar, rekommenderar vi att du aktiverar hanterings funktioner för självbetjänings program. Med appen för självbetjänings identifiering kan du:
* Användare kan hitta nya appar som de kan lägga till i sina appar. 
* Användare kan lägga till valfria appar som du inte vet att de behöver under installationen.

Godkännande arbets flöden är tillgängliga för explicit godkännande för åtkomst till program. Användare som är god kännare får meddelanden i portalen Mina appar när det finns väntande begäran om åtkomst till programmet.

## <a name="plan-self-service-group-membership"></a>Plan för självbetjänings grupp medlemskap 

Du kan göra det möjligt för användare att skapa och hantera egna säkerhets grupper eller Office 365-grupper i Azure Active Directory (Azure AD). Ägare av gruppen kan godkänna eller neka medlemskaps begär Anden och delegera kontrollen av grupp medlemskap. Funktioner för grupp hantering via självbetjäning är inte tillgängliga för e-postaktiverade säkerhets grupper eller distributions listor.

Om du vill planera för grupp medlemskap i självbetjäning, avgör du om du ska tillåta alla användare i din organisation att skapa och hantera grupper, eller endast en delmängd av användarna. Om du har en delmängd av användarna måste du konfigurera en grupp som dessa personer läggs till i. Mer information om hur du aktiverar de här scenarierna finns i Konfigurera självbetjänings [grupp hantering i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) .

## <a name="plan-reporting-and-auditing"></a>Planera rapportering och granskning

Azure AD tillhandahåller [rapporter som ger teknisk och affärs](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/)insikter. Samar beta med dina affärs-och teknik program ägare för att kunna ta del av de här rapporterna med jämna mellanrum. Tabellen nedan innehåller några exempel på typiska rapporterings scenarier.

|   | Hantera risk| Öka produktiviteten| Styrning och efterlevnad |
|  - |- | - | - |
| Rapporttyper|  Program behörigheter och användning.| Konto etablerings aktivitet| Granska vem som har åtkomst till programmen |
| Möjliga åtgärder| Granska åtkomst; återkalla behörigheter| Åtgärda eventuella etablerings fel| Återkalla åtkomst |

Azure AD behåller de flesta gransknings data i 30 dagar. Data är tillgängliga via Azure Admin Portal eller API som du kan ladda ned till analys systemen.

#### <a name="auditing"></a>Granskning

Gransknings loggar för program åtkomst är tillgängliga i 30 dagar. Om säkerhets granskning i företaget kräver längre kvarhållning måste loggarna exporteras till ett SIEM-verktyg (Security information Event and Management), till exempel Splunk eller ArcSight.

För säkerhets kopiering, rapportering och haveri beredskap, dokumentera den nödvändiga hämtnings frekvensen, mål systemet är och vem som ansvarar för att hantera varje säkerhets kopia. Du kanske inte behöver separata säkerhets kopior av granskning och rapportering. Din säkerhets kopiering av haveri beredskap bör vara en separat entitet.

## <a name="deploy-applications-to-users-my-apps-panel"></a>Distribuera program till användarnas panel för Mina appar

När ett program har kon figurer ATS för SSO tilldelas grupper åtkomst. Användare i de tilldelade grupperna har åtkomst och kommer att se programmet i sina appar och Office 365 App Launcher

Se [tilldela användare och grupper till ett program i Active Directory](methods-for-assigning-users-and-groups.md).

Om du under testning eller distribution vill lägga till grupperna, men ännu inte tillåta att programmen visas i Mina appar, se [Dölj ett program från användarens upplevelse i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

### <a name="deploy-microsoft-office-365-applications-to-my-apps"></a>Distribuera Microsoft Office 365-program till Mina appar

För Office 365-program får användarna en kopia av Office baserat på licenser som tilldelats dem. En förutsättning för att få åtkomst till Office-program är att tilldela användare rätt licenser knutna till Office-programmen. När du tilldelar en användare licensen ser de program som är kopplade till licensen automatiskt på sidan Mina appar och i Start programmet för O365-appen.

Om du vill dölja en uppsättning Office-program från användare finns det ett alternativ för att dölja appar från portalen Mina appar, samtidigt som åtkomsten till O365-portalen fortfarande tillåts. Sök efter de här inställningarna i användar inställnings delen av programmet. Läs mer: [Dölj ett program från användarens upplevelse i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

![En skärm bild av hur du konfigurerar hur du döljer program](media/access-panel-deployment-plan/ap-dp-o365-portal.png)

### <a name="deploy-application-self-service-capabilities"></a>Distribuera självbetjänings funktioner för program

Med program åtkomst via självbetjäning kan användare själv identifiera och begära åtkomst till alla program. Användare får friheten att få åtkomst till de appar som de behöver utan att gå igenom en IT-grupp varje gång de begär åtkomst. När en användare begär åtkomst och godkänns antingen automatiskt eller manuellt av en app-ägare, läggs de till i en grupp på Server delen. Rapportering är aktiverat för vem som har begärt, godkänt eller tagit bort åtkomst och ger dig kontroll över hanteringen av de tilldelade rollerna.

Du kan delegera godkännande av program åtkomst förfrågningar till affärs god kännare. Affärs god kännaren kan ange lösen ord för åtkomst till appen direkt från sidan för affärs god kännaren Mina appar.

Läs mer: [Använda självbetjäning för program åtkomst](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to)

![En skärm bild av konfigurering av hantering av självbetjänings program](media/access-panel-deployment-plan/ap-dp-salesforce.png)

## <a name="validate-your-deployment"></a>Verifiera distributionen

Se till att distributionen av åtkomst panelen är grundligt testade och att en återställnings plan är på plats.

Följande tester bör utföras med både företagsägda enheter och personliga enheter. Dessa test fall bör även avspegla affärs användnings fall. Följande är några fall baserat på exempel affärs kraven i det här dokumentet och på vanliga tekniska scenarier. Lägg till andra som är speciella för dina behov.

#### <a name="application-sso-access-test-case-examples"></a>Exempel på exempel på SSO-åtkomst för programkompatibilitet:


| Företagscase| Förväntat resultat |
| - | -|
| Användaren loggar in på Mina appar-portalen| Användaren kan logga in och se sina program |
| Användaren startar ett externt SSO-program| Användaren loggas automatiskt in i programmet |
| Användaren startar ett SSO-program för första gången| Användaren måste installera tillägget Mina appar |
| Användaren startar ett SSO-program en gång efter| Användaren loggas automatiskt in i programmet |
| Användaren startar en app från O365-portalen| Användaren loggas automatiskt in i programmet |
| Användaren startar en app från Managed Browser| Användaren loggas automatiskt in i programmet |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Exempel på test fall för program självbetjänings funktioner


| Företagscase| Förväntat resultat |
| - | - |
| Användaren kan hantera medlemskap för programmet| Användare kan lägga till/ta bort medlemmar som har åtkomst till appen |
| Användaren kan redigera programmet| Användaren kan redigera programmets beskrivning och autentiseringsuppgifter för SSO-program för lösen ord |

### <a name="rollback-steps"></a>Återställnings steg

Det är viktigt att planera vad som ska göras om distributionen inte fungerar som planerat. Om SSO-konfigurationen Miss lyckas under distributionen måste du förstå hur du [felsöker SSO-problem](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sso) och minskar påverkan på användarna. I extrema fall kan du behöva [återställa SSO](../manage-apps/plan-sso-deployment.md).


## <a name="manage-your-implementation"></a>Hantera din implementering

Microsoft rekommenderar att du använder den minst privilegierade rollen för att utföra en obligatorisk uppgift i Azure Active Directory. Microsoft rekommenderar att [du granskar de olika roller som är tillgängliga](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) och hur du väljer rätt för att lösa dina behov för varje person i det här programmet. Vissa roller kan behöva tillämpas tillfälligt och tas bort när distributionen har slutförts.

| Profiler| Roller| Azure AD-roll  |
| - | -| -|
| Support administratör| Nivå 1-stöd| Inga |
| Identitets administratör| Konfigurera och Felsök när problem påverkar Azure AD| Global administratör |
| Program administratör| Användar attestering i program, konfiguration av användare med behörigheter| Inga |
| Infrastruktur administratörer| Certifikat förnyelse ägare| Global administratör |
| Företags ägare/från intressenter| Användar attestering i program, konfiguration av användare med behörigheter| Inga |

Vi rekommenderar att du använder [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) för att hantera dina roller för att ge ytterligare gransknings-, kontroll-och åtkomst granskning för användare med katalog behörigheter.

### <a name="troubleshoot-access-panel-issues"></a>Felsöka problem med åtkomst panelen

Skapa fel söknings guider för support organisationen med vanliga scenarier och peka på Microsoft-dokumentationen om deras resolutioner. Du kanske vill skapa guider som bryter supporten till de nivåer som används av din organisation.

Se följande fel söknings guider för referens:

[Program som inte visas](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-application-not-appearing)

[Oväntade program visas](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-unexpected-application)

[Användaren kan inte logga in på åtkomst panelen](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-web-sign-in-problem)

[Problem med självbetjäning för program åtkomst](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-self-service-access)

[Problem med webb läsar tillägget](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-access-panel-browser-extension)

## <a name="next-steps"></a>Nästa steg

[Planera distribution av Azure Active Directory Multi-Factor Authentication](https://aka.ms/deploymentplans/mfa)
