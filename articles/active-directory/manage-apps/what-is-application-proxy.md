---
title: Publicera lokala appar med Azure AD Application Proxy
description: Förstå varför ska man använda programproxy för att publicera lokala webbprogram externt till fjärranslutna användare. Läs mer om Application Proxy-arkitekturen, anslutningar, autentiseringsmetoder och säkerhetsfördelarna.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 05/31/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66403a18be8337939d457c061b07de948c3e34e8
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66730877"
---
# <a name="using-azure-ad-application-proxy-to-publish-on-premises-apps-for-remote-users"></a>Använda Azure AD-programproxy för att publicera lokala appar för fjärranvändare

Azure Active Directory (Azure AD) erbjuder många funktioner för att skydda användare, appar och data i molnet och lokalt. I synnerhet kan Azure AD Application Proxy-funktionen implementeras med IT-proffs som vill publicera lokala webbprogram externt. Fjärranvändare som behöver åtkomst till interna appar kan sedan komma åt dem på ett säkert sätt.

Möjlighet att få säker åtkomst till interna appar från utanför nätverket blir ännu mer kritiska i en modern arbetsplats. Med scenarier som BYOD (Bring Your Own Device) och mobila enheter måste IT-proffs att uppfylla två mål:

* Slutanvändare att vara produktiva när som helst och var som helst
* Skydda företagets tillgångar vid alla tidpunkter

Många organisationer tror att de har kontroll och skyddade när resurser som finns inom gränserna för deras företagsnätverk. Men i dagens digitala arbetsplats gränsen har utökats med hanterade mobila enheter och resurser och tjänster i molnet. Nu måste du hantera komplexiteten med att skydda användarnas identiteter och data som lagras på sina enheter och appar.

Kanske använder du redan Azure AD för att hantera användare i molnet som behöver åtkomst till Office 365 och andra SaaS-program, samt webb-appar som finns lokalt. Om du redan har Azure AD kan du använda den som en kontrollplanet som ger sömlös och säker åtkomst till dina lokala program. Eller så kanske du överväger fortfarande en flytt till molnet. I så fall kan du börja din resa till molnet genom att aktivera Application Proxy och tar det första steget mot att skapa en stark identitet grund.

Även om inte omfattande visar några av de saker som du kan aktivera genom att implementera App Proxy i ett scenario med hybridanvändning samexistens i listan nedan:

* Publicera lokala webbprogram externt i ett förenklat sätt utan en DMZ
* Ha stöd för enkel inloggning (SSO) mellan enheter, resurser och appar i molnet och lokalt
* Stöd för multifaktorautentisering för appar i molnet och lokalt
* Snabbt använda molnfunktioner med säkerheten för Microsoft Cloud
* Centralisera hantering av användarkonton
* Centralisera kontroll över identitet och säkerhet
* Automatiskt lägga till eller ta bort åtkomst till program baserat på gruppmedlemskap

Den här artikeln förklarar hur Azure AD och Application Proxy ge fjärranvändare en enkel inloggning (SSO)-upplevelse. Användare ansluta på ett säkert sätt till lokala appar utan en VPN-anslutning eller dual-homed servrar och brandväggsregler. Den här artikeln hjälper dig att förstå hur Application Proxy ger dig funktioner och fördelarna med molnet på dina lokala webbprogram. Här beskrivs också arkitekturen och topologier som är möjliga.

## <a name="remote-access-in-the-past"></a>Fjärråtkomst i förflutna

Tidigare var din kontrollplanet för att skydda interna resurser från angripare samtidigt som det förenklar åtkomst för fjärranvändare på DMZ eller perimeternätverket. Men VPN- och omvänd proxy-lösningar som distribueras i Perimeternätverket som används av externa klienter för att komma åt företagets resurser motsvarar inte världen. De har vanligtvis nackdelar:

* Maskinvarukostnader
* Underhålla säkerhet (korrigering, övervakning portar, osv.)
* Autentisera användare till gränsen
* Autentisera användare till webbservrar i perimeternätverket
* Underhålla VPN-åtkomst för fjärranvändare med distributionen och konfigurationen av VPN-klientprogrammet. Dessutom underhålla domänanslutna servrar i Perimeternätverket, vilket kan vara sårbara för angrepp utifrån.

I dagens molnorienterade värld, Azure AD som är bäst att styra vem och vad hämtar om ditt nätverk. Azure AD Application Proxy kan integreras med modern autentisering och molnbaserade program som SaaS-program och identitetsleverantörer. Den här integreringen kan användarna komma åt appar från var som helst. Inte bara är mer lämpliga App Proxy för dagens digitala arbetsplats, det är säkrare än VPN- och omvänd proxy-lösningar och enklare att implementera. Fjärranslutna användare kan komma åt dina lokala program på samma sätt som de har åtkomst till O365 och andra SaaS-appar som integreras med Azure AD. Du behöver inte ändra eller uppdatera dina program att arbeta med Application Proxy. Dessutom kräver App Proxy inte att öppna inkommande anslutningar via brandväggen. Med App Proxy du helt enkelt ställas glömmer bort det.

## <a name="the-future-of-remote-access"></a>Framtiden för fjärråtkomst

