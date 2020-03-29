---
title: TCP/IP-prestandajustering för virtuella Azure-datorer | Microsoft-dokument
description: Lär dig olika vanliga TCP/IP-prestandajusteringstekniker och deras relation till virtuella Azure-datorer.
services: virtual-network
documentationcenter: na
author: rimayber
manager: paragk
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: rimayber
ms.reviewer: dgoddard, stegag, steveesp, minale, btalb, prachank
ms.openlocfilehash: bb23484903ac3ce129c6e7a7a27e0765c227fb1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68297774"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>TCP/IP-prestandajustering för virtuella Azure-datorer

I den här artikeln beskrivs vanliga TCP/IP-prestandajusteringstekniker och några saker att tänka på när du använder dem för virtuella datorer som körs på Azure. Det kommer att ge en grundläggande översikt över de tekniker och utforska hur de kan justeras.

## <a name="common-tcpip-tuning-techniques"></a>Vanliga TCP/IP-trimningstekniker

### <a name="mtu-fragmentation-and-large-send-offload"></a>MTU, fragmentering och stor avlastning

#### <a name="mtu"></a>Mtu

Den maximala överföringsenheten (MTU) är den största storleksramen (paketet), som anges i byte, som kan skickas över ett nätverksgränssnitt. MTU är en konfigurerbar inställning. Standard-MTU som används på virtuella Azure-datorer och standardinställningen på de flesta nätverksenheter globalt är 1 500 byte.

#### <a name="fragmentation"></a>Fragmentering

Fragmentering uppstår när ett paket skickas som överskrider MTU för ett nätverksgränssnitt. TCP/IP-stacken kommer att dela upp paketet i mindre delar (fragment) som överensstämmer med gränssnittets MTU. Fragmentering sker vid IP-lagret och är oberoende av det underliggande protokollet (till exempel TCP). När ett paket på 2 000 byte skickas över ett nätverksgränssnitt med en MTU på 1 500, delas paketet upp i ett paket på 1 500 byte och ett paket på 500 byte.

Nätverksenheter i sökvägen mellan en källa och ett mål kan antingen släppa paket som överskrider MTU eller fragmentera paketet i mindre delar.

#### <a name="the-dont-fragment-bit-in-an-ip-packet"></a>Don't Fragment-biten i ett IP-paket

