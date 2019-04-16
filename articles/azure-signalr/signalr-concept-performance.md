---
title: Prestandaguide för Azure SignalR Service
description: En översikt över Azure SignalR Service prestanda.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: zhshang
ms.openlocfilehash: f7cc05c8c2a299d809c4386d119fef58fa2548d5
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2019
ms.locfileid: "59579248"
---
# <a name="performance-guide-for-azure-signalr-service"></a>Prestandaguide för Azure SignalR Service

En av de främsta fördelarna med att använda Azure SignalR Service är enkelt att skala SignalR-program. I ett scenario med storskaliga är prestanda en viktig faktor. 

I den här guiden kommer vi att introducera de faktorer som påverkar SignalR programprestanda. Vi kommer att beskriva vanliga prestanda i olika användningsfall. I slutändan kommer vi att introducera miljön och verktyg som du kan använda för att skapa en systemprestanda-rapport.

## <a name="term-definitions"></a>Termdefinitioner

*Inkommande*: Det inkommande meddelandet till Azure SignalR Service.

*Utgående*: Det utgående meddelandet från Azure SignalR Service.

*Bandwidth*: Den totala storleken på alla meddelanden i 1 sekund.

*Standardläget*: Fungerande standardläget när en instans för Azure SignalR Service skapades. Azure SignalR Service förväntar sig app-servern för att upprätta en anslutning med den innan den accepterar klientanslutningar.

*Serverlös läge*: Ett läge där Azure SignalR Service accepterar endast-klientanslutningar. Ingen serveranslutning tillåts.

## <a name="overview"></a>Översikt

Azure SignalR Service definierar sju Standard-nivåerna för olika prestanda kapaciteter. Den här guiden får du svar på följande frågor:

-   Vad är den typiska Azure SignalR Service-prestandan för varje nivå?

-   Uppfyller kraven för meddelandedataflöde (exempelvis skickar 100 000 meddelanden per sekund) i Azure SignalR Service?

-   Min specifika scenario, vilken nivå passar mig? Eller hur kan jag välja rätt nivå?

-   Vilken typ av app-servern (VM-storlek) passar mig? Hur många av dem bör jag distribuerar?

Den här guiden innehåller först en övergripande förklaring av de faktorer som påverkar prestanda för att besvara dessa frågor. Den visar sedan maximalt antal inkommande och utgående meddelanden för varje nivå för typiska användningsexempel: **echo**, **sändning**, **skicka till gruppen**, och **skicka till anslutningen** (peer-to-peer chatta).

Den här guiden täcker inte alla scenarier (och olika användningsfall, meddelande storlekar, meddelande skickas mönster och så vidare). Men ger vissa metoder som hjälper dig att:

- Utvärdera din ungefärliga krav för inkommande eller utgående meddelanden.
- Hitta rätt nivåer genom att markera tabellen prestanda.

## <a name="performance-insight"></a>Prestandainsikt

Det här avsnittet beskrivs metoderna för utvärdering av prestanda och visar en lista över alla faktorer som påverkar prestanda. I slutändan ger det metoder för att hjälpa dig att utvärdera prestandakrav.

### <a name="methodology"></a>Metod

*Dataflöde* och *svarstid* finns två vanliga aspekter för kontroll av prestanda. För Azure SignalR Service har varje SKU-nivån en egen dataflöde som begränsning av principen. Principen definierar *högsta tillåtna dataflöde (inkommande och utgående bandbredd)* enligt högsta uppnått dataflöde när 99 procent av meddelanden har svarstid som är mindre än 1 sekund.

Svarstiden är tidsintervallet från meddelandet skickades till tar emot svarsmeddelandet från Azure SignalR Service-anslutningen. Låt oss ta **echo** som exempel. Varje klientanslutning lägger till en tidsstämpel i meddelandet. Programserverns hub skickar det ursprungliga meddelandet tillbaka till klienten. Fördröjningen spridning är så enkelt beräknas genom att varje klientanslutning. Tidsstämpeln kopplas för varje meddelande i **sändning**, **skicka till gruppen**, och **skicka till anslutningen**.

