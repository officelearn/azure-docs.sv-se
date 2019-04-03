---
title: TCP/IP prestandajustering för virtuella Azure-datorer | Microsoft Docs
description: Lär dig olika vanliga TCP/IP prestanda justering tekniker samt deras relation till virtuella Azure-datorer.
services: virtual-network
documentationcenter: na
author:
- rimayber
- dgoddard
- stegag
- steveesp
- minale
- btalb
- prachank
manager: paragk
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/30/2019
ms.author:
- rimayber
- dgoddard
- stegag
- steveesp
- minale
- btalb
- prachank
ms.openlocfilehash: c5d4f67e9c1e4e983133675c440b8c5b64183227
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2019
ms.locfileid: "58852017"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>TCP/IP-prestandajustering för virtuella Azure-datorer

Syftet med den här artikeln är att diskutera vanliga justering tekniker för TCP/IP-prestanda och deras överväganden för virtuella datorer som körs på Microsoft Azure. Det är viktigt att du först har en grundläggande förståelse av begrepp och sedan diskutera hur de kan ställa in.

## <a name="common-tcpip-tuning-techniques"></a>Vanliga tekniker för justering av TCP/IP

### <a name="mtu-fragmentation-and-large-send-offload-lso"></a>MTU fragmentering och stora skicka avlasta (LSO)

#### <a name="explanation-of-mtu"></a>Förklaring av MTU

Den högsta överföringsenheten (MTU) är den största storlek ramen (paket), särskilt i byte som kan skickas via ett nätverksgränssnitt. MTU är en konfigurerbar inställning och standardvärdet används för MTU på Azure Virtual Machines och standardinställningen på de flesta nätverksenheter globalt, är 1 500 byte.

#### <a name="explanation-of-fragmentation"></a>Förklaring av fragmentering

Fragmentering inträffar när ett paket har skickats som överskrider MTU för ett nätverksgränssnitt. TCP/IP-stack bryter paketet i mindre delar (fragment) som överensstämmer med gränssnitt som MTU. Fragmentering sker på IP-nivå och är oberoende av det underliggande protokollet (till exempel TCP). När en 2000-byte-paketet skickas via ett nätverksgränssnitt med en MTU på 1500, ska sedan den delas upp i ett paket för 1 500 byte och ett 500 byte-paket.

Nätverksenheter mellan en käll- och har möjlighet att ta bort paket som överstiger MTU eller fragmentera paketet i mindre delar.

#### <a name="the-dont-fragment-df-bit-in-an-ip-packet"></a>”Inte Fragment (DF)” biten i ett IP-paket

Biten Fragmentera är en flagga i rubriken för IP-protokollet. Om DF-biten är aktiverad, betyder det att mellanliggande nätverksenheter på sökvägen mellan avsändaren och mottagaren inte måste fragmentera paketet. Det finns många orsaker till varför den här biten kan vara aktiverad (se sökväg identifiering avsnittet nedan finns ett exempel). När en enhet tar emot ett paket med biten Fragmentera inte inställd och paketets överskrider gränssnittet enheter MTU, så normalt beteende är för enheten för att ta bort paketet och skicka ett ”ICMP fragmentering behövs”-paket tillbaka till den ursprungliga källan för den paket.

#### <a name="performance-implications-of-fragmentation"></a>Konsekvenser för prestanda för fragmentering

Fragmentering kan ha negativa prestanda. En av de viktigaste skälen för prestandapåverkan är processor/minne effekten av fragmentering och sammansättning av paket. När en nätverksenhet behöver Fragmentera ett paket, har det att allokera processor/minne resurser för att utföra fragmentering. Samma måste inträffa när paketet att återskapas. Nätverksenheten måste lagra alla fragment tills de tas emot så att den kan sätta ihop dem till det ursprungliga paketet. Den här processen för fragmentering/återsammansättning kan också orsaka fördröjning på grund av fragmentering/återsammansättning-processen.

Andra möjliga sämre prestanda konsekvenserna av fragmentering är att fragmenterade paket som anländer i fel ordning. Oordnade paket kan orsaka vissa typer av nätverksenheter att släppa oordnade paket - som kommer då att kräva hela paketet skickas igen. Några vanliga scenarier för att släppa fragment är säkerhetsenheter som nätverksbrandväggar eller när en enhet är att ta emot buffertar tömts. När en enhet är att ta emot buffertar tömts, en nätverksenhet försöker att sätta ihop ett fragmenterade paket men som saknar resurser för att lagra och reassume paketet.

Fragmentering kan uppfattas som en negativ igen, men stöd för fragmentering är nödvändiga för att ansluta olika nätverk via Internet.

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>Fördelar och konsekvenserna av att ändra MTU

