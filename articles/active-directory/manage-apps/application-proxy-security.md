---
title: Säkerhetsaspekter för Azure AD Application Proxy | Microsoft-dokument
description: Täcker säkerhetsaspekter för att använda Azure AD Application Proxy
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fd6794bafc3c209032f32626e8c46b51769d05e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481236"
---
# <a name="security-considerations-for-accessing-apps-remotely-with-azure-ad-application-proxy"></a>Säkerhetsöverväganden för åtkomst till appar på distans med Azure AD Application Proxy

I den här artikeln beskrivs de komponenter som fungerar för att skydda användare och program när du använder Azure Active Directory Application Proxy.

Följande diagram visar hur Azure AD möjliggör säker fjärråtkomst till dina lokala program.

 ![Diagram över säker fjärråtkomst via Azure AD Application Proxy](./media/application-proxy-security/secure-remote-access.png)

## <a name="security-benefits"></a>Säkerhetsfördelar

Azure AD Application Proxy erbjuder följande säkerhetsfördelar:

### <a name="authenticated-access"></a>Autentiserat åtkomst 

Om du väljer att använda Azure Active Directory-förautentisering kan endast autentiserade anslutningar komma åt nätverket.

Azure AD Application Proxy är beroende av Azure AD security token service (STS) för all autentisering.  Preauthentication, till sin natur, blockerar ett betydande antal anonyma attacker, eftersom endast autentiserade identiteter kan komma åt backend-programmet.

Om du väljer Passthrough som din förautentiseringsmetod får du inte den här förmånen. 

### <a name="conditional-access"></a>Villkorlig åtkomst

Använd rikare principkontroller innan anslutningar till nätverket upprättas.

Med [villkorlig åtkomst](../conditional-access/overview.md)kan du definiera begränsningar för vilken trafik som får komma åt dina backend-program. Du kan skapa principer som begränsar inloggningar baserat på plats, autentiseringsstyrka och användarriskprofil.