För att simulera tusentals samtidiga klientanslutningar, skapas flera virtuella datorer i ett virtuellt privat nätverk i Azure. Alla dessa virtuella datorer ansluta till samma Azure SignalR Service-instans.

I standardläget för Azure SignalR Service AppServer virtuella datorer distribueras i samma virtuella privata nätverk som virtuella datorer-klient. Alla klient virtuella datorer och virtuella app-servrar distribueras i samma virtuella nätverk i samma region för att undvika fördröjning mellan regioner.

### <a name="performance-factors"></a>Prestandafaktorer

Teoretiskt sett kan begränsas Azure SignalR Service kapacitet av beräkningsresurser som: Processor, minne och nätverk. Fler anslutningar till Azure SignalR Service kan till exempel tjänsten utnyttja mer minne. För större meddelandetrafik (till exempel alla meddelanden som är större än 2 048 byte), Azure SignalR Service behöver använda mer CPU-kraft för att bearbeta trafik. Under tiden kan tillämpar Azure nätverkets bandbredd också en gräns för maximal trafik.

Transporttypen är en annan faktor som påverkar prestanda. De tre typerna är [WebSocket](https://en.wikipedia.org/wiki/WebSocket), [Server skickas händelsen](https://en.wikipedia.org/wiki/Server-sent_events), och [-Longpolling](https://en.wikipedia.org/wiki/Push_technology). 

WebSocket är en dubbelriktad och full duplex kommunikationsprotokoll genom en enda TCP-anslutning. Server skickas händelsen är en enkelriktad protokoll att skicka meddelanden från servern till klienten. -Longpolling kräver att klienter för regelbundna avsökningar information från servern via en HTTP-begäran. WebSocket har bästa möjliga prestanda för samma API på samma villkor, Server skickas händelsen är långsammare och -Longpolling är långsammast. Azure SignalR Service rekommenderar WebSocket som standard.

Meddelandet routning kostnaden begränsar också prestanda. Azure SignalR Service spelar en roll som en router för meddelandet som dirigerar meddelande från en uppsättning klienter eller servrar till andra klienter eller servrar. Ett annat scenario eller API: et kräver en annan princip för routning. 

För **echo**klienten skickar ett meddelande till sig själv och routning målet är också själva. Det här mönstret har lägst routning kostnaden. Men för **sändning**, **skicka till gruppen**, och **skicka till anslutningen**, Azure SignalR Service behöver söka efter mål-anslutningar via interna distribuerad data strukturen. Den här extra bearbetningen använder mer CPU, minne och bandbredd i nätverket. Prestanda är därför långsammare.

I standardläget, kan app-servern också bli en flaskhals för vissa scenarier. Azure SignalR SDK måste anropa hubben, medan den upprätthåller en live-anslutning med varje klient via heartbeat-signaler.

Klienten skickar ett meddelande per HTTP-post, vilket inte är lika effektiva som WebSocket i serverlös läge.

En annan faktor är protokoll: JSON och [MessagePack](https://msgpack.org/index.html). MessagePack är mindre än storleken och levereras snabbare än JSON. MessagePack kan inte förbättra prestanda, men. Prestanda för Azure SignalR Service är inte känslig för protokoll eftersom det inte avkoda meddelandet nyttolasten under vidarebefordran av meddelanden från klienter till servrar eller vice versa.

Sammanfattningsvis påverkar följande faktorer kapaciteten för inkommande och utgående:

-   SKU-nivån (processor/minne)

-   Antal anslutningar

-   Meddelandestorlek

-   Skicka Meddelandefrekvens

-   Transport typ (WebSocket, Server skickas händelsen eller -Longpolling)

-   Användningsfall (Routning kostnad)

-   App-server och tjänsten anslutningar (i server-läge)


### <a name="finding-a-proper-sku"></a>Hitta en korrekt SKU

Hur kan du utvärdera inkommande/utgående kapacitet eller hitta vilken nivå är lämplig för ett specifikt användningsfall?

Anta att appservern är tillräckligt kraftfull och är inte flaskhals för prestanda. Kontrollera den maximala inkommande och utgående bandbredden för varje nivå.

#### <a name="quick-evaluation"></a>Snabb utvärdering

Vi kan förenkla utvärderingen först genom att vissa standardinställningar: 

- Transporttypen är WebSocket.
- Meddelandestorleken är 2 048 byte.
- Ett meddelande skickas varje sekund.
- Azure SignalR Service är i standardläget.

På varje nivå har sina egna Maximal bandbredd för inkommande och utgående bandbredd. En smidig användarupplevelse är inte säkert när inkommande eller utgående anslutning överskrider gränsen.

**Echo** ger maximal inkommande bandbredd eftersom den har den lägsta routning kostnaden. **Skicka** definierar den maximala utgående message bandbredden.

Gör *inte* överskrider de markerade värdena i följande två tabeller.

|       echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar                       | 1,000 | 2,000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| **Inkommande bandbredd** | **2 Mbit/s**    | **4 Mbit/s**    | **10 Mbit/s**   | **20 Mbit/s**    | **40 Mbit/s**    | **100 Mbit/s**   | **200 Mbit/s**    |
| Utgående bandbredd | 2 Mbit/s   | 4 Mbit/s   | 10 Mbit/s  | 20 Mbit/s   | 40 Mbit/s   | 100 Mbit/s  | 200 Mbit/s   |


|     Sändning             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Anslutningar               | 1,000 | 2,000 | 5 000  | 10 000 | 20,000 | 50,000  | 100 000 |
| Inkommande bandbredd  | 4 kbit/s   | 4 kbit/s   | 4 kbit/s    | 4 kbit/s    | 4 kbit/s    | 4 kbit/s     | 4 kbit/s    |
| **Utgående bandbredd** | **4 Mbit/s**    | **8 Mbit/s**    | **20 Mbit/s**    | **40 Mbit/s**    | **80 Mbit/s**    | **200 Mbit/s**    | **400 Mbit/s**   |

*Inkommande bandbredd* och *utgående bandbredd* är den totala meddelandestorleken per sekund.  Här följer formler för dem:
```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

- *inboundConnections*: Antalet anslutningar som meddelandet skickades.

- *outboundConnections*: Antalet anslutningar som tar emot meddelandet.

- *messageSize*: Storleken på ett enda meddelande (genomsnittligt värde). Ett litet meddelande som är mindre än 1 024 byte har en prestandapåverkan som liknar ett 1 024 byte-meddelande.

- *sendInterval*: Tid för att skicka ett meddelande. Det är vanligtvis 1 sekund per meddelande, vilket innebär att skicka ett meddelande varje sekund. Ett mindre intervall innebär mer meddelande skickades under en viss tidsperiod. Till exempel innebär 0,5 sekunder per meddelande och skicka två meddelanden per sekund.

- *Anslutningar*: Allokerade högsta tröskelvärdet för Azure SignalR Service för varje nivå. Om ytterligare ökas antalet anslutning, kommer den drabbas av anslutningsbegränsning av.

#### <a name="evaluation-for-complex-use-cases"></a>Utvärdering för komplexa användningsfall

##### <a name="bigger-message-size-or-different-sending-rate"></a>Större storlek eller annan takten för sändning

Den verkliga är mer komplicerad. Den kan skicka ett meddelande som är större än 2 048 byte eller skicka Meddelandefrekvens är inte ett meddelande per sekund. Låt oss ta Unit100's sändning som exempel för att ta reda på hur att utvärdera dess prestanda.

I följande tabell visas en verkliga exempel med **sändning**. Men meddelandestorlek, antal anslutningar och skicka rate meddelanden skiljer sig från vad vi antas i föregående avsnitt. Frågan är hur vi kan härleda dessa objekt (meddelandestorlek, antal anslutningar eller skicka Meddelandefrekvens) om vi vet att endast två av dem.

| Sändning  | Meddelandestorlek | Inkommande meddelanden per sekund | Anslutningar | Skicka intervall |
|---|---------------------|--------------------------|-------------|-------------------------|
| 1 | 20 KB                | 1                        | 100 000     | 5 SEK.                      |
| 2 | 256 kB               | 1                        | 8,000       | 5 SEK.                      |

Formeln är enkelt att härleda baserat på den föregående formeln:

```
outboundConnections = outboundBandwidth * sendInterval / messageSize
```

För Unit100 är den maximala utgående bandbredden 400 MB i föregående tabell. För en 20 KB meddelandestorlek maximalt antal utgående anslutningar bör vara 400 MB \* 5 / 20 KB = 100 000, som matchar det verkliga värdet.

##### <a name="mixed-use-cases"></a>Blandade användningsfall

Det verkliga användningsfallet vanligtvis blandas fyra grundläggande användningsfall tillsammans: **echo**, **sändning**, **skicka till gruppen**, och **skicka till anslutningen**. Den metod som används för att utvärdera kapaciteten är att:

1. Dela upp blandat användningsfallen i fyra grundläggande användningsfall.
1. Beräkna bandbredden som maximal inkommande och utgående meddelande med hjälp av de föregående formlerna separat.
1. Summera Bandbreddsberäkningar för att få maximal total inkommande/utgående bandbredd. 

Hämta sedan rätt nivå från tabellerna maximal inkommande/utgående bandbredd.

> [!NOTE]
> För att skicka ett meddelande till hundratals eller tusentals små grupper, eller för tusentals klienter skickar ett meddelande till varandra, blir routning kostnaden dominerande. Ta den här effekten på konto.

För användningsfall för att skicka ett meddelande till klienter, se till att appservern *inte* flaskhalsen. ”Fallstudie” nedan ger riktlinjer om hur många app-servrar du behöver och hur många anslutningar bör du konfigurera.

## <a name="case-study"></a>Fallstudie

I följande avsnitt går igenom fyra vanliga användningsområden för WebSocket transport: **echo**, **sändning**, **skicka till gruppen**, och **skicka till anslutningen**. För varje scenario visas i avsnitt den aktuella kapaciteten för inkommande och utgående för Azure SignalR Service. Den förklarar också de viktigaste faktorerna som påverkar prestanda.

App-servern skapar fem serveranslutningar i standardläget, med Azure SignalR Service. App-servern använder SDK för Azure SignalR som standard. I följande testresultaten för prestanda ökas serveranslutningar till 15 (eller mer för sändning och skicka ett meddelande till en stor grupp).

Olika användningsfall har olika krav för app-servrar. **Skicka** måste litet antal app-servrar. **Echo** eller **skicka till anslutningen** måste många app-servrar.

I alla användningsfall, meddelande standardstorleken är 2 048 byte och skicka meddelandeintervall är 1 sekund.

### <a name="default-mode"></a>Standardläge

Klienter, web app-servrar och Azure SignalR Service är inblandade i standardläge. Varje klient står för en enda anslutning.

#### <a name="echo"></a>echo

Först ansluter en webbapp till Azure SignalR Service. Dessutom många klienter ansluter till webbappen, som omdirigerar du klienterna till Azure SignalR Service med åtkomsttoken och slutpunkt. Klienterna upprätta sedan WebSocket-anslutningar med Azure SignalR Service.

När alla klienter upprätta anslutningar, börjar de skicka ett meddelande som innehåller en tidsstämpel till specifika hubben varje sekund. Hubben ekar svaret till dess ursprungliga klienten. Varje klient beräknar svarstiden när den får meddelandet echo tillbaka.

I följande diagram är 5 till 8 (röd markerade trafik) i en loop. Loopen körs för en standardvaraktighet (5 minuter) och hämtar statistik på alla meddelande svarstid.

![Trafik för echo-användningsfall](./media/signalr-concept-performance/echo.png)

Beteendet för **echo** anger att den inkommande maximal bandbredden är lika med den maximala utgående bandbredden. Mer information finns i följande tabell.

|       echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar                       | 1,000 | 2,000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Inkommande/utgående meddelanden per sekund | 1,000 | 2,000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Inkommande/utgående bandbredd | 2 Mbit/s   | 4 Mbit/s   | 10 Mbit/s  | 20 Mbit/s   | 40 Mbit/s   | 100 Mbit/s  | 200 Mbit/s   |

I det här användningsfallet anropar varje klient hubben som definierats i app-servern. Hubben anropar bara den metod som definierats i ursprungliga klientsidan. Den här hubben är den mest lightweight hubben för **echo**.

```
        public void Echo(IDictionary<string, object> data)
        {
            Clients.Client(Context.ConnectionId).SendAsync("RecordLatency", data);
        }
```

Även för den här enkla hubben trafik trycket på appservern är framträdande som den **echo** inkommande meddelande belastningen ökar. Detta trafik tryck kräver många app-servrar för stora SKU-nivåer. I följande tabell visas antalet app server för varje nivå.


|    echo          | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar      | 1,000 | 2,000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Antal för App-servrar | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Klienten anslutning number, meddelandestorleken, skicka hastighet, SKU-nivån och processor/minne för app-serverns meddelanden påverkar prestandan **echo**.

#### <a name="broadcast"></a>Sändning

För **sändning**när webbappen tar emot meddelandet skickas till alla klienter. Fler klienter det är att sända mer meddelandetrafik som finns på alla klienter. Se i följande diagram.

![Trafik för broadcast användningsfall](./media/signalr-concept-performance/broadcast.png)

Sänder ett litet antal klienter. Bandbredden som inkommande message är liten, men den utgående bandbredden är stor. Bandbredden som utgående message ökar som klientanslutningen eller broadcast-frekvens ökar.

I följande tabell sammanfattas maximala klientanslutningar, antal inkommande/utgående meddelanden och bandbredd.

|     Sändning             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Anslutningar               | 1,000 | 2,000 | 5 000  | 10 000 | 20,000 | 50,000  | 100 000 |
| Inkommande meddelanden per sekund  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Utgående meddelanden per sekund | 2,000 | 4,000 | 10 000 | 20,000 | 40,000 | 100 000 | 200 000 |
| Inkommande bandbredd  | 4 kbit/s   | 4 kbit/s   | 4 kbit/s    | 4 kbit/s    | 4 kbit/s    | 4 kbit/s     | 4 kbit/s     |
| Utgående bandbredd | 4 Mbit/s   | 8 Mbit/s   | 20 Mbit/s   | 40 Mbit/s   | 80 Mbit/s   | 200 Mbit/s   | 400 Mbit/s   |

Broadcasting klienter som skickar meddelanden finns mer än fyra. De behöver färre appservrar jämfört med **echo** eftersom det inkommande meddelandet är små. Två appservrar är tillräckligt för både SLA- och prestandaöverväganden. Men du bör öka serveranslutningar standard för att undvika obalans, särskilt för Unit50 och Unit100.

|   Sändning      | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar      | 1,000 | 2,000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Antal för App-servrar | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Öka standard serveranslutningar från 5 till 40 på varje appservern för att undvika möjlig Obalanserat anslutningar till Azure SignalR Service.
>
> Klienten anslutning antal, storlek, skicka Meddelandefrekvens och SKU-nivån påverkar prestandan för **sändning**.

#### <a name="send-to-group"></a>Skicka till grupp

Den **skicka till gruppen** användningsfall har ett liknande mönster för trafik till **sändning**. Skillnaden är att när klienter etablerar WebSocket-anslutningar med Azure SignalR Service, måste de ansluta grupper innan de kan skicka ett meddelande till en specifik grupp. Följande diagram illustrerar trafikflödet.

![Trafik för det skicka till grupp](./media/signalr-concept-performance/sendtogroup.png)

Gruppmedlem och antalet är två faktorer som påverkar prestanda. För att förenkla analysen kan definierar vi två typer av grupper:

- **Liten grupp**: Varje grupp har 10 anslutningar. Gruppnumret är lika med (max anslutningsräknare) / 10. Till exempel för Unit1, om det finns 1 000 anslutning antal, sedan har vi 1000 / 10 = 100 grupper.

- **Stor grupp**: Gruppnumret är alltid 10. Medlemsantal grupp är lika med (max anslutningsräknare) / 10. Till exempel för Unit1, om det finns 1 000 anslutning antal, sedan varje grupp har 1000 / 10 = 100 medlemmarna.

**Skicka till grupp** ger en routning kostnad för Azure SignalR Service eftersom den har att hitta mål-anslutningar via en distribuerad datastruktur. När du skickar anslutningarna ökar, ökar kostnaden.

##### <a name="small-group"></a>Liten grupp

Routning kostnaden är viktiga för att skicka meddelandet till många små grupper. För närvarande når Azure SignalR Service-implementeringen routning kostnadsgränsen på Unit50. Att lägga till mer Processorkraft och minne inte hjälper, så att det går inte att förbättra Unit100 mer avsiktligt. Om du behöver mer ingående bandbredd kan du kontakta Kundsupport.

|   Skicka till liten grupp     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50 | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| Anslutningar               | 1,000 | 2,000 | 5 000  | 10 000 | 20,000 | 50,000 | 100 000
| Antal medlemmar för gruppen        | 10    | 10    | 10     | 10     | 10     | 10     | 10 
| Antalet               | 100   | 200   | 500    | 1,000  | 2,000  | 5 000  | 10 000 
| Inkommande meddelanden per sekund  | 200   | 400   | 1,000  | 2,500  | 4,000  | 7,000  | 7,000   |
| Inkommande bandbredd  | 400 kbit/s  | 800 kbit/s  | 2 Mbit/s     | 5 Mbit/s     | 8 Mbit/s     | 14 Mbit/s    | 14 Mbit/s     |
| Utgående meddelanden per sekund | 2,000 | 4,000 | 10 000 | 25,000 | 40,000 | 70,000 | 70,000  |
| Utgående bandbredd | 4 Mbit/s    | 8 Mbit/s    | 20 Mbit/s    | 50 Mbit/s     | 80 Mbit/s    | 140 Mbit/s   | 140 Mbit/s    |

Många klientanslutningar anropar hubben, så att servern appnumret är viktigt för prestanda. I följande tabell visas server-antal föreslagna appar.

|  Skicka till liten grupp   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar      | 1,000 | 2,000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Antal för App-servrar | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Klienten anslutning number, meddelandestorleken, skicka hastighet, routning kostnader, SKU-nivån och processor/minne för app-serverns meddelanden påverkar prestandan **skicka till liten grupp**.

##### <a name="big-group"></a>Stor grupp

För **skicka till stor grupp**, utgående bandbredd blir flaskhalsen innan träffa routning kostnad gränsen. I följande tabell visas den maximala utgående bandbredden som är nästan desamma som för **sändning**.

|    Skicka till stor grupp      | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Anslutningar               | 1,000 | 2,000 | 5 000  | 10 000 | 20,000 | 50,000  | 100 000
| Antal medlemmar för gruppen        | 100   | 200   | 500    | 1,000  | 2,000  | 5 000   | 10 000 
| Antalet               | 10    | 10    | 10     | 10     | 10     | 10      | 10
| Inkommande meddelanden per sekund  | 20    | 20    | 20     | 20     | 20     | 20      | 20      |
| Inkommande bandbredd  | 80 kbit/s   | 40 kbit/s   | 40 kbit/s    | 20 kbit/s    | 40 kbit/s    | 40 kbit/s     | 40 kbit/s     |
| Utgående meddelanden per sekund | 2,000 | 4,000 | 10 000 | 20,000 | 40,000 | 100 000 | 200 000 |
| Utgående bandbredd | 8 Mbit/s    | 8 Mbit/s    | 20 Mbit/s    | 40 Mbit/s    | 80 Mbit/s    | 200 Mbit/s    | 400 Mbit/s    |

Skicka anslutningsräknare är fler än 40. Belastningen på app-servern är liten, så det föreslagna antalet webbprogram är liten.

|  Skicka till stor grupp  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar      | 1,000 | 2,000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Antal för App-servrar | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Öka standard serveranslutningar från 5 till 40 på varje appservern för att undvika möjlig Obalanserat anslutningar till Azure SignalR Service.
> 
> Klienten anslutning antal, meddelandestorlek, skicka Meddelandefrekvens, routning kostnad och SKU-nivån påverkar prestandan **skicka till stor grupp**.

#### <a name="send-to-connection"></a>Skicka till anslutning

I den **skicka till anslutningen** användningsfall när klienter upprätta anslutningar till Azure SignalR Service varje klient anropar en särskild hubb för att hämta sina egna anslutnings-ID Samlar in alla anslutning ID: N benchmark för prestanda, flyttar runt dem och tilldelar dem till alla klienter som skickar mål. Klienterna fortsätta skicka meddelandet till målanslutningen förrän prestandatest är klar.

![Trafik för det skickas till klient](./media/signalr-concept-performance/sendtoclient.png)

Routning kostnad för **skicka till anslutningen** liknar kostnaden för **skicka till liten grupp**.

När antalet anslutning ökar begränsar routning kostnaden övergripande prestanda. Unit50 har nått gränsen. Unit100 kan inte därmed förbättra ytterligare.

I följande tabell är en statistisk sammanfattning när du har många Rundar av att köra den **skicka till anslutningen** prestandamått.

|   Skicka till anslutning   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50          | Unit100         |
|------------------------------------|-------|-------|-------|--------|--------|-----------------|-----------------|
| Anslutningar                        | 1,000 | 2,000 | 5 000 | 10 000 | 20,000 | 50,000          | 100 000         |
| Inkommande/utgående meddelanden per sekund | 1,000 | 2,000 | 5 000 | 8,000  | 9,000  | 20,000 | 20,000 |
| Inkommande/utgående bandbredd | 2 Mbit/s    | 4 Mbit/s    | 10 Mbit/s   | 16 Mbit/s    | 18 Mbit/s    | 40 Mbit/s       | 40 Mbit/s       |

Det här användningsfallet kräver hög belastning på serversidan för appen. Se föreslagna appservern antal i tabellen nedan.

|  Skicka till anslutning  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar      | 1,000 | 2,000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Antal för App-servrar | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Klienten anslutning number, meddelandestorleken, skicka hastighet, routning kostnader, SKU-nivån och processor/minne för app-servern meddelanden påverkar prestandan **skicka till anslutningen**.

#### <a name="aspnet-signalr-echo-broadcast-and-send-to-small-group"></a>ASP.NET SignalR echo, sända, och skicka till liten grupp

Azure SignalR Service erbjuder samma prestanda kapacitet för ASP.NET SignalR. 

Prestandatest använder Azure Web Apps från [Standard planera S3](https://azure.microsoft.com/pricing/details/app-service/windows/) för ASP.NET SignalR.

Följande tabell innehåller antalet föreslagna web app för ASP.NET SignalR **echo**.

|   echo           | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar      | 1,000 | 2,000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Antal för App-servrar | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

Följande tabell innehåller antalet föreslagna web app för ASP.NET SignalR **sändning**.

|  Sändning       | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar      | 1,000 | 2,000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Antal för App-servrar | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

Följande tabell innehåller antalet föreslagna web app för ASP.NET SignalR **skicka till liten grupp**.

|  Skicka till liten grupp     | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar      | 1,000 | 2,000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Antal för App-servrar | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

### <a name="serverless-mode"></a>Serverlös läge

Klienter och Azure SignalR Service är inblandade i serverlös läge. Varje klient står för en enda anslutning. Klienten skickar meddelanden via REST API till en annan klient- eller broadcast-meddelanden till alla.

Skicka Högdensitet meddelanden via REST API är inte lika effektiva som använder WebSocket. Du måste skapa en ny HTTP-anslutning varje gång och som är någon extra kostnad i serverlös läge.

#### <a name="broadcast-through-rest-api"></a>Skicka via REST-API
Alla klienter upprätta WebSocket-anslutningar med Azure SignalR Service. Vissa klienter starta broadcasting via REST API. Skicka (inkommande) meddelanden är via HTTP Post som inte är effektiv jämfört med WebSocket.

|   Skicka via REST-API     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Anslutningar               | 1,000 | 2,000 | 5 000  | 10 000 | 20,000 | 50,000  | 100 000 |
| Inkommande meddelanden per sekund  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Utgående meddelanden per sekund | 2,000 | 4,000 | 10 000 | 20,000 | 40,000 | 100 000 | 200 000 |
| Inkommande bandbredd  | 4 kbit/s    | 4 kbit/s    | 4 kbit/s     | 4 kbit/s     | 4 kbit/s     | 4 kbit/s      | 4 kbit/s      |
| Utgående bandbredd | 4 Mbit/s    | 8 Mbit/s    | 20 Mbit/s    | 40 Mbit/s    | 80 Mbit/s    | 200 Mbit/s    | 400 Mbit/s    |

#### <a name="send-to-user-through-rest-api"></a>Skicka till användaren via REST-API
Benchmark tilldelar användarnamn till alla klienter innan de börjar ansluter till Azure SignalR Service. När klienterna upprätta WebSocket-anslutningar med Azure SignalR Service, börjar de skicka meddelanden till andra via HTTP Post.

|   Skicka till användaren via REST-API | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Anslutningar               | 1,000 | 2,000 | 5 000  | 10 000 | 20,000 | 50,000  | 100 000 |
| Inkommande meddelanden per sekund  | 300   | 600   | 900    | 1,300  | 2,000  | 10 000  | 18,000  |
| Utgående meddelanden per sekund | 300   | 600   | 900    | 1,300  | 2,000  | 10 000  | 18,000 |
| Inkommande bandbredd  | 600 kbit/s  | 1.2 Mbit/s  | 1.8 Mbit/s   | 2.6 Mbit/s   | 4 Mbit/s     | 10 Mbit/s     | 36 Mbit/s    |
| Utgående bandbredd | 600 kbit/s  | 1.2 Mbit/s  | 1.8 Mbit/s   | 2.6 Mbit/s   | 4 Mbit/s     | 10 Mbit/s     | 36 Mbit/s    |

## <a name="performance-test-environments"></a>Prestanda-och testmiljöer

För alla användningsfall som anges ovan, vi genomföras prestandatester i en Azure-miljö. Vi använde högst 50 klienten virtuella datorer och 20 AppServer virtuella datorer. Här följer vissa detaljer:

- Klienten VM-storlek: StandardDS2V2 (2 virtuella processorer, 7G minne)

- AppServer VM-storlek: StandardF4sV2 (4 vCPU, 8G minne)

- Azure SignalR SDK serveranslutningar: 15

## <a name="performance-tools"></a>Prestandaverktyg

Du hittar prestandaverktyg för Azure SignalR Service på [GitHub](https://github.com/Azure/azure-signalr-bench/tree/master/SignalRServiceBenchmarkPlugin).

## <a name="next-steps"></a>Nästa steg

I den här artikeln får du en översikt över Azure SignalR Service prestanda i vanliga användningsfall.

Om du vill visa information om innehållet i tjänsten och skalning för att den kan du läsa följande guider:

* [Azure SignalR Service från insidan](signalr-concept-internals.md)
* [Azure SignalR Service-skalning](signalr-howto-scale-multi-instances.md)
