---
title: "Säkerhetsaspekter för Azure AD Application Proxy | Microsoft Docs"
description: "Beskriver säkerhetsaspekter för att använda Azure AD Application Proxy"
services: active-directory
documentationcenter: 
author: kgremban
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 5a2ab5c68345deee3a34173d38d529a911c7ff3e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="security-considerations-for-accessing-apps-remotely-with-azure-ad-application-proxy"></a>Säkerhetsaspekter för att komma åt appar med Azure AD Application Proxy

Den här artikeln beskrivs de komponenter som arbetar för att skydda användare och program när du använder Azure Active Directory Application Proxy.

Följande diagram visar hur Azure AD kan säker fjärråtkomst till lokala program.

 ![Diagram över säker fjärråtkomst via Azure AD Application Proxy](./media/application-proxy-security-considerations/secure-remote-access.png)

## <a name="security-benefits"></a>Säkerhetsfördelarna

Azure AD Application Proxy fördelar följande säkerhet:

### <a name="authenticated-access"></a>Autentiserad åtkomst 

Om du väljer att använda Azure Active Directory förautentisering kan endast autentiserade anslutningar ansluta till nätverket.

Azure AD Application Proxy är beroende av Azure AD säkerhetstokentjänsten (STS) för alla autentisering.  Förautentisering, blockeras sin natur som ett stort antal anonyma attacker eftersom endast autentiserade identiteter har åtkomst till backend-programmet.

Om du väljer genomströmning som metod för förautentisering kan få du inte förmånen. 

### <a name="conditional-access"></a>Villkorlig åtkomst

Använd rikare kontrollerna för principer innan anslutningar till nätverket upprättas.

Med [villkorlig åtkomst](active-directory-conditional-access-azure-portal-get-started.md), kan du ange begränsningar på vilken trafik tillåts åtkomst till backend-program. Du kan skapa principer som begränsar inloggningar baserat på plats, stark autentisering och risken för användarprofil.

Du kan också använda villkorlig åtkomst för att konfigurera Multi-Factor Authentication-principer, att lägga till ytterligare en säkerhetsnivå till din användarautentisering. 

### <a name="traffic-termination"></a>Avslutning av trafik

All trafik avslutas i molnet.

Eftersom Azure AD Application Proxy är en omvänd proxy, avslutas all trafik till backend-program i tjänsten. Sessionen kan hämta återupprättat bara med backend-servern, vilket innebär att backend-servrar inte utsätts för direkt HTTP-trafik. Den här konfigurationen innebär att du skyddas bättre från riktade attacker.

### <a name="all-access-is-outbound"></a>All åtkomst är utgående 

Du behöver inte öppna inkommande anslutningar till företagsnätverket.

Application Proxy kopplingar kan bara använda utgående anslutningar till Azure AD Application Proxy-tjänst, vilket innebär att det finns ingen anledning att öppna portar i brandväggen för inkommande anslutningar. Traditionella proxyservrar krävs ett perimeternätverk (även kallat *DMZ*, *demilitariserad zon*, eller *avskärmat undernät*) och få tillgång till oautentiserade anslutningar nätverket kant. Det här scenariot måste investeringar i web application brandväggsprodukter att analysera trafik och skydda miljön. Med Application Proxy behöver du inte ett perimeternätverk eftersom alla anslutningar är utgående och sker över en säker kanal.

Läs mer om kopplingar [förstå Azure AD Application Proxy kopplingar](application-proxy-understand-connectors.md).

### <a name="cloud-scale-analytics-and-machine-learning"></a>Skalbar molnlagring analyser och maskininlärning 

Hämta senaste säkerhetsskydd.

Eftersom det är en del av Azure Active Directory Application Proxy kan utnyttja [Azure AD Identity Protection](active-directory-identityprotection.md), med data från Microsoft Security Response Center och Digital Crimes Unit. Tillsammans vi proaktivt identifiera avslöjade konton och ger skydd från hög risk inloggningar. Vi ta hänsyn till flera faktorer som påverkar vilka försök logga in är hög risk. Dessa faktorer är flaggning infekterade enheter, anonymizing nätverk och ovanliga eller osannolikt platser.

Många av dessa rapporter och händelser som är redan tillgängliga via ett API för integrering med din säkerhetsinformation och händelse-hanteringssystem (SIEM).

### <a name="remote-access-as-a-service"></a>Fjärråtkomst som en tjänst

Du behöver inte bry dig om underhåll och korrigering lokala servrar.

