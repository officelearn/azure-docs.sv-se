---
title: Publicera lokala appar med Azure AD Application Proxy
description: Förstå varför du använder Programproxy för att publicera lokala webbprogram externt till fjärranvändare. Lär dig mer om programproxyarkitektur, anslutningsappar, autentiseringsmetoder och säkerhetsfördelar.
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
ms.openlocfilehash: 7047dfd0f02ffe95dcacfdf4ddc014047a338513
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79481202"
---
# <a name="using-azure-ad-application-proxy-to-publish-on-premises-apps-for-remote-users"></a>Använda Azure AD Application Proxy för att publicera lokala appar för fjärranvändare

Azure Active Directory (Azure AD) erbjuder många funktioner för att skydda användare, appar och data i molnet och lokalt. Azure AD Application Proxy-funktionen kan implementeras av IT-proffs som vill publicera lokala webbprogram externt. Fjärranvändare som behöver åtkomst till interna appar kan sedan komma åt dem på ett säkert sätt.

Möjligheten att säkert komma åt interna appar utanför nätverket blir ännu mer kritisk på den moderna arbetsplatsen. Med scenarier som BYOD (Bring Your Own Device) och mobila enheter utmanas IT-proffs att uppfylla två mål:

* Ge slutanvändarna möjlighet att vara produktiva när som helst och var som helst
* Skydda företagets tillgångar hela tiden

Många organisationer tror att de har kontroll och skydd när resurser finns inom gränserna för deras företagsnätverk. Men på dagens digitala arbetsplats har den gränsen utökats med hanterade mobila enheter och resurser och tjänster i molnet. Nu måste du hantera komplexiteten i att skydda användarnas identiteter och data som lagras på deras enheter och appar.

Kanske använder du redan Azure AD för att hantera användare i molnet som behöver komma åt Office 365 och andra SaaS-program, samt webbappar som finns lokalt. Om du redan har Azure AD kan du utnyttja det som ett kontrollplan för att tillåta sömlös och säker åtkomst till dina lokala program. Eller, kanske du fortfarande överväger en flytt till molnet. Om så är fallet kan du börja din resa till molnet genom att implementera Application Proxy och ta det första steget mot att bygga en stark identitetsgrund.

Även om listan nedan inte är heltäckande, illustrerar listan nedan några av de saker du kan aktivera genom att implementera App Proxy i ett hybridsamexistencescenario:

* Publicera lokala webbappar externt på ett förenklat sätt utan DMZ
* Stöd för enkel inloggning (SSO) på enheter, resurser och appar i molnet och lokalt
* Stöd för multifaktorautentisering för appar i molnet och lokalt
* Utnyttja snabbt molnfunktioner med säkerheten i Microsoft Cloud
* Centralisera hantering av användarkonton
* Centralisera kontrollen av identitet och säkerhet
* Lägga till eller ta bort användaråtkomst automatiskt till program baserat på gruppmedlemskap

I den här artikeln beskrivs hur Azure AD och Programproxy ger fjärranvändare en enkel inloggningsupplevelse (SSO). Användare ansluter säkert till lokala appar utan VPN- eller dual-homed-servrar och brandväggsregler. Den här artikeln hjälper dig att förstå hur Application Proxy ger funktioner och säkerhetsfördelar i molnet till dina lokala webbprogram. Den beskriver också den arkitektur och topologier som är möjliga.

## <a name="remote-access-in-the-past"></a>Fjärråtkomst tidigare

Tidigare var ditt kontrollplan för att skydda interna resurser från angripare samtidigt som åtkomsten för fjärranvändare underlättades i DMZ- eller perimeternätverket. Men VPN- och omvänd proxylösningar som distribueras i DMZ som används av externa klienter för att komma åt företagsresurser är inte anpassade till molnvärlden. De lider vanligtvis av följande nackdelar:

* Maskinvarukostnader
* Upprätthålla säkerheten (korrigering, övervakningsportar osv.)
* Autentisera användare vid kanten
* Autentisera användare till webbservrar i perimeternätverket
* Upprätthålla VPN-åtkomst för fjärranvändare med distribution och konfiguration av VPN-klientprogramvara. Dessutom underhåller domänanslutna servrar i DMZ, som kan vara sårbara för externa attacker.

I dagens moln-första värld är Azure AD bäst lämpad att styra vem och vad som kommer in i ditt nätverk. Azure AD Application Proxy integreras med modern autentisering och molnbaserad teknik, till exempel SaaS-program och identitetsleverantörer. Med den här integreringen kan användare komma åt appar var som helst. App Proxy är inte bara mer lämpad för dagens digitala arbetsplats, den är säkrare än VPN- och reverse proxylösningar och enklare att implementera. Fjärranvändare kan komma åt dina lokala program på samma sätt som de får åtkomst till O365 och andra SaaS-appar som är integrerade med Azure AD. Du behöver inte ändra eller uppdatera dina program för att fungera med programproxy. App Proxy kräver inte heller att du öppnar inkommande anslutningar via brandväggen. Med App Proxy ställer du helt enkelt in den och glömmer den.

## <a name="the-future-of-remote-access"></a>Framtiden för fjärråtkomst