Som en allmän instruktion skapa ökar MTU ett mer effektivt nätverk. Varje paket som överförs har ytterligare information om läggs till i det ursprungliga paketet. Flera paket innebär mer rubrik overhead och nätverket är mindre effektivt som ett resultat.

Ethernet-huvudstorlek är till exempel 14 byte plus en 4-bytes ramens Kontrollera sekvens (FCS) att garantera konsekvens för ramen. Om det skickas ett 2000 byte-paket, läggs 18 byte av Ethernet-omkostnader i nätverket. Om paketet är fragmenterat i ett paket för 1 500 byte och ett paket med 500 byte, får varje paket 18 byte av Ethernet-huvud- eller 36 byte. Medan ett enda 2000-byte-paket har bara ett Ethernet-huvud med 18 byte.

Det är viktigt att notera att öka MTU i sig inte nödvändigtvis skapar ett mer effektivt nätverk. Om ett program skickar endast paket med 500 byte, kommer samma sidhuvudet finns om MTU är 1 500 byte eller 9000 byte. För nätverket ska vara mer effektivt att, sedan det måste också använda större paketstorlek som är i förhållande till MTU.

#### <a name="azure-and-vm-mtu"></a>Azure och VM-MTU

Standardstorleken för virtuella Azure-datorer är 1 500 byte. Azure Virtual Network-stack försöker Fragmentera ett paket på 1400 byte. Men kan i Azure Virtual Network-stacken paket upp till 2006 byte när ”” biten Fragmentera anges i IP-huvudet.

Det är viktigt att notera att den här fragmentering inte en Azure Virtual Network-stacken är sin natur ineffektiv eftersom den fragmenterar paket på 1400 byte när virtuella datorer har en MTU på 1500. Det är att en stor del av nätverkspaket är mycket mindre än 1 400 byte eller 1 500 byte.

#### <a name="azure-and-fragmentation"></a>Azure och fragmentering

Azures virtuella nätverksstacken är idag konfigurerad för att ta bort ”Out of ordning fragment” - vilket innebär att fragmenterade paket som inte tas emot i sin ursprungliga fragmenterade ordning. Främst på grund av ett säkerhetsproblem i nätverket tillkännagav i November 2018 kallas FragmentStack ignoreras dessa paket.

FragmentSmack är ett fel i hanteringen av sammansättning av fragmenterade IPv4 och IPv6-paket i Linux-kernel. En angripare kan använda felet till utlösaren dyra fragment sammansättning åtgärder, som leder till ökad processor- och DOS-attacker på målsystemet.

#### <a name="tune-the-mtu"></a>Finjustera MTU

Virtuella Azure-datorer stöder en konfigurerbar MTU precis som alla andra operativsystem. Fragmentering som inträffar inom Azure och som beskrivs ovan, bör dock övervägas när du konfigurerar MTU.

Azure uppmuntra inte kunderna att öka deras VM MTU. Den här diskussionen är avsedd att förklarar i detalj hur Azure implementerar MTU och utför fragmentering idag.

> [!IMPORTANT]
>Öka MTU inte har visat för att förbättra prestanda och kan ha en negativ inverkan på programmets prestanda.
>
>

#### <a name="large-send-offload-lso"></a>Avlastning för stor (LSO)

Stora skicka avlasta (LSO) kan förbättra nätverkets prestanda genom att avlasta segmentering av paket till Ethernet-adaptern. Med LSO aktiverat, TCP/IP-stack skapar ett stort TCP-paket och sedan skicka till Ethernet-adaptern för segmentering innan den vidarebefordrar den. Fördelen med LSO är att det utan kostnad CPU från segmentera paket till paketstorlek som motsvarar MTU och avlasta bearbetningen till Ethernet-gränssnitt där det utförs i maskinvara. Mer information om fördelarna med LSO finns i [prestanda i dokumentationen för Microsoft-nätverkskortet](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso).

När LSO aktiveras, kan Azure-kunder se stora bildstorlekar när fjärrinfångade utför paket. Dessa stora bildstorlekar kan leda vissa kunder att tro att fragmentering eller en jumbo MTU som används när det inte. Med LSO, kan ethernet-adaptern annonsera ett större MSS till TCP/IP-stacken för att skapa ett större TCP-paket. Den här hela icke-segmenterade ramen sedan vidarebefordras till Ethernet-adaptern och var tillgängliga i ett infångat som utfördes på den virtuella datorn. Dock ska paketet delas upp i många mindre ramar av Ethernet-kort enligt Ethernet-adaptern MTU.

