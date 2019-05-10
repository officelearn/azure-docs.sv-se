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
ms.date: 04/02/2019
ms.author:
- rimayber
- dgoddard
- stegag
- steveesp
- minale
- btalb
- prachank
ms.openlocfilehash: d0124d6656167af3942e0d054b4e1fa7a2b48e8b
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65410047"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>TCP/IP-prestandajustering för virtuella Azure-datorer

Den här artikeln beskrivs vanliga justering tekniker för TCP/IP-prestanda och vissa saker att tänka på när du använder dem för virtuella datorer som körs på Azure. Den ger en översikt över teknikerna och utforska hur de kan ställa in.

## <a name="common-tcpip-tuning-techniques"></a>Vanliga tekniker för justering av TCP/IP

### <a name="mtu-fragmentation-and-large-send-offload"></a>MTU fragmentering och avlastning för stor

#### <a name="mtu"></a>MTU

Den högsta överföringsenheten (MTU) är den största storlek ramen (paket), särskilt i byte som kan skickas via ett nätverksgränssnitt. MTU är en konfigurerbar inställning. Standard MTU används på Azure Virtual Machines och standardinställningen på de flesta nätverksenheter globalt, är 1 500 byte.

#### <a name="fragmentation"></a>Fragmentering

Fragmentering inträffar när ett paket har skickats som överskrider MTU för ett nätverksgränssnitt. TCP/IP-stack bryter paketet i mindre delar (fragment) som överensstämmer med gränssnittets MTU. Fragmentering sker på IP-nivå och är oberoende av det underliggande protokollet (till exempel TCP). När ett paket med 2 000 byte skickas via ett nätverksgränssnitt med en MTU på 1 500, ska paketet delas upp i ett 1 500 byte-paket och ett paket med 500 byte.

Nätverksenheter mellan en käll- och kan antingen ta bort paket som överskrider MTU eller fragmentera paketet i mindre delar.

#### <a name="the-dont-fragment-bit-in-an-ip-packet"></a>Fragmentera inte bit i ett IP-paket

Inte Fragment (DF)-biten är en flagga i rubriken för IP-protokollet. DF-bitars anger att nätverksenheter på sökvägen mellan avsändaren och mottagaren inte måste fragmentera paketet. Den här biten kan anges av flera orsaker. (Se avsnittet ”MTU-upptäckt” i den här artikeln finns ett exempel.) När en enhet tar emot ett paket med biten Fragmentera inte inställd och paketets överskrider enhetsgränssnitt MTU, är standard för enheten att släppa paketet. Enheten skickar meddelandet ICMP fragmentering behövs tillbaka till den ursprungliga källan för paketet.

#### <a name="performance-implications-of-fragmentation"></a>Konsekvenser för prestanda för fragmentering

Fragmentering kan ha negativa prestanda. En av de viktigaste skälen för effekten på prestanda är processor/minne effekten av fragmenteringen och sammansättning av paket. När en nätverksenhet behöver Fragmentera ett paket, har det att allokera processor/minne resurser för att utföra fragmentering.

Samma sak som händer när paketet att återskapas. Nätverksenheten måste lagra alla fragment tills de tas emot så att den kan sätta ihop dem till det ursprungliga paketet. Den här processen för fragmentering och sammansättning kan även orsaka svarstid.

Andra möjliga sämre prestanda konsekvenserna av fragmentering är att fragmenterade paket kan tas emot i fel ordning. När paket som tas emot i fel ordning, släppa vissa typer av nätverksenheter dem. När det sker så har det hela paketet skickas igen.

Fragment släpps normalt av säkerhetsenheter som nätverksbrandväggar eller när en enhet är att ta emot buffertar tömts. När en enhet är att ta emot buffertar tömts, en nätverksenhet försöker att sätta ihop ett fragmenterade paket men som saknar resurser för att lagra och reassume paketet.

Fragmentering kan ses som en negativ igen, men stöd för fragmentering är nödvändigt när du ansluter olika nätverk via internet.

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>Fördelar och konsekvenserna av att ändra MTU

Generellt sett kan du skapa ett mer effektivt nätverk genom att öka MTU. Varje paket som överförs har rubrikinformation som läggs till det ursprungliga paketet. När fragmentering skapar flera paket, det finns overhead mer rubrik och som gör att nätverket är mindre effektivt.

