---
title: Planera en enkel Azure Directory-distribution
description: Guide som hjälper dig att planera, distribuera och hantera SSO i din organisation.
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
ms.openlocfilehash: 92496fa572c5c1cae4588f82ac61c18de3024045
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76512835"
---
# <a name="plan-a-single-sign-on-deployment"></a>Planera en distribution för enkel inloggning

Enkel inloggning (SSO) innebär åtkomst till alla program och resurser som en användare behöver genom att logga in endast en gång med ett enda användarkonto. Med SSO kan användare komma åt alla nödvändiga program utan att behöva autentisera en andra gång.

## <a name="benefits-of-sso"></a>Fördelar med SSO

Enkel inloggning (SSO) lägger till säkerhet och bekvämlighet när användare loggar in på program i Azure Active Directory (Azure AD). 

Många organisationer förlitar sig på saaS-program (Software as a Service), till exempel Office 365, Box och Salesforce, för slutanvändarens produktivitet. Historiskt sett har IT-personal som behövs för att individuellt skapa och uppdatera användarkonton i varje SaaS-program, och användarna behövde komma ihåg ett lösenord för varje.

Azure Marketplace har över 3000 program med förintegrerade SSO-anslutningar, vilket gör det enkelt att integrera dem i din klientorganisation.

## <a name="licensing"></a>Licensiering

