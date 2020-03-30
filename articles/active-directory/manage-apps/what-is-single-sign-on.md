---
title: Enkel inloggning till program – Azure Active Directory | Microsoft-dokument
description: Lär dig hur du väljer en enda inloggningsmetod när du konfigurerar program i Azure Active Directory (Azure AD). Använd enkel inloggning så att användarna inte behöver komma ihåg lösenord för varje program och för att förenkla administrationen av kontohantering.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366078"
---
# <a name="single-sign-on-to-applications-in-azure-active-directory"></a>Enkel inloggning till program i Azure Active Directory

Enkel inloggning (SSO) lägger till säkerhet och bekvämlighet när användare loggar in på program i Azure Active Directory (Azure AD). I den här artikeln beskrivs de enskilda inloggningsmetoderna och du kan välja den lämpligaste SSO-metoden när du konfigurerar dina program.

- **Med enkel inloggning**loggar användarna in en gång med ett konto för att komma åt domänanslutna enheter, företagsresurser, programvara som en tjänst (SaaS) och webbprogram. När du har loggat in kan användaren starta program från Office 365-portalen eller åtkomstpanelen för Azure AD MyApps. Administratörer kan centralisera hantering av användarkonton och automatiskt lägga till eller ta bort användaråtkomst till program baserat på gruppmedlemskap.

- **Utan enkel inloggning**måste användarna komma ihåg programspecifika lösenord och logga in på varje program. IT-personal måste skapa och uppdatera användarkonton för varje program som Office 365, Box och Salesforce. Användarna måste komma ihåg sina lösenord, plus spendera tid att logga in på varje program.

## <a name="choosing-a-single-sign-on-method"></a>Välja en enda inloggningsmetod

Det finns flera sätt att konfigurera ett program för enkel inloggning. Att välja en enda inloggningsmetod beror på hur programmet är konfigurerat för autentisering.

- Molnprogram kan använda OpenID Connect, OAuth, SAML, lösenordsbaserade, länkade eller inaktiverade metoder för enkel inloggning. 
- Lokala program kan använda lösenordsbaserade, integrerad Windows-autentisering, rubrikbaserade, länkade eller inaktiverade metoder för enkel inloggning. De lokala alternativen fungerar när program konfigureras för programproxy.

Detta flödesschema hjälper dig att avgöra vilken enda inloggningsmetod som är bäst för din situation.

![Beslutsflödesschema för enkel inloggningsmetod](./media/what-is-single-sign-on/choose-single-sign-on-method-040419.png)

I följande tabell sammanfattas de enskilda inloggningsmetoderna och länkar till mer information.

