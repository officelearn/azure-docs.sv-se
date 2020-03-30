---
title: Prestandaguide för Azure SignalR Service
description: En översikt över prestanda och riktmärke för Azure SignalR-tjänsten. Viktiga mått att tänka på när du planerar kapaciteten.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 68cad32be177fa20794399157fca89e87c2f8f59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74157668"
---
# <a name="performance-guide-for-azure-signalr-service"></a>Prestandaguide för Azure SignalR Service

En av de viktigaste fördelarna med att använda Azure SignalR-tjänsten är hur enkelt det är att skala SignalR-program. I ett storskaligt scenario är prestanda en viktig faktor. 

I den här guiden introducerar vi de faktorer som påverkar SignalR-programmets prestanda. Vi beskriver typiska prestanda i olika användningsfallsscenarier. I slutändan introducerar vi miljön och verktygen som du kan använda för att generera en prestandarapport.

## <a name="term-definitions"></a>Termdefinitioner

*Inkommande*: Det inkommande meddelandet till Azure SignalR Service.

*Utgående*: Det utgående meddelandet från Azure SignalR Service.

*Bandbredd:* Den totala storleken på alla meddelanden på 1 sekund.

*Standardläge:* Standardarbetsläget när en Azure SignalR-tjänstinstans skapades. Azure SignalR Service förväntar sig att appservern upprättar en anslutning till den innan den accepterar eventuella klientanslutningar.

*Serverlöst läge*: Ett läge där Azure SignalR-tjänsten endast accepterar klientanslutningar. Ingen serveranslutning är tillåten.

## <a name="overview"></a>Översikt

Azure SignalR-tjänsten definierar sju standardnivåer för olika prestandakapaciteter. Den här guiden besvarar följande frågor:

-   Vilken är den typiska Azure SignalR-tjänstens prestanda för varje nivå?

-   Uppfyller Azure SignalR-tjänsten mina krav för meddelandedataflöde (till exempel att skicka 100 000 meddelanden per sekund)?

-   Vilken nivå är lämplig för mig för mitt specifika scenario? Eller hur kan jag välja rätt nivå?

-   Vilken typ av appserver (VM-storlek) är lämplig för mig? Hur många av dem ska jag distribuera?

För att besvara dessa frågor ger den här guiden först en förklaring på hög nivå av de faktorer som påverkar prestanda. Den illustrerar sedan maximalt inkommande och utgående meddelanden för alla nivåer för typiska användningsfall: **eko**, **broadcast,** **skicka till grupp**och skicka till **anslutning** (peer-to-peer-chattning).

Den här guiden kan inte omfatta alla scenarier (och olika användningsfall, meddelandestorlekar, meddelandesändningsmönster och så vidare). Men det ger några metoder för att hjälpa dig:

- Utvärdera ditt ungefärliga krav för inkommande eller utgående meddelanden.
- Hitta rätt nivåer genom att kontrollera prestandatabellen.

## <a name="performance-insight"></a>Resultatinsikt

I det här avsnittet beskrivs metoderna för utvärdering av prestanda och alla faktorer som påverkar prestanda visas. I slutändan innehåller den metoder som hjälper dig att utvärdera prestandakrav.

### <a name="methodology"></a>Metodik

*Dataflöde* och *svarstid* är två typiska aspekter av prestandakontroll. För Azure SignalR-tjänst har varje SKU-nivå en egen begränsningsprincip för dataflöde. Principen definierar *det högsta tillåtna dataflödet (inkommande och utgående bandbredd)* som maximalt uppnått dataflöde när 99 procent av meddelandena har svarstid som är mindre än 1 sekund.

Svarstid är tidsintervallet från anslutningen som skickar meddelandet till att ta emot svarsmeddelandet från Azure SignalR Service. Låt oss ta **eko** som ett exempel. Varje klientanslutning lägger till en tidsstämpel i meddelandet. Appserverns nav skickar det ursprungliga meddelandet tillbaka till klienten. Så spridningsfördröjningen beräknas enkelt av varje klientanslutning. Tidsstämpeln är kopplad för varje meddelande i **sändning,** **skicka till grupp**och skicka till **anslutning**.

