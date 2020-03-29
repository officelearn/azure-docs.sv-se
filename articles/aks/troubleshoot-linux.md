---
title: Linux-prestandaverktyg
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du felsöker och löser vanliga problem när du använder Azure Kubernetes Service (AKS)
services: container-service
author: alexeldeib
ms.service: container-service
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: aleldeib
ms.openlocfilehash: eb6b126b4d1794adf0380432040190b91a17a675
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925610"
---
# <a name="linux-performance-troubleshooting"></a>Felsökning av Linux-prestanda

Resursutmattning på Linux-maskiner är ett vanligt problem och kan manifesteras genom en mängd olika symptom. Det här dokumentet ger en översikt på hög nivå över de verktyg som finns tillgängliga för att diagnostisera sådana problem.

Många av dessa verktyg accepterar ett intervall för att producera rullande utgång. Detta utdataformat gör vanligtvis spotting mönster mycket lättare. Om det godkänns kommer exemplet `[interval]`på åkallan att innehålla .

Många av dessa verktyg har en omfattande historia och en bred uppsättning konfigurationsalternativ. Den här sidan innehåller bara en enkel delmängd av anrop för att belysa vanliga problem. Den kanoniska informationskällan är alltid referensdokumentationen för varje enskilt verktyg. Denna dokumentation kommer att vara mycket mer grundlig än vad som tillhandahålls här.

## <a name="guidance"></a>Riktlinjer

Var systematisk i din inställning till att undersöka prestandaproblem. Två vanliga metoder är ANVÄNDNING (användning, mättnad, fel) och RED (hastighet, fel, varaktighet). RED används vanligtvis i samband med tjänster för begäran-baserad övervakning. ANVÄNDNING används vanligtvis för att övervaka resurser: för varje resurs i en dator, övervaka användning, mättnad och fel. De fyra huvudsakliga typerna av resurser på alla datorer är cpu, minne, disk och nätverk. Hög användning, mättnad eller felfrekvens för någon av dessa resurser indikerar ett möjligt problem med systemet. När det finns ett problem undersöker du orsaken: varför är disk-I/O-svarstiden hög? Är diskarna eller den virtuella datorn SKU begränsade? Vilka processer skriver till enheterna och till vilka filer?

Några exempel på vanliga problem och indikatorer för att diagnostisera dem:
- IOPS-begränsning: använd iostat för att mäta IOPS per enhet. Se till att ingen enskild disk är över gränsen och summan för alla diskar är mindre än gränsen för den virtuella datorn.
- Bandbreddsbegränsning: använd iostat som för IOPS, men mäta läs-/skrivdataflöde. Se till att både per enhet och aggregerat dataflöde ligger under bandbreddsgränserna.
- SNAT utmattning: detta kan manifesteras som hög aktiva (utgående) anslutningar i SAR. 
- Paketförlust: Detta kan mätas genom proxy via TCP återtransmit antal i förhållande till skickade / mottagna räknas. Båda `sar` `netstat` och kan visa den här informationen.

## <a name="general"></a>Allmänt

Dessa verktyg är allmänna ändamål och omfattar grundläggande systeminformation. De är en bra utgångspunkt för vidare utredning.

### <a name="uptime"></a>Upptid

```
$ uptime
 19:32:33 up 17 days, 12:36,  0 users,  load average: 0.21, 0.77, 0.69
```

upptid ger system drifttid och 1, 5 och 15-minuters belastning genomsnitt. Dessa belastning genomsnitt ungefär motsvarar trådar gör arbete eller väntar på avbrottsfri arbete för att slutföra. I absoluta tal kan dessa siffror vara svåra att tolka, men mätt med tiden kan de berätta användbar information:

- 1-minuters genomsnittlig > 5-minuters genomsnitt innebär belastningen ökar.
- 1-minuters genomsnittlig < 5-minuters genomsnitt innebär belastningen minskar.

drifttid kan också belysa varför information inte är tillgänglig: problemet kan ha lösts på egen hand eller genom en omstart innan användaren kan komma åt datorn.

Inläsningsgenomsnitt som är högre än antalet tillgängliga CPU-trådar kan tyda på ett prestandaproblem med en viss arbetsbelastning.

### <a name="dmesg"></a>Dmesg

```
$ dmesg | tail 
$ dmesg --level=err | tail
```

dmesg dumpar kärnbufferten. Händelser som OOMKill lägger till en post i kärnbufferten. Att hitta en OOMKill eller andra meddelanden resursutmattning i dmesg loggar är en stark indikator på ett problem.

### <a name="top"></a>överst

