---
title: Arkiveringsjobb för vad som är nytt? – Azure Active Directory | Microsoft Docs
description: Vad är ny viktig i översikten delen av här Innehållspaketet innehåller 6 månaderna för aktiviteten. Efter sex månader artiklarna tas bort från den huvudsakliga artikeln och placera den här artikeln i arkivet.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fd42e1e06086a12b159a8510c3d53a7f32e46f6
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194647"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Arkiveringsjobb för vad som är nytt? i Azure Active Directory

Primärt [vad är nytt viktig](whats-new.md) artikeln innehåller de senaste 6 månaderna av information, även om den här artikeln innehåller all information som är äldre.

Vad är nytt viktig information innehåller information om:

- Den senaste versionen
- Kända problem
- Felkorrigeringar
- Inaktuell funktion
- Planer för ändringar

---

## <a name="july-2018"></a>Juli 2018

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Förbättringar av e-postfunktionen i Azure Active Directory

**Typ:** Ändrad funktion  
**Tjänstekategori:** Annat  
**Produkten kapacitet:** Identitetslivcykelhantering
 
Azure Active Directory (Azure AD) e-postmeddelanden har nu en uppdaterad design, samt ändringar i avsändarens e-postadress och visningsnamn för avsändaren, när de skickas från följande tjänster:
 
- Azure AD-Åtkomstgranskningar
- Azure AD Connect Health 
- Azure AD Identity Protection 
- Azure AD Privileged Identity Management
- Enterprise-appen upphör att gälla certifikat-meddelanden
- Etablering tjänstmeddelanden i Enterprise-App
 
E-postmeddelanden skickas från följande e-postadress och ditt visningsnamn:

- E-postadress: azure-noreply@microsoft.com
- Visningsnamn: Microsoft Azure
 
