---
title: Enkel inloggning till program – Azure Active Directory | Microsoft Docs
description: Lär dig mer om att välja en metod för enkel inloggning när du konfigurerar program i Azure Active Directory (AD Azure). Använd enkel inloggning så att användarna inte behöver komma ihåg lösenorden för alla program och för att förenkla administrationen av kontohantering.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: celested
ms.reviewer: arvindh, japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84f1b7c9461d2eba5e13be8b15b2cbcc62715c23
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792046"
---
# <a name="single-sign-on-to-applications-in-azure-active-directory"></a>Enkel inloggning till program i Azure Active Directory
Lägger till säkerhet och bekvämlighet enkel inloggning (SSO) när användare-inloggning till program i Azure Active Directory (AD Azure). Den här artikeln beskrivs metoderna som enkel inloggning och hjälper dig att välja den lämpligaste SSO-metoden när du konfigurerar dina program.

- **Med enkel inloggning**, användare logga in en gång med ett konto för att få åtkomst till domänanslutna enheter företagets resurser, programvara som en tjänst (SaaS)-program och webbprogram. Efter inloggningen kan användaren starta program från Office 365-portalen eller Azure AD MyApps åtkomstpanelen. Administratörer kan centralisera hantering av användarkonton, och automatiskt lägga till eller ta bort åtkomst till program baserat på gruppmedlemskap. 

- **Utan enkel inloggning**, användare måste komma ihåg programspecifika lösenord och logga in på varje program. IT-personal måste skapa och uppdatera användarkonton för varje program som Office 365, Box och Salesforce. Användare måste komma ihåg sina lösenord, samt lägga tid att logga in på varje program.

## <a name="choosing-a-single-sign-on-method"></a>Välja en metod för enkel inloggning

Det finns flera sätt att konfigurera ett program för enkel inloggning. Välja en metod för enkel inloggning beror på hur programmet är konfigurerat för autentisering. 

- Molnprogram kan använda OpenID Connect, OAuth, SAML lösenordsbaserad och länkade, eller inaktiverade metoder för enkel inloggning. 
- Lokala program kan använda lösenordsbaserad och integrerad Windows-autentisering, rubrikbaserad, länkade eller har inaktiverad metoder för enkel inloggning. Lokala alternativ fungerar när program har konfigurerats för Application Proxy.

Det här flödesschemat hjälper dig att bestämma vilken metod för enkel inloggning är bäst för just din situation. 

![Välj metod för enkel inloggning](./media/what-is-single-sign-on/choose-single-sign-on-method-updated.png)

I följande tabell sammanfattas metoderna som enkel inloggning och länkar till mer information. 