```
$ top
Tasks: 249 total,   1 running, 158 sleeping,   0 stopped,   0 zombie
%Cpu(s):  2.2 us,  1.3 sy,  0.0 ni, 95.4 id,  1.0 wa,  0.0 hi,  0.2 si,  0.0 st
KiB Mem : 65949064 total, 43415136 free,  2349328 used, 20184600 buff/cache
KiB Swap:        0 total,        0 free,        0 used. 62739060 avail Mem

   PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
116004 root      20   0  144400  41124  27028 S  11.8  0.1 248:45.45 coredns
  4503 root      20   0 1677980 167964  89464 S   5.9  0.3   1326:25 kubelet
     1 root      20   0  120212   6404   4044 S   0.0  0.0  48:20.38 systemd
     ...
```

`top`ger en bred översikt över det aktuella systemtillståndet. Rubrikerna ger användbar mängdinformation:

- uppgifter: löpning, sömn, stoppad.
- CPU-användning, i det här fallet mest visar inaktiv tid.
- totalt, fritt och använt systemminne.

`top`kan missa kortlivade processer; alternativ `htop` som `atop` och tillhandahålla liknande gränssnitt samtidigt som några av dessa brister åtgärdas.

## <a name="cpu"></a>Processor

Dessa verktyg ger information om cpu-användning. Detta är särskilt användbart med rullande utgång, där mönster blir lätta att upptäcka.

### <a name="mpstat"></a>mpstat (mpstat)

```
$ mpstat -P ALL [interval]
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

19:49:03     CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
19:49:04     all    1.01    0.00    0.63    2.14    0.00    0.13    0.00    0.00    0.00   96.11
19:49:04       0    1.01    0.00    1.01   17.17    0.00    0.00    0.00    0.00    0.00   80.81
19:49:04       1    1.98    0.00    0.99    0.00    0.00    0.00    0.00    0.00    0.00   97.03
19:49:04       2    1.01    0.00    0.00    0.00    0.00    1.01    0.00    0.00    0.00   97.98
19:49:04       3    0.00    0.00    0.99    0.00    0.00    0.99    0.00    0.00    0.00   98.02
19:49:04       4    1.98    0.00    1.98    0.00    0.00    0.00    0.00    0.00    0.00   96.04
19:49:04       5    1.00    0.00    1.00    0.00    0.00    0.00    0.00    0.00    0.00   98.00
19:49:04       6    1.00    0.00    1.00    0.00    0.00    0.00    0.00    0.00    0.00   98.00
19:49:04       7    1.98    0.00    0.99    0.00    0.00    0.00    0.00    0.00    0.00   97.03
```

`mpstat`skriver ut liknande CPU-information till toppen, men bryts ned efter CPU-tråd. Att se alla kärnor på en gång kan vara användbart för att upptäcka mycket obalanserad CPU-användning, till exempel när ett enda gängat program använder en kärna på 100% utnyttjande. Det här problemet kan vara svårare att upptäcka när aggregeras över alla processorer i systemet.

### <a name="vmstat"></a>vmstat (vmstat)

```
$ vmstat [interval]
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 43300372 545716 19691456    0    0     3    50    3    3  2  1 95  1  0
```

`vmstat`ger liknande `mpstat` `top`information och räknar upp antalet processer som väntar på CPU (r-kolumn), minnesstatistik och procent av CPU-tid som spenderas i varje arbetstillstånd.

## <a name="memory"></a>Minne

Minnet är en mycket viktig, och tack och lov lätt, resurs att spåra. Vissa verktyg kan rapportera både `vmstat`CPU och minne, till exempel . Men verktyg `free` som kan fortfarande vara användbara för snabb felsökning.

### <a name="free"></a>Gratis

```
$ free -m
              total        used        free      shared  buff/cache   available
Mem:          64403        2338       42485           1       19579       61223
Swap:             0           0           0
```

`free`presenterar grundläggande information om det totala minnet samt använt och ledigt minne. `vmstat`kan vara mer användbart även för grundläggande minnesanalys på grund av dess förmåga att ge rullande utgång.

## <a name="disk"></a>Disk

Dessa verktyg mäter disk IOPS, väntköer och totalt dataflöde. 

### <a name="iostat"></a>iostat

```
$ iostat -xy [interval] [count]
$ iostat -xy 1 1
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           3.42    0.00    2.92    1.90    0.00   91.76

Device:         rrqm/s   wrqm/s     r/s     w/s    rkB/s    wkB/s avgrq-sz avgqu-sz   await r_await w_await  svctm  %util
loop0             0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
sdb               0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
sda               0.00    56.00    0.00   65.00     0.00   504.00    15.51     0.01    3.02    0.00    3.02   0.12   0.80
scd0              0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
```

