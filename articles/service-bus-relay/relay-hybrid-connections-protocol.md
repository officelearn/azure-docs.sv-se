---
title: Azure Relay Hybridanslutningar protokollet guiden | Microsoft Docs
description: "Guide för Azure Relay Hybridanslutningar-protokollet."
services: service-bus-relay
documentationcenter: na
author: clemensv
manager: timlt
editor: 
ms.assetid: 149f980c-3702-4805-8069-5321275bc3e8
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: 9d015678dbd99b8d978c2c8200b36bf51cac8893
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-relay-hybrid-connections-protocol"></a>Azure Relay Hybridanslutningar protokoll
Azure Relay är en av kapaciteten för viktiga pelare i Azure Service Bus-plattformen. Den nya *Hybridanslutningar* möjligheterna för vidarebefordran är en säker, öppna protokoll utvecklingen baserat på http- och WebSockets. Det ersätter den tidigare lika med namnet *BizTalk-tjänst* funktion som har byggt på protokoll. Integrering av Hybridanslutningar i Azure App Service fortsätter att fungera som-är.

Hybridanslutningar möjliggör dubbelriktad, binär dataström kommunikation mellan två nätverksprogram då ena eller båda parterna kan finnas bakom NAT och brandväggar. Den här artikeln beskriver klientsidan samverkan med Hybridanslutningar relay för att ansluta klienter i lyssnaren och avsändaren roller och hur lyssnare ta emot nya anslutningar.

## <a name="interaction-model"></a>Interaktion modellen
Hybridanslutningar relay ansluter två parter genom att tillhandahålla en rendezvous-punkt i Azure-molnet både parter kan identifiera och ansluta till ur sina egna nätverk. Den tidpunkten och rendezvous kallas ”Hybridanslutning” i den här och övrig dokumentation i de API: er och även i Azure-portalen. Tjänstslutpunkten Hybridanslutningar kallas ”tjänst” för resten av den här artikeln. Interaktion modellen leans på nomenklaturen upprättas av många andra nätverksfunktioner API: er.

Det finns en lyssnare som först visar kan hantera inkommande anslutningar och accepterar dem senare när de tas emot. Det finns en anslutande klienter som ansluter till lyssnaren förväntas anslutningen ska godkännas för att upprätta en dubbelriktad kommunikation sökväg på den andra sidan.
”Anslut”, ”lyssna” och ”accepterar” är samma villkor som du hittar i de flesta socket API: er.

Alla vidarebefordrande kommunikation har någon av parterna utgående anslutningar upprättas mot en tjänstslutpunkt som gör att ”lyssnaren” också ”klient” talspråkliga används och kan göra att andra terminologi överlagringar. De exakta termer som vi därför använda Hybridanslutningar är följande:

Program på båda sidor av en anslutning kallas ”klienter”, eftersom de är klienter till tjänsten. Den klient som väntar och accepterar anslutningar är ”lyssnaren” eller anses vara ”lyssnare rollen”. Den klient som initierar en ny anslutning till en lyssnare via service kallas ”avsändaren”, eller finns i ”avsändaren roll”.

### <a name="listener-interactions"></a>Lyssnare interaktioner
Lyssnaren har fyra interaktioner med tjänsten; alla överföring uppgifter beskrivs nedan i referensavsnittet.

#### <a name="listen"></a>Lyssna
Om du vill ange beredskap för tjänsten som en lyssnare är redo att acceptera anslutningar, skapas en utgående WebSocket-anslutning. Handskakningen anslutningen innehåller namnet på en Hybridanslutning som konfigurerats på Relay-namnområde och en säkerhetstoken som ger ”lyssna” till att namnet.
När WebSocket accepteras av tjänsten registreringen är klar och etablerade web WebSocket bevaras alive som ”kontrollkanal” för att aktivera alla efterföljande interaktioner. Tjänsten kan upp till 25 samtidiga lyssnare på en Hybrid-anslutning. Om det finns två eller flera aktiva lyssnare, balanseras inkommande anslutningar mellan dem i slumpmässig ordning. rättvis fördelning är inte säkert.

