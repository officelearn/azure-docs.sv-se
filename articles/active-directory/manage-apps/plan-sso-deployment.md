---
title: Planera en Azure Active Directory enkel inloggning för distribution
description: Guide som hjälper dig att planera, distribuera och hantera enkel inloggning i din organisation.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29569302d20e23c95b6508a5b58c7ed96e005885
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66499256"
---
# <a name="plan-a-single-sign-on-deployment"></a>Planera en distribution för enkel inloggning

Enkel inloggning (SSO) innebär det att komma åt alla program och resurser som en användare behöver genom att logga in med ett enda användarkonto. Med enkel inloggning, kan användare komma åt alla nödvändiga program utan att behöva autentisera en gång.

## <a name="benefits-of-sso"></a>Fördelarna med enkel inloggning

Lägger till säkerhet och bekvämlighet enkel inloggning (SSO) när användare loggar in på program i Azure Active Directory (AD Azure). 

Många organisationer förlitar sig på programvara som en tjänst (SaaS)-program, till exempel Office 365, Box och Salesforce för slutanvändarens produktivitet. Historiskt sett har IT-personal behövs för att skapa och uppdatera användarkonton i varje SaaS-program och användare som behövs för att komma ihåg ett lösenord för varje individuellt.

Azure Marketplace har över 3000 program med förintegrerade SSO-anslutningar, vilket gör det enkelt att integrera dem i din klient.

## <a name="licensing"></a>Licensiering

