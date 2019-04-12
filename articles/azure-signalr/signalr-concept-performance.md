---
title: Prestandaguide för Azure SignalR Service
description: En översikt över Azure SignalR Service prestanda.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: zhshang
ms.openlocfilehash: 53139dd253c491ea6578fd0b9cbada4e7b331c7d
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502045"
---
# <a name="performance-guide-for-azure-signalr-service"></a>Prestandaguide för Azure SignalR Service

En av de främsta fördelarna för att använda Azure SignalR Service är enkelt att skala SignalR-program. I ett scenario med storskaliga blir prestanda en viktig faktor. I den här guiden kommer vi att införa de faktorer som har påverkan på SignalR programmets prestanda och under olika användningsfall, vad är typiska prestanda? I slutändan vi kommer också att införa miljön och verktyg som används för att generera en rapport över.

## <a name="terms-definition"></a>Definition av termer

*ASRS*: Azure SignalR Service

*Inkommande*: det inkommande meddelandet till Azure SignalR Service

*Utgående*: utgående meddelandet från Azure SignalR Service

*Bandbredd*: total storlek för alla meddelanden i 1 sekund

*Standardläget*: ASRS förväntar sig app-servern för att upprätta anslutningen med den innan du godkänner klientanslutningar. Det är standardläget för arbeta när en ASRS skapades.

*Serverlös läge*: ASRS bara tillåter klientanslutningar. Ingen serveranslutning tillåts.

## <a name="overview"></a>Översikt

ASRS definierar sju Standard-nivåerna för olika prestanda kapaciteter och den här guiden har för avsikt att besvara följande frågor:

-   Vad är vanliga ASRS prestanda för varje nivå?

-   ASRS uppfyller Mina krav på dataflöde för meddelanden, till exempel skicka 100 000 meddelanden per sekund?

-   Min specifika scenario, vilken nivå passar mig? Eller hur kan jag välja rätt nivå?

-   Vilken typ av app-servern (VM-storlek) är lämplig för mig och hur många av dem bör jag distribuera?

För att besvara dessa frågor handboken prestanda först ger en övergripande förklaring av de faktorer som ha påverkan på prestanda och sedan visar de maximala inkommande och utgående meddelandena för varje nivå för typiska användningsexempel: **echo**, **sändning**, **skicka till grupp**, och **skicka till anslutningen** (peer-to-peer chatta).

Det går inte för det här dokumentet att täcka alla scenarier (och olika användningsfall, olika meddelandestorlek skicka meddelandemönstret o.s.v.). Det ger dock några utvärderingsmetoder för att hjälpa användare att cirka utvärdera kraven för de inkommande eller utgående meddelandena och hitta rätt nivåer genom att markera tabellen prestanda.

## <a name="performance-insight"></a>Prestandainsikt

Det här avsnittet beskrivs metoderna för utvärdering av prestanda och listar alla faktorer som har påverkan på prestanda. I slutändan ger det metoder för att utvärdera prestandakraven.

### <a name="methodology"></a>Metod

**Dataflöde** och **svarstid** finns två vanliga aspekter för kontroll av prestanda. För ASRS har olika SKU-nivån olika dataflöde begränsning av principen. Detta dokument definierar **högsta tillåtna dataflöde (inkommande och utgående bandbredd)** eftersom det största värdet har uppnått dataflöde när 99% av meddelanden har svarstid mindre än 1 sekund.

Svarstiden är tidsintervallet från skicka meddelandet till tar emot svarsmeddelandet från ASRS-anslutningen. Låt oss ta **echo** exempelvis varje klientanslutning lägger till en tidsstämpel i meddelandet. App-servern hub skickar det ursprungliga meddelandet tillbaka till klienten. Fördröjningen spridning är så enkelt beräknas genom att varje klientanslutning. Tidsstämpeln kopplas för varje meddelande i **sändning**, **skicka till grupp**, och **skicka till anslutningen**.

Om du vill simulera tusentals samtidiga klienter anslutningar kan skapas flera virtuella datorer i ett virtuellt privat nätverk i Azure. Alla dessa virtuella datorer ansluta till samma ASRS-instans.

I ASRS standardläget distribueras också virtuella app-servrar i samma virtuella privata nätverk som virtuella datorer-klient.

