---
title: Planera en Azure Active Directory åtkomst panels distribution
description: Vägledning om hur du distribuerar Azure Active Directory åtkomst panelen
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
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76897072"
---
# <a name="plan-an-azure-active-directory-access-panel-deployment"></a>Planera en Azure Active Directory åtkomst panels distribution

Åtkomst panelen Azure Active Directory (Azure AD) är en webbaserad portal som hjälper till att sänka support kostnaderna, öka produktiviteten och säkerheten och minska användningen av användare. Systemet innehåller detaljerad rapportering som spårar när du kommer åt systemet och meddelar administratörer om missbruk eller missbruk.

Med hjälp av Azure AD-åtkomst panelen kan du:

* Identifiera och få åtkomst till alla företagets Azure AD-anslutna resurser, till exempel program
* Begär åtkomst till nya appar och grupper
* Hantera åtkomst till dessa resurser för andra
* Hantera lösen ords återställning med självbetjäning och Azure Multi-Factor Authentication inställningar
* Hantera sina enheter

Administratörer kan också hantera:

* Villkor för tjänsten
* Företag
* Åtkomstgranskningar


## <a name="benefits-of-azure-ad-access-panel-integration"></a>Fördelar med Azure AD Access panel-integrering

Azure AD Access-panelen fördelar företag på följande sätt:

**Ger intuitiv användar upplevelse**: åtkomst panelen ger dig en enda plattform för alla dina Azure Single Sign-on-Connected-program. Du har en enhetlig Portal för att hitta befintliga inställningar och nya funktioner, t. ex. grupp hantering och lösen ords återställning via självbetjäning, när de läggs till. Den intuitiva upplevelsen gör det möjligt för användarna att återgå till att arbeta snabbare och bli mer produktiva, samtidigt som de minskar sin syn.

**Ökar produktiviteten**: alla användar program på åtkomst panelen har SSO aktive rad. Att aktivera SSO över företags program och Office 365 skapar en överlägsen inloggning genom att minska eller eliminera ytterligare prompter för inloggning. Åtkomst panelen använder självbetjäning och dynamiskt medlemskap och förbättrar den övergripande säkerheten för ditt identitets system. Det gör det genom att se till att rätt personer hanterar åtkomst till programmen. Åtkomst panelen fungerar som en enhetlig landnings sida där du snabbt kan hitta resurser och fortsätta arbeta aktiviteter.

**Hanterar kostnad**: att aktivera åtkomst panelen med Azure AD kan hjälpa dig med divestment av lokala infrastrukturer. Det minskar support kostnaderna genom att tillhandahålla en konsekvent Portal för att hitta alla dina appar, begära åtkomst till resurser och hantera konton.

**Ökar flexibiliteten och säkerheten**: åtkomst panelen ger dig till gång till den säkerhet och flexibilitet som en moln plattform erbjuder. Administratörer kan enkelt ändra inställningar till program och resurser och kan hantera nya säkerhets krav utan att påverka användarna.

**Möjliggör robust gransknings-och användnings spårning**: gransknings-och användnings spårning för alla användar funktioner gör att du vet när användarna använder sina resurser och ser till att du kan utvärdera säkerheten.

### <a name="licensing-considerations"></a>Licens överväganden

Åtkomst panelen är kostnads fri och kräver inga licenser för användning på en grundläggande nivå. Antalet objekt i din katalog och de ytterligare funktioner som du vill distribuera kan dock kräva ytterligare licenser. Några vanliga Azure AD-scenarier som har licensierings krav omfattar följande säkerhetsfunktioner:

* [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works)

* [Grupp-baserat medlemskap](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)

* [Självåterställning av lösenord](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)