- **Azure AD-licensiering** -SSO för förintegrerade SaaS-program är kostnadsfria. Antalet objekt i din katalog och de funktioner som du vill distribuera kan dock kräva ytterligare licenser. En fullständig lista över licenskrav finns [Azure Active Directory-priser](https://azure.microsoft.com/pricing/details/active-directory/).
- **Licensiering** -du behöver rätt licenser för dina SaaS-program att uppfylla dina affärsbehov. Arbeta med programmets ägare att avgöra om de användare som tilldelats programmet har rätt licenser för deras roller i programmet. Om Azure AD hanterar Automatisk etablering baserat på roller, justeras de roller som tilldelats i Azure AD med antalet licenser som ägs i programmet. Felaktigt antal licenser som ägs i programmet kan leda till fel under etableringen/uppdatering av en användare.

## <a name="plan-your-sso-team"></a>Planera ditt team för enkel inloggning

- **Engagera rätt intressenter** – när teknik projekt misslyckas, det kan bero på att Felmatchade förväntningar på inverkan, resultat och ansvarsområden. Undvik dessa fallgropar [se till att du engagerande rätt intressenter](https://aka.ms/deploymentplans) och att intressenter förstå deras roller.
- **Planera kommunikation** -kommunikation är nyckeln till framgång för alla nya tjänster. Proaktivt meddela användarna om datorns hur deras upplevelse ändras när den kommer att ändras och hur du får support om de stöter på problem. Granska alternativen för [hur användare kommer åt sina SSO-aktiverade program](end-user-experiences.md), och skapa din kommunikation så att den matchar ditt val. 

## <a name="plan-your-sso-protocol"></a>Planera ditt SSO-protokoll

En SSO-implementering baserat på federation protokoll förbättrar säkerhet, tillförlitlighet, och slutanvändarna inträffar och är enklare att implementera. Många program redan är integrerade i Azure AD med [steg för steg hjälper tillgängliga](../saas-apps/tutorial-list.md). Du hittar dem på vår [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). Detaljerad information om varje metod för enkel inloggning finns i artikeln [enkel inloggning till program i Azure Active Directory](what-is-single-sign-on.md).

Det finns två huvudsakliga sätt som du kan aktivera enkel inloggning för användarna i dina appar:

- **Med federerad enkel inloggning** Azure AD autentiserar användaren till programmet genom att använda sina Azure AD-konto. Den här metoden har stöd för program som stöd för protokoll, till exempel SAML 2.0, WS-Federation och OpenID Connect, och är det bästa möjliga läget för enkel inloggning. Vi rekommenderar att du använder federerad enkel inloggning med Azure AD när ett program har stöd för den, i stället för lösenordsbaserad SSO och AD FS.

- **Med lösenordsbaserad enkel inloggning** användare loggar in på programmet med ett användarnamn och lösenord för första gången de komma åt den. Efter den första inloggning tillhandahåller Azure AD användarnamnet och lösenordet till programmet. Lösenordsbaserad enkel inloggning kan du säkert program lösenordslagring och replay med ett webbläsartillägg eller mobilapp. Det här alternativet utnyttjar den befintliga inloggningsprocessen tillhandahålls av programmet, gör att en administratör hantera lösenorden och kräver inte att användaren känner till lösenordet.

### <a name="considerations-for-federation-based-sso"></a>Överväganden för federation-baserad enkel inloggning

- **Med OpenID Connect och OAuth** – om programmet som du ansluter till stöder, använda OIDC/OAuth 2.0-metoden för att aktivera din enkel inloggning till programmet. Den här metoden kräver mindre konfiguration och gör en bättre användarupplevelse. Mer information finns i [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md), [OpenID Connect 1.0](../develop/v2-protocols-oidc.md), och [Utvecklarhandbok för Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).
- **Konfigurationer som slutpunkt för SAML-baserad enkel inloggning** -om du använder SAML dina utvecklare behöver viss information innan du konfigurerar programmet. Mer information finns i [konfigurera SAML-grundalternativ](configure-single-sign-on-portal.md).
- **Certifikathantering för SAML-baserad enkel inloggning** – när du aktiverar federerad enkel inloggning för dina program, Azure AD skapar ett certifikat som är som standard giltiga i tre år. Du kan anpassa ett sista giltighetsdatum för det certifikatet om det behövs. Kontrollera att du har processer för att förnya certifikat innan de gått ut. Mer information finns i [Azure AD hantera certifikat](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs).

### <a name="considerations-for-password-based-sso"></a>Överväganden för lösenordsbaserad SSO

Använda Azure AD för lösenordsbaserad SSO kräver distribution av ett webbläsartillägg som på ett säkert sätt hämta autentiseringsuppgifter och fylla i formulär för inloggning. Definiera en mekanism för att distribuera tillägget i stor skala med [webbläsare som stöds](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction). Alternativen är:

- [En grupprincip för Internet Explorer ](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)
- [System Center Configuration Manager (SCCM) för Internet Explorer ](https://docs.microsoft.com/sccm/core/clients/deploy/deploy-clients-to-windows-computers)
- [Användarstyrd nedladdning och konfiguration av Chrome, Firefox, Microsoft Edge eller Internet Explorer ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Mer information finns i [hur du konfigurerar lösenord för enkel inloggning](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery).

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>Samla in inloggningen formulär metadata för program som inte är i galleriet

Microsoft stöder in metadata i ett webbprogram för lösenord vaulting (samla in fälten användarnamn och lösenord). Gå till inloggnings-URL under konfigureringen av programmet för att hämta metadata för formulär. Be programägaren om exakt inloggnings-URL. Den här informationen används under inloggning, mappa autentiseringsuppgifter för Azure AD till programmet under inloggning.

Mer information finns i [vad är programåtkomst och enkel inloggning med Azure AD? – lösenordsbaserad SSO](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/).

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>Dessa metadata i formulär behöver att få tillbaka diskutrymme uppgifter

När program ändrar sina HTML-layouten, kan du behöva frigöra metadata för att justera för att ändringarna. Vanliga tecken som indikerar att HTML-layouten har förändrats är:

- Användare som rapporterar att när du klickar på programmet ”fastnar” i inloggningssidan
- Användare som rapporterar att användarnamnet eller lösenordet inte är är ifylld

#### <a name="shared-accounts"></a>Delade konton

Program med delade konton från perspektivet inloggning kan inte skiljer sig från ett galleriprogram som använder enkel inloggning med lösenord för enskilda användare. Det finns dock några ytterligare steg som krävs när du planerar och konfigurerar ett program som är avsedd att använda delade konton:

1. Arbeta med program företagsanvändare dokumenterar följande:
   1. Användare i organisationen som kommer att använda programmet
   1. Befintlig uppsättning autentiseringsuppgifter i programmet som är associerade med uppsättningen användare 
1. Skapa en säkerhetsgrupp för varje kombination av användaren set och autentiseringsuppgifter i molnet eller lokalt efter dina behov.
1. Återställa delade autentiseringsuppgifter. När appen har distribuerats i Azure AD behöver lösenordet för det delade kontot med enskilda användare. Eftersom Azure AD lagrar lösenordet, Överväg att den ska vara mycket långa och komplexa. 
1. Konfigurera automatisk förnyelse av lösenordet om programmet stöder den. På så sätt kan inte ens den administratör som gjorde den inledande installationen vet lösenordet för det delade kontot. 

## <a name="plan-your-authentication-method"></a>Planera autentiseringsmetod

Det är ett viktigt första beslut att konfigurera en hybrididentitetslösning i Azure AD för att välja rätt autentiseringsmetod. Implementera den autentiseringsmetod som konfigureras med hjälp av Azure AD Connect, som också etablerar användare i molnet.

Om du vill välja en autentiseringsmetod som du behöver tänka på tid, befintlig infrastruktur, komplexiteten och kostnaden för att implementera ditt val. Dessa faktorer är olika för varje organisation och kan ändras över tid. Du bör välja det som bäst passar din situation. Mer information finns i [välja rätt autentiseringsmetod för din Azure Active Directory-hybrididentitetslösning](https://docs.microsoft.com/azure/security/azure-ad-choose-authn).

## <a name="plan-your-security-and-governance"></a>Planera din säkerhet och styrning 

Identiteten är den nya primära pivot för säkerhet uppmärksamhet och investeringar eftersom nätverket perimetrar har blivit allt högre grad porös och mindre effektiva med expanderingen av BYOD-enheter och program i molnet. 

### <a name="plan-access-reviews"></a>Planera åtkomstgranskningar

[Åtkomstgranskningar](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) kan organisationer effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och rolltilldelningar. Du bör planera att granska användaråtkomst regelbundet att kontrollera att rätt personer ha fortsatt åtkomst.

Några av de viktigaste ämnena att planera för när du konfigurerar åtkomstgranskningar är:

1. Identifiera en takt för åtkomstgranskningar som passar företagets behov. Detta kan vara så ofta som en gång i veckan, varje månad, varje år eller öva på begäran.

1. Skapa grupper som representerar granskare av appen åtkomst till rapporter. Du måste se till att intressenter mest bekant med appen och dess målanvändare och användningsfall är deltagare i din åtkomstgranskningar

1. Slutför en åtkomstgranskning innehåller tar bort app åtkomstbehörigheter till användare som inte längre behöver åtkomst. Planera för hantering av potentiella supportärenden från nekade användare. En borttagen användare förblir har tagits bort i Azure AD i 30 dagar under tiden de kan återställas av en administratör om det behövs. Efter 30 dagar tas användaren bort permanent. Med hjälp av Azure Active Directory-portalen, en Global administratör kan uttryckligen permanent ta bort en nyligen borttagna användare innan den tidsperioden har nåtts.

### <a name="plan-auditing"></a>Planera granskning

Azure AD tillhandahåller [rapporter som innehåller tekniska och affärsmässiga insikter](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/). 

Säkerhets- och aktivitetsrapporter är tillgängliga. Säkerhetsrapporter visar användare som har flaggats för risk och riskfyllda inloggningar. Aktivitetsrapporter hjälper dig att förstå beteendet för användare i din organisation genom med information om inloggningsaktiviteter och ange granskningshistorik för alla inloggningar. Du kan använda rapporter för att hantera riskerna, öka produktiviteten och övervaka efterlevnad.

| Rapporttyp | Åtkomstgranskning | Säkerhetsrapporter | Logga in rapporten |
|-------------|---------------|------------------|----------------|
| Använd för att granska | Behörigheter för programmet och användning. | Potentiellt komprometterade konton | Vem har åtkomst till programmen |
| Möjliga åtgärder | Granska åtkomst återkalla behörigheter | Återkalla åtkomst; tvinga säkerhet återställning | Återkalla åtkomst |

Azure AD behåller de flesta granskningsdata i 30 dagar och gör dem tillgängliga via Azure-administrationsportalen eller via ett API som du kan ladda ned till din analyssystem.

### <a name="consider-using-microsoft-cloud-application-security"></a>Överväg att använda Microsoft Cloud Application Security

Microsoft Cloud App Security (MCAS) är en lösning i molnet åtkomst Security Broker (CASB). Det ger dig insyn i dina molnappar och tjänster, tillhandahåller avancerade analyser för att identifiera och bekämpa cyberhot och gör att du kan styra hur dina data skickas.

Distribuera MCAS kan du:

- Använd Cloud Discovery för att mappa och identifiera molnmiljön och de molnappar som din organisation använder.
- Sanktionering och ta bort sanktionera appar i molnet
- Använd enkla att distribuera appanslutningsverktyg som utnyttjar providerns API: er för synlighet och styrning av appar som du ansluter till
- Använda Appkontroll för villkorsstyrd åtkomst protection för att få insyn i realtid och kontroll över åtkomst och aktiviteter i dina appar i molnet
- Beskriver hur du kontinuerlig kontroll genom inställningen och kontinuerligt finjustera, principer.

Sessionskontrollen för Microsoft Cloud Application Security (MCAS) är tillgänglig för alla webbläsare på alla större plattformar på alla operativsystem. Mobilappar och skrivbordsappar kan också blockerad eller tillåten. Genom att integrera internt med Azure AD kan appar som är konfigurerade med SAML, eller öppna ID Connect-appar med enkel inloggning i Azure AD kan stödjas, inklusive [flera aktuella appar](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad).

Information om MCAS finns i den [översikt över Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). MCAS är en användarbaserad prenumerationstjänst. Du kan läsa licensiering informationen i den [MCAS licensiering datablad](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO).

### <a name="use-conditional-access"></a>Använd villkorlig åtkomst

Du kan använda villkorlig åtkomst för att automatisera villkorsbaserade besluten om åtkomstkontroll för dina appar i molnet.

Principer för villkorlig åtkomst tillämpas efter det att den första faktorautentiseringen har slutförts. Villkorlig åtkomst är därför inte avsedd som en första rad ansvarsfrihetsgrund för scenarier som denial of service-attacker, men kan använda signaler från dessa händelser för att fastställa åtkomst. Till exempel inloggning risknivån kan platsen för begäran och så vidare användas. Mer information om villkorlig åtkomst finns i [översikten](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access) och [distributionsplan](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access).

## <a name="azure-sso-technical-requirements"></a>Tekniska krav för Azure SSO

I följande avsnitt beskrivs kraven för att konfigurera ditt specifika program inklusive nödvändiga environment(s), slutpunkter, Anspråksmappning, obligatoriska attribut, certifikat och protokoll som används. Du behöver den här informationen för att konfigurera enkel inloggning i den [Azure AD-portalen](https://portal.azure.com/).

### <a name="authentication-mechanism-details"></a>Information om autentisering av mekanism

Microsoft tillhandahåller en självstudie för alla förintegrerade SaaS-appar, och du behöver inte den här informationen. Om programmet inte finns i vår marketplace för program / galleriet, du kan behöva samla in följande typer av data:

- **Aktuella identitetsprovider som används i programmet för enkel inloggning om tillämpligt** , till exempel: AD FS, PingFederate, Okta
- **Protokoll som stöds av målprogrammet** – till exempel SAML 2.0, OpenID Connect, OAuth, formulärbaserad autentisering, WS-Fed WS-Trust
- **Protokollet som konfigureras med Azure AD** – till exempel SAML 2.0 eller 1.1, OpenID Connect, OAuth, formulärbaserad, WS-Fed

### <a name="attribute-requirements"></a>Attributet krav

Det finns en förkonfigurerad uppsättning attribut och attributmappningar mellan Azure AD-användarobjekt och objekt för varje SaaS-app. Vissa appar hantera andra typer av objekt, t.ex grupper. Planera mappningen av användarattribut från Azure AD i ditt program och [anpassa attributmappningar standard](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) enligt ditt företag behöver.

### <a name="certificate-requirements"></a>Certifikatkrav

Certifikat för programmet måste vara uppdaterad eller det finns en risk för användare inte kan komma åt programmet. De flesta certifikat för SaaS-program är bra för 36 månader. Du kan ändra den certifikat-tiden i programbladet. Se till att dokumentera förfallodatum och veta hur du hanterar din certifikatförnyelse. 

Det finns två sätt att hantera dina certifikat. 

- **Automatisk certifikatförnyelse** -Microsoft stöder [signeringsnyckel i Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Det här är våra bästa metoden för att hantera certifikat, stöder inte alla ISV det här scenariot.

- **Uppdatera manuellt** -varje program har ett eget certifikat som går ut baserat på hur den har definierats. Innan programmets certifikatet upphör att gälla, skapa ett nytt certifikat och skicka den till ISV: er. Den här informationen kan hämtas från federationsmetadata. [Läs mer om federationsmetadata här.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata)

## <a name="implement-sso"></a>Implementera enkel inloggning

Använd följande faser för att planera för och distribuera din lösning i din organisation:

### <a name="user-configuration-for-sso"></a>Användarkonfiguration för enkel inloggning

- **Identifiera din testanvändare**

   Kontakta till appägaren och be dem att skapa minst tre testanvändare i programmet. Se till att den information som du ska använda som den primära identifieraren har fyllts i korrekt och matchar ett attribut som är tillgängliga i Azure AD. I de flesta fall kommer detta att mappa till ”NameID” för SAML-baserade program. För JWT-token är det ”preferred_username”.
   
   Skapa användaren i Azure AD antingen manuellt som en molnbaserad användare eller synkronisera användare från en lokal plats med hjälp av Azure AD Connect-Synkroniseringsmotorn. Kontrollera att informationen som matchar de anspråk som skickas till programmet.

- **Konfigurera enkel inloggning**

   Från den [programlista](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list), leta upp och öppna självstudierna för enkel inloggning för ditt program och sedan anvisningarna i självstudien på Konfigurera ditt SaaS-program.

   Om du inte kan hitta ditt program, se [anpassad programdokumentationen](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). Detta tar dig igenom hur du lägger till ett program som inte finns i Azure AD-galleriet.

   Du kan även använda anspråk som utfärdats i SAML-token för enterprise-programmet med [Microsofts vägledning dokumentation](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping). Se till att detta mappas till vad du förväntar dig att ta emot i SAML-svar för ditt program. Om det uppstår problem under konfigurationen, använda vår vägledning på [hur du felsöker SSO integration](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging).

Onboarding för anpassade program är en funktion i Azure AD Premium P1 eller P2-licenser.

### <a name="provide-sso-change-communications-to-end-users"></a>Tillhandahålla enkel inloggning ändra kommunikation till slutanvändare

Implementera din kommunikationsplan. Kontrollera att du är att låta användarna vet att en ändring som kommer, när det har kommit så här gör nu och hur du ber om hjälp.

### <a name="verify-end-user-scenarios-for-sso"></a>Kontrollera slutanvändaren scenarier för enkel inloggning

Du kan använda följande testfall för att utföra tester på företagsägda och personliga enheter för att garantera SSO-konfigurationer fungerar som förväntat. Scenarierna nedan förutsätter att en användare navigera till en program-URL och gå igenom en autentiseringsflödet som initieras av tjänstleverantören (SP-initierat autentiseringsflöde).

| Scenario | Förväntat resultat på SP-initierat autentiseringsflöde av användare |
|----------|---------------------------------------------------|
| Logga in på programmet med Internet Explorer på corpnet. | Integrerad Windows autentisering (IWA) sker utan ytterligare uppmaningar. |
| Logga in på programmet med Internet Explorer när du är från corpnet med nya inloggningsförsöket. | Formulärbaserad uppmaning till AD FS-servern. Användaren loggar in och uppmanar webbläsaren för MFA. |
| Logga in på programmet med Internet Explorer när du är från corpnet med en aktuell session och har aldrig utförts MFA. | Användare får inte fråga efter första faktorn. Användaren får snabb för MFA. |
| Logga in på programmet med Internet Explorer när du är från corpnet med en aktuell session och redan har utfört MFA i den här sessionen. | Användare får inte fråga efter första faktorn. Användare får inte MFA. Användaren fartygens i program. |
| Logga in på programmet med Safari/Chrome/Firefox medan du är från corpnet med en aktuell session och redan har utfört MFA i den här sessionen. | Användare får inte fråga efter första faktorn. Användare får inte MFA. Användaren SSOS i program. |
| Logga in i program med Safari/Chrome/Firefox medan du är från corpnet med nya inloggningsförsöket. | Formulärbaserad uppmaning till AD FS-servern. Användaren loggar in och uppmanar webbläsaren för MFA. |
| Logga in på programmet med Chrome/Firefox på företagets nätverk med en aktuell session. | Användare får inte fråga efter första faktorn. Användare får inte MFA. Användaren SSOS i program. |
| Logga in på programmet med program-mobilapp med en ny inloggningsförsöket. | Formulärbaserad uppmaning till AD FS-servern. Användaren loggar in och ADAL klienten frågar efter MFA. |
| Obehörig användare försöker logga in på programmet med inloggnings-URL. | Formulärbaserad uppmaning till AD FS-servern. Användaren inte logga in med första faktorn. |
| Behörig användare försöker logga in, men anger ett felaktigt lösenord. | Användaren navigerar till programmets URL och tar emot felaktiga användarnamn/lösenord. |
| Behörig användare klickar på länken i ett e-postmeddelande och redan har autentiserats. | Användaren klickar på URL: en och har loggat in i programmet utan ytterligare uppmaningar. |
| Behörig användare klickar på länken i ett e-postmeddelande och ännu har autentiserats inte. | Användaren klickar på URL: en och är prompten för att autentisera med första faktorn. |
| Auktoriserade användarloggar i program med program-mobilappen (SP-initierat) med en ny inloggningsförsöket. | Formulärbaserad uppmaning till AD FS-servern. Användaren loggar in och ADAL klienten frågar efter MFA. |
| Obehörig användare försöker logga in på programmet med inloggnings-URL (SP-initierat). | Formulärbaserad uppmaning till AD FS-servern. Användaren inte logga in med första faktorn. |
| Behörig användare försöker logga in, men anger ett felaktigt lösenord.| Användaren navigerar till programmets URL och tar emot felaktiga användarnamn/lösenord. |
| Auktoriserade användare loggar och loggar sedan in igen. | Om URL: en för utloggning har konfigurerats kan loggas användaren ut från alla tjänster och uppmaning om att autentisera. |
| Auktoriserade användare loggar och loggar sedan in igen. | Om URL: en för utloggning inte är konfigurerad, användaren kommer automatiskt att loggas igen med hjälp av befintliga token från den befintliga Azure AD-webbläsarsessionen. |
| Behörig användare klickar på länken i ett e-postmeddelande och redan har autentiserats. | Användaren klickar på URL: en och har loggat in i programmet utan ytterligare uppmaningar. |
| Behörig användare klickar på länken i ett e-postmeddelande och ännu har autentiserats inte. | Användaren klickar på URL: en och är prompten för att autentisera med första faktorn. |

## <a name="manage-sso"></a>Hantera enkel inloggning

Det här avsnittet beskrivs de krav och rekommendationer för att hantera enkel inloggning.

### <a name="required-administrative-roles"></a>Nödvändiga administrativa roller

Använd alltid rollen med minst antal behörigheter som är tillgängliga för att utföra nödvändiga uppgiften i Azure Active Directory. Microsoft rekommenderar att du [granska de olika rollerna som är tillgängliga](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) och välja rätt att lösa dina behov för varje person för det här programmet. Vissa roller kan behöva tillämpas tillfälligt och tas bort när distributionen är klar.

| Person| Roller | Azure AD-roll (vid behov) |
|--------|-------|-----------------------------|
| Help desk-administratör | Nivå 1-supporten | Ingen |
| Identity-administratör | Konfigurera och felsöka när problem påverkar Azure AD | Global administratör |
| Program-administratör | Användare-attestering i program, konfigurationen på användare med behörigheter | Ingen |
| Infrastruktur-administratörer | Ägare för förnyelse av certifikat | Global administratör |
| Företag ägare/intressenter | Användare-attestering i program, konfigurationen på användare med behörigheter | Ingen |

Vi rekommenderar att du använder [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) (PIM) för att hantera dina roller för att ge ytterligare granskning, kontroll och åtkomst granska för användare med katalogbehörigheter.

### <a name="sso-certificate-lifecycle-management"></a>Livscykelhantering för SSO-certifikat

Det är viktigt att identifiera rätt roller och e-distributionslistor som har behörighet att hantera livscykeln för signeringscertifikatet mellan Azure AD och programmet som konfigureras med enkel inloggning. Här är några av de viktiga roller som vi rekommenderar att du har på plats:

- Ägare för att uppdatera användaregenskaper i program
- Ägare på anrop för webbsupport
- Nära övervakade e-distributionslista certifikatrelaterade ändringsmeddelanden

Den maximala livstiden för ett certifikat är tre år. Vi rekommenderar att du upprättar en process på hur du ska hantera en ändring av certifikat mellan Azure AD och ditt program. Detta kan förhindra eller minimera ett avbrott på grund av ett certifikat upphör att gälla eller tvinga förnya certifikatet.

### <a name="rollback-process"></a>Återställningsprocessen

När du har slutfört testningen baserat på din testfall är det dags att flytta till produktion med ditt program. Flytta till produktion innebär att du ska implementera dina planerade och testade konfigurationer i din produktionsklient och distribuera den till dina användare. Det är dock viktigt att planera vad du gör om distributionen inte går som planerat. Om konfigurationen för enkel inloggning misslyckas under distributionen, måste du förstå hur du minimera eventuella driftstopp och minska inverkan på användarna.

Tillgängligheten för autentiseringsmetoder i programmet avgör din strategi för bästa. Se alltid till du har detaljerad dokumentation för app ägare om exakt hur du kommer tillbaka till det ursprungliga tillståndet för inloggning configuration om distributionen körs i problem.

- **Om appen stöder flera Identitetsproviders**för exempel LDAP- och AD FS- och Ping, inte ta bort den befintliga konfigurationen för enkel inloggning under distributionen. I stället inaktivera det under migreringen om du behöver växla tillbaka senare. 

- **Om din app inte stöder flera IDP: er** men tillåter användare att logga in med formulärbaserad autentisering (användarnamn/lösenord), se till att användare kan växla över till den här metoden om det inte går att distributionen för nya SSO-konfiguration.

### <a name="access-management"></a>Åtkomsthantering

Vi rekommenderar att du väljer en skalade metod när de har åtkomst till resurser. Vanliga metoder för inkludera använder lokala grupper genom att synkronisera via Azure AD Connect, [skapa dynamiska grupper i Azure AD baserat på användarattribut](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal), eller skapa [självbetjäningsgrupper](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) i Azure AD hanteras av en resursägare.

### <a name="monitor-security"></a>Övervakar säkerhet

Vi rekommenderar att du konfigurerar en vanlig takt där du granska de olika aspekterna av SaaS-appsäkerhet och utföra eventuella korrigerande åtgärder som krävs.

### <a name="troubleshooting"></a>Felsökning

Följande länkar finns felsökning scenarier. Du kanske vill skapa en specifik vägledning för supportpersonal som införlivar dessa scenarier och de steg du kan åtgärda problemen.

#### <a name="consent-issues"></a>Medgivande problem

- [Ett oväntat medgivande fel](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-prompt)

- [Fel vid användarmedgivande](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-error)

#### <a name="sign-in-issues"></a>Inloggningsproblem

- [Problem som loggar in från en anpassad portal](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-deeplink)

- [Problem vid inloggning från åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-access-panel)

- [Fel på inloggningssidan för programmet](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-application-error)

- [Problem som loggar in på ett Microsoft-program](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-first-party-microsoft)

#### <a name="sso-issues-for-applications-listed-in-the-azure-application-gallery"></a>Problem med enkel inloggning för program som anges i Azure-Programgalleriet

- [Problem med enkel inloggning med lösenord för program visas i Azure-Programgalleriet](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery) 

- [Problem med federerad enkel inloggning för program som anges i Azure-Programgalleriet](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-gallery)   

#### <a name="sso-issues-for-applications-not-listed-in-the-azure-application-gallery"></a>Problem med enkel inloggning för program som inte finns i Azure-Programgalleriet

- [Problem med enkel inloggning med lösenord för program som inte finns i Azure-Programgalleriet](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-non-gallery) 

- [Problem med federerad enkel inloggning för program som inte finns i Azure-Programgalleriet](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-non-gallery)

## <a name="next-steps"></a>Nästa steg

[Felsöka SAML-baserad enkel inloggning](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

[Anspråksmappning för appar via PowerShell](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

[Anpassa anspråk som utfärdats i SAML-token](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

[Enkel inloggning SAML-protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

[SAML-protokoll för enkel utloggning](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-out-protocol-reference)

[Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) (för externa användare, till exempel partners och leverantörer)

[Azure AD villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

[Azure Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

[Åtkomst med enkel inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

[Självstudiekursen om enkel inloggning](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