`iostat`ger djup inblick i diskanvändning. Denna åkallan `-x` passerar för utökad statistik, `-y` att hoppa över den `1 1` första utdata utskriftssystem genomsnitt sedan uppstart, och att ange vi vill ha 1-sekunders intervall, slutar efter ett block av utdata. 

`iostat`exponerar många användbara statistik:

- `r/s`och `w/s` läses per sekund och skriver per sekund. Summan av dessa värden är IOPS.
- `rkB/s`och `wkB/s` är kilobyte läsa / skriftliga per sekund. Summan av dessa värden är dataflöde.
- `await`är den genomsnittliga iowaittiden i millisekunder för köade begäranden.
- `avgqu-sz`är den genomsnittliga köstorleken över det angivna intervallet.

På en virtuell Azure-dator:

- summan av `r/s` `w/s` och för en enskild blockenhet får inte överskrida diskens SKU-gränser.
- summan av `rkB/s` `wkB/s` och för en enskild blockenhet får inte överskrida diskens SKU-gränser
- summan av `r/s` `w/s` och för alla blockenheter får inte överskrida gränserna för VM SKU.
- Summan av `rkB/s` och wkB/s för alla blockenheter får inte överskrida gränserna för VM SKU.

Observera att OS-disken räknas som en hanterad disk med den minsta SKU som motsvarar dess kapacitet. En 1024 GB OS-disk motsvarar till exempel en P30-disk. Efemära OS-diskar och tillfälliga diskar har inga individuella diskgränser. De begränsas endast av de fullständiga vm-gränserna.

Icke-noll värden för att vänta eller avgqu-sz är också bra indikatorer på IO påstående.

## <a name="network"></a>Nätverk

Dessa verktyg mäter nätverksstatistik som dataflöde, överföringsfel och användning. Djupare analys kan exponera finkornig TCP-statistik om överbelastning och ignorerade paket.

### <a name="sar"></a>Sar

```
$ sar -n DEV [interval]
22:36:57        IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s   %ifutil
22:36:58      docker0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
22:36:58    azv604be75d832      1.00      9.00      0.06      1.04      0.00      0.00      0.00      0.00
22:36:58       azure0     68.00     79.00     27.79     52.79      0.00      0.00      0.00      0.00
22:36:58    azv4a8e7704a5b    202.00    207.00     37.51     21.86      0.00      0.00      0.00      0.00
22:36:58    azve83c28f6d1c     21.00     30.00     24.12      4.11      0.00      0.00      0.00      0.00
22:36:58         eth0    314.00    321.00     70.87    163.28      0.00      0.00      0.00      0.00
22:36:58    azva3128390bff     12.00     20.00      1.14      2.29      0.00      0.00      0.00      0.00
22:36:58    azvf46c95ddea3     10.00     18.00     31.47      1.36      0.00      0.00      0.00      0.00
22:36:58       enP1s1     74.00    374.00     29.36    166.94      0.00      0.00      0.00      0.00
22:36:58           lo      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
22:36:58    azvdbf16b0b2fc      9.00     19.00      3.36      1.18      0.00      0.00      0.00      0.00
```

`sar`är ett kraftfullt verktyg för ett brett spektrum av analyser. Även om det här exemplet använder sin förmåga att mäta nätverksstatistik, är det lika kraftfullt för att mäta CPU och minnesförbrukning. I det `sar` här `-n` exemplet anropas med flagga för att ange nyckelordet `DEV` (nätverksenhet) som visar nätverksdataflödet per enhet.

- Summan av `rxKb/s` `txKb/s` och är totalt dataflöde för en viss enhet. När det här värdet överskrider gränsen för det etablerade Azure NIC-nätverket, kommer arbetsbelastningar på datorn att uppleva ökad nätverksfördröjning.
- `%ifutil`mäter användningen för en viss enhet. När det här värdet närmar sig 100 % får arbetsbelastningarna ökad nätverksfördröjning.

```
$ sar -n TCP,ETCP [interval]
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

22:50:08     active/s passive/s    iseg/s    oseg/s
22:50:09         2.00      0.00     19.00     24.00

22:50:08     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
22:50:09         0.00      0.00      0.00      0.00      0.00

Average:     active/s passive/s    iseg/s    oseg/s
Average:         2.00      0.00     19.00     24.00

Average:     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
Average:         0.00      0.00      0.00      0.00      0.00
```