| Metod för enkel inloggning | Programtyper | När du ska använda detta |
| :------ | :------- | :----- |
| [OpenID Connect och OAuth](#openid-connect-and-oauth) | Endast molnet | Använd OpenID Connect och OAuth när du utvecklar ett nytt program. Det här protokollet förenklar programkonfiguration, är enkel att använda SDK: er, vilket gör att programmet kan använda MS Graph.
| [SAML](#saml-sso) | molnet och lokalt | Välj SAML när det är möjligt för befintliga program som inte använder OpenID Connect eller OAuth. SAML fungerar för program som autentiseras med någon av SAML-protokoll.|
| [Lösenordsbaserad](#password-based-sso) | molnet och lokalt | Välj lösenordsbaserad när programmet autentiseras med användarnamn och lösenord. Lösenordsbaserad enkel inloggning kan du säkert program lösenordslagring och replay med ett webbläsartillägg eller mobilapp. Den här metoden använder den befintliga inloggningsprocessen tillhandahålls av programmet, men låter en administratör hantera lösenorden. |
| [Länkade](#linked-sso) | molnet och lokalt | Välj länkade enkel inloggning när programmet har konfigurerats för enkel inloggning i en annan identitet provider-tjänsten. Det här alternativet inte lägga till enkel inloggning till programmet. Programmet kan redan ha enkel inloggning implementeras med hjälp av en annan tjänst, till exempel Active Directory Federation Services.|
| [Inaktiverad](#disabled-sso) | molnet och lokalt | Välj inaktiverade enkel inloggning när appen inte kan konfigureras för enkel inloggning. Användare måste ange sitt användarnamn och lösenord varje gång de starta det här programmet.|
| [Integrerad Windows-autentisering (IWA)](#integrated-windows-authentication-iwa-sso) | endast lokalt | Välj IWA enkel inloggning för program som använder [integrerad Windows autentisering (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication), eller anspråksmedvetna program. För IWA använda Application Proxy-kopplingar Kerberos-begränsad delegering (KCD) för att autentisera användare till programmet. | 
| [Rubrikbaserad](#header-based-sso) | endast lokalt | Använd rubrikbaserad enkel inloggning när programmet använder rubriker för autentisering. Kräver PingAccess för Azure AD-huvud-baserad enkel inloggning. Programproxy använder Azure AD för att autentisera användaren och sedan skickar trafik via kopplingstjänsten.  | 

## <a name="openid-connect-and-oauth"></a>OpenID Connect och OAuth
När du utvecklar nya program, kan du använda moderna protokoll som OpenID Connect och OAuth för att uppnå den bästa enkel inloggning för din app på flera enhetsplattformar. OAuth gör att användare och administratörer att [ge medgivande](configure-user-consent.md) för skyddade resurser som [MS Graph](/graph/overview). Vi ger enkelt för att införa [SDK: er](../develop/reference-v2-libraries.md) för din app och dessutom din app är redo att använda [MS Graph](/graph/overview).

Mer information finns i:

- [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)
- [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)
- [Azure Active Directory – Utvecklingsguide](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

## <a name="saml-sso"></a>SAML SSO
Med **SAML enkel inloggning**, Azure AD autentiserar till programmet med hjälp av användarens Azure AD-konto. Azure AD kommunicerar information inloggning till programmet via en anslutningsprotokoll. Med SAML-baserad enkel inloggning, kan du mappa användare till specifika programroller baserat på regler som du definierar i SAML-anspråk.

Välj SAML-baserad enkel inloggning när programmet stöder den.


SAML-baserad enkel inloggning stöds för program som använder någon av dessa protokoll:

- SAML 2.0
- WS-Federation

För att konfigurera ett program för SAML-baserad enkel inloggning, se [konfigurera SAML-baserad enkel inloggning](configure-single-sign-on-portal.md). Dessutom många programvara som en tjänst (SaaS)-program som har en [programspecifika självstudien](../saas-apps/tutorial-list.md) som guida dig genom konfigurationen för SAML-baserad enkel inloggning.

Om du vill konfigurera ett program för WS-Federation, följa samma riktlinjer om du vill konfigurera program för SAML-baserad enkel inloggning finns i [konfigurera SAML-baserad enkel inloggning](configure-single-sign-on-portal.md). I steg för att konfigurera programmet att använda Azure AD, kommer du måste ersätta inloggnings-URL för Azure AD för slutpunkt för WS-Federation `https://login.microsoftonline.com/<tenant-ID>/wsfed`.

Läs mer om SAML-protokoll, [enkel inloggning SAML-protokoll](../develop/single-sign-on-saml-protocol.md).

## <a name="password-based-sso"></a>Lösenordsbaserad SSO
Med lösenordsbaserad inloggning, användare loggar in på programmet med ett användarnamn och lösenord för första gången som de har åtkomst till den. Efter den första inloggning tillhandahåller Azure AD användarnamnet och lösenordet till programmet. 

Lösenordsbaserad enkel inloggning använder du den befintliga autentiseringsprocessen som tillhandahålls av programmet. När du aktiverar lösenord för enkel inloggning för ett program, Azure AD samlar in och lagrar på ett säkert sätt användarnamn och lösenord för programmet. Användarens autentiseringsuppgifter lagras i ett krypterat tillstånd i katalogen. 

Välj lösenordsbaserad enkel inloggning när:

- Ett program stöder inte SAML enkel inloggning-protokoll.
- Ett program som autentiseras med ett användarnamn och lösenord i stället för åtkomst-token och rubriker.

Lösenordsbaserad enkel inloggning har stöd för alla molnbaserade program som har en HTML-baserad på inloggningssidan. Användaren kan använda någon av följande webbläsare:

- Internet Explorer 11 på Windows 7 eller senare
- Microsoft Edge i Windows 10 Anniversary Edition eller senare
- Chrome på Windows 7 eller senare, och i Mac OS X eller senare
- Firefox 26.0 eller senare på Windows XP SP2 eller senare, och på Mac OS X 10.6 eller senare

För att konfigurera ett molnprogram för lösenordsbaserad enkel inloggning, se [konfigurera program för lösenord för enkel inloggning](application-sign-in-problem-password-sso-gallery.md#configure-the-application-for-password-single-sign-on).

För att konfigurera ett lokalt program för enkel inloggning via programproxy Se [lösenord vaulting för enkel inloggning med programproxy](application-proxy-configure-single-sign-on-password-vaulting.md)

### <a name="how-authentication-works-for-password-based-sso"></a>Hur autentisering fungerar för lösenordsbaserad SSO

Om du vill autentisera en användare till ett program, Azure AD hämtar användarens autentiseringsuppgifter från katalogen och anger dem i programmets inloggnings-sidan.  Azure AD skickar autentiseringsuppgifter för användare på ett säkert sätt via ett webbläsartillägg eller mobilapp. Den här processen kan administratören hantera autentiseringsuppgifter för användare och behöver inte användare att komma ihåg lösenordet.

> [!IMPORTANT]
> Autentiseringsuppgifterna som har dolts från användaren under processen för automatisk inloggning. Autentiseringsuppgifterna är dock identifierbart med hjälp av web felsökningsverktyg. Användare och administratörer måste följa samma säkerhetsprinciper som om autentiseringsuppgifterna angavs direkt av användaren.

### <a name="managing-credentials-for-password-based-sso"></a>Hantera autentiseringsuppgifter för lösenordsbaserad SSO

Lösenord för varje program kan antingen hanteras av Azure AD-administratör eller av användare.

När Azure AD-administratör hanterar autentiseringsuppgifter:  

- Användaren behöver inte återställa eller komma ihåg det användarnamn och lösenord. Användaren kan komma åt programmet genom att klicka på den på sina åtkomstpaneler eller via en angiven länk.
- Administratören kan göra hanteringsuppgifter på autentiseringsuppgifterna. Administratören kan till exempel uppdatera programåtkomst enligt gruppmedlemskap och anställningsstatus.
- Administratören kan använda administrativa autentiseringsuppgifter för att ge åtkomst till program som delas mellan många användare. Administratören kan till exempel tillåta alla som har åtkomst till ett program ska ha åtkomst till en sociala medier eller dokumentdelning program.

När användaren hanterar autentiseringsuppgifter:

- Användarna kan hantera sina lösenord genom att uppdatera eller ta bort dem efter behov. 
- Administratörer kan fortfarande ange nya autentiseringsuppgifter för programmet.


## <a name="linked-sso"></a>Länkade SSO
Länkad inloggning gör att Azure AD att tillhandahålla enkel inloggning till ett program som redan är konfigurerad för enkel inloggning i en annan tjänst. Länkade programmet kan visas för slutanvändare i Office 365-portalen eller Azure AD MyApps-portalen. En användare kan till exempel starta ett program som har konfigurerats för enkel inloggning i Active Directory Federation Services 2.0 (AD FS) från Office 365-portalen. Ytterligare reporting är också tillgängligt för länkade program som startas från Office 365-portalen eller Azure AD MyApps-portalen. 

### <a name="linked-sso-for-application-migration"></a>Länkade enkel inloggning för program-migreringen

Länkade SSO kan ge användarna en enhetlig upplevelse när du migrerar program under en viss tidsperiod. Om du migrerar program till Azure Active Directory, kan du använda länkade enkel inloggning publicera länkar till alla program som du vill migrera.  Användare kan hitta alla länkar i den [MyApps-portalen](../user-help/active-directory-saas-access-panel-introduction.md) eller [startprogrammet för Office 365](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a). Användare känner inte till att de får åtkomst till ett länkat program eller ett migrerat program.  

När en användare har autentiserats med ett länkade program, måste en kontopost skapas innan användaren ges åtkomst för enkel inloggning. Etablera den här kontopost kan antingen ske automatiskt, eller det kan ske manuellt av en administratör.

## <a name="disabled-sso"></a>Inaktiverad SSO

Inaktiverat läge innebär att enkel inloggning inte används för programmet. När enkel inloggning har inaktiverats kan kan användarna behöva autentisera två gånger. Först användare autentisera till Azure AD och sedan logga in till programmet. 

Använd inaktiverat läge för enkel inloggning:

- Om du inte är redo att integrera programmet med Azure AD enkel inloggning, eller
- Om du testar andra delar av programmet, eller
- Som ett säkerhetslager till ett lokalt program som inte kräver att användarna ska autentisera. Användaren måste autentisera med inaktiveras. 

## <a name="integrated-windows-authentication-iwa-sso"></a>Integrerad Windows-autentisering (IWA) SSO

[Programproxy](application-proxy.md) tillhandahåller enkel inloggning (SSO) för program som använder [integrerad Windows autentisering (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication), eller anspråksmedvetna program. Om programmet använder IWA, autentiserar Application Proxy för programmet med hjälp av Kerberos-begränsad delegering (KCD). Fungerar eftersom användaren redan har autentiserats med hjälp av Azure AD enkel inloggning för ett anspråksmedvetet program som litar på Azure Active Directory.

Välj integrerad Windows-autentisering enkel inloggningsläge:

- Att tillhandahålla enkel inloggning till en lokal app som autentiserar med IWA. 

För att konfigurera en lokal app för IWA Se [Kerberos-begränsad delegering för enkel inloggning för dina program med Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md). 

### <a name="how-single-sign-on-with-kcd-works"></a>Hur enkel inloggning med KCD fungerar
Det här diagrammet beskriver flödet när en användare ansluter till ett lokalt program som använder IWA.

![Flödesdiagram för Microsoft AAD-autentisering](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. Användaren anger URL: en för att komma åt programmet på plats via programproxy.
2. Programproxyn omdirigerar begäran till Azure AD-autentiseringstjänster till preauthenticate. Nu kan gäller Azure AD alla tillämpliga autentisering och auktoriseringsprinciper, till exempel multifaktorautentisering. Om användaren har verifierats, Azure AD skapar en token och skickar det till användaren.
3. Användaren skickar token till Application Proxy.
4. Programproxyn verifierar token och hämtar User Principal Name (UPN) från token. Den skickar sedan begäran, UPN och Service Principal Name (SPN) till anslutningstjänsten via en autentiserad dually säker kanal.
5. Anslutningen används Kerberos-begränsad delegering (KCD)-förhandling med lokala AD, Personifiera användare för att få en Kerberos-token för programmet.
6. Active Directory skickar Kerberos-token för programmet till anslutningstjänsten.
7. Kopplingen skickar den ursprungliga begäran till programservern som använder Kerberos-token som togs emot från AD.
8. Programmet skickar svaret till anslutningstjänsten, som sedan returneras till Application Proxy-tjänsten och slutligen till användaren.

## <a name="header-based-sso"></a>Huvud-baserad enkel inloggning

Huvud-baserad enkel inloggning fungerar för program som använder HTTP-huvuden för autentisering. Den här inloggnings-metoden använder en tjänst för autentiseringsmetoder från tredje part som kallas PingAccess. En användare behöver bara autentisera till Azure AD. 

Välj rubrik-baserad enkel inloggning när:

- Application Proxy och PingAccess är konfigurerade för programmet

För att konfigurera rubrikbaserad autentisering, se [rubrikbaserad autentisering för enkel inloggning med programproxy](application-proxy-configure-single-sign-on-with-ping-access.md). 

### <a name="what-is-pingaccess-for-azure-ad"></a>Vad är PingAccess för Azure AD?

Med hjälp av PingAccess för Azure AD, användare kan åtkomst och enkel inloggning till program som använder rubriker för autentisering. Application Proxy behandlar programmen som någon annan, med Azure AD för att autentisera åtkomst och sedan skicka trafik via kopplingstjänsten. När autentiseringen sker översätter PingAccess-tjänsten Azure AD-åtkomsttoken till ett format för sidhuvud som skickas till programmet.

Användarna ser inte något annorlunda när de loggar in att använda företagets program. De kan fortsätta att arbeta från var som helst på valfri enhet. Programproxyanslutningar dirigera remote trafik till alla program och de fortsätter att belastningsutjämna automatiskt.

### <a name="how-do-i-get-a-license-for-pingaccess"></a>Hur får jag en licens för PingAccess?

Eftersom det här scenariot erbjuds via ett partnerskap mellan Azure AD och PingAccess, behöver du licenser för båda tjänsterna. Azure AD Premium-prenumerationer innehåller dock en grundläggande PingAccess-licens som omfattar upp till 20 program. Om du vill publicera fler än 20 rubrikbaserad program kan skaffa du ytterligare en licens från PingAccess. 

Mer information finns i [Azure Active Directory-versioner](../fundamentals/active-directory-whatis.md).


## <a name="related-articles"></a>Relaterade artiklar
* [Självstudier för att integrera SaaS-program med Azure Active Directory](../saas-apps/tutorial-list.md)
* [Självstudie för att konfigurera enkel inloggning](configure-single-sign-on-portal.md)
* [Introduktion till hantering av åtkomst till program](what-is-access-management.md)
* Nedladdningslänk: [Enkel inloggning distributionsplan](https://aka.ms/SSODeploymentPlan).


