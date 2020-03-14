---
title: Enkel inloggning till program – Azure Active Directory | Microsoft Docs
description: Lär dig mer om att välja en metod för enkel inloggning när du konfigurerar program i Azure Active Directory (AD Azure). Använd enkel inloggning så att användarna inte behöver komma ihåg lösenorden för alla program och för att förenkla administrationen av kontohantering.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: mimart
ms.reviewer: arvindh, japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: f46bcf412403d8f911e484e12a9d1f421b1666f0
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2020
ms.locfileid: "79366078"
---
# <a name="single-sign-on-to-applications-in-azure-active-directory"></a>Enkel inloggning till program i Azure Active Directory

Enkel inloggning (SSO) ger säkerhet och bekvämlighet när användare loggar in på program i Azure Active Directory (Azure AD). I den här artikeln beskrivs metoder för enkel inloggning och det hjälper dig att välja den lämpligaste SSO-metoden när du konfigurerar dina program.

- **Med enkel inloggning**loggar användare in en gång med ett konto för att komma åt domänanslutna enheter, företags resurser, SaaS-program (program vara som en tjänst) och webb program. Efter inloggningen kan användaren starta program från Office 365-portalen eller Azure AD MyApps åtkomstpanelen. Administratörer kan centralisera hantering av användarkonton, och automatiskt lägga till eller ta bort åtkomst till program baserat på gruppmedlemskap.

- **Utan enkel inloggning**måste användare komma ihåg programspecifika lösen ord och logga in på varje program. IT-personal måste skapa och uppdatera användarkonton för varje program som Office 365, Box och Salesforce. Användare måste komma ihåg sina lösenord, samt lägga tid att logga in på varje program.

## <a name="choosing-a-single-sign-on-method"></a>Välja en metod för enkel inloggning

Det finns flera sätt att konfigurera ett program för enkel inloggning. Att välja en enkel inloggnings metod beror på hur programmet är konfigurerat för autentisering.

- Moln program kan använda OpenID Connect, OAuth, SAML, Password-based, Linked eller disabled för enkel inloggning. 
- Lokala program kan använda lösenordsbaserad och integrerad Windows-autentisering, rubrikbaserad, länkade eller har inaktiverad metoder för enkel inloggning. Lokala alternativ fungerar när program har konfigurerats för Application Proxy.

Det här flödesschemat hjälper dig att bestämma vilken metod för enkel inloggning är bäst för just din situation.

![Besluts flödes schema för enkel inloggnings metod](./media/what-is-single-sign-on/choose-single-sign-on-method-040419.png)

I följande tabell sammanfattas metoderna som enkel inloggning och länkar till mer information.