Ett exempel på några av de nya e-designerna och mer information finns i [e-postmeddelanden i Azure AD PIM](https://go.microsoft.com/fwlink/?linkid=2005832).

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Azure AD-aktivitetsloggar är nu tillgängliga på Azure Monitor

**Typ:** Ny funktion  
**Tjänstekategori:** Rapportering  
**Produkten kapacitet:** Övervakning och rapportering

Azure AD-aktivitetsloggar finns nu i offentlig förhandsversion för Azure Monitor (Azures plattform hela övervakningstjänsten). Azure Monitor erbjuder långsiktig kvarhållning och smidig integrering, utöver dessa förbättringar:

- Långsiktig kvarhållning av Routning din loggfiler till din egen Azure storage-konto.

- Sömlös SIEM-integrering, utan att behöva skriva eller underhålla anpassade skript.

- Sömlös integrering med dina egna anpassade lösningar, analysverktyg eller lösningar för hantering av incidenter.

Mer information om de här nya funktionerna finns i vår blogg [Azure AD-aktivitetsloggar i Azure Monitor diagnostik finns nu i offentlig förhandsversion](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) och dokumentationen för [Azure Active Directory-aktivitetsloggar i Azure Övervakning (förhandsversion)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Information för villkorlig åtkomst har lagts till iAzure AD-inloggningsrapporten

**Typ:** Ny funktion  
**Tjänstekategori:** Rapportering  
**Produkten kapacitet:** Identitetssäkerhet och skydd
 
Den här uppdateringen kan du se vilka principer utvärderas när en användare loggar in tillsammans med principen resultatet. Dessutom kan innehåller rapporten nu typ av klientapp som används av användaren, så att du kan identifiera äldre protokolltrafik. Rapporten poster kan nu även genomsökas för ett Korrelations-ID som finns i felmeddelandet användarinriktade och kan användas för att identifiera och Felsök matchande inloggningsbegäran.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Visa äldre autentiseringar via inloggningsaktivitetsloggar

**Typ:** Ny funktion  
**Tjänstekategori:** Rapportering  
**Produkten kapacitet:** Övervakning och rapportering
 
Med introduktionen av den **Klientappen** fält i inloggningsaktivitet loggar, kunder kan nu se användare som använder äldre autentiseringar. Kunder kommer att kunna komma åt informationen med inloggningar MS Graph API eller via inloggningen aktivitetsloggar i Azure AD-portalen där du kan använda den **Klientappen** kontroll för att filtrera på äldre autentiseringar. Läs igenom dokumentationen för mer information.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Nya federerade appar finns tillgängliga i Azure AD-appgalleriet – juni 2018

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** Tredjepartsintegration
 
I juli 2018 har vi lagt till dessa 16 nya appar med stöd för app-galleriet:

[Innovation Hub](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [vissa Admin SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), PSUC mellanlagring [iPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [skärmbild-O-till](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial) , PowerSchool Unified klassrum, [Eli Onboarding](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial), [Bomgar Remote Support](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [Nimblex](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [Imagineer WebVision](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial) , [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [SecureW2 JoinNow Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [Kanbanize](https://review.docs.microsoft.com/azure/active-directory/saas-apps/kanbanize-tutorial), [SmartLPA](https://review.docs.microsoft.com/azure/active-directory/saas-apps/smartlpa-tutorial), [kunskaper Base](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

Mer information om apparna som finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial). Läs mer om att lista ditt program i Azure AD-appgalleri [lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Ny användarförsörjning för SaaS-appintegreringar – juli 2018

**Typ:** Ny funktion  
**Tjänstekategori:** App-etablering  
**Produkten kapacitet:** Tredjepartsintegration
 
Azure AD kan du automatisera den genereringen, underhållet och borttagningen av användaridentiteter i SaaS-program, till exempel Dropbox, Salesforce, ServiceNow med mera. Vi har lagt till stöd för följande program i Azure AD-appgalleriet för användaretablering för juli 2018:

- [Cisco Spark](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-spark-provisioning-tutorial)

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonusly](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

En lista över alla program som stöder etableringen av användare i Azure AD-galleriet finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health för synkronisering – ett enklare sätt att korrigera överblivna och dubblettattributsynkroniseringsfel på

**Typ:** Ny funktion  
**Tjänstekategori:** AD Connect  
**Produkten kapacitet:** Övervakning och rapportering
 
Azure AD Connect Health introducerar hjälpåtgärder för att du kan markera och åtgärda synkroniseringsfel. Den här funktionen felsöker Duplicerat attribut synkroniseringsfel och korrigeringar objekt som kopplas från Azure AD. Den här diagnos har följande fördelar:

- Begränsar Duplicerat attribut synkroniseringsfel att tillhandahålla specifikt korrigeringar

- Gäller en korrigering för särskilda scenarier för Azure AD, lösa problem i ett enda steg

- Ingen uppgradering eller konfiguration krävs för att aktivera och använda den här funktionen

Mer information finns i [diagnostisera och åtgärda synkroniseringsfel Duplicerat attribut](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Visuella uppdateringar av Azure AD och MSA-inloggningsupplevelserna

**Typ:** Ändrad funktion  
**Tjänstekategori:** Azure AD  
**Produkten kapacitet:** Användarautentisering

Vi har uppdaterat i Användargränssnittet för Microsoft online services-inloggningen, till exempel för Office 365 och Azure. Den här ändringen gör skärmarna mindre rörig och enklare. Mer information om den här ändringen finns i den [kommande förbättringar till Azure AD-inloggningen](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/) blogg.

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>En ny version av Azure AD Connect – juli 2018

**Typ:** Ändrad funktion  
**Tjänstekategori:** App-etablering  
**Produkten kapacitet:** Livscykelhantering för identiteter

Den senaste versionen av Azure AD Connect innehåller: 

- Felkorrigeringar och support-uppdateringar 

- Allmän tillgänglighet för Ping-federera-integrering

- Uppdateringar till den senaste SQL 2012-klienten 

Mer information om den här uppdateringen finns i [Azure AD Connect: Versionshistorik](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)

---

### <a name="updates-to-the-terms-of-use-tou-end-user-ui"></a>Uppdateringar av användningsvillkoren för slutanvändargränssnittet

**Typ:** Ändrad funktion  
**Tjänstekategori:** Användningsvillkor  
**Produkten kapacitet:** Styrning

Vi uppdaterar godkännande-strängen i Gränssnittet Användningsvillkoren för slutanvändare.

**Aktuell text.** För att komma åt resurser i [tenantName], måste du godkänna användningsvillkoren.<br>**Ny text.** Du måste läsa användningsvillkoren för att komma åt [tenantName] resurs.

**Aktuell text:** Om du väljer att acceptera innebär det att du godkänner alla ovanstående användningsvillkor.<br>**Ny text:** Klicka på Godkänn för att bekräfta att du har läst och förstått användningsvillkoren.

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Direktautentisering stöder äldre protokoll och program

**Typ:** Ändrad funktion  
**Tjänstekategori:** Autentiseringar (inloggningar)  
**Produkten kapacitet:** Användarautentisering
 
Direktautentisering nu stöder äldre protokoll och appar. Följande begränsningar finns nu fullständigt stöd:

- Användarinloggningar till äldre Office-program, Office 2010 och Office 2013, utan modern autentisering.

- Åtkomst till delade kalendrar och ledig eller upptagen i Exchange hybridmiljöer på Office 2010 endast.

- Användarinloggningar till Skype för företag-klientprogram utan modern autentisering.

- Användarinloggningar till PowerShell version 1.0.

- I Apples DEP (Apple DEP), med hjälp av iOS-Installationsassistenten. 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Hantering av konvergerad säkerhetsinformation för återställning av lösenord genom självbetjäning och Multi-Factor Authentication

**Typ:** Ny funktion  
**Tjänstekategori:** SSPR  
**Produkten kapacitet:** Användarautentisering

Den här nya funktionen kan användarna hantera deras säkerhetsinformation (till exempel telefonnummer, e-postadress, mobilapp och så vidare) för lösenordsåterställning via självbetjäning (SSPR) och Multi-Factor Authentication (MFA) i en enskild upplevelse. Användare kommer inte längre att registrera samma säkerhetsinformationen för SSPR och MFA i två olika upplevelser. Den här nya upplevelsen gäller även för användare som har SSPR eller MFA.

Om en organisation inte är tvingande MFA eller SSPR-registrering, användare kan registrera deras säkerhetsinformation via den **Mina appar** portal. Därifrån kan användare registrera andra metoder som aktiverats för MFA eller SSPR. 

Det här är en valbar offentlig förhandsversion. Administratörer kan aktivera den nya upplevelsen (om det behövs) för en grupp av användare eller alla användare i en klient.

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Använd Microsoft Authenticator-appen när du ska verifiera din identitet då du återställer ditt lösenord

**Typ:** Ändrad funktion  
**Tjänstekategori:** SSPR  
**Produkten kapacitet:** Användarautentisering

Den här funktionen kan icke-administratörer verifiera sin identitet när du återställer ett lösenord med hjälp av en avisering eller kod från Microsoft Authenticator (eller andra authenticator-appen). När administratörer har aktiverat den här lösenordsåterställning via självbetjäning metod, användare som har registrerat en mobil app via aka.ms/mfasetup eller aka.ms/setupsecurityinfo kan använda sina mobila appar som en verifieringsmetod vid återställning av lösenordet.

Mobilappavisering kan endast aktiveras som en del av en princip som kräver två metoder för att återställa ditt lösenord.

---

## <a name="june-2018"></a>Juni 2018

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Ändringsmeddelande: Säkerhetskorrigering till delegerad auktorisering flödet för appar som använder Azure AD-aktivitet loggar API

**Typ:** Förändringsplan  
**Tjänstekategori:** Rapportering  
**Produkten kapacitet:** Övervakning och rapportering

På grund av våra starkare säkerhet tvingande som vi har haft att göra en ändring i behörigheter för appar som använder ett flöde för delegerad auktorisering för att komma åt [Azure AD-aktivitet loggar API: er](https://aka.ms/aadreportsapi). Den här ändringen utförs av **26 juni 2018**.

Om någon av dina appar använder Azure AD-aktivitet Log API: er följer du stegen nedan för att se till att appen inte bryta när ändringen har skett.

**Uppdatera app-behörigheter**

1. Logga in på Azure portal, Välj **Azure Active Directory**, och välj sedan **Appregistreringar**.
2. Välj din app som använder Azure AD-aktivitet loggar API, Välj **inställningar**väljer **nödvändiga behörigheter**, och välj sedan den **Windows Azure Active Directory** API.
3. I den **delegerade behörigheter** område i den **Aktivera åtkomst** bladet markerar du kryssrutan bredvid **Läs directory** data och välj sedan **spara**.
4. Välj **bevilja**, och välj sedan **Ja**.
    
    >[!Note]
    >Du måste vara en Global administratör för att bevilja behörigheter till appen.

Mer information finns i den [bevilja](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-prerequisites-azure-portal#grant-permissions) tänkbara kraven för att få åtkomst till Azure AD reporting API-artikel.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>Konfigurera TLS för att ansluta till Azure AD-tjänster för PCI DSS-kompatibilitet

**Typ:** Ny funktion  
**Tjänstekategori:** Gäller inte  
**Produkten kapacitet:** Plattform

Transport Layer Security (TLS) är ett protokoll som ger sekretess och integritet mellan två kommunicerande program och är den vanligaste säkerhetsprotokoll som används idag.

Den [PCI Security Standards Council](https://www.pcisecuritystandards.org/) har fastställt att tidiga versioner av TLS och Secure Sockets Layer (SSL) måste inaktiveras av aktiverar nya och säkrare protokoll med efterlevnad som börjar på **den 30 juni 2018**. Den här ändringen innebär att om du ansluter till Azure AD-tjänster och kräva PCI DSS-efterlevnad, måste du inaktivera TLS 1.0. Flera versioner av TLS finns, men TLS 1.2 är den senaste versionen som är tillgängliga för Azure Active Directory Services. Vi rekommenderar starkt att flytta direkt till TLS 1.2 för både klient/server och webbläsaren/server kombinationer.

Inaktuella webbläsare stöder inte nyare TLS-versioner, till exempel TLS 1.2. Om du vill se vilka versioner av TLS som stöds av webbläsaren, gå till den [Qualys SSL Labs](https://www.ssllabs.com/) och klicka **testa din webbläsare**. Vi rekommenderar att du uppgraderar till den senaste versionen av din webbläsare och aktivera helst endast TLS 1.2.

**Aktivera TLS 1.2 i webbläsare**

- **Microsoft Edge och Internet Explorer (båda anges med hjälp av Internet Explorer)**

    1. Öppna Internet Explorer, Välj **verktyg** > **Internetalternativ** > **Avancerat**.
    2. I den **Security** Välj **använda TLS 1.2**, och välj sedan **OK**.
    3. Stänga alla webbläsarfönster och starta om Internet Explorer. 

- **Google Chrome**

    1. Öppna Google Chrome, typ *chrome://settings/* i adressfältet och tryck på **RETUR**.
    2. Expandera den **Avancerat** alternativ, gå till den **System** område och välj **öppna proxyinställningar**.
    3. I den **Internetegenskaper** väljer den **Avancerat** fliken, gå till den **Security** Välj **använda TLS 1.2**, och välj sedan  **OK**.
    4. Stänga alla webbläsarfönster och starta om Google Chrome.

- **Mozilla Firefox**

    1. Öppna Firefox, typ *om: config* i adressfältet och tryck sedan på **RETUR**.
    2. Sök efter period, *TLS*, och välj sedan den **security.tls.version.max** posten.
    3. Ange värdet till **3** att tvinga webbläsaren att använda upp till version TLS 1.2 och välj sedan **OK**.

        >[!NOTE]
        >Firefox version 60,0 stöder TLS 1.3 så att du kan också ange security.tls.version.max-värdet **4**.

    4. Stänga alla webbläsarfönster och starta om Mozilla Firefox.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Nya federerade appar är tillgängliga i appgalleriet för Azure AD - juni 2018

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** Tredjepartsintegration
 
I juni 2018 har vi lagt till stöd för dessa 15 nya appar med Federation i app-galleriet:

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), [reglera musik](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), [SAML 1.1 Token aktiverat LOB-App](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), [Supermood](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial), [Autotask](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [ Säkerhetskopiering](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Skyhigh nätverk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), Smartway2, [TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), [Zoho en](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), [ Lokal SharePoint-](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial), [förutser CX Suite](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), [Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial), [ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

Mer information om apparna som finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial). Läs mer om att lista ditt program i Azure AD-appgalleri [lista ditt program i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Azure AD-lösenordsskydd är allmänt tillgänglig förhandsversion

**Typ:** Ny funktion  
**Tjänstekategori:** Identity Protection  
**Produkten kapacitet:** Användarautentisering

Använd Azure AD-lösenordsskydd för att eliminera enkelt att gissa lösenord från din miljö. Eliminera lösenorden hjälper till att minska risken för angrepp mot ett lösenord besprutningsmedel typen av attacker.

Mer specifikt Azure AD-lösenordsskydd hjälper dig att:

- Skydda din organisations konton i både Azure AD och Windows Server Active Directory (AD). 
- Stoppar dina användare använder lösenord på en lista med mer än 500 av de vanligaste lösenorden och över 1 miljon tecken ersättningen varianter av dessa lösenord.
- Administrera Azure AD-lösenord skydd från en enda plats i Azure AD-portalen för både Azure AD och lokala Windows Server AD.

Läs mer om Azure AD-lösenordsskydd [eliminera felaktiga lösenord i din organisation](https://aka.ms/aadpasswordprotectiondocs).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-tou-creation"></a>Ny ”alla gäster” villkorlig åtkomst Principmall när användningsvillkor (ToU) skapades

**Typ:** Ny funktion  
**Tjänstekategori:** Användningsvillkor  
**Produkten kapacitet:** Styrning

När du skapar dina användningsvillkor (ToU) skapas också en ny mall för princip för villkorlig åtkomst för ”alla gäster” och ”alla appar”. Den här nya principmallen gäller den nyligen skapade ToU effektivisera skapandet och tvingande processen för gäster.

Mer information finns i [Azure Active Directory-villkoren i Använd funktion](https://docs.microsoft.com/azure/active-directory/active-directory-tou).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-tou-creation"></a>Nya principmallen för ”anpassad” villkorlig åtkomst när användningsvillkor (ToU) skapades

**Typ:** Ny funktion  
**Tjänstekategori:** Användningsvillkor  
**Produkten kapacitet:** Styrning

När du skapar dina användningsvillkor (ToU) skapas också en ny ”anpassad” Principmall för villkorlig åtkomst. Den här nya principmallen kan du skapa användningsvillkoren och sedan omedelbart gå till principbladet skapa villkorlig åtkomst utan att behöva gå manuellt via portalen.

Mer information finns i [Azure Active Directory-villkoren i Använd funktion](https://docs.microsoft.com/azure/active-directory/active-directory-tou).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Nya och heltäckande vägledning om hur du distribuerar Azure Multi-Factor Authentication

**Typ:** Ny funktion  
**Tjänstekategori:** Annat  
**Produkten kapacitet:** Identitetssäkerhet och skydd
 
Vi har släppt nya stegvisa anvisningar om hur du distribuerar Azure Multi-Factor Authentication (MFA) i din organisation.

Om du vill visa i distributionsguiden för MFA, går du till den [identitet distributionsguider](https://aka.ms/DeploymentPlans) lagringsplatsen på GitHub. Om du vill ge feedback om distributionsguiden, använda den [distributionen planera feedbackformulär](https://aka.ms/deploymentplanfeedback). Om du har frågor om distributionsguiden kontaktar du oss på [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Azure AD delegerad apphantering roller finns i offentlig förhandsversion

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** Åtkomstkontroll

Administratörer kan nu delegera hanteringsuppgifter utan att tilldela rollen som Global administratör. De nya roller och funktioner är:

- **Nya standarden administratörsroller i Azure AD:**

    - **Programadministratör.** Ger möjlighet att hantera alla aspekter av alla appar, inklusive registrering, inställningar för enkel inloggning, tilldelning av appar och licensiering, App proxy-inställningar och godkännande (förutom till Azure AD-resurser).

    - **Molnprogramadministratör.** Beviljar alla programadministratör funktioner, förutom App proxy eftersom det inte ger åtkomst till lokala.

    - **Programutvecklare.** Ger möjlighet att skapa appregistreringar, även om den **Tillåt användare att registrera appar** alternativet är inaktiverat.

- **Ägarskap (Konfigurera per app-registrering och per företag-appen, liknar grupp ägarskapet processen:**
 
    - **Appregistrering ägare.** Ger möjlighet att hantera alla aspekter av ägda appregistrering, inklusive appmanifestet och lägga till ytterligare ägare.

    - **Enterprise Appägaren.** Ger dig möjlighet att hantera många aspekter av ägda enterprise-appar, inklusive inställningar för enkel inloggning, tilldelning av appar och godkännande (förutom till Azure AD-resurser).

Läs mer om offentlig förhandsversion, den [Azure AD delegerad programhantering roller finns i offentlig förhandsversion!](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) blogg. Mer information om roller och behörigheter finns i [Tilldela administratörsroller i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

---

## <a name="may-2018"></a>Maj 2018

### <a name="expressroute-support-changes"></a>Ändringar av ExpressRoute-stöd

**Typ:** Förändringsplan  
**Tjänstekategori:** Autentiseringar (inloggningar)  
**Produkten kapacitet:** Plattform  

Programvara som tjänst, som Azure Active Directory (Azure AD) är utformade att fungera bäst genom att gå direkt via Internet, utan att kräva ExpressRoute eller några andra privata VPN-tunnlar. Därför på **1 augusti 2018**, vi upphör support för ExpressRoute för Azure AD-tjänster med hjälp av Azures offentliga peering och Azure webbgrupper i Microsoft-peering. Alla tjänster som påverkas av den här ändringen märker Azure AD-trafik gradvis man växlar från ExpressRoute till Internet.

Även om vi ändrar vår support vi också veta det är fortfarande situationer där du kan behöva använda en dedikerad uppsättning kretsar för din autentiseringstrafik. På grund av detta fortsätter Azure AD att stödja per klient IP intervallbegränsningar med ExpressRoute och tjänster redan på Microsoft-peering ”andra Office 365 Online-tjänster”-communityn. Om dina tjänster som påverkas, men du behöver ExpressRoute, måste du göra följande:

- **Om du använder Azures offentliga peering.** Flytta till Microsoft-peering och registrera dig för den **andra Office 365 Online-tjänster (12076:5100)** community. Mer information om hur du flyttar från Azure offentlig peering till Microsoft-peering finns i den [flytta en offentlig peering till Microsoft-peering](https://docs.microsoft.com/azure/expressroute/how-to-move-peering) artikeln.

- **Om du använder Microsoft-peering.** Registrera dig för den **andra Office 365 Online-tjänsten (12076:5100)** community. Mer information om krav för routning finns i den [stöd för BGP-communities avsnittet](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) av ExpressRoute-routning krav-artikeln.

Om du måste fortsätta att använda dedikerade kretsar, du behöver prata med ditt Microsoft Account team om hur du får tillstånd att använda den **andra Office 365 Online-tjänsten (12076:5100)** community. MS Office-hanterade-granskningsnämnd verifierar om du behöver dessa kretsar och kontrollera att du förstår de tekniska effekterna av att hålla dem. Obehörig prenumerationer som försöker skapa flödesfilter för Office 365 får ett felmeddelande. 
 
---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Microsoft Graph API: er för administrativa scenarier för Användningsvillkoren

**Typ:** Ny funktion  
**Tjänstekategori:** Användningsvillkor  
**Produkten kapacitet:** Developer-upplevelse
 
Vi har lagt till Microsoft Graph API: er för drift för administration av Azure AD användningsvillkor. Du kan skapa, uppdatera och ta bort användningsvillkor med objektet.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Lägg till slutpunkt för flera innehavare av Azure AD som en identitetsprovider i Azure AD B2C

**Typ:** Ny funktion  
**Tjänstekategori:** B2C – konsumentidentitetshantering  
**Produkten kapacitet:** B2B/B2C
 
Anpassade principer kan, du nu använda vanliga Azure AD-slutpunkten som en identitetsprovider i Azure AD B2C. På så sätt kan du ha en enda ingångspunkt för alla Azure AD-användare som loggar in i dina program. Mer information finns i [Azure Active Directory B2C: Tillåt användare att logga in på en identitetsprovider för flera innehavare Azure AD med hjälp av anpassade principer](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom).

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Använda interna URL: er för att komma åt appar från var som helst med vår Mina appar Inloggningstillägg och Azure AD-programproxyn

**Typ:** Ny funktion  
**Tjänstekategori:** Mina appar  
**Produkten kapacitet:** Enkel inloggning
 
Användare kan nu komma åt program via interna URL: er även när utanför företagets nätverk med hjälp av den Mina appar skyddat Inloggningstillägg för Azure AD. Detta fungerar med alla program som du har publicerat med hjälp av Azure AD Application Proxy i valfri webbläsare som också har åtkomstpanelen webbläsartillägget installerad. URL-omdirigering funktionerna aktiveras automatiskt när en användare loggar in på tillägget. Tillägget är tillgänglig för hämtning på [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367), och [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---
 
### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory - Data i Europa för Europa kunder

**Typ:** Ny funktion  
**Tjänstekategori:** Annat  
**Produkten kapacitet:** GoLocal

Kunder i Europa kräver att deras data stannar i Europa och replikeras inte utanför Europeiska Datacenter möte sekretess-och Europeiska lagar. Detta [artikeln](https://go.microsoft.com/fwlink/?linkid=872328) innehåller specifik information om vilka identitetsinformation lagras i Europa och tillhandahåller även information om information som kommer att lagras utanför Europeiska datacenter. 

---
 
### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Nya användaretablering för SaaS-app integreringar - maj 2018

**Typ:** Ny funktion  
**Tjänstekategori:** App-etablering  
**Produkten kapacitet:** Tredjepartsintegration
 
Azure AD kan du automatisera den genereringen, underhållet och borttagningen av användaridentiteter i SaaS-program, till exempel Dropbox, Salesforce, ServiceNow med mera. Vi har lagt till stöd för följande program i Azure AD-appgalleriet för användaretablering för maj 2018:

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [Hörnstenen OnDemand](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

En lista över alla program som stöder etableringen av användare i Azure AD-galleriet finns i [ https://aka.ms/appstutorial ](https://aka.ms/appstutorial).

---
 
### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>Azure AD-åtkomstgranskningar av grupper och åtkomst till appen nu ger återkommande granskningar

**Typ:** Ny funktion  
**Tjänstekategori:** Åtkomstgranskningar  
**Produkten kapacitet:** Styrning
 
Åtkomstgranskning av grupper och appar är nu allmänt tillgänglig som en del av Azure AD Premium P2.  Administratörer kommer att kunna konfigurera åtkomstgranskningar av gruppmedlemskap och programtilldelningar att återkomma automatiskt med jämna mellanrum, t.ex.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Azure AD-aktivitetsloggar (inloggningar och granskning) är nu tillgängliga via MS Graph

**Typ:** Ny funktion  
**Tjänstekategori:** Rapportering  
**Produkten kapacitet:** Övervakning och rapportering
 
Azure AD aktivitetsloggar, vilket innefattar inloggningar och granskningsloggar, är nu tillgängliga via MS Graph. Vi har visas två slutpunkter via MS Graph för att få åtkomst till dessa loggar. Kolla in våra [dokument](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) för programmässig åtkomst till Azure AD Reporting API: er att komma igång. 

---
 
### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Förbättringar av B2B-inlösen upplevelse och lämna en organisation

**Typ:** Ny funktion  
**Tjänstekategori:** B2B  
**Produkten kapacitet:** B2B/B2C

**Just-in-time-inlösen:** När du delar en resurs med en gästanvändare med B2B-API – du behöver inte skicka ett e-postmeddelande med särskild inbjudan. I de flesta fall gästanvändaren kan komma åt resursen och kommer att tas via inlösen upplevelse precis i tid. Ingen mer inverkan på grund av missade e-postmeddelanden. Inga fler ber din gästanvändare ”gjorde du klickar på länken inlösen systemet skickat dig”?. Det innebär att när SPO använder inbjudan manager – grumligt bifogade filer kan ha samma canonical URL för alla användare – interna och externa – i alla tillstånd för inlösen.

**Moderna inlösen upplevelse:** Inga fler delas upp på skärmen inlösen landningssida. Användarna ser en modern godkänna erfarenhet i organisationen som bjuder in sekretesspolicy, precis som de gör för appar från tredje part.

**Gästanvändare kan lämna organisationen:** När en användares relation med en organisation är över, kan de egen hantering lämnar organisationen. Inga fler anrop bjuder in organisationens administratör ”tas bort”, inga fler generering supportärenden.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Nya federerade appar är tillgängliga i appgalleriet för Azure AD - maj 2018

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** Tredjepartsintegration
 
I maj 2018 har vi lagt till dessa 18 nya appar med stöd till vår app-galleriet:

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), Infogix Data3Sixty styr [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [Jamf Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [Envi MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [skapandeprocessen Prime](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [Montage Online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial),[まなびポケット](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial), OpenReel, [båge publicering - enkel inloggning ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial), [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [Riskware](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [besättningen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial), [Reviewsnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

Mer information om apparna som finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial).

Läs mer om att lista ditt program i Azure AD-appgalleri [lista ditt program i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Ny stegvisa distributionsguider för Azure Active Directory

**Typ:** Ny funktion  
**Tjänstekategori:** Annat  
**Produkten kapacitet:** Katalog
 
Nya, stegvisa anvisningar om hur du distribuerar Azure Active Directory (Azure AD), inklusive lösenordsåterställning via självbetjäning (SSPR), enkel inloggning (SSO), villkorlig åtkomst (CA), App proxy, Användaretablering, Active Directory Federation Services (ADFS) till Direktautentisering (PTA) och ADFS till lösenordets hash-synkronisering (PHS).

Om du vill visa distributionsguiden, går du till den [identitet distributionsguider](https://aka.ms/DeploymentPlans) lagringsplatsen på GitHub. Om du vill ge feedback om distributionsguiden, använda den [distributionen planera feedbackformulär](https://aka.ms/deploymentplanfeedback). Om du har frågor om distributionsguiden kontaktar du oss på [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Företagsprogram Sök – Läs in fler appar

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** Enkel inloggning
 
Svårt att hitta dina program / tjänsthuvudnamn? Vi har lagt till möjligheten att läsa in fler program i enterprise-program listan med alla program. Som standard visar vi 20 program. Du kan nu klickar på **Läs in fler** att visa fler program. 

---
 
### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>Maj versionen av AADConnect innehåller en offentlig förhandsversion av integrering med PingFederate, viktiga säkerhetsuppdateringar, många felkorrigeringar och nya bra nya verktyg för felsökning. 

**Typ:** Ändrad funktion  
**Tjänstekategori:** AD Connect  
**Produkten kapacitet:** Livscykelhantering för identiteter
 
Maj versionen av AADConnect innehåller en offentlig förhandsversion av integrering med PingFederate, viktiga säkerhetsuppdateringar, många felkorrigeringar och nya bra nya verktyg för felsökning. Du hittar viktig [här](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190).

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Azure AD-åtkomstgranskningar: Använd automatiskt

**Typ:** Ändrad funktion  
**Tjänstekategori:** Åtkomstgranskningar  
**Produkten kapacitet:** Styrning

Åtkomstgranskningar av grupper och appar är nu allmänt tillgängliga som en del av Azure AD Premium P2. En administratör kan konfigurera för att automatiskt tillämpa granskarens ändringar till den grupp eller en app som åtkomstgranskningen är klar. Administratören kan också ange vad händer med användarens fortsatt åtkomst om granskarna inte svarar, ta bort åtkomst, blockera åtkomst eller ta system rekommendationer. 

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-responsemode-for-new-apps"></a>ID-token kan inte längre returneras med frågan response_mode för nya appar. 

**Typ:** Ändrad funktion  
**Tjänstekategori:** Autentiseringar (inloggningar)  
**Produkten kapacitet:** Användarautentisering
 
Appar som har skapats på eller efter den 25 April 2018 kommer inte längre att kunna begära en **id_token** med hjälp av den **fråga** response_mode.  Detta öppnar Azure AD-nivå med OIDC-specifikationer och hjälper till att minska din attackyta för appar.  Appar som har skapats innan den 25 April 2018 inte är blockerad från att använda den **fråga** response_mode med en response_type av **id_token**.  Felet som returnerades när du begär en id_token från AAD, är **AADSTS70007: ”query” är inte ett värde som stöds av 'response_mode ”när du begär en token**.

Den **fragment** och **form_post** response_modes fortsätter att fungera - när du skapar nya programobjekt (till exempel för App Proxy-användning), se till att använda något av dessa response_modes innan de skapar en nytt program.  

---
 
## <a name="april-2018"></a>April 2018 

### <a name="azure-ad-b2c-access-token-are-ga"></a>Azure AD B2C-åtkomsttoken är GA

**Typ:** Ny funktion  
**Tjänstekategori:** B2C – konsumentidentitetshantering  
**Produkten kapacitet:** B2B/B2C 

Du kan nu komma åt webb-API: er skyddas av Azure AD B2C med åtkomsttoken. Funktionen flyttas från allmänt tillgänglig förhandsversion till GA. Användargränssnitt för att konfigurera Azure AD B2C-program och webb-API: er har förbättrats och andra mindre förbättringar har gjorts.
 
Mer information finns i [Azure AD B2C: Begär åtkomsttoken](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens).

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Testa enkel inloggningkonfigurationen för SAML-baserade program

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** Enkel inloggning

När du konfigurerar SAML-baserad enkel inloggning program, kan du testa integrationen på konfigurationssidan. Om det uppstår ett fel när de loggar in kan du ange felet i belastningstester experience och Azure AD tillhandahåller Lösningssteg att lösa ditt problem.

Mer information finns i:

- [Konfigurera enkel inloggning för program som inte ingår i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [Så här felsöker du SAML-baserad enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

---
 
### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Azure AD användningsvillkor har nu rapportering per användare

**Typ:** Ny funktion  
**Tjänstekategori:** Användningsvillkor  
**Produkten kapacitet:** Efterlevnad
 
Administratörer kan nu välja ett visst användningsvillkor och se alla användare som har samtyckt till att användningsvillkoren och datum/tid då det skedde.

Mer information finns i den [funktionen Azure AD-användningsvillkor](https://docs.microsoft.com/azure/active-directory/active-directory-tou).

---
 
### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: Riskfyllda IP-adresser för AD FS extranät-kontoutelåsningsskydd 

**Typ:** Ny funktion  
**Tjänstekategori:** Annat  
**Produkten kapacitet:** Övervakning och rapportering

Connect Health nu stöder möjligheten att identifiera IP-adresser som överskrider ett tröskelvärde för misslyckade U/P-inloggningar på grundval av per timme eller dag. Funktionerna i den här funktionen är:

- Omfattande rapport som visar IP-adress och antal misslyckade inloggningar som genereras på en per timme/daglig basis med anpassningsbart tröskelvärde.
- E-postbaserad aviseringar som visar när en specifik IP-adress har passerat tröskelvärdet för misslyckade U/P-inloggningar på varje timme/daglig basis.
- En hämtning-möjlighet att göra en detaljerad analys av data

Mer information finns i [riskfyllda IP-adresser](https://aka.ms/aadchriskyip).

---
 
### <a name="easy-app-config-with-metadata-file-or-url"></a>Enkel appkonfiguration med metadatafil eller URL

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** Enkel inloggning

På sidan för Enterprise-program kan administratörer överföra en SAML-metadatafil för att konfigurera SAML-baserad-inloggning för AAD-galleriet och ett icke-galleriet.

Du kan dessutom använda Azure AD application federation metadata-URL för att konfigurera enkel inloggning med målprogrammet.

Mer information finns i [Konfigurera enkel inloggning till program som inte ingår i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Azure AD-användningsvillkor för nu allmänt tillgänglig

**Typ:** Ny funktion  
**Tjänstekategori:** Användningsvillkor  
**Produkten kapacitet:** Efterlevnad
 

Azure AD användningsvillkor har gått från förhandsversion till allmänt tillgänglig.

Mer information finns i den [funktionen Azure AD-användningsvillkor](https://docs.microsoft.com/azure/active-directory/active-directory-tou).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Tillåt eller blockera inbjudningar till B2B-användare från specifika organisationer

**Typ:** Ny funktion  
**Tjänstekategori:** B2B  
**Produkten kapacitet:** B2B/B2C
 

Du kan nu ange vilka partnerorganisationer som du vill dela och samarbeta med i Azure AD B2B-samarbete. Om du vill göra detta måste du skapa lista över specifika tillåter eller nekar domäner. När en domän blockeras med hjälp av dessa funktioner, kan anställda inte längre skicka inbjudningar till personer i domänen.

Detta hjälper dig att styra åtkomsten till dina resurser, samtidigt som en smidig upplevelse för godkända användare.

Den här funktionen för B2B-samarbete är tillgängligt för alla Azure Active Directory-kunder och kan användas tillsammans med Azure AD Premium-funktioner som villkorlig åtkomst och identitet skydd för mer detaljerad kontroll över när och hur externa företagsanvändare loggar i och få åtkomst.

Mer information finns i [Tillåt eller blockera inbjudningar till B2B-användare från specifika organisationer](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nya federerade appar är tillgängliga i Azure AD-appgalleriet

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** Tredjepartsintegration

I April 2018 har vi lagt till dessa 13 nya appar med stöd till vår app-galleriet:

Villkoret HCM-system, [FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial), [hemlighet Server (lokalt)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial), [dynamisk Signal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial), [mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial), [Organisationsschema Nu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), [AppNeta Prestandaövervakaren](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial), [Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial) , [Fluxx Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial), [ Cisco molnet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial), hylla, [SafetyNet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)

Mer information om apparna som finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial).

Läs mer om att lista ditt program i Azure AD-appgalleri [lista ditt program i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Grant B2B-användare i Azure AD åtkomst till dina lokala program (allmänt tillgänglig förhandsversion)

**Typ:** Ny funktion  
**Tjänstekategori:** B2B  
**Produkten kapacitet:** B2B/B2C

Som en organisation som använder Azure Active Directory (Azure AD) B2B-samarbetsfunktioner för att bjuda in gästanvändare från partnerorganisationer till din Azure AD nu kan du ange dessa B2B-användare åtkomst till lokala appar. De här lokala apparna kan använda SAML-baserad autentisering eller integrerad Windows-autentisering (IWA) med Kerberos-begränsad delegering (KCD).

Mer information finns i [Grant B2B-användare i Azure AD åtkomst till dina lokala program](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises).

---
 
### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Hämta SSO-integrationsjälvstudierna från Azure Marketplace

**Typ:** Ändrad funktion  
**Tjänstekategori:** Annat  
**Produkten kapacitet:** Tredjepartsintegration

Om ett program som anges i den [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) stöder SAML-baserad enkel inloggning, klicka på **Hämta nu** ger dig integrationsvägledning som är associerade med programmet. 

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Snabbare prestanda för Azure AD automatisk användaretablering för SaaS-program

**Typ:** Ändrad funktion  
**Tjänstekategori:** App-etablering  
**Produkten kapacitet:** Tredjepartsintegration
 
Tidigare kunder som använder Azure Active Directory-anslutningsappar för SaaS-program (till exempel Salesforce, ServiceNow och Box) för användaretablering kan uppleva långsam prestanda om deras Azure AD-klienter som finns över 100 000 kombinerade användare och grupper och de använde användar- och tilldelningar för att avgöra vilka användare som ska etableras.

Betydande prestandaförbättringar har distribuerats till Azure AD-etableringstjänsten som avsevärt minska den tid som behövs för att utföra inledande synkronisering mellan Azure Active Directory och mål SaaS-program på den 2 April 2018.

Därför kan många kunder som hade inledande synkronisering till appar som tog många dagar eller aldrig har slutförts kan nu slutföra inom några minuter eller timmar.

Mer information finns i [vad som händer under etableringen?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning#what-happens-during-provisioning)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Lösenord för självbetjäning från Windows 10-låsskärmen för Azure AD-hybridanslutna datorer

**Typ:** Ändrad funktion  
**Tjänstekategori:** Lösenordsåterställning via självbetjäning  
**Produkten kapacitet:** Användarautentisering
 
Vi har uppdaterat Windows 10 SSPR-funktionen för att inkludera stöd för datorer som är hybrid Azure AD-anslutna. Den här funktionen är tillgänglig i Windows 10 RS4 kan användarna återställa sina lösenord från låsskärmen på en Windows 10-dator. Användare som är aktiverade och som har registrerats för lösenordsåterställning via självbetjäning kan använda den här funktionen.

Mer information finns i [Azure AD-lösenordsåterställning från inloggningsskärmen](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows).

---

## <a name="march-2018"></a>Mars 2018
 
### <a name="certificate-expire-notification"></a>Certifikatet upphör att gälla meddelande

**Typ:** Korrigerat  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** Enkel inloggning
 
Azure AD skickar ett meddelande när ett certifikat för ett galleri eller icke-galleriprogram är på väg att upphöra att gälla. 

Vissa användare fick inget meddelanden för företagsprogram som konfigurerats för SAML-baserad enkel inloggning. Det här problemet har lösts. Azure AD skickar meddelande för certifikat som går ut inom 7, 30 och 60 dagar. Du kan se den här händelsen i granskningsloggarna. 

Mer information finns i:

- [Hantera certifikat för federerad enkel inloggning i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Granska aktivitetsrapporter i Azure Active Directory-portalen](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
 
---
 
### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Twitter- och GitHub-Identitetsproviders i Azure AD B2C

**Typ:** Ny funktion  
**Tjänstekategori:** B2C – konsumentidentitetshantering  
**Produkten kapacitet:** B2B/B2C
 
Du kan nu lägga till Twitter eller GitHub som identitetsprovider i Azure AD B2C. Twitter flyttar från allmänt tillgänglig förhandsversion till GA. GitHub släpps i offentlig förhandsversion.

Mer information finns i [vad är Azure AD B2B-samarbete?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
 
---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Begränsa webbläsaråtkomst med Intune Managed Browser med Azure AD-programbaserad villkorsstyrd åtkomst för iOS och Android

**Typ:** Ny funktion  
**Tjänstekategori:** Villkorlig åtkomst  
**Produkten kapacitet:** Identitetssäkerhet och skydd
 
**Nu i offentlig förhandsversion!**

**Intune Managed Browser enkel inloggning:** Dina anställda kan använda enkel inloggning i interna klienter (till exempel Microsoft Outlook) och Intune Managed Browser för alla Azure AD-anslutna appar.

**Intune Managed Browser stöd för villkorlig åtkomst:** Du kan nu kräva anställda att använda Intune Managed browser med programbaserade villkorliga åtkomstprinciper.

Läs mer om detta i vår [blogginlägget](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Mer information finns i:

- [Konfigurera-programbaserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

- [Konfigurera principer för hanterade webbläsare](https://aka.ms/managedbrowser)  

---
 
### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Approxy-cmdletar i Powershell GA-modulen

**Typ:** Ny funktion  
**Tjänstekategori:** App Proxy  
**Produkten kapacitet:** Åtkomstkontroll
 
Stöd för programproxy-cmdletar är nu i Powershell GA-modulen! Detta kräver att du kan hålla dig uppdaterad på Powershell-moduler - om du blir mer än ett år efter, kan vissa cmdletar kan sluta fungera. 

Mer information finns i [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0).
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Office 365-inbyggda klienter stöds av sömlös enkel inloggning med ett icke-interaktivt protokoll

**Typ:** Ny funktion  
**Tjänstekategori:** Autentiseringar (inloggningar)  
**Produkten kapacitet:** Användarautentisering
 
Användare som använder Office 365-inbyggda klienter (version 16.0.8730.xxxx och senare) får en tyst inloggningsupplevelse med sömlös enkel inloggning. Det här stödet tillhandahålls av tillägg ett icke-interaktivt protokoll (WS-Trust) till Azure AD.

Mer information finns i [hur logga in på en intern klient med sömlös SSO arbete?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work)
 
---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Användare får en tyst inloggningsupplevelse, med sömlös enkel om ett program skickar inloggningsförfrågningar till slutpunkter för Azure AD-klient

**Typ:** Ny funktion  
**Tjänstekategori:** Autentiseringar (inloggningar)  
**Produkten kapacitet:** Användarautentisering
 
Användare får en tyst inloggningsupplevelse, med sömlös enkel om ett program (till exempel `https://contoso.sharepoint.com`) skickar inloggningsförfrågningar till slutpunkter för Azure AD-klient – det vill säga `https://login.microsoftonline.com/contoso.com/<..>` eller `https://login.microsoftonline.com/<tenant_ID>/<..>` – i stället för Azure AD: s vanliga slutpunkt (`https://login.microsoftonline.com/common/<...>`).

Mer information finns i [Azure Active Directory sömlös enkel inloggning](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 

---
 
### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Lägga till endast en Azure AD-URL, istället för två URL: er tidigare, till användares Zoninställningar för intranätet att rulla ut sömlös enkel inloggning

**Typ:** Ny funktion  
**Tjänstekategori:** Autentiseringar (inloggningar)  
**Produkten kapacitet:** Användarautentisering
 
Om du vill rulla ut sömlös enkel inloggning till dina användare, du måste lägga till endast en Azure AD-URL till användarnas intranät-Zoninställningar med hjälp av Grupprincip i Active Directory: `https://autologon.microsoftazuread-sso.com`. Tidigare var kunderna tvungna att lägga till två URL: er.

Mer information finns i [Azure Active Directory sömlös enkel inloggning](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 
 
---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nya federerade appar är tillgängliga i Azure AD-appgalleriet

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** Tredjepartsintegration

I mars 2018 har vi lagt till dessa 15 nya appar med stöd till vår app-galleriet:

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), Assistant av FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), Vtiger CRM, inwink, [amplitud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial), [Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial), [Trisotech Digital Enterprise Server](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [Qumu molnet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).
 
Mer information om apparna som finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial).

Läs mer om att lista ditt program i Azure AD-appgalleri [lista ditt program i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="pim-for-azure-resources-is-generally-available"></a>PIM för Azure-resurser är allmänt tillgänglig

**Typ:** Ny funktion  
**Tjänstekategori:** Privileged Identity Management  
**Produkten kapacitet:** Privileged Identity Management
 
Om du använder Azure AD Privileged Identity Management för katalogroller, kan du nu använda PIMS Tidsbundna åtkomst och tilldelning av funktioner för Azure-resursroller som prenumerationer, resursgrupper, virtuella datorer och andra resurser som stöds med Azure Resource Manager. Använda Multifaktorautentisering när du aktiverar roller Just-In-Time och Schemalägg aktiveringar tillsammans med godkända ändra windows. Den här versionen lägger dessutom till förbättringar som inte är tillgängliga under förhandsversionen inklusive ett uppdaterat UI, arbetsflöden för godkännande och möjligheten att utöka roller som upphör snart att gälla och förnya utgångna roller.

Mer information finns i [PIM för Azure-resurser (förhandsgranskning)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)
 
---
 
### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Lägger till valfria anspråk till din apps tokens (allmänt tillgänglig förhandsversion)

**Typ:** Ny funktion  
**Tjänstekategori:** Autentiseringar (inloggningar)  
**Produkten kapacitet:** Användarautentisering
 
Azure AD-appen kan du nu begäran anpassade eller valfria anspråk i JWTs eller SAML token.  Det här är anspråk om användaren eller klientorganisationen som inte ingår som standard i token på grund av begränsningar för storlek eller tillämplighet.  Detta är för närvarande i förhandsversion för Azure AD-appar på slutpunkterna v1.0 och v2.0.  Se dokumentationen för information på vilka anspråk som kan läggas till och hur du redigerar ditt programmanifest för att begära dem.  

Mer information finns i [valfritt anspråk i Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).
 
---
 
### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Azure AD stöder PKCE för säkrare OAuth-flöden

**Typ:** Ny funktion  
**Tjänstekategori:** Autentiseringar (inloggningar)  
**Produkten kapacitet:** Användarautentisering
 
Azure AD docs har uppdaterats för att notera stöd för PKCE vilket tillåter säkrare kommunikation under flöde för att bevilja OAuth 2.0-auktoriseringskod.  Både S256 och klartext code_challenges stöds på slutpunkterna v1.0 och v2.0. 

Mer information finns i [begär en auktoriseringskod](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code). 
 
---
 
### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-getworkers-api"></a>Stöd för etablering av alla användarattributvärden i Workday Get_Workers-API

**Typ:** Ny funktion  
**Tjänstekategori:** App-etablering  
**Produkten kapacitet:** Tredjepartsintegration
 
Den offentliga förhandsversionen av inkommande etablering från Workday till Active Directory och Azure AD nu har stöd för möjligheten att extrahera och etablera alla attributvärden som är tillgängliga i Workday Get_Workers-API. Detta lägger till stöd för hundratals ytterligare standard och anpassade attribut utöver de som levereras med den första versionen av Workday inbound provisioning connector.

Mer information finns i: [Anpassning av listan över Workday-användarattribut](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Ändrar gruppmedlemskap från dynamisk till statisk och vice versa

**Typ:** Ny funktion  
**Tjänstekategori:** Grupphantering  
**Produkten kapacitet:** Samarbete
 
Det är möjligt att ändra hur medlemskap hanteras i en grupp. Detta är användbart när du vill behålla samma gruppnamn och ID i systemet, så att alla befintliga referenser i gruppen är fortfarande giltiga. Skapa en ny grupp kräver uppdatering av dessa referenser.
Vi har uppdaterat Azure AD-administratörscentret för att stödja den här funktionen. Nu kan konvertera kunder befintliga grupper från dynamiskt medlemskap till tilldelat medlemskap och vice versa. De befintliga PowerShell-cmdletarna finns också kvar.

Mer information finns i [ändra dynamiskt medlemskap till statiskt och vice versa](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Förbättrat utloggningsbeteende med sömlös enkel inloggning

**Typ:** Ändrad funktion  
**Tjänstekategori:** Autentiseringar (inloggningar)  
**Produkten kapacitet:** Användarautentisering
 
Tidigare, även om användarna explicit loggat ut från ett program som skyddas av Azure AD, de skulle vara loggas automatiskt in igen med sömlös enkel inloggning om de försöker få åtkomst till en Azure AD-program igen inom deras corpnet från sina domänanslutna enheter. Med den här ändringen stöds utloggning.  På så sätt kan användarna kan välja samma eller en annan Azure AD-konto för att logga tillbaka in med, i stället för att automatiskt som loggat in med sömlös enkel inloggning.

Mer information finns i [Azure Active Directory sömlös enkel inloggning](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)
 
---
 
### <a name="application-proxy-connector-version-154020-released"></a>Programproxy för Anslutningsapp Version 1.5.402.0 har släppts

**Typ:** Ändrad funktion  
**Tjänstekategori:** App Proxy  
**Produkten kapacitet:** Identitetssäkerhet och skydd
 
Den här connector-version distribueras gradvis via November. Den här nya anslutningsapp-versionen innehåller följande ändringar:

- Anslutningsappen ställer nu in domän på cookies i stället underdomän nivå. Detta säkerställer att en jämnare upplevelse med enkel inloggning och undviker redundanta autentisering anvisningarna.
- Stöd för chunked-kodning begäranden
- Förbättrad connector hälsoövervakning 
- Flera felkorrigeringar och stabilitetsförbättringar

Mer information finns i [alternativ för Azure AD-programproxyn kopplingar](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors).
 
---

## <a name="february-2018"></a>Februari 2018
 
### <a name="improved-navigation-for-managing-users-and-groups"></a>Förbättrad navigering för att hantera användare och grupper

**Typ:** Förändringsplan  
**Tjänstekategori:** Kataloghantering  
**Produkten kapacitet:** Katalog

Navigeringsupplevelsen för att hantera användare och grupper har effektiviserats. Du kan nu gå från översikt över directory direkt till listan över alla användare med enklare åtkomst till listan över borttagna användare. Du kan också navigera från en directory-översikt direkt till listan över alla grupper med enklare åtkomst till hanteringsinställningar för gruppen. Och även från översiktssidan directory du kan söka efter en användare, grupp, företagsprogram eller appregistrering. 

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Tillgänglighet för inloggningar och granska rapporter i Microsoft Azure som drivs av 21Vianet (Azure Kina 21Vianet)

**Typ:** Ny funktion  
**Tjänstekategori:** Azure Stack  
**Produkten kapacitet:** Övervakning och rapportering

Azure AD Activity log rapporter är nu tillgängliga i Microsoft Azure som drivs av 21Vianet (Azure Kina 21Vianet)-instanser. Det ingår följande loggar:

- **Inloggningar aktivitetsloggar** -innehåller alla inloggningar loggar som är associerade med din klient.

- **Lösenord-granskningsloggarna för självbetjäningsåterställning** -innehåller alla SSPR-granskningsloggar.

- **Directory Management granska loggar** -innehåller alla directory datorhanteringsrelaterade granskningsloggarna som användaren management, hantering och andra.

Med dessa loggar kan du få insikter om hur din miljö. Med dessa data kan du:

- Avgör hur dina appar och tjänster används av användarna.

- Felsöka problem som hindrar användare från att uträtta sitt arbete.

Mer information om hur du använder de här rapporterna finns i [Azure Active Directory-rapportering](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Använda ”rapportläsare” rollen (icke-administratörsroll) för att visa Azure AD-aktivitetsrapporter

**Typ:** Ny funktion  
**Tjänstekategori:** Rapportering  
**Produkten kapacitet:** Övervakning och rapportering

Som en del av kunder feedback för att aktivera icke-administratörsroller ha åtkomst till Azure AD activity loggar, har vi aktiverat möjligheten för användare som ingår i rollen ”rapportläsare” för åtkomst till inloggningar och aktivitetsrapporter i Azure portal samt med hjälp av våra Graph API: er. 

Mer information hur du använder de här rapporterna finns i [Azure Active Directory-rapportering](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>EmployeeID-anspråk finns tillgängligt som användarattribut och användaridentifierare

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** Enkel inloggning
 
Du kan konfigurera **EmployeeID** som användaridentifierare och användarattribut för medlemsanvändare och B2B-gäster i SAML-baserad inloggning för program från Enterprise-programmets användargränssnitt.

Mer information finns i [anpassa anspråk som utfärdats i SAML-token för företagsprogram i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Förenklad programhantering med jokertecken i Azure AD Application Proxy

**Typ:** Ny funktion  
**Tjänstekategori:** App Proxy  
**Produkten kapacitet:** Användarautentisering
 
För att underlätta programdistribution och minskar de administrativa omkostnaderna, stöder vi nu möjligheten att publicera program med jokertecken. Om du vill publicera ett jokertecken-program, kan du följer standard publishing programflödet, men använder jokertecken i de interna och externa URL: er.

Mer information finns i [jokertecken-program i Azure Active Directory-programproxyn](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard)

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Ny cmdletar som stöder konfiguration av programproxy

**Typ:** Ny funktion  
**Tjänstekategori:** App Proxy  
**Produkten kapacitet:** Plattform

Den senaste versionen av AzureAD PowerShell-förhandsversionsmodulen innehåller nya cmdlets som låter kunder konfigurera Application Proxy-program med hjälp av PowerShell.

De nya cmdletarna är: 

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup

---
 
### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Ny cmdletar som stöder konfiguration av grupper

**Typ:** Ny funktion  
**Tjänstekategori:** App Proxy  
**Produkten kapacitet:** Plattform

Den senaste versionen av AzureAD PowerShell-modulen innehåller cmdlets för att hantera grupper i Azure AD. Dessa cmdletar som tidigare var tillgängliga i modulen AzureADPreview och läggs nu till AzureAD-modulen

Grupp-cmdletar som är nu versionen för allmän tillgänglighet är: 

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup   
- Get-AzureADMSLifecyclePolicyGroup

---
 
### <a name="a-new-release-of-azure-ad-connect-is-available"></a>En ny version av Azure AD Connect är tillgänglig

**Typ:** Ny funktion  
**Tjänstekategori:** AD Sync  
**Produkten kapacitet:** Plattform
 
Azure AD Connect är det rekommenderade verktyget för att synkronisera data mellan Azure AD och på lokala datakällor, inklusive Windows Server Active Directory och LDAP.

>[!Important]
>Den här bygger introducerar schema- och synkronisera regel ändringar. Den Azure AD Connect-synkroniseringstjänsten utlöser en fullständig Import och fullständig synkronisering steg efter en uppgradering. Information om hur du ändrar det här problemet finns i [hur du skjuta upp fullständig synkronisering efter uppgradering](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade).

Den här versionen har följande uppdateringar och ändringar:

**Åtgärdade problem**

- Åtgärda tidsinställning fönstret på bakgrundsaktiviteter för Partitionsfiltrering sidan när du växlar till nästa sida.

- Ett fel som orsakade åtkomstfel när den anpassade åtgärden ConfigDB har åtgärdats.

- En bugg att komma tillrätta med tidsgräns för sql-anslutning.

- Ett fel har åtgärdats om certifikat med SAN-jokertecken inte uppfyllde kontroll.

- Ett fel som orsakar miiserver.exe krasch under exporten för AAD-koppling har åtgärdats.

- Ett fel har åtgärdats där ett felaktigt lösenord försök inloggad DC när körs orsakade AAD connect guiden för att ändra konfigurationen

**Nya funktioner och förbättringar**
 
- Programtelemetri - administratörer kan växla den här klassen av data på/av.

- Azure AD Health-data – administratörer måste besök health-portalen för att styra inställningar för deras hälsotillstånd. När tjänsten principen har ändrats, läser agenterna och använda den.

- Lagt till enhetsåtgärder tillbakaskrivning av konfigurationen och en förloppsindikator för att initiera dialogrutan.

- Förbättrad Allmänt diagnostik HTML-rapport och en fullständig datainsamling i en ZIP-Text / HTML-rapport.

- Förbättrad tillförlitlighet för automatisk uppgradering och lagt till ytterligare telemetri för att säkerställa att du kan kontrollera hälsotillståndet för servern.

- Begränsa åtkomstbehörighet till Privilegierade konton för AD-anslutningskontot. För nya installationer guiden begränsar de behörigheter som Privilegierade konton har för MSOL-kontot när du har skapat MSOL-konto. Ändringarna påverkar express-installationer och anpassade installationer med Skapa automatiskt konto.

- Ändra installationsprogrammet för att inte kräva SA privilegiet ren installation av AADConnect.

- Nya verktyg för att Felsök synkroniseringsproblem för ett specifikt objekt. Verktyget kontrollerar för närvarande följande saker:

    - UserPrincipalName matchningsfel mellan synkroniserade användarobjektet och användarkontot i Azure AD-klient.
  
    - Om objektet är filtrerad från synkronisering på grund av filtrering av domän
  
    - Om objektet är filtrerad från synkronisering på grund av organisationsenhet (OU) filtrering

- Nya verktyg för att synkronisera den aktuella lösenords-hash som lagras i en lokal Active Directory för ett visst användarkonto. Verktyget kräver inte en lösenordsändring. 

---
 
### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Program som stöder Intune App Protection principer som lagts till med Azure AD-programbaserad villkorsstyrd åtkomst

**Typ:** Ändrad funktion  
**Tjänstekategori:** Villkorlig åtkomst  
**Produkten kapacitet:** Identitetssäkerhet och skydd

Vi har lagt till fler program som stöder-programbaserad villkorlig åtkomst. Nu kan du få åtkomst till Office 365 och andra Azure AD-anslutna molnappar med hjälp av dessa godkända klientprogram.

Följande program kommer att läggas i slutet av februari:

- Microsoft Power BI

- Startprogram för Microsoft

- Microsoft fakturering

Mer information finns i:

- [Krav för godkända appar](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD-appbaserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Användningsvillkor uppdatera för den mobila upplevelsen 

**Typ:** Ändrad funktion  
**Tjänstekategori:** Användningsvillkor  
**Produkten kapacitet:** Efterlevnad

När användningsvillkoren visas kan du nu på **har du problem med att visa? Klicka här**. Om du klickar på den här länken öppnas användningsvillkoren internt på din enhet. Oavsett teckenstorlek på dokumentet eller enhetens skärmstorlek, kan du zooma och läsa dokumentet efter behov. 

---
 
## <a name="january-2018"></a>Januari 2018
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nya federerade appar är tillgängliga i Azure AD-appgalleriet 

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** Tredjepartsintegration

I januari 2018 har följande nya appar med stöd för identitetsfederation lagts till i app-galleriet:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust sekretess hanteringsprogramvara](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk federerat Directory, och [exakthet NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

Mer information om apparna som finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial).

Läs mer om att lista ditt program i Azure AD-appgalleri [lista ditt program i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="sign-in-with-additional-risk-detected"></a>Logga in med identifierad ytterligare risk

**Typ:** Ny funktion  
**Tjänstekategori:** Identity Protection  
**Produkten kapacitet:** Identitetssäkerhet och skydd

Den information som du får en identifierad riskhändelse är kopplad till din Azure AD-prenumeration. Med Azure AD Premium P2-versionen får du den mest detaljerade informationen om alla underliggande identifieringar.

Med Azure AD Premium P1-versionen visas identifieringar som inte omfattas av din licens som riskhändelse inloggning med identifierad ytterligare risk.

Mer information finns i avsnittet om [Azure Active Directory-riskhändelser](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events).
 
---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Dölj Office 365-program från slutanvändarens åtkomstpaneler

**Typ:** Ny funktion  
**Tjänstekategori:** Mina appar  
**Produkten kapacitet:** Enkel inloggning

Du kan nu bättre hantera hur Office 365-program som visas på användarnas åtkomstpaneler via en ny Användarinställning. Det här alternativet är användbart för att minska antalet appar i åtkomstpaneler för en användare om du vill visa enbart Office-appar i Office-portalen. Inställningen finns i den **användarinställningar** och är märkta, **användare kan bara se Office 365-appar i Office 365-portalen**.

Mer information finns i [dölja ett program från användarupplevelsen i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 
### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Sömlös inloggning till appar som har aktiverats för lösenord SSO direkt från appens Webbadress 

**Typ:** Ny funktion  
**Tjänstekategori:** Mina appar  
**Produkten kapacitet:** Enkel inloggning

Webbläsartillägget för Mina appar är nu tillgängligt via ett enkelt verktyg som ger dig den Mina appar enkel inloggning om funktionen som en genväg i webbläsaren. När installationen är klar ikonen användarens waffle i webbläsaren som ger dem snabb åtkomst till appar. Användare kan nu dra nytta av:

- Möjligheten att logga in direkt på lösenord SSO-baserade appar från appens på inloggningssidan
- Starta en app som använder funktionen Snabbsökning
- Genvägar till nyligen använda appar från tillägget
- Tillägget är tillgängligt för Microsoft Edge, Chrome och Firefox.
 
Mer information finns i [Mina appar skyddat Inloggningstillägg](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction#my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Azure AD-administration upplevelse i klassiska Azure-portalen har tagits bort

**Typ:** Inaktuell   
**Tjänstekategori:** Azure AD  
**Produkten kapacitet:** Katalog

Från och med den 8 januari 2018, Azure AD-administration upplevelse i den klassiska Azure-portalen har tagits bort. Detta ägde rum i samband med den klassiska Azure-portalen själva tas ur bruk. I framtiden, bör du använda den [Azure AD administratörscenter](https://aad.portal.azure.com) för din portal-baserad administration av Azure AD.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>Webbportalen PhoneFactor har tagits bort

**Typ:** Inaktuell  
**Tjänstekategori:** Azure AD  
**Produkten kapacitet:** Katalog
 
Från och med den 8 januari 2018 har tagits bort PhoneFactor-webbportalen. Den här portalen användes för administration av MFA-servern, men dessa funktioner har flyttats till Azure-portalen på portal.azure.com. 

MFA-konfigurationen finns här: **Azure Active Directory \> MFA Server**
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Avverka Azure AD-rapporter

**Typ:** Inaktuell  
**Tjänstekategori:** Rapportering  
**Produkten kapacitet:** Livscykelhantering för identiteter  


Med den allmänna tillgängligheten för nya Azure Active Directory-Administration-konsolen och nya API: er nu tillgängliga för både aktiviteten och säkerhet, rapporten API: er under ”/ rapporter” slutpunkten har tagits bort från och med slutet av den 31 December 2017.

**Vad är tillgängliga?**

Som en del av övergången till den nya administratörskonsolen, har vi gjort 2 nya API: er tillgängliga för hämtning av Azure AD-aktivitetsloggar. Den nya uppsättningen API: er ger bättre filtrering och sortering funktioner förutom att tillhandahålla bättre gransknings- och inloggningsaktiviteter. Data som tidigare var tillgängliga via security-rapporter kan nu nås via Identity Protection riskhändelser API i Microsoft Graph.

Mer information finns i:

- [Kom igång med Azure Active Directory reporting API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Kom igång med Azure Active Directory Identity Protection och Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)

---

## <a name="december-2017"></a>December 2017

### <a name="terms-of-use-in-the-access-panel"></a>Villkor för användning i åtkomstpanelen

**Typ:** Ny funktion  
**Tjänstekategori:** Användningsvillkor  
**Produkten kapacitet:** Efterlevnad
 
Du kan nu gå till åtkomstpanelen och visa användningsvillkoren som du tidigare har accepterat.

Följ de här stegen:

1. Gå till den [MyApps-portalen](https://myapps.microsoft.com), och logga in.

2. Välj ditt namn i det övre högra hörnet och välj sedan **profil** i listan. 

3. På din **profil**väljer **granska användningsvillkoren**. 

4. Nu kan du granska användningsvillkoren accepteras. 

Mer information finns i den [Azure AD användningsvillkor Använd funktion (förhandsversion)](https://docs.microsoft.com/azure/active-directory/active-directory-tou).
 
---
 
### <a name="new-azure-ad-sign-in-experience"></a>Nya Azure AD-inloggningen

**Typ:** Ny funktion  
**Tjänstekategori:** Azure AD  
**Produkten kapacitet:** Användarautentisering
 
Azure AD och identitetssystem gränssnitt för Microsoft-konto har gjorts om så att de har ett konsekvent utseende. Dessutom inloggningssidan för Azure AD samlar in användarnamnet först, följt av autentiseringsuppgifter på en annan skärm.

Mer information finns i [den nya Azure AD-inloggningen finns nu i offentlig förhandsversion](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).
 
---
 
### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Färre inloggning anvisningarna: En ny ”Håll mig inloggad” upplevelse för Azure AD-inloggningen

**Typ:** Ny funktion  
**Tjänstekategori:** Azure AD  
**Produkten kapacitet:** Användarautentisering
 
Den **vill förbli inloggad** kryssrutan på inloggningssidan för Azure AD har ersatts med en ny fråga som visas när du autentisera. 

Om du svarar **Ja** till den här uppmaningen tjänsten innehåller en beständig uppdateringstoken. Det här beteendet är samma som när du har valt den **vill förbli inloggad** kryssrutan i den gamla upplevelsen. För federerade klienter visar meddelandet efter att du autentisera med federerade tjänsten.

Mer information finns i [färre inloggning anvisningarna: Den nya ”Håll mig inloggad” upplevelsen för Azure AD är en förhandsversion](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/). 

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Lägg till konfiguration för att kräva användningsvillkor som ska expanderas innan de accepterar

**Typ:** Ny funktion  
**Tjänstekategori:** Användningsvillkor  
**Produkten kapacitet:** Efterlevnad
 
Ett alternativ för administratörer kräver deras användarna expanderar användningsvillkoren innan de accepterar villkoren.

Välj antingen **på** eller **av** vill kräva att användarna expanderar användningsvillkoren. Den **på** inställningen kräver att användare läsa villkoren innan de accepterar dem.

Mer information finns i den [Azure AD användningsvillkor Använd funktion (förhandsversion)](https://docs.microsoft.com/azure/active-directory/active-directory-tou).
 
---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Begränsade aktivering för kvalificerade rolltilldelningar

**Typ:** Ny funktion  
**Tjänstekategori:** Privileged Identity Management  
**Produkten kapacitet:** Privileged Identity Management
 
Du kan använda begränsade aktivering för att aktivera berättigade Azure-resurs rolltilldelningar med mindre självständigt än de ursprungliga tilldelningen standardvärdena. Ett exempel är om du är tilldelade som ägare till en prenumeration i din klient. Du kan aktivera rollen ägare för upp till fem resurser som ingår i prenumerationen (som resursgrupper och virtuella datorer) med begränsade aktivering. Omfång aktiveringen kan minska risken för körning av oönskade ändringar till viktiga Azure-resurser.

Mer information finns i [vad är Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure).
 
---
 
### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Nya federerade appar i appgalleriet för Azure AD

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsprogram  
**Produkten kapacitet:** Tredjepartsintegration

I December 2017 så vi har lagt till stöd för dessa nya appar med Federation till vår app-galleriet:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager [EFI digitala butik](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [bild fungerar](https://go.microsoft.com/fwlink/?linkid=863517), [MOBI](https://go.microsoft.com/fwlink/?linkid=863521), [MobileIron Azure AD-integrering](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [SAML SSO för bambu resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [SAML SSO för Bitbucket resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, Zenegy Azure AD-integrering.

Mer information om apparna som finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial).

Läs mer om att lista ditt program i Azure AD-appgalleri [lista ditt program i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 
 
---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Godkännandearbetsflöden för Azure AD-katalogroller

**Typ:** Ändrad funktion  
**Tjänstekategori:** Privileged Identity Management  
**Produkten kapacitet:** Privileged Identity Management
 
Arbetsflöde för godkännande för Azure AD-katalogroller är allmänt tillgänglig.

Med arbetsflöden för godkännande, kan privilegierade rollen administratörer kräva berättigade roller medlemmar att begäran om rollaktivering innan de kan använda den privilegierade rollen. Flera användare och grupper kan vara delegerade godkännande ansvarsområden. Berättigade rollmedlemmar ta emot meddelanden när godkännande har slutförts och deras roll är aktiv.

---
 
### <a name="pass-through-authentication-skype-for-business-support"></a>Direktautentisering: Skype för Business support

**Typ:** Ändrad funktion  
**Tjänstekategori:** Autentiseringar (inloggningar)  
**Produkten kapacitet:** Användarautentisering

Direktautentisering nu stöder användarinloggningar till Skype för företag-klientprogram som har stöd för modern autentisering, vilket innefattar online och hybridtopologier. 

Mer information finns i [Skype för företag-topologier som stöds med modern autentisering](https://technet.microsoft.com/library/mt803262.aspx).
 
---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Uppdateringar av Azure AD Privileged Identity Management för Azure RBAC (förhandsversion)

**Typ:** Ändrad funktion  
**Tjänstekategori:** Privileged Identity Management  
**Produkten kapacitet:** Privileged Identity Management
 
Med den offentliga förhandsversion uppdateringen av Azure AD Privileged Identity Management (PIM) för rollbaserad åtkomstkontroll (RBAC) kan du nu:

* Använd Enough Administration.
* Kräv godkännande för att aktivera resource roller.
* Schemalägga en framtida aktivering av en roll som kräver godkännande för både Azure AD och Azure RBAC-roller.
 
Mer information finns i [Privileged Identity Management för Azure-resurser (förhandsgranskning)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---
 
## <a name="november-2017"></a>November 2017
 
### <a name="access-control-service-retirement"></a>Access Control service pensionering

**Typ:** Förändringsplan  
**Tjänstekategori:** Access Control service  
**Produkten kapacitet:** Access Control service 

Azure Active Directory Access Control (även kallat Access Control service) kommer att dras tillbaka i slutet av 2018. Mer information som innehåller en detaljerad schema och övergripande migreringsvägledningen ges i de närmaste veckorna. Du kan lämna kommentarer på den här sidan om du har frågor om tjänsten Access Control och en gruppmedlem kommer att besvara dem.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Begränsa webbläsaråtkomst till Intune Managed Browser 

**Typ:** Förändringsplan  
**Tjänstekategori:** Villkorlig åtkomst  
**Produkten kapacitet:** Identitetssäkerhet och skydd

Du kan begränsa webbläsaråtkomst till Office 365 och andra Azure AD-anslutna molnappar genom att använda Intune Managed Browser som en godkänd app. 

Du kan nu konfigurera följande villkor för-programbaserad villkorsstyrd åtkomst:

**Klientappar:** Webbläsare

**Vad är effekten av ändringen?**

Idag, blockeras åtkomst när du använder det här tillståndet. När förhandsgranskningen är tillgänglig, kräver all åtkomst användning av managed browser-programmet. 

Leta efter den här funktionen och mer information finns i kommande bloggar och viktig information. 

Mer information finns i [för villkorlig åtkomst i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nya godkända klientappar för Microsoft Azure Active Directory-appbaserad villkorlig åtkomst

**Typ:** Förändringsplan  
**Tjänstekategori:** Villkorlig åtkomst  
**Produkten kapacitet:** Identitetssäkerhet och skydd

Följande appar är i listan över [godkända klientappar](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

Mer information finns i:

- [Krav för godkända appar](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD-appbaserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Användningsvillkor med stöd för flera språk

**Typ:** Ny funktion    
**Tjänstekategori:** Användningsvillkor  
**Produkten kapacitet:** Efterlevnad

Administratörer kan nu skapa nya användningsvillkor som innehåller flera PDF-dokument. Du kan tagga de här PDF-dokument med ett motsvarande språk. Användarna visas PDF-filen med motsvarande språk baserat på deras preferenser. Om det finns ingen matchning, visas standardspråket.

---
 
### <a name="real-time-password-writeback-client-status"></a>Klientstatus med tillbakaskrivning av lösenord i realtid

**Typ:** Ny funktion  
**Tjänstekategori:** Återställning av lösenord för självbetjäning  
**Produkten kapacitet:** Användarautentisering

Du kan nu granska statusen för din lokala tillbakaskrivningsklient för lösenord. Det här alternativet är tillgängligt i den **lokal integration** delen av den [lösenordsåterställning](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) sidan. 

Om det finns problem med anslutningen till din lokala tillbakaskrivningsklient, visas ett felmeddelande som ger dig med:

- Information om varför du inte kan ansluta till din lokala tillbakaskrivningsklient.
- En länk till dokumentationen som hjälper dig att lösa problemet. 

Mer information finns i [lokal integration](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

---

### <a name="azure-ad-app-based-conditional-access"></a>Azure AD-appbaserad villkorlig åtkomst 
 
**Typ:** Ny funktion  
**Tjänstekategori:** Azure AD  
**Produkten kapacitet:** Identitetssäkerhet och skydd

Du kan nu begränsa åtkomst till Office 365 och andra Azure AD-anslutna molnappar till [godkända klientappar](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement) som stöder Intunes appskyddsprinciper med hjälp av [appbaserad villkorlig åtkomst i Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Intunes appskyddsprinciper används för att konfigurera och skydda företagets data på dessa klientprogram.

Genom att kombinera [appbaserad](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) med [enhetsbaserad](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) principer för villkorlig åtkomst har du flexibiliteten att skydda data för personliga och företagets enheter.

Följande villkor och kontroller är nu tillgängliga för användning med appbaserad villkorlig åtkomst:

**Villkor för plattform som stöds**

- iOS
- Android

**Klient apps villkor**

- Mobila appar och skrivbordsklienter

**Åtkomstkontroll**

- Kräv godkänd klientapp

Mer information finns i [appbaserad villkorlig åtkomst i Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access).
 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Hantera Azure AD-enheter i Azure portal

**Typ:** Ny funktion  
**Tjänstekategori:** Enhetsregistrering och hantering  
**Produkten kapacitet:** Identitetssäkerhet och skydd

Nu hittar du alla dina enheter som är anslutna till Azure AD och enhetsrelaterade aktiviteterna i ett och samma ställe. Det finns en ny administration-lösning för att hantera alla dina enhetsidentiteter och inställningar i Azure-portalen. I den här versionen kan du:

- Visa alla dina enheter som är tillgängliga för villkorlig åtkomst i Azure AD.
- Visa egenskaper, som innehåller din hybrid Azure AD-anslutna enheter.
- Hitta BitLocker-nycklar för Azure AD-anslutna enheter, hantera din enhet med Intune och mycket mer.
- Hantera Azure AD-enhet-relaterade inställningar.

Mer information finns i [hantera enheter med hjälp av Azure portal](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Stöd för macOS som en enhetsplattform för Azure AD villkorsstyrd åtkomst 

**Typ:** Ny funktion    
**Tjänstekategori:** Villkorlig åtkomst  
**Produkten kapacitet:** Identitetssäkerhet och skydd 

Du nu inkludera (eller exkludera) macOS som ett villkor för plattformen av enheten i din Azure AD-princip för villkorlig åtkomst. Med hjälp av Mac OS för att enhetsplattformar som stöds kan du:

- **Registrera och hantera macOS-enheter med hjälp av Intune.** Precis som för andra plattformar som iOS och Android finns ett företagets portalprogram är tillgängliga för macOS att enhetlig registreringar. Du kan använda den nya företagsportalappen för macOS för att registrera en enhet med Intune och registrera den med Azure AD.
- **Se till att macOS-enheter uppfylla organisationens efterlevnadsprinciper som definierats i Intune.** I Intune på Azure portal kan ställa du nu in principer för efterlevnad för macOS-enheter. 
- **Begränsa åtkomsten till program i Azure AD för att endast kompatibla macOS-enheter.** Skapa principer med villkorlig åtkomst har macOS som en separat Enhetsinställningar som plattform. Nu kan du skapa principer för villkorlig åtkomst för macOS-specifika för det aktuella programmet i Azure.

Mer information finns i:

- [Skapa en princip för enhetsefterlevnad för macOS-enheter med Intune](https://aka.ms/macoscompliancepolicy)
- [Villkorlig åtkomst i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)
 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Network Policy Server-tillägg för Azure Multi-Factor Authentication 

**Typ:** Ny funktion    
**Tjänstekategori:**  Multi-Factor Authentication  
**Produkten kapacitet:** Användarautentisering

NPS-tillägget för Azure Multi-Factor Authentication lägger till molnbaserade Multi-Factor Authentication-funktioner till din infrastruktur för autentisering med hjälp av befintliga servrar. Med Network Policy Server-tillägg du kan lägga till telefonsamtal, textmeddelande eller app telefonverifiering till ditt befintliga autentiseringsflödet. Du behöver inte installera, konfigurera och underhålla nya servrar. 

Det här tillägget har skapats för organisationer som vill skydda virtuella privata nätverksanslutningar utan att distribuera Azure Multi-Factor Authentication-servern. Nätverksprincipservern tillägget fungerar som ett kort mellan RADIUS- och molnbaserade Azure Multi-Factor Authentication för att tillhandahålla en andra faktor autentisering för federerade eller synkroniserade användare.

Mer information finns i [integrera din befintliga NPS-infrastruktur med Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension).
 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Återställ eller ta bort borttagna användare permanent

**Typ:** Ny funktion    
**Tjänstekategori:** Användarhantering  
**Produkten kapacitet:** Katalog 

I Azure AD-administrationscentret kan du nu:

- Återställa en borttagen användare. 
- Ta bort en användare permanent.

**Prova:**

1. I Azure AD-administrationscentret väljer [alla användare](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) i den **hantera** avsnittet. 

2. Från den **visa** väljer **nyligen borttagna användare**. 

3. Välj en eller flera nyligen borttagna användare och återställa dem antingen eller ta bort dem permanent.
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nya godkända klientappar för Microsoft Azure Active Directory-appbaserad villkorlig åtkomst
 
**Typ:** Ändrad funktion  
**Tjänstekategori:** Villkorlig åtkomst  
**Produkten kapacitet:** Identitetssäkerhet och skydd

Följande appar har lagts till i listan över [godkända klientappar](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- Microsoft Planner
- Azure Information Protection 

Mer information finns i:

- [Krav för godkända appar](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD-appbaserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Använd ”OR” mellan kontroller i en princip för villkorlig åtkomst 

**Typ:** Ändrad funktion    
**Tjänstekategori:** Villkorlig åtkomst  
**Produkten kapacitet:** Identitetssäkerhet och skydd
 
Nu kan du använda ”eller” (begär en av de valda kontrollerna) för villkorlig åtkomstkontroller. Du kan använda den här funktionen för att skapa principer med ”eller” mellan åtkomstkontroller. Du kan till exempel använda den här funktionen för att skapa en princip som kräver att en användare att logga in med hjälp av Multi-Factor Authentication ”eller” på en kompatibel enhet.

Mer information finns i [kontroller i Azure AD villkorsstyrd åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).
 
---

### <a name="aggregation-of-real-time-risk-events"></a>Sammanställning av i realtid riskhändelser

**Typ:** Ändrad funktion    
**Tjänstekategori:** Identitetsskydd  
**Produkten kapacitet:** Identitetssäkerhet och skydd

I Azure AD Identity Protection sammanställs nu alla i realtid riskhändelser som kommer från samma IP-adress på en viss dag för varje typ av riskhändelse. Den här ändringen begränsar mängden riskhändelser visas utan ändringar i användarsäkerhet.

Den underliggande realtidsidentifiering fungerar varje gång användaren loggar in. Om du har en säkerhetsprincip för inloggningsrisk konfigurera Multifaktorautentisering eller blockera åtkomst till utlöst den fortfarande under varje riskfyllda inloggningen.
 
---
 
## <a name="october-2017"></a>Oktober 2017

### <a name="deprecate-azure-ad-reports"></a>Avverka Azure AD-rapporter

**Typ:** Förändringsplan  
**Tjänstekategori:** Rapportering  
**Produkten kapacitet:** Livscykelhantering för identiteter  

Azure-portalen ger dig:

- En ny Azure AD-administrationskonsolen.
- Nya API: er för aktiviteten och säkerhet.
 
På grund av de nya funktionerna rapporten API: er under/Reports slutpunkten drogs tillbaka den 10 December 2017. 

---

### <a name="automatic-sign-in-field-detection"></a>Automatisk inloggningsfält identifiering

**Typ:** Korrigerat   
**Tjänstekategori:** Mina appar  
**Produkten kapacitet:** Enkel inloggning  

Azure AD stöder automatisk inloggningsfält identifiering för program som renderar en HTML-Användarfält namn och lösenord. De här stegen finns dokumenterade i [automatiskt avbilda inloggningsfält för ett program](https://docs.microsoft.com/azure/active-directory/application-config-sso-problem-configure-password-sso-non-gallery#how-to-manually-capture-sign-in-fields-for-an-application). Du hittar den här funktionen genom att lägga till en *icke-galleriet* på den **företagsprogram** sidan i den [Azure-portalen](https://aad.portal.azure.com). Du kan också konfigurera den **enkel inloggning** läge på det här nya programmet till **lösenordsbaserad enkel inloggning**, ange en URL och spara sedan sidan.
 
Den här funktionen har tillfälligt inaktiverats på grund av ett problem med tjänsten. Problemet har lösts och automatisk inloggningsfält identifieringen är tillgänglig igen.

---

### <a name="new-multi-factor-authentication-features"></a>Nya Multi-Factor Authentication-funktioner

**Typ:** Ny funktion  
**Tjänstekategori:** Multi-Factor Authentication  
**Produkten kapacitet:** Identitetssäkerhet och skydd  

Multifaktorautentisering (MFA) är en viktig del av att skydda din organisation. Om du vill göra mer anpassningsbar autentiseringsuppgifter och erfarenhet smidigare, följande funktioner har lagts till: 

- Multi-Factor utmaning resultat är direkt integrerad i Azure AD-inloggningen rapporten som ger programmatisk åtkomst till MFA-resultat.
- MFA-konfigurationen är mer kommer att integreras i Azure AD-konfigurationen upplevelse i Azure-portalen.

Är en integrerad del av konfigurationsupplevelse för core Azure AD med den här offentliga förhandsversionen kan MFA-hantering och rapportering. Nu kan du hantera MFA management portal-funktionerna i Azure AD-upplevelse.

Mer information finns i [referens för MFA rapportering i Azure-portalen](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 

---

### <a name="terms-of-use"></a>Användningsvillkor

**Typ:** Ny funktion  
**Tjänstekategori:** Användningsvillkor  
**Produkten kapacitet:** Efterlevnad  

Du kan använda Azure AD-användningsvillkor för att presentera information, till exempel relevanta ansvarsfriskrivningar för juridiska krav eller efterlevnadskrav för användare.

Du kan använda Azure AD-användningsvillkor i följande scenarier:

- Allmänna villkor för användningsvillkor för alla användare i din organisation
- Specifika användningsvillkor baserat på en användares attribut (till exempel läkare jämfört med sjuksköterskor) eller inhemska jämfört med internationella medarbetare, som görs av dynamiska grupper
- Specifika användningsvillkor för att komma åt hög inverkan företagsappar som Salesforce

Mer information finns i [Azure AD-användningsvillkor](https://docs.microsoft.com/azure/active-directory/active-directory-tou).

---

### <a name="enhancements-to-privileged-identity-management"></a>Förbättringar av Privileged Identity Management

**Typ:** Ny funktion  
**Tjänstekategori:** Privileged Identity Management  
**Produkten kapacitet:** Privileged Identity Management  

Du kan hantera, kontrollera och övervaka åtkomst till Azure-resurser (förhandsversion) i organisationen kan med Azure AD Privileged Identity Management:

- Prenumerationer
- Resursgrupper
- Virtuella datorer 

Alla resurser i Azure-portalen som använder Azure RBAC-funktioner kan dra nytta av säkerhets- och hanteringsfunktioner för programlivscykel som Azure AD Privileged Identity Management har att erbjuda.

Mer information finns i [Privileged Identity Management för Azure-resurser](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

### <a name="access-reviews"></a>Åtkomstgranskningar

**Typ:** Ny funktion  
**Tjänstekategori:** Åtkomstgranskningar  
**Produkten kapacitet:** Efterlevnad  

Organisationer kan använda åtkomstgranskningar (förhandsversion) för att effektivt hantera gruppmedlemskap och åtkomst till företagsprogram: 

- Du kan certifiera om gästanvändares åtkomst med åtkomstgranskningar av deras åtkomst till program och medlemskap i grupper. Granskare kan effektivt bestämma om så att gästerna fortsatt åtkomst baserat på de insikter som tillhandahålls av åtkomstgranskningar.
- Du kan certifiera om medarbetares åtkomst till program och gruppmedlemskap med åtkomstgranskningar.

Du kan samla in åtkomstgranskningskontroller i program som är relevanta för din organisation för att spåra granskningar för efterlevnad eller riskkänsliga program.

Mer information finns i [Azure AD-åtkomstgranskningar](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Dölj program från tredje part från Mina program och Office 365-appstartaren

**Typ:** Ny funktion  
**Tjänstekategori:** Mina appar  
**Produkten kapacitet:** Enkel inloggning  

Nu kan du hantera appar som visas på användarnas portaler via en ny **Dölj app** egenskapen. Du kan dölja appar för att i fall där paneler som visas för backend-tjänster eller dubbla paneler och oreda användarnas app startprogram. Växlingsknappen är i den **egenskaper** i appen från tredje part och är märkt med **synlig för användare?** Du kan också dölja en app programmässigt via PowerShell. 

Mer information finns i [dölja ett program från tredje part från en användares gränssnitt i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app). 


**Vad är tillgängliga?**

 Som en del av övergången till den nya administratörskonsolen, två nya API: er för att hämta Azure AD activity loggar är tillgängliga. Den nya uppsättningen API: er ger bättre filtrering och sortering funktioner förutom att tillhandahålla bättre gransknings- och inloggningsaktiviteter. Tidigare var tillgängliga via säkerhetsrapporter nu data kan nås via Identity Protection Risk händelse-API i Microsoft Graph.


## <a name="september-2017"></a>September 2017

### <a name="hotfix-for-identity-manager"></a>Snabbkorrigering för Identity Manager

**Typ:** Ändrad funktion  
**Tjänstekategori:** Identity Manager  
**Produkten kapacitet:** Identitetslivcykelhantering  

En samlad snabbkorrigering (build 4.4.1642.0) är tillgänglig från och med den 25 September 2017 för Identity Manager 2016 Service Pack 1. Det här paketet:

- Löser problem och förbättringar.
- Är en ackumulerad uppdatering som ersätter alla uppdateringar i Identity Manager 2016 Service Pack 1 upp till build 4.4.1459.0 för Identity Manager 2016. 
- Kräver att du har Identity Manager 2016 skapa 4.4.1302.0. 

Mer information finns i [snabbkorrigeringen samlad (build 4.4.1642.0) är tillgänglig för Identity Manager 2016 Service Pack 1](https://support.microsoft.com/help/4021562). 

---
