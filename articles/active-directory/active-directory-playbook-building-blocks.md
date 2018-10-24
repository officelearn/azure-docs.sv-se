---
title: Azure Active Directory som bevis på koncept spelbok byggstenar | Microsoft Docs
description: Utforska och snabbt implementera scenarier för identitets- och åtkomsthantering
services: active-directory
keywords: Azure active directory, playbook Proof of Concept, PoC
documentationcenter: ''
author: dstefanMSFT
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2018
ms.author: dstefan
ms.openlocfilehash: db6778bdea2e3e133535bf28b3c2297cdb65599d
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49958915"
---
# <a name="azure-active-directory-proof-of-concept-playbook-building-blocks"></a>Azure Active Directory som bevis på koncept strategibok: komponenter

## <a name="catalog-of-roles"></a>Katalog med roller

| Roll | Beskrivning | Bevis på koncept (PoC) ansvar |
| --- | --- | --- |
| **Identitetsarkitektur / Utvecklingsteamet** | Det här teamet är vanligtvis det som utformar lösningen implementerar prototyper, styr godkännanden och slutligen lämnar ut till åtgärder | De tillhandahåller miljöerna och som utvärderar olika scenarier hanterbarhet perspektiv |
| **Lokala identitet driftsteamet** | Hanterar de olika identitet lokalerna datakällor: Active Directory-skogar, LDAP-kataloger, HR-system och Federation identitetsleverantörer. | Ge åtkomst till lokala resurser som krävs för PoC-scenarier.<br/>De bör ingå så lite som möjligt|
| **Teknisk programägare** | Teknisk ägare till de olika molnappar och tjänster som integreras med Azure AD | Innehåller detaljerad information om SaaS-program (potentiellt instanser för att testa) |
| **Global administratör för Azure AD** | Hanterar Azure AD-konfiguration | Ange autentiseringsuppgifter för att konfigurera synkroniseringstjänsten. Vanligtvis samma team som Identitetsarkitektur under PoC men avgränsa under fasen för åtgärder|
| **Database-teamet** | Ägare till databasen-infrastruktur | Ge åtkomst till SQL-miljö (AD FS eller Azure AD Connect) för specifika scenario förberedelser.<br/>De bör ingå så lite som möjligt |
| **Team av** | Ägare av nätverksinfrastrukturen | Ange nödvändig åtkomst på nätverksnivå för synkroniseringsservrar att korrekt åtkomst till datakällor och molntjänster (brandväggsregler, öppnade portar, IPSec-regler osv.) |
| **Säkerhetsteamet** | Definierar säkerhetsstrategi, analyserar säkerhetsrapporter från olika källor och följer på resultaten. | Ange mål security utvärdering scenarier |

## <a name="common-prerequisites-for-all-building-blocks"></a>Vanliga krav för alla byggblock

Här följer några förutsättningar som krävs för alla POC med Azure AD Premium.