#### <a name="accept"></a>Acceptera
När en avsändare öppnas en ny anslutning på tjänsten väljer tjänsten och meddelar en aktiva lyssnare på Hybrid-anslutning. Det här meddelandet skickas till lyssnaren över öppna kontrollkanalen som ett JSON-meddelande med URL för den WebSocket-slutpunkt som lyssnaren måste ansluta till för att acceptera anslutningen.

URL: en kan och måste användas direkt av lyssnare utan någon extra arbete.
Kodad information är endast giltig för en kort tidsperiod i stort sett under den tid som avsändaren är villigt att vänta för anslutningen ska vara etablerade slutpunkt till slutpunkt, men högst 30 sekunder. URL-Adressen kan bara användas för en lyckade anslutningsförsöket. Så snart WebSocket-anslutningen med rendezvous-URL har upprättats vidarebefordras alla ytterligare aktivitet på den här WebSocket från och till avsändaren, utan några åtgärder eller tolkning av tjänsten.

#### <a name="renew"></a>Förnya
Den säkerhetstoken som måste användas för att registrera lyssnaren och underhålla kontrollkanalen kan gälla när lyssnaren är aktiv. Token upphör att gälla påverkar inte pågående anslutningar, men den orsakar kontrollkanal tas bort av tjänsten på eller strax efter den tidpunkt då har upphört att gälla. Åtgärden ”förnya” är ett JSON-meddelande som lyssnaren kan skicka Ersätt token som är associerade med kontrollkanalen, så att kontrollkanalen kan underhållas under långa perioder.

#### <a name="ping"></a>Pinga
Om kontrollkanalen hålls inaktiv för länge mellanhand på sättet, till exempel belastningen kan belastningsutjämnare eller NAT-enheter släppa TCP-anslutningen. Åtgärden ”pinga” undviker som genom att skicka en liten mängd data på den kanal som påminner alla på nätverksväg som anslutningen ska vara alive och den fungerar också som en ”live” test för lyssnaren. Om pingningen misslyckas kontrollkanalen ska betraktas som inte kan användas och lyssnaren borde återansluta.

### <a name="sender-interaction"></a>Avsändaren interaktion
Avsändaren har bara en enskild interaktion med tjänsten: ansluter den.

#### <a name="connect"></a>Anslut
Åtgärden ”ansluta” öppnar en WebSocket på tjänsten, att ange namnet på Hybrid-anslutningen och (valfritt, men måste som standard) en säkerhet token ger ”skicka” behörighet i frågesträngen. Tjänsten sedan samverkar med lyssnaren på sätt som beskrivs ovan och lyssnaren skapar en rendezvous-anslutning som är kopplad till den här WebSocket. När WebSocket har godkänts, är alla ytterligare interaktioner på den WebSocket med en lyssnare för anslutna.

### <a name="interaction-summary"></a>Interaktion sammanfattning
Resultatet av den här interaktionen modellen är att avsändaren klienten kommer utanför handskakning med en ”ren” WebSocket som är ansluten till en lyssnare och som behöver ingen ytterligare preambles eller förberedelser. Den här modellen ger praktiskt taget alla befintliga WebSocket-klientimplementeringen enkelt kan dra nytta av tjänsten Hybridanslutningar genom att ange en korrekt byggda URL till sina lager för WebSocket-klienten.

Rendezvous-anslutningen WebSocket som lyssnaren hämtar via acceptera interaktionen är också rensa och kan överlämnas till alla befintliga WebSocket-serverimplementering med vissa minimal extra abstraktion som skiljer mellan ”acceptera” åtgärder på sina framework lokala nätverket lyssnare och Hybridanslutningar remote ”acceptera” åtgärder.

## <a name="protocol-reference"></a>Referens för protokollet

Det här avsnittet innehåller information om protokoll interaktioner som beskrivs ovan.

Alla WebSocket-anslutningar görs på port 443 som en uppgradering från HTTPS 1.1, som representeras ofta av vissa WebSocket framework eller API. Beskrivningen här sparas implementering neutral, utan tyder på ett specifikt ramverk.

### <a name="listener-protocol"></a>Lyssnarprotokollet
Lyssnarprotokollet består av två anslutning gester och tre meddelandeåtgärder.