Df-biten (Don't Fragment) är en flagga i IP-protokollhuvudet. DF-biten anger att nätverksenheter på sökvägen mellan avsändaren och mottagaren inte får fragmentera paketet. Denna bit kan ställas in av många skäl. (Se avsnittet "Path MTU Discovery" i den här artikeln till exempel.) När en nätverksenhet tar emot ett paket med bituppsättningen Fragmentera inte och det paketet överskrider enhetens gränssnitt MTU, är standardbeteendet att enheten släpper paketet. Enheten skickar ett meddelande om ICMP-fragmentering Som behövs tillbaka till paketets ursprungliga källa.

#### <a name="performance-implications-of-fragmentation"></a>Konsekvenser av fragmenteringens prestanda

Fragmentering kan ha negativa prestandakonsekvenser. En av de främsta orsakerna till effekten på prestanda är CPU / minne effekten av fragmentering och återmontering av paket. När en nätverksenhet behöver fragmentera ett paket måste den allokera CPU/minnesresurser för att utföra fragmentering.

Samma sak händer när paketet sätts ihop igen. Nätverksenheten måste lagra alla fragment tills de tas emot så att den kan sätta ihop dem igen i det ursprungliga paketet. Den här fragmenteringen och återmonteringen kan också orsaka svarstid.

Den andra möjliga negativa prestandakonsekvensen av fragmentering är att fragmenterade paket kan komma i fel ordning. När paket tas emot i oordning kan vissa typer av nätverksenheter släppa dem. När det händer måste hela paketet återsändas.

Fragment släpps vanligtvis av säkerhetsenheter som nätverksbrandvägger eller när en nätverksenhets mottagningsbuffertar är uttömda. När en nätverksenhets mottagningsbuffertar är försöker en nätverksenhet att sätta ihop ett fragmenterat paket igen, men har inte resurser att lagra och uppdatera paketet igen.

Fragmentering kan ses som en negativ operation, men stöd för fragmentering är nödvändigt när du ansluter olika nätverk via Internet.

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>Fördelar och konsekvenser av att ändra MTU

Generellt sett kan du skapa ett effektivare nätverk genom att öka MTU. Varje paket som överförs har rubrikinformation som läggs till i det ursprungliga paketet. När fragmentering skapar fler paket finns det mer rubrikomkostnader och det gör nätverket mindre effektivt.

Här är ett exempel. Ethernet-huvudstorleken är 14 byte plus en 4-byte ram kontrollsekvens för att säkerställa ramen konsekvens. Om ett paket med 2 000 byte skickas läggs 18 byte Ethernet-omkostnader till i nätverket. Om paketet är fragmenterat till ett paket med 1 500 byte och ett paket på 500 byte har varje paket 18 byte Ethernet-huvud, totalt 36 byte.

Tänk på att en ökning av MTU inte nödvändigtvis skapar ett effektivare nätverk. Om ett program bara skickar paket med 500 byte finns samma huvudomkostnader om MTU är 1 500 byte eller 9 000 byte. Nätverket blir bara effektivare om det använder större paketstorlekar som påverkas av MTU.

#### <a name="azure-and-vm-mtu"></a>Azure och VM MTU

Standardvärdet MTU för virtuella Azure-datorer är 1 500 byte. Azure Virtual Network stacken försöker fragmentera ett paket med 1 400 byte.

Observera att den virtuella nätverksstacken inte är ineffektiv eftersom den fragmenterar paket med 1 400 byte även om virtuella datorer har en MTU på 1 500. En stor andel av nätverkspaketen är mycket mindre än 1 400 eller 1 500 byte.

#### <a name="azure-and-fragmentation"></a>Azure och fragmentering

Virtual Network stack är inställd på att släppa "ur funktion fragment," det vill säga fragmenterade paket som inte kommer fram i sin ursprungliga fragmenterade ordning. Dessa paket tas bort främst på grund av en sårbarhet för nätverkssäkerhet som tillkännagavs i november 2018 som kallas FragmentSmack.

FragmentSmack är ett fel i hur Linux-kärnan hanteras återmontering av fragmenterade IPv4 och IPv6-paket. En fjärrangripare kan använda det här felet för att utlösa dyra fragment återmontering åtgärder, vilket kan leda till ökad CPU och en överbelastning på målsystemet.

#### <a name="tune-the-mtu"></a>Ställ in MTU

Du kan konfigurera en Azure VM MTU, som du kan i alla andra operativsystem. Men du bör tänka på den fragmentering som sker i Azure, som beskrivs ovan, när du konfigurerar en MTU.

Vi uppmuntrar inte kunder att öka VM MTUs. Den här diskussionen är avsedd att förklara information om hur Azure implementerar MTU och utför fragmentering.

> [!IMPORTANT]
>Att öka MTU är inte känt för att förbättra prestanda och kan ha en negativ effekt på programmets prestanda.
>
>

#### <a name="large-send-offload"></a>Stor avlastning av skicka

Stor skicka avlastning (LSO) kan förbättra nätverksprestanda genom att avlasta segmenteringen av paket till Ethernet-kortet. När LSO är aktiverat skapar TCP/IP-stacken ett stort TCP-paket och skickar det till Ethernet-kortet för segmentering innan du vidarebefordrar det. Fördelen med LSO är att det kan frigöra processorn från segmentering paket i storlekar som överensstämmer med MTU och avlasta den bearbetningen till Ethernet-gränssnittet där det utförs i hårdvara. Mer information om fördelarna med LSO finns i [Stöd för stora avlastning.](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso)

När LSO är aktiverat kan Azure-kunder se stora ramstorlekar när de utför paketinsamlingar. Dessa stora ramstorlekar kan leda till att vissa kunder tror att fragmentering sker eller att en stor MTU används när den inte är det. Med LSO kan Ethernet-kortet annonsera en större maximal segmentstorlek (MSS) till TCP/IP-stacken för att skapa ett större TCP-paket. Hela den här icke-segmenterade ramen vidarebefordras sedan till Ethernet-kortet och visas i en paketinsamling som utförs på den virtuella datorn. Men paketet kommer att delas upp i många mindre ramar av Ethernet-adaptern, enligt Ethernet-adapterns MTU.

### <a name="tcp-mss-window-scaling-and-pmtud"></a>TCP MSS fönsterskalning och PMTUD

#### <a name="tcp-maximum-segment-size"></a>Maximal segmentstorlek för TCP

TCP maximal segmentstorlek (MSS) är en inställning som begränsar storleken på TCP-segment, vilket undviker fragmentering av TCP-paket. Operativsystem använder vanligtvis den här formeln för att ange MSS:

`MSS = MTU - (IP header size + TCP header size)`

IP-huvudet och TCP-huvudet är 20 byte vardera, eller 40 byte totalt. Så ett gränssnitt med en MTU på 1.500 kommer att ha en MSS på 1.460. Men MSS är konfigurerbar.

Den här inställningen är överenskommen i TCP trevägshandskakning när en TCP-session ställs in mellan en källa och ett mål. Båda sidor skickar ett MSS-värde och den nedre av de två används för TCP-anslutningen.

Tänk på att MTUs för källan och målet inte är de enda faktorer som avgör MSS-värdet. Mellanliggande nätverksenheter, som VPN-gateways, inklusive Azure VPN Gateway, kan justera MTU oberoende av källan och målet för att säkerställa optimal nätverksprestanda.

#### <a name="path-mtu-discovery"></a>Identifiering av bana MTU

MSS förhandlas fram, men det kanske inte anger den faktiska mss som kan användas. Detta beror på att andra nätverksenheter i sökvägen mellan källan och målet kan ha ett lägre MTU-värde än källan och målet. I det här fallet släpper enheten vars MTU är mindre än paketet paketet. Enheten skickar tillbaka ett ICMP-fragmenteringsmeddelande som behövs (typ 3, kod 4) som innehåller dess MTU. Med det här ICMP-meddelandet kan källvärden minska sin sökväg MTU på rätt sätt. Processen kallas Path MTU Discovery (PMTUD).

PMTUD-processen är ineffektiv och påverkar nätverkets prestanda. När paket skickas som överskrider en nätverkssökvägs MTU måste paketen återsändas med en lägre MSS.When packets are sent that exceed a network path's MTU, the packets need to retransmitted with a lower MSS. Om avsändaren inte får meddelandet ICMP Fragmentation Needed, kanske på grund av en nätverksbrandvägg i sökvägen (vanligen kallat *ett PMTUD-svarthål),* vet avsändaren inte att den behöver sänka MSS och kontinuerligt skickar paketet. Det är därför vi inte rekommenderar att du ökar Azure VM MTU.

#### <a name="vpn-and-mtu"></a>VPN och MTU

Om du använder virtuella datorer som utför inkapsling (som IPsec VPN) finns det några ytterligare överväganden om paketstorlek och MTU. VPN lägger till fler rubriker i paket, vilket ökar paketstorleken och kräver en mindre MSS.

För Azure rekommenderar vi att du ställer in TCP MSS-fastspänning till 1 350 byte och tunnelgränssnitt MTU till 1 400. Mer information finns på [sidan VPN-enheter och IPSec/IKE-parametrar](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>Svarstid, rundresa och TCP-fönsterskalning

#### <a name="latency-and-round-trip-time"></a>Latens och rundresa

Nätverksfördröjning styrs av ljusets hastighet över ett fiberoptiskt nätverk. Nätverksgenomströmning för TCP styrs också effektivt av rundresatiden (RTT) mellan två nätverksenheter.

| | | | |
|-|-|-|-|
|**Routa**|**Avstånd**|**Enkelriktad tid**|**RTT**|
|New York till San Francisco|4 148 km|21 ms|42 ms|
|New York till London|5 585 km|28 ms|56 ms|
|New York till Sydney|15 993 km|80 ms|160 ms|

I den här tabellen visas det raka avståndet mellan två platser. I nätverk är avståndet vanligtvis längre än det raka avståndet. Här är en enkel formel för att beräkna minsta RTT som styrs av ljusets hastighet:

`minimum RTT = 2 * (Distance in kilometers / Speed of propagation)`

Du kan använda 200 för spridningshastigheten. Detta är avståndet, i meter, att ljuset färdas i 1 millisekund.

Låt oss ta New York till San Francisco som ett exempel. Den raka linjen är 4.148 km. Genom att koppla in det värdet i ekvationen får vi följande:

`Minimum RTT = 2 * (4,148 / 200)`

Utgången av ekvationen är i millisekunder.

Om du vill få bästa nätverksprestanda är det logiska alternativet att välja mål med det kortaste avståndet mellan dem. Du bör också utforma det virtuella nätverket för att optimera trafiksökvägen och minska svarstiden. Mer information finns i avsnittet "Nätverksdesignöverväganden" i den här artikeln.

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>Svarstids- och rundningstidseffekter på TCP

Tur-och-retur-tid har en direkt effekt på maximalt TCP-dataflöde. I TCP-protokollet är *fönsterstorlek* den maximala mängden trafik som kan skickas via en TCP-anslutning innan avsändaren behöver ta emot bekräftelse från mottagaren. Om TCP MSS är inställd på 1 460 och TCP-fönsterstorleken är inställd på 65 535 kan avsändaren skicka 45 paket innan den måste ta emot bekräftelse från mottagaren. Om avsändaren inte får bekräftelse, kommer det att återsända data. Här är formeln:

`TCP window size / TCP MSS = packets sent`

I det här exemplet avrundas 65 535 / 1 460 uppåt till 45.

Detta tillstånd "väntar på bekräftelse", en mekanism för att säkerställa tillförlitlig leverans av data, är vad som orsakar RTT att påverka TCP-dataflöde. Ju längre avsändaren väntar på bekräftelse, desto längre måste vänta innan du skickar mer data.

Här är formeln för att beräkna det maximala dataflödet för en enda TCP-anslutning:

`Window size / (RTT latency in milliseconds / 1,000) = maximum bytes/second`

Den här tabellen visar det maximala megabyte/per sekund-dataflödet för en enda TCP-anslutning. (För läsbarhet används megabyte för måttenheten.)

| | | | |
|-|-|-|-|
|**TCP-fönsterstorlek (byte)**|**RTT-svarstid (ms)**|**Maximalt megabyte/sekundgenomflöde**|**Maximalt megabit/sekundgenomflöde**|
|65,535|1|65.54|524.29|
|65,535|30|2.18|17.48|
|65,535|60|1.09|8.74|
|65,535|90|.73|5.83|
|65,535|120|.55|4.37|

Om paket går förlorade minskas det maximala dataflödet för en TCP-anslutning medan avsändaren skickar om data som den redan har skickat.

#### <a name="tcp-window-scaling"></a>TCP-fönsterskalning

TCP-fönsterskalning är en teknik som dynamiskt ökar TCP-fönsterstorleken så att fler data kan skickas innan en bekräftelse krävs. I föregående exempel skulle 45 paket skickas innan en bekräftelse krävdes. Om du ökar antalet paket som kan skickas innan en bekräftelse behövs minskar du antalet gånger en avsändare väntar på bekräftelse, vilket ökar TCP:s maximala dataflöde.

Den här tabellen illustrerar dessa relationer:

| | | | |
|-|-|-|-|
|**TCP-fönsterstorlek (byte)**|**RTT-svarstid (ms)**|**Maximalt megabyte/sekundgenomflöde**|**Maximalt megabit/sekundgenomflöde**|
|65,535|30|2.18|17.48|
|131,070|30|4.37|34.95|
|262,140|30|8.74|69.91|
|524,280|30|17.48|139.81|

Men TCP-huvudvärdet för TCP-fönsterstorleken är bara 2 byte lång, vilket innebär att det maximala värdet för ett mottagningsfönster är 65 535. För att öka den maximala fönsterstorleken introducerades en TCP-fönsterskalafaktor.

Skalfaktorn är också en inställning som du kan konfigurera i ett operativsystem. Här är formeln för att beräkna TCP-fönsterstorleken med hjälp av skalningsfaktorer:

`TCP window size = TCP window size in bytes \* (2^scale factor)`

Här är beräkningen för en fönsterskala faktor 3 och en fönsterstorlek på 65.535:

`65,535 \* (2^3) = 262,140 bytes`

En skalfaktor på 14 resulterar i en TCP-fönsterstorlek på 14 (den högsta tillåtna förskjutningen). TCP-fönsterstorleken är 1 073 725 440 byte (8,5 gigabit).

#### <a name="support-for-tcp-window-scaling"></a>Stöd för TCP-fönsterskalning

Windows kan ange olika skalningsfaktorer för olika anslutningstyper. (Klasser av anslutningar inkluderar datacenter, internet och så vidare.) Du använder `Get-NetTCPConnection` kommandot PowerShell för att visa anslutningstypen för fönsterskalning:

```powershell
Get-NetTCPConnection
```

Du kan `Get-NetTCPSetting` använda powershell-kommandot för att visa värdena för varje klass:

```powershell
Get-NetTCPSetting
```

Du kan ange den ursprungliga TCP-fönsterstorleken och TCP-skalningsfaktorn `Set-NetTCPSetting` i Windows med kommandot PowerShell. Mer information finns i [Set-NetTCPSetting](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps).

```powershell
Set-NetTCPSetting
```

Dessa är de effektiva TCP-inställningarna för: `AutoTuningLevel`

| | | | |
|-|-|-|-|
|**Autotuninglevel**|**Skalningsfaktor**|**Skalning multiplikator**|**Formel<br/>för att beräkna maximal fönsterstorlek**|
|Disabled|Inget|Inget|Fönsterstorlek|
|Begränsade|4|2^4|Fönsterstorlek * (2^4)|
|Mycket begränsad|2|2^2|Fönsterstorlek * (2^2)|
|Normal|8|2^8|Fönsterstorlek * (2^8)|
|Experimentell|14|2^14|Fönsterstorlek * (2^14)|

Dessa inställningar är mest benägna att påverka TCP-prestanda, men tänk på att många andra faktorer på internet, utanför Azures kontroll, också kan påverka TCP-prestanda.

#### <a name="increase-mtu-size"></a>Öka MTU-storleken

Eftersom en större MTU innebär en större MSS, kanske du undrar om en ökning av MTU kan öka TCP-prestanda. Förmodligen inte. Det finns fördelar och nackdelar med paketstorlek utöver bara TCP-trafik. Som diskuterats tidigare är de viktigaste faktorerna som påverkar TCP-dataflödesprestanda TCP-fönsterstorlek, paketförlust och RTT.

> [!IMPORTANT]
> Vi rekommenderar inte att Azure-kunder ändrar standardvärdet för MTU på virtuella datorer.
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>Snabbare nätverk och ta emot sidoskalning

#### <a name="accelerated-networking"></a>Snabbare nätverk

Nätverksfunktioner för virtuella datorer har historiskt sett varit PROCESSOR-intensiva på både gästdatorn och hypervisorn/värden. Varje paket som transiterar genom värden bearbetas i programvara av värdprocessorn, inklusive alla virtuella nätverksinkapsling och kapsling. Så ju mer trafik som går igenom värden, desto högre CPU belastning. Och om värdprocessorn är upptagen med andra åtgärder, som också kommer att påverka nätverksdataflödet och svarstiden. Azure åtgärdar det här problemet med accelererade nätverk.

Accelererade nätverk ger konsekvent ultralåg nätverksfördröjning via den interna programmerbara maskinvaran för Azure och tekniker som SR-IOV. Accelererade nätverk flyttar mycket av Azure-programvarudefinierade nätverksstacken från processorerna och till FPGA-baserade SmartNIC.Accelerated networking moves much of the Azure software-defined networking stack off the CPCUs and into FPGA-based SmartNICs. Den här ändringen gör det möjligt för slutanvändaren att återta beräkningscykler, vilket ger mindre belastning på den virtuella datorn, vilket minskar jitter och inkonsekvens i svarstiden. Med andra ord kan prestanda vara mer deterministiska.

Accelererade nätverk förbättrar prestanda genom att låta gästen VM kringgå värden och upprätta en dataväg direkt med en värds SmartNIC. Här är några fördelar med accelererade nätverk:

- **Lägre latens / högre paket per sekund (pps)**: Ta bort den virtuella växeln från datapath eliminerar tid paket spendera i värden för principbearbetning och ökar antalet paket som kan bearbetas i den virtuella datorn.

- **Minskad jitter:** Virtuell växelbearbetning beror på hur mycket princip som måste tillämpas och arbetsbelastningen för processorn som gör bearbetningen. Avlastning av principförsämringen till maskinvaran tar bort den variabiliteten genom att leverera paket direkt till den virtuella datorn, vilket eliminerar kommunikation mellan värd och dator och alla programvaruavbrott och kontextväxlar.

- **Minskad CPU-användning:** Förbi den virtuella växeln i värden leder till mindre CPU-användning för bearbetning av nätverkstrafik.

Om du vill använda accelererade nätverk måste du uttryckligen aktivera det på varje tillämplig virtuell dator. Se [Skapa en virtuell Linux-dator med accelererade nätverk](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) för instruktioner.

#### <a name="receive-side-scaling"></a>Ta emot sidoskalning

Ta emot sidoskalning (RSS) är en nätverksdrivrutinsteknik som distribuerar mottagning av nätverkstrafik mer effektivt genom att distribuera mottagningsbearbetning över flera processorer i ett system med flera processorer. Enkelt uttryckt tillåter RSS ett system för att bearbeta mer mottagen trafik eftersom det använder alla tillgängliga processorer istället för bara en. En mer teknisk diskussion om RSS finns i [Introduktion för att ta emot sidoskalning](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling).

För att få bästa prestanda när accelererade nätverk är aktiverat på en virtuell dator måste du aktivera RSS. RSS kan också ge fördelar för virtuella datorer som inte använder accelererade nätverk. En översikt över hur du tar reda på om RSS är aktiverat och hur du aktiverar det finns [i Optimera nätverksdataflödet för virtuella Azure-datorer](https://aka.ms/FastVM).

### <a name="tcp-time_wait-and-time_wait-assassination"></a>Mordet på TCP TIME_WAIT och TIME_WAIT

TCP TIME_WAIT är en annan vanlig inställning som påverkar nätverkets och programmets prestanda. På upptagna virtuella datorer som öppnar och stänger många sockets, antingen som klienter eller som servrar (Source IP:Source Port + Destination IP:Destination Port), under normal drift av TCP, kan en viss socket hamna i ett TIME_WAIT tillstånd under lång tid. Det TIME_WAIT tillståndet är avsett att tillåta att ytterligare data levereras på ett uttag innan du stänger den. Så TCP / IP stackar i allmänhet förhindra återanvändning av en socket genom att tyst släppa klientens TCP SYN-paket.

Den tid som ett uttag är i TIME_WAIT kan konfigureras. Det kan variera från 30 sekunder till 240 sekunder. Sockets är en begränsad resurs och antalet sockets som kan användas vid en given tidpunkt kan konfigureras. (Antalet tillgängliga sockets är vanligtvis cirka 30 000.) Om de tillgängliga socketarna förbrukas, eller om klienter och servrar har matchat TIME_WAIT inställningar och en virtuell dator försöker återanvända en socket i ett TIME_WAIT tillstånd, misslyckas nya anslutningar när TCP SYN-paket tyst tas bort.

Värdet för portintervallet för utgående sockets kan vanligtvis konfigureras i TCP/IP-stacken i ett operativsystem. Samma sak gäller för TCP TIME_WAIT inställningar och uttag återanvändning. Om du ändrar dessa siffror kan skalbarheten förbättras. Men beroende på situationen kan dessa förändringar orsaka driftskompatibilitetsproblem. Du bör vara försiktig om du ändrar dessa värden.

Du kan använda TIME_WAIT mordet för att ta itu med den här skalningsbegränsningen. TIME_WAIT mordet gör att en socket kan återanvändas i vissa situationer, till exempel när sekvensnumret i IP-paketet för den nya anslutningen överskrider sekvensnumret för det sista paketet från den tidigare anslutningen. I det här fallet tillåter operativsystemet att den nya anslutningen upprättas (det accepterar den nya SYN/ACK) och tvingar stänga den tidigare anslutningen som var i ett TIME_WAIT tillstånd. Den här funktionen stöds på virtuella Windows-datorer i Azure. Om du vill veta mer om support i andra virtuella datorer kontaktar du os-leverantören.

Mer information om hur du konfigurerar TCP-TIME_WAIT inställningar och källportintervall finns [i Inställningar som kan ändras för att förbättra nätverksprestanda](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance).

## <a name="virtual-network-factors-that-can-affect-performance"></a>Virtuella nätverksfaktorer som kan påverka prestanda

### <a name="vm-maximum-outbound-throughput"></a>Dator maximal utgående dataflöde

Azure tillhandahåller en mängd olika vm-storlekar och typer, var och en med en annan blandning av prestandafunktioner. En av dessa funktioner är nätverksdataflöde (eller bandbredd), som mäts i megabit per sekund (Mbps). Eftersom virtuella datorer finns på delad maskinvara måste nätverkskapaciteten delas rättvist mellan de virtuella datorerna med samma maskinvara. Större virtuella datorer allokeras mer bandbredd än mindre virtuella datorer.

Nätverksbandbredden som allokeras till varje virtuell dator mäts på utgående (utgående) trafik från den virtuella datorn. All nätverkstrafik som lämnar den virtuella datorn räknas in i den allokerade gränsen, oavsett mål. Om en virtuell dator till exempel har en gräns på 1 000 Mbit/s gäller den gränsen om den utgående trafiken är avsedd för en annan virtuell dator i samma virtuella nätverk eller en utanför Azure.

Inträngning mäts inte eller begränsas direkt. Men det finns andra faktorer, som CPU och lagringsgränser, som kan påverka en virtuell dators förmåga att bearbeta inkommande data.

Accelererad nätverk är utformad för att förbättra nätverkets prestanda, inklusive svarstid, dataflöde och CPU-användning. Accelererade nätverk kan förbättra en virtuell dators dataflöde, men det kan bara göra det upp till den virtuella datorns allokerade bandbredd.

Virtuella Azure-datorer har minst ett nätverksgränssnitt kopplat till dem. De kan ha flera. Den bandbredd som allokeras till en virtuell dator är summan av all utgående trafik i alla nätverksgränssnitt som är anslutna till datorn. Med andra ord allokeras bandbredden per virtuell dator, oavsett hur många nätverksgränssnitt som är anslutna till datorn.

Förväntat utgående dataflöde och antalet nätverksgränssnitt som stöds av varje vm-storlek beskrivs i [Storlekar för virtuella Windows-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Om du vill visa maximalt dataflöde väljer du en typ, till **exempel Allmänt ändamål,** och letar sedan reda på avsnittet om storleksserierna på den resulterande sidan (till exempel "Dv2-serien"). För varje serie finns det en tabell som innehåller nätverksspecifikationer i den sista kolumnen, som heter "Max nätverkskort / Förväntad nätverksbandbredd (Mbps)."

Gränsen för dataflöde gäller för den virtuella datorn. Dataflöde påverkas inte av dessa faktorer:

- **Antal nätverksgränssnitt: Bandbreddsgränsen**gäller summan av all utgående trafik från den virtuella datorn.

- **Accelererade nätverk:** Även om den här funktionen kan vara till hjälp för att uppnå den publicerade gränsen, ändrar den inte gränsen.

- **Trafikdestination**: Alla destinationer räknas in mot den utgående gränsen.

- **Protokoll**: All utgående trafik över alla protokoll räknas mot gränsen.

Mer information finns i [Bandbredd för virtuell datornätverk](https://aka.ms/AzureBandwidth).

### <a name="internet-performance-considerations"></a>Hänsyn till internetprestanda

Som diskuterats i hela den här artikeln kan faktorer på internet och utanför kontrollen av Azure påverka nätverkets prestanda. Här är några av dessa faktorer:

- **Svarstid:** Tur-och-returtiden mellan två destinationer kan påverkas av problem på mellanliggande nätverk, av trafik som inte tar den "kortaste" avståndsvägen och av suboptimala peering-vägar.

- **Paketförlust:** Paketförlust kan orsakas av nätverksöverbelastning, problem med fysiska sökvägar och underpresterande nätverksenheter.

- **MTU storlek /fragmentering:** Fragmentering längs sökvägen kan leda till förseningar i dataankomst eller i paket som anländer i oordning, vilket kan påverka leveransen av paket.

Traceroute är ett bra verktyg för att mäta nätverksprestandaegenskaper (som paketförlust och svarstid) längs varje nätverkssökväg mellan en källenhet och en målenhet.

### <a name="network-design-considerations"></a>Hänsyn till nätverksdesign

Tillsammans med de överväganden som diskuterats tidigare i den här artikeln kan topologin för ett virtuellt nätverk påverka nätverkets prestanda. En hub-and-spoke-design som backhauls trafik globalt till ett virtuellt nätverk med en hubb introducerar till exempel nätverksfördröjning, vilket påverkar den totala nätverksprestandan.

Antalet nätverksenheter som nätverkstrafiken passerar genom kan också påverka den totala svarstiden. I en hub-and-spoke-design kan nätverksvirvliga enheter införa svarstid om trafiken passerar genom en virtuell virtuell e-nätverksinstallation och en virtuell hubb-apparat innan den överförs till internet.

### <a name="azure-regions-virtual-networks-and-latency"></a>Azure-regioner, virtuella nätverk och svarstid

Azure-regioner består av flera datacenter som finns inom ett allmänt geografiskt område. Dessa datacenter kanske inte är fysiskt bredvid varandra. I vissa fall är de åtskilda med så mycket som 10 kilometer. Det virtuella nätverket är ett logiskt överlägg ovanpå Azures fysiska datacenternätverk. Ett virtuellt nätverk innebär inte någon specifik nätverkstopologi i datacentret.

Till exempel kan två virtuella datorer som finns i samma virtuella nätverk och undernät finnas i olika rack, rader eller till och med datacenter. De kan separeras med fötter av fiberoptisk kabel eller kilometer fiberoptisk kabel. Den här variationen kan medföra variabel svarstid (några millisekunders skillnad) mellan olika virtuella datorer.

Den geografiska placeringen av virtuella datorer och den potentiella resulterande svarstiden mellan två virtuella datorer kan påverkas av konfigurationen av tillgänglighetsuppsättningar och tillgänglighetszoner. Men avståndet mellan datacenter i en region är regionspecifikt och påverkas främst av datacentertopologi i regionen.

### <a name="source-nat-port-exhaustion"></a>Utmattning av NAT-portar för källa

En distribution i Azure kan kommunicera med slutpunkter utanför Azure på det offentliga internet och/eller i det offentliga IP-utrymmet. När en instans initierar en utgående anslutning mappar Azure dynamiskt den privata IP-adressen till en offentlig IP-adress. När Azure har skapat den här mappningen kan returtrafik för det utgående flödet också nå den privata IP-adressen där flödet har sitt ursprung.

För varje utgående anslutning måste Azure Load Balancer underhålla den här mappningen under en viss tidsperiod. Med Azures multitenant karaktär kan det vara resurskrävande att underhålla den här mappningen för varje utgående flöde för varje virtuell dator. Så det finns gränser som är inställda och baserat på konfigurationen av Azure Virtual Network. Eller, för att säga att mer exakt, en Azure VM kan bara göra ett visst antal utgående anslutningar vid en viss tidpunkt. När dessa gränser har uppnåtts kan den virtuella datorn inte skapa fler utgående anslutningar.

Men detta beteende är konfigurerbart. Mer information om utmattning av SNAT- och SNAT-portar finns i [den här artikeln](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections).

## <a name="measure-network-performance-on-azure"></a>Mäta nätverksprestanda på Azure

Ett antal av prestandamaxringarna i den här artikeln är relaterade till nätverksfördröjningen/rundresatiden (RTT) mellan två virtuella datorer. Det här avsnittet innehåller några förslag på hur du testar svarstid/RTT och hur du testar TCP-prestanda och VM-nätverksprestanda. Du kan ställa in och prestandatesta TCP/IP- och nätverksvärden som diskuterats tidigare med hjälp av de tekniker som beskrivs i det här avsnittet. Du kan koppla svarstids-, MTU-, MSS- och fönsterstorleksvärdena i beräkningarna som angetts tidigare och jämföra teoretiska maximum med faktiska värden som du observerar under testningen.

### <a name="measure-round-trip-time-and-packet-loss"></a>Mät tur-och-retur-tid och paketförlust

TCP-prestanda är starkt beroende av RTT och paketförlust. PING-verktyget som finns i Windows och Linux är det enklaste sättet att mäta RTT och paketförlust. Utdata från PING visar minsta/högsta/genomsnittliga svarstiden mellan en källa och ett mål. Det kommer också att visa paketförlust. PING använder ICMP-protokollet som standard. Du kan använda PsPing för att testa TCP RTT. Mer information finns i [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping).

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>Mäta det faktiska dataflödet för en TCP-anslutning

NTttcp är ett verktyg för att testa TCP-prestanda för en Linux eller Windows VM. Du kan ändra olika TCP-inställningar och sedan testa fördelarna med hjälp av NTttcp. Mer information finns i följande resurser:

- [Testning av bandbredd/dataflöde (NTttcp)](https://aka.ms/TestNetworkThroughput)

- [NTttcp-verktyg](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>Mäta den faktiska bandbredden för en virtuell dator

Du kan testa prestanda för olika vm-typer, accelererade nätverk och så vidare, med hjälp av ett verktyg som kallas iPerf. iPerf finns även på Linux och Windows. iPerf kan använda TCP eller UDP för att testa det övergripande nätverksdataflödet. iPerf TCP-dataflödestester påverkas av de faktorer som beskrivs i den här artikeln (som latens och RTT). Så UDP kan ge bättre resultat om du bara vill testa maximalt dataflöde.

Mer information finns i dessa artiklar:

- [Felsöka Expressroute-nätverksprestanda](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [Verifiera VPN-dataflöde till ett virtuellt nätverk](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet)

### <a name="detect-inefficient-tcp-behaviors"></a>Identifiera ineffektiva TCP-beteenden

I paketinsamlingar kan Azure-kunder se TCP-paket med TCP-flaggor (SACK, DUP ACK, RETRANSMIT och FAST RETRANSMIT) som kan tyda på problem med nätverksprestanda. Dessa paket anger specifikt nätverks ineffektivitet som uppstår till följd av paketförlust. Men paketförlust orsakas inte nödvändigtvis av Azure-prestandaproblem. Prestandaproblem kan bero på programproblem, operativsystemproblem eller andra problem som kanske inte är direkt relaterade till Azure-plattformen.

Tänk också på att vissa återsändningar och dubbla åtkomstkontrollistor är normala i ett nätverk. TCP-protokoll byggdes för att vara tillförlitliga. Bevis på dessa TCP-paket i en paketfångst tyder inte nödvändigtvis på ett systemfelsproblem, såvida de inte är överdrivna.

Ändå är dessa pakettyper indikationer på att TCP-dataflödet inte uppnår sin maximala prestanda, av skäl som beskrivs i andra avsnitt i den här artikeln.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig mer om TCP/IP-prestandajustering för virtuella Azure-datorer kanske du vill läsa om andra överväganden för [att planera virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) eller lära dig mer om hur du ansluter och [konfigurerar virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/).