På dagens digitala arbetsplats arbetar användarna var som helst med flera enheter och appar. Den enda konstanten är användaridentitet. Därför är det första steget till ett säkert nätverk idag att använda [Azure AD:s identitetshanteringsfunktioner](https://docs.microsoft.com/azure/security/security-identity-management-overview) som ditt säkerhetskontrollplan. En modell som använder identitet som kontrollplan består vanligtvis av följande komponenter:

* En identitetsleverantör för att hålla reda på användare och användarrelaterad information.
* Enhetskatalog för att underhålla en lista över enheter som har åtkomst till företagsresurser. Denna katalog innehåller motsvarande enhetsinformation (till exempel typ av enhet, integritet etc.).
* Principutvärderingstjänst för att avgöra om en användare och enhet överensstämmer med den princip som anges av säkerhetsadministratörer.
* Möjligheten att bevilja eller neka åtkomst till organisationsresurser.

Med Application Proxy håller Azure AD reda på användare som behöver komma åt webbappar som publiceras lokalt och i molnet. Det ger en central hanteringsplats för dessa appar. Även om det inte krävs, rekommenderar vi att du även aktiverar Azure AD Villkorlig åtkomst. Genom att definiera villkor för hur användare autentiserar och får åtkomst säkerställer du att rätt personer har åtkomst till program.

**Anm.:** Det är viktigt att förstå att Azure AD Application Proxy är avsedd som en VPN eller omvänd proxy ersättning för roaming (eller fjärr) användare som behöver åtkomst till interna resurser. Den är inte avsedd för interna användare i företagsnätverket. Interna användare som i onödan använder Application Proxy kan introducera oväntade och oönskade prestandaproblem.

![Azure Active Directory och alla dina appar](media/what-is-application-proxy/azure-ad-and-all-your-apps.png)

### <a name="an-overview-of-how-app-proxy-works"></a>En översikt över hur App Proxy fungerar

Programproxy är en Azure AD-tjänst som du konfigurerar i Azure-portalen. Det gör att du kan publicera en extern offentlig HTTP/HTTPS-URL-slutpunkt i Azure Cloud, som ansluter till en intern programserver-URL i din organisation. Dessa lokala webbappar kan integreras med Azure AD för att stödja enkel inloggning. Slutanvändare kan sedan komma åt lokala webbappar på samma sätt som de får åtkomst till Office 365 och andra SaaS-appar.

Komponenter i den här funktionen inkluderar application proxy-tjänsten, som körs i molnet, Application Proxy-anslutningen, som är en lättagent som körs på en lokal server, och Azure AD, som är identitetsprovidern. Alla tre komponenterna arbetar tillsammans för att ge användaren en enda inloggningsupplevelse för att komma åt lokala webbprogram.

När externa användare har loggat in kan de komma åt lokala webbprogram med hjälp av en välbekant URL eller [åtkomstpanelen för MyApps](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access) från sina stationära eller iOS/MAC-enheter. Appproxy kan till exempel ge fjärråtkomst och enkel inloggning till lob-program för fjärrskrivbord, SharePoint-webbplatser, Tabell, Qlik, Outlook på webben och LOB-program (line-of-business).

![Azure AD-programproxyarkitektur](media/what-is-application-proxy/azure-ad-application-proxy-architecture.png)

### <a name="authentication"></a>Autentisering

Det finns flera sätt att konfigurera ett program för enkel inloggning och vilken metod du väljer beror på vilken autentisering programmet använder. Programproxy stöder följande typer av program:

* Webbprogram
* Webb-API:er som du vill exponera för avancerade program på olika enheter
* Program som finns bakom en gateway för fjärrskrivbord
* Omfattande klientappar som är integrerade med Active Directory Authentication Library (ADAL)

AppProxy fungerar med appar som använder följande inbyggda autentiseringsprotokoll:

* **[Integrerad Windows-autentisering (IWA)](application-proxy-configure-single-sign-on-with-kcd.md).** För IWA använder Application Proxy-kopplingarNa Kerberos Constrained Delegation (KCD) för att autentisera användare till Kerberos-programmet.

Appproxy stöder även följande autentiseringsprotokoll med tredjepartsintegrering eller i specifika konfigurationsscenarier:

* [**Rubrikbaserad autentisering**](application-proxy-configure-single-sign-on-with-ping-access.md). Den här inloggningsmetoden använder en autentiseringstjänst från tredje part som heter PingAccess och används när programmet använder rubriker för autentisering. I det här fallet hanteras autentisering av PingAccess.
* [**Formulär- eller lösenordsbaserad autentisering**](application-proxy-configure-single-sign-on-password-vaulting.md). Med den här autentiseringsmetoden loggar användarna in på programmet med ett användarnamn och lösenord första gången de kommer åt det. Efter den första inloggningen levererar Azure AD användarnamnet och lösenordet till programmet. I det här fallet hanteras autentisering av Azure AD.
* [**SAML-autentisering**](application-proxy-configure-single-sign-on-on-premises-apps.md). SAML-baserad enkel inloggning stöds för program som använder antingen SAML 2.0- eller WS-Federation-protokoll. Med SAML enkel inloggning autentiserar Azure AD till programmet med hjälp av användarens Azure AD-konto.

Mer information om metoder som stöds finns i [Välja en enda inloggningsmetod](what-is-single-sign-on.md#choosing-a-single-sign-on-method).

### <a name="security-benefits"></a>Säkerhetsfördelar

Fjärråtkomstlösningen som erbjuds av Application Proxy och Azure AD stöder flera säkerhetsfördelar som kunder kan dra nytta av, inklusive:

* **Autentrad åtkomst**. Programproxy är bäst lämpad att publicera program med [förautentisering för](application-proxy-security.md#authenticated-access) att säkerställa att endast autentiserade anslutningar träffar nätverket. För program som publiceras med förautentisering tillåts ingen trafik att passera via appproxytjänsten till din lokala miljö, utan en giltig token. Pre-autentisering, till sin natur, blockerar ett betydande antal riktade attacker, eftersom endast autentiserade identiteter kan komma åt backend-programmet.
* **Villkorlig åtkomst**. Rikare principkontroller kan tillämpas innan anslutningar till nätverket upprättas. Med villkorlig åtkomst kan du definiera begränsningar för den trafik som du tillåter för att slå till mot serveringsprogrammet. Du skapar principer som begränsar inloggningar baserat på plats, autentiseringsstyrka och användarriskprofil. I takt med att villkorlig åtkomst utvecklas läggs fler kontroller till för att ge ytterligare säkerhet, till exempel integrering med MICROSOFT Cloud App Security (MCAS). MCAS-integrering gör att du kan konfigurera ett lokalt program för [realtidsövervakning](application-proxy-integrate-with-microsoft-cloud-application-security.md) genom att utnyttja villkorlig åtkomst för att övervaka och styra sessioner i realtid baserat på principer för villkorlig åtkomst.
* **Trafikavslutning**. All trafik till serverdaprogrammet avslutas vid tjänsten Programproxy i molnet medan sessionen återupprättas med serverdaservern. Den här anslutningsstrategin innebär att serveringsservrarna inte utsätts för direkt HTTP-trafik. De är bättre skyddade mot riktade DoS-attacker (denial-of-service) eftersom brandväggen inte är under attack.
* **All åtkomst är utgående**. Application Proxy-anslutningsapparna använder endast utgående anslutningar till application proxy-tjänsten i molnet över portarna 80 och 443. Utan inkommande anslutningar behöver du inte öppna brandväggsportar för inkommande anslutningar eller komponenter i DMZ. Alla anslutningar är utgående och över en säker kanal.
* **Security Analytics och Machine Learning (ML) baserad intelligens**. Eftersom det är en del av Azure Active Directory kan Programproxy utnyttja [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview) (kräver Premium [P2-licensiering).](https://azure.microsoft.com/pricing/details/active-directory/) Azure AD Identity Protection kombinerar säkerhetsinformation för maskininlärning med dataflöden från Microsofts [enhet för digitala brott](https://news.microsoft.com/stories/cybercrime/index.html) och Microsoft Security Response [Center](https://www.microsoft.com/msrc) för att proaktivt identifiera komprometterade konton. Identity Protection erbjuder realtidsskydd mot högrisk inloggningar. Det tar hänsyn till faktorer som åtkomster från infekterade enheter, genom anonymisering av nätverk, eller från atypiska och osannolika platser för att öka riskprofilen för en session. Den här riskprofilen används för realtidsskydd. Många av dessa rapporter och händelser är redan tillgängliga via ett API för integrering med dina SIEM-system.

* **Fjärråtkomst som en tjänst**. Du behöver inte oroa dig för att underhålla och korrigera lokala servrar för att möjliggöra fjärråtkomst. Application Proxy är en internetskala tjänst som Microsoft äger, så du får alltid de senaste säkerhetskorrigeringarna och uppgraderingarna. Opatrullerad programvara står fortfarande för ett stort antal attacker. Enligt Department of Homeland Security, så många som [85 procent av riktade attacker kan förebyggas](https://www.us-cert.gov/ncas/alerts/TA15-119A). Med den här tjänstemodellen behöver du inte bära den tunga bördan av att hantera dina kantservrar längre och klättra för att lappa dem efter behov.

* **Intune integration**. Med Intune dirigeras företagstrafiken separat från personlig trafik. Programproxy säkerställer att företagstrafiken autentiseras. [Application Proxy och Intune Managed Browser](https://docs.microsoft.com/intune/app-configuration-managed-browser#how-to-configure-application-proxy-settings-for-protected-browsers) kapacitet kan också användas tillsammans för att fjärranvändare att säkert komma åt interna webbplatser från iOS och Android-enheter.

### <a name="roadmap-to-the-cloud"></a>Färdplan för molnet

En annan stor fördel med att implementera programproxy är att utöka Azure AD till din lokala miljö. Att implementera App Proxy är ett viktigt steg för att flytta din organisation och dina appar till molnet. Genom att flytta till molnet och bort från lokal autentisering minskar du ditt lokala fotavtryck och använder Azure AD:s identitetshanteringsfunktioner som kontrollplan. Med minimala eller inga uppdateringar av befintliga program har du åtkomst till molnfunktioner som enkel inloggning, multifaktorautentisering och central hantering. Installera nödvändiga komponenter till App Proxy är en enkel process för att upprätta en fjärråtkomst ramverk. Och genom att flytta till molnet har du tillgång till de senaste Azure AD-funktionerna, uppdateringar och funktioner, till exempel hög tillgänglighet och haveriberedskap.

Mer information om hur du migrerar dina appar till Azure AD finns i faktabladet [Migrera dina program till Azure Active Directory.](https://aka.ms/migrateapps/whitepaper)

## <a name="architecture"></a>Arkitektur

Följande diagram illustrerar i allmänhet hur Azure AD-autentiseringstjänster och programproxy fungerar tillsammans för att tillhandahålla enkel inloggning till lokala program till slutanvändare.

![Autentiseringsflöde för Azure AD-programproxy](media/what-is-application-proxy/azure-ad-application-proxy-authentication-flow.png)

1. När användaren har åtkomst till programmet via en slutpunkt omdirigeras användaren till inloggningssidan för Azure AD. Om du har konfigurerat principer för villkorlig åtkomst kontrolleras specifika villkor för närvarande för att säkerställa att du uppfyller organisationens säkerhetskrav.
2. Efter en lyckad inloggning skickar Azure AD en token till användarens klientenhet.
3. Klienten skickar token till application proxy-tjänsten, som hämtar användarens huvudnamn (UPN) och säkerhetsobjektet (SPN) från token.
4. Application Proxy vidarebefordrar begäran, som plockas upp av Application [Proxy-anslutningen](application-proxy-connectors.md).
5. Anslutningen utför all ytterligare autentisering som krävs för användarens räkning (*Valfritt beroende på autentiseringsmetod*), begär den interna slutpunkten för programservern och skickar begäran till det lokala programmet.
6. Svaret från programservern skickas via anslutningen till tjänsten Application Proxy.
7. Svaret skickas från tjänsten Programproxy till användaren.

|**Komponent**|**Beskrivning**|
|:-|:-|
|Slutpunkt|Slutpunkten är en URL eller en [slutanvändarportal](end-user-experiences.md). Användare kan nå program utanför nätverket genom att komma åt en extern URL. Användare i nätverket kan komma åt programmet via en URL eller en slutanvändarportal. När användare går till en av dessa slutpunkter autentiseras de i Azure AD och dirigeras sedan via kopplingen till det lokala programmet.|
|Azure AD|Azure AD utför autentiseringen med hjälp av klientkatalogen som lagras i molnet.|
|Tjänsten Programproxy|Den här programproxytjänsten körs i molnet som en del av Azure AD. Den skickar inloggningstoken från användaren till Application Proxy Connector. Application Proxy vidarebefordrar alla tillgängliga rubriker på begäran och ställer in rubrikerna enligt protokollet, till klientens IP-adress. Om den inkommande begäran till proxyn redan har det huvudet läggs klient-IP-adressen till i slutet av den kommaavgränsade listan som är värdet för huvudet.|
|Proxy-anslutning för program|Anslutningen är en lättviktsagent som körs på en Windows Server i nätverket. Anslutningsappen hanterar kommunikationen mellan programproxytjänsten i molnet och det lokala programmet. Anslutningen använder endast utgående anslutningar, så du behöver inte öppna några inkommande portar eller placera något i DMZ. Kopplingarna är tillståndslösa och hämtar information från molnet efter behov. Mer information om kopplingar, till exempel hur de belastningssaldo och autentisera finns i [Förstå Azure AD Application Proxy-kopplingar](application-proxy-connectors.md).|
|Active Directory (AD)|Active Directory körs lokalt för att utföra autentisering för domänkonton. När enkel inloggning är konfigurerad kommunicerar kopplingen med AD för att utföra eventuell ytterligare autentisering som krävs.|
|Lokalt program|Slutligen kan användaren komma åt ett lokalt program.|

Azure AD Application Proxy består av den molnbaserade programproxytjänsten och en lokal anslutningsapp. Anslutningen lyssnar efter begäranden från tjänsten Programproxy och hanterar anslutningar till interna program. Det är viktigt att notera att all kommunikation sker via TLS och alltid kommer från anslutningen till application proxy-tjänsten. Det vill än, kommunikation är utgående bara. Anslutningen använder ett klientcertifikat för att autentisera till application proxy-tjänsten för alla anrop. Det enda undantaget från anslutningssäkerheten är det första installationssteget där klientcertifikatet upprättas. Se application proxy [under huven](application-proxy-security.md#under-the-hood) för mer information.

### <a name="application-proxy-connectors"></a>Proxyanslutningar för program

[Application Proxy-kopplingar](application-proxy-connectors.md) är lätta agenter som distribueras lokalt och som underlättar den utgående anslutningen till application proxy-tjänsten i molnet. Anslutningsapparna måste vara installerade på en Windows Server som har åtkomst till serverdprogrammet. Användare ansluter till molntjänsten App Proxy som dirigerar sin trafik till apparna via apparna enligt bilden nedan.

![Azure AD Application Proxy nätverksanslutningar](media/what-is-application-proxy/azure-ad-application-proxy-network-connections.png)

Installation och registrering mellan en anslutningsapp och appproxytjänsten utförs på följande sätt:

1. IT-administratören öppnar portarna 80 och 443 för utgående trafik och ger åtkomst till flera url:er som behövs av anslutningen, appproxytjänsten och Azure AD.
2. Administratören loggar in på Azure-portalen och kör en körbar för att installera anslutningen på en lokal Windows-server.
3. Anslutningen börjar "lyssna" på appproxytjänsten.
4. Administratören lägger till det lokala programmet i Azure AD och konfigurerar inställningar som de webbadresser som användarna behöver för att ansluta till sina appar.

Mer information finns i [Planera en Azure AD Application Proxy-distribution](application-proxy-deployment-plan.md).

Vi rekommenderar att du alltid distribuerar flera kopplingar för redundans och skala. Kontakterna, tillsammans med tjänsten, tar hand om alla uppgifter med hög tillgänglighet och kan läggas till eller tas bort dynamiskt. Varje gång en ny begäran anländer dirigeras den till en av de anslutningar som är tillgängliga. När en anslutning körs förblir den aktiv när den ansluter till tjänsten. Om en koppling inte är tillgänglig för tillfället svarar den inte på den här trafiken. Oanvända kopplingar taggas som inaktiva och tas bort efter 10 dagars inaktivitet.

Kopplingar avsöker också servern för att ta reda på om det finns en nyare version av anslutningen. Även om du kan göra en manuell uppdatering uppdateras kopplingarna automatiskt så länge tjänsten Application Proxy Connector Updater körs. För klienter med flera kopplingar riktar sig automatiska uppdateringar mot en koppling i taget i varje grupp för att förhindra driftstopp i din miljö.

**Obs:** Du kan övervaka sidan Application Proxy [version historia](application-proxy-release-version-history.md) att meddelas när uppdateringar har släppts genom att prenumerera på dess RSS-flöde.

Varje Programproxykoppling tilldelas en [kopplingsgrupp](application-proxy-connector-groups.md). Kopplingar i samma kopplingsgrupp fungerar som en enhet för hög tillgänglighet och belastningsutjämning. Du kan skapa nya grupper, tilldela kopplingar till dem i Azure-portalen och sedan tilldela specifika kopplingar för att betjäna specifika program. Vi rekommenderar att du har minst två kopplingar i varje kopplingsgrupp för hög tillgänglighet.

Anslutningsgrupper är användbara när du behöver stödja följande scenarier:

* Geografisk apppublicering
* Programsegmentering/isolering
* Publicera webbappar som körs i molnet eller lokalt

Mer information om hur du väljer var dina anslutningsappar ska installeras och optimera nätverket finns i [Nätverkstopologiöverväganden när du använder Azure Active Directory Application Proxy](application-proxy-network-topology.md).

## <a name="other-use-cases"></a>Andra användningsfall

Fram till denna punkt har vi fokuserat på att använda Application Proxy för att publicera lokala appar externt samtidigt som du aktiverar enkel inloggning till alla dina moln- och lokala appar. Det finns dock andra användningsfall för App Proxy som är värda att nämna. De omfattar:

* **Publicera REST-API:er på ett säkert sätt**. När du har affärslogik eller API:er som körs lokalt eller finns på virtuella datorer i molnet tillhandahåller Programproxy en offentlig slutpunkt för API-åtkomst. Med API-slutpunktsåtkomst kan du styra autentisering och auktorisering utan att kräva inkommande portar. Det ger ytterligare säkerhet via Azure AD Premium-funktioner som multifaktorautentisering och enhetsbaserad villkorlig åtkomst för stationära datorer, iOS, MAC och Android-enheter som använder Intune. Mer information finns i [Så här aktiverar du inbyggda klientprogram för att interagera med proxyprogram](application-proxy-configure-native-client-application.md) och skydda ett API med [OAuth 2.0 med Azure Active Directory och API Management](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad).
* **Fjärrskrivbordstjänster** ( **RDS)**. Standard RDS-distributioner kräver öppna inkommande anslutningar. [Rds-distributionen med Programproxy](application-proxy-integrate-with-remote-desktop-services.md) har dock en permanent utgående anslutning från servern som kör anslutningstjänsten. På så sätt kan du erbjuda fler program till slutanvändare genom att publicera lokala program via Fjärrskrivbordstjänster. Du kan också minska angreppsytan för distributionen med en begränsad uppsättning tvåstegsverifierings- och villkorlig åtkomstkontroller till RDS.
* **Publicera program som ansluter med WebSockets**. Stöd med [Qlik Sense](application-proxy-qlik.md) är i offentlig förhandsversion och kommer att utökas till andra appar i framtiden.
* **Aktivera inbyggda klientprogram för att interagera med proxyprogram**. Du kan använda Azure AD Application Proxy för att publicera webbappar, men den kan också användas för att publicera [inbyggda klientprogram](application-proxy-configure-native-client-application.md) som är konfigurerade med Azure AD Authentication Library (ADAL). Inbyggda klientprogram skiljer sig från webbappar eftersom de är installerade på en enhet, medan webbappar nås via en webbläsare.

## <a name="conclusion"></a>Slutsats

Vårt sätt att arbeta och de verktyg vi använder förändras snabbt. Med fler anställda som tar sina egna enheter till jobbet och den genomgripande användningen av SaaS-program (Software-as-a-Service) måste också hur organisationer hanterar och säkrar sina data utvecklas. Företag verkar inte längre enbart inom sina egna väggar, skyddade av en vallgrav som omger deras gräns. Data färdas till fler platser än någonsin tidigare – i både lokala miljöer och molnmiljöer. Denna utveckling har bidragit till att öka användarnas produktivitet och förmåga att samarbeta, men det gör också att skydda känsliga data mer utmanande.

Oavsett om du för närvarande använder Azure AD för att hantera användare i ett hybridsamexistensscenario eller är intresserad av att starta din resa till molnet, kan implementering av Azure AD Application Proxy bidra till att minska storleken på ditt lokala fotavtryck genom att tillhandahålla fjärr- åtkomst som en tjänst.

Organisationer bör börja dra nytta av App Proxy idag för att dra nytta av följande fördelar:

* Publicera lokala appar externt utan de omkostnader som är förknippade med att underhålla traditionella VPN-lösningar eller andra lokala webbpubliceringslösningar och DMZ-tillvägagångssätt
* Enkel inloggning till alla program, vare sig det gäller Office 365 eller andra SaaS-appar och inklusive lokala program
* Säkerhet i molnskalning där Azure AD utnyttjar Office 365-telemetri för att förhindra obehörig åtkomst
* Intune integration för att säkerställa företagstrafik autentiseras
* Centralisering av hantering av användarkonton
* Automatiska uppdateringar för att säkerställa att du har de senaste säkerhetskorrigeringarna
* Nya funktioner när de släpps; den senaste är stöd för SAML enda sign-on och mer detaljerad hantering av applikationscookies

## <a name="next-steps"></a>Nästa steg

* Information om hur du planerar, driver och hanterar Azure AD Application Proxy finns i [Planera en Azure AD Application Proxy-distribution](application-proxy-deployment-plan.md).
* Om du vill schemalägga en livedemo eller få en kostnadsfri 90-dagars utvärderingsversion läser [du Komma igång med Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial).