I dagens digitala arbetsplats arbeta användarna var som helst med flera enheter och appar. Endast konstant är användaridentitet. Det är därför det första steget till ett säkert nätverk idag är att använda [Identitetshantering för Azure Active Directorys](https://docs.microsoft.com/azure/security/security-identity-management-overview) funktioner som din säkerhet kontrollplanet. En modell som använder identiteter som din kontrollplanet vanligtvis består av följande komponenter:

* En identitetsprovider för att hålla reda på användare och användarrelaterad information.
* Enheten directory att underhålla en lista över enheter som har åtkomst till företagets resurser. Den här katalogen innehåller information om motsvarande enhet (till exempel typ av enhet, integritet osv.).
* Principtjänst utvärdering för att avgöra om en användare och enhet uppfyller den princip som av säkerhetsadministratörer.
* Möjlighet att bevilja eller neka åtkomst till organisationens resurser.

Med Application Proxy behåller Azure AD åtkomst till användare som behöver åtkomst till web apps publicerade lokalt och i molnet. Det ger en central hantering apparna. Måste inte, bör du också aktivera Azure AD villkorsstyrd åtkomst. Genom att definiera villkor för hur användare autentiseras och få åtkomst kan du ytterligare se till rätt personer har åtkomst till program.

**Obs:** Det är viktigt att förstå att Azure AD Application Proxy är avsedd som en VPN- eller omvänd proxy ersättning för centrala (eller fjärranslutna) användare som behöver åtkomst till interna resurser. Det är inte avsett för interna användare i företagsnätverket. Intern användare som använder onödigt Application Proxy kan medföra oväntade och oönskade prestandaproblem.

![Azure Active Directory och alla dina appar](media/what-is-application-proxy/azure-ad-and-all-your-apps.png)

### <a name="an-overview-of-how-app-proxy-works"></a>En översikt över hur Approxy fungerar

Programproxyn är en Azure AD-tjänst som du konfigurerar i Azure-portalen. Det gör att du kan publicera en extern offentlig HTTP/HTTPS-URL-slutpunkt i Azure-molnet som ansluter till en server-URL för interna program i din organisation. Dessa lokala webbappar kan integreras med Azure AD för enkel inloggning. Slutanvändare kan sedan få tillgång till lokala webbprogram på samma sätt som de har åtkomst till Office 365 och andra SaaS-appar.

Komponenterna i den här funktionen är tjänsten Application Proxy, som körs i molnet, Application Proxy connector, som är en förenklad agent som körs på en lokal server och Azure AD, som identitetsprovider. Alla tre komponenter fungerar tillsammans för att ge användare med en enkel inloggning för att få åtkomst till lokala webbprogram.

Efter inloggningen externa användare kan komma åt lokala webbprogram med hjälp av en bekant URL eller [MyApps åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access) från sina desktop eller iOS/MAC-enheter. Till exempel App Proxy kan ge fjärråtkomst och enkel inloggning för Remote Desktop, SharePoint-platser, Tableau, Qlik, Outlook på webben och line-of-business (LOB) program.

![Azure AD Application Proxy-arkitektur](media/what-is-application-proxy/azure-ad-application-proxy-architecture.png)

### <a name="authentication"></a>Autentisering

Det finns flera sätt att konfigurera ett program för enkel inloggning och den metod du väljer beror på vilken autentisering som används i ditt program. Programproxyn har stöd för följande typer av program:

* Webbprogram
* Web API: er som du vill exponera omfattande program på olika enheter
* Program som finns bakom en fjärrskrivbordsgateway
* Rich-klientprogram som är integrerade med Active Directory Authentication Library (ADAL)

App Proxy fungerar med appar som använder följande ursprungliga autentiseringsprotokoll:

* **[Integrerad Windows-autentisering (IWA)](application-proxy-configure-single-sign-on-with-kcd.md).** För IWA använda Application Proxy-kopplingar Kerberos-begränsad delegering (KCD) för att autentisera användare till Kerberos-programmet.

App Proxy stöder också följande autentiseringsprotokoll med integration från tredje part eller i scenarier med specifik konfiguration:

* [**Rubrikbaserad autentisering**](application-proxy-configure-single-sign-on-with-ping-access.md). Den här metoden inloggning använder en tjänst för autentiseringsmetoder från tredje part som kallas PingAccess och som ska användas när programmet använder rubriker för autentisering. I det här scenariot hanteras autentisering av PingAccess.
* [**Formulär - eller lösenordsbaserad autentisering**](application-proxy-configure-single-sign-on-password-vaulting.md). Med den här autentiseringsmetoden användare loggar in på programmet med ett användarnamn och lösenord för första gången som de har åtkomst till den. Efter den första inloggning tillhandahåller Azure AD användarnamnet och lösenordet till programmet. I det här scenariot hanteras autentisering av Azure AD.
* [**SAML-autentisering**](application-proxy-configure-single-sign-on-on-premises-apps.md). SAML-baserad enkel inloggning har stöd för program som använder SAML 2.0 eller WS-Federation-protokoll. Med SAML enkel inloggning autentiserar Azure AD till programmet med hjälp av användarens Azure AD-konto.

Mer information om metoder som stöds finns i [välja en metod för enkel inloggning](what-is-single-sign-on.md#choosing-a-single-sign-on-method).

### <a name="security-benefits"></a>Säkerhetsfördelarna

Lösning för fjärråtkomst som erbjuds av Application Proxy och Azure AD stöder flera säkerhetsfördelarna kunder kan dra nytta av, inklusive:

* **Autentiserad åtkomst**. Programproxyn är bäst lämpad att publicera program med [förautentisering](application-proxy-security.md#authenticated-access) så att endast autentiserade anslutningar når ditt nätverk. För program som publiceras med förautentisering, tillåts ingen trafik att passera App Proxy-tjänsten till din lokala miljö, utan giltig token. Föregående autentisering blockerar till sin natur, ett stort antal riktade attacker som endast autentiserade identiteter har åtkomst till backend-programmet.
* **Villkorlig åtkomst**. Rikare principkontroller kan tillämpas innan anslutningar till nätverket upprättas. Med villkorlig åtkomst kan du ange begränsningar för trafiken som du tillåter för att träffa backend-applikationer. Du kan skapa principer som begränsar inloggningar baserat på plats, styrkan hos autentisering och användarprofilen för risken. När villkorlig åtkomst utvecklas, läggs fler kontroller för att ge extra säkerhet som integrering med Microsoft Cloud App Security (MCAS). MCAS-integrering kan du konfigurera ett lokalt program för [realtidsövervakning](application-proxy-integrate-with-microsoft-cloud-application-security.md) genom att använda villkorlig åtkomst för att övervaka och kontrollera sessioner i realtid baserat på principer för villkorlig åtkomst.
* **Trafik avslutning**. All trafik till backend-applikationer avslutas vid Application Proxy-tjänst i molnet, medan sessionen upprättas igen med backend-servern. Den här strategin för anslutning innebär att ditt serverdels-servrarna inte exponeras direkt HTTP-trafik. De är bättre skyddade mot riktade (denial of service) för DoS-attacker eftersom brandväggen inte är utsatt för en attack.
* **All åtkomst är utgående**. Application Proxy-anslutningar använder bara utgående anslutningar till Application Proxy-tjänsten i molnet över portarna 80 och 443. Med inga inkommande anslutningar finns behöver inte öppna portar i brandväggen för inkommande anslutningar eller komponenter i Perimeternätverket. Alla anslutningar är utgående och via en säker kanal.
* **Säkerhetsanalyser och Machine Learning (ML) baserade intelligence**. Eftersom det är en del av Azure Active Directory Application Proxy kan utnyttja [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview) (kräver [Premium P2 licensiering](https://azure.microsoft.com/pricing/details/active-directory/)). Azure AD Identity Protection kombinerar säkerhetsinformation för machine learning med datafeeds från Microsofts [Digital Crimes Unit](https://news.microsoft.com/stories/cybercrime/index.html) och [Microsoft Security Response Center](https://www.microsoft.com/msrc) att proaktivt identifiera kapade konton. Identity Protection ger realtidsskydd mot med hög risk inloggningar. Det tar i beräkningen faktorer som åtkomst från angripna enheter via maskera nätverk, eller från ovanliga och sannolikt inte kommer att öka risken profilen för en session. Den här profilen för risker används för realtidsskydd. Många av dessa rapporter och händelser är redan tillgänglig via ett API för integrering med SIEM-system.

* **Fjärråtkomst som en tjänst**. Du behöver bekymra dig om underhåll och korrigeringar på lokala servrar för att aktivera fjärråtkomst. Programproxyn är en tjänst för internet skala som Microsoft äger, så att du alltid får de senaste säkerhetskorrigeringar och uppgraderingar. Okorrigerad programvara fortfarande står för ett stort antal attacker. Enligt den avdelning av Inrikessäkerhetsdepartement, så många som [85 procent av riktade attacker är möjligt](https://www.us-cert.gov/ncas/alerts/TA15-119A). Med den här tjänstmodellen har du inte utföra tung bördan med att hantera din edge-servrar längre och avkoda att korrigera dem efter behov.

* **Intune-integrering**. Företagets trafiken dirigeras separat från personliga trafik med Intune. Programproxy säkerställer att företagets trafik autentiseras. [Application Proxy och Intune Managed Browser](https://docs.microsoft.com/intune/app-configuration-managed-browser#how-to-configure-application-proxy-settings-for-protected-browsers) funktionen kan också användas tillsammans att fjärranslutna användare säker åtkomst till interna webbplatser från iOS- och Android-enheter.

### <a name="roadmap-to-the-cloud"></a>Molnet

En annan stor fördel med att implementera Application Proxy förlänger Azure AD till din lokala miljö. I själva verket är implementera App Proxy ett viktigt steg i att flytta din organisation och dina appar till molnet. Genom att flytta till molnet och från lokal autentisering, minska storleken på plats och Använd Azure AD identity management-funktioner som din kontrollplanet. Med minimala kodändringar eller inga uppdateringar att befintliga program som du har åtkomst till funktioner som enkel inloggning, multifaktorautentisering och central hantering i molnet. Installera de nödvändiga komponenterna på App Proxy är en enkel process för att upprätta ett ramverk för fjärråtkomst. Och genom att flytta till molnet, du har åtkomst till den senaste Azure AD-funktioner, uppdateringar och funktioner, t.ex hög tillgänglighet och den för haveriberedskap.

Mer information om hur du migrerar dina appar till Azure AD finns i [migrera din program till Azure Active Directory](https://aka.ms/migrateapps/whitepaper) white paper om.

## <a name="architecture"></a>Arkitektur

I följande diagram illustreras i allmänhet hur Azure AD-autentiseringstjänster och Application Proxy fungerar tillsammans för att tillhandahålla enkel inloggning till lokala program för slutanvändare.

![Azure AD Application Proxy-autentiseringsflödet](media/what-is-application-proxy/azure-ad-application-proxy-authentication-flow.png)

1. När användaren har åtkomst till programmet via en slutpunkt, omdirigeras användaren till inloggningssidan för Azure AD. Om du har konfigurerat principer för villkorlig åtkomst kan kontrolleras särskilda villkor just nu att se till att du uppfyller organisationens säkerhetskrav.
2. Efter en lyckad inloggning skickar Azure AD en token till användarens klientenheten.
3. Klienten skickar token till tjänsten Application Proxy, som hämtar användarens huvudnamn (UPN) och säkerhet huvudnamn (SPN) från token.
4. Programproxy vidarebefordrar begäran, som hämtas av programproxyn [connector](application-proxy-connectors.md).
5. Anslutningen utför ytterligare autentisering krävs för användarens räkning (*valfritt beroende på autentiseringsmetod*), begär den interna slutpunkten på programservern och skickar en begäran till lokalt programmet.
6. Svaret från programservern skickas via anslutningen till Application Proxy-tjänsten.
7. Svaret skickas från programproxytjänsten för användaren.

|**Komponent**|**Beskrivning**|
|:-|:-|
|Slutpunkt|Slutpunkten är en URL eller en [slutanvändarportal](end-user-experiences.md). Användarna kan nå program medan utanför ditt nätverk genom att gå till en extern URL. Användare i nätverket kan komma åt programmet via en URL eller ett slutanvändarportal. När användare går till en av de här slutpunkterna, de autentiseras i Azure AD och sedan dirigeras via anslutningen så att dina lokala program.|
|Azure AD|Azure AD autentiserar med hjälp av klientkatalogen lagras i molnet.|
|Tjänsten för Application Proxy|Den här Application Proxy-tjänsten körs i molnet som en del av Azure AD. Den skickar inloggnings-token från användaren till Application Proxy Connector. Programproxy vidarebefordrar tillgänglig rubriker på begäran och anger rubriker enligt dess protokoll till klientens IP-adress. Om den inkommande begäran till proxyn har redan den rubriken, klientens IP-adress har lagts till i slutet av CSV-listan som evalueras till värdet för sidhuvudet.|
|Application Proxy connector|Anslutningen är en förenklad agent som körs på en Windows-Server i ditt nätverk. Anslutningen hanterar kommunikationen mellan Application Proxy-tjänst i molnet och lokalt program. Anslutningsappen använder bara utgående anslutningar så att du inte behöver öppna några ingående portar eller lägga något i Perimeternätverket. Anslutningarna är tillståndslösa och hämta information från molnet efter behov. Mer information om anslutningar kan, liksom hur de belastningsutjämna och autentisera, se [alternativ för Azure AD-programproxyn kopplingar](application-proxy-connectors.md).|
|Active Directory (AD)|Active Directory körs lokalt för att utföra autentisering för domänkonton. När enkel inloggning har konfigurerats, kommunicerar anslutningen med AD för att utföra ytterligare autentisering krävs.|
|Lokalt program|Slutligen kan användaren komma åt ett lokalt program.|

Azure AD Application Proxy består av molnbaserad Application Proxy-tjänst och en lokal anslutning. Anslutningstjänsten lyssnar efter förfrågningar från Application Proxy-tjänsten och hanterar anslutningar till de interna program. Det är viktigt att Observera att all kommunikation sker över SSL och alltid härrör från anslutningen till Application Proxy-tjänsten. Det vill säga är kommunikationen utgående endast. Anslutningsappen använder ett klientcertifikat för att autentisera till tjänsten Application Proxy för alla anrop. Det enda undantaget anslutningssäkerhet är det första installationssteget där Klientcertifikatet har upprättats. Se programproxyn [Under huven](application-proxy-security.md#under-the-hood) för mer information.

### <a name="application-proxy-connectors"></a>Anslutningar för programproxy

[Programproxyanslutningar](application-proxy-connectors.md) lightweight agenter som har distribuerats lokalt som underlättar den utgående anslutningen till Application Proxy-tjänst i molnet. Kopplingar måste installeras på en Windows-Server som har åtkomst till backend-applikationer. Användare som ansluter till Molntjänsten för App Proxy som vägar som sin trafik till appar via kopplingar som visas nedan.

![Azure AD Application Proxy-nätverksanslutningar](media/what-is-application-proxy/azure-ad-application-proxy-network-connections.png)

Installation och registrering mellan en anslutningsapp och App Proxy-tjänsten utförs på följande sätt:

1. IT-administratören öppnar portarna 80 och 443 för utgående trafik och ger åtkomst till flera URL: er som krävs av anslutningstjänsten, App Proxy-tjänsten och Azure AD.
2. Administratören loggar in på Azure-portalen och kör en körbar fil för att installera anslutningen på en lokal Windows server.
3. Anslutningen börjar ”lyssna” till App Proxy-tjänsten.
4. Administratören lägger till lokala program till Azure AD och konfigurerar inställningar som de URL: er måste du ansluta till sina appar.

Mer information finns i [planera en distribution av Azure AD Application Proxy](application-proxy-deployment-plan.md).

Vi rekommenderar att du alltid distribuera flera anslutningsprogram för redundans och skalning. Anslutningsappar, tillsammans med tjänsten, kan ta hand om alla aktiviteter för hög tillgänglighet och har lagts till eller tas bort dynamiskt. Varje gång en ny begäran kommer dirigeras till en av de kopplingar som är tillgänglig. När en koppling är igång, förblir den aktiv när den ansluter till tjänsten. Om en anslutning är inte tillgänglig för tillfället, kan de inte svara på den här trafiken. Oanvända anslutningar taggas som inaktiva och tas bort efter 10 dagar av inaktivitet.

Kopplingar avsöka också servern för att ta reda på om det finns en nyare version av anslutningen. Även om du gör en manuell uppdatering måste uppdatera anslutningsappar automatiskt så länge Application Proxy Connector Updater-tjänsten körs. För klienter med flera anslutningar kan rikta automatiska uppdateringar en anslutning i taget i varje grupp för att förhindra avbrott i din miljö.

**Obs!** Du kan övervaka programproxyn [sidan för version tidigare](application-proxy-release-version-history.md) vill meddelas när det finns uppdateringar genom att prenumerera på dess RSS-feed.

Varje programproxy-kopplingen har tilldelats en [anslutningsgrupp](application-proxy-connector-groups.md). Kopplingar i anslutningsgruppen samma fungerar som en enda enhet för hög tillgänglighet och belastningsutjämning. Du kan skapa nya grupper, tilldela kopplingar till dem i Azure portal och sedan tilldela specifika anslutningsappar för att hantera specifika program. Vi rekommenderar att du har minst två anslutningsappar i varje anslutningsgrupp för hög tillgänglighet.

Anslutningsapp-grupper är användbara när du behöver stöd för följande scenarier:

* Publicering av geografisk appar
* Segmentering/programisolering
* Publicera web apps som körs i molnet eller lokalt

Mer information om att välja var du vill installera dina anslutningsprogram och optimera ditt nätverk Se [Network topologiöverväganden när du använder Azure Active Directory Application Proxy](application-proxy-network-topology.md).

## <a name="other-use-cases"></a>Andra användningsfall

I det här läget har vi fokuserat på att använda programproxy för att publicera lokala appar externt vid aktivering av enkel inloggning till alla dina appar för molnet och lokalt. Det finns dock andra användningsområden för App Proxy som är värda att nämna. Dessa är:

* **Säkert publicera REST API: er**. När du har affärslogik eller API: er som kör en lokal eller värdbaserad på virtuella datorer i molnet, ger Application Proxy en offentlig slutpunkt för API-åtkomst. API-slutpunkt-åtkomst kan du styra autentisering och auktorisering utan inkommande portar. Det ger ökad säkerhet via Azure AD Premium-funktioner, till exempel multifaktorautentisering och enhetsbaserad villkorlig åtkomst för stationära datorer, iOS, MAC och Android-enheter med Intune. Mer information finns i [så här aktiverar du interna klientprogram att interagera med proxy-program](application-proxy-configure-native-client-application.md) och [skydda ett API med hjälp av OAuth 2.0 med Azure Active Directory och API Management](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad).
* **Fjärrskrivbordstjänster** **(RDS)** . Standard RDS-distributioner kräva öppna inkommande anslutningar. Men den [RDS-distribution med Application Proxy](application-proxy-integrate-with-remote-desktop-services.md) har en permanent utgående anslutning från den server som kör kopplingstjänsten. På så sätt kan du erbjuda fler program för slutanvändare genom att publicera lokala program via Fjärrskrivbordstjänster. Du kan också minska risken för angrepp på distributionen med en begränsad uppsättning tvåstegsverifiering och kontroller för villkorlig åtkomst till Fjärrskrivbordstjänster.
* **Publicera program som ansluter via WebSockets**. Stöd för med [Qlik Sense](application-proxy-qlik.md) finns i offentlig förhandsversion och kommer att utökas till andra appar i framtiden.
* **Aktivera interna klientprogram att interagera med proxy-program**. Du kan använda Azure AD-programproxy för att publicera webbappar, men det kan också användas till att publicera [interna klientprogram](application-proxy-configure-native-client-application.md) som är konfigurerade med Azure AD Authentication Library (ADAL). Interna klientprogram skiljer sig från web apps eftersom de är installerade på en enhet, medan webbappar som kan nås via en webbläsare.

## <a name="conclusion"></a>Sammanfattning

Hur vi arbetar och de verktyg som vi använder ting ändras snabbt. Hur organisationer hanterar med fler anställda sina egna enheter för att arbeta och genomträngande användning av Software-as-a-Service (SaaS)-program, och skydda sina data måste också utvecklas. Företag fungerar inte längre endast i sina egna väggar, skyddas av en moat som omger sina kantlinje. Data skickas till fler platser än någonsin tidigare--över både lokala och molnbaserade miljöer. Denna utveckling har hjälpt till att öka användarnas produktivitet och möjligheten att samarbeta, men den gör det också skydda känsliga data mer utmanande.

Om du använder Azure AD för att hantera användare i ett scenario med hybridanvändning samexistens eller är intresserad av att startar din resa till molnet, implementera Azure AD Application Proxy kan hjälpa att minska storleken på din lokala avtryck genom att tillhandahålla fjärråtkomst åtkomst till som en tjänst.

Organisationer bör börja dra fördel av App Proxy och idag kan dra nytta av följande fördelar:

* Publicera lokala appar externt utan den overhead som förknippas med att underhålla traditionell VPN eller andra lokala webbpublicering lösningar och DMZ-metod
* Enkel inloggning till alla program, oavsett om de är Office 365 eller andra SaaS-appar och inklusive lokala program
* Skala molnsäkerhet där Azure AD använder Office 365-telemetri för att förhindra obehörig åtkomst
* Intune-integrering för att se till att företagets trafik autentiseras
* Centralisering av hantering av användarkonton
* Automatiska uppdateringar ska se till att du har de senaste uppdateringarna för säkerhet
* Nya funktioner när de blir tillgängliga; senaste som stöd för SAML enkel inloggning och mer detaljerade hanteringen av program cookies

## <a name="next-steps"></a>Nästa steg

* Information om planering, använda och hantera Azure AD Application Proxy finns i [planera en distribution av Azure AD Application Proxy](application-proxy-deployment-plan.md).
* Om du vill schemalägga en direktsänd demonstration eller skaffa en kostnadsfri 90-dagars utvärderingsversion för utvärdering, se [komma igång med Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial).