För att simulera tusentals samtidiga klientanslutningar skapas flera virtuella datorer i ett virtuellt privat nätverk i Azure. Alla dessa virtuella datorer ansluter till samma Azure SignalR-tjänstinstans.

I standardläget för Azure SignalR Service distribueras virtuella appserver-datorer i samma virtuella privata nätverk som klient-virtuella datorer. Alla virtuella klient-datorer och virtuella appserver-datorer distribueras i samma nätverk i samma region för att undvika svarstid mellan regioner.

### <a name="performance-factors"></a>Prestandafaktorer

Teoretiskt begränsas Azure SignalR-tjänstkapaciteten av beräkningsresurser: CPU, minne och nätverk. Till exempel orsakar fler anslutningar till Azure SignalR-tjänsten att tjänsten använder mer minne. För större meddelandetrafik (till exempel är varje meddelande större än 2 048 byte) behöver Azure SignalR-tjänsten spendera mer CPU-cykler för att bearbeta trafik. Samtidigt innebär Azure-nätverksbandbredd också en gräns för maximal trafik.

Transporttypen är en annan faktor som påverkar prestanda. De tre typerna är [WebSocket,](https://en.wikipedia.org/wiki/WebSocket) [Server-Sent-Event](https://en.wikipedia.org/wiki/Server-sent_events)och [Long-Polling](https://en.wikipedia.org/wiki/Push_technology). 

WebSocket är ett dubbelriktat och fullständigt dubbelsidigt kommunikationsprotokoll över en enda TCP-anslutning. Server-Sent-Event är ett enkelriktat protokoll för att skicka meddelanden från server till klient. Long-Polling kräver att klienterna regelbundet avsöker information från servern via en HTTP-begäran. För samma API under samma förhållanden har WebSocket bästa prestanda, Server-Sent-Event är långsammare och Long-Polling är den långsammaste. Azure SignalR Service rekommenderar WebSocket som standard.

Kostnaden för meddelanderoutning begränsar också prestanda. Azure SignalR-tjänsten spelar en roll som en meddelanderouter, som dirigerar meddelandet från en uppsättning klienter eller servrar till andra klienter eller servrar. Ett annat scenario eller API kräver en annan routningsprincip. 

För **echo**skickar klienten ett meddelande till sig själv och routningsmålet är också själv. Det här mönstret har den lägsta flödeskostnaden. Men för **broadcast,** **skicka till grupp**och skicka till **anslutning**, Azure SignalR Service måste slå upp målanslutningar via den interna distribuerade datastrukturen. Den här extra bearbetningen använder mer cpu, minne och nätverksbandbredd. Som ett resultat är prestanda långsammare.

I standardläget kan appservern också bli en flaskhals för vissa scenarier. Azure SignalR SDK måste anropa navet, medan den upprätthåller en live-anslutning med varje klient via pulsslagssignaler.

I serverlöst läge skickar klienten ett meddelande via HTTP-inlägg, vilket inte är lika effektivt som WebSocket.

En annan faktor är protokoll: JSON och [MessagePack](https://msgpack.org/index.html). MessagePack är mindre i storlek och levereras snabbare än JSON. MessagePack kanske inte förbättrar prestanda. Prestanda för Azure SignalR-tjänsten är inte känslig för protokoll eftersom den inte avkodar meddelandenyttolasten under vidarebefordran av meddelanden från klienter till servrar eller vice versa.

Sammanfattningsvis påverkar följande faktorer den inkommande och utgående kapaciteten:

-   SKU-nivå (CPU/minne)

-   Antal anslutningar

-   Meddelandestorlek

-   Sändningshastighet för meddelanden

-   Transporttyp (WebSocket, Server-Sent-Event eller Long-Polling)

-   Användningsscenario (operationsföljdskostnad)

-   Appserver- och tjänstanslutningar (i serverläge)


### <a name="finding-a-proper-sku"></a>Hitta en riktig SKU

Hur kan du utvärdera den inkommande/utgående kapaciteten eller hitta vilken nivå som är lämplig för ett specifikt användningsfall?

Anta att appservern är tillräckligt kraftfull och inte är flaskhalsen i prestanda. Kontrollera sedan den maximala inkommande och utgående bandbredden för varje nivå.

#### <a name="quick-evaluation"></a>Snabb utvärdering

Låt oss förenkla utvärderingen först genom att anta några standardinställningar: 

- Transporttypen är WebSocket.
- Meddelandestorleken är 2 048 byte.
- Ett meddelande skickas var 1 sekund.
- Azure SignalR-tjänsten är i standardläge.

Varje nivå har sin egen maximala inkommande bandbredd och utgående bandbredd. En smidig användarupplevelse garanteras inte när den inkommande eller utgående anslutningen överskrider gränsen.

**Echo** ger maximal inkommande bandbredd eftersom den har den lägsta routningskostnaden. **Broadcast** definierar den maximala utgående meddelandebandbredden.

Överskrid *inte* de markerade värdena i följande två tabeller.

|       Echo                        | Enhet1 | Enhet2 | Enhet5 | Enhet10 | Enhet20 | Enhet50 | Enhet100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar                       | 1,000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| **Inkommande bandbredd** | **2 Mbit/s**    | **4 Mbit/s**    | **10 Mbit/s**   | **20 MBps**    | **40 MBps**    | **100 MBps**   | **200 MBps**    |
| Utgående bandbredd | 2 Mbit/s   | 4 Mbit/s   | 10 Mbit/s  | 20 MBps   | 40 MBps   | 100 MBps  | 200 MBps   |


|     Sändning             | Enhet1 | Enhet2 | Enhet5  | Enhet10 | Enhet20 | Enhet50  | Enhet100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Anslutningar               | 1,000 | 2 000 | 5 000  | 10 000 | 20 000 | 50 000  | 100 000 |
| Inkommande bandbredd  | 4 KBps   | 4 KBps   | 4 KBps    | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps    |
| **Utgående bandbredd** | **4 Mbit/s**    | **8 Mbit/s**    | **20 MBps**    | **40 MBps**    | **80 MBps**    | **200 MBps**    | **400 MBps**   |

*Inkommande bandbredd* och *utgående bandbredd* är den totala meddelandestorleken per sekund.  Här är formlerna för dem:
```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

- *inkommandeAnslutningar*: Antalet anslutningar som skickar meddelandet.

- *utgåendeAnslutningar*: Antalet anslutningar som tar emot meddelandet.

- *messageSize*: Storleken på ett enskilt meddelande (genomsnittligt värde). Ett litet meddelande som är mindre än 1 024 byte har en prestandapåverkan som liknar ett meddelande på 1 024 byte.

- *sendInterval*: Tiden för att skicka ett meddelande. Vanligtvis är det 1 sekund per meddelande, vilket innebär att skicka ett meddelande varje sekund. Ett mindre intervall innebär att skicka fler meddelanden under en tidsperiod. Till exempel innebär 0,5 sekunder per meddelande att två meddelanden skickas varje sekund.

- *Anslutningar*: Den bekräftade maximala tröskelvärdet för Azure SignalR-tjänst för varje nivå. Om anslutningsnumret ökas ytterligare kommer det att drabbas av anslutningsbegränsning.

#### <a name="evaluation-for-complex-use-cases"></a>Utvärdering för komplexa användningsfall

##### <a name="bigger-message-size-or-different-sending-rate"></a>Större meddelandestorlek eller annan sändningshastighet

Det verkliga användningsfallet är mer komplicerat. Det kan skicka ett meddelande som är större än 2 048 byte, eller så är meddelandefrekvensen inte ett meddelande per sekund. Låt oss ta Unit100:s sändning som ett exempel för att hitta hur du utvärderar dess prestanda.

I följande tabell visas ett verkligt användningsfall **för sändning**. Men meddelandestorlek, antal anslutningar och meddelandesändning skiljer sig från vad vi antog i föregående avsnitt. Frågan är hur vi kan härleda någon av dessa objekt (meddelandestorlek, anslutningsantal eller meddelande sändningshastighet) om vi bara känner till två av dem.

| Sändning  | Meddelandestorlek | Inkommande meddelanden per sekund | Anslutningar | Skicka intervall |
|---|---------------------|--------------------------|-------------|-------------------------|
| 1 | 20 KB                | 1                        | 100 000     | 5 sek                      |
| 2 | 256 kB               | 1                        | 8,000       | 5 sek                      |

Följande formel är lätt att dra slutsatsen baserat på föregående formel:

```
outboundConnections = outboundBandwidth * sendInterval / messageSize
```

För Unit100 är den maximala utgående bandbredden 400 MB från föregående tabell. För en meddelandestorlek på 20 KB bör den maximala utgående \* anslutningen vara 400 MB 5 / 20 KB = 100 000, vilket motsvarar det verkliga värdet.

##### <a name="mixed-use-cases"></a>Fall med blandad användning

Det verkliga användningsfallet blandar vanligtvis de fyra grundläggande användningsfallen tillsammans: **echo**, **broadcast,** **send to group**och send to **connection**. Den metod som du använder för att utvärdera kapaciteten är att:

1. Dela upp de blandade användningsfallen i fyra grundläggande användningsfall.
1. Beräkna den maximala inkommande och utgående meddelandebandbredden med hjälp av föregående formler separat.
1. Summera bandbreddsberäkningarna för att få den totala maximala inkommande/utgående bandbredden. 

Plocka sedan upp rätt nivå från de maximala inkommande/utgående bandbreddstabellerna.

> [!NOTE]
> För att skicka ett meddelande till hundratals eller tusentals små grupper, eller för tusentals klienter som skickar ett meddelande till varandra, kommer routningskostnaden att bli dominerande. Ta hänsyn till denna inverkan.

Kontrollera att appservern *inte* är flaskhalsen för att skicka ett meddelande till klienter. Följande avsnitt "Fallstudie" innehåller riktlinjer för hur många appservrar du behöver och hur många serveranslutningar du ska konfigurera.

## <a name="case-study"></a>Fallstudie

Följande avsnitt går igenom fyra vanliga användningsfall för WebSocket transport: **echo**, **broadcast,** **send to group**och send to **connection**. För varje scenario visas den aktuella inkommande och utgående kapaciteten för Azure SignalR-tjänsten. Det förklarar också de viktigaste faktorerna som påverkar prestanda.

I standardläget skapar appservern fem serveranslutningar med Azure SignalR Service. Appservern använder Azure SignalR Service SDK som standard. I följande prestandatestresultat ökas serveranslutningarna till 15 (eller mer för sändning och sändning av ett meddelande till en stor grupp).

Olika användningsfall har olika krav för appservrar. **Broadcast** behöver ett litet antal appservrar. **Eko** eller **skicka till anslutning** behöver många appservrar.

I alla användningsfall är standardmeddelandestorleken 2 048 byte och meddelandesändningsintervallet är 1 sekund.

### <a name="default-mode"></a>Standardläge

Klienter, webbappservrar och Azure SignalR-tjänst är involverade i standardläget. Varje kund står för en enda anslutning.

#### <a name="echo"></a>Echo

Först ansluter en webbapp till Azure SignalR Service. För det andra ansluter många klienter till webbappen, som omdirigerar klienterna till Azure SignalR-tjänsten med åtkomsttoken och slutpunkten. Sedan upprättar klienterna WebSocket-anslutningar med Azure SignalR-tjänst.

När alla klienter har upprättat anslutningar börjar de skicka ett meddelande som innehåller en tidsstämpel till den specifika hubben varje sekund. Navet ekar meddelandet tillbaka till sin ursprungliga klient. Varje klient beräknar svarstiden när den tar emot ekomeddelandet tillbaka.

I följande diagram är 5 till 8 (röd markerad trafik) i en slinga. Loopen körs för en standardvaraktighet (5 minuter) och hämtar statistik för all meddelandefördröjning.

![Trafik för eko användningsfallet](./media/signalr-concept-performance/echo.png)

Beteendet för **eko** avgör att den maximala inkommande bandbredden är lika med den maximala utgående bandbredden. Mer information finns i följande tabell.

|       Echo                        | Enhet1 | Enhet2 | Enhet5 | Enhet10 | Enhet20 | Enhet50 | Enhet100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar                       | 1,000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Inkommande/utgående meddelanden per sekund | 1,000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Inkommande/utgående bandbredd | 2 Mbit/s   | 4 Mbit/s   | 10 Mbit/s  | 20 MBps   | 40 MBps   | 100 MBps  | 200 MBps   |

I det här användningsfallet anropar varje klient navet som definierats i appservern. Navet anropar bara den metod som definierats på den ursprungliga klientsidan. Det här navet är det mest lätta navet för **eko**.

```
        public void Echo(IDictionary<string, object> data)
        {
            Clients.Client(Context.ConnectionId).SendAsync("RecordLatency", data);
        }
```

Även för den här enkla hubben är trafiktrycket på appservern framträdande när **ekots** inkommande meddelandebelastning ökar. Detta trafiktryck kräver många appservrar för stora SKU-nivåer. I följande tabell visas antalet appserver för varje nivå.


|    Echo          | Enhet1 | Enhet2 | Enhet5 | Enhet10 | Enhet20 | Enhet50 | Enhet100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar      | 1,000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Antal appserver | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Appserverns klientanslutningsnummer, meddelandestorlek, meddelandesändningshastighet, SKU-nivå och CPU/minne för appservern påverkar den övergripande **prestandan för ekot**.

#### <a name="broadcast"></a>Sändning

För **sändning**, när webbappen tar emot meddelandet, sänds det till alla klienter. Ju fler klienter det finns att sända, desto mer meddelande trafik finns till alla klienter. Se följande diagram.

![Trafik för sändningsanvändningsfallet](./media/signalr-concept-performance/broadcast.png)

Ett litet antal kunder sänder. Den inkommande meddelande bandbredden är liten, men den utgående bandbredden är enorm. Bandbredden för utgående meddelanden ökar när klientanslutningen eller sändningshastigheten ökar.

I följande tabell sammanfattas maximala klientanslutningar, inkommande/utgående meddelandeantal och bandbredd.

|     Sändning             | Enhet1 | Enhet2 | Enhet5  | Enhet10 | Enhet20 | Enhet50  | Enhet100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Anslutningar               | 1,000 | 2 000 | 5 000  | 10 000 | 20 000 | 50 000  | 100 000 |
| Inkommande meddelanden per sekund  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Utgående meddelanden per sekund | 2 000 | 4 000 | 10 000 | 20 000 | 40 000 | 100 000 | 200 000 |
| Inkommande bandbredd  | 4 KBps   | 4 KBps   | 4 KBps    | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps     |
| Utgående bandbredd | 4 Mbit/s   | 8 Mbit/s   | 20 MBps   | 40 MBps   | 80 MBps   | 200 MBps   | 400 MBps   |

De sändningsklienter som publicerar meddelanden är inte fler än fyra. De behöver färre appservrar jämfört med **eko** eftersom inkommande meddelandebelopp är litet. Två appservrar räcker för både SLA och prestandaöverväganden. Men du bör öka standardserveranslutningarna för att undvika obalans, särskilt för Unit50 och Unit100.

|   Sändning      | Enhet1 | Enhet2 | Enhet5 | Enhet10 | Enhet20 | Enhet50 | Enhet100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar      | 1,000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Antal appserver | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Öka standardserveranslutningarna från 5 till 40 på varje appserver för att undvika eventuella obalanserade serveranslutningar till Azure SignalR Service.
>
> Klientanslutningsnumret, meddelandestorleken, meddelandesändningshastigheten och SKU-nivån påverkar det övergripande resultatet för **sändning**.

#### <a name="send-to-group"></a>Skicka till grupp

**Användningsfallet skicka till grupp** har ett liknande trafikmönster som ska **sändas**. Skillnaden är att när klienter har upprättat WebSocket-anslutningar med Azure SignalR-tjänst måste de gå med i grupper innan de kan skicka ett meddelande till en viss grupp. Följande diagram illustrerar trafikflödet.

![Trafik för användningsfallet för skicka till grupp](./media/signalr-concept-performance/sendtogroup.png)

Gruppmedlem och gruppantal är två faktorer som påverkar prestanda. För att förenkla analysen definierar vi två typer av grupper:

- **Liten grupp:** Varje grupp har 10 anslutningar. Gruppnumret är lika med (max anslutningsantal) / 10. Till exempel för Enhet1, om det finns 1000 anslutningsantal, så har vi 1000 / 10 = 100 grupper.

- **Stor grupp:** Gruppnumret är alltid 10. Antalet gruppmedlemmar är lika med (max anslutningsantal) / 10. Till exempel för Unit1, om det finns 1000 anslutningsantal, har varje grupp 1000 / 10 = 100 medlemmar.

**Skicka till grupp** ger en routningskostnad till Azure SignalR-tjänsten eftersom den måste hitta målanslutningarna via en distribuerad datastruktur. När de sändande anslutningarna ökar ökar kostnaden.

##### <a name="small-group"></a>Liten grupp

Flödeskostnaden är viktig för att skicka meddelanden till många små grupper. Azure SignalR-tjänstimplementeringen når kostnadsgränsen för routning på Unit50. Att lägga till mer CPU och minne hjälper inte, så Unit100 kan inte förbättras ytterligare genom design. Om du behöver mer inkommande bandbredd kontaktar du kundtjänst.

|   Skicka till liten grupp     | Enhet1 | Enhet2 | Enhet5  | Enhet10 | Enhet20 | Enhet50 | Enhet100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| Anslutningar               | 1,000 | 2 000 | 5 000  | 10 000 | 20 000 | 50 000 | 100 000
| Antal medlemmar i grupp        | 10    | 10    | 10     | 10     | 10     | 10     | 10 
| Antal grupper               | 100   | 200   | 500    | 1,000  | 2 000  | 5 000  | 10 000 
| Inkommande meddelanden per sekund  | 200   | 400   | 1,000  | 2 500  | 4 000  | 7,000  | 7,000   |
| Inkommande bandbredd  | 400 KBps  | 800 KBps  | 2 Mbit/s     | 5 Mbit/s     | 8 Mbit/s     | 14 MBps    | 14 MBps     |
| Utgående meddelanden per sekund | 2 000 | 4 000 | 10 000 | 25,000 | 40 000 | 70,000 | 70,000  |
| Utgående bandbredd | 4 Mbit/s    | 8 Mbit/s    | 20 MBps    | 50 MBps     | 80 MBps    | 140 MBps   | 140 MBps    |

Många klientanslutningar anropar navet, så appservernumret är också avgörande för prestanda. I följande tabell visas antalet föreslagna appserver.

|  Skicka till liten grupp   | Enhet1 | Enhet2 | Enhet5 | Enhet10 | Enhet20 | Enhet50 | Enhet100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar      | 1,000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Antal appserver | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Appserverns klientanslutningsnummer, meddelandestorlek, meddelandeöverföringshastighet, routningskostnad, SKU-nivå och CPU/minne för appservern påverkar den övergripande prestandan **för sändning till liten grupp**.

##### <a name="big-group"></a>Stor grupp

För **skicka till stor grupp**blir den utgående bandbredden flaskhalsen innan du når gränsen för routningskostnad. I följande tabell visas den maximala utgående bandbredden, vilket är nästan samma som för **sändning**.

|    Skicka till stor grupp      | Enhet1 | Enhet2 | Enhet5  | Enhet10 | Enhet20 | Enhet50  | Enhet100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Anslutningar               | 1,000 | 2 000 | 5 000  | 10 000 | 20 000 | 50 000  | 100 000
| Antal medlemmar i grupp        | 100   | 200   | 500    | 1,000  | 2 000  | 5 000   | 10 000 
| Antal grupper               | 10    | 10    | 10     | 10     | 10     | 10      | 10
| Inkommande meddelanden per sekund  | 20    | 20    | 20     | 20     | 20     | 20      | 20      |
| Inkommande bandbredd  | 80 KBps   | 40 KBps   | 40 KBps    | 20 KBps (20 KBps)    | 40 KBps    | 40 KBps     | 40 KBps     |
| Utgående meddelanden per sekund | 2 000 | 4 000 | 10 000 | 20 000 | 40 000 | 100 000 | 200 000 |
| Utgående bandbredd | 8 Mbit/s    | 8 Mbit/s    | 20 MBps    | 40 MBps    | 80 MBps    | 200 MBps    | 400 MBps    |

Antalet sändande anslutningar är inte mer än 40. Bördan på appservern är liten, så det föreslagna antalet webbappar är litet.

|  Skicka till stor grupp  | Enhet1 | Enhet2 | Enhet5 | Enhet10 | Enhet20 | Enhet50 | Enhet100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar      | 1,000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Antal appserver | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Öka standardserveranslutningarna från 5 till 40 på varje appserver för att undvika eventuella obalanserade serveranslutningar till Azure SignalR Service.
> 
> Klientanslutningsnumret, meddelandestorleken, meddelandeöverföringshastigheten, routningskostnaden och SKU-nivån påverkar den övergripande prestandan **för skicka till stor grupp**.

#### <a name="send-to-connection"></a>Skicka till anslutning

När **send to connection** klienter upprättar anslutningarna till Azure SignalR-tjänsten anropar varje klient en särskild hubb för att få ett eget anslutnings-ID när klienter upprättar anslutningarna till Azure SignalR-tjänsten. Prestandariktmärket samlar in alla anslutnings-ID:er, blandar dem och återtillvisar dem till alla klienter som ett sändningsmål. Klienterna fortsätter att skicka meddelandet till målanslutningen tills prestandatestet är klart.

![Trafik för användningsfallet för skicka till klient](./media/signalr-concept-performance/sendtoclient.png)

Flödeskostnaden för **skicka till anslutning** liknar kostnaden för att skicka till en liten **grupp**.

När antalet anslutningar ökar begränsar operationsföljdskostnaden det totala resultatet. Enhet50 har nått gränsen. Som ett resultat kan Unit100 inte förbättras ytterligare.

Följande tabell är en statistisk sammanfattning efter många omgångar för att köra **referensvärdet för skicka till anslutning.**

|   Skicka till anslutning   | Enhet1 | Enhet2 | Enhet5 | Enhet10 | Enhet20 | Enhet50          | Enhet100         |
|------------------------------------|-------|-------|-------|--------|--------|-----------------|-----------------|
| Anslutningar                        | 1,000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000          | 100 000         |
| Inkommande/utgående meddelanden per sekund | 1,000 | 2 000 | 5 000 | 8,000  | 9,000  | 20 000 | 20 000 |
| Inkommande/utgående bandbredd | 2 Mbit/s    | 4 Mbit/s    | 10 Mbit/s   | 16 MBps    | 18 MBps    | 40 MBps       | 40 MBps       |

Det här användningsfallet kräver hög belastning på appserversidan. Se det föreslagna antalet appserver i följande tabell.

|  Skicka till anslutning  | Enhet1 | Enhet2 | Enhet5 | Enhet10 | Enhet20 | Enhet50 | Enhet100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar      | 1,000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Antal appserver | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Klientanslutningsnummer, meddelandestorlek, meddelandeöverföringshastighet, routningskostnad, SKU-nivå och CPU/minne för appservern påverkar den övergripande prestandan för **skicka till anslutning**.

#### <a name="aspnet-signalr-echo-broadcast-and-send-to-small-group"></a>ASP.NET SignalR eko, broadcast och skicka till liten grupp

Azure SignalR-tjänsten ger samma prestandakapacitet för ASP.NET SignalR. 

Prestandatestet använder Azure Web Apps från [Standard Service Plan S3](https://azure.microsoft.com/pricing/details/app-service/windows/) för ASP.NET SignalR.

I följande tabell visas det föreslagna antalet webbappar för ASP.NET **SignalR-ekot**.

|   Echo           | Enhet1 | Enhet2 | Enhet5 | Enhet10 | Enhet20 | Enhet50 | Enhet100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar      | 1,000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Antal appserver | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

I följande tabell visas det föreslagna antalet webbappar för ASP.NET **SignalR-sändning**.

|  Sändning       | Enhet1 | Enhet2 | Enhet5 | Enhet10 | Enhet20 | Enhet50 | Enhet100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar      | 1,000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Antal appserver | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

I följande tabell visas det föreslagna antalet webbappar för ASP.NET **SignalR-sändning till liten grupp**.

|  Skicka till liten grupp     | Enhet1 | Enhet2 | Enhet5 | Enhet10 | Enhet20 | Enhet50 | Enhet100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar      | 1,000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Antal appserver | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

### <a name="serverless-mode"></a>Serverlöst läge

Klienter och Azure SignalR-tjänst är involverade i serverlöst läge. Varje kund står för en enda anslutning. Klienten skickar meddelanden via REST API till en annan klient eller broadcast-meddelanden till alla.

Att skicka meddelanden med hög densitet via REST API är inte lika effektivt som att använda WebSocket. Det kräver att du skapar en ny HTTP-anslutning varje gång, och det är en extra kostnad i serverlöst läge.

#### <a name="broadcast-through-rest-api"></a>Sändning via REST API
Alla klienter upprättar WebSocket-anslutningar med Azure SignalR-tjänst. Sedan börjar vissa klienter sända via REST API. Meddelandet som skickas (inkommande) är allt via HTTP Post, vilket inte är effektivt jämfört med WebSocket.

|   Sändning via REST API     | Enhet1 | Enhet2 | Enhet5  | Enhet10 | Enhet20 | Enhet50  | Enhet100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Anslutningar               | 1,000 | 2 000 | 5 000  | 10 000 | 20 000 | 50 000  | 100 000 |
| Inkommande meddelanden per sekund  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Utgående meddelanden per sekund | 2 000 | 4 000 | 10 000 | 20 000 | 40 000 | 100 000 | 200 000 |
| Inkommande bandbredd  | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps     | 4 KBps     | 4 KBps      | 4 KBps      |
| Utgående bandbredd | 4 Mbit/s    | 8 Mbit/s    | 20 MBps    | 40 MBps    | 80 MBps    | 200 MBps    | 400 MBps    |

#### <a name="send-to-user-through-rest-api"></a>Skicka till användare via REST API
Riktmärket tilldelar användarnamn till alla klienter innan de börjar ansluta till Azure SignalR Service. När klienterna har upprättat WebSocket-anslutningar med Azure SignalR-tjänsten börjar de skicka meddelanden till andra via HTTP Post.

|   Skicka till användare via REST API | Enhet1 | Enhet2 | Enhet5  | Enhet10 | Enhet20 | Enhet50  | Enhet100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Anslutningar               | 1,000 | 2 000 | 5 000  | 10 000 | 20 000 | 50 000  | 100 000 |
| Inkommande meddelanden per sekund  | 300   | 600   | 900    | 1,300  | 2 000  | 10 000  | 18 000  |
| Utgående meddelanden per sekund | 300   | 600   | 900    | 1,300  | 2 000  | 10 000  | 18 000 |
| Inkommande bandbredd  | 600 KBps  | 1,2 MBps  | 1,8 MBps   | 2,6 MBps   | 4 Mbit/s     | 10 Mbit/s     | 36 MBps    |
| Utgående bandbredd | 600 KBps  | 1,2 MBps  | 1,8 MBps   | 2,6 MBps   | 4 Mbit/s     | 10 Mbit/s     | 36 MBps    |

## <a name="performance-test-environments"></a>Prestandatestmiljöer

För alla användningsfall som anges tidigare genomförde vi prestandatesterna i en Azure-miljö. Som mest använde vi 50 virtuella klient-datorer och 20 appserver-virtuella datorer. Här följer lite information:

- Vm-storlek för klient: StandardDS2V2 (2 vCPU, 7G-minne)

- Vm-storlek för appserver: StandardF4sV2 (4 vCPU, 8G-minne)

- Azure SignalR SDK-serveranslutningar: 15

## <a name="performance-tools"></a>Prestandaverktyg

Du kan hitta prestandaverktyg för Azure SignalR-tjänsten på [GitHub](https://github.com/Azure/azure-signalr-bench/).

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du fått en översikt över Azure SignalR-tjänstens prestanda i typiska användningsfallsscenarier.

Om du vill ha information om tjänstens interna utgifter och skalning för den läser du följande guider:

* [Azure SignalR Service – internt](signalr-concept-internals.md)
* [Skala azure signalr-tjänsten](signalr-howto-scale-multi-instances.md)