Här är ett exempel. Ethernet-huvudstorlek är 14 byte plus en 4-bytes ramens Kontrollera sekvens för att garantera konsekvens för ramens. Om det skickas ett 2 000 byte-paket, läggs 18 byte av Ethernet-omkostnader i nätverket. Om paketet är fragmenterat i ett paket för 1 500 byte och ett paket med 500 byte, måste varje paket 18 byte av huvudet för Ethernet, 36 byte totalt.

Tänk på att öka MTU inte nödvändigtvis att skapa ett mer effektivt nätverk. Om ett program skickar endast paket med 500 byte, kommer att finnas i samma sidhuvudet om MTU är 1 500 byte eller 9 000 byte. Nätverket blir mer effektiv endast om den använder större paketstorlek som påverkas av MTU.

#### <a name="azure-and-vm-mtu"></a>Azure och VM-MTU

Standardstorleken för virtuella Azure-datorer är 1 500 byte. Azure Virtual Network-stack försöker Fragmentera ett paket på 1,400 byte. Men den virtuella nätverksstacken tillåter paket upp till 2,006 byte när biten Fragmentera anges i IP-huvudet.

Observera att den virtuella nätverksstacken inte sin natur ineffektiv eftersom den fragmenterar paket på 1,400 byte, även om virtuella datorer har en MTU på 1 500. En stor del av nätverkspaket är mycket mindre än 1,400 eller 1 500 byte.

#### <a name="azure-and-fragmentation"></a>Azure och fragmentering

Virtuella nätverksstacken har ställts in att släppa ”oordnade meningar”, det vill säga fragmenterade paket som inte tas emot i sin ursprungliga fragmenterade ordning. Främst på grund av ett säkerhetsproblem i nätverket tillkännagav i November 2018 kallas FragmentSmack ignoreras dessa paket.

FragmentSmack är ett fel i hanteringen av sammansättning av fragmenterade IPv4 och IPv6-paket i Linux-kernel. En angripare kan använda felet till utlösaren dyra fragment sammansättning åtgärder, som kan leda till ökad processor- och DOS-attacker på målsystemet.

#### <a name="tune-the-mtu"></a>Finjustera MTU

Du kan konfigurera en Azure VM-MTU, som i något annat operativsystem. Men du bör överväga fragmentering som uppstår i Azure, enligt beskrivningen ovan, när du konfigurerar en MTU.

Vi uppmuntra inte kunderna att öka VM MTU. Den här diskussionen är avsedd att förklara hur Azure implementerar MTU och utför fragmentering.

> [!IMPORTANT]
>Öka MTU inte är kända för att förbättra prestanda och kan ha en negativ inverkan på programmets prestanda.
>
>

#### <a name="large-send-offload"></a>Avlastning för stor

Avlastning för stor (LSO) kan förbättra nätverkets prestanda genom att avlasta segmentering av paket till Ethernet-adaptern. När LSO aktiveras, TCP/IP-stack skapar ett stort TCP-paket och skickar den till Ethernet-adaptern för segmentering innan den vidarebefordrar den. Fördelen med LSO är att det utan kostnad CPU från segmentera paket till storlekar som motsvarar MTU och avlasta bearbetningen till Ethernet-gränssnitt där det utförs i maskinvara. Mer information om fördelarna med LSO finns [stöd för stora send offload](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso).

När LSO aktiveras, kan Azure-kunder se stora bildstorlekar när de utför infångade paket. Dessa stora bildstorlekar kan leda vissa kunder att tänka fragmentering sker eller att en stor MTU används när det inte. Med LSO, kan Ethernet-adaptern annonsera ett större maximal storlek (MSS) att TCP/IP-stacken för att skapa ett större TCP-paket. Den här hela icke-segmenterade ramen sedan vidarebefordras till Ethernet-adaptern och var tillgängliga i ett infångat som utfördes på den virtuella datorn. Men paketet ska delas upp i många mindre ramar av Ethernet-kort, beroende på Ethernet-adaptern MTU.

### <a name="tcp-mss-window-scaling-and-pmtud"></a>TCP-MSS fönstret skalning och PMTUD

#### <a name="tcp-maximum-segment-size"></a>TCP maximal storlek

TCP maximal storlek (MSS) är en inställning som begränsar storleken på TCP-segment, vilket innebär fragmentering av TCP-paket. Den här formeln använder vanligtvis att ställa in MSS operativsystem:

`MSS = MTU - (IP header size + TCP header size)`

IP-huvud och TCP-huvudet är 20 byte eller 40 byte totalt. Så har ett gränssnitt med en MTU på 1 500 en MSS av 1,460. Men MSS kan konfigureras.

