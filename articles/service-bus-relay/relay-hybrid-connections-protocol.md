---
title: Azure Relay-Hybridanslutningar protokollet guide | Microsoft Docs
description: Azure Relay-Hybridanslutningar-protokollguide.
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
ms.openlocfilehash: e96d0103a03e841f39e8adb88215f6d6e24a305a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60420053"
---
# <a name="azure-relay-hybrid-connections-protocol"></a>Azure Relay-Hybridanslutningar-protokoll

Azure Relay är en av de viktigaste funktionen pelare i Azure Service Bus-plattformen. Den nya _Hybridanslutningar_ möjligheterna för Relay är en säker öppet protokoll vidareutveckling baserat på HTTP och WebSockets. Det ersätter den tidigare _BizTalk Services_ funktion som har skapats på en protokoll-grund. Integrering av Hybridanslutningar i Azure App Services kommer att fortsätta att fungera som – är.

Hybridanslutningar kan dubbelriktad binärdataström kommunikation och enkel datagram flöde mellan två nätverksprogram. Ena eller båda parterna finns bakom en NAT eller brandvägg.

Den här artikeln beskriver klientsidan samverkan med relay Hybridanslutningar för att ansluta klienter i lyssnare och avsändaren roller. Här beskrivs också hur lyssnare acceptera nya anslutningar och förfrågningar.

## <a name="interaction-model"></a>Interaktion modell

Relay-Hybridanslutningar ansluter två parter genom att tillhandahålla en rendezvous-plats i Azure-molnet som parter kan upptäcka och ansluta till från sina egna nätverksperspektiv. Rendezvous-punkt kallas ”Hybridanslutning” i den här och annan dokumentation i API: er och även i Azure-portalen. Hybrid Connections tjänstslutpunkten kallas ”tjänst” för resten av den här artikeln.

Tjänsten tillåter för att vidarebefordra socketanslutningar för webb- och HTTP (S)-begäranden och svar.

Modellen interaktion leans på den terminologi som upprättats av många andra nätverk API: er. Det finns en lyssnare som först visar kan hantera inkommande anslutningar, och därefter accepterar dem när de tas emot. En klient ansluter till lyssnaren och förväntas anslutningen ska godkännas för att upprätta en dubbelriktad kommunikation sökväg på den andra sidan. ”Ansluta”, ”Listen” och ”godkänner” är samma villkor som du hittar i de flesta socket API: er.

Alla vidarebefordrande modell har parterna utgående anslutningar upprättas för en tjänstslutpunkt. Detta gör av ”lyssnaren” även en ”klient” talspråkliga används och kan också göra att andra terminologi överlagringar. De exakta termer som används därför för Hybrid Connections är följande:

Program på båda sidorna av en anslutning kallas ”klienter”, eftersom de är klienter till tjänsten. Den klient som väntar och accepterar anslutningar är ”lyssnaren” eller kallas för ”lyssnaren roll”. Den klient som initierar en ny anslutning till en lyssnare via tjänsten kallas ”avsändaren” eller finns i ”avsändaren roll”.

### <a name="listener-interactions"></a>Lyssnaren interaktioner

Lyssnaren har fem interaktioner med tjänsten; alla beskrivs wire senare i den här artikeln i referensavsnittet.

Lyssna och acceptera begäran meddelanden tas emot från tjänsten. Förnya, och Ping operations skickas av lyssnaren.

#### <a name="listen-message"></a>Lyssna meddelande

Om du vill ange readiness-tjänsten som en lyssnare är redo att acceptera anslutningar, skapas en utgående WebSocket-anslutning. Handskakningen anslutningen innehåller namnet på en Hybrid-anslutning som konfigurerats på Relay-namnområde och en säkerhetstoken som ger ”Listen” rätt på som namn.

När WebSocket accepteras av tjänsten, registreringen är klar och etablerade WebSocket sparas alive som ”kontrollkanal” för att aktivera alla efterföljande interaktioner. Tjänsten kan upp till 25 samtidiga lyssnare en Hybridanslutning. Kvoten för AppHooks ska fastställas.

För Hybridanslutningar, om det finns två eller flera aktiva lyssnare balanseras inkommande anslutningar mellan dem i slumpmässig ordning. rättvis fördelning görs med bästa prestanda.

#### <a name="accept-message"></a>Godkänn meddelandet

När en avsändare öppnas en ny anslutning på tjänsten, väljer tjänsten och meddelar en av de aktiva lyssnarna på Hybridanslutningen. Det här meddelandet skickas till lyssnaren via öppna kontrollkanalen som ett JSON-meddelande. Meddelandet innehåller URL: en för den WebSocket-slutpunkt som lyssnaren måste ansluta till för att acceptera anslutningen.