### <a name="tcpmss-window-scaling-and-pmtud"></a>TCP/MSS fönstret skalning och PMTUD

#### <a name="explanation-of-tcp-mss"></a>Förklaring av TCP-MSS

TCP maximala storlek MSS (Segment) är en inställning som är avsedd att ställa in maxstorleken för TCP-segment undviks fragmentering av TCP-paket. Operativsystem vanligtvis anger MSS som MSS = MTU - IP- & TCP-huvudet storlek (20 byte eller 40 byte totalt). Så har ett gränssnitt med en MTU 1500 en MSS av 1 460. MSS, men kan konfigureras.

Den här inställningen är accepterat i 3-vägs-handskakning TCP när en TCP-session har konfigurerats mellan en källa och mål. Båda sidor skicka ett MSS-värde och lägre av två används för TCP-anslutning.

Mellanliggande nätverksenheter, t.ex. VPN-gatewayer, inklusive Azure VPN Gateway, möjlighet att justera MTU oberoende av källa och mål för att säkerställa optimal nätverkets prestanda. Det bör noteras att källans och målets enbart MTU inte är de enda faktorerna i det faktiska MSS-värdet.

#### <a name="explanation-of-path-mtu-discovery-pmtud"></a>Förklaring av identifiering av sökvägen MTU (PMTUD)

Medan MSS förhandlas betyda det inte faktiska MSS som kan användas som andra nätverksenheter i sökvägen mellan källan och målet kan ha ett lägre värde för MTU än källan och målet. I det här fallet kommer enheten vars MTU är mindre än paketet släppa paketet och skicka tillbaka meddelandet ICMP Internet Control Message Protocol () fragmentering behövs (typ 3, 4 kod) som innehåller dess MTU. Det här ICMP-meddelandet kan källvärden att minska dess sökvägs-MTU på rätt sätt. Processen kallas identifiering av sökvägs-MTU.

Processen för PMTUD är sin natur ineffektiv och har konsekvenser på nätverkets prestanda. När det skickas paket som överskrider nätverkssökvägar MTU, måste dessa paket skickas igen med en lägre MSS. Om avsändaren inte får ICMP fragmentering behövs paketet, kanske på grund av en nätverksbrandvägg i sökvägen (vanligtvis kallas PMTUD svartlistade), och sedan avsändaren inte vet som behövs för att sänka MSS och kommer kontinuerligt snabbåterställningen paketet. Därför rekommenderar vi inte öka Azure VM-MTU.

#### <a name="vpn-considerations-with-mtu"></a>VPN-överväganden med MTU

Kunder som använder virtuella datorer som utför inkapsling (till exempel IPSec-VPN) kan ha ytterligare effekter paketstorlek och MTU. VPN: lägga till ytterligare rubriker läggs till det ursprungliga paketet därför ökar storleken på nätverkspaket och som kräver en mindre MSS.