Den här inställningen är accepterat i 3-vägs-handskakning TCP när en TCP-session har konfigurerats mellan en källa och ett mål. Båda sidor skicka ett MSS-värde och lägre av två används för TCP-anslutning.

Tänk på att MTU på källan och målet inte är de enda faktorer som MSS-värdet. Mellanliggande nätverksenheter, t.ex. VPN-gatewayer, inklusive Azure VPN Gateway, kan justera MTU oberoende av källa och mål för att säkerställa optimal nätverkets prestanda.

#### <a name="path-mtu-discovery"></a>MTU-upptäckt

MSS förhandlas, men den inte kan tyda på den faktiska MSS som kan användas. Det beror på att andra nätverksenheter mellan källan och målet kan ha ett lägre värde för MTU än källan och målet. I det här fallet kommer enheten vars MTU är mindre än paketet släppa paketet. Enheten skickar tillbaka meddelandet ICMP fragmentering behövs (typ 3, 4 kod) som innehåller dess MTU. Det här ICMP-meddelandet kan källvärden att minska dess sökvägs-MTU på rätt sätt. Processen kallas identifiering för sökväg-MTU (PMTUD).

PMTUD-processen är ineffektiv och påverkar nätverkets prestanda. När det skickas paket som överskrider en nätverkssökväg MTU, måste paketen skickas igen med en lägre MSS. Om avsändaren inte meddelande ICMP fragmentering behövs, kanske på grund av en nätverksbrandvägg i sökvägen (som ofta kallas en *PMTUD svartlistade*), avsändaren inte vet som behövs för att sänka MSS och kommer kontinuerligt snabbåterställningen paketet. Det är därför rekommenderar vi inte att öka Azure VM-MTU.

#### <a name="vpn-and-mtu"></a>VPN- och MTU

Om du använder virtuella datorer som utför inkapsling (till exempel IPsec-VPN), finns det vissa ytterligare överväganden om paketstorlek och MTU. VPN lägga till fler rubriker paket, vilket ökar paketstorleken och kräver en mindre MSS.

