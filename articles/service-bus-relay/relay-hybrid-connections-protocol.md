---
title: Azure Relay Hybridanslutningar protokollet guiden | Microsoft Docs
description: Guide för Azure Relay Hybridanslutningar-protokollet.
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
ms.date: 05/02/2018
ms.author: clemensv
ms.openlocfilehash: 306a21add76261dce99c954a2ba373e4b5047a75
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="azure-relay-hybrid-connections-protocol"></a>Azure Relay Hybridanslutningar protokoll

Azure Relay är en av kapaciteten för viktiga pelare i Azure Service Bus-plattformen. Den nya _Hybridanslutningar_ möjligheterna för vidarebefordran är en säker, öppna protokoll utvecklingen baserat på http- och WebSockets. Det ersätter den tidigare lika med namnet _BizTalk-tjänst_ funktion som har byggt på protokoll. Integrering av Hybridanslutningar i Azure App Service fortsätter att fungera som-är.

Hybridanslutningar kan dubbelriktad, binär dataström kommunikation och enkel datagram flödar mellan två nätverksprogram. Ett av eller båda parterna kan finnas bakom NAT och brandväggar.

Den här artikeln beskriver klientsidan samverkan med Hybridanslutningar relay för att ansluta klienter i lyssnaren och avsändaren roller. Här beskrivs också hur lyssnare godta nya anslutningar och begäranden.

## <a name="interaction-model"></a>Interaktion modellen

Hybridanslutningar relay ansluter två parter genom att tillhandahålla en rendezvous-punkt i Azure-molnet parter kan identifiera och ansluta till ur sina egna nätverk. Rendezvous-punkt kallas ”Hybridanslutning” i den här och övrig dokumentation i de API: er och även i Azure-portalen. Tjänstslutpunkten Hybridanslutningar kallas ”tjänst” för resten av den här artikeln.

Tjänsten tillåter för att vidarebefordra Web Socket anslutningar och HTTP (S) och -svar.

Interaktion modellen leans på nomenklaturen upprättas av många andra nätverksfunktioner API: er. Det finns en lyssnare som först visar kan hantera inkommande anslutningar och accepterar dem senare när de tas emot. En klient ansluter till lyssnaren förväntas anslutningen ska godkännas för att upprätta en dubbelriktad kommunikation sökväg på den andra sidan. ”Anslut”, ”lyssna” och ”accepterar” är samma villkor som du hittar i de flesta socket API: er.

Alla vidarebefordrande kommunikation har någon av parterna utgående anslutningar upprättas mot en tjänstslutpunkt. Detta gör att ”lyssnaren” också ”klient” talspråkliga används och kan också göra att andra terminologi överlagringar. De exakta termer som används därför för Hybridanslutningar är följande:

Program på båda sidor av en anslutning kallas ”klienter”, eftersom de är klienter till tjänsten. Den klient som väntar och accepterar anslutningar är ”lyssnaren” eller anses vara ”lyssnare rollen”. Den klient som initierar en ny anslutning till en lyssnare via service kallas ”avsändaren”, eller finns i ”avsändaren roll”.

### <a name="listener-interactions"></a>Lyssnare interaktioner

Lyssnaren har fem interaktioner med tjänsten; alla överföring uppgifter beskrivs nedan i referensavsnittet.

Lyssna och acceptera begäran om meddelanden tas emot från tjänsten. Förnya, och Ping operations skickas av lyssnaren.

#### <a name="listen-message"></a>Lyssna meddelande

Om du vill ange beredskap för tjänsten som en lyssnare är redo att acceptera anslutningar, skapas en utgående WebSocket-anslutning. Handskakningen anslutningen innehåller namnet på en Hybridanslutning som konfigurerats på Relay-namnområde och en säkerhetstoken som ger ”lyssna” till att namnet.

När WebSocket accepteras av tjänsten registreringen är klar och etablerade WebSocket bevaras alive som ”kontrollkanal” för att aktivera alla efterföljande interaktioner. Tjänsten tillåter upp till 25 samtidiga lyssnare en Hybrid-anslutning. Kvoten för AppHooks ska fastställas.

För Hybridanslutningar, om det finns två eller flera aktiva lyssnare balanseras inkommande anslutningar mellan dem i slumpmässig ordning. rättvis fördelning görs med bästa prestanda.

#### <a name="accept-message"></a>Acceptera meddelandet

När en avsändare öppnas en ny anslutning på tjänsten väljer tjänsten och meddelar en aktiva lyssnare på Hybrid-anslutning. Det här meddelandet skickas till lyssnaren över öppna kontrollkanalen som ett JSON-meddelande. Meddelandet innehåller URL-Adressen till den WebSocket-slutpunkt som lyssnaren måste ansluta till för att acceptera anslutningen.