Du kan också använda villkorlig åtkomst för att konfigurera principer för multifaktorautentisering och lägga till ytterligare ett säkerhetslager i dina användarautentiseringar. Dessutom kan dina program också dirigeras till Microsoft Cloud App Security via Azure AD Villkorlig åtkomst för att tillhandahålla övervakning och kontroller i realtid, via [åtkomst-](https://docs.microsoft.com/cloud-app-security/access-policy-aad) och [sessionsprinciper](https://docs.microsoft.com/cloud-app-security/session-policy-aad)

### <a name="traffic-termination"></a>Trafikavsättning

All trafik avslutas i molnet.

Eftersom Azure AD Application Proxy är en omvänd proxy avslutas all trafik till backend-program vid tjänsten. Sessionen kan bara återupprättas med backend-servern, vilket innebär att backend-servrarna inte utsätts för direkt HTTP-trafik. Den här konfigurationen innebär att du är bättre skyddad från riktade attacker.

### <a name="all-access-is-outbound"></a>All åtkomst är utgående 

Du behöver inte öppna inkommande anslutningar till företagsnätverket.

Application Proxy-kopplingar använder endast utgående anslutningar till Azure AD Application Proxy-tjänsten, vilket innebär att det inte finns något behov av att öppna brandväggsportar för inkommande anslutningar. Traditionella proxyservrar krävde ett perimeternätverk (kallas även *DMZ,* *demilitariserad zon*eller *skärmat undernät)* och tillät åtkomst till oautentiserade anslutningar vid nätverkskanten. Det här scenariot krävde investeringar i brandväggsprodukter för webbprogram för att analysera trafik och skydda miljön. Med Application Proxy behöver du inte ett perimeternätverk eftersom alla anslutningar är utgående och sker över en säker kanal.

Mer information om kopplingar finns i [Förstå Azure AD Application Proxy-kopplingar](application-proxy-connectors.md).

### <a name="cloud-scale-analytics-and-machine-learning"></a>Analys och maskininlärning i molnskala 

Få ett ledande säkerhetsskydd.

Eftersom det är en del av Azure Active Directory kan Programproxy utnyttja [Azure AD Identity Protection](../active-directory-identityprotection.md), med data från Microsoft Security Response Center och Digital Crimes Unit. Tillsammans identifierar vi proaktivt komprometterade konton och erbjuder skydd mot högriskde logga in. Vi tar hänsyn till många faktorer för att avgöra vilka inloggningsförsök som är högrisk. Dessa faktorer inkluderar flaggning av infekterade enheter, anonymisering av nätverk och atypiska eller osannolika platser.

Många av dessa rapporter och händelser är redan tillgängliga via ett API för integrering med dina SIEM-system (Security Information and Event Management).

### <a name="remote-access-as-a-service"></a>Fjärråtkomst som en tjänst

Du behöver inte oroa dig för att underhålla och korrigera lokala servrar.

Opatrullerad programvara står fortfarande för ett stort antal attacker. Azure AD Application Proxy är en tjänst i Internetskala som Microsoft äger, så du får alltid de senaste säkerhetskorrigeringarna och uppgraderingarna.

För att förbättra säkerheten för program som publiceras av Azure AD Application Proxy blockerar vi sökrobotar från att indexera och arkivera dina program. Varje gång en sökrobot försöker hämta robotens inställningar för en publicerad app svarar Application Proxy `User-agent: * Disallow: /`med en robots.txt-fil som innehåller .

#### <a name="azure-ddos-protection-service"></a>Azure DDoS-skyddstjänst

Program som publiceras via Application Proxy skyddas mot DDoS-attacker (Distributed Denial of Service). **Azure DDoS-skydd** är en tjänst som erbjuds med Azure-plattformen för att skydda dina Azure-resurser från överbelastningsattacker. **Basic-tjänstnivån** aktiveras automatiskt, vilket ger trafikövervakning alltid och begränsning i realtid av vanliga attacker på nätverksnivå. En **standardnivå** är också tillgänglig och erbjuder ytterligare begränsningsfunktioner som är just inställda på Azure Virtual Network-resurser. Mer information finns i [översikt över Azure DDoS Protection Standard](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

## <a name="under-the-hood"></a>Under huven

Azure AD Application Proxy består av två delar:

* Den molnbaserade tjänsten: Den här tjänsten körs i Azure och är där de externa klient-/användaranslutningarna görs.
* [Den lokala anslutningsappen](application-proxy-connectors.md): En lokal komponent, anslutningen lyssnar efter begäranden från Azure AD Application Proxy-tjänsten och hanterar anslutningar till interna program. 

Ett flöde mellan anslutningen och application proxy-tjänsten upprättas när:

* Kopplingen ställs först in.
* Anslutningen hämtar konfigurationsinformation från tjänsten Programproxy.
* En användare kommer åt ett publicerat program.

>[!NOTE]
>All kommunikation sker via TLS och de kommer alltid från anslutningen till tjänsten Application Proxy. Tjänsten är endast utgående.

Anslutningen använder ett klientcertifikat för att autentisera till application proxy-tjänsten för nästan alla anrop. Det enda undantaget från den här processen är det första installationssteget, där klientcertifikatet upprättas.

### <a name="installing-the-connector"></a>Installera kontakten

När kopplingen först konfigureras sker följande flödeshändelser:

1. Anslutningsregistreringen till tjänsten sker som en del av installationen av anslutningen. Användare uppmanas att ange sina Azure AD-administratörsautentiseringsuppgifter.Token som hämtas från den här autentiseringen presenteras sedan för Azure AD Application Proxy-tjänsten.
2. Tjänsten Application Proxy utvärderar token. Den kontrollerar om användaren är en företagsadministratör i klienten.Om användaren inte är administratör avslutas processen.
3. Anslutningsappen genererar en klientcertifikatbegäran och skickar den, tillsammans med token, till application proxy-tjänsten. Tjänsten verifierar i sin tur token och signerar klientcertifikatbegäran.
4. Anslutningen använder klientcertifikatet för framtida kommunikation med application proxy-tjänsten.
5. Anslutningen utför en första dragning av systemkonfigurationsdata från tjänsten med hjälp av klientcertifikatet och är nu redo att ta begäranden.

### <a name="updating-the-configuration-settings"></a>Uppdatera konfigurationsinställningarna

När application proxy-tjänsten uppdaterar konfigurationsinställningarna sker följande flödeshändelser:

1. Anslutningen ansluter till konfigurationsslutpunkten i application proxy-tjänsten med hjälp av klientcertifikatet.
2. När klientcertifikatet har validerats returnerar tjänsten Application Proxy konfigurationsdata till kopplingen (till exempel anslutningsgruppen som anslutningen ska ingå i).
3. Om det aktuella certifikatet är äldre än 180 dagar genererar kopplingen en ny certifikatbegäran som uppdaterar klientcertifikatet var 180:e dag.

### <a name="accessing-published-applications"></a>Komma åt publicerade program

När användare öppnar ett publicerat program inträffar följande händelser mellan tjänsten Application Proxy och Application Proxy-anslutningen:

1. Tjänsten autentiserar användaren för appen
2. Tjänsten placerar en begäran i anslutningskön
3. En anslutning bearbetar begäran från kön
4. Kontakten väntar på ett svar
5. Tjänsten strömmar data till användaren

Om du vill veta mer om vad som sker i vart och ett av dessa steg fortsätter du att läsa.


#### <a name="1-the-service-authenticates-the-user-for-the-app"></a>1. Tjänsten autentiserar användaren för appen

Om du har konfigurerat appen för att använda Passthrough som förautentiseringsmetod hoppas stegen i det här avsnittet över.

Om du har konfigurerat appen för att föra autentisering med Azure AD omdirigeras användare till Azure AD STS för att autentisera och följande steg vidtas:

1. Programproxy söker efter villkorsstyrda principkrav för det specifika programmet. Det här steget säkerställer att användaren har tilldelats programmet. Om tvåstegsverifiering krävs uppmanar autentiseringssekvensen användaren att ange en andra autentiseringsmetod.

2. När alla kontroller har skickats utfärdar Azure AD STS en signerad token för programmet och omdirigerar användaren tillbaka till application proxy-tjänsten.

3. Programproxy verifierar att token har utfärdats till rätt program. Den utför andra kontroller också, till exempel att se till att token har signerats av Azure AD och att den fortfarande finns i det giltiga fönstret.

4. Application Proxy anger en krypterad autentiseringscookie för att indikera att autentisering till programmet har inträffat. Cookien innehåller en tidsstämpel för förfallodatum som baseras på token från Azure AD och andra data, till exempel användarnamnet som autentiseringen baseras på. Cookien krypteras med en privat nyckel som endast är känd för tjänsten Application Proxy.

5. Programproxy omdirigerar användaren tillbaka till den ursprungligen begärda WEBBADRESSEN.

Om någon del av förautentiseringsstegen misslyckas nekas användarens begäran och användaren visas ett meddelande som anger orsaken till problemet.


#### <a name="2-the-service-places-a-request-in-the-connector-queue"></a>2. Tjänsten placerar en begäran i anslutningskön

Kopplingar håller en utgående anslutning öppen för tjänsten Programproxy. När en begäran kommer in köar tjänsten begäran på en av de öppna anslutningarna som anslutningen ska hämtas.

Begäran innehåller objekt från programmet, till exempel begäranden, data från den krypterade cookien, användaren som gör begäran och begärande-ID. Även om data från den krypterade cookien skickas med begäran, är autentiseringscookien i sig inte.

#### <a name="3-the-connector-processes-the-request-from-the-queue"></a>3. Anslutningsappen bearbetar begäran från kön. 

Baserat på begäran utför Application Proxy någon av följande åtgärder:

* Om begäran är en enkel åtgärd (till exempel finns det inga data i brödtexten som med en RESTful *GET-begäran),* anslutningen gör en anslutning till målet intern resurs och väntar sedan på ett svar.

* Om begäran har data som är associerade med den i brödtexten (till exempel en RESTful *POST-åtgärd)* upprättar anslutningen en utgående anslutning med hjälp av klientcertifikatet till Application Proxy-instansen. Den här anslutningen för att begära data och öppna en anslutning till den interna resursen. När den har tagit emot begäran från anslutningen börjar application proxy-tjänsten acceptera innehåll från användaren och vidarebefordrar data till anslutningen. Kopplingen vidarebefordrar i sin tur data till den interna resursen.

#### <a name="4-the-connector-waits-for-a-response"></a>4. Kontakten väntar på ett svar.

När begäran och överföringen av allt innehåll till baksidan är klar väntar anslutningen på ett svar.

När den har fått ett svar upprättar anslutningen en utgående anslutning till application proxy-tjänsten, för att returnera huvudinformationen och börja strömma returdata.

#### <a name="5-the-service-streams-data-to-the-user"></a>5. Tjänsten strömmar data till användaren. 

Viss bearbetning av programmet kan ske här. Om du har konfigurerat Programproxy för att översätta rubriker eller webbadresser i ditt program sker den bearbetningen efter behov under det här steget.


## <a name="next-steps"></a>Nästa steg

[Nätverkstopologiöverväganden när du använder Azure AD Application Proxy](application-proxy-network-topology.md)

[Förstå Azure AD-programproxy-kopplingar](application-proxy-connectors.md)