URL: en kan och måste användas direkt av lyssnare utan extra arbete.
Kodad information är endast giltig för en kort tidsperiod, i stort sett så länge som avsändaren är villigt att vänta tills anslutningen är upprättad från slutpunkt till slutpunkt. Maximalt att anta är 30 sekunder. URL: en kan endast användas för en lyckade anslutningsförsöket. Så snart WebSocket-anslutning med rendezvous-URL: en har upprättats kan alla ytterligare aktiviteter på den här WebSocket-enhetsnätverket från och till avsändaren. Detta sker utan inblandning eller tolkning av tjänsten.

### <a name="request-message"></a>Meddelande om begäran

Förutom WebSocket anslutningar, kan lyssnaren även få bildrutor för HTTP-begäran från en avsändare om den här funktionen aktiveras uttryckligen på Hybridanslutningen.

Lyssnare som kopplas till Hybridanslutningar med HTTP-stöd måste hantera den `request` gest. En lyssnare som kan inte hantera `request` och därför gör upprepade timeout-fel när ansluts kan vara Svartlistad av tjänsten i framtiden.

HTTP ramens rubrik metadata översätts till JSON för enklare hantering av lyssnare-ramverket också eftersom HTTP-huvud parsning bibliotek är sällsynta än JSON-parser. HTTP-metadata som endast är relevanta för relationen mellan avsändaren och Relay HTTP-gateway, inklusive auktoriseringsinformation, vidarebefordras inte. HTTP-begärandetext överförs transparent som binära WebSocket bildrutor.

Lyssnaren kan svara på HTTP-begäranden med en gest motsvarande svar.

Begäran/svar-flödet använder kontrollkanalen som standard, men kan ”uppgraderas” till en distinkt rendezvous WebSocket varje gång som krävs. Distinkta WebSocket anslutningar förbättra dataflödet för varje klient konversationen, men de belastar lyssnaren med fler anslutningar som behöver hanteras, vilket kanske inte kan önskan för lightweight-klienter.