- **Azure AD-licensiering** – SSO för förintegrerade SaaS-program är kostnadsfri. Antalet objekt i katalogen och de funktioner som du vill distribuera kan dock kräva ytterligare licenser. En fullständig lista över licenskrav finns i [Azure Active Directory Pricing](https://azure.microsoft.com/pricing/details/active-directory/).
- **Programlicensiering** – Du behöver lämpliga licenser för dina SaaS-program för att uppfylla dina affärsbehov. Arbeta med programägaren för att avgöra om de användare som tilldelats programmet har rätt licenser för sina roller i programmet. Om Azure AD hanterar den automatiska etableringen baserat på roller måste de roller som tilldelats i Azure AD anpassas till antalet licenser som ägs i programmet. Felaktigt antal licenser som ägs i programmet kan leda till fel under etableringen/uppdateringen av en användare.

## <a name="plan-your-sso-team"></a>Planera ditt SSO-team

- **Engagera rätt intressenter** - När teknikprojekt misslyckas beror det oftast på felaktiga förväntningar på påverkan, resultat och ansvar. Undvik dessa fallgropar [genom att se till att du engagerar rätt intressenter](https://aka.ms/deploymentplans) och att intressenter förstår deras roller.
- **Planera kommunikation** - Kommunikation är avgörande för att en ny tjänst ska lyckas. Kommunicera proaktivt med användarna om hur deras upplevelse kommer att förändras, när den ändras och hur de får support om de får problem. Granska alternativen för [hur slutanvändare kommer åt sina SSO-aktiverade program](end-user-experiences.md)och skapa din kommunikation så att den matchar ditt val. 

## <a name="plan-your-sso-protocol"></a>Planera ditt SSO-protokoll

En SSO-implementering baserad på federationsprotokoll förbättrar säkerhet, tillförlitlighet och slutanvändares upplevelser och är enklare att implementera. Många program är förintegrerade i Azure AD med [stegvisa guider tillgängliga](../saas-apps/tutorial-list.md). Du hittar dem på vår [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). Detaljerad information om varje [SSO-metod](what-is-single-sign-on.md)finns i artikeln Enkel inloggning till program i Azure Active Directory .

Det finns två huvudsakliga sätt på vilka du kan göra det möjligt för användarna att logga in på dina appar:

- **Med federerade enda sign-on** Azure AD autentiserar användaren till programmet med hjälp av deras Azure AD-konto. Den här metoden stöds för program som stöder protokoll som SAML 2.0, WS-Federation eller OpenID Connect och är det rikaste läget för enkel inloggning. Vi rekommenderar att du använder Federerad SSO med Azure AD när ett program stöder det, i stället för lösenordsbaserad SSO och ADFS.

- **Med lösenordsbaserade användare med enkel inloggning** loggar de in på programmet med ett användarnamn och lösenord första gången, de kommer åt det. Efter den första inloggningen levererar Azure AD användarnamnet och lösenordet till programmet. Lösenordsbaserad enkel inloggning möjliggör säker lagring av programlösenord och spelas upp igen med ett webbläsartillägg eller en mobilapp. Det här alternativet utnyttjar den befintliga inloggningsprocessen som tillhandahålls av programmet, gör det möjligt för en administratör att hantera lösenorden och kräver inte att användaren känner till lösenordet.

### <a name="considerations-for-federation-based-sso"></a>Överväganden för federationsbaserad SSO

- **Använda OpenID Connect och OAuth** - Om programmet du ansluter till stöder det använder du metoden OIDC/OAuth 2.0 för att aktivera SSO till det programmet. Den här metoden kräver mindre konfiguration och möjliggör en rikare användarupplevelse. Mer information finns i [OAuth 2.0,](../develop/v2-oauth2-auth-code-flow.md) [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)och [Azure Active Directory developer's guide](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).
- **Slutpunktskonfigurationer för SAML-baserade SSO** - Om du använder SAML behöver dina utvecklare specifik information innan du konfigurerar programmet. Mer information finns i [Redigera den grundläggande SAML-konfigurationen](configure-single-sign-on-non-gallery-applications.md).
- **Certifikathantering för SAML-baserad SSO** – När du aktiverar Federerad SSO för ditt program skapar Azure AD ett certifikat som är som standard giltigt i tre år. Du kan anpassa utgångsdatumet för certifikatet om det behövs. Se till att du har processer på plats för att förnya certifikat innan de upphör att gälla. Mer information finns i [Azure AD Managing Certificates](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs).

### <a name="considerations-for-password-based-sso"></a>Överväganden för lösenordsbaserad SSO

Använda Azure AD för lösenordsbaserad SSO kräver distribution av ett webbläsartillägg som på ett säkert sätt hämtar autentiseringsuppgifterna och fyller i inloggningsformulären. Definiera en mekanism för att distribuera tillägget i stor skala med [webbläsare som stöds](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction). Alternativen är:

- [Grupprincip för Internet Explorer](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)
- [Configuration Manager för Internet Explorer](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)
- [Användardriven nedladdning och konfiguration för Chrome, Firefox, Microsoft Edge eller IE](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Mer information finns i [Så här konfigurerar du lösenordsinställning med enkel inloggning](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery).

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>Samla in metadata för inloggningsformulär för program som inte finns i galleriet

Microsoft stöder insamling av metadata i ett webbprogram för lösenordsvalv (fånga användarnamn och lösenordsfält). Navigera till inloggnings-URL:en under processen att konfigurera programmet för att samla in formulärmetadata. Fråga programägaren om den exakta inloggningsadressen. Den här informationen används under inloggningsprocessen och mappar Azure AD-autentiseringsuppgifter till programmet under inloggning.

Mer information finns i [Vad är programåtkomst och SSO med Azure AD? – Lösenordsbaserad SSO](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/).

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>Indikationer på att metadata i formulär måste återerövras

När program ändrar HTML-layouten kan du behöva hämta metadata för att justera för ändringarna. Vanliga symptom som tyder på att HTML-layouten har ändrats är:

- Användare som rapporterar att klicka på programmet fastnar i inloggningssidan
- Användare som rapporterar att användarnamnet eller lösenordet inte är ifyllt

#### <a name="shared-accounts"></a>Delade konton

Ur inloggningssynpunkt skiljer sig inte program med delade konton från ett galleriprogram som använder lösenords-SSO för enskilda användare. Det finns dock några ytterligare steg som krävs när du planerar och konfigurerar ett program som är avsett att använda delade konton:

1. Arbeta med programföretagsanvändare för att dokumentera följande:
   1. Uppsättning användare i organisationen som ska använda programmet
   1. Befintlig uppsättning autentiseringsuppgifter i programmet som är associerade med uppsättningen användare 
1. Skapa en säkerhetsgrupp i molnet eller lokalt för varje kombination av användaruppsättning och autentiseringsuppgifter.
1. Återställ de delade autentiseringsuppgifterna. När appen har distribuerats i Azure AD behöver enskilda personer inte lösenordet för det delade kontot. Eftersom Azure AD lagrar lösenordet bör du överväga att ange att det är mycket långt och komplext. 
1. Konfigurera automatisk överrullning av lösenordet om programmet stöder det. På så sätt kommer inte ens administratören som gjorde den första installationen att känna till lösenordet för det delade kontot. 

## <a name="plan-your-authentication-method"></a>Planera din autentiseringsmetod

Att välja rätt autentiseringsmetod är ett avgörande första beslut när du konfigurerar en Azure AD-hybrididentitetslösning. Implementera autentiseringsmetoden som konfigureras med hjälp av Azure AD Connect, som också etablerar användare i molnet.

Om du vill välja en autentiseringsmetod måste du tänka på tid, befintlig infrastruktur, komplexitet och kostnad för att implementera ditt val. Dessa faktorer är olika för varje organisation och kan förändras med tiden. Du bör välja den som bäst matchar ditt specifika scenario. Mer information finns i [Välja rätt autentiseringsmetod för azure Active Directory hybrididentitetslösning](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn).

## <a name="plan-your-security-and-governance"></a>Planera din säkerhet och styrning 

Identitet är den nya primära pivot för säkerhetsövervakning och investeringar eftersom nätverksperimeter har blivit allt porösa och mindre effektiva med explosionen av BYOD-enheter och molnapplikationer. 

### <a name="plan-access-reviews"></a>Planera åtkomstgranskningar

[Access Reviews](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) gör det möjligt för organisationer att effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och rolltilldelningar. Du bör planera att granska användaråtkomst regelbundet för att se till att endast rätt personer har fortsatt åtkomst.

Några av de viktigaste ämnena att planera för när du ställer in åtkomstgranskningar är:

1. Identifiera en kadens för åtkomst recensioner som passar ditt företags behov. Detta kan vara så vanligt som en gång i veckan, månadsvis, årligen eller som en on-demand övning.

1. Skapa grupper som representerar granskarna av appåtkomstrapporterna. Du måste se till att intressenter som är mest bekanta med appen och dess målanvändare och användningsfall är deltagare i dina åtkomstgranskningar

1. Genom att slutföra en åtkomstgranskning ingår att ta bort appåtkomstbehörigheter till användare som inte längre behöver åtkomst. Planera för hantering av potentiella supportbegäranden från nekade användare. En borttagen användare kommer att förbli borttagen i Azure AD i 30 dagar under vilken tid de kan återställas av en administratör om det behövs. Efter 30 dagar tas användaren bort permanent. Med hjälp av Azure Active Directory-portalen kan en global administratör uttryckligen ta bort en nyligen borttagen användare permanent innan den tidsperioden har uppnåtts.

### <a name="plan-auditing"></a>Planera granskning

Azure AD innehåller [rapporter som innehåller tekniska insikter och affärsinsikter](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/). 

Både säkerhets- och aktivitetsrapporter är tillgängliga. Säkerhetsrapporter visar användare som flaggats för risk och riskfyllda inloggningar. Du kan använda rapporter för att hantera risker, öka produktiviteten och övervaka efterlevnaden.

| Rapporttyp | Recension av Access | Säkerhetsrapporter | Inloggningsrapport |
|-------------|---------------|------------------|----------------|
| Används för att granska | Programbehörigheter och användning. | Potentiellt komprometterade konton | Vem har åtkomst till programmen |
| Potentiella åtgärder | Tillgång till revision. återkalla behörigheter | Återkalla åtkomst; tvinga fram säkerhetsåterställning | Återkalla åtkomst |

Azure AD behåller de flesta granskningsdata i 30 dagar och gör data tillgängliga via Azure-administratörsportalen eller via ett API som du kan hämta till analyssystemen.

### <a name="consider-using-microsoft-cloud-application-security"></a>Överväg att använda Microsoft Cloud Application Security

McAS (Microsoft Cloud App Security) är en CASB-lösning (Cloud Access Security Broker). Det ger dig insyn i dina molnappar och molntjänster, tillhandahåller sofistikerade analyser för att identifiera och bekämpa cyberhot och gör att du kan styra hur dina data färdas.

Genom att distribuera MCAS kan du:

- Använd Cloud Discovery för att kartlägga och identifiera din molnmiljö och de molnappar som din organisation använder.
- Sanktionera och avseaktionsappar i molnet
- Använd lätt distribuerappkopplingar som utnyttjar provider-API:er, för synlighet och styrning av appar som du ansluter till
- Använd skydd för appkontroll för villkorlig åtkomst för att få synlighet i realtid och kontroll över åtkomst och aktiviteter i dina molnappar
- Hjälper dig att ha kontinuerlig kontroll genom att ställa in och sedan kontinuerligt finjustera, principer.

McAS-sessionskontrollen (Microsoft Cloud Application Security) är tillgänglig för alla webbläsare på alla större plattformar i alla operativsystem. Mobilappar och skrivbordsappar kan också blockeras eller tillåtas. Genom att integrera med Azure AD kan alla appar som är konfigurerade med SAML eller Open ID Connect-appar med enkel inloggning i Azure AD stödjas, inklusive [flera utvalda appar](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad).

Information om MCAS finns i [översikten över Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). MCAS är en användarbaserad prenumerationstjänst. Du kan granska licensieringsinformation i [MCAS-licensieringsdatabladet](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO).

### <a name="use-conditional-access"></a>Använd villkorlig åtkomst

Med villkorlig åtkomst kan du automatisera villkorsbaserade beslut om åtkomstkontroll för dina molnappar.

Principer för villkorlig åtkomst tillämpas efter att den första faktorautentiseringen har slutförts. Villkorlig åtkomst är därför inte avsedd som ett första linjeförsvar för scenarier som DoS-attacker (Denial-of-Service), men kan använda signaler från dessa händelser för att avgöra åtkomst. Till exempel kan inloggningsrisknivån, platsen för begäran och så vidare användas. Mer information om villkorlig åtkomst finns [i översikten](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access) och [distributionsplanen](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access).

## <a name="azure-sso-technical-requirements"></a>Tekniska krav på Azure SSO

I följande avsnitt beskrivs kraven för att konfigurera ditt specifika program, inklusive nödvändiga miljöer, slutpunkter, anspråksmappning, obligatoriska attribut, certifikat och protokoll som används. Du behöver den här informationen för att konfigurera SSO i [Azure AD-portalen](https://portal.azure.com/).

### <a name="authentication-mechanism-details"></a>Information om autentiseringsmekanism

För alla förintegrerade SaaS-appar tillhandahåller Microsoft en självstudiekurs och du behöver inte den här informationen. Om programmet inte finns i vår applikationsmarknadsplats/galleri kan du behöva samla in följande data:

- **Aktuell identitetsleverantör som programmet använder för SSO om tillämpligt** - Till exempel: AD FS, PingFederate, Okta
- **Protokoll som stöds av målprogrammet** - Till exempel SAML 2.0, OpenID Connect, OAuth, Forms-Based Auth, WS-Fed, WS-Trust
- **Protokoll som konfigureras med Azure AD** – till exempel SAML 2.0 eller 1.1, OpenID Connect, OAuth, Forms-Based, WS-Fed

### <a name="attribute-requirements"></a>Attributkrav

Det finns en förkonfigurerad uppsättning attribut och attributmappningar mellan Azure AD-användarobjekt och varje SaaS-apps användarobjekt. Vissa appar hanterar andra typer av objekt, till exempel grupper. Planera mappningen av användarattribut från Azure AD till ditt program och [anpassa standardattributmappningarna](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) efter dina affärsbehov.

### <a name="certificate-requirements"></a>Certifikatkrav

Certifikatet för programmet måste vara uppdaterat eller det finns en risk för att användare inte kan komma åt programmet. De flesta SaaS-programcertifikat är bra i 36 månader. Du ändrar certifikatets varaktighet i programbladet. Se till att dokumentera förfallodatumet och vet hur du hanterar certifikatförnyelsen. 

Det finns två sätt att hantera dina certifikat. 

- **Automatisk certifikatöverrullning** – Microsoft stöder [signering av nyckelöverrullning i Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Även om detta är vår föredragna metod för att hantera certifikat, stöder inte alla ISV det här scenariot.

- **Manuellt uppdatering** - Varje program har ett eget certifikat som upphör att gälla baserat på hur det definieras. Innan programmets certifikat upphör att gälla skapar du ett nytt certifikat och skickar det till ISV. Denna information kan hämtas från federationens metadata. [Läs mer om federationens metadata här.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata)

## <a name="implement-sso"></a>Implementera SSO

Använd följande faser för att planera och distribuera din lösning i din organisation:

### <a name="user-configuration-for-sso"></a>Användarkonfiguration för SSO

- **Identifiera dina testanvändare**

   Kontakta appens ägare och be dem att skapa minst tre testanvändare i programmet. Kontrollera att informationen som du ska använda som primär identifierare fylls i korrekt och matchar ett attribut som är tillgängligt i Azure AD. I de flesta fall kommer detta att mappas till "NameID" för SAML-baserade program. För JWT-tokens är det "preferred_username".
   
   Skapa användaren i Azure AD antingen manuellt som en molnbaserad användare eller synkronisera användaren från lokalt med hjälp av Azure AD Connect-synkroniseringsmotorn. Se till att informationen matchar de anspråk som skickas till programmet.

- **Konfigurera enkel inloggning**

   Från [listan över program](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list), hitta och öppna SSO handledning för ditt program, följ sedan handledningens steg på att framgångsrikt konfigurera din SaaS-program.

   Om du inte kan hitta programmet läser du [Dokumentation för anpassat program](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). Detta kommer att gå igenom hur du lägger till ett program som inte finns i Azure AD-galleriet.

   Du kan också använda anspråk som utfärdats i SAML-token för företagsprogrammet med hjälp av [Microsofts vägledningsdokumentation](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping). Se till att den här mappningen ska vara vad du förväntar dig att få i SAML-svaret för ditt program. Om du stöter på problem under konfigurationen kan du använda vår vägledning om [hur du felsöker SSO-integrering](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging).

Anpassad programanpassning är en Azure AD Premium P1- eller P2-licensfunktion.

### <a name="provide-sso-change-communications-to-end-users"></a>Ge SSO-ändringskommunikation till slutanvändare

Implementera din kommunikationsplan. Se till att du låter dina slutanvändare veta att en förändring kommer, när den har kommit, vad du ska göra nu och hur du söker hjälp.

### <a name="verify-end-user-scenarios-for-sso"></a>Verifiera slutanvändarscenarier för SSO

Du kan använda följande testfall för att utföra tester på företagsägda och personliga enheter för att säkerställa att SSO-konfigurationerna fungerar som förväntat. Scenarierna nedan förutsätter att en användare navigerar till en program-URL och går igenom ett autentiseringsflöde som initierats av tjänsteleverantören (SP-initierat auth-flöde).

| Scenario | Förväntat resultat på SP-initierat auth-flöde av användare |
|----------|---------------------------------------------------|
| Logga in på ansökan med IE medan på corpnet. | Integrerad Windows-autentisering (IWA) sker utan ytterligare uppmaningar. |
| Logga in på ansökan med IE medan off corpnet med nya inloggningsförsök. | Formulärbaserad prompt på AD FS Server. Användaren loggar in och webbläsaruppmaningar för MFA. |
| Logga in på ansökan med IE medan off corpnet med en aktuell session och har aldrig utfört MFA. | Användaren får inte fråga för första faktorn. Användaren får fråga om MFA. |
| Logga in på ansökan med IE medan off corpnet med en aktuell session och har redan utfört MFA i denna session. | Användaren får inte fråga för första faktorn. Användaren får inte MFA. Användare SSOs i ansökan. |
| Logga in på ansökan med Chrome / Firefox / Safari medan off corpnet med en aktuell session och har redan utfört MFA i denna session. | Användaren får inte fråga för första faktorn. Användaren får inte MFA. Användaren sso's i ansökan. |
| Logga in på ansökan med Chrome / Firefox / Safari medan off corpnet med nya inloggningsförsök. | Formulärbaserad prompt på AD FS Server. Användaren loggar in och webbläsaruppmaningar för MFA. |
| Logga in på ansökan med Chrome / Firefox medan på företagets nätverk med en aktuell session. | Användaren får inte fråga för första faktorn. Användaren får inte MFA. Användaren sso's i ansökan. |
| Logga in på ansökan med ansökan mobilapp med ett nytt inloggningsförsök. | Formulärbaserad prompt på AD FS Server. Användaren loggar in och ADAL-klienten uppmanas till MFA. |
| Obehörig användare försöker logga in på programmet med inloggnings-URL. | Formulärbaserad prompt på AD FS Server. Användaren misslyckas med att logga in med första faktorn. |
| Behörig användare försöker logga in men anger ett felaktigt lösenord. | Användaren navigerar till programmets URL och får fel på användarnamn/lösenord. |
| Behörig användare klickar på länken i ett e-postmeddelande och är redan autentiserat. | Användaren klickar på URL och är inloggad i programmet utan ytterligare uppmaningar. |
| Behörig användare klickar på länken i ett e-postmeddelande och är ännu inte autentiserat. | Användaren klickar på URL och uppmanas att autentisera med första faktorn. |
| Behörig användare loggar in på programmet med application mobile app (SP-initierad) med ett nytt inloggningsförsök. | Formulärbaserad prompt på AD FS Server. Användaren loggar in och ADAL-klienten uppmanas till MFA. |
| Obehörig användare försöker logga in på programmet med inloggnings-URL (SP-initierad). | Formulärbaserad prompt på AD FS Server. Användaren misslyckas med att logga in med första faktorn. |
| Behörig användare försöker logga in men anger ett felaktigt lösenord.| Användaren navigerar till programmets URL och får fel på användarnamn/lösenord. |
| Behöriga användare loggar ut och loggar sedan in igen. | Om utloggnings-URL:en är konfigurerad loggas användaren ut från alla tjänster och uppmanas att autentisera. |
| Behöriga användare loggar ut och loggar sedan in igen. | Om signerings-URL:en inte har konfigurerats loggas användaren automatiskt tillbaka med hjälp av befintlig token från den befintliga Azure AD-webbläsarsessionen. |
| Behörig användare klickar på länken i ett e-postmeddelande och är redan autentiserat. | Användaren klickar på URL och är inloggad i programmet utan ytterligare uppmaningar. |
| Behörig användare klickar på länken i ett e-postmeddelande och är ännu inte autentiserat. | Användaren klickar på URL och uppmanas att autentisera med första faktorn. |

## <a name="manage-sso"></a>Hantera SSO

I det här avsnittet beskrivs kraven och rekommendationerna för att hantera SSO.

### <a name="required-administrative-roles"></a>Obligatoriska administrativa roller

Använd alltid rollen med minst tillgängliga behörigheter för att utföra den uppgift som krävs i Azure Active Directory. Microsoft rekommenderar [att du granskar de olika roller som är tillgängliga](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) och väljer rätt för att lösa dina behov för varje persona för det här programmet. Vissa roller kan behöva tillämpas tillfälligt och tas bort när distributionen har slutförts.

| Persona| Roller | Azure AD-roll (om det behövs) |
|--------|-------|-----------------------------|
| Helpdesk admin | Nivå 1-stöd | Inget |
| Identitetsadministratör | Konfigurera och felsöka när problem påverkar Azure AD | Global administratör |
| Programadministratör | Användarens attestation i programmet, konfiguration för användare med behörigheter | Inget |
| Infrastrukturadministratörer | Certifiera rollover ägare | Global administratör |
| Företagare/intressent | Användarens attestation i programmet, konfiguration för användare med behörigheter | Inget |

Vi rekommenderar att du använder [PIM (Privileged Identity Management)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) för att hantera dina roller för att tillhandahålla ytterligare granskning, kontroll och åtkomstgranskning för användare med katalogbehörigheter.

### <a name="sso-certificate-lifecycle-management"></a>Livscykelhantering för SSO-certifikat

Det är viktigt att identifiera rätt roller och e-postdistributionslistor som har till uppgift att hantera livscykeln för signeringscertifikatet mellan Azure AD och programmet som konfigureras med enkel inloggning. Här är några av de viktiga roller som vi rekommenderar att ha på plats:

- Ägare för uppdatering av användaregenskaper i programmet
- Support för jourförhring för ägare för programpaus/korrigering
- Noggrant övervakad e-postdistributionslista för certifikatrelaterade ändringsmeddelanden

Certifikatets längsta livslängd är tre år. Vi rekommenderar att du upprättar en process för hur du hanterar en certifikatändring mellan Azure AD och ditt program. Detta kan bidra till att förhindra eller minimera ett avbrott på grund av att ett certifikat löper ut eller tvingar certifikatet att välta.

### <a name="rollback-process"></a>Återställningsprocess

När du har slutfört testningen baserat på dina testfall är det dags att gå in i produktionen med ditt program. Att gå över till produktion innebär att du implementerar dina planerade och testade konfigurationer i din produktionsklient och distribuerar den till användarna. Det är dock viktigt att planera vad du ska göra om distributionen inte går som planerat. Om SSO-konfigurationen misslyckas under distributionen måste du förstå hur du kan minska eventuella avbrott och minska påverkan på användarna.

Tillgängligheten för autentiseringsmetoder i programmet avgör din bästa strategi. Se alltid till att du har detaljerad dokumentation för appägare om exakt hur du kommer tillbaka till det ursprungliga inloggningskonfigurationstillståndet om distributionen hamnar i problem.

- **Om din app stöder flera identitetsleverantörer,** till exempel LDAP och AD FS och Ping, ska du inte ta bort den befintliga SSO-konfigurationen under distributionen. Inaktivera den i stället under migreringen om du behöver växla tillbaka den senare. 

- **Om din app inte stöder flera IDPs** men tillåter användare att logga in med hjälp av formulärbaserad autentisering (användarnamn /lösenord), se till att användarna kan falla tillbaka till den här metoden om den nya SSO-konfigurationsutrullningen misslyckas.

### <a name="access-management"></a>Åtkomsthantering

Vi rekommenderar att du väljer en skalad metod när du hanterar åtkomst till resurser. Vanliga metoder är att använda lokala grupper genom att synkronisera via Azure AD Connect, [skapa dynamiska grupper i Azure AD baserat på användarattribut](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal)eller skapa [självbetjäningsgrupper](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) i Azure AD som hanteras av en resursägare.

### <a name="monitor-security"></a>Övervaka säkerheten

Vi rekommenderar att du ställer in en regelbunden kadens där du granskar de olika aspekterna av SaaS-appsäkerhet och utför alla korrigerande åtgärder som krävs.

### <a name="troubleshooting"></a>Felsökning

Följande länkar innehåller felsökningsscenarier. Du kanske vill skapa en specifik guide för supportpersonalen som innehåller dessa scenarier och stegen för att åtgärda dem.

#### <a name="consent-issues"></a>Frågor om samtycke

- [Oväntat samtyckesfel](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-prompt)

- [Fel vid användarmedgivande](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-error)

#### <a name="sign-in-issues"></a>Inloggningsproblem

- [Problem med att logga in från en anpassad portal](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-deeplink)

- [Problem vid inloggning från åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-access-panel)

- [Fel på inloggningssidan för programmet](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-application-error)

- [Problem med att logga in på ett Microsoft-program](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-first-party-microsoft)

#### <a name="sso-issues-for-applications-listed-in-the-azure-application-gallery"></a>SSO-problem för program som anges i Azure Application Gallery

- [Problem med lösenord SSO för program som anges i Azure Application Gallery](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery) 

- [Problem med federerade SSO för program som anges i Azure Application Gallery](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-gallery)   

#### <a name="sso-issues-for-applications-not-listed-in-the-azure-application-gallery"></a>SSO-problem för program SOM INTE finns med i Azure Application Gallery

- [Problem med lösenord SSO för program SOM INTE finns med i Azure Application Gallery](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-non-gallery) 

- [Problem med federerade SSO för program SOM INTE finns med i Azure Application Gallery](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-non-gallery)

## <a name="next-steps"></a>Nästa steg

[Felsöka SAML-baserad enkel inloggning](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

[Anspråksmappning för appar via PowerShell](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

[Anpassa anspråk som utfärdats i SAML-token](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

[Saml-protokoll för enkel inloggning](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

[SAML-protokoll för enkel ut logga ut](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-out-protocol-reference)

[Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) (för externa användare som partner och leverantörer)

[Villkorlig åtkomst för Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

[Skydd mot Azure-identitet](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

[Åtkomst med enkel inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

[Självstudiekurs för program SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
