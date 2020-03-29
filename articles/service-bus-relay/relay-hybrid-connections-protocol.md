---
title: Protokollguide för Azure Relay Hybrid Connections | Microsoft-dokument
description: I den här artikeln beskrivs interaktioner på klientsidan med hybridanslutningsreläet för anslutning av klienter i lyssnar- och avsändande roller.
services: service-bus-relay
documentationcenter: na
author: clemensv
manager: timlt
editor: ''
ms.assetid: 149f980c-3702-4805-8069-5321275bc3e8
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: clemensv
ms.openlocfilehash: 68668452152064584d1c419a3053ccb642b103f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514960"
---
# <a name="azure-relay-hybrid-connections-protocol"></a>Azure Relay Hybrid Connections-protokoll

Azure Relay är en av de viktigaste kapacitetspelarna på Azure Service Bus-plattformen. Den nya _hybridanslutningsfunktionen_ i Relay är en säker utveckling med öppna protokoll baserat på HTTP och WebSockets. Det ersätter den tidigare, lika kallade _BizTalk Services-funktionen_ som byggdes på en egen protokollgrund. Integreringen av hybridanslutningar i Azure App Services fortsätter att fungera som den är.

Hybridanslutningar möjliggör dubbelriktad, binär strömkommunikation och enkelt datagramflöde mellan två nätverksbaserade program. Antingen eller båda parter kan finnas bakom NATs eller brandväggar.

I den här artikeln beskrivs interaktioner på klientsidan med hybridanslutningsreläet för anslutning av klienter i lyssnar- och avsändande roller. Den beskriver också hur lyssnare accepterar nya anslutningar och förfrågningar.

## <a name="interaction-model"></a>Interaktionsmodell

Hybridanslutningsreläet ansluter två parter genom att tillhandahålla en mötesplats i Azure-molnet som parterna kan identifiera och ansluta till från sitt eget nätverks perspektiv. Mötesplatspunkten kallas "Hybridanslutning" i den här och annan dokumentation, i API:erna och även i Azure-portalen. Tjänstslutpunkten Hybridanslutningar kallas "tjänsten" för resten av den här artikeln.

Tjänsten gör det möjligt att vidarebefordra Webb socket-anslutningar och HTTP(S) begäranden och svar.

Interaktionsmodellen lutar åt den nomenklatur som fastställts av många andra api:er för nätverk. Det finns en lyssnare som först anger beredskap att hantera inkommande anslutningar, och därefter accepterar dem när de anländer. På andra sidan ansluter en klient till lyssnaren och förväntar sig att anslutningen ska accepteras för att upprätta en dubbelriktad kommunikationsväg. "Anslut", "Lyssna" och "Acceptera" är samma termer som du hittar i de flesta socket-API:er.

Alla förmedlade kommunikationsmodeller har någon av parterna som upprättar utgående anslutningar mot en tjänstslutpunkt. Detta gör "lyssnaren" också en "klient" i vardagligt bruk, och kan också orsaka andra terminologi överbelastningar. Den exakta terminologin som därför används för hybridanslutningar är följande:

Programmen på båda sidor av en anslutning kallas "klienter", eftersom de är kunder till tjänsten. Klienten som väntar på och accepterar anslutningar är "lyssnaren", eller sägs vara i "lyssnarrollen". Klienten som initierar en ny anslutning till en lyssnare via tjänsten kallas "avsändare" eller är i rollen "avsändare".

### <a name="listener-interactions"></a>Lyssnare interaktioner

Lyssnaren har fem interaktioner med tjänsten; alla tråddetaljer beskrivs senare i den här artikeln i referensavsnittet.

Meddelandena Lyssna, Acceptera och Begär tas emot från tjänsten. Åtgärderna Förnya och Ping skickas av lyssnaren.

#### <a name="listen-message"></a>Lyssna meddelande

Om du vill visa att tjänsten är redo att acceptera anslutningar skapas en utgående WebSocket-anslutning för att visa att en lyssnare är redo att acceptera anslutningar. Anslutningshandskakningen bär namnet på en hybridanslutning som konfigurerats på relay-namnområdet och en säkerhetstoken som ger "Lyssna" direkt på det namnet.

När WebSocket accepteras av tjänsten är registreringen klar och den etablerade WebSocket hålls vid liv som "kontrollkanal" för att aktivera alla efterföljande interaktioner. Tjänsten tillåter upp till 25 samtidiga lyssnare för en hybridanslutning. Kvoten för AppHooks ska fastställas.

Om det finns två eller flera aktiva lyssnare för hybridanslutningar balanseras inkommande anslutningar över dem i slumpmässig ordning. rättvis fördelning görs med bästa ansträngning.

#### <a name="accept-message"></a>Acceptera meddelande

När en avsändare öppnar en ny anslutning på tjänsten väljer och meddelar tjänsten en av de aktiva lyssnarna på Hybridanslutningen. Det här meddelandet skickas till lyssnaren via den öppna kontrollkanalen som ett JSON-meddelande. Meddelandet innehåller URL:en för webSockets slutpunkt som lyssnaren måste ansluta till för att acceptera anslutningen.

