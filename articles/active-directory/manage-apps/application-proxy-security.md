---
title: Säkerhetsöverväganden för Azure AD Application Proxy | Microsoft Docs
description: Beskriver säkerhetsöverväganden vid användning av Azure AD Application Proxy
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 985ea1f16cff010041d61d808280cb47f2b77aa9
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39618367"
---
# <a name="security-considerations-for-accessing-apps-remotely-with-azure-ad-application-proxy"></a>Säkerhetsöverväganden för att komma åt appar med Azure AD Application Proxy

Den här artikeln beskriver de komponenter som arbetar för att skydda dina användare och program när du använder Azure Active Directory Application Proxy.

Följande diagram visar hur Azure AD gör det möjligt för säker fjärråtkomst till lokala program.

 ![Diagram över säker fjärråtkomst via Azure AD Application Proxy](./media/application-proxy-security/secure-remote-access.png)

## <a name="security-benefits"></a>Säkerhetsfördelarna

Azure AD-programproxyn erbjuder följande säkerhetsfördelarna:

### <a name="authenticated-access"></a>Autentiserad åtkomst 

Om du väljer att använda Azure Active Directory förautentisering komma endast autentiserade anslutningar åt ditt nätverk.

Azure AD Application Proxy är beroende av de Azure AD säkerhetstokentjänsten (STS) för all autentisering.  Förautentisering, blockerar till sin natur, ett stort antal anonyma attacker eftersom endast autentiserade identiteter har åtkomst till backend-programmet.

Om du väljer genomströmning som metod för förautentisering kan får du inte den här förmånen. 

### <a name="conditional-access"></a>Villkorlig åtkomst

Använd rikare principkontroller innan anslutningar till nätverket upprättas.

Med [villkorlig åtkomst](../conditional-access/overview.md), kan du ange begränsningar på vilken trafik tillåts åtkomst till backend-server-program. Du kan skapa principer som begränsar inloggningar baserat på plats, styrkan hos autentisering och användarprofilen för risken.

Du kan också använda villkorlig åtkomst för att konfigurera Multi-Factor Authentication-principer, att lägga till ytterligare en säkerhetsnivå till din användarautentiseringar. 

### <a name="traffic-termination"></a>Trafik-avslutning

All trafik har avslutats i molnet.

Eftersom Azure AD Application Proxy är en omvänd proxy, avslutas all trafik till backend-program på tjänsten. Sessionen kan hämta återupprättat endast med backend-servern, vilket innebär att backend-servrarna inte exponeras för HTTP-trafik direkt. Den här konfigurationen innebär att du är bättre skyddade från riktade attacker.

### <a name="all-access-is-outbound"></a>All åtkomst är utgående 

Du behöver inte öppna inkommande anslutningar till företagets nätverk.

Anslutningar för programproxy kan bara använda utgående anslutningar till Azure AD Application Proxy-tjänst, vilket innebär att det finns inget behov att öppna portar i brandväggen för inkommande anslutningar. Traditionella proxyservrar krävs ett perimeternätverk (även kallat *DMZ*, *demilitariserad zon*, eller *bevakat undernät*) och får åtkomst till oautentiserade anslutningar vid nätverksgräns. Det här scenariot måste investeringar i web application firewall produkter att analysera trafik och skydda miljön. Med Application Proxy behöver du inte ett perimeternätverk eftersom alla anslutningar är utgående och kan ske under en säker kanal.

Mer information om anslutningsappar finns i [alternativ för Azure AD-programproxyn kopplingar](application-proxy-connectors.md).

### <a name="cloud-scale-analytics-and-machine-learning"></a>Skalbar analys och maskininlärning 

Få de senaste säkerhetsskydd.

Eftersom det är en del av Azure Active Directory Application Proxy kan utnyttja [Azure AD Identity Protection](../active-directory-identityprotection.md), med data från Microsoft Security Response Center och Digital Crimes Unit. Tillsammans vi proaktivt identifiera komprometterade konton och erbjuder skydd mot med hög risk inloggningar. Vi tar hänsyn till flera faktorer som påverkar vilka försök logga in är hög risk. Dessa faktorer omfattar flaggning smittade enheter, maskera nätverk och ovanliga eller osannolikt platser.

Många av dessa rapporter och händelser är redan tillgänglig via ett API för integrering med din säkerhetsinformation och Händelsehanteringssystem (SIEM).

### <a name="remote-access-as-a-service"></a>Fjärråtkomst som en tjänst

Du behöver bekymra dig om underhåll och korrigeringar på lokala servrar.