Den här anrop av `sar` använder nyckelorden `TCP,ETCP` för att undersöka TCP-anslutningar. Den tredje kolumnen i den sista raden, "återsändning", är antalet TCP-återsändningar per sekund. Höga värden för det här fältet anger en opålitlig nätverksanslutning. I Den första och tredje raden betyder "aktiv" en anslutning som kommer från den lokala enheten, medan "fjärr" indikerar en inkommande anslutning.  Ett vanligt problem på Azure är SNAT-portutmattning, vilket `sar` kan hjälpa till att identifiera. SNAT-portutmattning skulle manifesteras som höga "aktiva" värden, eftersom problemet beror på en hög frekvens av utgående, lokalt initierade TCP-anslutningar.

Som `sar` tar ett intervall, skrivs rullande utdata och sedan skriver ut sista rader av utdata som innehåller de genomsnittliga resultaten från åkallan.

### <a name="netstat"></a>Netstat

```
$ netstat -s
Ip:
    71046295 total packets received
    78 forwarded
    0 incoming packets discarded
    71046066 incoming packets delivered
    83774622 requests sent out
    40 outgoing packets dropped
Icmp:
    103 ICMP messages received
    0 input ICMP message failed.
    ICMP input histogram:
        destination unreachable: 103
    412802 ICMP messages sent
    0 ICMP messages failed
    ICMP output histogram:
        destination unreachable: 412802
IcmpMsg:
        InType3: 103
        OutType3: 412802
Tcp:
    11487089 active connections openings
    592 passive connection openings
    1137 failed connection attempts
    404 connection resets received
    17 connections established
    70880911 segments received
    95242567 segments send out
    176658 segments retransmited
    3 bad segments received.
    163295 resets sent
Udp:
    164968 packets received
    84 packets to unknown port received.
    0 packet receive errors
    165082 packets sent
UdpLite:
TcpExt:
    5 resets received for embryonic SYN_RECV sockets
    1670559 TCP sockets finished time wait in fast timer
    95 packets rejects in established connections because of timestamp
    756870 delayed acks sent
    2236 delayed acks further delayed because of locked socket
    Quick ack mode was activated 479 times
    11983969 packet headers predicted
    25061447 acknowledgments not containing data payload received
    5596263 predicted acknowledgments
    19 times recovered from packet loss by selective acknowledgements
    Detected reordering 114 times using SACK
    Detected reordering 4 times using time stamp
    5 congestion windows fully recovered without slow start
    1 congestion windows partially recovered using Hoe heuristic
    5 congestion windows recovered without slow start by DSACK
    111 congestion windows recovered without slow start after partial ack
    73 fast retransmits
    26 retransmits in slow start
    311 other TCP timeouts
    TCPLossProbes: 198845
    TCPLossProbeRecovery: 147
    480 DSACKs sent for old packets
    175310 DSACKs received
    316 connections reset due to unexpected data
    272 connections reset due to early user close
    5 connections aborted due to timeout
    TCPDSACKIgnoredNoUndo: 8498
    TCPSpuriousRTOs: 1
    TCPSackShifted: 3
    TCPSackMerged: 9
    TCPSackShiftFallback: 177
    IPReversePathFilter: 4
    TCPRcvCoalesce: 1501457
    TCPOFOQueue: 9898
    TCPChallengeACK: 342
    TCPSYNChallenge: 3
    TCPSpuriousRtxHostQueues: 17
    TCPAutoCorking: 2315642
    TCPFromZeroWindowAdv: 483
    TCPToZeroWindowAdv: 483
    TCPWantZeroWindowAdv: 115
    TCPSynRetrans: 885
    TCPOrigDataSent: 51140171
    TCPHystartTrainDetect: 349
    TCPHystartTrainCwnd: 7045
    TCPHystartDelayDetect: 26
    TCPHystartDelayCwnd: 862
    TCPACKSkippedPAWS: 3
    TCPACKSkippedSeq: 4
    TCPKeepAlive: 62517
IpExt:
    InOctets: 36416951539
    OutOctets: 41520580596
    InNoECTPkts: 86631440
    InECT0Pkts: 14
```

`netstat`kan introspect en mängd olika nätverk statistik, här åberopas med sammanfattning utgång. Det finns många användbara fält här beroende på problemet. Ett användbart fält i avsnittet TCP är "misslyckade anslutningsförsök". Detta kan vara en indikation på SNAT-portutmattning eller andra problem med att göra utgående anslutningar. En hög frekvens av återsända segment (även under avsnittet TCP) kan tyda på problem med paketleverans. 