Webbadressen kan och måste användas direkt av lyssnaren utan extra arbete.
Den kodade informationen är endast giltig under en kort tidsperiod, i huvudsak så länge avsändaren är villig att vänta på att anslutningen ska upprättas från på på rad. Det maximala att anta är 30 sekunder. URL:en kan bara användas för ett lyckat anslutningsförsök. Så snart WebSocket-anslutningen till rendezvous-URL:en har upprättats vidarebefordras all ytterligare aktivitet på denna WebSocket från och till avsändaren. Detta sker utan ingripande eller tolkning av tjänsten.

### <a name="request-message"></a>Begär meddelande

Förutom WebSocket-anslutningar kan lyssnaren även ta emot HTTP-begäranderamar från en avsändare, om den här funktionen uttryckligen är aktiverad på hybridanslutningen.

Lyssnare som kopplas till Hybridanslutningar `request` med HTTP-stöd MÅSTE hantera gesten. En lyssnare som inte `request` hanterar och därför orsakar upprepade timeout-fel när den är ansluten kan svartlistas av tjänsten i framtiden.

HTTP-ramhuvudmetadata översätts till JSON för enklare hantering av lyssnarramen, också eftersom HTTP-huvudtolkningsbibliotek är ovanligare än JSON-tolkare. HTTP-metadata som bara är relevanta för relationen mellan avsändaren och Relay HTTP-gatewayen, inklusive auktoriseringsinformation, vidarebefordras inte. HTTP-begäranden överförs transparent som binära WebSocket-ramar.

Lyssnaren kan svara på HTTP-begäranden med hjälp av en motsvarande svarsgest.

Flödet för begäran/svar använder kontrollkanalen som standard, men kan "uppgraderas" till en distinkt rendezvous WebSocket när det behövs. Distinkta WebSocket-anslutningar förbättrar dataflödet för varje klientkonversation, men de belastar lyssnaren med fler anslutningar som måste hanteras, vilket kanske inte är en önskan om lätta klienter.