| Metod för enkel inloggning | Programtyper | När du ska använda detta |
| :------ | :------- | :----- |
| [OpenID Connect och OAuth](#openid-connect-and-oauth) | endast moln | Använd OpenID Connect och OAuth när du utvecklar ett nytt program. Det här protokollet fören klar program konfigurationen, har lättanvända SDK: er och gör att ditt program kan använda MS Graph.
| [SAML](#saml-sso) | molnet och lokalt | Välj SAML när det är möjligt för befintliga program som inte använder OpenID Connect eller OAuth. SAML fungerar för program som autentiserar med ett av SAML-protokollen.|
| [Lösenordsbaserade](#password-based-sso) | molnet och lokalt | Välj Password-based när programmet autentiserar med användar namn och lösen ord. Lösenordsbaserad enkel inloggning kan du säkert program lösenordslagring och replay med ett webbläsartillägg eller mobilapp. Den här metoden använder den befintliga inloggningsprocessen tillhandahålls av programmet, men låter en administratör hantera lösenorden. |
| [Länkade](#linked-sign-on) | molnet och lokalt | Välj länkad inloggning när programmet har kon figurer ATS för enkel inloggning i en annan Identity Provider-tjänst. Det här alternativet inte lägga till enkel inloggning till programmet. Programmet kan redan ha enkel inloggning implementeras med hjälp av en annan tjänst, till exempel Active Directory Federation Services.|
| [Inaktiverad](#disabled-sso) | molnet och lokalt | Välj inaktive rad enkel inloggning när appen inte är redo att konfigureras för enkel inloggning. Det här läget är standard när du skapar appen.|
| [Integrerad Windows-autentisering (IWA)](#integrated-windows-authentication-iwa-sso) | endast lokalt | Välj IWA enkel inloggning för program som använder [integrerad Windows-autentisering (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)eller anspråks känsliga program. För IWA använder Application Proxy-kopplingarna Kerberos-begränsad delegering (KCD) för att autentisera användare till programmet. |
| [Rubrik baserad](#header-based-sso) | endast lokalt | Använd rubrikbaserad enkel inloggning när programmet använder rubriker för autentisering. För rubrik-baserad enkel inloggning krävs PingAccess för Azure AD. Programproxy använder Azure AD för att autentisera användaren och sedan skickar trafik via kopplingstjänsten.  |

## <a name="openid-connect-and-oauth"></a>OpenID Connect och OAuth

När du utvecklar nya program bör du använda moderna protokoll som OpenID Connect och OAuth för att uppnå bästa möjliga inloggnings upplevelse för din app över flera plattformar. Med OAuth kan användare eller administratörer [bevilja medgivande](configure-user-consent.md) för skyddade resurser som [Microsoft Graph](/graph/overview). Vi ger dig enkel att använda [SDK](../develop/reference-v2-libraries.md) : er för din app och dessutom är din app redo att använda [Microsoft Graph](/graph/overview).

Mer information finns i:

- [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)
- [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)
- [Microsoft Identity Platform Developer-Guide](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

## <a name="saml-sso"></a>SAML SSO

Med **enkel inloggning i SAML**autentiserar Azure AD programmet med hjälp av användarens Azure AD-konto. Azure AD kommunicerar information inloggning till programmet via en anslutningsprotokoll. Med SAML-baserad enkel inloggning kan du mappa användare till specifika program roller baserat på regler som du definierar i dina SAML-anspråk.

Välj SAML-baserad enkel inloggning när programmet har stöd för det.

SAML-baserad enkel inloggning stöds för program som använder någon av dessa protokoll:

- SAML 2.0
- WS-Federation

Information om hur du konfigurerar ett SaaS-program för SAML-baserad enkel inloggning finns i [Konfigurera SAML-baserad enkel inloggning](configure-single-sign-on-non-gallery-applications.md). Många program vara som en tjänst (SaaS) har också en [programspecifik självstudie](../saas-apps/tutorial-list.md) som steg för steg tar dig igenom konfigurationen för SAML-baserad enkel inloggning.

Om du vill konfigurera ett program för WS-Federation följer du samma anvisningar för att konfigurera program för SAML-baserad enkel inloggning. mer information finns i [Konfigurera SAML-baserad enkel inloggning](configure-single-sign-on-non-gallery-applications.md). I steget för att konfigurera programmet till att använda Azure AD måste du ersätta inloggnings-URL: en för Azure AD för slut punkten för WS-Federation-`https://login.microsoftonline.com/<tenant-ID>/wsfed`.

Om du vill konfigurera ett lokalt program för SAML-baserad enkel inloggning, se [SAML enkel inloggning för lokala program med Application Proxy](application-proxy-configure-single-sign-on-on-premises-apps.md).

Mer information om SAML-protokollet finns i [SAML-protokoll för enkel inloggning](../develop/single-sign-on-saml-protocol.md).

## <a name="password-based-sso"></a>Lösenordsbaserad SSO

Vid lösenordsbaserad inloggning loggas användarna in på programmet med ett användar namn och lösen ord första gången de använder den. Efter den första inloggningen skickar Azure AD användar namn och lösen ord till programmet.

Lösenordsbaserad enkel inloggning använder du den befintliga autentiseringsprocessen som tillhandahålls av programmet. När du aktiverar lösenord för enkel inloggning för ett program, Azure AD samlar in och lagrar på ett säkert sätt användarnamn och lösenord för programmet. Användarens autentiseringsuppgifter lagras i ett krypterat tillstånd i katalogen.

Välj lösenordsbaserad enkel inloggning när:

- Ett program har inte stöd för SAML-protokoll för enkel inloggning.
- Ett program som autentiseras med ett användarnamn och lösenord i stället för åtkomst-token och rubriker.

Lösenordsbaserad enkel inloggning har stöd för alla molnbaserade program som har en HTML-baserad på inloggningssidan. Användaren kan använda någon av följande webbläsare:

- Internet Explorer 11 på Windows 7 eller senare
   > [!NOTE]
   > Internet Explorer har begränsad support och tar inte längre emot nya program uppdateringar. Microsoft Edge är den rekommenderade webbläsaren.

- Microsoft Edge på Windows 10-jubileums version eller senare
- Microsoft Edge för iOS och Android
- Intune Managed Browser
- Chrome på Windows 7 eller senare, och i Mac OS X eller senare
- Firefox 26.0 eller senare på Windows XP SP2 eller senare, och på Mac OS X 10.6 eller senare

Information om hur du konfigurerar ett moln program för lösenordsbaserad enkel inloggning finns i [Konfigurera enkel inloggning för lösen ord](configure-password-single-sign-on-non-gallery-applications.md).

Om du vill konfigurera ett lokalt program för enkel inloggning via Application Proxy, se [lösen ords valv för enkel inloggning med programproxy](application-proxy-configure-single-sign-on-password-vaulting.md)

### <a name="how-authentication-works-for-password-based-sso"></a>Hur autentisering fungerar med lösenordsbaserad SSO

För att autentisera en användare i ett program hämtar Azure AD användarens autentiseringsuppgifter från katalogen och anger dem i programmets inloggnings sida.  Azure AD skickar autentiseringsuppgifter för användare på ett säkert sätt via ett webbläsartillägg eller mobilapp. Den här processen kan administratören hantera autentiseringsuppgifter för användare och behöver inte användare att komma ihåg lösenordet.

> [!IMPORTANT]
> Autentiseringsuppgifterna är fördunklade från användaren under den automatiserade inloggnings processen. Autentiseringsuppgifterna är dock identifierbart med hjälp av web felsökningsverktyg. Användare och administratörer måste följa samma säkerhetsprinciper som om autentiseringsuppgifterna angavs direkt av användaren.

### <a name="managing-credentials-for-password-based-sso"></a>Hantera autentiseringsuppgifter för lösenordsbaserad SSO

Lösenord för varje program kan antingen hanteras av Azure AD-administratör eller av användare.

När Azure AD-administratör hanterar autentiseringsuppgifter:  

- Användaren behöver inte återställa eller komma ihåg det användarnamn och lösenord. Användaren kan komma åt programmet genom att klicka på den på sina åtkomstpaneler eller via en angiven länk.
- Administratören kan göra hanteringsuppgifter på autentiseringsuppgifterna. Administratören kan till exempel uppdatera programåtkomst enligt gruppmedlemskap och anställningsstatus.
- Administratören kan använda administrativa autentiseringsuppgifter för att ge åtkomst till program som delas mellan många användare. Administratören kan till exempel tillåta alla som har åtkomst till ett program ska ha åtkomst till en sociala medier eller dokumentdelning program.

När användaren hanterar autentiseringsuppgifter:

- Användarna kan hantera sina lösenord genom att uppdatera eller ta bort dem efter behov.
- Administratörer kan fortfarande ange nya autentiseringsuppgifter för programmet.

## <a name="linked-sign-on"></a>Länkad inloggning
Länkad inloggning gör att Azure AD att tillhandahålla enkel inloggning till ett program som redan är konfigurerad för enkel inloggning i en annan tjänst. Länkade programmet kan visas för slutanvändare i Office 365-portalen eller Azure AD MyApps-portalen. En användare kan till exempel starta ett program som har konfigurerats för enkel inloggning i Active Directory Federation Services 2.0 (AD FS) från Office 365-portalen. Ytterligare reporting är också tillgängligt för länkade program som startas från Office 365-portalen eller Azure AD MyApps-portalen. Information om hur du konfigurerar ett program för länkad inloggning finns i [Konfigurera länkad inloggning](configure-linked-sign-on.md).

### <a name="linked-sign-on-for-application-migration"></a>Länkad inloggning för programmigrering

Länkad inloggning kan ge en konsekvent användar upplevelse när du migrerar program under en viss tids period. Om du migrerar program till Azure Active Directory kan du använda länkad inloggning för att snabbt publicera länkar till alla program som du vill migrera.  Användarna kan hitta alla länkar i portalen för mina [appar](../user-help/active-directory-saas-access-panel-introduction.md) eller [Office 365-programmet](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a). Användare känner inte till att de får åtkomst till ett länkat program eller ett migrerat program.  

När en användare har autentiserats med ett länkade program, måste en kontopost skapas innan användaren ges åtkomst för enkel inloggning. Etablera den här kontopost kan antingen ske automatiskt, eller det kan ske manuellt av en administratör.

## <a name="disabled-sso"></a>Inaktiverad SSO

Inaktiverat läge innebär att enkel inloggning inte används för programmet. När enkel inloggning har inaktiverats kan kan användarna behöva autentisera två gånger. Först användare autentisera till Azure AD och sedan logga in till programmet.

Använd inaktiverat läge för enkel inloggning:

- Om du inte är redo att integrera programmet med Azure AD enkel inloggning, eller
- Om du testar andra delar av programmet, eller
- Som ett säkerhetslager till ett lokalt program som inte kräver att användarna ska autentisera. Användaren måste autentisera med inaktiveras.

Observera att om du har konfigurerat programmet för SP-initierad SAML-baserad enkel inloggning och du ändrar SSO-läget så att det inte hindrar användare från att logga in på programmet utanför portalen för Mina appar. För att uppnå detta måste du [inaktivera möjligheten för användare att logga](disable-user-sign-in-portal.md) in

## <a name="integrated-windows-authentication-iwa-sso"></a>Integrerad Windows-autentisering (IWA) SSO

[Application Proxy](application-proxy.md) tillhandahåller enkel inloggning (SSO) till program som använder [integrerad Windows-autentisering (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)eller anspråksmedvetna program. Om programmet använder IWA, autentiserar Application Proxy för programmet med hjälp av Kerberos-begränsad delegering (KCD). Fungerar eftersom användaren redan har autentiserats med hjälp av Azure AD enkel inloggning för ett anspråksmedvetet program som litar på Azure Active Directory.

Välj läge för integrerad Windows-autentisering för enkel inloggning för att tillhandahålla enkel inloggning till en lokal app som autentiserar med IWA.

Om du vill konfigurera en lokal app för IWA, se [Kerberos-begränsad delegering för enkel inloggning till dina program med Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md).

### <a name="how-single-sign-on-with-kcd-works"></a>Hur enkel inloggning med KCD fungerar
Det här diagrammet beskriver flödet när en användare ansluter till ett lokalt program som använder IWA.

![Flödes diagram för Microsoft Azure AD-autentisering](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. Användaren anger URL: en för att komma åt den lokala appen via programproxyn.
1. Programproxyn omdirigerar begäran till Azure AD-autentiseringstjänster till preauthenticate. Nu kan gäller Azure AD alla tillämpliga autentisering och auktoriseringsprinciper, till exempel multifaktorautentisering. Om användaren har verifierats, Azure AD skapar en token och skickar det till användaren.
1. Användaren skickar token till Application Proxy.
1. Programproxyn verifierar token och hämtar User Principal Name (UPN) från token. Den skickar sedan begäran, UPN och Service Principal Name (SPN) till anslutningstjänsten via en autentiserad dually säker kanal.
1. I anslutnings tjänsten används KCD-förhandling (Kerberos-begränsad delegering) med lokal AD, som personifierar användaren för att hämta en Kerberos-token till programmet.
1. Active Directory skickar Kerberos-token för programmet till anslutningstjänsten.
1. Kopplingen skickar den ursprungliga begäran till programservern som använder Kerberos-token som togs emot från AD.
1. Programmet skickar svaret till anslutningstjänsten, som sedan returneras till Application Proxy-tjänsten och slutligen till användaren.

## <a name="header-based-sso"></a>Huvud-baserad enkel inloggning

Huvud-baserad enkel inloggning fungerar för program som använder HTTP-huvuden för autentisering. Den här inloggnings-metoden använder en tjänst för autentiseringsmetoder från tredje part som kallas PingAccess. En användare behöver bara autentisera till Azure AD.

Välj sidhuvud-baserad enkel inloggning när programproxyn och PingAccess har kon figurer ATS för programmet.

Om du vill konfigurera en huvud-baserad autentisering, se [rubrikbaserade autentisering för enkel inloggning med programproxy](application-proxy-configure-single-sign-on-with-ping-access.md).

### <a name="what-is-pingaccess-for-azure-ad"></a>Vad är PingAccess för Azure AD?

Med hjälp av PingAccess för Azure AD, användare kan åtkomst och enkel inloggning till program som använder rubriker för autentisering. Application Proxy behandlar programmen som någon annan, med Azure AD för att autentisera åtkomst och sedan skicka trafik via kopplingstjänsten. När autentiseringen sker översätter PingAccess-tjänsten Azure AD-åtkomsttoken till ett format för sidhuvud som skickas till programmet.

Användarna ser inte något annorlunda när de loggar in att använda företagets program. De kan fortsätta att arbeta från var som helst på valfri enhet. Programproxyanslutningar dirigera remote trafik till alla program och de fortsätter att belastningsutjämna automatiskt.

### <a name="how-do-i-get-a-license-for-pingaccess"></a>Hur får jag en licens för PingAccess?

Eftersom det här scenariot erbjuds via ett partnerskap mellan Azure AD och PingAccess, behöver du licenser för båda tjänsterna. Azure AD Premium prenumerationer innehåller dock en grundläggande PingAccess-licens som omfattar upp till 20 program. Om du vill publicera fler än 20 rubrikbaserad program kan skaffa du ytterligare en licens från PingAccess.

Mer information finns i [Azure Active Directory-versioner](../fundamentals/active-directory-whatis.md).

## <a name="related-articles"></a>Relaterade artiklar
* [Självstudier för att integrera SaaS-appar med Azure Active Directory](../saas-apps/tutorial-list.md)
* [Konfigurera SAML-baserad enkel inloggning](configure-single-sign-on-non-gallery-applications.md)
* [Konfigurera lösenordsbaserad enkel inloggning](configure-password-single-sign-on-non-gallery-applications.md)
* [Konfigurera länkad inloggning](configure-linked-sign-on.md)
* [Introduktion till hantering av åtkomst till program](what-is-access-management.md)
* Nedladdnings länk: [distributions plan för enkel inloggning](https://aka.ms/SSODeploymentPlan).