På kontrollkanalen är begäran och svar organ begränsade till högst 64 kB stora. HTTP-huvud metadata är begränsad till högst 32 kB. Om begäran eller svaret överstiger gränsen, lyssnaren måste uppgradera till en rendezvous WebSocket med en gest som motsvarar hantering av den [acceptera](#accept-message).

För förfrågningar om bestämmer tjänsten sig för att dirigera begäranden över kontrollkanalen. Detta inkluderar, men inte begränsat till fall om en begäran överskrider 64 kB (huvuden plus brödtext) en gång eller om begäran skickades med [”segmentvis” transfer-encoding](https://tools.ietf.org/html/rfc7230#section-4.1) och tjänsten har väntar för begäran överskrider 64 kB eller läsning av begäran är inte omedelbar. Om tjänsten väljer att leverera begäran över rendezvous, skickar endast rendezvous-adressen till lyssnaren.
Lyssnaren upprätta sedan rendezvous WebSocket och tjänsten levererar snabbt fullständig begärd inklusive organ över rendezvous WebSocket. Svaret måste också använda rendezvous WebSocket.

För förfrågningar via kontrollkanalen, beslutar lyssnaren om du vill svara via kontrollkanalen eller via rendezvous. Tjänsten måste innehålla en rendezvous-adress med varje begäran dirigeras över kontrollkanalen. Den här adressen är endast giltig för att uppgradera från den aktuella begäran.

Om lyssnaren väljer att uppgradera, ansluter och levererar snabbt svaret över etablerade rendezvous-socket.

En gång rendezvous WebSocket har upprättats, lyssnaren ska underhålla den för ytterligare hantering av begäranden och svar från samma klient. Tjänsten ska underhålla WebSocket för så länge HTTPS socket anslutning med avsändaren kvarstår och dirigerar alla efterföljande förfrågningar från sändaren via behålla WebSocket. Om lyssnaren väljer att ta bort rendezvous WebSocket från sidan, tjänsten också att ta bort anslutningen till avsändaren, oavsett om en efterföljande begäran kanske redan pågår.

#### <a name="renew-operation"></a>Förnya åtgärden

Den säkerhetstoken som måste användas för att registrera lyssnaren och bibehålla kontrollkanal kan upphöra att gälla när lyssnaren är aktiv. Utgångsdatum för token påverkar inte pågående anslutningar, men det innebär att kontrollkanal tas bort av tjänsten vid eller strax efter den tidpunkt då har upphört att gälla. Åtgärden ”förnya” är ett JSON-meddelande som lyssnaren kan skicka att ersätta den token som är associerade med kontrollkanal, så att kontrollkanalen kan hanteras under långa perioder.

#### <a name="ping-operation"></a>Ping-åtgärd

Om kontrollkanalen förblir inaktiva under en längre tid, en mellanhand på sättet som belastning kan belastningsutjämnare eller NAT-enheter släppa TCP-anslutningen. Åtgärden ”pinga” undviker som genom att skicka en liten mängd data på den kanal som påminner alla i nätverksväg som anslutningen är avsett att och den fungerar också som ett ”live” test för lyssnaren. Om pingningen misslyckas kontrollkanalen ska betraktas som inte kan användas och lyssnaren borde återansluta.

### <a name="sender-interaction"></a>Avsändaren interaktion

Avsändaren har två interaktioner med tjänsten: det ansluter en Web-Socket eller den skickar begäranden via HTTPS. Begäranden skickas inte via en Web-Socket från rollen avsändare.

#### <a name="connect-operation"></a>Åtgärd för anslutning

Åtgärden ”ansluta” öppnas en WebSocket på tjänsten, som att ange namnet på Hybridanslutningen och (om du vill, men måste som standard) en token om ”skicka” behörighet i frågesträngen. Tjänsten sedan interagerar med lyssnaren på det sätt som beskrivs ovan och lyssnaren skapar en rendezvous-anslutning som är kopplad till den här WebSocket. När WebSocket har accepterats, är alla ytterligare interaktioner på den WebSocket med en ansluten lyssnare.

#### <a name="request-operation"></a>Förfrågan

För Hybridanslutningar har aktiverat funktionen, skicka avsändaren i stort sett obegränsade HTTP-begäranden till lyssnare.

Förutom en Relay åtkomst-token som är antingen inbäddat i frågesträngen eller i ett HTTP-huvud för begäran är Relay helt transparent för alla HTTP-åtgärder på reläadress och alla suffix i adressfältet Relay lämnar lyssnaren fullständigt kontroll över en d-to-end-auktorisering och även HTTP-tillägg-funktioner som [CORS](https://www.w3.org/TR/cors/).

Avsändaren auktorisering med Relay-slutpunkten är aktiverad som standard, men är valfritt. Ägaren av Hybridanslutningen kan välja att tillåta anonyma avsändare. Tjänsten intercept, granska och ta bort auktoriseringsinformation på följande sätt:

1. Om frågesträngen innehåller en `sb-hc-token` uttryck, uttrycket kommer alltid att tas bort från frågesträngen. Den kommer utvärderas om Relay auktorisering är aktiverat.
2. Om begärandehuvuden innehåller en `ServiceBusAuthorization` rubrik, rubriken uttrycket kommer alltid att tas bort från samlingen med rubriken.
   Den kommer utvärderas om Relay auktorisering är aktiverat.
3. Endast om Relay auktorisering slås på och om begärandehuvuden innehåller en `Authorization` rubrik och ingen av de föregående uttryck finns, rubriken ska utvärderas och tas bort. I annat fall den `Authorization`skickas alltid som – är.

Om det finns ingen aktiv lyssnare, returnerar tjänsten en 502 ”felaktig Gateway”-felkod. Om tjänsten inte visas hantera begäran, returnerar tjänsten en 504 ”Gateway-Timeout” efter 60 sekunder.

### <a name="interaction-summary"></a>Sammanfattning av interaktion

Resultatet av den här interaktionen modellen är att avsändaren klienten kommer från handskakning med en ”ren” WebSocket som är ansluten till en lyssnare och som behöver inga ytterligare preambles eller förberedelse. Den här modellen kan praktiskt taget alla befintliga WebSocket-klientimplementeringen att lätt kunna utnyttja tjänsten Hybrid Connections genom att ange en korrekt konstruerade URL till sina WebSocket klienten lager.

Rendezvous anslutningen WebSocket som lyssnaren hämtar genom att acceptera interaktionen är också rensa och kan lämnas eventuella befintliga implementeringen av WebSocket med vissa minimal extra abstraktion som skiljer mellan ”acceptera”-åtgärder på deras framework lokalt nätverk lyssnare och Hybrid Connections remote acceptera ”” åtgärder.

HTTP-begäran/svar-modellen ger avsändaren ett stort sett obegränsade HTTP-protokollet utsatt område med ett valfritt auktorisering lager. Lyssnaren hämtar ett förväg parsade rubrikavsnitt för HTTP-begäran som kan aktiveras igen i en underordnad HTTP-begäran eller enligt, med binära ramar överförs HTTP organ som hanteras. Svar använder samma format. Interaktioner med mindre än 64 KB brödtext för begäran och svar kan hanteras via en enda Web Socket som är gemensam för alla avsändare. Större begäranden och svar kan hanteras med hjälp av rendezvous-modellen.

## <a name="protocol-reference"></a>Referens för protokollet

Det här avsnittet innehåller information om protokoll-interaktioner som beskrivs ovan.

Alla WebSocket-anslutningar görs på port 443 som en uppgradering från HTTPS 1.1, som vanligtvis representeras av vissa WebSocket framework eller API: et. Beskrivningen här sparas implementering neutral känsla, utan att föreslå ett specifikt ramverk.

### <a name="listener-protocol"></a>Lyssnarprotokollet

Lyssnarprotokollet består av två anslutning gester och tre meddelandeåtgärder.

#### <a name="listener-control-channel-connection"></a>Lyssnaranslutning kontroll kanal

Kontrollkanal öppnas med att skapa en WebSocket-anslutning till:

`wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...`

Den `namespace-address` är fullständigt kvalificerade domännamnet för Azure Relay-namnområde som är värd för Hybridanslutningen, vanligtvis i formatet `{myname}.servicebus.windows.net`.

Frågealternativ för sträng-parametern är som följer.

| Parameter        | Obligatoriskt | Beskrivning
| ---------------- | -------- | -------------------------------------------
| `sb-hc-action`   | Ja      | Parametern måste vara för rollen lyssnare **sb-hc-action = listen**
| `{path}`         | Ja      | Förkonfigurerade Hybridanslutningen att registrera den här lyssnaren på URL-kodade namnområde sökväg. Det här uttrycket läggs till i fast `$hc/` sökvägsdelen.
| `sb-hc-token`    | Ja\*    | Lyssnaren måste ange en giltig, URL-kodade Service Bus delad åtkomst-Token för namnområde eller Hybridanslutning som ger den **lyssna** rätt.
| `sb-hc-id`       | Nej       | Den här klienten anger valfritt ID kan diagnostikspårning för slutpunkt till slutpunkt.

Om WebSocket-anslutning misslyckas på grund av Hybridanslutningen sökvägen inte är registrerad, eller en ogiltig eller saknas-token eller några andra fel, tillhandahålls fel-feedback med hjälp av vanliga HTTP 1.1 status feedback-modellen. Statusbeskrivningen innehåller ett fel för spårnings-id som kan förmedlas till Azure supportpersonal:

| Kod | Fel          | Beskrivning
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Kunde inte hittas      | Hybridanslutning sökvägen är ogiltig eller den grundläggande Webbadressen felskriven.
| 401  | Behörighet saknas   | Säkerhetstoken är saknad eller skadad eller ogiltig.
| 403  | Förbjudna      | Säkerhetstoken är inte giltig för den här sökvägen för den här åtgärden.
| 500  | Internt fel | Något gick fel i tjänsten.

Om WebSocket-anslutning är avsiktligt stänger av tjänsten efter att det ursprungligen har ställts in orsaken till detta överförs med hjälp av lämplig WebSocket-protokoll felkoden tillsammans med ett felmeddelande som innehåller också ett spårnings-ID. Tjänsten stängs inte kontrollkanal utan påträffar ett fel. Alla ren avstängning av är klient kontrolleras.

| WS-Status | Beskrivning
| --------- | -------------------------------------------------------------------------------
| 1001      | Sökväg för Hybridanslutning har tagits bort eller inaktiverad.
| 1008      | Säkerhetstoken har upphört att gälla, därför auktoriseringsprincipen har överskridits.
| 1011      | Något gick fel i tjänsten.

#### <a name="accept-handshake"></a>Acceptera handskakning

Meddelandet ”godkänner” skickas av tjänsten till lyssnaren över tidigare upprättat kontrollkanalen som ett JSON-meddelande i en WebSocket ram. Det finns inga svar på meddelandet.

Meddelandet innehåller ett JSON-objekt med namnet ”godkänner”, som definierar följande egenskaper just nu:

* **adress** – URL-strängen som ska användas för att upprätta WebSocket till tjänsten för att godkänna en inkommande anslutning.
* **ID** – den unika identifieraren för den här anslutningen. Om ID har angetts av avsändaren klienten så är värdet för avsändaren anges, annars är den ett systemgenererat värde.
* **connectHeaders** – alla HTTP-huvuden som har angetts till Relay-slutpunkten av avsändaren, vilket även innefattar sek-WebSocket-protokoll och sek-WebSocket-tillägg-rubriker.

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

Webbadress i JSON-meddelandet används av lyssnaren för att upprätta WebSocket för att godkänna eller avvisa avsändar-socket.

##### <a name="accepting-the-socket"></a>Acceptera socketen

För att godkänna, upprättar lyssnaren en WebSocket-anslutning till den angivna adressen.

Om meddelandet ”godkänner” har en `Sec-WebSocket-Protocol` rubrik som är det förväntas att lyssnaren endast accepterar WebSocket om den stöder det protokollet. Dessutom anger rubriken som WebSocket har upprättats.

Detsamma gäller för den `Sec-WebSocket-Extensions` rubrik. Om ramverket har stöd för ett tillägg, ska det in rubriken på svaret från serversidan av de nödvändiga `Sec-WebSocket-Extensions` handskakningen för tillägget.

URL: en måste användas som – är för att upprätta acceptera socket, men innehåller följande parametrar:

| Parameter      | Obligatoriskt | Beskrivning
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Ja      | För att acceptera en socket måste parametern vara `sb-hc-action=accept`
| `{path}`       | Ja      | (se följande punkt)
| `sb-hc-id`     | Nej       | Se föregående beskrivning av **id**.

`{path}` är URL-kodade namnområdessökvägen på förkonfigurerade Hybridanslutningen som du vill registrera den här lyssnaren. Det här uttrycket läggs till i fast `$hc/` sökvägsdelen.

Den `path` uttryck kan utökas med ett suffix och ett frågeuttryck för strängen som följer den registrerade namnen efter ett separertratten snedstreck.
Detta gör att avsändaren klienten att skicka dispatch argument till den tar emot lyssnaren när det inte går att inkludera HTTP-huvuden. Väntas att lyssnare framework analyserar fast sökvägsdelen och registrerade namnet från sökvägen och gör resten, eventuellt utan någon fråga strängargument prefixet `sb-`, tillgängliga för programmet för att bestämma Om du vill acceptera anslutningen.

Mer information finns i avsnittet ”avsändaren protokoll”.

Om det finns ett fel, kan tjänsten svara på följande sätt:

| Kod | Fel          | Beskrivning
| ---- | -------------- | -----------------------------------
| 403  | Förbjudna      | Webbadressen är inte giltig.
| 500  | Internt fel | Det uppstod ett fel i tjänsten

 När anslutningen har upprättats stängs servern av WebSocket när avsändaren WebSocket stänger, eller med följande status:

| WS-Status | Beskrivning                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | Avsändaren klienten avslutar anslutningen.                                    |
| 1001      | Sökväg för Hybridanslutning har tagits bort eller inaktiverad.                        |
| 1008      | Säkerhetstoken har upphört att gälla, därför auktoriseringsprincipen har överskridits. |
| 1011      | Något gick fel i tjänsten.                                            |

##### <a name="rejecting-the-socket"></a>Avvisa socket

 Avvisa socket efter att ha kontrollerat det `accept` meddelande kräver en liknande handskakning så att statuskoden och beskrivning av kommunikation av orsak för avvisningen kan flöda tillbaka till avsändaren.

 Protokollet designen har valts här är att använda en WebSocket-handskakning (som är utformade för att sluta med en definierad feltillstånd) så att lyssnare klientimplementeringar kan fortsätta att förlita dig på en WebSocket-klient och behöver inte använda någon extra, utan någon HTTP-klient.

 Om du vill avvisa socket klienten tar den URI-adressen från den `accept` meddelande och lägger till två parametrar för frågesträngen, enligt följande:

| Param                   | Obligatoriskt | Beskrivning                              |
| ----------------------- | -------- | ---------------------------------------- |
| sb-hc-statusCode        | Ja      | Numeriska HTTP-statuskod.                |
| sb-hc-statusDescription | Ja      | Mänskliga läsbara orsak för avvisningen. |

Den resulterande URI: N används sedan för att upprätta en WebSocket-anslutning.

När du går igenom korrekt, misslyckas denna handskakning avsiktligt med en HTTP-felkod 410, eftersom inga WebSocket har upprättats. Om något går fel, beskriver följande koder felet:

| Kod | Fel          | Beskrivning                          |
| ---- | -------------- | ------------------------------------ |
| 403  | Förbjudna      | Webbadressen är inte giltig.                |
| 500  | Internt fel | Något gick fel i tjänsten. |

#### <a name="request-message"></a>Meddelande om begäran

Den `request` meddelande skickas av tjänsten till lyssnaren via kontrollkanalen. Samma meddelande skickas även över rendezvous WebSocket när du har.

Den `request` består av två delar: en rubrik och binär brödtext bildruta.
Om det finns ingen text, har brödtext bildrutor utelämnats. Indikator för det om det finns en brödtext är boolean `body` -egenskapen i meddelandet med begäran.

För en begäran med en begärandetext strukturen kan se ut så här:

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

Lyssnaren måste hantera tar emot begärandetexten dela upp på flera binära bildrutor (se [WebSocket fragment](https://tools.ietf.org/html/rfc6455#section-5.4)).
Begäran avslutas när en binär ram med FIN-flaggan inställd har tagits emot.

En begäran utan en brödtext har endast en ram.

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

JSON-innehåll för `request` är följande:

* **adress** -URI-sträng. Det här är rendezvous-adressen som ska användas för den här begäran. Om den inkommande begäranden är större än 64 kB, resten av det här meddelandet är tomt och klienten måste initiera en rendezvous-handskakning som motsvarar den `accept` åtgärden som beskrivs nedan. Tjänsten kommer sedan att placera den fullständiga `request` för den etablerade Web-socketen. Om svaret kan förväntas överskrida 64 kB, måste lyssnaren också starta en rendezvous-handskakning och överföra svaret över etablerade Web-socket.
* **ID** – sträng. Den unika identifieraren för den här begäran.
* **requestHeaders** – det här objektet innehåller alla HTTP-huvuden som har angetts till slutpunkten av avsändaren, med undantag av auktoriseringsinformation som förklaras [ovan](#request-operation), och rubriker som strikt relaterar till den anslutning med gatewayen. Mer specifikt alla rubriker har definierats eller reserverade i [RFC7230](https://tools.ietf.org/html/rfc7230), utom `Via`, tas bort och inte vidarebefordras:

  * `Connection` (RFC7230 avsnitt 6.1)
  * `Content-Length`  (RFC7230 avsnitt 3.3.2)
  * `Host`  (RFC7230 avsnittet 5.4)
  * `TE`  (RFC7230 avsnittet 4.3)
  * `Trailer`  (RFC7230 avsnittet 4.4)
  * `Transfer-Encoding`  (RFC7230 avsnittet 3.3.1)
  * `Upgrade` (RFC7230 avsnittet 6.7)
  * `Close`  (RFC7230 avsnittet 8.1)

* **requestTarget** – sträng. Den här egenskapen innehåller den [”begäran Target” (RFC7230, avsnittet 5.3)](https://tools.ietf.org/html/rfc7230#section-5.3) för begäran. Detta inkluderar strängdelen frågan som tas bort för alla `sb-hc-` prefixet parametrar.
* **metoden** -sträng. Det här är metoden för begäran, per [RFC7231, avsnitt 4](https://tools.ietf.org/html/rfc7231#section-4). Den `CONNECT` metoden får inte användas.
* **brödtext** – booleskt. Anger om en eller flera binära brödtext ramens följer.

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

Mottagaren måste svara. Upprepade fel att svara på begäranden samtidigt som anslutningen kan resultera i lyssnaren komma Svartlistad.

Svar kan skickas i valfri ordning, men varje begäran måste svarat på inom 60 sekunder eller leveransen kommer att rapporteras som har misslyckats. Tidsgränsen för 60-sekunders räknas tills de `response` ram har tagits emot av tjänsten. Ett pågående svar med flera binära bildrutor kan inte bli inaktiv i mer än 60 sekunder eller så avslutas.

Om en begäran tas emot via kontrollkanalen svaret måste antingen skickas på kontrollkanalen från där begäran togs emot. den måste skickas via en rendezvous-kanal

Svaret är ett JSON-objekt med namnet ”svar”. Regler för hantering av innehåll i begärandetexten är samma sätt som med den `request` meddelandet och baserat på den `body` egenskapen.

* **requestId** – sträng. KRÄVS. Den `id` egenskapsvärdet för den `request` meddelande besvaras.
* **statusCode** – nummer. KRÄVS. ett numeriskt HTTP-statuskod som visar resultatet av meddelandet. Alla statuskoder för [RFC7231, avsnitt 6](https://tools.ietf.org/html/rfc7231#section-6) tillåts, förutom för [502 ”felaktig Gateway”](https://tools.ietf.org/html/rfc7231#section-6.6.3) och [504 ”Gateway-Timeout”](https://tools.ietf.org/html/rfc7231#section-6.6.5).
* **Statusbeskrivning** -sträng. VALFRITT. HTTP-statuskod orsaksfras per [RFC7230, punkt 3.1.2](https://tools.ietf.org/html/rfc7230#section-3.1.2)
* **responseHeaders** – HTTP-huvuden anges i en extern HTTP-svar.
  Precis som med de `request`, RFC7230 definierade sidhuvuden inte får användas.
* **brödtext** – booleskt. Anger om binära brödtext bildruta follow(s).

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

För svar som överskrider 64 kB skickas svaret via en rendezvous-socket. Även om begäran överskrider 64 kB och `request` endast innehåller adressfältet en rendezvous-socket måste införas för att hämta den `request`. När en rendezvous-socket har upprättats, att svar på respektive klient och efterföljande förfrågningar från respektive klienten leverera över rendezvous-socket medan den finns kvar.

Den `address` URL: en i den `request` får användas som – är för att upprätta rendezvous-socket, men innehåller följande parametrar:

| Parameter      | Obligatoriskt | Beskrivning
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Ja      | För att acceptera en socket måste parametern vara `sb-hc-action=request`

Om det finns ett fel, kan tjänsten svara på följande sätt:

| Kod | Fel           | Beskrivning
| ---- | --------------- | -----------------------------------
| 400  | Ogiltig begäran | Okänd åtgärd eller URL: en inte giltig.
| 403  | Förbjudna       | URL: en har upphört att gälla.
| 500  | Internt fel  | Det uppstod ett fel i tjänsten

 När anslutningen har upprättats stängs servern av WebSocket när klientens HTTP socket stängs av eller med följande status:

| WS-Status | Beskrivning                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | Avsändaren klienten avslutar anslutningen.                                    |
| 1001      | Sökväg för Hybridanslutning har tagits bort eller inaktiverad.                        |
| 1008      | Säkerhetstoken har upphört att gälla, därför auktoriseringsprincipen har överskridits. |
| 1011      | Något gick fel i tjänsten.                                            |


#### <a name="listener-token-renewal"></a>Förnyelse av token-lyssnare

När den lyssnare token snart upphör att gälla, ersätter den den genom att skicka ett textmeddelande för ramens till tjänsten via den etablerade kontrollkanalen. Meddelandet innehåller ett JSON-objekt som kallas `renewToken`, som definierar följande egenskap just nu:

* **token** – en giltig, URL-kodade Service Bus delad åtkomst-token för namnområde eller Hybridanslutning som ger den **lyssna** rätt.

```json
{
  "renewToken": {
    "token":
      "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"
  }
}
```

Om token verifieringen misslyckas, åtkomst nekad och Molntjänsten stängs kontrollkanal WebSocket med ett fel. Annars är inget svar.

| WS-Status | Beskrivning                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1008      | Säkerhetstoken har upphört att gälla, därför auktoriseringsprincipen har överskridits. |

### <a name="web-socket-connect-protocol"></a>Web Socket ansluta protokoll

Avsändar-protokollet är effektivt samma sätt som en lyssnare har upprättats.
Målet är högsta genomskinlighet för slutpunkt till slutpunkt-WebSocket. Den adress som ska ansluta till är samma som för lyssnare, men ”action” skiljer sig och token måste ha ett annat tillstånd:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sbc-hc-token=...
```

Den _namnområde-address_ är fullständigt kvalificerade domännamnet för Azure Relay-namnområde som är värd för Hybridanslutningen, vanligtvis i formatet `{myname}.servicebus.windows.net`.

Begäran kan innehålla godtycklig extra HTTP-huvuden, inklusive programdefinierade som. Alla angivna sidhuvudena flöda till lyssnaren och finns på den `connectHeader` objekt av den **acceptera** kontroll.

Frågealternativ för sträng-parametern är följande:

| Param          | Krävs? | Beskrivning
| -------------- | --------- | -------------------------- |
| `sb-hc-action` | Ja       | Parametern måste vara för rollen avsändaren `sb-hc-action=connect`.
| `{path}`       | Ja       | (se följande punkt)
| `sb-hc-token`  | Ja\*     | Lyssnaren måste ange en giltig, URL-kodade Service Bus delad åtkomst-Token för namnområde eller Hybridanslutning som ger den **skicka** rätt.
| `sb-hc-id`     | Nej        | Ett valfritt ID som aktiverar diagnostikspårning för slutpunkt till slutpunkt och få tillgång till lyssnaren under accept-handskakning.

 Den `{path}` är URL-kodade namnområdessökvägen på förkonfigurerade Hybridanslutningen som du vill registrera den här lyssnaren. Den `path` uttryck kan utökas med ett suffix och stränguttryck fråga för att kommunicera ytterligare. Om Hybridanslutningen är registrerat under sökvägen `hyco`, `path` uttryck kan vara `hyco/suffix?param=value&...` följt av frågeparametrar för strängen som anges här. En fullständig uttryck kanske sedan enligt följande:

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sbc-hc-token=...
```

Den `path` uttryck skickas vidare till lyssnare i adress-URI som ingår i fönstret ”godkänner” kontroll.

Om WebSocket-anslutning misslyckas på grund av Hybridanslutningen sökvägen inte registreras, en ogiltig eller saknas-token eller några andra fel, tillhandahålls fel-feedback med hjälp av vanliga HTTP 1.1 status feedback-modellen. Statusbeskrivningen innehåller ett fel spårnings-ID som kan förmedlas till support för Azure-personal:

| Kod | Fel          | Beskrivning
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Kunde inte hittas      | Hybridanslutning sökvägen är ogiltig eller den grundläggande Webbadressen felskriven.
| 401  | Behörighet saknas   | Säkerhetstoken är saknad eller skadad eller ogiltig.
| 403  | Förbjudna      | Säkerhetstoken är inte giltigt för den här sökvägen och för den här åtgärden.
| 500  | Internt fel | Något gick fel i tjänsten.

Om WebSocket-anslutning är avsiktligt stänger av tjänsten när den ursprungligen har ställts in orsaken till detta överförs med hjälp av lämplig WebSocket-protokoll felkoden tillsammans med ett felmeddelande som innehåller också en spårnings-ID .

| WS-Status | Beskrivning
| --------- | ------------------------------------------------------------------------------- 
| 1000      | Lyssnaren Stäng socket.
| 1001      | Sökväg för Hybridanslutning har tagits bort eller inaktiverad.
| 1008      | Säkerhetstoken har upphört att gälla, därför auktoriseringsprincipen har överskridits.
| 1011      | Något gick fel i tjänsten.

### <a name="http-request-protocol"></a>HTTP-begäran-protokoll

Protokollet HTTP-begäran kan godtyckligt HTTP-begäranden, förutom protokollet uppgraderingar.
HTTP-begäranden är marken entitetens regelbundna runtime-adress, utan de $hc infix som används för hybridanslutningar WebSocket-klienter.

```
https://{namespace-address}/{path}?sbc-hc-token=...
```

Den _namnområde-address_ är fullständigt kvalificerade domännamnet för Azure Relay-namnområde som är värd för Hybridanslutningen, vanligtvis i formatet `{myname}.servicebus.windows.net`.

Begäran kan innehålla godtycklig extra HTTP-huvuden, inklusive programdefinierade som. Alla angivna rubriker, utom de som definierats direkt i RFC7230 (se [begärandemeddelandet](#Request message)) flöda till lyssnaren och finns på den `requestHeader` objekt av den **begäran** meddelande.

Frågealternativ för sträng-parametern är följande:

| Param          | Krävs? | Beskrivning
| -------------- | --------- | ---------------- |
| `sb-hc-token`  | Ja\*     | Lyssnaren måste ange en giltig, URL-kodade Service Bus delad åtkomst-Token för namnområde eller Hybridanslutning som ger den **skicka** rätt.

Token kan också utföras antingen den `ServiceBusAuthorization` eller `Authorization` HTTP-huvud. Token kan utelämnas om Hybridanslutningen har konfigurerats för att tillåta anonyma begäranden.

Eftersom tjänsten effektivt fungerar som en proxy, även om inte som en HTTP-proxy som SANT antingen läggs en `Via` rubrik eller annoterar den befintliga `Via` rubrik som är kompatibla med [RFC7230, avsnitt 5.7.1](https://tools.ietf.org/html/rfc7230#section-5.7.1).
Tjänsten lägger till värdnamnet för Relay-namnområde till `Via`.

| Kod | Meddelande  | Beskrivning                    |
| ---- | -------- | ------------------------------ |
| 200  | Ok       | Begäran har hanterats av minst en lyssnare.  |
| 202  | Accepterad | Begäran har godkänts av minst en lyssnare. |

Om det finns ett fel, kan tjänsten svara på följande sätt. Om svaret samlas in från tjänsten eller lyssnaren kan identifieras via förekomst av den `Via` rubrik. Om rubriken finns är svaret från lyssnaren.

| Kod | Fel           | Beskrivning
| ---- | --------------- |--------- |
| 404  | Kunde inte hittas       | Hybridanslutning sökvägen är ogiltig eller den grundläggande Webbadressen felskriven.
| 401  | Behörighet saknas    | Säkerhetstoken är saknad eller skadad eller ogiltig.
| 403  | Förbjudna       | Säkerhetstoken är inte giltigt för den här sökvägen och för den här åtgärden.
| 500  | Internt fel  | Något gick fel i tjänsten.
| 503  | Felaktig gateway     | Begäran kan inte dirigeras till valfri lyssnaren.
| 504  | Gateway-timeout | Begäran har dirigerats till en lyssnare, men lyssnaren bekräfta inte mottagandet i den begärda tiden.

## <a name="next-steps"></a>Nästa steg

* [Vanliga frågor och svar om Relay](relay-faq.md)
* [Skapa ett namnområde](relay-create-namespace-portal.md)
* [Kom igång med .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Kom igång med Node](relay-hybrid-connections-node-get-started.md)