På kontrollkanalen är för- och svarsorganen begränsade till högst 64 kB i storlek. HTTP-huvudmetadata är begränsad till totalt 32 kB. Om antingen begäran eller svaret överskrider tröskelvärdet måste lyssnaren uppgradera till en rendezvous WebSocket med en gest som motsvarar hanteringen av [Acceptera](#accept-message).

För begäranden bestämmer tjänsten om begäranden ska dirigeras över kontrollkanalen. Detta inkluderar, men får inte begränsas till fall där en begäran överstiger 64 kB (rubriker plus brödtext) direkt, eller om begäran skickas med ["chunked" transfer-kodning](https://tools.ietf.org/html/rfc7230#section-4.1) och tjänsten har anledning att förvänta sig att begäran ska överstiga 64kB eller läsa begäran är inte ögonblicklig. Om tjänsten väljer att leverera begäran via mötesplats, skickar den bara mötesplatsen till lyssnaren.
Lyssnaren måste sedan upprätta rendezvous WebSocket och tjänsten levererar snabbt hela begäran inklusive organ över mötesplatsen WebSocket. Svaret MÅSTE också använda rendezvous WebSocket.

För förfrågningar som kommer över kontrollkanalen bestämmer lyssnaren om han ska svara över kontrollkanalen eller via mötesplats. Tjänsten MÅSTE innehålla en mötesplats adress med varje begäran dirigeras över kontrollkanalen. Den här adressen är endast giltig för uppgradering från den aktuella begäran.

Om lyssnaren väljer att uppgradera, ansluter den och levererar snabbt svaret över den etablerade rendezvous-socketen.

När rendezvous WebSocket har upprättats, bör lyssnaren behålla den för ytterligare hantering av förfrågningar och svar från samma klient. Tjänsten kommer att underhålla WebSocket så länge HTTPS-socketanslutningen med avsändaren kvarstår och dirigerar alla efterföljande begäranden från avsändaren via den underhållna WebSocket. Om lyssnaren väljer att släppa rendezvous WebSocket från sin sida, kommer tjänsten också släppa anslutningen till avsändaren, oavsett om en efterföljande begäran redan kan vara på gång.

#### <a name="renew-operation"></a>Förnya åtgärd

Säkerhetstoken som måste användas för att registrera lyssnaren och underhålla kontrollkanalen kan upphöra att gälla medan lyssnaren är aktiv. Tokenutgången påverkar inte pågående anslutningar, men det gör att kontrollkanalen tas bort av tjänsten vid eller strax efter det att tidpunkten för utgången. Åtgärden "förnya" är ett JSON-meddelande som lyssnaren kan skicka för att ersätta den token som är associerad med kontrollkanalen, så att kontrollkanalen kan underhållas under längre perioder.

#### <a name="ping-operation"></a>Ping-åtgärd

Om kontrollkanalen förblir inaktiv under en längre tid kan mellanhänder på vägen, till exempel belastningsutjämnare eller NATs, släppa TCP-anslutningen. Den "ping" operation undviker att genom att skicka en liten mängd data på kanalen som påminner alla på nätet rutten att anslutningen är tänkt att vara vid liv, och det fungerar också som en "live" test för lyssnaren. Om ping misslyckas bör kontrollkanalen betraktas som oanvändbar och lyssnaren ska återansluta.

### <a name="sender-interaction"></a>Interaktion mellan avsändare

Avsändaren har två interaktioner med tjänsten: den ansluter en webb socket eller den skickar förfrågningar via HTTPS. Begäranden kan inte skickas via en Web Socket från avsändarrollen.

#### <a name="connect-operation"></a>Koppla ihop åtgärden

Åtgärden "connect" öppnar en WebSocket på tjänsten, med namnet på hybridanslutningen och (eventuellt men som standard) en säkerhetstoken som ger behörigheten "Skicka" i frågesträngen. Tjänsten interagerar sedan med lyssnaren på det sätt som beskrivits tidigare, och lyssnaren skapar en rendezvous-anslutning som är kopplad till den här WebSocket. När WebSocket har accepterats, alla ytterligare interaktioner på att WebSocket är med en ansluten lyssnare.

#### <a name="request-operation"></a>Åtgärden Begär

För hybridanslutningar som funktionen har aktiverats för kan avsändaren skicka i stort sett obegränsade HTTP-begäranden till lyssnare.

Med undantag för en Relay-åtkomsttoken som antingen är inbäddad i frågesträngen eller i ett HTTP-huvud för begäran, är relayen helt transparent för alla HTTP-åtgärder på Relay-adressen och alla suffix i relay-adresssökvägen, vilket gör att lyssnaren har full kontroll över end-to-end-auktorisering och till och med HTTP-tilläggsfunktioner som [CORS](https://www.w3.org/TR/cors/).

Avsändande auktorisering med relay-slutpunkten är aktiverad som standard, men är VALFRI. Ägaren till Hybridanslutningen kan välja att tillåta anonyma avsändare. Tjänsten kommer att avlyssna, inspektera och ta bort tillståndsinformation enligt följande:

1. Om frågesträngen innehåller `sb-hc-token` ett uttryck tas uttrycket ALLTID bort från frågesträngen. Den utvärderas om Relay-auktorisering är aktiverad.
2. Om förfråckshuvudena innehåller ett `ServiceBusAuthorization` huvud tas alltid rubrikuttrycket bort från rubriksamlingen.
   Den utvärderas om Relay-auktorisering är aktiverad.
3. Endast om Relay-auktorisering är aktiverat och `Authorization` om begäranhuvudena innehåller ett huvud och inget av de tidigare uttrycken finns, utvärderas och tas bort huvudet. Annars är `Authorization`det alltid vidare som det är.

Om det inte finns någon aktiv lyssnare returnerar tjänsten en felkod på 502 "Bad Gateway". Om tjänsten inte verkar hantera begäran returnerar tjänsten en 504 "Gateway Timeout" efter 60 sekunder.

### <a name="interaction-summary"></a>Interaktionssammanfattning

Resultatet av denna interaktionsmodell är att avsändarens klient kommer ut ur handskakningen med en "ren" WebSocket, som är ansluten till en lyssnare och som inte behöver några ytterligare ingresser eller förberedelser. Den här modellen gör det möjligt för praktiskt taget alla befintliga WebSocket-klientimplementering att enkelt dra nytta av hybridanslutningstjänsten genom att tillhandahålla en korrekt konstruerad URL till sitt WebSocket-klientlager.

Den rendezvous anslutning WebSocket som lyssnaren erhåller genom acceptera interaktion är också ren och kan lämnas till alla befintliga WebSocket server implementering med några minimal extra abstraktion som skiljer mellan "acceptera" åtgärder på ramverkets lokala nätverkslyssnare och hybridanslutningar fjärr "acceptera" åtgärder.

HTTP-begäran/svar-modellen ger avsändaren en i stort sett obegränsad HTTP-protokollyta med ett VALFRITT auktoriseringslager. Lyssnaren får ett förtolat HTTP-begäranhuvudavsnitt som kan återföras till en http-begäran i nedströms eller hanteras som den är, med binära ramar som bär HTTP-organ. Svaren använder samma format. Interaktioner med mindre än 64 KB begäran och svarstext kan hanteras via en enda Webb socket som delas för alla avsändare. Större begäranden och svar kan hanteras med hjälp av rendezvous-modellen.

## <a name="protocol-reference"></a>Protokollreferens

I det här avsnittet beskrivs information om protokollinteraktioner som beskrivits tidigare.

Alla WebSocket-anslutningar görs på port 443 som en uppgradering från HTTPS 1.1, som ofta abstraheras av vissa WebSocket-ramverk eller API. Beskrivningen här hålls genomförandet neutral, utan att föreslå en särskild ram.

### <a name="listener-protocol"></a>Lyssnarprotokoll

Lyssnarprotokollet består av två anslutningsgester och tre meddelandeåtgärder.

#### <a name="listener-control-channel-connection"></a>Anslutning av lyssnarekontrollkanal

Kontrollkanalen öppnas med att skapa en WebSocket-anslutning till:

`wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...`

Det `namespace-address` är det fullständigt kvalificerade domännamnet för Azure Relay-namnområdet som `{myname}.servicebus.windows.net`är värd för Hybrid-anslutningen, vanligtvis i formuläret .

Parameteralternativen för frågesträngen är följande.

| Parameter        | Krävs | Beskrivning
| ---------------- | -------- | -------------------------------------------
| `sb-hc-action`   | Ja      | För lyssnarrollen måste parametern vara **sb-hc-action=listen**
| `{path}`         | Ja      | Url-kodad namnområdessökväg för den förkonfigurerade hybridanslutningen för att registrera lyssnaren på. Det här uttrycket läggs `$hc/` till i den fasta sökvägen.
| `sb-hc-token`    | Ja\*    | Lyssnaren måste tillhandahålla en giltig, URL-kodad servicebuss delad åtkomsttoken för namnområdet eller hybridanslutningen som ger **lyssningsrättigheten.**
| `sb-hc-id`       | Inga       | Det här klienttillförda valfria ID:t möjliggör heltäckande diagnostikspårning.

Om WebSocket-anslutningen misslyckas på grund av att sökvägen till hybridanslutning inte registreras, eller en ogiltig eller saknad token, eller något annat fel, tillhandahålls felfeedbacken med den vanliga HTTP 1.1-statusfeedbackmodellen. Statusbeskrivningen innehåller ett felspårnings-ID som kan kommuniceras till Azure-supportpersonal:

| Kod | Fel          | Beskrivning
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Hittades inte      | Sökvägen till hybridanslutning är ogiltig eller bas-URL:en är felaktig.
| 401  | Behörighet saknas   | Säkerhetstoken saknas eller är felaktig eller ogiltig.
| 403  | Förbjudet      | Säkerhetstoken är inte giltig för den här sökvägen för den här åtgärden.
| 500  | Internt fel | Något gick fel i tjänsten.

Om WebSocket-anslutningen avsiktligt stängs av av tjänsten efter att den först har konfigurerats, meddelas orsaken till detta med hjälp av en lämplig WebSocket-protokollfelkod tillsammans med ett beskrivande felmeddelande som också innehåller ett spårnings-ID. Tjänsten kommer inte att stänga av kontrollkanalen utan att stöta på ett feltillstånd. All ren avstängning är klientstyrd.

| WS-status | Beskrivning
| --------- | -------------------------------------------------------------------------------
| 1001      | Sökvägen till hybridanslutning har tagits bort eller inaktiverats.
| 1008      | Säkerhetstoken har upphört att gälla, därför bryts auktoriseringsprincipen.
| 1011      | Något gick fel i tjänsten.

#### <a name="accept-handshake"></a>Acceptera handslag

Meddelandet "acceptera" skickas av tjänsten till lyssnaren via den tidigare etablerade kontrollkanalen som ett JSON-meddelande i en WebSocket-textram. Det finns inget svar på detta meddelande.

Meddelandet innehåller ett JSON-objekt med namnet "acceptera", som definierar följande egenskaper just nu:

* **adress** – URL-strängen som ska användas för att upprätta WebSocket till tjänsten för att acceptera en inkommande anslutning.
* **id** – den unika identifieraren för den här anslutningen. Om ID:et angavs av avsändarens klient är det avsändarens angivna värde, annars är det ett systemgenererat värde.
* **connectHeaders** – alla HTTP-huvuden som har levererats till Relay-slutpunkten av avsändaren, som också innehåller Sec-WebSocket-Protocol och Sec-WebSocket-Extensions-huvudena.

```json
{
    "accept" : {
        "address" : "wss://dc-node.servicebus.windows.net:443/$hc/{path}?..."
        "id" : "4cb542c3-047a-4d40-a19f-bdc66441e736",
        "connectHeaders" : {
            "Host" : "...",
            "Sec-WebSocket-Protocol" : "...",
            "Sec-WebSocket-Extensions" : "..."
        }
     }
}
```

Adress-URL:en som anges i JSON-meddelandet används av lyssnaren för att upprätta WebSocket för att acceptera eller avvisa avsändaruttaget.

##### <a name="accepting-the-socket"></a>Acceptera uttaget

Lyssnaren upprättar en WebSocket-anslutning till den angivna adressen för att acceptera.

Om meddelandet "acceptera" `Sec-WebSocket-Protocol` har en rubrik förväntas lyssnaren bara accepterar WebSocket om den stöder det protokollet. Dessutom anges rubriken när WebSocket upprättas.

Detsamma gäller `Sec-WebSocket-Extensions` för rubriken. Om ramverket stöder ett tillägg bör det ange huvudet till `Sec-WebSocket-Extensions` serversidan svara på den nödvändiga handskakningen för tillägget.

URL:en måste användas som den är för att upprätta den accepterande socketen, men innehåller följande parametrar:

| Parameter      | Krävs | Beskrivning
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Ja      | För att acceptera en socket måste parametern vara`sb-hc-action=accept`
| `{path}`       | Ja      | (se följande stycke)
| `sb-hc-id`     | Inga       | Se tidigare beskrivning av **id**.

`{path}`är url-kodad namnområdessökväg för den förkonfigurerade hybridanslutningen som lyssnaren ska registreras på. Det här uttrycket läggs `$hc/` till i den fasta sökvägen.

Uttrycket `path` kan utökas med ett suffix och ett frågestränguttryck som följer det registrerade namnet efter ett separat snedstreck.
Detta gör det möjligt för avsändarklienten att skicka sändningsargument till den accepterande lyssnaren när det inte är möjligt att inkludera HTTP-huvuden. Förhoppningen är att lyssnarramsverket tolkar ut den fasta sökvägen och det registrerade namnet från sökvägen och `sb-`gör resten, eventuellt utan några frågesträngargument som föregås av , tillgängliga för programmet för att avgöra om anslutningen ska accepteras.

Mer information finns i följande avsnitt "Avsändningsprotokoll".

Om det finns ett fel kan tjänsten svara på följande sätt:

| Kod | Fel          | Beskrivning
| ---- | -------------- | -----------------------------------
| 403  | Förbjudet      | Webbadressen är inte giltig.
| 500  | Internt fel | Något gick fel i tjänsten

 När anslutningen har upprättats stänger servern av WebSocket när avsändaren WebSocket stängs av eller med följande status:

| WS-status | Beskrivning                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | Avsändarens klient stänger av anslutningen.                                    |
| 1001      | Sökvägen till hybridanslutning har tagits bort eller inaktiverats.                        |
| 1008      | Säkerhetstoken har upphört att gälla, därför bryts auktoriseringsprincipen. |
| 1011      | Något gick fel i tjänsten.                                            |

##### <a name="rejecting-the-socket"></a>Avvisa uttaget

 Avvisa uttaget efter att `accept` ha inspekterat meddelandet kräver ett liknande handslag så att statuskoden och statusbeskrivningen som kommunicerar orsaken till avslaget kan flöda tillbaka till avsändaren.

 Valet av protokolldesign här är att använda ett WebSocket-handslag (som är utformat för att sluta i ett definierat feltillstånd) så att lyssnarklientimplementeringar kan fortsätta att förlita sig på en WebSocket-klient och inte behöver använda en extra, bare HTTP-klient.

 Om du vill avvisa socketen tar klienten adressen URI från meddelandet och lägger till två frågesträngparametrar till den, `accept` enligt följande:

| Param                   | Krävs | Beskrivning                              |
| ----------------------- | -------- | ---------------------------------------- |
| sb-hc-statusCode        | Ja      | Numerisk HTTP-statuskod.                |
| sb-hc-statusBeskrivning | Ja      | Mänsklig läsbar orsak till avslaget. |

Den resulterande URI används sedan för att upprätta en WebSocket-anslutning.

När du slutför korrekt misslyckas det här handskakningen avsiktligt med en HTTP-felkod 410, eftersom ingen WebSocket har upprättats. Om något går fel beskriver följande koder felet:

| Kod | Fel          | Beskrivning                          |
| ---- | -------------- | ------------------------------------ |
| 403  | Förbjudet      | Webbadressen är inte giltig.                |
| 500  | Internt fel | Något gick fel i tjänsten. |

#### <a name="request-message"></a>Begär meddelande

Meddelandet `request` skickas av tjänsten till lyssnaren över kontrollkanalen. Samma meddelande skickas också över rendezvous WebSocket gång etablerat.

Består `request` av två delar: en rubrik och binära kroppsramar.
Om det inte finns någon kropp utelämnas kroppsramarna. Indikatorn för om det finns en `body` brödtext är den booleska egenskapen i meddelandet om begäran.

För en begäran med en begäran kropp, kan strukturen se ut så här:

``` text
----- Web Socket text frame ----
{
    "request" :
    {
        "body" : true,
        ...
    }
}
----- Web Socket binary frame ----
FEFEFEFEFEFEFEFEFEFEF...
----- Web Socket binary frame ----
FEFEFEFEFEFEFEFEFEFEF...
----- Web Socket binary frame -FIN
FEFEFEFEFEFEFEFEFEFEF...
----------------------------------
```

Lyssnaren MÅSTE hantera mottagandet av begärandetexten som delas över flera binära ramar (se [WebSocket-fragment).](https://tools.ietf.org/html/rfc6455#section-5.4)
Begäran avslutas när en binär ram med FIN-flaggan har tagits emot.

För en begäran utan en brödtext finns det bara en textram.

``` text
----- Web Socket text frame ----
{
    "request" :
    {
        "body" : false,
        ...
    }
}
----------------------------------
```

JSON-innehållet `request` för är följande:

* **adress** - URI-sträng. Det här är mötesplatsen som ska användas för den här begäran. Om den inkommande begäran är större än 64 kB lämnas resten av meddelandet tomt och klienten `accept` MÅSTE initiera ett rendezvous-handslag som motsvarar den åtgärd som beskrivs nedan. Tjänsten kommer sedan att `request` sätta hela på den etablerade webbuttaget. Om svaret kan förväntas överstiga 64 kB måste lyssnaren också initiera ett rendezvous-handslag och sedan överföra svaret över det etablerade webbuttaget.
* **id** – sträng. Den unika identifieraren för den här begäran.
* **requestHeaders** – det här objektet innehåller alla HTTP-huvuden som har levererats till slutpunkten av avsändaren, med undantag för auktoriseringsinformation enligt beskrivningen [ovan](#request-operation)och rubriker som strikt relaterar till anslutningen till gatewayen. I synnerhet tas alla rubriker som definierats eller `Via`reserverats i [RFC7230](https://tools.ietf.org/html/rfc7230), utom , bort och vidarebefordras inte:

  * `Connection`(RFC7230, avsnitt 6.1)
  * `Content-Length`(RFC7230, avsnitt 3.3.2)
  * `Host`(RFC7230, avsnitt 5.4)
  * `TE`(RFC7230, avsnitt 4.3)
  * `Trailer`(RFC7230, avsnitt 4.4)
  * `Transfer-Encoding`(RFC7230, avsnitt 3.3.1)
  * `Upgrade`(RFC7230, avsnitt 6.7)
  * `Close`(RFC7230, avsnitt 8.1)

* **requestTarget** – sträng. Den här egenskapen innehåller [begärandens begärandeställe (RFC7230, avsnitt 5.3).](https://tools.ietf.org/html/rfc7230#section-5.3) Detta inkluderar frågesträngdelen, som tas `sb-hc-` bort från ALLA förutblottade parametrar.
* **metod** - sträng. Detta är metoden för begäran, per [RFC7231, avsnitt 4](https://tools.ietf.org/html/rfc7231#section-4). Metoden `CONNECT` FÅR INTE användas.
* **kropp** – booleska. Anger om en eller flera binära kroppsramar följer.

``` JSON
{
    "request" : {
        "address" : "wss://dc-node.servicebus.windows.net:443/$hc/{path}?...",
        "id" : "42c34cb5-7a04-4d40-a19f-bdc66441e736",
        "requestTarget" : "/abc/def?myarg=value&otherarg=...",
        "method" : "GET",
        "requestHeaders" : {
            "Host" : "...",
            "Content-Type" : "...",
            "User-Agent" : "..."
        },
        "body" : true
     }
}
```

##### <a name="responding-to-requests"></a>Svara på förfrågningar

Mottagaren MÅSTE svara. Upprepade försök att svara på begäranden samtidigt som anslutningen bibehålls kan resultera i att lyssnaren blir svartlistad.

Svar kan skickas i valfri ordning, men varje begäran måste besvaras inom 60 sekunder eller leveransen kommer att rapporteras som har misslyckats. Tidsfristen på 60 sekunder räknas `response` tills ramen har tagits emot av tjänsten. Ett pågående svar med flera binära ramar kan inte bli inaktiv i mer än 60 sekunder eller avslutas.

Om begäran tas emot via kontrollkanalen måste svaret antingen skickas på kontrollkanalen från den plats där begäran togs emot eller skickas via en rendezvous-kanal.

Svaret är ett JSON-objekt med namnet "svar". Reglerna för hantering av kroppsinnehåll `request` är exakt som `body` med meddelandet och baserat på egenskapen.

* **requestId** – sträng. Krävs. Egenskapsvärdet `id` för `request` meddelandet som besvaras.
* **statusKod** – nummer. Krävs. en numerisk HTTP-statuskod som anger resultatet av meddelandet. Alla statuskoder för [RFC7231, avsnitt 6](https://tools.ietf.org/html/rfc7231#section-6) är tillåtna, med undantag för [502 "Bad Gateway"](https://tools.ietf.org/html/rfc7231#section-6.6.3) och [504 "Gateway Timeout".](https://tools.ietf.org/html/rfc7231#section-6.6.5)
* **statusDescription** - sträng. Valfri. HTTP-orsaksfras för HTTP-kod per [RFC7230, avsnitt 3.1.2](https://tools.ietf.org/html/rfc7230#section-3.1.2)
* **responseHeaders** – HTTP-huvuden som ska anges i ett externt HTTP-svar.
  Som med `request`de RFC7230-definierade rubrikerna FÅR INTE användas.
* **kropp** – booleska. Anger om binära kroppsramar följer(er).

``` text
----- Web Socket text frame ----
{
    "response" : {
        "requestId" : "42c34cb5-7a04-4d40-a19f-bdc66441e736",
        "statusCode" : "200",
        "responseHeaders" : {
            "Content-Type" : "application/json",
            "Content-Encoding" : "gzip"
        }
         "body" : true
     }
}
----- Web Socket binary frame -FIN
{ "hey" : "mydata" }
----------------------------------
```

##### <a name="responding-via-rendezvous"></a>Svara via rendezvous

För svar som överstiger 64 kB måste svaret levereras via ett rendezvous-uttag. Om begäran överskrider 64 kB och `request` den enda innehåller adressfältet måste ett rendezvous-uttag upprättas för att hämta `request`. När ett rendezvous-uttag har upprättats måste svar på respektive klient och efterföljande begäranden från respektive klient levereras över rendezvous-socketen medan den kvarstår.

URL:en `address` `request` i måste användas som den är för att upprätta rendezvous-socketen, men innehåller följande parametrar:

| Parameter      | Krävs | Beskrivning
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Ja      | För att acceptera en socket måste parametern vara`sb-hc-action=request`

Om det finns ett fel kan tjänsten svara på följande sätt:

| Kod | Fel           | Beskrivning
| ---- | --------------- | -----------------------------------
| 400  | Ogiltig begäran | Okänd åtgärd eller URL är ogiltig.
| 403  | Förbjudet       | WEBBADRESSEN har upphört att gälla.
| 500  | Internt fel  | Något gick fel i tjänsten

 När anslutningen har upprättats stänger servern av WebSocket när klientens HTTP-socket stängs av eller med följande status:

| WS-status | Beskrivning                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | Avsändarens klient stänger av anslutningen.                                    |
| 1001      | Sökvägen till hybridanslutning har tagits bort eller inaktiverats.                        |
| 1008      | Säkerhetstoken har upphört att gälla, därför bryts auktoriseringsprincipen. |
| 1011      | Något gick fel i tjänsten.                                            |


#### <a name="listener-token-renewal"></a>Förnyelse av lyssnaretoken

När lyssnartoken håller på att upphöra att gälla kan den ersätta den genom att skicka ett textramsmeddelande till tjänsten via den etablerade kontrollkanalen. Meddelandet innehåller ett JSON-objekt som heter `renewToken`, som definierar följande egenskap just nu:

* **token** – en giltig, URL-kodad servicebuss delad åtkomsttoken för namnområdet eller hybridanslutningen som ger **lyssningsrättigheten.**

```json
{
  "renewToken": {
    "token":
      "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"
  }
}
```

Om tokenvalideringen misslyckas nekas åtkomst och molntjänsten stänger kontrollkanalen WebSocket med ett fel. Annars finns det inget svar.

| WS-status | Beskrivning                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1008      | Säkerhetstoken har upphört att gälla, därför bryts auktoriseringsprincipen. |

### <a name="web-socket-connect-protocol"></a>Ansluta protokoll för webbuttag

Avsändningsprotokollet är i själva verket identiskt med hur en lyssnare upprättas.
Målet är maximal transparens för end-to-end WebSocket. Adressen som ska anslutas till är samma som för lyssnaren, men "åtgärden" skiljer sig åt och token behöver en annan behörighet:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sbc-hc-token=...
```

_Namnområdesadressen_ är det fullständigt kvalificerade domännamnet för azure Relay-namnområdet som `{myname}.servicebus.windows.net`är värd för Hybrid-anslutningen, vanligtvis i formuläret .

Begäran kan innehålla godtyckliga extra HTTP-huvuden, inklusive programdefinierade. Alla angivna rubriker flödar till lyssnaren och `connectHeader` finns på objektet för **meddelandet acceptera** kontroll.

Parameteralternativen för frågesträngen är följande:

| Param          | Krävs? | Beskrivning
| -------------- | --------- | -------------------------- |
| `sb-hc-action` | Ja       | För avsänarrollen måste `sb-hc-action=connect`parametern vara .
| `{path}`       | Ja       | (se följande stycke)
| `sb-hc-token`  | Ja\*     | Lyssnaren måste tillhandahålla en giltig, URL-kodad servicebuss delad åtkomsttoken för namnområdet eller hybridanslutningen som ger **skicka** rätt.
| `sb-hc-id`     | Inga        | Ett valfritt ID som möjliggör heltäckande diagnostikspårning och görs tillgängligt för lyssnaren under det accepterande handskakningen.

 Den `{path}` url-kodade namnområdessökvägen för den förkonfigurerade hybridanslutningen som lyssnaren ska registreras på. Uttrycket `path` kan utökas med ett suffix och ett frågestränguttryck för att kommunicera vidare. Om Hybridanslutningen registreras under `hyco`sökvägen kan uttrycket `path` följas `hyco/suffix?param=value&...` av frågesträngparametrarna som definieras här. Ett fullständigt uttryck kan då vara följande:

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sbc-hc-token=...
```

Uttrycket `path` skickas vidare till lyssnaren i adressen URI som finns i kontrollmeddelandet "acceptera".

Om WebSocket-anslutningen misslyckas på grund av att sökvägen till hybridanslutning inte registreras, en ogiltig eller saknad token eller något annat fel, tillhandahålls felfeedbacken med den vanliga HTTP 1.1-statusåterkopplingsmodellen. Statusbeskrivningen innehåller ett felspårnings-ID som kan kommuniceras till Azure-supportpersonal:

| Kod | Fel          | Beskrivning
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Hittades inte      | Sökvägen till hybridanslutning är ogiltig eller bas-URL:en är felaktig.
| 401  | Behörighet saknas   | Säkerhetstoken saknas eller är felaktig eller ogiltig.
| 403  | Förbjudet      | Säkerhetstoken är inte giltig för den här sökvägen och för den här åtgärden.
| 500  | Internt fel | Något gick fel i tjänsten.

Om WebSocket-anslutningen avsiktligt stängs av av tjänsten efter att den har konfigurerats, meddelas orsaken till detta med hjälp av en lämplig WebSocket-protokollfelkod tillsammans med ett beskrivande felmeddelande som också innehåller ett spårnings-ID .

| WS-status | Beskrivning
| --------- | ------------------------------------------------------------------------------- 
| 1000      | Lyssnaren stängde av uttaget.
| 1001      | Sökvägen till hybridanslutning har tagits bort eller inaktiverats.
| 1008      | Säkerhetstoken har upphört att gälla, därför bryts auktoriseringsprincipen.
| 1011      | Något gick fel i tjänsten.

### <a name="http-request-protocol"></a>HTTP-begäran protokoll

HTTP-begärandeprotokollet tillåter godtyckliga HTTP-begäranden, förutom protokolluppgraderingar.
HTTP-begäranden pekas på entitetens vanliga körningsadress, utan den $hc infix som används för hybridanslutningar WebSocket-klienter.

```
https://{namespace-address}/{path}?sbc-hc-token=...
```

_Namnområdesadressen_ är det fullständigt kvalificerade domännamnet för azure Relay-namnområdet som `{myname}.servicebus.windows.net`är värd för Hybrid-anslutningen, vanligtvis i formuläret .

Begäran kan innehålla godtyckliga extra HTTP-huvuden, inklusive programdefinierade. Alla angivna rubriker, utom de som är direkt definierade i RFC7230 (se meddelande `requestHeader` om [begäran)](#Request message)flödar till lyssnaren och finns på objektet i meddelandet om **begäran.**

Parameteralternativen för frågesträngen är följande:

| Param          | Krävs? | Beskrivning
| -------------- | --------- | ---------------- |
| `sb-hc-token`  | Ja\*     | Lyssnaren måste tillhandahålla en giltig, URL-kodad servicebuss delad åtkomsttoken för namnområdet eller hybridanslutningen som ger **skicka** rätt.

Token kan också bäras `ServiceBusAuthorization` i `Authorization` antingen HTTP-huvudet eller HTTP-huvudet. Token kan utelämnas om hybridanslutningen är konfigurerad för att tillåta anonyma begäranden.

Eftersom tjänsten fungerar effektivt som en proxy, även om den inte `Via` är en sann HTTP-proxy, lägger den antingen till ett huvud eller kommenterar det befintliga `Via` huvudet som är kompatibelt med [RFC7230, avsnitt 5.7.1](https://tools.ietf.org/html/rfc7230#section-5.7.1).
Tjänsten lägger till värdnamnet `Via`Relay-namnområde i .

| Kod | Meddelande  | Beskrivning                    |
| ---- | -------- | ------------------------------ |
| 200  | OK       | Begäran har hanterats av minst en lyssnare.  |
| 202  | Accepterad | Begäran har accepterats av minst en lyssnare. |

Om det finns ett fel kan tjänsten svara på följande sätt. Om svaret kommer från tjänsten eller från lyssnaren kan identifieras `Via` genom närvaro av huvudet. Om rubriken finns kommer svaret från lyssnaren.

| Kod | Fel           | Beskrivning
| ---- | --------------- |--------- |
| 404  | Hittades inte       | Sökvägen till hybridanslutning är ogiltig eller bas-URL:en är felaktig.
| 401  | Behörighet saknas    | Säkerhetstoken saknas eller är felaktig eller ogiltig.
| 403  | Förbjudet       | Säkerhetstoken är inte giltig för den här sökvägen och för den här åtgärden.
| 500  | Internt fel  | Något gick fel i tjänsten.
| 503  | Felaktig gateway     | Begäran kunde inte dirigeras till någon lyssnare.
| 504  | Gateway Timeout | Begäran dirigerades till en lyssnare, men lyssnaren bekräftade inte kvittot inom den tid som krävdes.

## <a name="next-steps"></a>Nästa steg

* [Vanliga frågor och svar om Relay](relay-faq.md)
* [Skapa ett namnområde](relay-create-namespace-portal.md)
* [Komma igång med .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Kom igång med Node](relay-hybrid-connections-node-get-started.md)
