---
title: Azure Active Directory som bevis på koncept playbook byggblock | Microsoft Docs
description: Utforska och snabbt implementera scenarier för identitets- och åtkomsthantering
services: active-directory
keywords: Azure active directory, playbook, konceptbevis, PoC
documentationcenter: ''
author: dstefanMSFT
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: dstefan
ms.openlocfilehash: 056821bd26e9c6c110b23a048df4aa13bc4ab8fa
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36754680"
---
# <a name="azure-active-directory-proof-of-concept-playbook-building-blocks"></a>Azure Active Directory som bevis på koncept playbook: byggblock

## <a name="catalog-of-roles"></a>Katalog över roller

| Roll | Beskrivning | Bevis på koncept (PoC) ansvar |
| --- | --- | --- |
| **Identity-arkitektur / Utvecklingsteamet** | Teamet är vanligtvis den som designerna lösningen, implementerar prototyper, godkännanden-enheter och slutligen lämnar till åtgärder | De ger miljöerna och som utvärderar olika scenarier ur hanterbarhet |
| **Lokal identitet driftteamet** | Hanterar annan identitet källor lokal: Active Directory-skogar, LDAP-kataloger, HR-system och Federation identitetsleverantörer. | Ge åtkomst till lokala resurser som krävs för PoC-scenarier.<br/>De bör vara inblandade så lite som möjligt|
| **Tekniska ägare** | Tekniska ägare av olika molnappar och tjänster som ska integreras med Azure AD | Innehåller detaljerad information om SaaS-program (potentiellt instanser för att testa) |
| **Global administratör för Azure AD** | Hanterar Azure AD-konfiguration | Ange autentiseringsuppgifter för att konfigurera synkroniseringstjänsten. Vanligtvis samma team som identitet arkitektur under PoC men avgränsa under fasen åtgärder|
| **Databas-teamet** | Ägarna av databasinfrastruktur | Ge åtkomst till SQL-miljö (AD FS eller Azure AD Connect) för specifik situation förberedelser.<br/>De bör vara inblandade så lite som möjligt |
| **Nätverk-teamet** | Ägarna av nätverkets infrastruktur | Ange nödvändiga åtkomsten på nätverksnivån för synkroniseringsservrar att korrekt åtkomst till datakällor och molntjänster (brandväggsregler, öppnade portar, IPSec-regler osv.) |
| **Säkerhetsteamet** | Definierar säkerhetsstrategi, analyserar säkerhetsrapporter från olika källor och följer på resultat. | Ange mål säkerhet utvärdering scenarier |

## <a name="common-prerequisites-for-all-building-blocks"></a>Gemensamma krav för alla byggblock

Följande är några förutsättningar för alla POC med Azure AD Premium.