| Enkel inloggningsmetod | Programtyper | När du ska använda detta |
| :------ | :------- | :----- |
| [OpenID Connect och OAuth](#openid-connect-and-oauth) | endast molnet | Använd OpenID Connect och OAuth när du utvecklar ett nytt program. Det här protokollet förenklar programkonfigurationen, har lättanvända SDK:er och gör det möjligt för ditt program att använda MS Graph.
| [SAML](#saml-sso) | moln och lokalt | Välj SAML när det är möjligt för befintliga program som inte använder OpenID Connect eller OAuth. SAML fungerar för program som autentiserar med något av SAML-protokollen.|
| [Lösenordsbaserad](#password-based-sso) | moln och lokalt | Välj lösenordsbaserad när programmet autentiseras med användarnamn och lösenord. Lösenordsbaserad enkel inloggning möjliggör säker lagring av programlösenord och spelas upp igen med ett webbläsartillägg eller en mobilapp. Den här metoden använder den befintliga inloggningsprocessen som tillhandahålls av programmet, men gör det möjligt för en administratör att hantera lösenorden. |
| [Länkade](#linked-sign-on) | moln och lokalt | Välj länkad inloggning när programmet är konfigurerat för enkel inloggning i en annan identitetsprovidertjänst. Det här alternativet lägger inte till enkel inloggning i programmet. Programmet kanske redan har en enda inloggning implementerad med en annan tjänst, till exempel Active Directory Federation Services.|
| [Disabled](#disabled-sso) | moln och lokalt | Välj inaktiverad enkel inloggning när appen inte är redo att konfigureras för enkel inloggning. Det här läget är standard när du skapar appen.|
| [Integrerad Windows-autentisering (IWA)](#integrated-windows-authentication-iwa-sso) | endast lokalt | Välj IWA enkel inloggning för program som använder [integrerad Windows-autentisering (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)eller anspråksmedvetna program. För IWA använder Application Proxy-kopplingarNa Kerberos Constrained Delegation (KCD) för att autentisera användare till programmet. |
| [Rubrikbaserad](#header-based-sso) | endast lokalt | Använd rubrikbaserad enkel inloggning när programmet använder rubriker för autentisering. Rubrikbaserad enkel inloggning kräver PingAccess för Azure AD. Programproxy använder Azure AD för att autentisera användaren och sedan skickar trafik via anslutningstjänsten.  |

## <a name="openid-connect-and-oauth"></a>OpenID Connect och OAuth

När du utvecklar nya program kan du använda moderna protokoll som OpenID Connect och OAuth för att uppnå den bästa enskilda inloggningsupplevelsen för din app på flera enhetsplattformar. OAuth gör det möjligt för användare eller administratörer att [bevilja samtycke](configure-user-consent.md) för skyddade resurser som [Microsoft Graph](/graph/overview). Vi erbjuder enkla att använda [SDK:er](../develop/reference-v2-libraries.md) för din app, och dessutom är din app redo att använda [Microsoft Graph](/graph/overview).

Mer information finns i:

- [OAuth 2,0](../develop/v2-oauth2-auth-code-flow.md)
- [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)
- [Guiden för utvecklaren av Microsoft-identitetsplattform](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

## <a name="saml-sso"></a>SAML SSO

Med **SAML enkel inloggning**autentiserar Azure AD till programmet med hjälp av användarens Azure AD-konto. Azure AD kommunicerar inloggningsinformationen till programmet via ett anslutningsprotokoll. Med SAML-baserad enkel inloggning kan du mappa användare till specifika programroller baserat på regler som du definierar i dina SAML-anspråk.

Välj SAML-baserad enkel inloggning när programmet stöder det.

SAML-baserad enkel inloggning stöds för program som använder något av dessa protokoll:

- SAML 2.0
- WS-Federation

Mer om du vill konfigurera ett SaaS-program för SAML-baserad enkel inloggning finns i [Konfigurera SAML-baserad enkel inloggning](configure-single-sign-on-non-gallery-applications.md). Dessutom har många SaaS-program (Software as a Service) en [programspecifik självstudiekurs](../saas-apps/tutorial-list.md) som hjälper dig att gå igenom konfigurationen för SAML-baserade enkel inloggning.

Information om hur du konfigurerar ett program för WS-Federation följer du samma vägledning för att konfigurera program för SAML-baserad enkel inloggning finns i [Konfigurera SAML-baserad enkel inloggning](configure-single-sign-on-non-gallery-applications.md). I steget för att konfigurera programmet så att det använder Azure AD måste du ersätta Azure `https://login.microsoftonline.com/<tenant-ID>/wsfed`AD-inloggnings-URL:en för WS-Federation-slutpunkten .

Om du vill konfigurera ett lokalt program för SAML-baserad enkel inloggning finns i [SAML-enkel inloggning för lokala program med Application Proxy](application-proxy-configure-single-sign-on-on-premises-apps.md).

Mer information om SAML-protokollet finns i [SAML-protokollet för enkel inloggning](../develop/single-sign-on-saml-protocol.md).

## <a name="password-based-sso"></a>Lösenordsbaserad SSO

Med lösenordsbaserad inloggning loggar användarna in på programmet med ett användarnamn och lösenord första gången de kommer åt det. Efter den första inloggningen levererar Azure AD användarnamnet och lösenordet till programmet.

Lösenordsbaserad enkel inloggning använder den befintliga autentiseringsprocessen som tillhandahålls av programmet. När du aktiverar lösenord enkel inloggning för ett program samlar Azure AD in och lagrar användarnamn och lösenord för programmet på ett säkert sätt. Användarautentiseringsuppgifter lagras i krypterat tillstånd i katalogen.

Välj lösenordsbaserad enkel inloggning när:

- Ett program stöder inte SAML-protokollet för enkel inloggning.
- Ett program autentiserar med ett användarnamn och lösenord i stället för åtkomsttoken och rubriker.

Lösenordsbaserad enkel inloggning stöds för alla molnbaserade program som har en HTML-baserad inloggningssida. Användaren kan använda någon av följande webbläsare:

- Internet Explorer 11 på Windows 7 eller senare
   > [!NOTE]
   > Internet Explorer har begränsad support och får inte längre nya programuppdateringar. Microsoft Edge är den rekommenderade webbläsaren.

- Microsoft Edge på Windows 10 Anniversary Edition eller senare
- Microsoft Edge för iOS och Android
- Intune Managed Browser
- Chrome på Windows 7 eller senare och på MacOS X eller senare
- Firefox 26.0 eller senare i Windows XP SP2 eller senare, och på Mac OS X 10.6 eller senare

Mer om du vill konfigurera ett molnprogram för lösenordsbaserad enkel inloggning finns i [Konfigurera lösenord enkel inloggning](configure-password-single-sign-on-non-gallery-applications.md).

Om du vill konfigurera ett lokalt program för enkel inloggning via Programproxy finns i [Lösenordsvalv för enkel inloggning med programproxy](application-proxy-configure-single-sign-on-password-vaulting.md)

### <a name="how-authentication-works-for-password-based-sso"></a>Så här fungerar autentisering för lösenordsbaserad SSO

För att autentisera en användare till ett program hämtar Azure AD användarens autentiseringsuppgifter från katalogen och anger dem på programmets inloggningssida.  Azure AD skickar användarens autentiseringsuppgifter på ett säkert sätt via ett webbläsartillägg eller en mobilapp. Den här processen gör det möjligt för en administratör att hantera användaruppgifter och kräver inte att användarna kommer ihåg sitt lösenord.

> [!IMPORTANT]
> Autentiseringsuppgifterna fördunklars från användaren under den automatiska inloggningsprocessen. Autentiseringsuppgifterna kan dock identifieras med hjälp av webbfelsökningsverktyg. Användare och administratörer måste följa samma säkerhetsprinciper som om autentiseringsuppgifterna angavs direkt av användaren.

### <a name="managing-credentials-for-password-based-sso"></a>Hantera autentiseringsuppgifter för lösenordsbaserad SSO

Lösenord för varje program kan antingen hanteras av Azure AD-administratören eller av användarna.

När Azure AD-administratören hanterar autentiseringsuppgifterna:  

- Användaren behöver inte återställa eller komma ihåg användarnamnet och lösenordet. Användaren kan komma åt programmet genom att klicka på den i sin åtkomstpanel eller via en medföljande länk.
- Administratören kan utföra hanteringsuppgifter på autentiseringsuppgifterna. Administratören kan till exempel uppdatera programåtkomsten enligt medlemskap i användargrupper och medarbetarstatus.
- Administratören kan använda administrativa autentiseringsuppgifter för att ge åtkomst till program som delas mellan många användare. Administratören kan till exempel tillåta alla som kan komma åt ett program att få åtkomst till ett program för delning av sociala medier eller dokument.

När slutanvändaren hanterar autentiseringsuppgifterna:

- Användare kan hantera sina lösenord genom att uppdatera eller ta bort dem efter behov.
- Administratörer kan fortfarande ange nya autentiseringsuppgifter för programmet.

## <a name="linked-sign-on"></a>Länkad inloggning
Länkad inloggning gör det möjligt för Azure AD att tillhandahålla enkel inloggning till ett program som redan är konfigurerat för enkel inloggning i en annan tjänst. Det länkade programmet kan visas för slutanvändare i Office 365-portalen eller Azure AD MyApps-portalen. En användare kan till exempel starta ett program som har konfigurerats för enkel inloggning i Active Directory Federation Services 2.0 (AD FS) från Office 365-portalen. Ytterligare rapportering är också tillgänglig för länkade program som startas från Office 365-portalen eller Azure AD MyApps-portalen. En konfiguration av ett program för länkad inloggning finns i [Konfigurera länkad inloggning](configure-linked-sign-on.md).

### <a name="linked-sign-on-for-application-migration"></a>Länkad inloggning för programmigrering

Länkad inloggning kan ge en konsekvent användarupplevelse medan du migrerar program över en viss tidsperiod. Om du migrerar program till Azure Active Directory kan du använda länkad inloggning för att snabbt publicera länkar till alla program som du tänker migrera.  Användare kan hitta alla länkar i [MyApps-portalen](../user-help/active-directory-saas-access-panel-introduction.md) eller [Office 365-programstartprogrammet](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a). Användarna vet inte att de har åtkomst till ett länkat program eller ett migrerat program.  

När en användare har autentiserats med ett länkat program måste en kontopost skapas innan slutanvändaren får enkel inloggningsåtkomst. Etablering av den här kontoposten kan antingen ske automatiskt eller så kan den ske manuellt av en administratör.

## <a name="disabled-sso"></a>Inaktiverad SSO

Inaktiverat läge innebär att enkel inloggning inte används för programmet. När enkel inloggning är inaktiverad kan användarna behöva autentisera två gånger. Först autentiserar användare till Azure AD och sedan loggar de in på programmet.

Använd inaktiverat enkel inloggningsläge:

- Om du inte är redo att integrera det här programmet med azure AD enkel inloggning, eller
- Om du testar andra aspekter av programmet, eller
- Som ett säkerhetslager till ett lokalt program som inte kräver att användare autentiserar. Med inaktiverad måste användaren autentisera.

Observera att om du har konfigurerat programmet för SP-initierad SAML-baserad enkel inloggning och du ändrar SSO-läget för att inaktivera, hindrar det inte användare från att logga in på programmet utanför MyApps-portalen. För att uppnå detta måste du [inaktivera möjligheten för användare att logga in](disable-user-sign-in-portal.md)

## <a name="integrated-windows-authentication-iwa-sso"></a>Integrerad Windows-autentisering (IWA) SSO

[Application Proxy](application-proxy.md) ger enkel inloggning (SSO) till program som använder [integrerad Windows-autentisering (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)eller anspråksmedvetna program. Om programmet använder IWA autentiserar Application Proxy till programmet med hjälp av Kerberos Constrained Delegation (KCD). För ett anspråksmedvetna program som har förtroende för Azure Active Directory fungerar enkel inloggning eftersom användaren redan autentiserats med hjälp av Azure AD.

Välj Ett enda inloggningsläge för integrerad Windows-autentisering om du vill ha enkel inloggning till en lokal app som autentiserar med IWA.

Om du vill konfigurera en lokal app för IWA läser du [Kerberos Constrained Delegation för enkel inloggning till dina program med Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md).

### <a name="how-single-sign-on-with-kcd-works"></a>Så här fungerar enkel inloggning med KCD
Det här diagrammet förklarar flödet när en användare kommer åt ett lokalt program som använder IWA.

![Flödesdiagram för Microsoft Azure AD-autentisering](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. Användaren anger URL:en för att komma åt det lokala programmet via Programproxy.
1. Programproxy omdirigerar begäran till Azure AD-autentiseringstjänster för att förauktorisera. Nu tillämpar Azure AD alla tillämpliga autentiserings- och auktoriseringsprinciper, till exempel multifaktorautentisering. Om användaren valideras skapar Azure AD en token och skickar den till användaren.
1. Användaren skickar token till Programproxy.
1. Programproxy validerar token och hämtar UPN (User Principal Name) från token. Den skickar sedan begäran, UPN och Service Principal Name (SPN) till anslutningen via en vederbörligen autentiserade säker kanal.
1. Kopplingen använder Kerberos Constrained Delegation (KCD) förhandling med den lokala AD, personifiera användaren att få en Kerberos token till programmet.
1. Active Directory skickar Kerberos-token för programmet till kopplingen.
1. Anslutningen skickar den ursprungliga begäran till programservern med den Kerberos-token som den tog emot från AD.
1. Programmet skickar svaret till anslutningen, som sedan returneras till application proxy-tjänsten och slutligen till användaren.

## <a name="header-based-sso"></a>Rubrikbaserad SSO

Rubrikbaserad enkel inloggning fungerar för program som använder HTTP-huvuden för autentisering. Den här inloggningsmetoden använder en autentiseringstjänst från tredje part som heter PingAccess. En användare behöver bara autentisera till Azure AD.

Välj rubrikbaserad enkel inloggning när Programproxy och PingAccess är konfigurerade för programmet.

Om du vill konfigurera rubrikbaserad autentisering finns i [Rubrikbaserad autentisering för enkel inloggning med Programproxy](application-proxy-configure-single-sign-on-with-ping-access.md).

### <a name="what-is-pingaccess-for-azure-ad"></a>Vad är PingAccess för Azure AD?

Med PingAccess för Azure AD kan användare komma åt och enkel inloggning till program som använder rubriker för autentisering. Programproxy behandlar dessa program som alla andra, med hjälp av Azure AD för att autentisera åtkomst och sedan överföra trafik via anslutningstjänsten. När autentiseringen har inträffat översätter PingAccess-tjänsten Azure AD-åtkomsttoken till ett huvudformat som skickas till programmet.

Användarna märker inget annat när de loggar in för att använda dina företagsprogram. De kan fortfarande fungera var som helst på vilken enhet som helst. Application Proxy-kopplingarna dirigerar fjärrtrafik till alla program och de fortsätter att belastningsutjämnas automatiskt.

### <a name="how-do-i-get-a-license-for-pingaccess"></a>Hur skaffar jag en licens för PingAccess?

Eftersom det här scenariot erbjuds genom ett partnerskap mellan Azure AD och PingAccess behöver du licenser för båda tjänsterna. Azure AD Premium-prenumerationer innehåller dock en grundläggande PingAccess-licens som täcker upp till 20 program. Om du behöver publicera fler än 20 huvudbaserade program kan du skaffa ytterligare en licens från PingAccess.

Mer information finns i [Azure Active Directory-versioner](../fundamentals/active-directory-whatis.md).

## <a name="related-articles"></a>Relaterade artiklar
* [Självstudier för att integrera SaaS-appar med Azure Active Directory](../saas-apps/tutorial-list.md)
* [Konfigurera SAML-baserad enkel inloggning](configure-single-sign-on-non-gallery-applications.md)
* [Konfigurera lösenordsbaserad enkel inloggning](configure-password-single-sign-on-non-gallery-applications.md)
* [Konfigurera länkad inloggning](configure-linked-sign-on.md)
* [Introduktion till hantering av åtkomst till program](what-is-access-management.md)
* Hämta länk: [Enkel distributionsplan för inloggning](https://aka.ms/SSODeploymentPlan).