URL: en kan och måste användas direkt av lyssnare utan någon extra arbete.
Kodad information är endast giltig för en kort tidsperiod i stort sett under den tid som avsändaren är villigt att vänta på att anslutningen ska vara etablerade slutpunkt till slutpunkt. Det maximala antalet att anta är 30 sekunder. URL-Adressen kan bara användas för en lyckade anslutningsförsöket. Så snart WebSocket-anslutningen med rendezvous-URL har upprättats vidarebefordras alla ytterligare aktivitet på den här WebSocket från och till avsändaren. Detta sker utan några åtgärder eller tolkning av tjänsten.

### <a name="request-message"></a>Begärandemeddelandet

Förutom WebSocket-anslutningar kan lyssnaren också få ramar för HTTP-begäran från en avsändare om den här funktionen aktiveras uttryckligen på Hybrid-anslutningen.

Lyssnare som kopplas till Hybridanslutningar med HTTP-stöd måste hantera den `request` gest. En lyssnare som kan inte hantera `request` och därför ger upprepade timeout-fel när ansluts kan vara övriga av tjänsten i framtiden.

HTTP-ram sidhuvud metadata översätts till JSON för enklare hantering av framework lyssnare också eftersom HTTP-huvudet tolkning bibliotek är sällsynta än JSON-parser. HTTP-metadata som endast är relevant för relationen mellan avsändare och Relay HTTP-gateway, inklusive auktoriseringsinformation, vidarebefordras inte. HTTP-begäran organ överförs transparent som binära WebSocket ramar.

Lyssnaren kan svara på HTTP-begäranden med en motsvarande svar.

Frågor och svar flödet använder kontrollkanalen som standard, men kan ”uppgraderas” till en distinkta rendezvous WebSocket när krävs. Distinkta WebSocket-anslutningar bättre genomströmning för varje klient konversation, men de belastar lyssnaren med flera anslutningar som ska hanteras, som kanske inte kan önskan för lightweight-klienter.