#### <a name="listener-control-channel-connection"></a>Lyssnare kontrollanslutningen kanal
Kontrollkanalen har öppnats med att skapa en WebSocket-anslutning till:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...
```

Den `namespace-address` är fullständigt kvalificerade domännamnet för Azure Relay-namnområde som är värd för Hybrid-anslutningen, vanligtvis i formatet `{myname}.servicebus.windows.net`.

Frågealternativen sträng parameter är som följer.

| Parameter | Krävs | Beskrivning |
| --- | --- | --- |
| `sb-hc-action` |Ja |För rollen lyssnare parametern måste vara **sb-hc-action = lyssna** |
| `{path}` |Ja |URL-kodade namnområdessökvägen till den förkonfigurerade Hybridanslutning att registrera den här lyssnaren på. Det här uttrycket läggs till den fasta `$hc/` sökvägsdelen. |
| `sb-hc-token` |Ja\* |Lyssnaren måste ange en giltig, URL-kodade Service Bus delad åtkomst-Token för namnområdet eller Hybridanslutning som ger den **lyssna** rätt. |
| `sb-hc-id` |Nej |Den här klienten har angett valfritt ID aktiverar diagnostikspårning för slutpunkt till slutpunkt. |

Om WebSocket-anslutningen misslyckas på grund av Hybridanslutning sökvägen inte är registrerad, eller en ogiltig eller saknas token eller några andra fel, tillhandahålls fel feedback med vanlig HTTP 1.1 status feedback modellen. Statusbeskrivningen innehåller ett fel för spårnings-id som kan överföras till Azure supporttekniker:

| Kod | Fel | Beskrivning |
| --- | --- | --- |
| 404 |Det gick inte att hitta |Hybridanslutningen sökväg är ogiltig eller den grundläggande Webbadressen är felaktig. |
| 401 |Behörighet saknas |Säkerhetstoken är saknas eller felaktig eller ogiltig. |
| 403 |Tillåts inte |Säkerhetstoken är inte giltig för den här sökvägen för den här åtgärden. |
| 500 |Internt fel |Något gick fel i tjänsten. |

Om WebSocket-anslutningen avsiktligt stängs av tjänsten efter att det ursprungligen har ställts in orsaken till detta så överförs med hjälp av lämplig WebSocket-protokollet felkoden tillsammans med ett beskrivande felmeddelande som innehåller också en spårnings-ID: t. Tjänsten stängs inte kontrollkanalen utan ett feltillstånd. En ren avstängning är klient kontrolleras.

| WS-Status | Beskrivning |
| --- | --- |
| 1001 |Hybridanslutningen sökvägen har tagits bort eller inaktiverats. |
| 1008 |Säkerhets-token har upphört att gälla, därför auktoriseringsprincipen överskrids. |
| 1011 |Något gick fel i tjänsten. |

### <a name="accept-handshake"></a>Acceptera handskakning
”Accepterar”-meddelande skickas av tjänsten till lyssnaren över tidigare upprättad kontrollkanalen som ett JSON-meddelande i en WebSocket ram. Det finns inga svar på meddelandet.

Meddelandet innehåller en JSON-objekt med namnet ”accepterar”, som definierar följande egenskaper just nu:

* **adress** – URL-sträng som ska användas för att fastställa WebSocket till tjänsten för att godkänna en inkommande anslutning.
* **ID** – den unika identifieraren för den här anslutningen. Om ID som har angetts av klienten avsändaren är avsändaren angivna-värdet, annars är det ett värde som genereras av systemet.
* **connectHeaders** – alla HTTP-huvuden som har angetts till slutpunkten för vidarebefordran av avsändaren, vilket även innefattar sek-WebSocket-protokollet och WebSocket-s-tillägg-rubriker.

#### <a name="accept-message"></a>Acceptera meddelandet

```json
{                                                           
    "accept" : {
        "address" : "wss://168.61.148.205:443/$hc/{path}?..."    
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

#### <a name="accepting-the-socket"></a>Acceptera socketen
Om du vill acceptera, upprättar lyssnaren en WebSocket-anslutning till den angivna adressen.

Om meddelandet ”accepterar” har en `Sec-WebSocket-Protocol` rubrik som är det förväntas att lyssnaren accepterar bara WebSocket om den har stöd för protokollet. Dessutom anger huvudet som WebSocket har upprättats.

Samma gäller den `Sec-WebSocket-Extensions` rubrik. Om ramen har stöd för ett tillägg, ska den in rubriken på serversidan svaret obligatoriska `Sec-WebSocket-Extensions` handskakning för tillägget.

URL: en måste användas som-är för att fastställa acceptera socketen men innehåller följande parametrar:

| Parameter | Krävs | Beskrivning |
| --- | --- | --- |
| `sb-hc-action` |Ja |Parametern måste vara för att acceptera en socket`sb-hc-action=accept` |
| `{path}` |Ja |(se följande punkt) |
| `sb-hc-id` |Nej |Se föregående beskrivning av **id**. |

`{path}`är URL-kodade namnområdessökvägen till den förkonfigurerade Hybridanslutning som du vill registrera den här lyssnaren. Det här uttrycket läggs till den fasta `$hc/` sökvägsdelen. 

Den `path` uttryck kan utökas med ett suffix och ett stränguttryck för frågan som följer det registrerade namnet efter ett separertratten snedstreck. Detta gör att avsändaren klienten att skicka dispatch argument till lyssnaren accepterar när det inte är möjligt att inkludera HTTP-huvuden. Förväntningen är att lyssnare framework analyserar fast sökvägsdelen och det registrerade namnet från sökvägen och gör resten, möjligen utan någon fråga strängargument föregås av `sb-`, tillgänglig för program för att bestämma om du vill acceptera anslutningen.

Mer information finns i avsnittet ”avsändaren protokoll”.

Om det uppstår ett fel kan i tjänsten svara på följande sätt:

| Kod | Fel | Beskrivning |
| --- | --- | --- |
| 403 |Tillåts inte |URL: en är inte giltig. |
| 500 |Internt fel |Det uppstod ett fel i tjänsten |

När anslutningen har upprättats stängs servern av WebSocket när avsändaren WebSocket stänger, eller med följande status:

| WS-Status | Beskrivning |
| --- | --- |
| 1001 |Avsändaren klienten avslutar anslutningen. |
| 1001 |Hybridanslutningen sökvägen har tagits bort eller inaktiverats. |
| 1008 |Säkerhets-token har upphört att gälla, därför auktoriseringsprincipen överskrids. |
| 1011 |Något gick fel i tjänsten. |

#### <a name="rejecting-the-socket"></a>Avvisa socket
Avvisa socket efter att ha kontrollerat meddelandet ”accepterar” kräver en liknande handskakning så att statuskoden och statusbeskrivningen orsaken till att kommunikationen kan flöda till avsändaren.

Protokollet designen har valts här är att använda en WebSocket-handskakning (som är utformade för att sluta med en definierad feltillstånd) så att lyssnare klientimplementeringar kan fortsätta att förlita sig på en WebSocket-klienten och behöver inte använda en extra, utan http-klienten.

Om du vill avvisa socket klienten tar URI-adress från meddelandet ”accepterar” och lägger till två frågeparametrar sträng, enligt följande:

| Param | Krävs | Beskrivning |
| --- | --- | --- |
| statusCode |Ja |Numeriska HTTP-statuskod. |
| StatusDescription |Ja |Mänsklig läsbar orsak till att. |

Resulterande URI: N används sedan för att upprätta en WebSocket-anslutning.

När den har slutförts korrekt, misslyckas denna handskakning avsiktligt med en HTTP-felkod 410, eftersom ingen WebSocket har upprättats. Om något går fel beskrivs följande koder felet:

| Kod | Fel | Beskrivning |
| --- | --- | --- |
| 403 |Tillåts inte |URL: en är inte giltig. |
| 500 |Internt fel |Något gick fel i tjänsten. |

### <a name="listener-token-renewal"></a>Lyssnare token förnyelse
När lyssnare-token upphör snart att gälla, ersätter den den genom att skicka ett SMS ram till tjänsten via etablerade kontrollkanalen. Meddelandet innehåller en JSON-objekt som kallas `renewToken`, som definierar följande egenskap just nu:

* **token** – en giltig, URL-kodade Service Bus delade åtkomsttoken för namnområdet eller Hybridanslutning som ger den **lyssna** rätt.

#### <a name="renewtoken-message"></a>renewToken meddelande

```json
{                                                                                                                                                                        
    "renewToken" : {                                                                                                                                                      
        "token" : "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"  
    }                                                                                                                                                                     
}
```

Om token verifieringen misslyckas åtkomst nekas och Molntjänsten stängs kontrollkanal WebSocket med ett fel. Annars finns inget svar.

| WS-Status | Beskrivning |
| --- | --- |
| 1008 |Säkerhets-token har upphört att gälla, därför auktoriseringsprincipen överskrids. |

## <a name="sender-protocol"></a>Avsändaren protokoll
Avsändaren protokollet är identiska effektivt sätt som en lyssnare har upprättats.
Målet är maximal insyn för WebSocket slutpunkt till slutpunkt. Den adress som ska ansluta till är desamma som för lyssnare, men ”åtgärden” skiljer sig och token måste ha ett annat tillstånd:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sbc-hc-token=...
```

Den *namnområde adress* är fullständigt kvalificerade domännamnet för Azure Relay-namnområde som är värd för Hybrid-anslutningen, vanligtvis i formatet `{myname}.servicebus.windows.net`.

Begäran kan innehålla godtycklig extra HTTP-rubriker, inklusive programdefinierade de. Alla angivna huvuden flöda till lyssnaren och finns på den `connectHeader` objekt av den **acceptera** kontroll.

Frågealternativen sträng parametern är följande:

| Param | Krävs? | Beskrivning |
| --- | --- | --- |
| `sb-hc-action` |Ja |Parametern måste vara för rollen avsändaren `action=connect`. |
| `{path}` |Ja |(se följande punkt) |
| `sb-hc-token` |Ja\* |Lyssnaren måste ange en giltig, URL-kodade Service Bus delad åtkomst-Token för namnområdet eller Hybridanslutning som ger den **skicka** rätt. |
| `sb-hc-id` |Nej |Ett valfritt ID som möjliggör slutpunkt till slutpunkt diagnostikspårning och görs tillgängligt för lyssnaren under acceptera handskakning. |

Den `{path}` URL-kodade namnområde sökvägen till den förkonfigurerade Hybridanslutning som du vill registrera den här lyssnaren. Den `path` uttryck kan utökas med ett suffix och stränguttryck frågan för att kommunicera ytterligare. Om Hybrid-anslutningen är registrerad under sökvägen `hyco`, `path` uttrycket kan vara `hyco/suffix?param=value&...` följt av frågan string-parametrar som anges här. En fullständig uttryck kan sedan vara följande:

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sbc-hc-token=...
```

Den `path` uttryck som skickas med lyssnaren i adressen URI i meddelandet ”accepterar” kontroll.

Om WebSocket-anslutningen misslyckas på grund av Hybrid anslutningssökvägen inte registreras, en ogiltig eller saknas token eller några andra fel, tillhandahålls fel feedback med vanlig HTTP 1.1 status feedback modellen. Statusbeskrivningen innehåller ett fel spårnings-ID som kan överföras till Azure-supportpersonal:

| Kod | Fel | Beskrivning |
| --- | --- | --- |
| 404 |Det gick inte att hitta |Hybridanslutningen sökväg är ogiltig eller den grundläggande Webbadressen är felaktig. |
| 401 |Behörighet saknas |Säkerhetstoken är saknas eller felaktig eller ogiltig. |
| 403 |Tillåts inte |Säkerhetstoken är inte giltigt för den här sökvägen och för den här åtgärden. |
| 500 |Internt fel |Något gick fel i tjänsten. |

Om WebSocket-anslutningen avsiktligt stängs av tjänsten när den ursprungligen har ställts in orsaken till detta så överförs med hjälp av lämplig WebSocket-protokollet felkoden tillsammans med ett beskrivande felmeddelande som innehåller också en spårnings-ID: t.

| WS-Status | Beskrivning |
| --- | --- |
| 1000 |Lyssnaren Stäng socket. |
| 1001 |Hybridanslutningen sökvägen har tagits bort eller inaktiverats. |
| 1008 |Säkerhets-token har upphört att gälla, därför auktoriseringsprincipen överskrids. |
| 1011 |Något gick fel i tjänsten. |

## <a name="next-steps"></a>Nästa steg
* [Vanliga frågor och svar om Relay](relay-faq.md)
* [Skapa ett namnområde](relay-create-namespace-portal.md)
* [Kom igång med .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Kom igång med Node](relay-hybrid-connections-node-get-started.md)