Okorrigerade programvara fortfarande står för ett stort antal attacker. Azure AD Application Proxy är en Internet-skala som Microsoft äger, så du får alltid senaste säkerhetskorrigeringar och uppgraderingar.

Om du vill förbättra säkerheten för program som publicerats av Azure AD Application Proxy blockera vi web crawler robotar från indexering och arkivera dina program. Varje gång en web crawler robot försöker hämta av roboten inställningarna för en publicerad appen Application Proxy svarar med en robots.txt-fil som innehåller `User-agent: * Disallow: /`.

### <a name="ddos-prevention"></a>DDOS förebyggande

Program som publicerats via Application Proxy är skyddade mot Distributed Denial av tjänsten DDOS-attacker.

Tjänsten Application Proxy övervakar hur mycket trafik som försöker nå dina program och nätverk. Om antalet enheter som begär åtkomst till dina program fjärr ger spikar i diagrammet, begränsar Microsoft åtkomst till nätverket. 

Microsoft bevakar trafikmönster för enskilda program och din prenumeration som helhet. Om ett program tar emot högre än normal begäranden nekas begäranden att komma åt programmet under en kort tidsperiod. Om du får högre än normal begäranden över hela prenumerationen, nekas begäranden att komma åt dina appar. Åtgärden förebyggande behåller programservrar överbelastas med fjärråtkomstbegäranden, så att dina lokala användare kan behålla åtkomst till sina appar. 

## <a name="under-the-hood"></a>Under huven

Azure AD Application Proxy består av två delar:

* Molnbaserad tjänst: den här tjänsten körs i Azure och är där externa klientanvändare anslutningar görs.
* [Lokal anslutning](application-proxy-understand-connectors.md): komponenten lokalt kopplingen lyssnar efter förfrågningar från Azure AD Application Proxy-tjänsten och hanterar anslutningar till de interna program. 

Ett flöde mellan kopplingen och tjänsten Application Proxy upprättat när:

* Anslutningen konfigureras först.
* Kopplingen hämtar konfigurationsinformation från tjänsten Application Proxy.
* En användare kommer åt ett publicerat program.

>[!NOTE]
>All kommunikation som sker över SSL och de kommer alltid vid anslutningen till tjänsten Application Proxy. Tjänsten är endast utgående.

Anslutningen används ett klientcertifikat för att autentisera till Application Proxy-tjänst för nästan alla samtal. Det enda undantaget till detta är det första installation-steget där Klientcertifikatet har upprättats.

### <a name="installing-the-connector"></a>Installerar connector

När kopplingen först har konfigurerats ske på följande händelser för flödet:

1. Registreringen av anslutningsverktyget för tjänsten sker som en del av installationen av anslutningen. Användarna uppmanas att ange sina autentiseringsuppgifter för Azure AD-administratör. Den token som erhållits från den här autentiseringen sedan presenteras för Azure AD Application Proxy-tjänsten.
2. Tjänsten Application Proxy utvärderar token. Den kontrollerar om användaren är en företagsadministratör i klienten. Om användaren inte är administratör avslutas processen.
3. Kopplingen genererar en certifikatbegäran för klienten och skickar den, tillsammans med denna token till Application Proxy-tjänsten. Tjänsten i sin tur verifierar token och signerar certifikat klientbegäran.
4. Anslutningen används klientcertifikatet för framtida kommunikation med tjänsten Application Proxy.
5. Kopplingen utför en inledande pull system konfigurationsdata från tjänsten med hjälp av dess klientcertifikatet och är nu redo att ta begäranden.

### <a name="updating-the-configuration-settings"></a>Uppdaterar konfigurationsinställningarna

När tjänsten Application Proxy uppdaterar konfigurationsinställningarna, äga rum flödet följande händelser:

1. Kopplingen ansluter till slutpunkten konfigurationen i tjänsten Application Proxy genom att använda dess klientcertifikat.
2. När klientens certifikat har verifierats returnerar tjänsten Application Proxy konfigurationsdata för koppling (till exempel connector gruppen som anslutningen ska vara en del av).
3. Om det aktuella certifikatet är mer än 180 dagar, genererar kopplingen en ny certifikatbegäran som effektivt uppdaterar klientcertifikatet varje 180 dagar.

### <a name="accessing-published-applications"></a>Åtkomst till publicerade program

När användare har åtkomst till ett publicerat program äga rum mellan Application Proxy-tjänsten och Application Proxy connector följande händelser:

1. [Tjänsten autentiserar användaren för appen](#the-service-checks-the-configuration-settings-for-the-app)
2. [Tjänsten placerar en begäran i kö för kopplingen](#The-service-places-a-request-in-the-connector-queue)
3. [En koppling bearbetar begäran från kön](#the-connector-receives-the-request-from-the-queue)
4. [Anslutningen väntar på svar](#the-connector-waits-for-a-response)
5. [Tjänsten strömmar data till användaren](#the-service-streams-data-to-the-user)

Om du vill veta mer om vad sker i var och en av de här stegen kan du fortsätta läsa.


#### <a name="1-the-service-authenticates-the-user-for-the-app"></a>1. Tjänsten autentiserar användaren för appen

Om du har konfigurerat appen för att använda genomströmning som dess förautentiseringsmetoden hoppas över stegen i det här avsnittet.

Om du har konfigurerat appen att preauthenticate med Azure AD, omdirigeras användarna till Azure AD-STS att autentisera och ske i följande steg:

1. Application Proxy kontrollerar alla krav på villkorlig åtkomst för det specifika programmet. Det här steget säkerställer att användaren har tilldelats till programmet. Om tvåstegsverifiering krävs efterfrågar autentisering sekvensen en andra autentiseringsmetod.

2. När alla kontroller har klarat STS Azure AD utfärdar en signerade token för programmet och omdirigeras användaren till Application Proxy-tjänsten.

3. Programproxy verifierar att token har utfärdats för att åtgärda programmet. Den genomför också andra kontroller som säkerställer att token har signerats av Azure AD och att det är fortfarande i fönstret giltig.

4. Application Proxy anger en krypterad autentiseringscookie som anger att autentiseringen till programmet har uppstått. Cookien innehåller ett förfallodatum tidsstämpel som baseras på token från Azure AD och andra data, till exempel användarnamn som autentiseringen baseras på. Cookien krypteras med en privat nyckel som känner till Application Proxy-tjänsten.

5. Application Proxy dirigerar användaren till den ursprungligen begärda URL: en.

Om någon del av förautentisering steg misslyckas, användarens begäran nekas och användaren visas ett meddelande om orsaken till problemet.


#### <a name="2-the-service-places-a-request-in-the-connector-queue"></a>2. Tjänsten placerar en begäran i kö för kopplingen

Kopplingar hålla en utgående anslutning öppen till Application Proxy-tjänsten. När en begäran kommer, köar tjänsten in den på en av de anslutningar som är öppna för kopplingen för att hämta.

Begäran innehåller objekt från programmet, till exempel begärandehuvuden data från den kryptera cookien användaren att göra begäran och begärande-ID. Även om data från den kryptera cookien skickas med förfrågan är inte autentiseringscookien sig själv.

#### <a name="3-the-connector-processes-the-request-from-the-queue"></a>3. Kopplingen bearbetar begäran från kön. 

Programproxy utför baserat på begäran, någon av följande åtgärder:

* Om begäran är en enkel åtgärd (t.ex, det finns inga data i meddelandets brödtext som med en RESTful *hämta* begäran), kopplingen gör en anslutning till den interna målresursen och sedan väntar på svar.

* Om begäran har data som är associerade med den i meddelandetexten (till exempel en RESTful *POST* åtgärden), kopplingen gör en utgående anslutning med hjälp av klientcertifikatet till Application Proxy-instans. Det gör den här anslutningen att begära data och öppna en anslutning till den interna resursen. När den tar emot en begäran från kopplingen Application Proxy-tjänsten börjar ta emot innehåll från användaren och vidarebefordrar data till anslutningstjänsten. Anslutningen kan i sin tur vidarebefordrar data till den interna resursen.

#### <a name="4-the-connector-waits-for-a-response"></a>4. Anslutningen väntar på svar.

När begäran och överföring av allt innehåll serverdelen är klar väntar anslutningen på svar.

När den tar emot ett svar gör kopplingen en utgående anslutning till tjänsten Application Proxy att returnera information för sidhuvud och börja strömning returnerade data.

#### <a name="5-the-service-streams-data-to-the-user"></a>5. Tjänsten strömmas data för användaren. 

Vissa bearbetningen av programmet kan inträffa här. Om du har konfigurerat Application Proxy att översätta huvuden eller URL: er i ditt program sker bearbetningen som behövs för det här steget.


## <a name="next-steps"></a>Nästa steg

[Topologiöverväganden nätverk när du använder Azure AD Application Proxy](application-proxy-network-topology-considerations.md)

[Förstå Azure AD Application Proxy-kopplingar](application-proxy-understand-connectors.md)