Aktuella rekommendationen för Azure är att ange TCP-MSS-ihopfogning 1350 byte och tunnelgränssnittet MTU med 1400. Mer information finns på den [VPN-enheter och IPSec/IKE-parametrar sidan](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>Svarstid, tidsfördröjningen och skalning av TCP-fönster

#### <a name="latency-and-round-trip-time"></a>Svarstid och tidszonsbevarande tid

Nätverksfördröjningen regleras av hastigheten för ljus över ett fiberoptiska fiber-nätverk. Är, genomflödet för TCP är också effektivt styrda (praktiska maximum) grund av av tidszonsbevarande tid (Retur) mellan två nätverksenheter.

| | | | |
|-|-|-|-|
|Routa|Avstånd|Enkelriktad tid|Fram och åter tid|
|New York till San Francisco|4,148 km|21 ms|42 ms|
|New York to London|5,585 km|28 ms|56 ms|
|New York till Sydney|15,993 km|80 ms|160 ms|

Den här tabellen visas linjär avståndet mellan två platser, i nätverk, avståndet är dock längre tid än linjär avståndet. En enkel formel för att beräkna minsta RTT som styrs av hastigheten för ljus är: minsta RTT = 2 * (avstånd kilometer / snabbare för spridning).

Ett standardvärde på 200 kan användas för hastigheten på spridning - värdet är avståndet i mätare ljus färdas 1 millisekund.

I exemplet New York till San Francisco är det 4,148 km linjär avstånd. Minimum RTT = 2 * (4,148 / 20). Resultatet av formeln kommer att visas i millisekunder.

Som det fysiska avståndet mellan två platser är en fast verklighet om högsta möjliga nätverksprestanda krävs, är det mest logiska alternativet att välja mål med minsta avståndet mellan dem. Sekundärt, kan du göra designbeslut i det virtuella nätverket för att optimera sökvägen till trafik och minska svarstiden. Dessa överväganden för virtuellt nätverk finns i avsnittet nätverksdesign nedan.

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>Svarstid och tidszonsbevarande tid effekterna på TCP

Tur och RETUR har tid en direkt inverkan på maximalt dataflöde för TCP. TCP-protokollet har ett koncept för fönsterstorlek. Tidsperioden är den maximala mängden trafik som kan skickas via en TCP-anslutning innan avsändaren måste få godkännande från mottagaren. Om TCP-MSS har angetts till 1 460 och TCP-fönsterstorlek anges till 65535 kan avsändaren skicka 45 paket innan den måste få godkännande från mottagaren. Om bekräftelse inte tas emot kommer avsändaren snabbåterställningen. I det här exemplet, TCP-fönsterstorlek / TCP MSS = paket som skickats. Eller 65535 / 1 460 avrundas upp till 45.

Det här tillståndet ”väntar på bekräftelse”, är som en mekanism för att skapa en pålitlig leverans av data, vad som orsakar RTT att påverka TCP dataflöde effektivt. Ju längre avsändaren väntar på bekräftelse, desto längre tid också måste vänta innan du skickar mer data.

Formel för att beräkna maximalt dataflöde på en enda TCP-anslutning är följande: Fönsterstorlek / (RTT svarstid i millisekunder / 1000) = högsta byte per sekund. Tabellen nedan är formaterad i megabyte för läsbarhet och visar den maximala megabyte / per sekund dataflöde i en enda TCP-anslutning.

| | | | |
|-|-|-|-|
|TCP-fönsterstorlek i byte|RTT Latency<br/>i millisekunder|Maximal<br/>MB per sekund dataflöde|Maximal<br/> Megabit per sekund dataflöde|
|65535|1|65.54|524.29|
|65535|30|2.18|17.48|
|65535|60|1.09|8.74|
|65535|90|.73|5.83|
|65535|120|.55|4.37|

Om det finns några paketförluster, kommer det minska maximalt dataflöde för en TCP-anslutning när avsändaren återöverför data som den redan har skickat.

#### <a name="explanation-of-tcp-window-scaling"></a>Förklaring av skalning av TCP-fönster

TCP Window Scaling är ett begrepp som dynamiskt ökar TCP-fönsterstorleken så att mer data som ska skickas innan en bekräftelse krävs. I vårt tidigare exempel skulle 45 paket skickas innan en bekräftelse krävdes. Om antalet paket som skickas innan en bekräftelse ökas sedan TCP maximalt dataflöde också ökas genom att minska antalet gånger som en avsändare väntar på bekräftelse.

TCP dataflödet visar en enkel tabell nedan:

| | | | |
|-|-|-|-|
|TCP-fönsterstorlek<br/>i byte|RTT svarstid i millisekunder|Maximal<br/>MB per sekund dataflöde|Maximal<br/> Megabit per sekund dataflöde|
|65535|30|2.18|17.48|
|131,070|30|4.37|34.95|
|262,140|30|8.74|69.91|
|524,280|30|17.48|139.81|

TCP-huvudets värde för TCP-fönsterstorlek är dock endast 2 byte långt, vilket innebär att det maximala värdet för en receive-fönstret är 65535. För att öka den maximala fönsterstorleken introducerades en skalningsfaktor för TCP-fönster.

Skalningsfaktorn är också en inställning som kan konfigureras i ett operativsystem. Formel för beräkning av TCP-fönsterstorlek skalningsfaktor är följande: TCP-fönsterstorlek = TCP-fönsterstorlek i byte \* (2 ^ skala faktor). Om fönstret skalningsfaktorn är 3 och fönsterstorleken för 65535, är beräkningen följande: 65535 \* (2 ^ 3) = 262,140 byte. Skala upp till 14 resulterar i en TCP-fönsterstorlek av 14 (den maximala förskjutningen tillåts) och TCP-fönsterstorlek blir 1,073,725,440 byte (8,5 Gigabit).

#### <a name="support-for-tcp-window-scaling"></a>Stöd för skalning av TCP-fönster

Windows har möjlighet att ange olika faktorer som skalning på en per typ av anslutning – det finns flera typer av anslutningar (datacenter, internet och så vidare). Du kan se fönstret skalning anslutning klassificering med powershell-kommandot Get-NetTCPConnection.

```powershell
Get-NetTCPConnection
```

Du kan se värdena för varje klass med powershell-kommandot Get-NetTCPSetting.

```powershell
Get-NetTCPSetting
```

Inledande TCP-fönsterstorlek och TCP skalning faktor kan ställas in i Windows via powershell-kommandot Set-NetTCPSetting. Mer information finns på den [Set-NetTCPSetting sidan](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps)

```powershell
Set-NetTCPSetting
```

De effektiva inställningarna för TCP-AutoTuningLevel är som följer.

| | | | |
|-|-|-|-|
|AutoTuningLevel|Skalningsfaktor|Skala multiplikatorn|Formel för att<br/>Beräkna maximal fönsterstorlek|
|Disabled|Ingen|Ingen|Fönsterstorlek|
|Begränsat|4|2^4|Fönsterstorlek * (2 ^ 4)|
|Mycket begränsad|2|2^2|Fönsterstorlek * (2 ^ 2)|
|Normal|8|2^8|Fönsterstorlek * (2 ^ 8)|
|Experimentell|14|2^14|Fönsterstorlek * (2 ^ 14)|

De här inställningarna är det mest troligt att påverka prestanda för TCP, bör det noteras att många andra faktorer via Internet, utanför kontrollen av Azure, kan också påverka prestanda för TCP.

#### <a name="increase-mtu-size"></a>Öka storleken på MTU

En logisk fråga be är ”ökar MTU ökar TCP-prestanda som en större MTU innebär en större MSS”? Enkel svaret är – troligen inte. Som beskrivs kan finns det för- och nackdelar till paketstorlek som gäller utöver bara TCP-trafik. Som beskrivs ovan, de viktigaste faktorerna som påverkar är TCP dataflödesprestanda TCP-fönsterstorlek, paketförlust och RTT.

> [!IMPORTANT]
> Azure rekommenderar inte att Azure-kunder ändrar standardvärdet för MTU på virtuella datorer.
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>Accelererat nätverk och skalning på mottagarsidan

#### <a name="accelerated-networking"></a>Accelererat nätverk

Nätverksfunktioner för virtuell dator har historiskt Processorintensiva i både virtuell gästdator och Hypervisor-/ värden. Varje paket som eltransit via värden bearbetas i programvara av värdens CPU -, inklusive alla de virtuella nätverk-inkapsling/Tyskland-capsulation. Därför mer trafik som passerar värden och desto högre CPU-belastningen. Och om värdens CPU är upptaget med andra åtgärder, sedan som påverkar också nätverkets dataflöde och svarstid. Det här problemet åtgärdats via Accelererat nätverk.

Accelererat nätverk ger konsekvent extremt låg nätverksfördröjning via Azures interna programmerbart maskin- och tekniker som SR-IOV. Genom att flytta stor del av Azures programdefinierade nätverksstack av processorerna och i FPGA-baserat SmartNICs compute cykler har frigjorts av program för slutanvändare, placera mindre belastning på den virtuella datorn, minskar jitter och inkonsekvens i svarstid. Prestanda kan med andra ord vara mer deterministisk.

Accelererat nätverk ger prestandaförbättringar genom att tillåta att den Virtuella gästdatorn att kringgå värden och upprätta en datapath direkt med en värds SmartNIC. Fördelarna med Accelererat nätverk är:

- **Lägre latens / högre paket per sekund (pps)**: Ta bort den virtuella växeln från datapath tar bort paket tid i värden för behandling av princip för och ökar antalet paket som kan bearbetas i den virtuella datorn.

- **Minskar jitter**: Virtuell växel bearbetning beror på mängden principinformation som måste installeras och arbetsbelastningen processorkraft som klarar bearbetningen. Avlastning av principtillämpning till maskinvara tar bort den variationen genom att tillhandahålla paket direkt till den virtuella datorn, ta bort värden till VM-kommunikation och all programvara avbrott och kontext växlar.

- **Minskar CPU-belastning**: Kringgår den virtuella växeln på värden leder till färre CPU-belastningen för bearbetning av nätverkstrafik.

Accelererat nätverk måste uttryckligen aktiveras på basis av per virtuell dator. Instruktioner för att aktivera Accelererat nätverk på en virtuell dator är tillgänglig på den [skapa en Linux-dator med Accelererat nätverk sidan](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).

#### <a name="receive-side-scaling-rss"></a>Skalning på mottagarsidan (RSS)

Skalning på mottagarsidan är en nätverksteknik för drivrutinen som distribuerar mottagande av nätverkstrafik effektivare genom att distribuera får bearbetning över flera processorer i ett system med flera processorer. Enkelt uttryckt innebär medför RSS att ett system att bearbeta en större mängd mottagna trafik eftersom den använder alla tillgängliga processorer i stället för bara en. En mer teknisk diskussion om RSS kan hittas på den [introduktion till Receive Side Scaling sidan](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling).

RSS krävs för att uppnå högsta prestanda vid Accelererat nätverk är aktiverat på en virtuell dator. Det kan också finnas fördelar med RSS på virtuella datorer som inte har aktiverat accelererat nätverk. En översikt över hur du avgör om RSS är aktiverat och konfiguration för att aktivera den finns på den [optimera nätverkets dataflöde för Azure-datorer sida](http://aka.ms/FastVM).

### <a name="tcp-time-wait-and-time-wait-assassination"></a>Vänta Assassination TCP väntetid och tid

Ett annat vanligt problem som påverkar nätverket och programmets prestanda är TCP tid vänta-inställning. På upptagna virtuella datorer som är öppna och stänga många sockets, antingen som en klient eller server (källport IP:Source + IP:Destination målport) under normal drift av TCP, kan en viss socket hamnar i vänteläge tid för en betydande tidsperiod. Det här tillståndet ”väntar du tid”, är avsedd att tillåta ytterligare data som ska levereras på en socket innan du stänger den. TCP/IP-stackar förhindra därför vanligtvis återanvändning av en socket genom att släppa tyst TCP SYN-paket för klienter.

Den här lång tid en socket är i vänteläge konfigureras men kan vara allt från 30 sekunder till 240 sekunder. Sockets är en begränsad resurs och antalet sockets som kan användas vid en given tidpunkt kan konfigureras (hur många ligger vanligtvis cirka 30 000 potentiella sockets). Om det här talet är slut, eller klienter och servrar har felaktigt tidsinställningar för vänta och en virtuell dator försöker att återanvända en socket i vänteläge tid, misslyckas nya anslutningar som ignoreras tyst TCP SYN-paket.

Värdet för portintervall för utgående sockets, samt TCP tid vänta inställningar och socket återanvändning kan vanligtvis konfigureras i TCP/IP-Stack med ett operativsystem. Ändra dessa siffror kan förbättra skalbarhet, men beroende på scenario, kan införa samverkansproblem och bör ändras med försiktighet.

En funktion som heter tid vänta Assassination introducerades för att lösa det skalning begränsning. Tid vänta Assassination kan en socket återanvänds under vissa scenarier när det numret i IP-paket för den nya anslutningen överskrider sekvensnumret för senaste paketet från den tidigare anslutningen. I det här fallet operativsystemet gör att den nya anslutningen ska upprättas (acceptera nya SYN ACK) och kraft att Stäng den tidigare anslutningen som har tidpunkt väntar tillstånd. Den här funktionen stöds på Windows virtuella datorer i Azure idag och stöd i andra virtuella datorer bör undersökas av Azure-kunder med respektive OS-leverantörer.

Dokumentation om hur du konfigurerar inställningar för TCP tid vänta och portintervallet för källan är tillgänglig på den [inställningar som kan ändras för att förbättra nätverksprestanda sidan](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance).

## <a name="virtual-network-factors-that-can-affect-performance"></a>Virtuella nätverk faktorer som kan påverka prestanda

### <a name="vm-maximum-outbound-throughput"></a>Maximalt utgående VM-dataflöde

Azure erbjuder en mängd olika storlekar på Virtuella datorer och typer, var och en med en blandning av prestandafunktioner. En sådan prestandafunktion är nätverket dataflöde (eller bandbredd), mätt i megabit per sekund (Mbps). Eftersom virtuella datorer finns på delad maskinvara, måste nätverkskapacitet delas ganska av de virtuella datorer som delar samma maskinvara. Större virtuella datorer allokeras relativt mer bandbredd än mindre virtuella datorer.

Den nätverksbandbredd som allokeras till varje virtuell dator är debiteras baserat på utgående (utgående) trafik från den virtuella datorn. All nätverkstrafik som lämnar den virtuella datorn räknas mot den allokerade begränsning, oavsett mål. Till exempel gäller en virtuell dator har en gräns på 1 000 Mbit/s, denna gräns om utgående trafik är avsedd för en annan virtuell dator i samma virtuella nätverk eller utanför Azure.
Inkommande mäts inte eller begränsad direkt. Det finns dock andra faktorer, till exempel processor- och gränser som kan påverka en virtuell dator möjligheten att ta emot inkommande data.

Snabbare nätverksfunktioner är en funktion som utformats för att förbättra nätverksprestanda, inklusive svarstid, dataflöde och CPU-användning. Medan Accelererat nätverk kan du förbättra dataflödet för en virtuell dator, kan det göra det bara upp till den virtuella datorn allokerade bandbredd.

Azure-datorer måste ha en, men kan ha flera, nätverksgränssnitt som är anslutna till dem. Bandbredden som allokeras till en virtuell dator är summan av all utgående trafik för alla nätverksgränssnitt som är kopplade till en virtuell dator. Med andra ord är tilldelad bandbredd per virtuell dator, oavsett hur många nätverksgränssnitt är kopplade till den virtuella datorn.
 
Förväntade utgående dataflöde och antalet nätverksgränssnitt som stöds av varje VM-storlek beskrivs här. Om du vill se maximalt dataflöde, Välj en typ, till exempel generell användning, och välj sedan en storlek-serien på den resulterande sidan, t.ex Dv2-serien. Varje serie har en tabell med nätverk specifikationer i den sista kolumnen med rubriken maximalt antal nätverkskort / förväntade nätverksprestanda (Mbit/s).

Dataflöde gränsen gäller för den virtuella datorn. Dataflödet påverkas inte av följande faktorer:

- **Antalet nätverksgränssnitt**: Bandbreddsbegränsningen beräknas kumulativt för all utgående trafik från den virtuella datorn.

- **Accelerated networking**: Även om funktionen kan vara användbart för att uppnå den publicerade gränsen, ändras inte gränsen.

- **Trafikmål**: Alla mål räknas utgående.

- **Protokoll**: All utgående trafik över alla protokoll räknas mot gränsen.

En [tabell med maximal bandbredd per typ av virtuell dator kan hittas genom att gå till den här sidan](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) och klicka på respektive typ av virtuell dator. Varje typ på sidan visas en tabell maximala nätverkskort och förväntade nätverkets maximala bandbredd.

Mer information om Virtuella nätverkets bandbredd kan hittas på [VM nätverksbandbredd](http://aka.ms/AzureBandwidth).

### <a name="internet-performance-considerations"></a>Prestandaöverväganden för Internet

Som beskrivs i den här artikeln kan kan faktorer på Internet och utanför kontrollen av Azure påverka nätverkets prestanda. Dessa faktorer är:

- **Svarstid**: Mäts tiden mellan två mål kan påverkas av problem i mellanliggande nätverk, trafik som inte tar ”kortaste” avståndet sökväg möjligt och icke-optimal peeringsökvägar

- **Paketförlust**: Paketförlust kan orsakas av överbelastning på nätverket, fysisk sökväg problem och under utför nätverksenheter

- **MTU storlek/fragmentering**: Fragmentering längs vägen kan leda till fördröjningar i data ankomst eller paket som inkommer i fel ordning, vilket kan påverka leveransen av paket

Traceroute är ett bra verktyg för att mäta prestanda Nätverksegenskaper (till exempel paketförlust och fördröjning) med varje nätverkssökvägen mellan en käll- och enhet.

### <a name="network-design-considerations"></a>Nätverksdesign

Topologin för ett virtuellt nätverk kan påverka prestanda för virtuella nätverk tillsammans med ovanstående överväganden. Till exempel en nav och ekrar design att backhauls trafik globalt till en enda virtuella navnätverket kommer introducera svarstid för nätverk och därför i kraft nätverksprestanda. Antal nätverksenheter som nätverkstrafik skickas via kan på liknande sätt kan också påverka Total svarstid. Till exempel i en nav och ekrar design, kan om trafiken passerar genom en eker nätverks virtuella installation och en virtuell installation Hub innan transit till Internet, sedan svarstid uppstå med virtuella nätverksinstallationer.

### <a name="azure-regions-virtual-networks-and-latency"></a>Azure-regioner, virtuella nätverk och svarstid

Azure-regioner består av flera datacenter som finns inom ett visst geografiskt område. Dessa Datacenter kanske inte fysiskt bredvid varandra och i vissa fall kan avgränsas med så mycket som 10 kilometer. Det virtuella nätverket är ett logiskt överlägg ovanpå Azures fysiska datacenternätverk och ett virtuellt nätverk innebär inte några specifika nätverkets topologi i datacenter. Till exempel VM A och B VM finns i samma virtuella nätverk och undernät, men kan finnas i olika rack, rader eller även datacenter. De kan avgränsas med fot av fiberoptisk kabel eller kilometer av fiberoptisk kabel. Den här verklighet kan införa variabel svarstid (några millisekunder förändringen) mellan olika virtuella datorer.

Den här geografisk placering och därmed fördröjning mellan två virtuella datorer kan påverkas med konfigurationen av Tillgänglighetsuppsättningar och Tillgänglighetszoner, men avståndet mellan datacenter i en region är regionspecifika och främst påverkas av Datacenter-topologi i regionen.

### <a name="source-nat-port-exhaustion"></a>Portöverbelastning NAT för källa

En distribution i Azure kan kommunicera med slutpunkter utanför Azure i det offentliga Internet och/eller den offentliga IP-adressutrymmet. När en instans initierar utgående anslutningen, matchar Azure dynamiskt den privata IP-adressen till en offentlig IP-adress. När du har skapat den här mappningen kan Returtrafiken för det här utgående Start flödet också nå privata IP-adress som flödet kom från.

För varje utgående anslutning måste Azure Load Balancer ha den här mappningen för en viss tidsperiod. Med flera innehavare natur Azure, kan det vara resurskrävande att underhålla den här mappningen för varje utgående flöde för varje virtuell dator. Det finns därför gränser som anges och baserat på konfigurationen av Azure Virtual Network. Eller anges mer exakt – en Azure-dator kan endast se ett visst antal utgående anslutningar vid en given tidpunkt. När dessa gränser är uttömd, kommer Azure VM förhindras från att göra några ytterligare utgående anslutningar.

Det här beteendet kan dock konfigureras. Mer information om [SNAT och SNAT port överbelastning], se [i den här artikeln](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections).

## <a name="measure-network-performance-on-azure"></a>Måttet nätverksprestanda på Azure

Ett antal prestanda maximum i den här artikeln är relaterade till Nätverksfördröjningen / fram och åter tid mellan två virtuella datorer. Det här avsnittet innehåller några förslag på hur du testar svarstid/RTT, samt TCP-prestanda och VM-nätverksprestanda. TCP/IP och värden som beskrivs ovan kan ställa in och prestanda testas med hjälp av de metoder som beskrivs nedan. Värdena för svarstid, MTU, MSS och fönsterstorleken kan användas i beräkningar som anges ovan och teoretiska maximum kan jämföras med faktiska värden som observerats under testningen.

### <a name="measure-round-trip-time-and-packet-loss"></a>Tidsfördröjningen för mått och paketförlust

TCP-prestanda bygger kraftigt på RTT och paketförlust. Det enklaste sättet att mäta RTT och paketförlust använder ping-verktyget finns i Windows och Linux. Minsta/högsta/genomsnittlig svarstid mellan en källa och mål samt paketförlust visar resultatet av ping. Ping använder ICMP-protokollet som standard. Om du vill testa TCP RTT kan sedan PsPing användas. Mer information om PsPing finns på [den här länken](https://docs.microsoft.com/sysinternals/downloads/psping).

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>Måttet faktiska dataflödet för en TCP-anslutning

NTttcp är ett verktyg som används för att testa TCP-prestanda för en Linux eller Windows-VM. Olika TCP-inställningar kan tweaked och fördelarna testats med NTttcp. Mer information om NTttcp finns på länkarna nedan.

- [Bandbredd/dataflöde testning (NTttcp)](https://aka.ms/TestNetworkThroughput)

- [NTttcp-verktyget](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>Måttet faktiska bandbredd för en virtuell dator

Prestandatestning för olika typer av virtuella datorer, Accelererat nätverk och så vidare, kan testas med ett verktyg som kallas Iperf, även tillgängliga på Linux och Windows. Iperf kan använda TCP eller UDP för att testa den övergripande nätverkets genomflöde. TCP dataflöde testerna via Iperf påverkas av de faktorer som beskrivs i den här artikeln (svarstid, RTT och så vidare). Därför kan UDP ge bättre resultat för att bara testa maximalt dataflöde.

Mer information finns nedan:

- [Felsökning av nätverksprestanda för Expressroute](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [Verifiera VPN-dataflöde till ett virtuellt nätverk](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet)

### <a name="detect-inefficient-tcp-behaviors"></a>Identifiera ineffektiv TCP-beteenden

Azure-kunder kan se TCP-paket med TCP-flaggor (SÄCK, Duplicering ACK, SÄNDNINGSFÖRSÖK och snabb SNABBÅTERSTÄLLNINGEN) i infångade paket som kan tyda på problem med nätverksprestanda. Dessa paket uttryckligen har angett nätverk ineffektivitet till följd av paketförlust. Paketförlust är dock inte nödvändigtvis på grund av problem med Azure prestanda. Problem med prestanda kan vara resultatet av program, operativsystem eller andra problem som inte kan vara direkt relaterade till Azure-plattformen. Det är också viktigt att Observera att vissa återöverföring eller dubbla Ack i ett nätverk är normalt – TCP-protokoll har skapats för att vara tillförlitliga. Och bevis på dessa TCP-paket i ett infångat innebär inte nödvändigtvis ett systemfel nätverksproblem om de inte är hög.

Men bör det anges tydligt att dessa typer av paket är indikationer att TCP-dataflöde inte uppnår sin maximala prestanda – för orsaker som beskrevs i andra avsnitt.