Okorrigerad programvara fortfarande står för ett stort antal attacker. Azure AD Application Proxy är en tjänst för internetanpassade som Microsoft äger, så att du alltid får den senaste säkerhetskorrigeringar och uppgraderingar.

För att förbättra säkerheten för program som publicerats av Azure AD Application Proxy kan blockera vi webbrobotar som crawlern från indexering och arkivera dina program. Varje gång som en web crawler robot försöker hämta den robot-inställningar för en publicerad app Application Proxy svarar den med en robots.txt-fil som innehåller `User-agent: * Disallow: /`.

### <a name="ddos-prevention"></a>DDOS-skydd

Program som publicerats via programproxy skyddas mot Distributed Denial av tjänsten DDOS-attacker.

Application Proxy-tjänsten övervakar hur mycket trafik som försöker nå dina program och nätverk. Om antalet enheter som begär fjärråtkomst till dina program vid tillfälliga toppar begränsar Microsoft åtkomst till ditt nätverk. 

Microsoft bevakar trafikmönster för enskilda program och din prenumeration som helhet. Om ett program tar emot högre än normala begäranden, nekas förfrågningar om åtkomst till programmet för en kort tidsperiod. Om du får högre än normala begäranden i hela prenumerationen, nekas begäranden för åtkomst till alla dina appar. Det här förebyggande måttet håller programservrar överbelastas av fjärråtkomst begäran så att dina lokala användare kan behålla åtkomst till sina appar. 

## <a name="under-the-hood"></a>Under huven

Azure AD Application Proxy består av två delar:

* Molnbaserad tjänst: den här tjänsten körs i Azure och är där externa klient/användare anslutningar görs.
* [On-premises connector](application-proxy-connectors.md): komponenten lokala anslutningstjänsten lyssnar efter förfrågningar från Azure AD Application Proxy-tjänsten och hanterar anslutningar till de interna program. 

Ett flöde mellan anslutningsappen och programproxytjänsten upprättas när:

* Anslutningen konfigureras först.
* Anslutningstjänsten hämtar konfigurationsinformation från Application Proxy-tjänsten.
* En användare kommer åt ett publicerat program.

>[!NOTE]
>All kommunikation sker över SSL och de kommer alltid på anslutningsappen till Application Proxy-tjänsten. Tjänsten är endast utgående.

Anslutningsappen använder ett klientcertifikat för att autentisera till tjänsten Application Proxy för nästan alla anrop. Det enda undantaget till detta är den första installationen-steg där Klientcertifikatet har upprättats.

### <a name="installing-the-connector"></a>Installera anslutningstjänsten

När anslutningen är först ställer in, äga rum i flödet följande händelser:

1. Registreringen av anslutningsappen till tjänsten sker som en del av installationen av anslutningen. Användare uppmanas att ange sina autentiseringsuppgifter för Azure AD-administratör. Den token som anskaffats från den här autentiseringen sedan presenteras för Azure AD Application Proxy-tjänsten.
2. Application Proxy-tjänsten utvärderar token. Den kontrollerar om användaren är en företagsadministratör i klienten. Om användaren inte är administratör avslutas processen.
3. Anslutningen genererar en certifikatbegäran för klienten och skickar dem, tillsammans med denna token till Application Proxy-tjänsten. Tjänsten i sin tur verifierar token och signerar certifikat klientbegäran.
4. Anslutningsappen använder klientcertifikatet för framtida kommunikation med tjänsten Application Proxy.
5. Anslutningen utför en inledande pull av system-konfigurationsdata från tjänsten med hjälp av dess klientcertifikatet och det är nu redo att ta begäranden.

### <a name="updating-the-configuration-settings"></a>Uppdaterar konfigurationsinställningarna

När tjänsten Application Proxy uppdaterar konfigurationsinställningarna, äga rum i flödet följande händelser:

1. Anslutningen ansluter till slutpunkten konfiguration i Application Proxy-tjänsten med hjälp av dess klientcertifikat.
2. När Klientcertifikatet har verifierats returnerar programproxytjänsten konfigurationsdata till (till exempel den anslutningsgrupp som anslutningen ska vara en del av)-anslutningstjänsten.
3. Om det aktuella certifikatet är äldre än 180 dagar, genererar anslutningen en ny certifikatbegäran om som effektivt uppdaterar klientcertifikatet var 180: e dag.

### <a name="accessing-published-applications"></a>Åtkomst till publicerade program

När användare kommer åt ett publicerat program, äga rum mellan Application Proxy-tjänsten och programproxy-kopplingen i följande händelser:

1. [Tjänsten autentiserar användaren för appen](#the-service-checks-the-configuration-settings-for-the-app)
2. [Tjänsten placerar en begäran i anslutningskö](#The-service-places-a-request-in-the-connector-queue)
3. [En anslutning bearbetar begäran från kön](#the-connector-receives-the-request-from-the-queue)
4. [Anslutningen väntar på svar](#the-connector-waits-for-a-response)
5. [Tjänsten strömmar data för användaren](#the-service-streams-data-to-the-user)

Om du vill veta mer om vad äger rum i var och en av de här stegen kan du fortsätta läsa.


#### <a name="1-the-service-authenticates-the-user-for-the-app"></a>1. Tjänsten autentiserar användaren för appen

Om du har konfigurerat appen för att använda genomströmning som dess förautentiseringsmetoden ignoreras stegen i det här avsnittet.

Om du har konfigurerat appen att preauthenticate med Azure AD omdirigeras användarna till Azure AD-STS att autentisera och ske i följande steg:

1. Application Proxy kontrollerar alla krav för princip för villkorlig åtkomst för det specifika programmet. Det här steget säkerställer att användaren har tilldelats till programmet. Om tvåstegsverifiering krävs meddelanderuta autentisering sekvensen för en andra autentiseringsmetod.

2. När alla kontroller är uppfyllda, Azure AD STS utfärdar en signerade token för programmet och omdirigerar användaren tillbaka till Application Proxy-tjänsten.

3. Programproxyn verifierar att token utfärdats för att åtgärda programmet. Den utför även andra kontroller, till exempel att säkerställa att token har signerats av Azure AD och att det är fortfarande i giltigt fönstret.

4. Application Proxy anger en krypterad autentiseringscookie att indikera att autentiseringen till programmet har uppstått. Cookien innehåller ett förfallodatum tidsstämpel som baseras på token från Azure AD och andra data, till exempel det användarnamn som autentiseringen baseras på. Cookien krypteras med en privat nyckel som bara du känner till Application Proxy-tjänsten.

5. Programproxyn omdirigerar användaren tillbaka till den ursprungligen begärda URL: en.

Om någon del av förautentisering steg misslyckas, av användaren nekas och användaren visas ett meddelande om orsaken till problemet.


#### <a name="2-the-service-places-a-request-in-the-connector-queue"></a>2. Tjänsten placerar en begäran i anslutningskö

Kopplingar håller en utgående anslutning öppen till Application Proxy-tjänsten. När en begäran kommer in, placerar tjänsten upp begäran på en av de öppna anslutningar att hämta-anslutningen.

Begäran innehåller objekt från programmet, till exempel begärandehuvuden data från den krypterad cookien användaren att göra begäran och begärande-ID. Även om data från en krypterad cookie skickas med begäran, är inte autentiseringscookien själva.

#### <a name="3-the-connector-processes-the-request-from-the-queue"></a>3. Anslutningen bearbetar begäran från kön. 

Baserat på begäran, utför Application Proxy någon av följande åtgärder:

* Om begäran är en enkel åtgärd (t.ex, det finns inga data inifrån skick med en RESTful *hämta* begäran), anslutningstjänsten ansluter till den interna målresursen och väntar sedan på ett svar.

* Om begäran har data som hör till den i brödtexten (till exempel en RESTful *POST* åtgärden), anslutningen gör en utgående anslutning med hjälp av klientcertifikatet till Application Proxy-instans. Det gör den här anslutningen för att begära data och öppna en anslutning till den interna resursen. När den tar emot begäran från anslutningen, Application Proxy-tjänsten börjar ta emot innehåll från användaren och vidarebefordrar data till anslutningstjänsten. Anslutningstjänsten, i sin tur vidarebefordrar data till den interna resursen.

#### <a name="4-the-connector-waits-for-a-response"></a>4. Anslutningen väntar på svar.

När begäran och överföring av innehåll till backend-server är klar väntar anslutningen på svar.

När den får ett svar gör anslutningen en utgående anslutning till tjänsten Application Proxy att returnera de information om och påbörja direktuppspelning returnerade data.

#### <a name="5-the-service-streams-data-to-the-user"></a>5. Tjänsten strömmar data för användaren. 

Viss bearbetning av programmet kan inträffa här. Om du har konfigurerat Application Proxy att översätta rubriker eller URL: er i ditt program sker bearbetningen efter behov under det här steget.


## <a name="next-steps"></a>Nästa steg

[Topologiöverväganden för nätverk när du använder Azure AD Application Proxy](application-proxy-network-topology.md)

[Förstå Azure AD Application Proxy-anslutningar](application-proxy-connectors.md)