Alla klient virtuella datorer och AppServer virtuella datorerna distribueras i samma virtuella nätverk i samma region för att undvika mellan region svarstid.

### <a name="performance-factors"></a>Prestandafaktorer

Teoretiskt sett kan begränsas ASRS kapacitet av beräkningsresurser som: Processor, minne och nätverk. Till exempel fler anslutningar till ASRS mer minne ASRS förbrukas. För större meddelandetrafik, till exempel alla meddelanden som är större än 2 048 byte, ASRS att spendera mer CPU-cykler för att bearbeta samt krävs. Under tiden kan tillämpar Azure nätverkets bandbredd också en gräns för maximal trafik.

Transporttypen [WebSocket](https://en.wikipedia.org/wiki/WebSocket), [servern skickas händelsen](https://en.wikipedia.org/wiki/Server-sent_events), eller [-Longpolling](https://en.wikipedia.org/wiki/Push_technology), är en annan faktor som påverkar prestanda. WebSocket är ett dubbelriktat och full duplex kommunikationsprotokoll genom en enda TCP-anslutning. Servern skickade händelse är dock enkelriktad protokoll till push-meddelande från servern till klienten. -Longpolling kräver att klienter för regelbundna avsökningar information från servern via HTTP-begäran. WebSocket har bästa möjliga prestanda för samma API under samma villkor, servern skickas händelsen är långsammare och -Longpolling är långsammast. ASRS rekommenderar WebSocket som standard.

Dessutom begränsar meddelande routning kostnaden också prestanda. ASRS spelar en roll som en router för meddelandet som dirigerar meddelande från en uppsättning klienter eller servrar till andra klienter eller servrar. Annat scenario eller API: et kräver olika routningsprincip. För **echo**klienten skickar ett meddelande till sig själv och routning målet är också själva. Det här mönstret har lägst routning kostnaden. Men för **sändning**, **skicka till grupp**, **skicka till anslutningen**, ASRS behöver söka efter mål-anslutningar via interna distribuerade datastrukturen, som förbrukar mer CPU, minne och även bandbredd i nätverket. Därför kan prestanda är långsammare än **echo**.

I standardläget bli app-servern också en flaskhals för vissa scenarier, eftersom Azure SignalR SDK måste anropa hubben, under tiden den upprätthåller live-anslutning med varje klient via pulsslag signaler.

Klienten skickar meddelandet per HTTP-post, vilket inte är lika effektiva som WebSocket i serverlös läge.

En annan faktor är protokoll: JSON och [MessagePack](https://msgpack.org/index.html). MessagePack är mindre än storleken och levereras snabbare än JSON. Intuitivt, MessagePack skulle ha nytta av prestanda, men ASRS prestanda är inte känsliga med protokoll eftersom det inte avkoda meddelandet nyttolasten under vidarebefordran av meddelanden från klienter till servrar eller vice versa.

Sammanfattningsvis har följande faktorer påverkar på inkommande och utgående kapacitet:

-   SKU-nivån (processor/minne)

-   Antal anslutningar

-   Meddelandestorlek

-   Skicka Meddelandefrekvens

-   transporttyp (WebSocket/Server-skickas-händelse /-Longpolling)

-   användningsfallsscenario (Routning kostnad)

-   App-server och tjänsten anslutningar (i server-läge)


### <a name="find-a-proper-sku"></a>Hitta en korrekt SKU

Så här utvärderar inkommande/utgående kapacitet eller hitta vilken nivå som passar specifika användningsfall?

Vi förutsätter att appservern är tillräckligt kraftfull och är inte flaskhals för prestanda. Sedan markerar vi den maximala inkommande och utgående bandbredden för varje nivå.

#### <a name="quick-evaluation"></a>Snabb utvärdering

Vi kan förenkla utvärderingen först genom att vissa standardinställningar: WebSocket används, meddelandestorlek är 2 048 byte, och skicka meddelande varje sekund och det är i läge.

På varje nivå har sina egna Maximal bandbredd för inkommande och utgående bandbredd. Smidig användarupplevelse är inte säkert när inkommande eller utgående överskrider gränsen.

**Echo** ger maximal inkommande bandbredd eftersom den har den lägsta routning kostnaden. **Skicka** definierar den maximala utgående message bandbredden.

Gör **inte** överskrider de markerade värdena i följande två tabeller.

|       echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar                       | 1,000 | 2,000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| **Inkommande bandbredd (byte/s)** | **2M**    | **4M**    | **10M**   | **20M**    | **40M**    | **100M**   | **200M**    |
| Utgående bandbredd (byte/s) | 2M    | 4M    | 10M   | 20M    | 40M    | 100M   | 200M    |


|     Sändning             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Anslutningar               | 1,000 | 2,000 | 5 000  | 10 000 | 20,000 | 50,000  | 100 000 |
| Inkommande bandbredd (byte/s)  | 4K    | 4K    | 4K     | 4K     | 4K     | 4K      | 4K     |
| **Utgående bandbredd (byte/s)** | **4M**    | **8M**    | **20M**    | **40M**    | **80M**    | **200M**    | **400M**   |

Bandbredd för inkommande och utgående bandbredd formler:
```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

*inboundConnections*: antalet anslutningar som skickar meddelande

*outboundConnections*: antalet anslutningar som har fått meddelandet

*messageSize*: storleken på ett enda meddelande (genomsnittligt värde). För små meddelandet vars storlek är mindre än 1 024 byte, har den liknande prestandapåverkan som 1024 byte-meddelande.

*sendInterval*: tid för att skicka ett meddelande, vanligtvis det är 1 sekund per meddelande, vilket innebär att skicka ett meddelande varje sekund. Mindre sendInterval innebär mer meddelande skickades angivna tidsperiod. Till exempel innebär 0,5 sekund per meddelande och skicka två meddelanden per sekund.

*Anslutningar* är ASRS allokerade högsta tröskelvärdet för varje nivå. Om ytterligare ökas antalet anslutning, kommer den drabbas av anslutningsbegränsning av.

*Inkommande bandbredd* och *utgående bandbredd* är den totala meddelandestorleken per sekund. Här är ”innebär att megabyte för enkelhetens skull.

#### <a name="evaluation-for-complex-use-cases"></a>Utvärdering för komplexa användningsfall

##### <a name="bigger-message-size-or-different-sending-rate"></a>Större storlek eller annan takten för sändning

Den verkliga är mer komplicerad. Det kan skicka meddelande som är större än 2 048 byte eller skicka Meddelandefrekvens är inte ett meddelande per sekund. Låt oss ta unit100's sändning som exempel för att ta reda på hur att utvärdera dess prestanda.

I följande tabell visas en verklig fallet med **sändning**, men meddelandestorlek, antal anslutningar och skicka rate meddelanden skiljer sig från vad vi antas i föregående avsnitt. Frågan är hur vi kan härleda dessa objekt (meddelandestorlek, antal anslutningar eller skicka Meddelandefrekvens) om vi bara känner till 2 av dem.

| Sändning  | Meddelandestorlek (byte) | Inkommande (meddelanden/s) | Anslutningar | Skicka intervall (andra) |
|---|---------------------|--------------------------|-------------|-------------------------|
| 1 | 20 K                 | 1                        | 100 000     | 5                       |
| 2 | 256 KB                | 1                        | 8,000       | 5                       |

Följande formel som är enkelt att härledas baserat på den föregående befintliga formeln:

```
outboundConnections = outboundBandwidth * sendInterval / messageSize
```

För unit100, vi vet max utgående bandbredd är 400M från föregående tabell, så för 20-K meddelandestorlek högsta antal utgående anslutningar bör vara 400M \* 5 / 20 K = 100 000, som matchar det verkliga värdet.

##### <a name="mixed-use-cases"></a>Blandade användningsfall

Det verkliga användningsfallet vanligtvis blandas fyra grundläggande användningsfall tillsammans: **echo**, **sändning**, **skicka till gruppen**, eller **skicka till anslutningen**. Den metod som används för att utvärdera kapaciteten är att dela upp blandat användningsfallen i fyra grundläggande användningsfall **beräkna bandbredden som maximal inkommande och utgående message** med hjälp av ovanstående formlerna separat och summerar dem för att få det totala antalet Maximal inkommande/utgående bandbredd. Hämta sedan rätt nivå från tabellerna maximal inkommande/utgående bandbredd.

Under tiden för att skicka meddelandet till hundratals eller tusentals små grupper eller tusentals klienter skickar meddelandet till varandra, blir routning kostnaden dominerande. Denna effekt bör beaktas. Mer information beskrivs i avsnitten ”Fallstudie”.

För användningsfall för att skicka meddelandet till klienter, kontrollera app-servern är **inte** flaskhalsen. ”Fallstudie” avsnittet ger riktlinjer om hur många app-servrar du behöver och hur många anslutningar ska konfigureras.

## <a name="case-study"></a>Fallstudie

I följande avsnitt går igenom fyra vanliga användningsområden för WebSocket transport: **echo**, **sändning**, **skicka till grupp**, och **skicka till anslutning** . För varje scenario, visas en lista med aktuella ASRS inkommande och utgående kapacitet under tiden som förklarar vad är de viktigaste faktorerna på prestanda.

I skapar läget, App-servern via Azure SignalR Service SDK som standard fem serveranslutningar med ASRS. I prestanda testresultat nedan, server anslutningar ökas till 15 (eller mer för sändning och skicka meddelandet till stor grupp).

Olika användningsfall har olika krav på app-servrar. **Skicka** måste litet antal app-servrar. **Echo** eller **skicka till anslutningen** måste många app-servrar.

I alla användningsfall, meddelande standardstorleken är 2 048 byte och message send intervall är 1 sekund.

## <a name="default-mode"></a>Standardläge

Klienter, web app-servrar och ASRS ingår i det här läget. Varje klient står för en enda anslutning.

### <a name="echo"></a>echo

Det första ansluta webbappar till ASRS. För det andra många klienter ansluter till webbapp som omdirigerar klienterna till ASRS med åtkomsttoken och slutpunkt. Klienter etablerar sedan WebSocket-anslutningar med ASRS.

När alla klienter upprätta anslutningar, börjar de skicka postmeddelande som innehåller en tidsstämpel till specifika hubben varje sekund. Hubben ekar svaret till dess ursprungliga klienten. Varje klient beräknar svarstiden när den får meddelandet echo tillbaka.

Steg 5\~8 (röd markerade trafik) finns i en slinga, som ska köras för en standardvaraktighet (5 minuter) och få statistik på alla meddelande svarstid.
Guiden prestanda visar högsta tal för anslutning av klienten.

![echo](./media/signalr-concept-performance/echo.png)

**Echo**'s beteendet anger att den inkommande maximal bandbredden är lika med högsta utgående bandbredd. Se tabellen nedan.

|       echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar                       | 1,000 | 2,000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Inkommande/utgående (meddelanden/s) | 1,000 | 2,000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Inkommande/utgående bandbredd (byte/s) | 2M    | 4M    | 10M   | 20M    | 40M    | 100M   | 200M    |

I det här användningsfallet anropar varje klient hubben som definierats i app-servern. Hubben anropar bara den metod som definierats i ursprungliga klientsidan. Den här hubben är den mest ljus vägda hubben för **echo**.

```
        public void Echo(IDictionary<string, object> data)
        {
            Clients.Client(Context.ConnectionId).SendAsync("RecordLatency", data);
        }
```

Även för den här enkla hubben är trafik trycket på app-servern också framstående som den **echo** inkommande meddelande ökar. Detta kräver därför många app-servrar för stora SKU-nivåer. I följande tabell visas antalet app server för varje nivå.


|    echo          | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar      | 1,000 | 2,000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Antal för App-servrar | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
>
> Klienten anslutning number, meddelandestorleken, skicka hastighet, SKU-nivån och programserverns processor/minne meddelanden har inverkan på prestandan **echo**.

### <a name="broadcast"></a>Sändning

För **sändning**när webbapp tar emot meddelandet skickas till alla klienter. Fler klienter att sända mer meddelandetrafik till alla klienter. Se i följande diagram.

![Sändning](./media/signalr-concept-performance/broadcast.png)

Egenskap för sändning är att det finns ett litet antal klienter som skickar data, vilket innebär att den inkommande message bandbredden är liten, men den utgående bandbredden är stor. Bandbredden som utgående message ökar som klientanslutningen eller broadcast-frekvens ökar.

Den maximala klientanslutningar, antal inkommande/utgående meddelanden och bandbredd sammanfattas i tabellen nedan.

|     Sändning             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Anslutningar               | 1,000 | 2,000 | 5 000  | 10 000 | 20,000 | 50,000  | 100 000 |
| Inkommande (meddelanden/s)  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Utgående (meddelanden/s) | 2,000 | 4,000 | 10 000 | 20,000 | 40,000 | 100 000 | 200 000 |
| Inkommande bandbredd (byte/s)  | 4K    | 4K    | 4K     | 4K     | 4K     | 4K      | 4K      |
| Utgående bandbredd (byte/s) | 4M    | 8M    | 20M    | 40M    | 80M    | 200M    | 400M    |

Broadcasting klienter som skickar meddelanden mellan mer än 4, kräver färre appservrar jämfört med **echo** eftersom det inkommande meddelandet är små. Två appservrar är tillräckligt överväger både SLA och prestanda. Men standard serveranslutningar bör utökas för att undvika Obalanserat problemet särskilt för Unit50 och Unit100.

|   Sändning      | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar      | 1,000 | 2,000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Antal för App-servrar | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
>
> Öka standard serveranslutningar från 5 till 40 på varje appservern för att undvika möjlig Obalanserat anslutningar till ASRS.
>
> Klienten anslutning antal, storlek, skicka Meddelandefrekvens och SKU-nivån inverka på prestandan för **sändning**

### <a name="send-to-group"></a>Skicka till grupp

**Skicka till grupp** har liknande trafikmönstret förutom att när du har klienter upprätta WebSocket-anslutningar med ASRS, måste de ansluta grupper innan de kan skicka meddelande till en specifik grupp. Trafikflödet illustreras av diagrammet nedan.

![Skicka till grupp](./media/signalr-concept-performance/sendtogroup.png)

Gruppmedlem och antalet är två faktorer med påverkan på prestanda. För att förenkla analysen kan vi definierar två typer av grupper: liten grupp och stor grupp.

- `small group`: 10 anslutningar i varje grupp. Gruppnumret är lika med (max anslutningsräknare) / 10. Till exempel för enhet 1, om det finns 1000 anslutning antal sedan har vi 1000 / 10 = 100 grupper.

- `Big group`: Gruppnumret är alltid 10. Medlemsantal grupp är lika med (max anslutningsräknare) / 10. För 1 enhet, om det finns 1000 anslutning antal sedan varje grupp har exempelvis 1000 / 10 = 100 medlemmarna.

**Skicka till grupp** ger routning kostnaden för ASRS eftersom den har att hitta mål-anslutningar via en distribuerad datastruktur. När skicka anslutningarna ökar, ökar även kostnaden.

#### <a name="small-group"></a>Liten grupp

Routning kostnaden är viktiga för att skicka meddelandet till många små grupper. För närvarande når ASRS implementeringen routning kostnadsgränsen vid unit50. Att lägga till mer Processorkraft och minne inte hjälper, så att det går inte att förbättra unit100 mer avsiktligt. Om du behöver mer ingående bandbredd, kontaktar du kundsupport för anpassning.

|   Skicka till liten grupp     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50 | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| Anslutningar               | 1,000 | 2,000 | 5 000  | 10 000 | 20,000 | 50,000 | 100 000
| Antal medlemmar för gruppen        | 10    | 10    | 10     | 10     | 10     | 10     | 10 
| Antalet               | 100   | 200   | 500    | 1,000  | 2,000  | 5 000  | 10 000 
| Inkommande (meddelanden/s)  | 200   | 400   | 1,000  | 2,500  | 4,000  | 7,000  | 7,000   |
| Inkommande bandbredd (byte/s)  | 400 K  | 800 K  | 2M     | 5M     | 8M     | 14M    | 14M     |
| Utgående (meddelanden/s) | 2,000 | 4,000 | 10 000 | 25,000 | 40,000 | 70,000 | 70,000  |
| Utgående bandbredd (byte/s) | 4M    | 8M    | 20M    | 50M     | 80M    | 140M   | 140M    |

Det finns många klientanslutningar anropa navet, därför är server appnumret viktigt för prestanda. Serverantal föreslagna appen visas i följande tabell.

|  Skicka till liten grupp   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar      | 1,000 | 2,000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Antal för App-servrar | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
>
> Klienten anslutning tal, meddelandestorlek, skicka Meddelandefrekvens, routning kostnader, SKU-nivån och programserverns processor/minne inverka på prestandan **skicka till mindre grupper**.

#### <a name="big-group"></a>Stor grupp

För **skicka-till-många-personer**, utgående bandbredd blir flaskhalsen innan träffa routning kostnad gränsen. I följande tabell visas den maximala utgående bandbredden som är nästan desamma som **sändning**.

|    Skicka till stor grupp      | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Anslutningar               | 1,000 | 2,000 | 5 000  | 10 000 | 20,000 | 50,000  | 100 000
| Antal medlemmar för gruppen        | 100   | 200   | 500    | 1,000  | 2,000  | 5 000   | 10 000 
| Antalet               | 10    | 10    | 10     | 10     | 10     | 10      | 10
| Inkommande (meddelanden/s)  | 20    | 20    | 20     | 20     | 20     | 20      | 20      |
| Inkommande bandbredd (byte/s)  | 80 K   | 40 K   | 40 K    | 20 K    | 40 K    | 40 K     | 40 K     |
| Utgående (meddelanden/s) | 2,000 | 4,000 | 10 000 | 20,000 | 40,000 | 100 000 | 200 000 |
| Utgående bandbredd (byte/s) | 8M    | 8M    | 20M    | 40M    | 80M    | 200M    | 400M    |

Skicka anslutningsräknare är mer än 40, belastningen på app-servern är liten, föreslagna web app-nummer är därför också små.

|  Skicka till stor grupp  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar      | 1,000 | 2,000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Antal för App-servrar | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
>
> Öka standard serveranslutningar från 5 till 40 på varje appservern för att undvika möjlig Obalanserat anslutningar till ASRS.
> 
> Klienten anslutning antal, meddelandestorlek, skicka Meddelandefrekvens, routning kostnad och SKU-nivån inverka på prestandan **skicka-till-många-personer**.

### <a name="send-to-connection"></a>Skicka till anslutning

I det här användningsfallet när klienter upprätta anslutningar till ASRS, anropar varje klient en särskild hubb för att hämta sina egna anslutnings-ID Prestanda-benchmark ansvarar för att samla in alla anslutning ID: N, blanda dem och tilldela dem till alla klienter som skickar mål. Klienterna fortsätta skicka meddelandet till målanslutningen tills prestandatest har slutförts.

![Skicka till klienten](./media/signalr-concept-performance/sendtoclient.png)

Routning kostnad för **skicka till anslutningen** liknar som **skicka till mindre grupper**.

När antalet anslutning ökar är den övergripande prestandan begränsat av Routning kostnaden. Enhet 50 har nått gränsen. Enhet 100 kan inte därmed förbättra ytterligare.

I följande tabell är en sammanfattande statistik efter många Rundar av att köra **skicka till anslutningen** prestandamått

|   Skicka till anslutning   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50          | Unit100         |
|------------------------------------|-------|-------|-------|--------|--------|-----------------|-----------------|
| Anslutningar                        | 1,000 | 2,000 | 5 000 | 10 000 | 20,000 | 50,000          | 100 000         |
| Inkommande / utgående (meddelanden/s) | 1,000 | 2,000 | 5 000 | 8,000  | 9,000  | 20,000 | 20,000 |
| Inkommande / utgående bandbredd (byte/s) | 2M    | 4M    | 10M   | 16M    | 18M    | 40M       | 40M       |

Det här användningsfallet kräver hög belastning på serversidan för appen. Se föreslagna appservern antal i tabellen nedan.

|  Skicka till anslutning  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar      | 1,000 | 2,000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Antal för App-servrar | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
>
> Klienten anslutning tal, meddelandestorlek, skicka Meddelandefrekvens, routning kostnader, SKU-nivån och programserverns processor/minne inverka på prestandan **skicka till anslutningen**.

### <a name="aspnet-signalr-echobroadcastsend-to-connection"></a>ASP.NET SignalR echo/sändning/skicka-till-anslutning

ASRS ger samma kapacitet för prestanda för ASP.NET SignalR. Det här avsnittet ger det föreslagna web app-antalet för ASP.NET SignalR **echo**, **sändning**, och **skicka till mindre grupper**.

Prestandatest använder Azure Web App av [Standard planera S3](https://azure.microsoft.com/pricing/details/app-service/windows/) för ASP.NET SignalR.

- `echo`

|   echo           | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar      | 1,000 | 2,000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Antal för App-servrar | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

- `broadcast`

|  Sändning       | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar      | 1,000 | 2,000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Antal för App-servrar | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

- `Send-to-small-group`

|  Skicka till liten grupp     | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar      | 1,000 | 2,000 | 5 000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Antal för App-servrar | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

## <a name="serverless-mode"></a>Serverlös läge

Klienter och ASRS ingår i det här läget. Varje klient står för en enda anslutning. Klienten skickar meddelanden via REST-API till en annan klient- eller broadcast-meddelanden till alla.

Skicka Högdensitet meddelanden via REST-API är inte lika effektiva som WebSocket, eftersom det krävs för att skapa en ny HTTP-anslutning varje gång - någon extra kostnad i serverlös läge.

### <a name="broadcast-through-rest-api"></a>Skicka via REST-API
Alla klienter upprätta WebSocket-anslutningar med ASRS. Vissa klienter starta broadcasting via REST-API. Skicka (inkommande) meddelanden är via HTTP Post som inte är effektiv jämfört med WebSocket.

|   Skicka via REST-API     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Anslutningar               | 1,000 | 2,000 | 5 000  | 10 000 | 20,000 | 50,000  | 100 000 |
| Inkommande (meddelanden/s)  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Utgående (meddelanden/s) | 2,000 | 4,000 | 10 000 | 20,000 | 40,000 | 100 000 | 200 000 |
| Inkommande bandbredd (byte/s)  | 4K    | 4K    | 4K     | 4K     | 4K     | 4K      | 4K      |
| Utgående bandbredd (byte/s) | 4M    | 8M    | 20M    | 40M    | 80M    | 200M    | 400M    |

### <a name="send-to-user-through-rest-api"></a>Skicka till användaren via REST-API
Benchmark tilldelar användarnamn till alla klienter innan de börjar ansluter till ASRS. När klienterna upprättats WebSocket-anslutningar med ASRS kan börja de skicka meddelanden till andra via HTTP Post.

|   Skicka till användaren via REST-API | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Anslutningar               | 1,000 | 2,000 | 5 000  | 10 000 | 20,000 | 50,000  | 100 000 |
| Inkommande (meddelanden/s)  | 300   | 600   | 900    | 1,300  | 2,000  | 10 000  | 18,000  |
| Utgående (meddelanden/s) | 300   | 600   | 900    | 1,300  | 2,000  | 10 000  | 18,000 |
| Inkommande bandbredd (byte/s)  | 600 K  | 1.2 M  | 1.8 M   | 2.6 M   | 4M     | 10M     | 36M    |
| Utgående bandbredd (byte/s) | 600 K  | 1.2 M  | 1.8 M   | 2.6 M   | 4M     | 10M     | 36M    |

## <a name="performance-test-environments"></a>Prestanda-och testmiljöer

Prestandatest för alla användningsfall som anges ovan har utförts i Azure-miljö. Virtuella datorer som används i de flesta 50 klienten virtuella datorer och 20 app-servern.

Klienten VM-storlek: StandardDS2V2 (2 virtuella processorer, 7G minne)

AppServer VM-storlek: StandardF4sV2 (4 vCPU, 8G minne)

Azure SignalR SDK serveranslutningar: 15

## <a name="performance-tools"></a>Prestandaverktyg

https://github.com/Azure/azure-signalr-bench/tree/master/SignalRServiceBenchmarkPlugin

## <a name="next-steps"></a>Nästa steg

I den här artikeln kan du få en översikt över SignalR Service prestanda i vanliga användningsfall.

Om du vill visa mer information om systemarkitekturen SignalR Service och skalning för SignalR Service kan du läsa enligt följande anvisningar.

* [Azure SignalR Service Internals](signalr-concept-internals.md)
* [Azure SignalR Service-skalning](signalr-howto-scale-multi-instances.md)