| Förhandskrav | Resurser |
| --- | --- |
| Azure AD-klient som definierats med en giltig Azure-prenumeration | [Skaffa en Azure Active Directory-klient](develop/quickstart-create-new-tenant.md)<br/>**Obs:** om du redan har en miljö med Azure AD Premium-licenser kan du få en noll cap-prenumeration genom att gå till https://aka.ms/accessaad <br/>Läs mer på: https://blogs.technet.microsoft.com/enterprisemobility/2016/02/26/azure-ad-mailbag-azure-subscriptions-and-azure-ad-2/ och https://technet.microsoft.com/library/dn832618.aspx |
| Domäner som har definierats och verifierats | [Lägga till ett anpassat domännamn i Azure Active Directory](active-directory-domains-add-azure-portal.md)<br/>**Obs:** vissa arbetsbelastningar som till exempel Power BI kunde har etablerat en azure AD-klient under försättsbladen. Om du vill kontrollera om en viss domän är kopplade till en klient, navigera till https://login.microsoftonline.com/{domain}/v2.0/.well-known/openid-configuration. Om du får ett lyckat svar och sedan domänen har redan tilldelats en klient och ta över kan behövas. I så fall kan du kontakta Microsoft för ytterligare vägledning. Mer information om de gäller alternativ på: [självbetjäningsregistrering för Azure?](users-groups-roles/directory-self-service-signup.md) |
| Azure AD Premium eller EMS utvärdering aktiverad | [Azure Active Directory Premium kostnadsfritt i en månad](https://azure.microsoft.com/trial/get-started-active-directory/) |
| Du har tilldelat Azure AD Premium eller EMS-licenser till PoC-användare | [Licensiera själv och dina användare i Azure Active Directory](active-directory-licensing-get-started-azure-portal.md) |
| Autentiseringsuppgifter för Azure AD Global administratör | [Tilldela administratörsroller i Azure Active Directory](users-groups-roles/directory-assign-admin-roles.md) |
| Valfritt men rekommenderas starkt: parallella laboratoriemiljö som reserv | [Förutsättningar för Azure AD Connect](hybrid/how-to-connect-install-prerequisites.md) |

## <a name="directory-synchronization---password-hash-sync-phs---new-installation"></a>Directory-synkronisering – lösenordets Hash-synkronisering (PHS) - nyinstallation

Ungefärlig tid att Slutför: en timme för mindre än 1 000 PoC-användare

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| Server för att köra Azure AD Connect | [Förutsättningar för Azure AD Connect](hybrid/how-to-connect-install-prerequisites.md) |
| POC-användare i samma domän och en del av en grupp och Organisationsenhet | [Anpassad installation av Azure AD Connect](hybrid/how-to-connect-install-custom.md#domain-and-ou-filtering) |
| Azure AD Connect funktioner som behövs för Konceptbeviset identifieras | [Ansluta Active Directory med Azure Active Directory – Konfigurera synkronisering funktioner](hybrid/how-to-connect-install-roadmap.md#configure-sync-features) |
| Du har krävs autentiseringsuppgifter för lokala och molnbaserade miljöer  | [Azure AD Connect: Konton och behörigheter](hybrid/reference-connect-accounts-permissions.md) |

### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Hämta den senaste versionen av Azure AD Connect | [Ladda ned Microsoft Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594) |
| Installera Azure AD Connect med den enklaste sökvägen: Express <br/>1. Filtrera till mål-Organisationsenhet för att minimera tid som Synkroniseringscykel<br/>2. Välj Målet uppsättning användare i den lokala gruppen.<br/>3. Distribuera funktioner som krävs av den andra POC-teman | [Azure AD Connect: Anpassad installation: domän och Organisationsenhet filtrering](hybrid/how-to-connect-install-custom.md#domain-and-ou-filtering) <br/>[Azure AD Connect: Anpassad installation: Filtrering baserad på grupp](hybrid/how-to-connect-install-custom.md#sync-filtering-based-on-groups)<br/>[Azure AD Connect: Integrera dina lokala identiteter med Azure Active Directory: Konfigurera synkroniseringsfunktioner](hybrid/how-to-connect-install-roadmap.md#configure-sync-features) |
| Öppna Azure AD Connect Användargränssnittet och se körs profiler slutförda (Import, synkronisering och export) | [Azure AD Connect sync: Scheduler](hybrid/how-to-connect-sync-feature-scheduler.md) |
| Öppna den [Azure AD-hanteringsportalen](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/), går du till ”alla användare”-bladet, Lägg till kolumn ”auktoritetskälla” och se användarna visas markerad korrekt som kommer från ”Windows Server AD” | [Azure AD-hanteringsportalen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) |

### <a name="considerations"></a>Överväganden

1. Titta på säkerhetsåtgärder för synkronisering av lösenords-hash [här](hybrid/how-to-connect-password-hash-synchronization.md).  Om synkronisering av lösenords-hash för pilot produktionsanvändarna definitivt inte är ett alternativ, bör du följande alternativ:
   * Skapa testanvändare i till produktionsdomänen. Kontrollera att du inte synkroniserar något annat konto
   * Flytta till en UAT-miljö
2.  Om du vill använda federation är det värt att förstå kostnaderna kopplade till en federerad lösning en lokal identitetsprovider utöver POC och mäta som mot fördelarna du letar efter:
    * Det är i den kritiska vägen så att du behöver att utforma för hög tillgänglighet
    * Det är en lokal tjänst måste du planera för kapacitet
    * Det är en lokal tjänst som du behöver övervaka/Underhåll/patch

Läs mer: [förstå Office 365 identitets- och Azure Active Directory - federerad identitet](https://support.office.com/article/Understanding-Office-365-identity-and-Azure-Active-Directory-06a189e7-5ec6-4af2-94bf-a22ea225a7a9#bk_federated)

## <a name="branding"></a>Anpassning

Ungefärlig tid att Slutför: 15 minuter

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| Tillgångar (bilder, logotyper, osv.); För bästa visualiseringen se till att ha resurserna storlekarna som rekommenderas. | [Lägga till företagsprofilering för inloggningssidan i Azure Active Directory](active-directory-branding-custom-signon-azure-portal.md) |
| Valfritt: Om miljön har en AD FS-servern, åtkomst till servern för att anpassa webbtema | [AD FS-inloggning användaranpassning](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/ad-fs-user-sign-in-customization) |
| Klientdatorn att utföra inloggning för slutanvändaren |  |
| Valfritt: Mobila enheter för att verifiera upplevelse |  |

### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Gå till Azure AD-hanteringsportalen | [Azure AD-hanteringsportalen - företagsanpassning](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/LoginTenantBranding) |
| Ladda upp resurser för inloggningssidan (hero logotyp, liten logotyp, etiketter, osv.). Du kan också om du har AD FS kan justera samma tillgångar med AD FS-inloggningssidor | [Lägga till företagsprofilering för din inloggning och åtkomstpanelen sidor: anpassningsbara element](fundamentals/customize-branding.md) |
| Vänta några minuter för att ändringen ska börja gälla helt |  |
| Logga in med autentiseringsuppgifter för POC till https://myapps.microsoft.com |  |
| Bekräfta utseendet och känslan i webbläsare | [Lägga till företagsprofilering för din inloggning och åtkomstpanelen sidor](fundamentals/customize-branding.md) |
| Du kan också bekräfta utseendet och känslan i andra enheter |  |

### <a name="considerations"></a>Överväganden

Om gamla utseendet och känslan kvar vid anpassning av tömma klientcachen webbläsaren och försök igen.

## <a name="group-based-licensing"></a>Grupp baserad licensiering

Ungefärlig tid att Slutför: 10 minuter

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| Alla användare i POC är en del av en säkerhetsgrupp (moln eller på plats) | [Skapa en grupp och Lägg till medlemmar i Azure Active Directory](fundamentals/active-directory-groups-create-azure-portal.md) |

### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Gå till bladet för licenser i Azure AD-hanteringsportalen | [Azure AD-hanteringsportalen: licensiering](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) |
| Tilldela licenser till gruppen med POC-användare. | [Tilldela licenser till en grupp av användare i Azure Active Directory](users-groups-roles/licensing-groups-assign.md) |

### <a name="considerations"></a>Överväganden

Vid eventuella problem går du till [scenarier, begränsningar och kända problem med att använda grupper för att hantera licensiering i Azure Active Directory](users-groups-roles/licensing-group-advanced.md)

## <a name="saas-federated-sso-configuration"></a>Konfiguration för SaaS-federerad enkel inloggning

Ungefärlig tid att Slutför: 60 minuter

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| Testmiljö för SaaS-program tillgängliga. I den här guiden använder vi ServiceNow som ett exempel.<br/>Vi rekommenderar starkt för att använda en test-instans för att minska friktionen hur du navigerar befintliga datakvalitet och mappningar. | Gå till https://developer.servicenow.com/app.do#! / home att starta processen för att få en test-instans |
| Administratörsåtkomst till ServiceNow-hanteringskonsolen | [Självstudier: Azure Active Directory-integration med ServiceNow](saas-apps/servicenow-tutorial.md) |
| Target uppsättning användare att tilldela programmet till. En säkerhetsgrupp som innehåller de PoC rekommenderas. <br/>Om det inte är möjligt att skapa gruppen, tilldela användare till direkt till programmet för konceptbeviset | [Tilldela en användare eller grupp till en enterprise-app i Azure Active Directory](manage-apps/assign-user-or-group-access-portal.md) |

### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Dela självstudiekursen och alla aktörer från Microsoft-Documentation  | [Självstudier: Azure Active Directory-integration med ServiceNow](saas-apps/servicenow-tutorial.md) |
| Ange ett fungerande möte och följ självstudier steg med varje skådespelare. | [Självstudier: Azure Active Directory-integration med ServiceNow](saas-apps/servicenow-tutorial.md) |
| Tilldela appen till den grupp som definieras i förutsättningarna. Om POC har villkorlig åtkomst i omfattningen, kan du gå tillbaka som senare och lägga till MFA, och liknande. <br/>Observera att detta startar i etableringsprocessen (om konfigurerad) |  [Tilldela en användare eller grupp till en enterprise-app i Azure Active Directory](manage-apps/assign-user-or-group-access-portal.md) <br/>[Skapa en grupp och Lägg till medlemmar i Azure Active Directory](fundamentals/active-directory-groups-create-azure-portal.md) |
| Använda Azure AD Portal för att lägga till ServiceNow program från galleriet| [Hantering av Azure AD Portal: företagsprogram](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/Overview) <br/>[Självstudier: Azure Active Directory-integration med ServiceNow](saas-apps/servicenow-tutorial.md) |
| Aktivera ”SAML-baserad inloggning” i ”enkel inloggning”-bladet på ServiceNow-App |  |
| Fyll i fälten ”inloggning på URL” och ”identifierare” med din ServiceNow-URL<br/>Markera kryssrutan för att ”gör nytt certifikat aktivt”<br/>och spara inställningarna |  |
| Öppna bladet ”konfigurera ServiceNow” längst ned på panelen för att visa anpassade anvisningar för hur du konfigurerar ServiceNow |  |
| Följ anvisningarna för att konfigurera ServiceNow |  |
| Aktivera ”automatisk” etablering i ”etablering”-bladet på ServiceNow-App | [Hantera konto etablering för företagsappar i nya Azure portal](manage-apps/configure-automatic-user-provisioning-portal.md) |
| Vänta några minuter medan etableringen har slutförts.  Under tiden kan kan du kontrollera etableringsrapporterna |  |
| Logga in på https://myapps.microsoft.com/ som en användare som har åtkomst | [Vad är åtkomstpanelen?](user-help/active-directory-saas-access-panel-introduction.md) |
| Klicka på ikonen för programmet som nyss skapades. Bekräfta åtkomst |  |
| Alternativt kan du kontrollera användningsrapporter för programmet. Observera att det finns vissa svarstid, så du behöver vänta en stund att se att trafiken i rapporterna. | [Logga in aktivitetsrapporter i Azure Active Directory-portalen: användning av hanterade program](reports-monitoring/concept-sign-ins.md#usage-of-managed-applications)<br/>[Kvarhållningsprinciper för rapporter i Azure Active Directory](reports-monitoring/reference-reports-data-retention.md) |

### <a name="considerations"></a>Överväganden

1. Om målprogrammet inte finns i galleriet, kan du använda ”ta med din egen app”. Läs mer: [konfigurera program som inte ingår i Azure AD-programgalleriet](manage-apps/configure-single-sign-on-non-gallery-applications.md).

## <a name="saas-password-sso-configuration"></a>SaaS-lösenord SSO-konfiguration

Ungefärlig tid att Slutför: 15 minuter

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| Testmiljö för SaaS-program. Ett exempel på lösenord SSO är HipChat och Twitter. För alla andra program måste den exakta Webbadressen till sidan med html inloggningsformuläret. | [Twitter på Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/aad.twitter)<br/>[HipChat på Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/aad.hipchat) |
| Testa konton för programmen. | [Registrera dig för Twitter](https://twitter.com/signup?lang=en)<br/>[Registrera dig kostnadsfritt: HipChat](https://www.hipchat.com/sign_up) |
| Target uppsättning användare att tilldela programmet till. En säkerhetsgrupp som innehåller användarna rekommenderas. | [Tilldela en användare eller grupp till en enterprise-app i Azure Active Directory](manage-apps/assign-user-or-group-access-portal.md) |
| Lokal administratörsåtkomst till en dator för att distribuera Access Panel-tillägg för Internet Explorer, Chrome eller Firefox | [Access Panel-tillägg för Internet Explorer](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Access Panel-tillägg för Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Access Panel-tillägg för Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |

### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Installera webbläsartillägget för | [Access Panel-tillägg för Internet Explorer](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Access Panel-tillägg för Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Access Panel-tillägg för Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |
| Konfigurera program från galleriet | [Självstudie: Konfigurera SAML-baserad enkel inloggning](manage-apps/configure-single-sign-on-portal.md) |
| Konfigurera enkel inloggning med lösenord | [Hantera enkel inloggning för företagsappar i nya Azure portal: lösenordsbaserad inloggning](manage-apps/what-is-single-sign-on.md#how-does-single-sign-on-with-azure-active-directory-work).|
| Tilldela appen till den grupp som definieras i förutsättningarna | [Tilldela en användare eller grupp till en enterprise-app i Azure Active Directory](manage-apps/assign-user-or-group-access-portal.md) |
| Logga in på https://myapps.microsoft.com/ som en användare som har åtkomst |  |
| Klicka på ikonen för programmet som nyss skapades. | [Vad är åtkomstpanelen?: lösenordsbaserad SSO utan att behöva etablera identitet](user-help/active-directory-saas-access-panel-introduction.md#password-based-sso-without-identity-provisioning) |
| Ange autentiseringsuppgifter för programmet | [Vad är åtkomstpanelen?: lösenordsbaserad SSO utan att behöva etablera identitet](user-help/active-directory-saas-access-panel-introduction.md#password-based-sso-without-identity-provisioning) |
| Stäng webbläsaren och upprepa inloggningen. Den här gången ska användaren få sömlös åtkomst till programmet. |  |
| Alternativt kan du kontrollera användningsrapporter för programmet. Observera att det finns vissa svarstid, så du behöver vänta en stund att se att trafiken i rapporterna. | [Logga in aktivitetsrapporter i Azure Active Directory-portalen: användning av hanterade program](reports-monitoring/concept-sign-ins.md#usage-of-managed-applications)<br/>[Kvarhållningsprinciper för rapporter i Azure Active Directory](reports-monitoring/reference-reports-data-retention.md) |

### <a name="considerations"></a>Överväganden

Om målprogrammet inte finns i galleriet, kan du använda ”ta med din egen app”. Läs mer: [konfigurera program som inte ingår i Azure AD-programgalleriet](manage-apps/configure-single-sign-on-non-gallery-applications.md)

 Tänk på följande krav:
   * Programmet bör ha en känd inloggnings-URL.
   * På inloggningssidan ska innehålla ett HTML-formulär med en mer textfält som webbläsartillägg kan automatisk ifyllning. Den bör åtminstone innehålla användarnamn och lösenord.

## <a name="saas-shared-accounts-configuration"></a>SaaS delad konfiguration för konton

Ungefärlig tid att Slutför: 30 minuter

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| Lista över målprogram och exakta logga in URL: en i tid. Exempelvis kan du använda Twitter. | [Twitter på Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/aad.twitter)<br/>[Registrera dig för Twitter](https://twitter.com/signup?lang=en) |
| Delade autentiseringsuppgifter för SaaS-programmet. | [Dela konton med hjälp av Azure AD](active-directory-sharing-accounts.md)<br/>[Azure AD automatiserad lösenord övergången för Facebook, Twitter och LinkedIn nu i förhandsversion! – Enterprise Mobility and Security-bloggen] (https://blogs.technet.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/ ) |
| Autentiseringsuppgifter för minst två gruppmedlemmar som kan komma åt samma konto. De måste inte ingå i en säkerhetsgrupp. | [Tilldela en användare eller grupp till en enterprise-app i Azure Active Directory](manage-apps/assign-user-or-group-access-portal.md) |
| Lokal administratörsåtkomst till en dator för att distribuera Access Panel-tillägg för Internet Explorer, Chrome eller Firefox | [Access Panel-tillägg för Internet Explorer](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Access Panel-tillägg för Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Access Panel-tillägg för Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |

### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Installera webbläsartillägget för | [Access Panel-tillägg för Internet Explorer](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Access Panel-tillägg för Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Access Panel-tillägg för Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |
| Konfigurera program från galleriet | [Självstudie: Konfigurera SAML-baserad enkel inloggning för ett program](manage-apps/configure-single-sign-on-portal.md) |
| Konfigurera enkel inloggning med lösenord | [Hantera enkel inloggning för företagsappar i nya Azure portal: lösenordsbaserad inloggning](manage-apps/what-is-single-sign-on.md#how-does-single-sign-on-with-azure-active-directory-work).|
| Tilldela appen till den grupp som definieras i förutsättningarna vid tilldelning av autentiseringsuppgifter | [Tilldela en användare eller grupp till en enterprise-app i Azure Active Directory](manage-apps/assign-user-or-group-access-portal.md) |
| Logga in som olika användare åtkomst till appen som den **samma delade konto.**  |  |
| Alternativt kan du kontrollera användningsrapporter för programmet. Observera att det finns vissa svarstid, så du behöver vänta en stund att se att trafiken i rapporterna. | [Logga in aktivitetsrapporter i Azure Active Directory-portalen: användning av hanterade program](reports-monitoring/concept-sign-ins.md#usage-of-managed-applications)<br/>[Kvarhållningsprinciper för rapporter i Azure Active Directory](reports-monitoring/reference-reports-data-retention.md) |


### <a name="considerations"></a>Överväganden

Om målprogrammet inte finns i galleriet, kan du använda ”ta med din egen app”. Läs mer: [Konfigurera enkel inloggning till program som inte ingår i Azure AD-programgalleriet](manage-apps/configure-single-sign-on-non-gallery-applications.md)

 Tänk på följande krav:
   * Programmet ska ha en känd inloggnings-URL
   * På inloggningssidan ska innehålla ett HTML-formulär med en mer textfält som webbläsartillägg kan automatisk ifyllning. Den bör åtminstone innehålla användarnamn och lösenord.

## <a name="app-proxy-configuration"></a>Approxy-konfiguration

Ungefärlig tid att Slutför: 20 minuter

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| Ett Microsoft Azure AD basic eller premium-prenumeration och Azure AD-katalog som du är en global administratör | [Azure Active Directory-versioner](fundamentals/active-directory-whatis.md) |
| Ett webbprogram finns en lokal som du vill konfigurera för fjärråtkomst |  |
| En server som kör Windows Server 2012 R2 eller Windows 8.1 eller senare, där du kan installera Application Proxy Connector | [Förstå Azure AD Application Proxy-anslutningar](manage-apps/application-proxy-connectors.md) |
| Om det finns en brandvägg i sökvägen, se till att den är öppen så att anslutningsverktyget kan göra förfrågningar HTTPS (TCP) till programproxyn | [Aktivera Application Proxy i Azure portal: Application Proxy-krav](manage-apps/application-proxy-enable.md#application-proxy-prerequisites) |
| Om din organisation använder proxyservrar för att ansluta till internet, ta en titt på bloggen publicera arbeta med befintliga lokala proxyservrar för information om hur du konfigurerar dem. | [Arbeta med befintliga lokala proxyservrar](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md) |


### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Installera en anslutningsapp på servern | [Aktivera Application Proxy i Azure portal: Installera och registrera Anslutningsappen](manage-apps/application-proxy-enable.md#install-and-register-a-connector) |
| Publicera lokala program i Azure AD som ett programproxy-program | [Publicera program med Azure AD Application Proxy](manage-apps/application-proxy-publish-azure-portal.md) |
| Tilldela testanvändare | [Publicera program med Azure AD Application Proxy: Lägg till en testanvändare](manage-apps/application-proxy-publish-azure-portal.md#add-a-test-user) |
| Alternativt kan du konfigurera en enkel inloggning för dina användare | [Tillhandahålla enkel inloggning med Azure AD Application Proxy](manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md) |
| Testa appen genom att logga in till MyApps-portalen som tilldelade användare | https://myapps.microsoft.com |

### <a name="considerations"></a>Överväganden

1. Vi rekommenderar att placera anslutningen i företagets nätverk, men det finns fall när du ser bättre prestanda för att placera det i molnet. Läs mer: [Network topologiöverväganden när du använder Azure Active Directory Application Proxy](manage-apps/application-proxy-network-topology.md)
2. Mer information om säkerhet och hur detta ger en mycket säker fjärråtkomst lösningen genom att endast utgående anslutningar finns i: [säkerhetsöverväganden för att komma åt appar via fjärranslutning med hjälp av Azure AD Application Proxy](manage-apps/application-proxy-security.md)

## <a name="generic-ldap-connector-configuration"></a>Allmän LDAP Connector-konfigurationen

Ungefärlig tid att Slutför: 60 minuter

> [!IMPORTANT]
> Det här är en avancerad konfiguration som kräver bekant med FIM/MIM. Om används i produktion, rekommenderar vi frågor om den här konfigurationen går igenom [Premier Support](https://support.microsoft.com/premier).

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| Azure AD Connect installerat och konfigurerat | Byggblock: [katalogsynkronisering - lösenordets Hash-synkronisering](#directory-synchronization--password-hash-sync-phs--new-installation) |
| ADLDS instans uppfyller kraven | [Teknisk referens för allmän LDAP Connector: översikt över den allmänna LDAP-Anslutningsappen](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap#overview-of-the-generic-ldap-connector) |
| Lista över arbetsbelastningar som använder och attribut som är associerade med dessa arbetsbelastningar | [Azure AD Connect-synkronisering: attribut som synkroniseras till Azure Active Directory](hybrid/reference-connect-sync-attributes-synchronized.md) |


### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Lägg till allmän LDAP Connector | [Teknisk referens för allmän LDAP Connector: skapa en ny anslutning](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap#create-a-new-connector) |
| Skapa körningsprofiler för kopplingen som skapats (fullständig import, Deltaimport, fullständig synkronisering, Deltasynkronisering, export) | [Skapa en Körningsprofil för Management-agenten](https://technet.microsoft.com/library/jj590219(v=ws.10).aspx)<br/> [Med hjälp av kopplingar med Azure AD Connect Sync Service Manager](hybrid/how-to-connect-sync-service-manager-ui-connectors.md)|
| Kör fullständig import-profil och kontrollera att det finns objekt i anslutarplatsen | [Sök efter en Anslutarplatsen](https://technet.microsoft.com/library/jj590287(v=ws.10).aspx)<br/>[Med hjälp av kopplingar med Azure AD Connect Sync Service Manager: Search Connector Space](hybrid/how-to-connect-sync-service-manager-ui-connectors.md#search-connector-space) |
| Skapa Synkroniseringsregler, så att objekt i Metaversumet har attribut som krävs för arbetsbelastningar | [Azure AD Connect-synkronisering: Metodtips för att ändra standardkonfigurationen: ändras till Synkroniseringsregler](hybrid/how-to-connect-sync-best-practices-changing-default-configuration.md#changes-to-synchronization-rules)<br/>[Azure AD Connect-synkronisering: Förstå deklarativ etablering](hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)<br/>[Azure AD Connect-synkronisering: Förstå uttryck för deklarativ etablering](hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) |
| Starta fullständig synkroniseringscykel | [Azure AD Connect-synkronisering: Scheduler: starta scheduler](hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler) |
| Gör eventuella fel felsökning | [Felsök ett objekt som inte synkroniseras med Azure AD](hybrid/tshoot-connect-object-not-syncing.md) |
| Kontrollera att LDAP-användare kan logga in och komma åt programmet | https://myapps.microsoft.com |

### <a name="considerations"></a>Överväganden

> [!IMPORTANT]
> Det här är en avancerad konfiguration som kräver bekant med FIM/MIM. Om används i produktion, rekommenderar vi frågor om den här konfigurationen går igenom [Premier Support](https://support.microsoft.com/premier).

## <a name="groups---delegated-ownership"></a>Grupper – delegerad ägarskap

Ungefärlig tid att Slutför: 10 minuter

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| SaaS-program (federerad enkel inloggning eller lösenord för enkel inloggning) har redan konfigurerats | Byggblock: [SaaS federerad SSO-konfiguration](#saas-federated-sso-configuration) |
| Cloud-grupp som har tilldelats åtkomst till programmet i #1 identifieras | Byggblock: [SaaS federerad SSO-konfiguration](#saas-federated-sso-configuration) <br/>[Skapa en grupp och Lägg till medlemmar i Azure Active Directory](fundamentals/active-directory-groups-create-azure-portal.md) |
| Autentiseringsuppgifter för gruppägare är tillgängliga | [Hantera åtkomst till resurser med Azure Active Directory-grupper](fundamentals/active-directory-manage-groups.md) |
| Autentiseringsuppgifter för personer som arbetar med åtkomst till apparna som har identifierats | [Vad är åtkomstpanelen?](user-help/active-directory-saas-access-panel-introduction.md) |


### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Identifiera den grupp som har beviljats åtkomst till programmet och konfigurera ägaren av en viss grupp| [Hantera inställningar för en grupp i Azure Active Directory ](fundamentals/active-directory-groups-settings-azure-portal.md) |
| Logga in som gruppägare till, finns i medlemskap i grupper-fliken i åtkomstpanelen | [Azure Active Directory-grupper Management-sidan](https://account.activedirectory.windowsazure.com/r#/groups) |
| Lägg till informationsanställda som du vill testa |  |
| Logga in som informationsanställda, bekräfta panelen är tillgänglig | [Vad är åtkomstpanelen?](user-help/active-directory-saas-access-panel-introduction.md) |

### <a name="considerations"></a>Överväganden

Om programmet har etablering aktiverad, kan du behöva vänta några minuter för etablering för att slutföra innan du använder programmet som informationsanställda.

## <a name="saas-and-identity-lifecycle"></a>SaaS- och Identitetslivscykel

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| SaaS-program (federerad enkel inloggning eller lösenord för enkel inloggning) har redan konfigurerats | Byggblock: [SaaS federerad SSO-konfiguration](#saas-federated-sso-configuration) |
| Cloud-grupp som har tilldelats åtkomst till programmet i #1 identifieras | Byggblock: [SaaS federerad SSO-konfiguration](#saas-federated-sso-configuration) <br/>[Skapa en grupp och Lägg till medlemmar i Azure Active Directory](fundamentals/active-directory-groups-create-azure-portal.md) |
| Autentiseringsuppgifter för personer som arbetar med åtkomst till apparna som har identifierats | [Vad är åtkomstpanelen?](user-help/active-directory-saas-access-panel-introduction.md) |


### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Ta bort användaren från gruppen har tilldelats appen | [Hantera gruppmedlemskap för användare i din Azure Active Directory-klient](fundamentals/active-directory-groups-members-azure-portal.md) |
| Vänta några minuter för avetablering | [Automatisk Användaretablering för SaaS-App i Azure AD: hur fungerar automatisk etablering?](manage-apps/user-provisioning.md#how-does-automatic-provisioning-work) |
| Logga in som informationsarbetare till Mina appar-portalen och bekräfta den panelen saknas på en separat webbläsarsession | http://myapps.microsoft.com |


### <a name="considerations"></a>Överväganden

Extrapolera PoC-scenario för att förtid uppgår och/eller frånvaron scenarier. Om användaren hämtar inaktiverad i lokala AD eller tas bort, det finns inte längre ett sätt att logga in på SaaS-program.

## <a name="self-service-access-to-application-management"></a>Åtkomst till hantering av program för Självbetjäningsåterställning

Ungefärlig tid att Slutför: 10 minuter

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| Identifiera POC-användare som begär åtkomst till program som en del av gruppen | Byggblock: [SaaS federerad SSO-konfiguration](#saas-federated-sso-configuration) |
| Målprogrammet distribueras | Byggblock: [SaaS federerad SSO-konfiguration](#saas-federated-sso-configuration) |

### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Gå till bladet för företagsprogram i Azure AD-hanteringsportalen | [Azure AD-hanteringsportalen: Företagsprogram](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) |
| Konfigurera program från krav med självbetjäning | [Konfigurera självbetjäningsåtkomsten](manage-apps/manage-self-service-access.md) |
| Logga in som informationsarbetare till Mina appar-portalen | http://myapps.microsoft.com |
| Lägg märke till ”+ Lägg till app” knappen op på sidan. Använda den för att få åtkomst till appen |  |

### <a name="considerations"></a>Överväganden

De program som har valt kanske har etablering krav, så kommer omedelbart att appen kan orsaka fel. Om programmet valt har stöd för etablering med azure ad och den är konfigurerad, kan du använda detta som en möjlighet för att visa hela flödet arbeta från slutpunkt till slutpunkt. Se byggstenar för [SaaS federerad enkel inloggning Configuration](#saas-federated-sso-configuration) för ytterligare rekommendationer

## <a name="self-service-password-reset"></a>Lösenordsåterställning via självbetjäning

Ungefärlig tid att Slutför: 15 minuter

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| Aktivera hantering av lösenord för självbetjäning i din klient. | [Azure Active Directory-lösenordsåterställning för IT-administratörer](user-help/active-directory-passwords-update-your-own-password.md) |
| Aktivera tillbakaskrivning av lösenord att hantera lösenord från en lokal plats. Obs detta kräver viss Azure AD Connect versioner | [Krav för tillbakaskrivning av lösenord](authentication/howto-sspr-writeback.md) |
| Identifiera de PoC-användare som kommer att använda den här funktionen och kontrollera att de är medlemmar i en säkerhetsgrupp. Användarna måste vara icke-administratörer att fullständigt demonstrera funktionen | [Anpassa: Azure AD-lösenordshantering: begränsa åtkomst till återställning av lösenord](authentication/howto-sspr-writeback.md) |


### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Gå till Azure AD-hanteringsportalen: återställning av lösenord | [Azure AD-hanteringsportalen: Återställning av lösenord](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) |
| Fastställa princip för återställning av lösenordet. För POC, kan du använda telefonsamtal och frågor och svar. Vi rekommenderar att aktivera registrering som krävs på Logga in på åtkomstpanelen |  |
| Logga ut och logga in som en informationsanställd |  |
| Ange informationen som självbetjäningsportalen för lösenordsåterställning som du konfigurerade per steg 2 | https://aka.ms/ssprsetup |
| Stäng webbläsaren |  |
| Starta om inloggningen som informationsarbetare som du använde i steg 4 |  |
| Återställa lösenordet | [Uppdatera ditt eget lösenord: återställa mitt lösenord](user-help/active-directory-passwords-update-your-own-password.md) |
| Försök logga in med ditt nya lösenord till Azure AD samt att lokala resurser |  |

### <a name="considerations"></a>Överväganden

1. Om du ska uppgradera Azure AD Connect kan orsaka problem, Överväg att använda mot molnkonton eller blir en demo mot en separat miljö
2. Administratörerna har en annan princip och med administratörskontot för att återställa lösenordet kan förorena PoC och orsaka förvirring. Kontrollera att du använder ett vanligt användarkonto för att testa åtgärderna för återställning


## <a name="azure-multi-factor-authentication-with-phone-calls"></a>Azure Multi-Factor Authentication med telefonsamtal

Ungefärlig tid att Slutför: 10 minuter

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| Identifiera POC-användare som ska använda MFA  |  |
| Phone med mottagning för MFA-kontrollen  | [Vad är Azure Multi-Factor Authentication?](authentication/multi-factor-authentication.md) |

### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Navigera till ”användare och grupper”-bladet i Azure AD-hanteringsportalen | [Azure AD-hanteringsportalen: Användare och grupper](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/Overview/menuId/) |
| Välj ”alla användare”-bladet |  |
| I överst raden Välj ”Multifaktorautentisering”-knapp | Direkt-URL för Azure MFA-portalen: https://aka.ms/mfaportal |
| I inställningarna för ”användare” Välj PoC-användare och aktivera dem för MFA | [Användartillstånd i Azure Multi-Factor Authentication](authentication/howto-mfa-userstates.md) |
| Logga in som PoC användar- och genomgång av processen för proof-up  |  |

### <a name="considerations"></a>Överväganden

1. PoC-stegen i den här byggblock som uttryckligen ställa in MFA för en användare på alla inloggningar. Det finns andra verktyg, till exempel villkorlig åtkomst och identitetsskydd som engagerar MFA på fler vissa typer av situationer. Det här är något att tänka på när du flyttar från POC till produktion.
2. PoC-stegen i den här byggblock använder uttryckligen telefonsamtal som den MFA-metoden för expedience. Eftersom du övergår från POC till produktion, rekommenderar vi att använda program som de [Microsoft Authenticator](user-help/microsoft-authenticator-app-how-to.md) som din andra faktor när det är möjligt.
Läs mer: [DRAFT NIST Special Publication 800 63B](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="mfa-conditional-access-for-saas-applications"></a>MFA villkorlig åtkomst för SaaS-program

Ungefärlig tid att Slutför: 10 minuter

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| Identifiera PoC-användare för att ange principen. Dessa användare får vara i en säkerhetsgrupp för att definiera omfattningen av principen för villkorlig åtkomst | [Konfiguration för SaaS-federerad enkel inloggning](#saas-federated-sso-configuration) |
| SaaS-program har redan konfigurerats |  |
| PoC-användare har redan tilldelats till programmet |  |
| Autentiseringsuppgifter för POC-användaren är tillgängliga |  |
| POC-användaren har registrerats för MFA. Med hjälp av en telefon med mottagning | https://aka.ms/ssprsetup |
| Enhet i det interna nätverket. IP-adress som konfigurerats i det interna adressintervallet | Hitta din ip-adress: https://www.bing.com/search?q=what%27s+my+ip |
| Enhet i det externa nätverket (kan vara en telefon med hjälp av vilken operatör mobila nätverk) |  |

### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Gå till Azure AD-hanteringsportalen: bladet för villkorlig åtkomst | [Azure AD-hanteringsportalen: Villkorlig åtkomst](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) |
| Skapa princip för villkorlig åtkomst:<br/>-PoC målanvändare under ”användare och grupper”<br/>-PoC målprogrammet under ”molnappar”<br/>-Rikta alla platser utom betrodda som ”villkor” -> ”platser” **Obs:** tillförlitliga IP-adresser har konfigurerats i [MFA-portalen](https://account.activedirectory.windowsazure.com/UserManagement/MfaSettings.aspx)<br/>-Kräva multifaktorautentisering under ”bidrag” | [Skapa principer för villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-mfa#create-your-conditional-access-policy) |
| Åtkomst till programmet från inuti företagsnätverket | [Testa din princip för villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-mfa#test-your-conditional-access-policy) |
| Åtkomst till programmet från offentligt nätverk | [Testa din princip för villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-mfa#test-your-conditional-access-policy) |

### <a name="considerations"></a>Överväganden

Om du använder federation, kan du använda en lokal identitetsprovider (IdP) att kommunicera inuti/utanför företagsnätverket tillstånd med anspråk. Du kan använda den här tekniken utan att behöva hantera listan över IP-adresser som kan vara komplexa för att utvärdera och hantera i stora organisationer. Du behöver i att inställningarna för ”network centrala”-scenariot (en användare som loggar från det interna nätverket och samtidigt inloggade växlar platser, till exempel ett kafé) och kontrollera att du förstår följderna. Läs mer: [skydda molnresurser med Azure Multi-Factor Authentication och AD FS: tillförlitliga IP-adresser för federerade användare](authentication/howto-mfa-adfs.md#trusted-ips-for-federated-users)

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

Ungefärlig tid att Slutför: 15 minuter

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| Identifiera den globala administratören som ska ingå i POC för PIM | [Börja använda Azure AD Privileged Identity Management](privileged-identity-management/pim-getting-started.md) |
| Identifiera den globala administratören som ska bli säkerhetsadministratören | [Börja använda Azure AD Privileged Identity Management](privileged-identity-management/pim-getting-started.md)<br/> [Olika administrativa roller i Azure Active Directory-PIM](privileged-identity-management/pim-roles.md) |
| Valfritt: Bekräfta om de globala administratörerna har e-poståtkomst att e-postmeddelanden i PIM | [Vad är Azure AD Privileged Identity Management?: Konfigurera aktivering rollinställningar](privileged-identity-management/pim-configure.md#configure-the-role-activation-settings)


### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Logga in på https://portal.azure.com som en global administratör (GA) och bootstrap PIM-bladet. Den globala administratören som utför det här steget har angetts som security-administratör.  Vi kan kalla den här aktören GA1 | [Guiden Säkerhet i Azure AD Privileged Identity Management](privileged-identity-management/pim-security-wizard.md) |
| Identifiera den globala administratören och flytta dem från permanent till berättigade. Det bör vara en administratör som är separat från den som används i steg 1 för tydlighetens skull. Vi kan kalla den här aktören GA2 | [Azure AD Privileged Identity Management: Så här lägger du till eller ta bort en användarroll](privileged-identity-management/pim-how-to-add-role-to-user.md)<br/>[Vad är Azure AD Privileged Identity Management?: Konfigurera aktivering rollinställningar](privileged-identity-management/pim-configure.md#configure-the-role-activation-settings)  |
| Nu kan logga in som GA2 till https://portal.azure.com och försök att ändra ”användarinställningar”. Observera att vissa alternativ är nedtonade. | |
| I en ny flik och i samma session som steg 3, navigera nu till https://portal.azure.com och lägga till PIM-bladet på instrumentpanelen. | [Börja använda PIM](privileged-identity-management/pim-getting-started.md) |
| Begäran om aktivering till rollen som Global administratör | [Aktivera eller inaktivera roller i Azure AD Privileged Identity Management: aktivera en roll](privileged-identity-management/pim-how-to-activate-role.md#activate-a-role) |
| Observera att om GA2 aldrig registrerat dig för MFA, registrering för Azure MFA ska vara nödvändigt |  |
| Gå tillbaka till den ursprungliga fliken i steg 3 och klicka på Uppdatera i webbläsaren. Observera att du nu har behörighet att ändra ”användarinställningar” | |
| Om dina globala administratörer har e-postaktiverad kan du eventuellt se GA1 och GA2's inkorg och meddelandet i rollen som aktiveras |  |
| 8 Kontrollera granskningshistoriken och se rapporten för att bekräfta utökas GA2 visas. | [Vad är Azure AD Privileged Identity Management?: rollen granskningsaktivitet](privileged-identity-management/pim-configure.md#review-role-activity) |

### <a name="considerations"></a>Överväganden

Den här funktionen är en del av Azure AD Premium P2 och/eller EMS E5

## <a name="discovering-risk-events"></a>Upptäck riskhändelser

Ungefärlig tid att Slutför: 20 minuter

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| Enhet med Tor webbläsare hämtas och installeras | [Hämta Tor webbläsare](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Åtkomst till POC användare göra inloggningen | [Azure Active Directory Identity Protection spelbok](identity-protection/playbook.md) |

### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Öppna tor webbläsare | [Hämta Tor webbläsare](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Logga in på https://myapps.microsoft.com med POC-användarkonto | [Azure Active Directory Identity Protection-strategibok: simulera riskhändelser](identity-protection/playbook.md#simulating-risk-events) |
| Vänta 5 – 7 minuter |  |
| Logga in som global administratör för att https://portal.azure.com och öppna bladet Identity Protection | https://aka.ms/aadipgetstarted |
| Öppna bladet risk händelser. Du bör se en post under ”inloggningar från anonyma IP-adresser”  | [Azure Active Directory Identity Protection-strategibok: simulera riskhändelser](identity-protection/playbook.md#simulating-risk-events) |

### <a name="considerations"></a>Överväganden

Den här funktionen är en del av Azure AD Premium P2 och/eller EMS E5

## <a name="deploying-sign-in-risk-policies"></a>Distribuera principer för åtkomstrisk inloggning

Ungefärlig tid att Slutför: 10 minuter

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| Enhet med Tor webbläsare hämtas och installeras | [Hämta Tor webbläsare](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Åtkomst som en POC-användare att göra loggen i testning |  |
| POC-användaren har registrerats med MFA. Se till att använda en telefon med mottagning | Byggblock: [Azure Multi-Factor Authentication med telefonsamtal](#azure-multi-factor-authentication-with-phone-calls) |


### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Logga in som global administratör för att https://portal.azure.com och öppna bladet Identity Protection | https://aka.ms/aadipgetstarted |
| Aktivera en princip för inloggningsrisk på följande sätt:<br/>-Tilldelade till: POC-användare<br/>-Villkor: Inloggningsrisk medelstora eller högre (inloggning från anonym plats anses som en Medelrisk-nivå)<br/>-Kontroller: Kräva MFA | [Azure Active Directory Identity Protection-strategibok: inloggningsrisk](identity-protection/playbook.md) |
| Öppna tor webbläsare | [Hämta Tor webbläsare](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Logga in på https://myapps.microsoft.com med PoC-användarkonto |  |
| Lägg märke till MFA-kontrollen | [Logga in som inträffar med Azure AD Identity Protection: riskfylld inloggning återställning](identity-protection/flows.md#risky-sign-in-recovery)

### <a name="considerations"></a>Överväganden

Den här funktionen är en del av Azure AD Premium P2 och/eller EMS E5. Läs mer om riskhändelser besök: [Azure Active Directory-riskhändelser](reports-monitoring/concept-risk-events.md)

## <a name="configuring-certificate-based-authentication"></a>Konfigurera certifikatbaserad autentisering

Ungefärlig tid att slutföra: 20 minuter

### <a name="pre-requisites"></a>Förutsättningar

| Förhandskrav | Resurser |
| --- | --- |
| Enhet med användarcertifikat etablerats (Windows, iOS eller Android) från Företags-PKI | [Distribuera användarcertifikat](https://msdn.microsoft.com/library/cc770857.aspx) |
| Azure AD-domän som är federerad med AD FS | [Azure AD Connect och federation](hybrid/how-to-connect-fed-whatis.md)<br/>[Översikt över Active Directory Certificate Services](https://technet.microsoft.com/library/hh831740.aspx)|
| För iOS-enheter har installerat Microsoft Authenticator-appen | [Kom igång med Microsoft Authenticator-appen](user-help/microsoft-authenticator-app-how-to.md) |

### <a name="steps"></a>Steg

| Steg | Resurser |
| --- | --- |
| Aktivera ”autentisering med användarcertifikat” på AD FS | [Konfigurera autentiseringsprinciper: Konfigurera globalt primär autentisering i Windows Server 2012 R2](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-authentication-policies#to-configure-primary-authentication-globally-in-windows-server-2012-r2) |
| Valfritt: Aktivera autentisering i Azure AD för Exchange Active Sync-klienter | [Komma igång med certifikatbaserad autentisering i Azure Active Directory](./authentication/active-directory-certificate-based-authentication-get-started.md) |
| Gå till åtkomstpanelen och autentisera med hjälp av användarcertifikat | https://myapps.microsoft.com |

### <a name="considerations"></a>Överväganden

Läs mer om varningar av den här distributionen finns: [ADFS: autentisering med Azure AD & Office 365](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/)



> [!NOTE]
> Tillgång användarcertifikat bör skyddas. Antingen genom att hantera enheter eller med PIN-kod vid smartkort.



[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]