För Azure rekommenderar vi att du anger TCP-MSS-ihopfogning till alltså 1 350 byte och tunnel gränssnitt MTU för 1 400. Mer information finns i den [VPN-enheter och IPSec/IKE-parametrar sidan](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>Svarstid, tidsfördröjningen och skalning av TCP-fönster

#### <a name="latency-and-round-trip-time"></a>Svarstid och tidszonsbevarande tid

Nätverksfördröjningen regleras av hastigheten för ljus över ett fiberoptiska fiber-nätverk. Nätverkets genomflöde i TCP styrs även effektivt av den fram och åter tid mellan två nätverksenheter.

| | | | |
|-|-|-|-|
|**väg**|**avstånd**|**Enkelriktad tid**|**RTT**|
|New York till San Francisco|4,148 km|21 ms|42 ms|
|New York to London|5,585 km|28 ms|56 ms|
|New York till Sydney|15,993 km|80 ms|160 ms|

Den här tabellen visar linjär avståndet mellan två platser. Avståndet är vanligtvis längre än linjär avståndet i nätverk. Här är en enkel formel för att beräkna minsta RTT som styrs av hastigheten för ljus:

`minimum RTT = 2 * (Distance in kilometers / Speed of propagation)`

Du kan använda 200 för hastigheten på spridning. Det här är avståndet i mätare, light färdas 1 millisekund.

Låt oss ta New York till San Francisco som exempel. Linjär avståndet är 4,148 km från varandra. Ansluta detta värde till formeln får vi följande:

`Minimum RTT = 2 * (4,148 / 200)`

Resultatet av formeln är i millisekunder.

Om du vill ha bästa nätverksprestanda är logiska alternativet att välja mål med kortast avstånd mellan dem. Du bör också utforma ditt virtuella nätverk för att optimera sökvägen till trafik och minska svarstiden. Mer information finns i avsnittet ”nätverksdesign” i den här artikeln.

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>Svarstid och tidszonsbevarande tid effekterna på TCP

Tidsfördröjningen har en direkt inverkan på maximalt dataflöde för TCP. I TCP-protokollet, *fönsterstorlek* är den maximala mängden trafik som kan skickas via en TCP-anslutning innan avsändaren måste få godkännande från mottagaren. Om TCP-MSS är inställd på 1,460 och TCP-fönsterstorlek har angetts till 65 535, skicka avsändaren 45 paket innan den har att få godkännande från mottagaren. Om avsändaren inte får bekräftelse kan överföra data igen. Här är formeln:

`TCP window size / TCP MSS = packets sent`

I det här exemplet 65 535 / 1,460 avrundas upp till 45.

Det här tillståndet som ”väntar på bekräftelse” en mekanism för att säkerställa tillförlitlig leverans av data, är vad som orsakar RTT att påverka TCP dataflöde. Ju längre avsändaren väntar på bekräftelse, desto längre tid som behövs för att vänta innan du skickar mer data.

Här är formeln för att beräkna maximalt dataflöde för en enda TCP-anslutning:

`Window size / (RTT latency in milliseconds / 1,000) = maximum bytes/second`

Den här tabellen visas de maximala utrymme i MB / per sekund dataflöde i en enda TCP-anslutning. (För megabyte används för enheten.)

| | | | |
|-|-|-|-|
|**TCP-fönsterstorlek (byte)**|**RTT latency (ms)**|**Maximal MB/sek dataflöde**|**Maximal megabit/sek dataflöde**|
|65,535|1|65.54|524.29|
|65,535|30|2.18|17.48|
|65,535|60|1.09|8.74|
|65,535|90|.73|5.83|
|65,535|120|.55|4.37|

Om paket försvinner sänks maximalt dataflöde för en TCP-anslutning när avsändaren återöverför data som den redan har skickat.

#### <a name="tcp-window-scaling"></a>Skalning av TCP-fönster

Skalning av TCP-fönster är en teknik som dynamiskt ökar TCP-fönsterstorlek för att tillåta mer data som ska skickas innan en bekräftelse krävs. I exemplet ovan skulle 45 paket skickas innan en bekräftelse krävdes. Om du ökar antalet paket som kan skickas innan en bekräftelse krävs, du minskar antalet gånger som en avsändare väntar på bekräftelse, vilket ökar TCP maximalt dataflöde.

Den här tabellen visar dessa relationer:

| | | | |
|-|-|-|-|
|**TCP-fönsterstorlek (byte)**|**RTT latency (ms)**|**Maximal MB/sek dataflöde**|**Maximal megabit/sek dataflöde**|
|65,535|30|2.18|17.48|
|131,070|30|4.37|34.95|
|262,140|30|8.74|69.91|
|524,280|30|17.48|139.81|

Men TCP huvudets värde för TCP-fönsterstorlek är 2 byte långt, vilket innebär att det maximala värdet för en receive-fönstret är 65 535. För att öka den maximala fönsterstorleken introducerades en skalningsfaktor för TCP-fönster.

Skalningsfaktorn är också en inställning som du kan konfigurera i ett operativsystem. Här är formeln för att beräkna TCP-fönsterstorlek med hjälp av skala faktorer:

`TCP window size = TCP window size in bytes \* (2^scale factor)`

Här är beräkning för ett fönster skalfaktor på 3 och en fönsterstorlek 65 535:

`65,535 \* (2^3) = 262,140 bytes`

Skala upp till 14 resulterar i en TCP-fönsterstorlek av 14 (den maximala förskjutningen tillåts). TCP-fönsterstorlek blir 1,073,725,440 byte (8,5 Gigabit).

#### <a name="support-for-tcp-window-scaling"></a>Stöd för skalning av TCP-fönster

Windows kan ange olika skalning faktorer för olika anslutningstyper. (Klasser av anslutningar exempel datacenter, internet och så vidare.) Du använder den `Get-NetTCPConnection` PowerShell-kommando för att visa fönstret skalning anslutningstyp:

```powershell
Get-NetTCPConnection
```

Du kan använda den `Get-NetTCPSetting` PowerShell-kommando för att visa värdena för varje klass:

```powershell
Get-NetTCPSetting
```

Du kan ange inledande TCP-fönsterstorlek och TCP skalningsfaktor i Windows med hjälp av den `Set-NetTCPSetting` PowerShell-kommando. Mer information finns i [Set-NetTCPSetting](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps).

```powershell
Set-NetTCPSetting
```

Dessa är de effektiva TCP-inställningarna för `AutoTuningLevel`:

| | | | |
|-|-|-|-|
|**AutoTuningLevel**|**Skalningsfaktor**|**Skala multiplikatorn**|**Formeln till<br/>beräkna maximal fönsterstorlek**|
|Har inaktiverats|Ingen|Ingen|Fönsterstorlek|
|Begränsat|4|2^4|Fönsterstorlek * (2 ^ 4)|
|Mycket begränsad|2|2^2|Fönsterstorlek * (2 ^ 2)|
|Normal|8|2^8|Fönsterstorlek * (2 ^ 8)|
|Experimentell|14|2^14|Fönsterstorlek * (2 ^ 14)|

De här inställningarna är det mest troligt att påverkar TCP-prestanda, men tänk på att många andra faktorer via internet, utanför kontrollen av Azure, kan också påverka prestanda för TCP.

#### <a name="increase-mtu-size"></a>Öka storleken på MTU

Eftersom en större MTU innebär en större MSS, kanske du undrar om ökar MTU kan öka prestanda på TCP. Förmodligen inte. Det finns för- och nackdelar till paketstorlek utöver bara TCP-trafik. Som tidigare diskuterats, är de viktigaste faktorerna som påverkar TCP dataflödesprestanda TCP-fönsterstorlek, paketförlust och RTT.

> [!IMPORTANT]
> Vi rekommenderar inte att Azure-kunder ändrar du standardvärdet för MTU på virtuella datorer.
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>Accelererat nätverk och skalning på mottagarsidan

#### <a name="accelerated-networking"></a>Accelererat nätverk

Nätverksfunktioner för virtuell dator har historiskt Processorintensiva på både den Virtuella gästdatorn och hypervisor-/ värden. Varje paket som eltransit via värden bearbetas i programvara av CPU, inklusive alla inkapsling för virtuellt nätverk och avkapsling-värden. Så mer trafik som passerar värden, desto högre CPU-belastningen. Och om värdens CPU är upptagen med andra åtgärder, som även att påverka nätverkets dataflöde och svarstid. Azure löser problemet med accelererat nätverk.

Accelererat nätverk ger konsekvent ultralow Nätverksfördröjningen via Azure och tekniker som SR-IOV interna programmerbart maskinvara. Accelererat nätverk flyttar mycket av Azure programvarudefinierade nätverksstacken av processorerna och till FPGA-baserat SmartNICs. Den här ändringen kan frigöra beräkning cykler-program för slutanvändare som placerar mindre belastning på den virtuella datorn, minskar jitter och inkonsekvens i svarstid. Prestanda kan med andra ord vara mer deterministisk.

Accelererat nätverk förbättrar prestanda genom att tillåta att Virtuella kringgå värden och upprätta en datapath direkt med en värds SmartNIC gästdatorn. Här följer några av fördelarna med accelererat nätverk:

- **Lägre latens / högre paket per sekund (pps)**: Ta bort den virtuella växeln från datapath eliminerar tid i värden för behandling av princip för paket och ökar antalet paket som kan bearbetas i den virtuella datorn.

- **Minskar jitter**: Virtuell växel bearbetning beror på mängden principinformation som måste installeras och arbetsbelastningen processorkraft som klarar bearbetningen. Avlastning av principtillämpning till maskinvara tar bort den variationen genom att tillhandahålla paket direkt till den virtuella datorn, vilket eliminerar värd-till-VM-kommunikation och all programvara avbrott och kontext växlar.

- **Minskar CPU-belastning**: Kringgår den virtuella växeln på värden leder till färre CPU-belastningen för bearbetning av nätverkstrafik.

Om du vill använda accelererat nätverk måste uttryckligen aktivera det på varje virtuell dator. Se [skapa en Linux-dator med Accelererat nätverk](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) anvisningar.

#### <a name="receive-side-scaling"></a>Skalning på mottagarsidan

Ta emot sida mottagarsidan (RSS) är en nätverksteknik för drivrutinen som distribuerar mottagande av nätverkstrafik effektivare genom att distribuera får bearbetning över flera processorer till processorer. Enkelt uttryckt innebär medför RSS att ett system att bearbeta fler mottagna trafik eftersom den använder alla tillgängliga processorer i stället för bara en. Läs mer teknisk information om RSS uppför [introduktion till skalning på mottagarsidan](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling).

För att få bästa möjliga prestanda när accelererat nätverk är aktiverat på en virtuell dator kan behöva du Aktivera RSS. RSS kan också ge fördelar på virtuella datorer som inte använder accelererat nätverk. En översikt över hur du avgör om RSS är aktiverat och hur du aktiverar det finns i [optimera nätverkets dataflöde för Azure virtual machines](https://aka.ms/FastVM).

### <a name="tcp-timewait-and-timewait-assassination"></a>TCP TIME_WAIT och TIME_WAIT assassination

TCP TIME_WAIT är en annan vanlig inställning som påverkar nätverket och programmets prestanda. På upptagna virtuella datorer som öppna och stänga många sockets, antingen som klienter eller servrar (källport IP:Source + IP:Destination målport) under normal drift av TCP, kan en viss socket hamnar i ett TIME_WAIT-tillstånd under en längre tid. TIME_WAIT-tillståndet är avsedd att tillåta ytterligare data som ska levereras på en socket innan du stänger den. TCP/IP-stackar Allmänt förhindra återanvändning av en socket genom att släppa tyst klientens TCP SYN-paket.

Hur lång tid en socket är i TIME_WAIT kan konfigureras. Det kan röra sig om 30 sekunder till 240 sekunder. Sockets är en begränsad resurs och antalet sockets som kan användas vid en given tidpunkt kan konfigureras. (Antalet tillgängliga sockets är vanligtvis cirka 30 000.) Om den tillgängliga sockets förbrukas, eller om klienter och servrar har felaktigt TIME_WAIT-inställningar och en virtuell dator försöker att återanvända en socket i ett TIME_WAIT-tillstånd, misslyckas nya anslutningar som TCP SYN ignoreras tyst paket.

Värdet för portintervall för utgående sockets konfigureras vanligtvis i TCP/IP-stack med ett operativsystem. Samma sak gäller för TCP TIME_WAIT-inställningar och socket återanvändning. Ändra dessa siffror kan förbättra skalbarheten. Men, beroende på situationen, dessa ändringar kan orsaka samverkansproblem. Du bör vara försiktig om du ändrar dessa värden.

Du kan använda TIME_WAIT assassination för att åtgärda problemet skalning. TIME_WAIT assassination kan en socket som ska återanvändas i vissa situationer, t.ex. när numret i IP-paket för den nya anslutningen överskrider sekvensnumret för senaste paketet från den tidigare anslutningen. I det här fallet operativsystemet gör att den nya anslutningen ska upprättas (det ska ta emot nya SYN-ACK) och kraft att Stäng den tidigare anslutningen som fanns i ett TIME_WAIT-tillstånd. Den här funktionen stöds på Windows virtuella datorer i Azure. Mer information om support på andra virtuella datorer, fråga OS-leverantören.

Läs om hur du konfigurerar inställningar för TCP TIME_WAIT och portintervallet för källan i [inställningar som kan ändras för att förbättra nätverksprestanda](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance).

## <a name="virtual-network-factors-that-can-affect-performance"></a>Virtuellt nätverk faktorer som kan påverka prestanda

### <a name="vm-maximum-outbound-throughput"></a>Maximalt utgående VM-dataflöde

Azure erbjuder en mängd olika storlekar på Virtuella datorer och typer, var och en med en blandning av prestandafunktioner. En av dessa funktioner är nätverket dataflöde (eller bandbredd), som mäts i megabit per sekund (Mbps). Eftersom virtuella datorer finns på delad maskinvara, måste nätverkskapacitet ganska delas mellan de virtuella datorerna med samma maskinvara. Större virtuella datorer tilldelas mer bandbredd än mindre virtuella datorer.

Den nätverksbandbredd som allokeras till varje virtuell dator är debiteras baserat på utgående (utgående) trafik från den virtuella datorn. All nätverkstrafik som lämnar den virtuella datorn räknas mot den allokerade begränsning, oavsett mål. Till exempel gäller en virtuell dator har en gräns på 1 000 Mbit/s, denna gräns om utgående trafik är avsedd för en annan virtuell dator i samma virtuella nätverk eller en utanför Azure.

Inkommande mäts inte eller begränsad direkt. Men det finns andra faktorer, till exempel processor- och lagringsbegränsningar som kan påverka en virtuell dators möjlighet att ta emot inkommande data.

Accelererat nätverk är utformad för att förbättra nätverksprestanda, inklusive svarstid, dataflöde och CPU-användning. Accelererat nätverk kan förbättra dataflödet för en virtuell dator, men det kan göra det bara upp till den virtuella datorns allokerade bandbredd.

Azure virtuella datorer har minst ett nätverksgränssnitt som är anslutna till dem. De kan ha flera. Bandbredden som allokeras till en virtuell dator är summan av all utgående trafik för alla nätverksgränssnitt som är kopplade till datorn. Med andra ord allokeras bandbredden på basis av per virtuell dator, oavsett hur många nätverksgränssnitt är kopplade till datorn.

Förväntade utgående dataflöde och antalet nätverksgränssnitt som stöds av varje VM-storlek finns beskrivna i [storlekar för Windows-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Om du vill se maximalt dataflöde, Välj en typ som **generella**, och leta sedan reda på avsnittet om seriens storlek på sidan resulterande (till exempel ”Dv2-serien”). För varje serie finns också en tabell som innehåller nätverk specifikationer i den sista kolumnen som heter ”maximalt antal nätverkskort / förväntade nätverksbandbredd (Mbit/s)”.

Dataflöde gränsen gäller för den virtuella datorn. Dataflödet påverkas inte av dessa faktorer:

- **Antalet nätverksgränssnitt**: Bandbreddsbegränsningen gäller summan av all utgående trafik från den virtuella datorn.

- **Accelerated networking**: Även om den här funktionen kan vara användbart för att uppnå den publicerade gränsen, ändras inte gränsen.

- **Trafikmål**: Alla mål räknas utgående.

- **Protokoll**: All utgående trafik över alla protokoll räknas mot gränsen.

Mer information finns i [VM nätverksbandbredd](https://aka.ms/AzureBandwidth).

### <a name="internet-performance-considerations"></a>Prestandaöverväganden för Internet

Som beskrivs i den här artikeln kan kan faktorer på internet och utanför kontrollen av Azure påverka nätverkets prestanda. Här är några av de faktorerna som:

- **Svarstid**: Mäts tiden mellan två mål kan påverkas av problem i mellanliggande nätverk, trafik som inte tar emot ”kortaste” avståndet sökvägen och icke-optimal peeringsökvägar.

- **Paketförlust**: Paketförlust kan ha orsakats av överbelastning på nätverket, fysisk sökväg problem och underpresterar nätverksenheter.

- **MTU storlek/fragmentering**: Fragmentering längs vägen kan leda till fördröjningar i data ankomst eller paket som inkommer i fel ordning, vilket kan påverka leveransen av paket.

Traceroute är ett bra verktyg för att mäta prestanda Nätverksegenskaper (till exempel paketförlust och fördröjning) med varje nätverkssökvägen mellan en källenhet och en målenhet.

### <a name="network-design-considerations"></a>Nätverksdesign

Tillsammans med överväganden som beskrivs tidigare i den här artikeln, kan topologin för ett virtuellt nätverk påverka nätverkets prestanda. Utformningen av en nav och eker att backhauls globalt trafik till ett virtuellt nätverk för single-hub kommer exempelvis att införa Nätverksfördröjningen, vilket påverkar nätverksprestanda.

Antal nätverksenheter som nätverkstrafik skickas via kan också påverka Total svarstid. Till exempel i en nav-och-eker-design, kan om trafik som passerar genom en virtuell nätverksinstallation för eker och en virtuell installation hub innan transit till internet, nätverkets virtuella installationer introducera svarstid.

### <a name="azure-regions-virtual-networks-and-latency"></a>Azure-regioner, virtuella nätverk och svarstid

Azure-regioner består av flera datacenter som finns inom ett visst geografiskt område. Dessa Datacenter kanske inte fysiskt bredvid varandra. I vissa fall är de avgränsade med så mycket som 10 kilometer. Det virtuella nätverket är ett logiskt överlägg ovanpå Azure fysiska datacenter-nätverket. Ett virtuellt nätverk innebär inte några specifika nätverkets topologi inom datacentret.

Exempelvis kanske två virtuella datorer som finns i samma virtuella nätverk och undernät i olika rack, rader eller även datacenter. De kan vara avgränsade med fot av fiberoptisk kabel kilometer av fiberoptisk kabel. Den här variationen kan införa variabel svarstid (några millisekunder förändringen) mellan olika virtuella datorer.

Geografiska placeringen av virtuella datorer och potentiella resulterande fördröjning mellan två virtuella datorer kan påverkas av konfigurationen av tillgänglighetsuppsättningar och Tillgänglighetszoner. Men avståndet mellan datacenter i en region är regionspecifika och påverkas primärt av datacenter topologi i regionen.

### <a name="source-nat-port-exhaustion"></a>Portöverbelastning NAT för källa

En distribution i Azure kan kommunicera med slutpunkter utanför Azure på internet och/eller i det offentliga IP-adressutrymmet. När en instans initierar en utgående anslutning, matchar Azure dynamiskt den privata IP-adressen till en offentlig IP-adress. När Azure skapar den här mappningen kan kan Returtrafiken för utgående Start flödet också nå den privata IP-adressen som flödet kom från.

Azure Load Balancer måste behålla den här mappningen för en period för varje utgående anslutning. Med flera innehavare natur Azure, kan det vara resurskrävande att underhålla den här mappningen för varje utgående flöde för varje virtuell dator. Det finns så gränser som anges och baserat på konfigurationen av Azure Virtual Network. Eller: Om du vill säga att mer exakt en virtuell Azure-dator kan bara ett visst antal utgående anslutningar vid en given tidpunkt. När dessa har nått den virtuella datorn inte att skapa fler utgående anslutningar.

Men det här beteendet kan konfigureras. Mer information om SNAT och SNAT port överbelastning, se [i den här artikeln](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections).

## <a name="measure-network-performance-on-azure"></a>Måttet nätverksprestanda på Azure

Ett antal prestanda maximum i den här artikeln är relaterade till svarstiden i nätverk / fram och åter tid mellan två virtuella datorer. Det här avsnittet innehåller några förslag på hur du testar svarstid/RTT och hur du testar TCP-prestanda och VM-nätverksprestanda. Du kan finjustera och testa prestandan hos TCP/IP- och värden som beskrivs ovan med hjälp av de metoder som beskrivs i det här avsnittet. Du kan ansluta svarstid, MTU, MSS och fönstret storlekarna beräkningar som angavs tidigare och jämför teoretisk maximum till faktiska värden som du anser under testningen.

### <a name="measure-round-trip-time-and-packet-loss"></a>Tidsfördröjningen för mått och paketförlust

TCP-prestanda bygger kraftigt på RTT och paket går förlorade. PING-verktyget finns i Windows och Linux ger det enklaste sättet att mäta förlust av RTT och paket. Utdata från PING visas den minsta/högsta/genomsnittlig svarstiden mellan en källa och mål. Den visas också paketförlust. PING använder ICMP-protokollet som standard. Du kan använda PsPing för att testa TCP RTT. Mer information finns i [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping).

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>Måttet faktiska dataflödet för en TCP-anslutning

NTttcp är ett verktyg för att testa TCP-prestanda för en Linux eller Windows-VM. Du kan ändra inställningarna för olika TCP och sedan testa fördelarna med hjälp av NTttcp. Mer information finns i följande källor:

- [Bandbredd/dataflöde testning (NTttcp)](https://aka.ms/TestNetworkThroughput)

- [NTttcp-verktyget](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>Måttet faktiska bandbredd för en virtuell dator

Du kan testa prestanda hos olika typer av virtuella datorer, accelererat nätverk och så vidare, med hjälp av verktyget iPerf. iPerf är också tillgängliga på Linux och Windows. iPerf kan använda TCP eller UDP för att testa den övergripande nätverkets genomflöde. iPerf TCP dataflöde tester påverkas av de faktorer som beskrivs i den här artikeln (till exempel svarstid och RTT). Så att UDP kan ge bättre resultat om du bara vill testa maximalt dataflöde.

Mer information finns i dessa artiklar:

- [Felsökning av nätverksprestanda för Expressroute](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [Verifiera VPN-dataflöde till ett virtuellt nätverk](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet)

### <a name="detect-inefficient-tcp-behaviors"></a>Identifiera ineffektiv TCP-beteenden

I infångade paket, kan Azure-kunder Se TCP-paket med TCP-flaggor (SÄCK, Duplicering ACK, SÄNDNINGSFÖRSÖK och snabb SNABBÅTERSTÄLLNINGEN) som kan tyda på problem med prestanda. Dessa paket uttryckligen har angett ineffektivitet i nätverket som uppstår vid paketförlust. Men paketförlust är inte nödvändigtvis på grund av problem med Azure prestanda. Problem med prestanda kan vara resultatet av programproblem, problem med operativsystemet eller andra problem som inte kan vara direkt relaterade till Azure-plattformen.

Tänk också på att vissa återöverföring och duplicerade Ack är normalt i ett nätverk. TCP-protokoll har skapats för att vara tillförlitliga. Bevis på dessa TCP-paket i ett infångat behöver inte betyda i ett systemfel nätverksproblem, såvida de inte är hög.

Dessa typer av paket finns fortfarande, uppgifter som att TCP-dataflöde inte går som planerat sin maximala prestanda för orsaker som beskrevs i andra avsnitt i den här artikeln.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om TCP/IP prestandajustering för virtuella Azure-datorer kan du läsa om andra överväganden för [planera virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) eller [Läs mer om att ansluta och konfigurera virtuella nätverk ](https://docs.microsoft.com/azure/virtual-network/).