På kontrollkanalen är begäran och svar begränsade till högst 64 kB. HTTP-huvudet metadata är begränsad till högst 32 kB. Om begäran eller svaret överskrider gränsen, lyssnaren måste uppgradera till en rendezvous WebSocket med en motsvarande till hantering av [acceptera](#accept-message).

För begäranden bestämmer tjänsten sig för att vidarebefordra begäranden via kontrollkanalen. Detta inkluderar, men kan inte begränsas till fall där en begäran överskrider 64 kB (huvuden plus brödtext) direkt, eller om begäran skickas med [”chunked” transfer-encoding](https://tools.ietf.org/html/rfc7230#section-4.1) och tjänsten har väntar för begäran om att överskrida 64 kB eller läsa begäran är inte omedelbar. Om tjänsten väljer att leverera begäran via rendezvous, skickar bara rendezvous-adressen till lyssnaren.
Lyssnaren måste sedan upprätta rendezvous WebSocket och tjänsten ger omedelbart fullständig begärd inklusive organ över rendezvous WebSocket. Svaret måste också använda rendezvous WebSocket.

För begäranden som kommer över kontrollkanalen bestämmer lyssnaren sig för att svara via kontrollkanalen eller via rendezvous. Tjänsten måste innehålla en rendezvous-adress med varje begäran som skickas över kontrollkanalen. Den här adressen är endast giltig för att uppgradera från den aktuella begäranden.

Om lyssnaren väljer att uppgradera, ansluter och levererar omgående svaret över etablerade rendezvous-socket.

En gång rendezvous WebSocket har upprättats, lyssnaren ska upprätthålla för ytterligare hantering av begäranden och -svar från samma klient. Tjänsten ska behålla WebSocket för så länge HTTPS socket anslutning med avsändaren kvarstår och dirigerar alla efterföljande förfrågningar från sändaren över behålla WebSocket. Om lyssnaren väljer att släppa rendezvous WebSocket från sidan, tjänsten också att ta bort anslutningen till avsändaren, oavsett om en efterföljande begäran kanske redan pågår.

#### <a name="renew-operation"></a>Förnya åtgärden

Den säkerhetstoken som måste användas för att registrera lyssnaren och underhålla kontrollkanalen kan gälla när lyssnaren är aktiv. Token upphör att gälla påverkar inte pågående anslutningar, men den orsakar kontrollkanal tas bort av tjänsten på eller strax efter den tidpunkt då har upphört att gälla. Åtgärden ”förnya” är ett JSON-meddelande som lyssnaren kan skicka Ersätt token som är associerade med kontrollkanalen, så att kontrollkanalen kan underhållas under långa perioder.

#### <a name="ping-operation"></a>Ping-åtgärden

Om kontrollkanalen hålls inaktiv för länge mellanhand på sättet, till exempel belastningen kan belastningsutjämnare eller NAT-enheter släppa TCP-anslutningen. Åtgärden ”pinga” undviker som genom att skicka en liten mängd data på den kanal som påminner alla på nätverksväg som anslutningen ska vara alive och den fungerar också som en ”live” test för lyssnaren. Om pingningen misslyckas kontrollkanalen ska betraktas som inte kan användas och lyssnaren borde återansluta.

### <a name="sender-interaction"></a>Avsändaren interaktion

Avsändaren har två interaktioner med tjänsten: den ansluter en Socket på webben eller den skickar begäranden via HTTPS. Begäranden kan inte skickas med en webb-Socket från rollen avsändare.

#### <a name="connect-operation"></a>Åtgärden med anslutning

Åtgärden ”ansluta” öppnar en WebSocket på tjänsten, att ange namnet på Hybrid-anslutningen och (valfritt, men måste som standard) en säkerhet token ger ”skicka” behörighet i frågesträngen. Tjänsten sedan samverkar med lyssnaren på sätt som beskrivs ovan och lyssnaren skapar en rendezvous-anslutning som är kopplad till den här WebSocket. När WebSocket har godkänts, är alla ytterligare interaktioner på den WebSocket med en lyssnare för anslutna.

#### <a name="request-operation"></a>Request-åtgärd

För Hybrid-anslutningar som har aktiverat funktionen kan avsändaren skicka HTTP-begäranden som i stort sett obegränsad till lyssnare.

Förutom en Relay åtkomst-token som är antingen inbäddat i frågesträngen eller i ett HTTP-huvud för begäran är vidarebefordran helt transparent för alla HTTP-åtgärder på Relay-adress och alla suffix Relay adress sökvägens lämnar lyssnaren fullständigt kontroll över en d-till-slutpunkt auktoriserings- och även http-funktioner som [CORS](https://www.w3.org/TR/cors/).

Avsändaren auktorisering med Relay-slutpunkten är aktiverad som standard, men är valfritt. Ägaren av Hybrid-anslutningen kan välja att tillåta anonyma avsändare. Tjänsten ska fånga upp, inspektera och ta bort auktoriseringsinformation på följande sätt:

1. Om frågesträngen innehåller en `sb-hc-token` uttryck, uttrycket att alltid rensas bort från frågesträngen. Den kommer att utvärderas om Relay tillstånd är aktiverat.
2. Om huvuden för begäran innehåller en `ServiceBusAuthorization` huvud, huvudet uttryck att alltid rensas bort från samlingen sidhuvud.
   Den kommer att utvärderas om Relay tillstånd är aktiverat.
3. Endast om Relay tillstånd är aktiverat och om huvuden för begäran innehåller en `Authorization` sidhuvud och ingen av de föregående uttryck finns, huvudet utvärderas och bort. I annat fall den `Authorization`skickas alltid som-är.

Om det inte finns några aktiva lyssnare, returnerar tjänsten en 502 ”Ogiltig Gateway”-felkod. Om tjänsten inte visas hantera begäran, returnerar tjänsten en 504 ”Gateway-Timeout” efter 60 sekunder.

### <a name="interaction-summary"></a>Interaktion sammanfattning

Resultatet av den här interaktionen modellen är att avsändaren klienten kommer utanför handskakning med en ”ren” WebSocket som är ansluten till en lyssnare och som behöver ingen ytterligare preambles eller förberedelser. Den här modellen ger praktiskt taget alla befintliga WebSocket-klientimplementeringen enkelt kan dra nytta av tjänsten Hybridanslutningar genom att ange en korrekt konstruerade URL till sina lager för WebSocket-klienten.

Rendezvous-anslutningen WebSocket som lyssnaren hämtar via acceptera interaktionen är också rensa och kan överlämnas till alla befintliga WebSocket-serverimplementering med vissa minimal extra abstraktion som skiljer mellan ”acceptera” åtgärder på sina framework lokala nätverket lyssnare och Hybridanslutningar remote ”acceptera” åtgärder.

HTTP-begäran och svar modellen ger avsändaren en i stort sett obegränsad HTTP-protokollet yta med ett valfritt auktorisering lager. Lyssnaren hämtar ett före parsade sidhuvud avsnitt för HTTP-begäran som kan omvandlas tillbaka till en underordnad HTTP-begäran eller hanteras som, med binära ramar överförs HTTP organ. Svar använder samma format. Interaktioner med mindre än 64 KB brödtext i begäran och svar kan hanteras via en enskild Socket för webbprogram som är gemensam för alla avsändare. Större begäranden och -svar kan hanteras med hjälp av rendezvous-modellen.

## <a name="protocol-reference"></a>Referens för protokollet

Det här avsnittet innehåller information om protokoll interaktioner som beskrivs ovan.

Alla WebSocket-anslutningar görs på port 443 som en uppgradering från HTTPS 1.1, som representeras ofta av vissa WebSocket framework eller API. Beskrivningen här sparas implementering neutral, utan tyder på ett specifikt ramverk.

### <a name="listener-protocol"></a>Lyssnarprotokollet

Lyssnarprotokollet består av två anslutning gester och tre meddelandeåtgärder.

#### <a name="listener-control-channel-connection"></a>Lyssnare kontrollanslutningen kanal

Kontrollkanalen har öppnats med att skapa en WebSocket-anslutning till:

`wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...`

Den `namespace-address` är fullständigt kvalificerade domännamnet för Azure Relay-namnområde som är värd för Hybrid-anslutningen, vanligtvis i formatet `{myname}.servicebus.windows.net`.

Frågealternativen sträng parameter är som följer.

| Parameter        | Krävs | Beskrivning
| ---------------- | -------- | -------------------------------------------
| `sb-hc-action`   | Ja      | Parametern måste vara för rollen lyssnare **sb-hc-action = lyssna**
| `{path}`         | Ja      | URL-kodade namnområdessökvägen till den förkonfigurerade Hybridanslutning att registrera den här lyssnaren på. Det här uttrycket läggs till den fasta `$hc/` sökvägsdelen.
| `sb-hc-token`    | Ja\*    | Lyssnaren måste ange en giltig, URL-kodade Service Bus delad åtkomst-Token för namnområdet eller Hybridanslutning som ger den **lyssna** rätt.
| `sb-hc-id`       | Nej       | Den här klienten har angett valfritt ID aktiverar diagnostikspårning för slutpunkt till slutpunkt.

Om WebSocket-anslutningen misslyckas på grund av Hybridanslutning sökvägen inte är registrerad, eller en ogiltig eller saknas token eller några andra fel, tillhandahålls fel feedback med vanlig HTTP 1.1 status feedback modellen. Statusbeskrivningen innehåller ett fel för spårnings-id som kan överföras till Azure supporttekniker:

| Kod | Fel          | Beskrivning
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Kunde inte hittas      | Hybridanslutningen sökväg är ogiltig eller den grundläggande Webbadressen är felaktig.
| 401  | Behörighet saknas   | Säkerhetstoken är saknas eller felaktig eller ogiltig.
| 403  | Förbjudna      | Säkerhetstoken är inte giltig för den här sökvägen för den här åtgärden.
| 500  | Internt fel | Något gick fel i tjänsten.

Om WebSocket-anslutningen avsiktligt stängs av tjänsten efter att det ursprungligen har ställts in orsaken till detta så överförs med hjälp av lämplig WebSocket-protokollet felkoden tillsammans med ett beskrivande felmeddelande som innehåller också en spårnings-ID: t. Tjänsten stängs inte kontrollkanalen utan ett feltillstånd. En ren avstängning är klient kontrolleras.

| WS-Status | Beskrivning
| --------- | -------------------------------------------------------------------------------
| 1001      | Hybridanslutningen sökvägen har tagits bort eller inaktiverats.
| 1008      | Säkerhets-token har upphört att gälla, därför auktoriseringsprincipen överskrids.
| 1011      | Något gick fel i tjänsten.

#### <a name="accept-handshake"></a>Acceptera handskakning

”Accepterar”-meddelande skickas av tjänsten till lyssnaren över tidigare upprättad kontrollkanalen som ett JSON-meddelande i en WebSocket ram. Det finns inga svar på meddelandet.

Meddelandet innehåller en JSON-objekt med namnet ”accepterar”, som definierar följande egenskaper just nu:

* **adress** – URL-sträng som ska användas för att fastställa WebSocket till tjänsten för att godkänna en inkommande anslutning.
* **ID** – den unika identifieraren för den här anslutningen. Om ID som har angetts av klienten avsändaren är avsändaren angivna-värdet, annars är det ett värde som genereras av systemet.
* **connectHeaders** – alla HTTP-huvuden som har angetts till slutpunkten för vidarebefordran av avsändaren, vilket även innefattar sek-WebSocket-protokollet och WebSocket-s-tillägg-rubriker.

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

Adress-URL som anges i JSON-meddelandet används av lyssnaren upprätta WebSocket för att godkänna eller avvisa avsändaren socket.

##### <a name="accepting-the-socket"></a>Acceptera socketen

Om du vill acceptera, upprättar lyssnaren en WebSocket-anslutning till den angivna adressen.

Om meddelandet ”accepterar” har en `Sec-WebSocket-Protocol` rubrik som är det förväntas att lyssnaren accepterar bara WebSocket om den har stöd för protokollet. Dessutom anger huvudet som WebSocket har upprättats.

Samma gäller den `Sec-WebSocket-Extensions` rubrik. Om ramen har stöd för ett tillägg, ska den in rubriken på serversidan svaret obligatoriska `Sec-WebSocket-Extensions` handskakning för tillägget.

URL: en måste användas som-är för att fastställa acceptera socketen men innehåller följande parametrar:

| Parameter      | Krävs | Beskrivning
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Ja      | Parametern måste vara för att acceptera en socket `sb-hc-action=accept`
| `{path}`       | Ja      | (se följande punkt)
| `sb-hc-id`     | Nej       | Se föregående beskrivning av **id**.

`{path}` är URL-kodade namnområdessökvägen till den förkonfigurerade Hybridanslutning som du vill registrera den här lyssnaren. Det här uttrycket läggs till den fasta `$hc/` sökvägsdelen.

Den `path` uttryck kan utökas med ett suffix och ett stränguttryck för frågan som följer det registrerade namnet efter ett separertratten snedstreck.
Detta gör att avsändaren klienten att skicka dispatch argument till lyssnaren accepterar när det inte är möjligt att inkludera HTTP-huvuden. Förväntningen är att lyssnare framework analyserar fast sökvägsdelen och det registrerade namnet från sökvägen och gör resten, möjligen utan någon fråga strängargument föregås av `sb-`, tillgänglig för program för att bestämma om du vill acceptera anslutningen.

Mer information finns i avsnittet ”avsändaren protokoll”.

Om det uppstår ett fel kan i tjänsten svara på följande sätt:

| Kod | Fel          | Beskrivning
| ---- | -------------- | -----------------------------------
| 403  | Förbjudna      | URL: en är inte giltig.
| 500  | Internt fel | Det uppstod ett fel i tjänsten

 När anslutningen har upprättats stängs servern av WebSocket när avsändaren WebSocket stänger, eller med följande status:

| WS-Status | Beskrivning                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | Avsändaren klienten avslutar anslutningen.                                    |
| 1001      | Hybridanslutningen sökvägen har tagits bort eller inaktiverats.                        |
| 1008      | Säkerhets-token har upphört att gälla, därför auktoriseringsprincipen överskrids. |
| 1011      | Något gick fel i tjänsten.                                            |

##### <a name="rejecting-the-socket"></a>Avvisa socket

 Avvisa socket efter att ha kontrollerat det `accept` meddelande kräver en liknande handskakning så att statuskoden och statusbeskrivningen kommunicerar orsaken för avslaget kan flöda tillbaka till avsändaren.

 Protokollet designen har valts här är att använda en WebSocket-handskakning (som är utformade för att sluta med en definierad feltillstånd) så att lyssnare klientimplementeringar kan fortsätta att förlita sig på en WebSocket-klienten och behöver inte använda en extra, utan http-klienten.

 Om du vill avvisa socket klienten tar den URI-adressen från den `accept` meddelande och lägger till två frågeparametrar sträng, enligt följande:

| Param                   | Krävs | Beskrivning                              |
| ----------------------- | -------- | ---------------------------------------- |
| SB-hc-statusCode        | Ja      | Numeriska HTTP-statuskod.                |
| SB-hc-statusDescription | Ja      | Mänsklig läsbar orsak till att. |

Resulterande URI: N används sedan för att upprätta en WebSocket-anslutning.

När den har slutförts korrekt, misslyckas denna handskakning avsiktligt med en HTTP-felkod 410, eftersom ingen WebSocket har upprättats. Om något går fel beskrivs följande koder felet:

| Kod | Fel          | Beskrivning                          |
| ---- | -------------- | ------------------------------------ |
| 403  | Förbjudna      | URL: en är inte giltig.                |
| 500  | Internt fel | Något gick fel i tjänsten. |

#### <a name="request-message"></a>Begärandemeddelandet

Den `request` meddelandet skickas av tjänsten till lyssnaren över kontrollkanalen. Samma meddelande skickas också över rendezvous WebSocket när du har valt.

Den `request` består av två delar: en rubrik och binär brödtext bildruta.
Om det finns ingen brödtext, har brödtext ramar uteslutits. Indikator för det om det finns en brödtext är boolean `body` egenskap i meddelandet med begäran.

För en begäran med en begärantext strukturen kan se ut så här:

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

Lyssnaren måste hantera tar emot begärandetexten dela upp på flera binära ramar (se [WebSocket fragment](https://tools.ietf.org/html/rfc6455#section-5.4)).
Begäran avslutas när en binär ram med flaggan Finland har tagits emot.

Det finns endast en ram för en begäran utan brödtext.

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

Innehållet i JSON för `request` är följande:

* **adress** -URI-strängen. Detta är rendezvous-adressen ska användas för denna begäran. Om den inkommande begäranden är större än 64 kB resten av det här meddelandet är tomt och klienten måste initiera en rendezvous-handskakning som motsvarar den `accept` åtgärden som beskrivs nedan. Tjänsten kommer att placeras det fullständiga `request` på etablerade Web-socket. Om svaret kan förväntas vara längre än 64 kB, måste lyssnaren också starta en rendezvous-handskakning och överför sedan svaret över etablerade Web-socket.
* **ID** – sträng. Den unika identifieraren för denna begäran.
* **requestHeaders** – det här objektet innehåller alla HTTP-huvuden som har angetts till slutpunkten av avsändaren, med undantag av auktoriseringsinformation som förklaras [ovan](#request-operation), och rubriker som strikt relaterar till den anslutning med gatewayen. Mer specifikt alla huvuden definierats eller reserverade i [RFC7230](https://tools.ietf.org/html/rfc7230), utom `Via`, bort och inte vidarebefordras:

  * `Connection` (RFC7230 avsnitt 6.1)
  * `Content-Length`  (RFC7230 avsnitt 3.3.2)
  * `Host`  (RFC7230 avsnitt 5.4)
  * `TE`  (RFC7230 avsnitt 4.3)
  * `Trailer`  (RFC7230 avsnitt 4.4)
  * `Transfer-Encoding`  (RFC7230 avsnitt 3.3.1)
  * `Upgrade` (RFC7230 avsnittet 6.7)
  * `Close`  (RFC7230 avsnitt 8.1)

* **requestTarget** – sträng. Den här egenskapen innehåller den [”begäran mål” (RFC7230, avsnittet 5.3)](https://tools.ietf.org/html/rfc7230#section-5.3) för begäran. Detta inkluderar strängdelen frågan som tas bort för alla `sb-hc-` föregås parametrar.
* **metoden** -sträng. Det här är metoden för begäran, per [RFC7231, avsnitt 4](https://tools.ietf.org/html/rfc7231#section-4). Den `CONNECT` metoden får inte användas.
* **brödtext** – boolean. Anger om en mer mer binära brödtext ram följer.

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

##### <a name="responding-to-requests"></a>Svara på begäranden

Mottagaren måste svara. Upprepade fel att svara på begäranden samtidigt som anslutningen kan resultera i lyssnaren komma övriga.

Svar kan skickas i valfri ordning, men varje begäran måste vara svarat inom 60 sekunder eller leveransen kommer att rapporteras som har misslyckats. Tidsgränsen för 60-sekunders räknas förrän den `response` ram har tagits emot av tjänsten. Ett pågående svar med flera binära ramar kan inte bli inaktiv mer än 60 sekunder eller så avslutas den.

Om begäran tas emot via kontrollkanalen svaret måste antingen skickas på kontrollkanalen från där begäran togs emot eller sändas via en rendezvous-kanal.

Svaret är ett JSON-objekt med namnet ”svar”. Regler för hantering av brödtextinnehåll är samma sätt som med den `request` meddelande och baserat på den `body` egenskapen.

* **requestId** – sträng. KRÄVS. Den `id` -värdet för den `request` meddelande besvaras.
* **statusCode** – nummer. KRÄVS. en numerisk HTTP-statuskod som visar resultatet av meddelandet. Alla statuskoder i [RFC7231, avsnitt 6](https://tools.ietf.org/html/rfc7231#section-6) tillåts, förutom för [502 ”Ogiltig Gateway”](https://tools.ietf.org/html/rfc7231#section-6.6.3) och [504 ”Gateway-Timeout”](https://tools.ietf.org/html/rfc7231#section-6.6.5).
* **statusDescription** -sträng. VALFRITT. HTTP-statuskod orsaksfrasen per [RFC7230, punkt 3.1.2](https://tools.ietf.org/html/rfc7230#section-3.1.2)
* **responseHeaders** – HTTP-huvuden anges i en extern HTTP-svar.
  Med den `request`, RFC7230 definierade huvuden inte får användas.
* **brödtext** – boolean. Anger om binära brödtext bildruta follow(s).

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

För svar som överskrider 64 kB skickas svaret över en rendezvous-socket. Även om begäran överskrider 64 kB och `request` endast innehåller adressfältet en rendezvous-socket måste införas för att hämta den `request`. När en rendezvous-socket har upprättats levereras svar på respektive klient och efterföljande förfrågningar från respektive klienten via rendezvous-socket medan den kvar.

Den `address` URL i den `request` måste användas som-är för att fastställa rendezvous-socket, men innehåller följande parametrar:

| Parameter      | Krävs | Beskrivning
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Ja      | Parametern måste vara för att acceptera en socket `sb-hc-action=request`

Om det uppstår ett fel kan i tjänsten svara på följande sätt:

| Kod | Fel           | Beskrivning
| ---- | --------------- | -----------------------------------
| 400  | Ogiltig förfrågan | Okänd action eller URL som är inte giltigt.
| 403  | Förbjudna       | URL: en har gått ut.
| 500  | Internt fel  | Det uppstod ett fel i tjänsten

 När anslutningen har upprättats stängs servern av WebSocket när klientens HTTP socket stängs av, eller med följande status:

| WS-Status | Beskrivning                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | Avsändaren klienten avslutar anslutningen.                                    |
| 1001      | Hybridanslutningen sökvägen har tagits bort eller inaktiverats.                        |
| 1008      | Säkerhets-token har upphört att gälla, därför auktoriseringsprincipen överskrids. |
| 1011      | Något gick fel i tjänsten.                                            |


#### <a name="listener-token-renewal"></a>Lyssnare token förnyelse

När lyssnare-token upphör snart att gälla, ersätter den den genom att skicka ett SMS ram till tjänsten via etablerade kontrollkanalen. Meddelandet innehåller en JSON-objekt som kallas `renewToken`, som definierar följande egenskap just nu:

* **token** – en giltig, URL-kodade Service Bus delade åtkomsttoken för namnområdet eller Hybridanslutning som ger den **lyssna** rätt.

```json
{
  "renewToken": {
    "token":
      "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"
  }
}
```

Om token verifieringen misslyckas åtkomst nekas och Molntjänsten stängs kontrollkanal WebSocket med ett fel. Annars finns inget svar.

| WS-Status | Beskrivning                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1008      | Säkerhets-token har upphört att gälla, därför auktoriseringsprincipen överskrids. |

### <a name="web-socket-connect-protocol"></a>Web Socket ansluta protokoll

Avsändaren protokollet är identiska effektivt sätt som en lyssnare har upprättats.
Målet är maximal insyn för WebSocket slutpunkt till slutpunkt. Den adress som ska ansluta till är desamma som för lyssnare, men ”åtgärden” skiljer sig och token måste ha ett annat tillstånd:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sbc-hc-token=...
```

Den _namnområde adress_ är fullständigt kvalificerade domännamnet för Azure Relay-namnområde som är värd för Hybrid-anslutningen, vanligtvis i formatet `{myname}.servicebus.windows.net`.

Begäran kan innehålla godtycklig extra HTTP-rubriker, inklusive programdefinierade de. Alla angivna huvuden flöda till lyssnaren och finns på den `connectHeader` objekt av den **acceptera** kontroll.

Frågealternativen sträng parametern är följande:

| Param          | Krävs? | Beskrivning
| -------------- | --------- | -------------------------- |
| `sb-hc-action` | Ja       | Parametern måste vara för rollen avsändaren `sb-hc-action=connect`.
| `{path}`       | Ja       | (se följande punkt)
| `sb-hc-token`  | Ja\*     | Lyssnaren måste ange en giltig, URL-kodade Service Bus delad åtkomst-Token för namnområdet eller Hybridanslutning som ger den **skicka** rätt.
| `sb-hc-id`     | Nej        | Ett valfritt ID som möjliggör slutpunkt till slutpunkt diagnostikspårning och görs tillgängligt för lyssnaren under acceptera handskakning.

 Den `{path}` URL-kodade namnområde sökvägen till den förkonfigurerade Hybridanslutning som du vill registrera den här lyssnaren. Den `path` uttryck kan utökas med ett suffix och stränguttryck frågan för att kommunicera ytterligare. Om Hybrid-anslutningen är registrerad under sökvägen `hyco`, `path` uttrycket kan vara `hyco/suffix?param=value&...` följt av frågan string-parametrar som anges här. En fullständig uttryck kan sedan vara följande:

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sbc-hc-token=...
```

Den `path` uttryck som skickas med lyssnaren i adressen URI i meddelandet ”accepterar” kontroll.

Om WebSocket-anslutningen misslyckas på grund av Hybrid anslutningssökvägen inte registreras, en ogiltig eller saknas token eller några andra fel, tillhandahålls fel feedback med vanlig HTTP 1.1 status feedback modellen. Statusbeskrivningen innehåller ett fel spårnings-ID som kan överföras till Azure-supportpersonal:

| Kod | Fel          | Beskrivning
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Kunde inte hittas      | Hybridanslutningen sökväg är ogiltig eller den grundläggande Webbadressen är felaktig.
| 401  | Behörighet saknas   | Säkerhetstoken är saknas eller felaktig eller ogiltig.
| 403  | Förbjudna      | Säkerhetstoken är inte giltigt för den här sökvägen och för den här åtgärden.
| 500  | Internt fel | Något gick fel i tjänsten.

Om WebSocket-anslutningen avsiktligt stängs av tjänsten när den ursprungligen har ställts in orsaken till detta så överförs med hjälp av lämplig WebSocket-protokollet felkoden tillsammans med ett beskrivande felmeddelande som innehåller också en spårnings-ID: t.

| WS-Status | Beskrivning
| --------- | ------------------------------------------------------------------------------- 
| 1000      | Lyssnaren Stäng socket.
| 1001      | Hybridanslutningen sökvägen har tagits bort eller inaktiverats.
| 1008      | Säkerhets-token har upphört att gälla, därför auktoriseringsprincipen överskrids.
| 1011      | Något gick fel i tjänsten.

### <a name="http-request-protocol"></a>HTTP-begäran-protokollet

Protokollet HTTP-begäran kan godtyckligt HTTP-begäranden, utom protokollet uppgraderingar.
HTTP-begäranden som pekar på entitetens reguljära runtime-adress, utan $hc infix som används för hybridanslutningar WebSocket-klienter.

```
https://{namespace-address}/{path}?sbc-hc-token=...
```

Den _namnområde adress_ är fullständigt kvalificerade domännamnet för Azure Relay-namnområde som är värd för Hybrid-anslutningen, vanligtvis i formatet `{myname}.servicebus.windows.net`.

Begäran kan innehålla godtycklig extra HTTP-rubriker, inklusive programdefinierade de. Alla angivna sidhuvud, utom de som definierats direkt i RFC7230 (se [begärandemeddelandet](#Request message)) flöda till lyssnaren och finns på den `requestHeader` objekt av den **begäran** meddelande.

Frågealternativen sträng parametern är följande:

| Param          | Krävs? | Beskrivning
| -------------- | --------- | ---------------- |
| `sb-hc-token`  | Ja\*     | Lyssnaren måste ange en giltig, URL-kodade Service Bus delad åtkomst-Token för namnområdet eller Hybridanslutning som ger den **skicka** rätt.

Token kan också köras antingen i den `ServiceBusAuthorization` eller `Authorization` HTTP-huvud. Token kan utelämnas om Hybrid-anslutningen är konfigurerad för att tillåta anonyma begäranden.

Eftersom tjänsten effektivt fungerar som en proxy, även om inte som en HTTP-proxy som true, antingen läggs en `Via` sidhuvud eller annoterar den befintliga `Via` rubrik som är kompatibla med [RFC7230, avsnitt 5.7.1](https://tools.ietf.org/html/rfc7230#section-5.7.1).
Tjänsten lägger till Relay namnområde värdnamnet till `Via`.

| Kod | Meddelande  | Beskrivning                    |
| ---- | -------- | ------------------------------ |
| 200  | Ok       | Begäran har hanterats av minst en lyssnare.  |
| 202  | Accepterad | Begäran har godkänts av minst en lyssnare. |

Om det uppstår ett fel kan i tjänsten svara på följande sätt. Om svaret kommer från tjänsten eller lyssnaren kan identifieras via förekomst av den `Via` rubrik. Om rubriken finns är svaret från lyssnaren.

| Kod | Fel           | Beskrivning
| ---- | --------------- |--------- |
| 404  | Kunde inte hittas       | Hybridanslutningen sökväg är ogiltig eller den grundläggande Webbadressen är felaktig.
| 401  | Behörighet saknas    | Säkerhetstoken är saknas eller felaktig eller ogiltig.
| 403  | Förbjudna       | Säkerhetstoken är inte giltigt för den här sökvägen och för den här åtgärden.
| 500  | Internt fel  | Något gick fel i tjänsten.
| 503  | Felaktig gateway     | Begäran kan inte dirigeras till en lyssnare.
| 504  | Gateway-Timeout | Begäran har vidarebefordrats till en lyssnare, men lyssnaren godkände inte emot inom den nödvändiga tidsperioden.

## <a name="next-steps"></a>Nästa steg

* [Vanliga frågor och svar om Relay](relay-faq.md)
* [Skapa ett namnområde](relay-create-namespace-portal.md)
* [Kom igång med .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Kom igång med Node](relay-hybrid-connections-node-get-started.md)