| Förhandskrav | Resurser |
| --- | --- |
| Azure AD-klient som definierats med en giltig Azure-prenumeration | [Skaffa en Azure Active Directory-klient](active-directory-howto-tenant.md)<br/>**Obs:** om du redan har en miljö med Azure AD Premium-licenser kan du skaffa en noll cap-prenumeration genom att gå till https://aka.ms/accessaad <br/>Mer information finns i: https://blogs.technet.microsoft.com/enterprisemobility/2016/02/26/azure-ad-mailbag-azure-subscriptions-and-azure-ad-2/ och https://technet.microsoft.com/library/dn832618.aspx |
| Domäner som har definierats och verifierats | [Lägga till ett anpassat domännamn i Azure Active Directory](active-directory-domains-add-azure-portal.md)<br/>**Obs:** vissa arbetsbelastningar som till exempel Power BI kunde har etablerat en azure AD-klient under försättsbladen. Om du vill kontrollera om en viss domän är kopplad till en klient, navigera till https://login.microsoftonline.com/{domain}/v2.0/.well-known/openid-configuration. Om du får ett lyckat svar och domänen är redan tilldelad till en klient och tar över kan behövas. I så fall, kan du kontakta Microsoft för ytterligare. Mer information om de gäller alternativ på: [självbetjäningsregistrering för Azure?](active-directory-self-service-signup.md) |
| Azure AD Premium eller EMS utvärderingsversion aktiverad | [Azure Active Directory Premium gratis för en månad](https://azure.microsoft.com/trial/get-started-active-directory/) |
| Du har tilldelat Azure AD Premium eller licenser för EMS PoC användare | [Licens dig och dina användare i Azure Active Directory](active-directory-licensing-get-started-azure-portal.md) |
| Autentiseringsuppgifter för Azure AD Global administratör | [Tilldela administratörsroller i Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md) |
| Valfritt men rekommenderas starkt: parallella laboratoriemiljö som reserv | [Förutsättningar för Azure AD Connect](./connect/active-directory-aadconnect-prerequisites.md) |

## <a name="directory-synchronization---password-hash-sync-phs---new-installation"></a>Directory synkronisering - Lösenordshashsynkronisering (PHS) - ny-Installation

Ungefärlig tid till slutförd: en timme för mindre än 1 000 PoC-användare

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| Server för att köra Azure AD Connect | [Förutsättningar för Azure AD Connect](./connect/active-directory-aadconnect-prerequisites.md) |
| Rikta POC användare i samma domän och en del av en säkerhetsgrupp och Organisationsenhet | [Anpassad installation av Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) |
| Azure AD Connect funktioner som behövs för Konceptbeviset identifieras | [Ansluta Active Directory med Azure Active Directory - Konfigurera synkronisering funktioner](./connect/active-directory-aadconnect.md#configure-sync-features) |
| Du har krävs autentiseringsuppgifter för lokala och molnet miljöer  | [Azure AD Connect: Konton och behörigheter](./connect/active-directory-aadconnect-accounts-permissions.md) |

### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Hämta den senaste versionen av Azure AD Connect | [Ladda ned Microsoft Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594) |
| Installera Azure AD Connect med den enklaste sökvägen: Express <br/>1. Filtrera till målet Organisationsenhet för att minimera tid som synkronisering<br/>2. Välj Målet uppsättning användare i den lokala gruppen.<br/>3. Distribuera funktioner som krävs av andra POC-teman | [Azure AD Connect: Anpassad installation: domän och Organisationsenhet filtrering](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) <br/>[Azure AD Connect: Anpassad installation: grupp baserad filtrering](./connect/active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups)<br/>[Azure AD Connect: Integrera dina lokala identiteter med Azure Active Directory: Konfigurera synkroniseringsfunktioner](./connect/active-directory-aadconnect.md#configure-sync-features) |
| Öppna Azure AD Connect Användargränssnittet och se den körs profiler slutförda (Import, synkronisering och export) | [Azure AD Connect sync: Scheduler](./connect/active-directory-aadconnectsync-feature-scheduler.md) |
| Öppna den [hanteringsportalen för Azure AD](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/), gå till bladet ”alla användare”, Lägg till ”av” källkolumnen och se användarna visas markerad korrekt som kommer från ”Windows Server AD” | [Hanteringsportalen för Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) |

### <a name="considerations"></a>Överväganden

1. Titta på säkerhetsaspekter för lösenordshashsynkronisering [här](./connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md).  Om lösenordets hash-synkronisering för pilotprojekt produktionsanvändare definitivt inte är ett alternativ, bör du följande alternativ:
   * Skapa testanvändare i produktionsdomänen. Kontrollera att du inte synkroniserar något annat konto
   * Flytta till en UAT-miljö
2.  Om du vill fortsätta federation kan det vara värt att förstå kostnader associerade en federerad lösning med lokala identitetsprovider utöver Konceptbeviset och mått som mot de fördelar som du söker efter:
    * Den är i kritiskt sökvägen så att du behöver utforma för hög tillgänglighet
    * Det är en lokal tjänst måste du planera för kapacitet
    * Det är en lokal tjänst som du behöver övervaka/Underhåll/korrigering

Läs mer: [identitet förstå Office 365 och Azure Active Directory - federerad identitet](https://support.office.com/article/Understanding-Office-365-identity-and-Azure-Active-Directory-06a189e7-5ec6-4af2-94bf-a22ea225a7a9#bk_federated)

## <a name="branding"></a>Anpassning

Ungefärlig tid till slutförd: 15 minuter

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| Tillgångar (bilder, logotyper, etc.); För bästa visualiseringen kontrollerar du att ha tillgångarna rekommenderade storlekar. | [Lägga till företagsanpassning till inloggningssidan i Azure Active Directory](active-directory-branding-custom-signon-azure-portal.md) |
| Valfritt: Om miljön har AD FS-server, åtkomst till servern för att anpassa webbtema | [AD FS-inloggning användaranpassning](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/ad-fs-user-sign-in-customization) |
| Klientdatorn att utföra slutanvändarens upplevelse för inloggning |  |
| Valfritt: Mobila enheter för att verifiera upplevelse |  |

### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Gå till Azure AD Management-portalen | [Hanteringsportalen för Azure AD - företagsanpassning](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/LoginTenantBranding) |
| Överför tillgångar för inloggningssidan (hjälte logotyp, liten logotyp, etiketter, etc.). Du kan också om du har AD FS kan justera samma tillgångar med AD FS-inloggningssidor | [Lägga till företagsanpassning till inloggnings-- och åtkomstpanel sidor: anpassningsbara element](fundamentals/customize-branding.md) |
| Vänta några minuter för att ändringarna ska börja gälla helt |  |
| Logga in med autentiseringsuppgifterna för användaren POC att https://myapps.microsoft.com |  |
| Bekräfta utseendet och känslan i webbläsare | [Lägga till företagsanpassning till inloggnings-- och åtkomstpanel sidor](fundamentals/customize-branding.md) |
| Du kan också bekräfta utseendet och känslan i andra enheter |  |

### <a name="considerations"></a>Överväganden

Om gamla utseendet och känslan fortfarande efter anpassningen Rensa klientcachen webbläsaren och försök igen.

## <a name="group-based-licensing"></a>Grupp baserad licensiering

Ungefärlig tid till slutförd: 10 minuter

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| Alla POC användare är en del av en säkerhetsgrupp (molnet eller lokalt) | [Skapa en grupp och lägga till medlemmar i Azure Active Directory](fundamentals/active-directory-groups-create-azure-portal.md) |

### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Gå till bladet med licenser i hanteringsportalen för Azure AD | [Azure AD-hanteringsportalen: licensiering](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) |
| Tilldela licenser till gruppen med POC användare. | [Tilldela licenser till en grupp användare i Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md) |

### <a name="considerations"></a>Överväganden

Vid eventuella problem går du till [scenarier, begränsningar och kända problem med att använda grupper för att hantera licensiering i Azure Active Directory](active-directory-licensing-group-advanced.md)

## <a name="saas-federated-sso-configuration"></a>Konfiguration för SaaS-federerad enkel inloggning

Ungefärlig tid till slutförd: 60 minuter

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| Testmiljö för SaaS-program tillgängliga. I den här guiden använder vi ServiceNow som exempel.<br/>Vi rekommenderar starkt för att använda en test-instans för att minimera friktion om navigering befintliga kvalitet och mappningar. | Gå till https://developer.servicenow.com/app.do#! / hemma att starta processen för att få en test-instans |
| Administratörsåtkomst till ServiceNow-hanteringskonsolen | [Självstudier: Azure Active Directory-integrering med ServiceNow](saas-apps/servicenow-tutorial.md) |
| Mål uppsättning användare att tilldela program till. En säkerhetsgrupp som innehåller användarna PoC rekommenderas. <br/>Om det inte är möjligt att skapa gruppen, tilldela användare till direkt till programmet för konceptbeviset | [Tilldela en användare eller grupp till en enterprise-app i Azure Active Directory](manage-apps/assign-user-or-group-access-portal.md) |

### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Dela guiden för att alla aktörer från Microsoft Documentation  | [Självstudier: Azure Active Directory-integrering med ServiceNow](saas-apps/servicenow-tutorial.md) |
| Ange ett fungerande möte gör självstudiekurs med varje. | [Självstudier: Azure Active Directory-integrering med ServiceNow](saas-apps/servicenow-tutorial.md) |
| Koppla appen till den grupp som definieras i förutsättningarna. Om Konceptbeviset har villkorlig åtkomst i omfånget, kan du besöker som senare och lägga till MFA och liknande. <br/>Observera att detta startar i etableringsprocessen (om konfigurerad) |  [Tilldela en användare eller grupp till en enterprise-app i Azure Active Directory](manage-apps/assign-user-or-group-access-portal.md) <br/>[Skapa en grupp och lägga till medlemmar i Azure Active Directory](fundamentals/active-directory-groups-create-azure-portal.md) |
| Använda Azure AD Portal för att lägga till ServiceNow program från galleriet| [Azure AD management Portal: företagsprogram](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/Overview) <br/>[Vad är nytt i Enterprise programhantering i Azure Active Directory](active-directory-enterprise-apps-whats-new-azure-portal.md) |
| Aktivera ”SAML-baserade inloggning” i ”enkel inloggning” bladet ServiceNow-appen |  |
| Fyll i ”logga URL” och ”ID”-fält med ServiceNow-URL<br/>Markera kryssrutan för ”aktivera nytt certifikat”<br/>och spara inställningar |  |
| Öppna bladet ”konfigurera ServiceNow” längst ned på panelen om du vill visa anpassade anvisningar för hur du konfigurerar ServiceNow |  |
| Följ instruktionerna för att konfigurera ServiceNow |  |
| Bladet för ServiceNow-App i ”etablering” aktivera ”automatisk” etablering | [Hantera användarkonto etablering för företagsappar i den nya Azure-portalen](manage-apps/configure-automatic-user-provisioning-portal.md) |
| Vänta några minuter medan etablering är slutförd.  Under tiden kan du kontrollera etablering rapporter |  |
| Logga in på https://myapps.microsoft.com/ som en användare som har åtkomst | [Vad är åtkomstpanelen?](active-directory-saas-access-panel-introduction.md) |
| Klicka på ikonen för programmet som nyss skapades. Bekräfta åtkomst |  |
| Du kan också kan du kontrollera användningsrapporter program. Observera att det finns vissa svarstid, så du måste vänta ett tag för att se trafik i rapporterna. | [Inloggningsaktivitet rapporterna i Azure Active Directory-portalen: användning av hanterade program](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Kvarhållningsprinciper för rapporter i Azure Active Directory](active-directory-reporting-retention.md) |

### <a name="considerations"></a>Överväganden

1. Ovan [kursen](saas-apps/servicenow-tutorial.md) refererar till gamla Azure AD-hanteringen. Men PoC baseras på [Quickstart](active-directory-enterprise-apps-whats-new-azure-portal.md#quickstart-get-going-with-your-new-application-right-away) upplevelse.
2. Om det inte finns i galleriet målprogrammet, kan du använda ”ta med din egen app”. Läs mer: [vad är nytt i Enterprise programhantering i Azure Active Directory: lägga till anpassade program från en plats](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

## <a name="saas-password-sso-configuration"></a>SaaS-lösenord SSO-konfiguration

Ungefärlig tid till slutförd: 15 minuter

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| Testmiljö för SaaS-program. Ett exempel på lösenord SSO är HipChat och Twitter. För alla andra program måste exakt URL till sidan med inloggningen html-formulär. | [Twitter på Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/aad.twitter)<br/>[HipChat på Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/aad.hipchat) |
| Testa konton för programmen. | [Registrera dig för Twitter](https://twitter.com/signup?lang=en)<br/>[Registrera dig för gratis: HipChat](https://www.hipchat.com/sign_up) |
| Mål uppsättning användare att tilldela program till. En säkerhetsgrupp som innehåller användarna rekommenderas. | [Tilldela en användare eller grupp till en enterprise-app i Azure Active Directory](manage-apps/assign-user-or-group-access-portal.md) |
| Lokal administratörsåtkomst till en dator för att distribuera Access panelen-tillägg för Internet Explorer, Chrome eller Firefox | [Tillägget för åtkomst-panelen för Internet Explorer](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Tillägget för åtkomst-panelen för Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Tillägget för åtkomst-panelen för Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |

### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Installera webbläsartillägg för | [Tillägget för åtkomst-panelen för Internet Explorer](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Tillägget för åtkomst-panelen för Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Tillägget för åtkomst-panelen för Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |
| Konfigurera program från galleriet | [Vad är nytt i Enterprise programhantering i Azure Active Directory: nya och förbättrade programgalleriet](active-directory-enterprise-apps-whats-new-azure-portal.md#improvements-to-the-azure-active-directory-application-gallery) |
| Konfigurera enkel inloggning | [Hantera enkel inloggning för företagsappar i den nya Azure-portalen: lösenordsbaserade inloggning](manage-apps/configure-single-sign-on-portal.md#password-based-sign-on) |
| Tilldela den grupp som definieras i förutsättningarna appen | [Tilldela en användare eller grupp till en enterprise-app i Azure Active Directory](manage-apps/assign-user-or-group-access-portal.md) |
| Logga in på https://myapps.microsoft.com/ som en användare som har åtkomst |  |
| Klicka på ikonen för programmet som nyss skapades. | [Vad är åtkomstpanelen?: lösenordsbaserade SSO utan identitet etablering](active-directory-saas-access-panel-introduction.md#password-based-sso-without-identity-provisioning) |
| Ange autentiseringsuppgifter för programmet | [Vad är åtkomstpanelen?: lösenordsbaserade SSO utan identitet etablering](active-directory-saas-access-panel-introduction.md#password-based-sso-without-identity-provisioning) |
| Stäng webbläsaren och upprepa inloggningen. Den här gången runt bör användaren se sömlös åtkomst till programmet. |  |
| Du kan också kan du kontrollera användningsrapporter program. Observera att det finns vissa svarstid, så du måste vänta ett tag för att se trafik i rapporterna. | [Inloggningsaktivitet rapporterna i Azure Active Directory-portalen: användning av hanterade program](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Kvarhållningsprinciper för rapporter i Azure Active Directory](active-directory-reporting-retention.md) |

### <a name="considerations"></a>Överväganden

Om det inte finns i galleriet målprogrammet, kan du använda ”ta med din egen app”. Läs mer: [vad är nytt i Enterprise programhantering i Azure Active Directory: lägga till anpassade program från en plats](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

 Tänk på följande krav:
   * Programmet måste ha en känd inloggnings-URL
   * På inloggningssidan ska innehålla ett HTML-formulär med en mer textfält som webbläsartillägg kan automatisk ifyllning. Åtminstone ska den innehålla användarnamn och lösenord.

## <a name="saas-shared-accounts-configuration"></a>SaaS delad konfiguration för konton

Ungefärlig tid till slutförd: 30 minuter

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| Listan över program och exakt inloggning URL: er i förväg. Du kan exempelvis använda Twitter. | [Twitter på Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/aad.twitter)<br/>[Registrera dig för Twitter](https://twitter.com/signup?lang=en) |
| Delade autentiseringsuppgifter för SaaS-programmet. | [Dela konton med hjälp av Azure AD](active-directory-sharing-accounts.md)<br/>[Azure AD automated lösenord förlängningen för Facebook, Twitter och LinkedIn nu i preview! -Bloggen Enterprise Mobility and Security] (https://blogs.technet.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/ ) |
| Autentiseringsuppgifter för minst två teammedlemmar som kommer att använda samma konto. De måste vara en del av en säkerhetsgrupp. | [Tilldela en användare eller grupp till en enterprise-app i Azure Active Directory](manage-apps/assign-user-or-group-access-portal.md) |
| Lokal administratörsåtkomst till en dator för att distribuera Access panelen-tillägg för Internet Explorer, Chrome eller Firefox | [Tillägget för åtkomst-panelen för Internet Explorer](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Tillägget för åtkomst-panelen för Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Tillägget för åtkomst-panelen för Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |

### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Installera webbläsartillägg för | [Tillägget för åtkomst-panelen för Internet Explorer](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Tillägget för åtkomst-panelen för Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Tillägget för åtkomst-panelen för Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |
| Konfigurera program från galleriet | [Vad är nytt i Enterprise programhantering i Azure Active Directory: nya och förbättrade programgalleriet](active-directory-enterprise-apps-whats-new-azure-portal.md#improvements-to-the-azure-active-directory-application-gallery) |
| Konfigurera enkel inloggning | [Hantera enkel inloggning för företagsappar i den nya Azure-portalen: lösenordsbaserade inloggning](manage-apps/configure-single-sign-on-portal.md#password-based-sign-on) |
| Tilldela appen till den grupp som definieras i förutsättningarna vid tilldelning av autentiseringsuppgifter | [Tilldela en användare eller grupp till en enterprise-app i Azure Active Directory](manage-apps/assign-user-or-group-access-portal.md) |
| Logga in som olika användare åtkomst till appen som den **samma delade kontot.**  |  |
| Du kan också kan du kontrollera användningsrapporter program. Observera att det finns vissa svarstid, så du måste vänta ett tag för att se trafik i rapporterna. | [Inloggningsaktivitet rapporterna i Azure Active Directory-portalen: användning av hanterade program](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Kvarhållningsprinciper för rapporter i Azure Active Directory](active-directory-reporting-retention.md) |


### <a name="considerations"></a>Överväganden

Om det inte finns i galleriet målprogrammet, kan du använda ”ta med din egen app”. Läs mer: [vad är nytt i Enterprise programhantering i Azure Active Directory: lägga till anpassade program från en plats](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

 Tänk på följande krav:
   * Programmet måste ha en känd inloggnings-URL
   * På inloggningssidan ska innehålla ett HTML-formulär med en mer textfält som webbläsartillägg kan automatisk ifyllning. Åtminstone ska den innehålla användarnamn och lösenord.

## <a name="app-proxy-configuration"></a>Appen proxykonfiguration

Ungefärlig tid till slutförd: 20 minuter

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| Ett Microsoft Azure AD basic eller premium-prenumeration och Azure AD-katalog som du är en global administratör | [Azure Active Directory-versioner](fundamentals/active-directory-whatis.md) |
| Ett webbprogram finns lokalt som du vill konfigurera för fjärråtkomst |  |
| En server som kör Windows Server 2012 R2 eller Windows 8.1 eller senare, där du kan installera Application Proxy Connector | [Förstå Azure AD Application Proxy-kopplingar](manage-apps/application-proxy-connectors.md) |
| Om det finns en brandvägg i sökvägen, kontrollerar du att den är öppen så att anslutningsverktyget kan göra HTTPS (TCP) förfrågningar till Application Proxy | [Aktivera Application Proxy på Azure-portalen: krav för Application Proxy](manage-apps/application-proxy-enable.md#application-proxy-prerequisites) |
| Om din organisation använder proxyservrar för att ansluta till internet, titta närmare på bloggen efter arbeta med befintliga lokala proxyservrar för information om hur du konfigurerar dem. | [Arbeta med befintliga lokala proxyservrar](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md) |


### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Installera en koppling på servern | [Aktivera Application Proxy på Azure-portalen: Installera och registrera kopplingen](manage-apps/application-proxy-enable.md#install-and-register-a-connector) |
| Publicera lokala program i Azure AD som ett program med Application Proxy | [Publicera program med Azure AD Application Proxy](manage-apps/application-proxy-publish-azure-portal.md) |
| Tilldela testanvändare | [Publicera program med Azure AD Application Proxy: Lägg till en testanvändare](manage-apps/application-proxy-publish-azure-portal.md#add-a-test-user) |
| Alternativt kan du konfigurera en enkel inloggning för dina användare | [Tillhandahålla enkel inloggning med Azure AD Application Proxy](manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md) |
| Testa appen genom att logga in till MyApps portalen som tilldelad användare | https://myapps.microsoft.com |

### <a name="considerations"></a>Överväganden

1. Vi rekommenderar att placera kopplingen i företagets nätverk, men det finns fall när du ser bättre prestanda att placera det i molnet. Läs mer: [nätverk topologiöverväganden när du använder Azure Active Directory Application Proxy](manage-apps/application-proxy-network-topology.md)
2. För ytterligare säkerhetsinformation om och hur detta ger ett särskilt säker fjärråtkomst lösning genom att bara hantera utgående anslutningar finns: [säkerhetsaspekter för att komma åt appar från en fjärrdator med hjälp av Azure AD Application Proxy](manage-apps/application-proxy-security.md)

## <a name="generic-ldap-connector-configuration"></a>Allmän LDAP Connector-konfiguration

Ungefärlig tid till slutförd: 60 minuter

> [!IMPORTANT]
> Det här är en avancerad konfiguration kräver bekant med FIM/MIM. Om används i produktionen, rekommenderar vi frågor om den här konfigurationen gå igenom [Premier Support](https://support.microsoft.com/premier).

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| Azure AD Connect installeras och konfigureras | Byggblock: [katalogsynkronisering - Hash-synkronisering](#directory-synchronization--password-hash-sync-phs--new-installation) |
| ADLDS instans möte krav | [Teknisk referens för allmän LDAP Connector: översikt över allmän LDAP Connector](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap#overview-of-the-generic-ldap-connector) |
| Lista över arbetsbelastningar som använder och attribut som är kopplade till dessa arbetsbelastningar | [Azure AD Connect-synkronisering: attribut synkroniserade till Azure Active Directory](./connect/active-directory-aadconnectsync-attributes-synchronized.md) |


### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Lägg till allmän LDAP Connector | [Teknisk referens för allmän LDAP Connector: skapa en ny koppling](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap#create-a-new-connector) |
| Skapa körningsprofiler för kopplingen som skapats (fullständig import, Deltaimport, fullständig synkronisering, Deltasynkronisering, export) | [Skapa en Management Agent kör profil](https://technet.microsoft.com/library/jj590219(v=ws.10).aspx)<br/> [Med hjälp av anslutningar med Azure AD Connect Sync Service Manager](./connect/active-directory-aadconnectsync-service-manager-ui-connectors.md)|
| Kör fullständig import profil och kontrollera att det finns objekt i anslutarplats | [Sök efter ett utrymme Connector-objekt](https://technet.microsoft.com/library/jj590287(v=ws.10).aspx)<br/>[Med hjälp av anslutningar med Azure AD Connect Sync Service Manager: söka Anslutarplats](./connect/active-directory-aadconnectsync-service-manager-ui-connectors.md#search-connector-space) |
| Skapa Synkroniseringsregler, så att objekt i Metaversumet har attribut som krävs för arbetsbelastningar | [Azure AD Connect-synkronisering: bästa praxis för att ändra standardkonfigurationen: ändras till Synkroniseringsregler](./connect/active-directory-aadconnectsync-best-practices-changing-default-configuration.md#changes-to-synchronization-rules)<br/>[Azure AD Connect-synkronisering: Förstå deklarativ etablering](./connect/active-directory-aadconnectsync-understanding-declarative-provisioning.md)<br/>[Azure AD Connect-synkronisering: Förstå uttryck för deklarativ etablering](./connect/active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) |
| Starta fullständig synkroniseringscykel | [Azure AD Connect-synkronisering: Schemaläggaren: starta Schemaläggaren](./connect/active-directory-aadconnectsync-feature-scheduler.md#start-the-scheduler) |
| Gör eventuella fel felsökning | [Felsök ett objekt som inte synkroniseras med Azure AD](./connect/active-directory-aadconnectsync-troubleshoot-object-not-syncing.md) |
| Kontrollera att LDAP-användare kan logga in och komma åt programmet | https://myapps.microsoft.com |

### <a name="considerations"></a>Överväganden

> [!IMPORTANT]
> Det här är en avancerad konfiguration kräver bekant med FIM/MIM. Om används i produktionen, rekommenderar vi frågor om den här konfigurationen gå igenom [Premier Support](https://support.microsoft.com/premier).

## <a name="groups---delegated-ownership"></a>Grupper – delegerad ägarskap

Ungefärlig tid till slutförd: 10 minuter

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| SaaS-program (federerad enkel inloggning eller lösenord SSO) har redan konfigurerats | Byggblock: [SaaS federerad enkel inloggning konfiguration](#saas-federated-sso-configuration) |
| Moln-grupp som tilldelas åtkomst till programmet i #1 identifieras | Byggblock: [SaaS federerad enkel inloggning konfiguration](#saas-federated-sso-configuration) <br/>[Skapa en grupp och lägga till medlemmar i Azure Active Directory](fundamentals/active-directory-groups-create-azure-portal.md) |
| Autentiseringsuppgifterna för gruppägare är tillgängliga | [Hantera åtkomst till resurser med Azure Active Directory-grupper](fundamentals/active-directory-manage-groups.md) |
| Autentiseringsuppgifter för informationsarbetare att komma åt apparna som har identifierats | [Vad är åtkomstpanelen?](active-directory-saas-access-panel-introduction.md) |


### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Identifiera den grupp som har beviljats åtkomst till programmet och konfigurera ägare av given grupp| [Hantera inställningar för en grupp i Azure Active Directory ](fundamentals/active-directory-groups-settings-azure-portal.md) |
| Logga in som gruppägare, se medlemskap i grupper-fliken i åtkomstpanelen | [Azure Active Directory-grupper Management-sidan](https://account.activedirectory.windowsazure.com/r#/groups) |
| Lägg till informationsarbetare som du vill testa |  |
| Logga in som informationsanställda, bekräfta panelen är tillgängligt | [Vad är åtkomstpanelen?](active-directory-saas-access-panel-introduction.md) |

### <a name="considerations"></a>Överväganden

Om programmet har etablering aktiverat kan behöva du vänta några minuter för etablering för att slutföra innan du använder program som informationsanställda.

## <a name="saas-and-identity-lifecycle"></a>SaaS och identitet livscykel

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| SaaS-program (federerad enkel inloggning eller lösenord SSO) har redan konfigurerats | Byggblock: [SaaS federerad enkel inloggning konfiguration](#saas-federated-sso-configuration) |
| Moln-grupp som tilldelas åtkomst till programmet i #1 identifieras | Byggblock: [SaaS federerad enkel inloggning konfiguration](#saas-federated-sso-configuration) <br/>[Skapa en grupp och lägga till medlemmar i Azure Active Directory](fundamentals/active-directory-groups-create-azure-portal.md) |
| Autentiseringsuppgifter för informationsarbetare att komma åt apparna som har identifierats | [Vad är åtkomstpanelen?](active-directory-saas-access-panel-introduction.md) |


### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Ta bort användaren från appen har tilldelats gruppen | [Hantera gruppmedlemskap för användare i din Azure Active Directory-klient](fundamentals/active-directory-groups-members-azure-portal.md) |
| Vänta några minuter för avetablering | [Automatisk Användaretablering för SaaS-App i Azure AD: hur fungerar automatisk etablering arbete?](active-directory-saas-app-provisioning.md#how-does-automatic-provisioning-work) |
| Logga in som informationsarbetare till Mina appar portal och bekräfta brickan saknas på en separat webbläsarsession | http://myapps.microsoft.com |


### <a name="considerations"></a>Överväganden

Dra slutsatser PoC scenariot för att förtid uppgår och/eller frånvaron scenarier. Om användaren hämtar inaktiverad i lokala AD eller tas bort, finns det inte längre ett sätt att logga in till SaaS-program.

## <a name="self-service-access-to-application-management"></a>Åtkomst till hantering av program via självbetjäning

Ungefärlig tid till slutförd: 10 minuter

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| Identifiera POC användare som begär åtkomst till program som en del av gruppen | Byggblock: [SaaS federerad enkel inloggning konfiguration](#saas-federated-sso-configuration) |
| Målprogrammet distribueras | Byggblock: [SaaS federerad enkel inloggning konfiguration](#saas-federated-sso-configuration) |

### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Gå till bladet företagsprogram i hanteringsportalen för Azure AD | [Azure AD-hanteringsportalen: Företagsprogram](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) |
| Konfigurera program från förutsättningar med självbetjäning | [Vad är nytt i Enterprise programhantering i Azure Active Directory: Konfigurera självbetjäning programåtkomst](active-directory-enterprise-apps-whats-new-azure-portal.md#configure-self-service-application-access) |
| Logga in som informationsarbetare till Mina appar portal | http://myapps.microsoft.com |
| Lägg märke till ”+ Lägg till app” knappen op på sidan. Få åtkomst till appen. |  |

### <a name="considerations"></a>Överväganden

De program som har valt kanske har etablering kraven, så kommer omedelbart att appen kan orsaka fel. Om programmet valt har stöd för etablering med azure ad och den är konfigurerad, kan du använda den som en möjlighet för att visa hela flödet fungerar slutpunkt till slutpunkt. Se byggblock för [SaaS federerad enkel inloggning Configuration](#saas-federated-sso-configuration) för ytterligare rekommendationer

## <a name="self-service-password-reset"></a>Lösenordsåterställning via självbetjäning

Ungefärlig tid till slutförd: 15 minuter

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| Aktivera lösenordshantering av självbetjäning i din klient. | [Azure Active Directory återställning av lösenord för IT-administratörer](active-directory-passwords-update-your-own-password.md) |
| Aktivera tillbakaskrivning av lösenord hantera lösenord från lokala. Obs detta kräver vissa Azure AD Connect versioner | [Krav för tillbakaskrivning av lösenord](authentication/howto-sspr-writeback.md) |
| Identifiera PoC-användare som kommer att använda den här funktionen och kontrollera att de är medlemmar i en säkerhetsgrupp. Användarna måste vara icke-administratörer att fullständigt demonstrerar kapacitet | [Anpassa: Azure AD-lösenordshantering: begränsa åtkomst till återställning av lösenord](authentication/howto-sspr-writeback.md) |


### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Gå till Azure AD-hanteringsportalen: återställning av lösenord | [Hanteringsportalen för Azure AD: Lösenordsåterställning via](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) |
| Kontrollera att principen för återställning av lösenord. POC ändamål, du kan använda för telefonsamtal och Q & A. Det rekommenderas att aktivera registrering krävs vid logga in på åtkomstpanelen |  |
| Logga ut och logga in som en informationsanställd |  |
| Ange data för självbetjäning av återställning av lösenord som konfigurerats per steg 2 | https://aka.ms/ssprsetup |
| Stäng webbläsaren |  |
| Börja om från början inloggningen som informationsarbetare som du använde i steg 4 |  |
| Återställa lösenordet | [Uppdatera ditt eget lösenord: återställa mitt lösenord](active-directory-passwords-update-your-own-password.md) |
| Försök logga in med ditt nya lösenord till Azure AD samt att de lokala resurser |  |

### <a name="considerations"></a>Överväganden

1. Om uppgradering av Azure AD Connect kommer att orsaka friktion, Överväg att använda mot molnet konton eller göra det en demonstration mot en separat miljö
2. Administratörerna har en annan princip och med administratörskontot för att återställa lösenordet kan förorena konceptbeviset och orsaka förvirring. Kontrollera att du använder ett vanligt användarkonto för att testa åtgärder för återställning


## <a name="azure-multi-factor-authentication-with-phone-calls"></a>Azure Multi-Factor Authentication med telefonsamtal

Ungefärlig tid till slutförd: 10 minuter

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| Identifiera POC-användare som använder MFA  |  |
| Phone med mottagning för MFA-kontrollen  | [Vad är Azure Multi-Factor Authentication?](authentication/multi-factor-authentication.md) |

### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Gå till ”användare och grupper” bladet i hanteringsportalen för Azure AD | [Hanteringsportalen för Azure AD: Användare och grupper](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/Overview/menuId/) |
| Välj ”alla användare” bladet |  |
| I överst raden Välj ”Multifaktorautentisering”-knapp | Direkt-URL för Azure MFA-portalen: https://aka.ms/mfaportal |
| I inställningarna för ”användare” Välj PoC-användare och aktivera dem för MFA | [Användartillstånd i Azure Multi-Factor Authentication](authentication/howto-mfa-userstates.md) |
| Logga in som PoC användar- och gå igenom processen bevis upp  |  |

### <a name="considerations"></a>Överväganden

1. Stegen i den här inställningen uttryckligen MFA för en användare på alla inloggningar byggblock PoC. Det finns andra verktyg, till exempel villkorlig åtkomst och identitetsskydd som engagera MFA på fler scenarier som mål. Detta kan vara något att tänka på när du flyttar från POC till produktionen.
2. PoC stegen i den här byggblock använder explicit telefonsamtal som metod för MFA för expedience. När du flyttar från POC till produktion bör du använda program som de [Microsoft Authenticator](../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) som din andra faktor när det är möjligt.
Läs mer: [utkast NIST Special Publication 800 63B](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="mfa-conditional-access-for-saas-applications"></a>MFA villkorlig åtkomst för SaaS-program

Ungefärlig tid till slutförd: 10 minuter

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| Identifiera PoC användare om du vill rikta principen. Dessa användare måste vara i en säkerhetsgrupp för att definiera omfattningen av principen för villkorlig åtkomst | [Konfiguration för SaaS-federerad enkel inloggning](#saas-federated-sso-configuration) |
| SaaS-program har redan konfigurerats |  |
| PoC användare har redan tilldelats till programmet |  |
| Autentiseringsuppgifterna för användaren POC är tillgängliga |  |
| POC användare är registrerad för Multifaktorautentisering. Använda en telefon med mottagning | https://aka.ms/ssprsetup |
| Enheter i det interna nätverket. IP-adress som konfigurerats i det interna adressintervallet | Hitta din ip-adress: https://www.bing.com/search?q=what%27s+my+ip |
| Enhet i det externa nätverket (kan vara en telefon med hjälp av en operatör mobila nätverk) |  |

### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Gå till Azure AD-hanteringsportalen: bladet för villkorlig åtkomst | [Hanteringsportalen för Azure AD: Villkorlig åtkomst](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) |
| Skapa princip för villkorlig åtkomst:<br/>-PoC målgruppsanvändare under ”användare och grupper”<br/>-PoC målprogrammet under ”molnappar”<br/>-Mål förutom betrodda relationer ”villkor” -> ”platser” alla platser **Obs:** tillförlitliga IP-adresser har konfigurerats i [MFA-portalen](https://account.activedirectory.windowsazure.com/UserManagement/MfaSettings.aspx)<br/>-Kräv Multi-Factor authentication under ”bidrag” | [Kom igång med villkorlig åtkomst i Azure Active Directory: principen konfigurationssteg](active-directory-conditional-access-azure-portal-get-started.md#policy-configuration-steps) |
| Åtkomst till program från företagsnätverk | [Kom igång med villkorlig åtkomst i Azure Active Directory: testa principen](active-directory-conditional-access-azure-portal-get-started.md#testing-the-policy) |
| Åtkomst till programmet från offentliga nätverk | [Kom igång med villkorlig åtkomst i Azure Active Directory: testa principen](active-directory-conditional-access-azure-portal-get-started.md#testing-the-policy) |

### <a name="considerations"></a>Överväganden

Om du använder federation kan du använda lokala identitetsprovider (IdP) för att kommunicera tillståndet inuti/utanför företagets nätverk med anspråk. Du kan använda den här tekniken utan att behöva hantera listan över IP-adresser som kan vara komplicerade att utvärdera och hantera i stora organisationer. Du behöver konto på ”nätverket centrala”-scenariot (en användare som loggar från det interna nätverket och när du är inloggad växlar platser, till exempel ett kafé) och kontrollera att du förstår följderna i som installationsprogrammet. Läs mer: [skydda molnresurser med Azure Multi-Factor Authentication och AD FS: tillförlitliga IP-adresser för externa användare](authentication/howto-mfa-adfs.md#trusted-ips-for-federated-users)

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

Ungefärlig tid till slutförd: 15 minuter

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| Identifiera den globala administratören som ska ingå i POC för PIM | [Börja använda Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md) |
| Identifiera den globala administratören som ska bli säkerhetsadministratör | [Börja använda Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md)<br/> [Olika administrativa roller i Azure Active Directory-PIM](active-directory-privileged-identity-management-roles.md) |
| Valfritt: Kontrollera om de globala administratörerna har e-poståtkomst till utöva e-postaviseringar i PIM | [Vad är Azure AD Privileged Identity Management?: Konfigurera produktaktivering rollinställningar](active-directory-privileged-identity-management-configure.md#configure-the-role-activation-settings)


### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Logga in på https://portal.azure.com som en global administratör (GA) och bootstrap PIM-bladet. Den globala administratören som utför det här steget dirigeras som säkerhetsadministratör.  Vi ska anropa den här aktören GA1 | [Guiden Säkerhet i Azure AD Privileged Identity Management](active-directory-privileged-identity-management-security-wizard.md) |
| Identifiera den globala administratören och flytta dem från permanent till berättigade. Detta bör vara en administratör som är separat från den som används i steg 1 för tydlighetens skull. Vi ska anropa den här aktören GA2 | [Azure AD Privileged Identity Management: Så att lägga till eller ta bort en användarroll](active-directory-privileged-identity-management-how-to-add-role-to-user.md)<br/>[Vad är Azure AD Privileged Identity Management?: Konfigurera produktaktivering rollinställningar](active-directory-privileged-identity-management-configure.md#configure-the-role-activation-settings)  |
| Nu kan logga in som GA2 till https://portal.azure.com och försök att ändra ”inställningar”. Observera att vissa alternativ är nedtonade. | |
| I en ny flik och samma session som steg 3 kan nu gå till https://portal.azure.com och lägga till PIM-bladet på instrumentpanelen. | [Så här aktiverar eller inaktiverar roller i Azure AD Privileged Identity Management: lägga till programmet Privileged Identity Management](active-directory-privileged-identity-management-how-to-activate-role.md#add-the-privileged-identity-management-application) |
| Begära aktivering till rollen Global administratör | [Så här aktiverar eller inaktiverar roller i Azure AD Privileged Identity Management: aktivera en roll](active-directory-privileged-identity-management-how-to-activate-role.md#activate-a-role) |
| Observera att om GA2 aldrig registrerat för Multifaktorautentisering, registrering för Azure MFA blir det nödvändigt |  |
| Gå tillbaka till den ursprungliga fliken i steg 3 och klicka på Uppdatera i webbläsaren. Observera att du nu har behörighet att ändra ”inställningar” | |
| Om dina globala administratörer har e-post som är aktiverad kan du eventuellt Kontrollera GA1 och GA2's inkorg och se meddelandet i rollen som aktiveras |  |
| 8 Kontrollera granskningshistorik och se rapporten för att bekräfta höjning av GA2 visas. | [Vad är Azure AD Privileged Identity Management?: rollen granskningsaktivitet](active-directory-privileged-identity-management-configure.md#review-role-activity) |

### <a name="considerations"></a>Överväganden

Den här funktionen är en del av Azure AD Premium P2 och/eller EMS E5

## <a name="discovering-risk-events"></a>Identifiering av riskhändelser

Ungefärlig tid till slutförd: 20 minuter

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| Enhet med Tor webbläsaren hämtas och installeras | [Hämta Tor webbläsare](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Åtkomst till POC användare göra inloggningen | [Azure Active Directory-identitetsskydd playbook](active-directory-identityprotection-playbook.md) |

### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Öppna tor webbläsare | [Hämta Tor webbläsare](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Logga in på https://myapps.microsoft.com med POC-användarkonto | [Azure Active Directory-identitetsskydd playbook: simulering av riskhändelser](active-directory-identityprotection-playbook.md#simulating-risk-events) |
| Vänta 5-7 minuter |  |
| Logga in som global administratör för att https://portal.azure.com och öppna bladet Identity Protection | https://aka.ms/aadipgetstarted |
| Öppna bladet risk händelser. Du bör se en post under ”inloggningar från anonyma IP-adresser”  | [Azure Active Directory-identitetsskydd playbook: simulering av riskhändelser](active-directory-identityprotection-playbook.md#simulating-risk-events) |

### <a name="considerations"></a>Överväganden

Den här funktionen är en del av Azure AD Premium P2 och/eller EMS E5

## <a name="deploying-sign-in-risk-policies"></a>Distribuera inloggning risk principer

Ungefärlig tid till slutförd: 10 minuter

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| Enhet med Tor webbläsaren hämtas och installeras | [Hämta Tor webbläsare](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Åtkomst som POC användare att logga i tester |  |
| POC användare är registrerad med MFA. Se till att använda en telefon med mottagning | Byggblock: [Azure Multi-Factor Authentication med telefonsamtal](#azure-multi-factor-authentication-with-phone-calls) |


### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Logga in som global administratör för att https://portal.azure.com och öppna bladet Identity Protection | https://aka.ms/aadipgetstarted |
| Aktivera inloggning riskprincipen på följande sätt:<br/>-Tilldelad till: POC användare<br/>-Villkor: Logga in risk medelhög eller högre (logga in från anonyma plats anses som en medelhög risknivå)<br/>-Kontroller: Kräva MFA | [Azure Active Directory-identitetsskydd playbook: Logga in risk](active-directory-identityprotection-playbook.md) |
| Öppna tor webbläsare | [Hämta Tor webbläsare](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Logga in på https://myapps.microsoft.com med PoC-användarkonto |  |
| Lägg märke till MFA-kontrollen | [Logga in som inträffar med Azure AD Identity Protection: riskfyllda inloggning återställning](active-directory-identityprotection-flows.md#risky-sign-in-recovery)

### <a name="considerations"></a>Överväganden

Den här funktionen är en del av Azure AD Premium P2 och/eller EMS E5. Läs mer om riskhändelser besök: [riskhändelser för Azure Active Directory](active-directory-reporting-risk-events.md)

## <a name="configuring-certificate-based-authentication"></a>Konfigurera certifikatbaserad autentisering

Ungefärlig tid för att slutföra: 20 minuter

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| Enheten användarcertifikat etablerats (Windows, iOS eller Android) från Företags-PKI | [Distribuera användarcertifikat](https://msdn.microsoft.com/library/cc770857.aspx) |
| Azure AD-domän federerade med AD FS | [Azure AD Connect och federation](./connect/active-directory-aadconnectfed-whatis.md)<br/>[Översikt över Active Directory Certificate Services](https://technet.microsoft.com/library/hh831740.aspx)|
| Har Microsoft Authenticator-appen för iOS-enheter | [Kom igång med Microsoft Authenticator-appen](../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) |

### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Aktivera ”autentisering med användarcertifikat” på AD FS | [Konfigurera autentiseringsprinciper: Konfigurera globalt primär autentisering i Windows Server 2012 R2](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-authentication-policies#to-configure-primary-authentication-globally-in-windows-server-2012-r2) |
| Valfritt: Aktivera certifikatautentisering i Azure AD för Exchange Active Sync-klienter | [Komma igång med certifikatbaserad autentisering i Azure Active Directory](active-directory-certificate-based-authentication-get-started.md) |
| Navigera till åtkomstpanelen och autentisera med hjälp av användarcertifikat | https://myapps.microsoft.com |

### <a name="considerations"></a>Överväganden

Läs mer om varningar av den här distributionen finns: [ADFS: autentisering med datorcertifikat med Azure AD & Office 365](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/)



> [!NOTE]
> Vara bör låst innehas av användarcertifikat. Antingen genom att hantera enheter eller med PIN-kod vid smartkort.



[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]