* [Identitetsskydd för Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

Se den [fullständiga licens guiden för Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites-for-deploying-the-azure-ad-access-panel"></a>Krav för att distribuera Azure AD åtkomst panel

Slutför följande krav innan du påbörjar det här projektet:

* [Integrera Application SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-sso-deployment)

* [Hantera Azure AD-användare och-grupp-infrastruktur](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)

## <a name="plan-the-azure-ad-access-panel-deployment"></a>Planera distribution av Azure AD-åtkomst panel

I följande tabell beskrivs de viktigaste användnings fallen för en åtkomst panels distribution:

| Område| Beskrivning |
| - | - |
| Åtkomst| Åtkomst panels portalen är tillgänglig från företags-och personliga enheter inom företags nätverket. |
|Åtkomst | Åtkomst panels portalen är tillgänglig från företags enheter utanför företags nätverket. |
| Granskning| Användnings data hämtas till företags system minst var 29: e dag. |
| Styrning| Livs cykeln för användar tilldelningarna till Azure AD-anslutna program och grupper definieras och övervakas. |
| Säkerhet| Åtkomst till resurser styrs via användar-och grupp tilldelningar. Endast auktoriserade användare kan hantera resurs åtkomst. |
| Prestanda| Tids linjer för distribution av åtkomst tilldelningar dokumenteras och övervakas. |
| Användar upplevelse| Användarna är medvetna om funktionerna i åtkomst panelen och hur de används.|
| Användar upplevelse| Användare kan hantera sina åtkomst till program och grupper.|
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
   * Multi-Factor Authentication inställningar
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
| Fastställa pilot grupper| Identifiera Azure AD-säkerhetsgruppen som ska användas och se till att alla pilot medlemmar ingår i gruppen. |
| Bestäm vilka grupper eller grupper som ska aktive ras för produktion.| Identifiera Azure AD-säkerhetsgrupper, eller de Active Directory grupper som synkroniserats till Azure AD, som ska användas. Se till att alla pilot medlemmar är en del av gruppen. |
| Tillåt användare att använda SSO för vissa typer av program| Federerad SSO, OAuth, lösen ords inloggning, App-proxy |
| Tillåt användare att använda självbetjäning för återställning av lösen ord | Ja |
| Tillåt användare att använda Multi-Factor Authentication| Ja |
| Tillåt användare att använda självbetjänings grupp hantering för vissa typer av grupper| Säkerhets grupper, Office 365-grupper |
| Tillåt användare att använda självbetjänings program hantering| Ja |
| Tillåt användare att använda åtkomst granskningar| Ja |

### <a name="plan-consent-strategy"></a>Planera godkännande strategin

Användare eller administratörer måste godkänna användnings villkoren och sekretess policyn för ett program. Om möjligt kan du använda administratörs medgivande, vilket ger användarna en bättre upplevelse.

Om du vill använda administratörs medgivande måste du vara global administratör för organisationen och programmen måste vara antingen:

* Registrerad i din organisation

* Registreras i en annan Azure AD-organisation och har tidigare godkänts av minst en användare

Mer information finns i Konfigurera hur slutanvändare [godkänner ett program i Azure Active Directory](configure-user-consent.md).

### <a name="engage-the-right-stakeholders"></a>Engagera rätt intressenter

När teknik projekt inte fungerar, gör de vanligt vis på grund av felmatchade förväntningar på påverkan, resultat och ansvars områden. För att undvika dessa fall GRO par bör [du se till att du är engagerande rätt intressenter](../fundamentals/active-directory-deployment-plans.md) och att från intressenter-rollerna i projektet är väl förstå.

### <a name="plan-communications"></a>Planera kommunikation

Kommunikationen är nödvändig för att en ny tjänst ska lyckas. Informera användarna om hur och när deras upplevelse kommer att ändras och hur de får support om det behövs.

Även om åtkomst panelen inte brukar skapa användar problem är det viktigt att du förbereder. Skapa guider och en lista över alla resurser för support personalen innan du startar.

#### <a name="communications-templates"></a>Kommunikations mallar

Microsoft tillhandahåller [anpassningsbara mallar för e-post och annan kommunikation](https://aka.ms/APTemplates) för åtkomst panelen. Du kan anpassa dessa till gångar för användning i andra kommunikations kanaler efter behov för din företags kultur.

## <a name="plan-your-sso-configuration"></a>Planera SSO-konfigurationen

När en användare loggar in till ett program, går de igenom en autentiseringsprocess och de måste bevisa vem de är. Utan SSO lagras ett lösen ord i programmet och användaren måste känna till det här lösen ordet. Med SSO skickas användarnas autentiseringsuppgifter till programmet, så de behöver inte ange lösen orden för varje program igen.

SSO måste vara aktiverat för att du ska kunna starta program i Mina appar.

Azure AD har stöd för tre olika sätt att aktivera [enkel inloggning i program](what-is-single-sign-on.md):

* **Federerad enkel inloggning** 
    * Gör det möjligt för ett program att omdirigera till Azure AD för användarautentisering, i stället för att uppmanas att ange ett lösen ord. 
    * Stöds för program som använder protokoll, till exempel SAML 2,0, WS-Federation eller OpenID Connect, och är det mest avancerade läget för enkel inloggning.

* **Lösenordsbaserad enkel inloggning** 
    * Möjliggör säker lagring av lösen ord för program och uppspelning med hjälp av ett webb läsar tillägg eller en mobilapp. 
    * Drar nytta av den befintliga inloggnings processen som tillhandahålls av programmet men gör det möjligt för en administratör att hantera lösen orden. Användaren behöver inte känna till lösen ordet.

* **Befintlig enkel inloggning** 
    * Gör att Azure AD kan dra nytta av befintlig enkel inloggning som har kon figurer ATS för programmet.
    * Gör att dessa program kan länkas till Office 365-eller Azure AD Access panel-portalerna. 
    * Aktiverar ytterligare rapportering i Azure AD när programmen startas där. 
    * Inkluderar användning av Azure Application Proxy och länkat läge för enkel inloggning.

Lär dig hur du konfigurerar ett programs SSO-läge här: [enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

För bästa möjliga upplevelse med sidan Mina appar börjar du med integrering av moln program som är tillgängliga för federerad SSO. Federerad SSO gör det möjligt för användare att ha en enhetlig upplevelse med ett klick i appen och är mer robust i konfigurations kontrollen.

Använd federerad enkel inloggning med Azure AD (OpenID Connect/SAML) när ett program stöder det, i stället för lösenordsbaserad SSO och ADFS.

Mer information om hur du distribuerar och konfigurerar dina SaaS-program finns i [distributions planen SaaS SSO](https://aka.ms/deploymentplans/sso).

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>Planera för att distribuera webb läsar tillägget Mina appar

När användarna loggar in på lösenordsbaserade SSO-program måste de installera och använda säkra inloggnings tillägg för Mina appar. Tillägget kör ett skript som skickar lösen ordet till programmets inloggnings formulär. Användarna uppmanas att installera tillägget när de först startar det lösenordsbaserade SSO-programmet. Mer information om tillägget finns i den här dokumentationen om [hur du installerar åtkomst panelens webb läsar tillägg](https://docs.microsoft.com/azure/active-directory/application-access-panel-extension-problem-installing?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav).

Om du måste integrera lösenordsbaserade SSO-program bör du definiera en mekanism för att distribuera tillägget i skala med webbläsare som [stöds](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction). Alternativen är:

* [grupprincip för Internet Explorer](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)

* [Configuration Manager för Internet Explorer](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

* [Användar driven nedladdning och konfiguration för Chrome, Firefox, Microsoft Edge eller IE](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Läs mer: [så här konfigurerar du enkel inloggning för lösen ord](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery).

Användare som inte använder lösenordsbaserade SSO-program drar också nytta av tillägget. De här fördelarna är möjligheten att starta alla appar från Sök fältet, hitta åtkomst till nyligen använda program och att ha en länk till sidan Mina appar.

Så här ser användaren när ett lösenordsbaserade SSO-program startas för första gången:

![Skärm bild av installations skärmen för webb läsar tillägg för Mina appar ](media/access-panel-deployment-plan/ap-dp-sign-in.png)

#### <a name="plan-for-mobile-access"></a>Planera för mobil åtkomst

En webbläsare som skyddas med Intune-principer (Microsoft Edge eller Intune Managed Browser) krävs för att mobila användare ska kunna starta lösenordsbaserade SSO-program. En princip – skyddad webbläsare möjliggör överföring av lösen ordet som sparats för programmet. Microsoft Edge eller Managed Browser innehåller en uppsättning funktioner för webb data skydd. Du kan också använda Microsoft Edge för företags scenarier på iOS-och Android-enheter. Microsoft Edge stöder samma hanterings scenarier som Intune Managed Browser och förbättrar användar upplevelsen. Läs mer: [Hantera webb åtkomst med hjälp av en Microsoft Intune-princip-skyddad webbläsare](https://docs.microsoft.com/intune/app-configuration-managed-browser).

## <a name="plan-your-my-apps-deployment"></a>Planera distributionen av mina appar

Grunden för åtkomst panelen är programmet som används för att starta Mina appar, som användare kommer åt på [https://myapps.microsoft.com](https://myapps.microsoft.com/). Sidorna Mina appar ger användare en enda plats för att starta sitt arbete och få de program som behövs. Här hittar användarna en lista över alla program som de har enkel inloggning till. 

![En skärm bild av panelen appar](media/access-panel-deployment-plan/ap-dp-app-launcher.png)

Samma program kommer att visas i Office 365-appen starta när användarna använder Office 365-portalen.

Planera i vilken ordning du ska lägga till program i Start programmet för Mina appar och bestäm om du ska lägga ut dem gradvis eller samtidigt. Om du vill göra det skapar du en program inventering som visar typen av autentisering och eventuella befintliga SSO-integrationer för varje program.

#### <a name="add-applications-to-the-my-apps-panel"></a>Lägg till program i panelen Mina appar

Alla Azure AD SSO-aktiverade program kan läggas till i Start programmet för Mina appar. Andra program läggs till med hjälp av det länkade SSO-alternativet. Du kan konfigurera en program panel som länkar till URL: en för ditt befintliga webb program. Med länkad SSO kan du börja dirigera användare till portalen för Mina appar utan att migrera alla program till Azure AD SSO. Du kan gradvis flytta till Azure AD SSO-konfigurerade program utan att störa användarnas upplevelse.

#### <a name="use-my-apps-collections"></a>Använda Mina appar-samlingar

Som standard visas alla program på en enda sida. Men du kan använda samlingar för att gruppera relaterade program och presentera dem på en separat flik, vilket gör dem lättare att hitta. Du kan till exempel använda samlingar för att skapa logiska grupperingar av program för vissa jobb roller, aktiviteter, projekt och så vidare. Mer information finns i så [här anpassar du användar åtkomst paneler med mina appar-samlingar](access-panel-collections.md). 

#### <a name="plan-whether-to-use-my-apps-or-an-existing-portal"></a>Planera om du ska använda Mina appar eller en befintlig Portal

Dina användare kanske redan har ett program eller en annan portal än mina appar. I så fall, Bestäm om du vill stödja båda portalerna eller bara använda en.

Om en befintlig Portal redan används som utgångs punkt för användare kan du integrera mina apps-funktioner med hjälp av URL: er för användar åtkomst. URL: er för användar åtkomst fungerar som direkt länkar till de program som är tillgängliga i portalen Mina appar. Dessa URL: er kan bäddas in i en befintlig webbplats. När en användare väljer länken öppnas programmet från portalen Mina appar.

Du kan hitta URL-egenskapen för användar åtkomst i avsnittet **Egenskaper** i programmet i Azure Portal.

![En skärm bild av panelen appar](media/access-panel-deployment-plan/ap-dp-user-access-url.png)


## <a name="plan-self-service-application-discovery-and-access"></a>Planera identifiering och åtkomst av självbetjänings program

När en central uppsättning program har distribuerats till en användares Mina appar, bör du aktivera funktioner för hantering av självbetjänings program. Med hjälp av appen för självbetjänings identifiering kan användarna:

* Hitta nya appar som ska läggas till i sina appar. 
* Lägg till valfria appar som de kanske inte vet att de behöver under installationen.

Godkännande arbets flöden är tillgängliga för explicit godkännande för åtkomst till program. Användare som är god kännare får meddelanden i portalen Mina appar när det finns väntande begäran om åtkomst till programmet.

## <a name="plan-self-service-group-membership"></a>Plan för självbetjänings grupp medlemskap 

Du kan göra det möjligt för användare att skapa och hantera egna säkerhets grupper eller Office 365-grupper i Azure AD. Ägare av gruppen kan godkänna eller neka medlemskaps begär Anden och delegera kontrollen av grupp medlemskap. Funktioner för grupp hantering via självbetjäning är inte tillgängliga för e-postaktiverade säkerhets grupper eller distributions listor.

Om du vill planera för grupp medlemskap i självbetjäning kontrollerar du om du ska tillåta alla användare i din organisation att skapa och hantera grupper eller endast en delmängd av användarna. Om du tillåter en delmängd av användarna måste du konfigurera en grupp som dessa personer läggs till i. Mer information om hur du aktiverar de här scenarierna finns i Konfigurera självbetjänings [grupp hantering i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) .

## <a name="plan-reporting-and-auditing"></a>Planera rapportering och granskning

Azure AD tillhandahåller [rapporter som ger teknisk och affärs insikter](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/). Samar beta med dina affärs-och teknik program ägare för att ta över ägandet av dessa rapporter och använda dem regelbundet. Följande tabell innehåller några exempel på typiska rapporterings scenarier.

|   | Hantera risk| Öka produktiviteten| Styrning och efterlevnad |
|  - |- | - | - |
| Rapporttyper|  Program behörigheter och användning| Konto etablerings aktivitet| Granska vem som har åtkomst till programmen |
| Möjliga åtgärder| Granska åtkomst; återkalla behörigheter| Åtgärda eventuella etablerings fel| Återkalla åtkomst |

Azure AD behåller de flesta gransknings data i 30 dagar. Data är tillgängliga via Azure Admin Portal eller API som du kan ladda ned till analys systemen.

#### <a name="auditing"></a>Granskning

Gransknings loggar för program åtkomst är tillgängliga i 30 dagar. Om säkerhets granskning i företaget kräver längre kvarhållning måste loggarna exporteras till ett SIEM-verktyg (Security information Event and Management), till exempel Splunk eller ArcSight.

För säkerhets kopiering, rapportering och haveri beredskap, dokumentera den nödvändiga hämtnings frekvensen, vad mål systemet är och vem som ansvarar för att hantera varje säkerhets kopia. Du kanske inte behöver separata säkerhets kopior av granskning och rapportering. Din säkerhets kopiering av haveri beredskap bör vara en separat entitet.

## <a name="deploy-applications-to-users-my-apps-panel"></a>Distribuera program till användarnas panel för Mina appar

När ett program har kon figurer ATS för SSO tilldelas grupper åtkomst. Användare i de tilldelade grupperna kommer att ha åtkomst och de kommer att se programmet i sina appar och Office 365-program start.

Se [tilldela användare och grupper till ett program i Active Directory](methods-for-assigning-users-and-groups.md).

Om du under testning eller distribution vill lägga till grupperna, men ännu inte tillåta att programmen visas i Mina appar, se [Dölj ett program från användarens upplevelse i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

### <a name="deploy-microsoft-office-365-applications-to-my-apps"></a>Distribuera Microsoft Office 365-program till Mina appar

För Office 365-program får användarna en kopia av Office baserat på licenser som tilldelats dem. En förutsättning för att få åtkomst till Office-program är att användarna ska tilldelas rätt licenser knutna till Office-programmen. När du tilldelar en licens för en användare ser de automatiskt de program som är associerade med licensen på sidan Mina appar och i Start programmet för Office 365.

Om du vill dölja en uppsättning Office-program från användare finns det ett alternativ för att dölja appar från portalen Mina appar, samtidigt som du tillåter åtkomst från Office 365-portalen. Sök efter de här inställningarna i användar inställnings delen av programmet. Läs mer: [Dölj ett program från användarens upplevelse i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

![En skärm bild av hur du konfigurerar hur du döljer program](media/access-panel-deployment-plan/ap-dp-o365-portal.png)

### <a name="deploy-application-self-service-capabilities"></a>Distribuera självbetjänings funktioner för program

Med program åtkomst via självbetjäning kan användare själv identifiera och begära åtkomst till program. Användare har frihet att komma åt de appar som de behöver utan att gå igenom en IT-grupp varje gång de begär åtkomst. När en användare begär åtkomst och godkänns, antingen automatiskt eller manuellt av en app-ägare, läggs de till i en grupp på Server delen. Rapportering är aktiverat för vem som har begärt, godkänt eller tagit bort åtkomst och ger dig kontroll över hanteringen av de tilldelade rollerna.

Du kan delegera godkännande av program åtkomst förfrågningar till affärs god kännare. Affärs god kännaren kan ställa in appens åtkomst lösen ord på sidan Mina appar i affärs god kännaren.

Läs mer: [så här använder du självbetjäning för program åtkomst](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to).

![En skärm bild av konfigurering av hantering av självbetjänings program](media/access-panel-deployment-plan/ap-dp-salesforce.png)

## <a name="validate-your-deployment"></a>Verifiera distributionen

Se till att distributionen av åtkomst panelen är grundligt testade och att en återställnings plan är på plats.

Följande tester bör utföras med både företagsägda enheter och personliga enheter. Dessa test fall bör även avspegla affärs användnings fall. Följande är några fall baserat på exempel affärs kraven i det här dokumentet och på vanliga tekniska scenarier. Lägg till andra som är speciella för dina behov.

#### <a name="application-sso-access-test-case-examples"></a>Exempel på exempel på SSO-åtkomst för programkompatibilitet:


| Företagscase| Expected result |
| - | -|
| Användaren loggar in på Mina appar-portalen| Användaren kan logga in och se sina program |
| User launches a federated SSO application| User is automatically signed in to the application |
| User launches a password SSO application for the first time| User needs to install the My Apps extension |
| User launches a password SSO application a subsequent time| User is automatically signed in to the application |
| User launches an app from Office 365 Portal| User is automatically signed in to the application |
| User launches an app from the Managed Browser| User is automatically signed in to the application |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Application self-service capabilities test case examples


| Företagscase| Expected result |
| - | - |
| User can manage membership to the application| User can add/remove members who have access to the app |
| User can edit the application| User can edit the application’s description and credentials for password SSO applications |

### <a name="rollback-steps"></a>Rollback steps

It’s important to plan what to do if your deployment doesn’t go as planned. If SSO configuration fails during deployment, you must understand how to [troubleshoot SSO issues](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sso) and reduce impact to your users. In extreme circumstances, you might need to [roll back SSO](../manage-apps/plan-sso-deployment.md).


## <a name="manage-your-implementation"></a>Manage your implementation

You should use the least privileged role to accomplish a required task within Azure Active Directory. [Review the different roles that are available](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) and choose the right one to solve your needs for each persona for this application. Some roles might need to be applied temporarily and removed after the deployment is completed.

| Profiler| Roller| Azure AD role  |
| - | -| -|
| Helpdesk admin| Tier 1 support| Inget |
| Identity admin| Configure and debug when issues impact Azure AD| Global administratör |
| Application admin| User attestation in application, configuration on users with permissions| Inget |
| Infrastructure admins| Cert rollover owner| Global administratör |
| Business owner/stakeholder| User attestation in application, configuration on users with permissions| Inget |

You can use [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) to manage your roles to provide additional auditing, control, and access review for users with directory permissions.

### <a name="troubleshoot-access-panel-issues"></a>Troubleshoot Access Panel issues

Create troubleshooting guides for your support organization with common scenarios, which point to Microsoft documentation in their resolutions. You might want to create guides that break support into the tiers used by your organization.

See these troubleshooting guides for reference:

[Applications not appearing](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-application-not-appearing)

[Unexpected applications appearing](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-unexpected-application)

[User cannot sign in to the Access Panel](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-web-sign-in-problem)

[Problems using self-service application access](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-self-service-access)

[Issues with the browser extension](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-access-panel-browser-extension)

## <a name="next-steps"></a>Nästa steg

[Plan a deployment of Azure Multi-Factor Authentication](https://aka.ms/deploymentplans/mfa)
