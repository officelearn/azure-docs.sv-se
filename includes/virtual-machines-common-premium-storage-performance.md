---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 88b19257a6a7d335e6a928a9eaf7526fbfd5b02e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75942791"
---
## <a name="application-performance-indicators"></a>Prestandaindikatorer för program

Vi bedömer om ett program fungerar bra eller inte med hjälp av prestandaindikatorer som, hur snabbt ett program bearbetar en användarbegäran, hur mycket data ett program bearbetar per begäran, hur många begäranden är en programbearbetning i en viss men, hur länge en användare måste vänta för att få ett svar efter att ha skickat sin begäran. De tekniska villkoren för dessa prestandaindikatorer är IOPS, Dataflöde eller Bandbredd samt Svarstid.

I det här avsnittet kommer vi att diskutera de gemensamma resultatindikatorerna i samband med Premium Storage. I följande avsnitt, Samla in programkrav, får du lära dig hur du mäter dessa resultatindikatorer för ditt program. Senare i Optimering av programprestanda får du lära dig om de faktorer som påverkar dessa prestandaindikatorer och rekommendationer för att optimera dem.

## <a name="iops"></a>IOPS

IOPS, eller Input/output Operations Per Sekund, är antalet begäranden som programmet skickar till lagringsdiskarna på en sekund. En indata-/utdataåtgärd kan läsas eller skrivas, sekventiell eller slumpmässig. Online Transaction Processing (OLTP) program som en online detaljhandeln webbplats måste bearbeta många samtidiga användarförfrågningar omedelbart. Användarbegärandena infogas och uppdatera intensiva databastransaktioner, som programmet måste bearbeta snabbt. Därför kräver OLTP-program mycket hög IOPS. Sådana program hanterar miljontals små och slumpmässiga IO-begäranden. Om du har ett sådant program måste du utforma programinfrastrukturen för att optimera för IOPS. I det senare avsnittet, *Optimera programprestanda*, diskuterar vi i detalj alla faktorer som du måste tänka på för att få hög IOPS.

När du kopplar en premiumlagringsdisk till en virtuell dator på hög nivå tilldelar Azure ett garanterat antal IOPS enligt diskspecifikationen. Till exempel en P50-disk tilldelar 7500 IOPS. Varje storlek för virtuella datorer på hög nivå har en specifik IOPS-gräns. En virtuell standard-GS5-dator har till exempel 80 000 IOPS-gräns.

## <a name="throughput"></a>Dataflöde

Dataflöde eller bandbredd är den mängd data som programmet skickar till lagringsdiskarna i ett angivet intervall. Om ditt program utför in-/utdataåtgärder med stora IO-enhetsstorlekar kräver det högt dataflöde. Datalagerprogram tenderar att utfärda genomsökningsintensiva åtgärder som kommer åt stora delar av data åt gången och utför ofta massåtgärder. Med andra ord kräver sådana program högre dataflöde. Om du har ett sådant program måste du utforma dess infrastruktur för att optimera för dataflöde. I nästa avsnitt diskuterar vi i detalj de faktorer du måste ställa in för att uppnå detta.

När du ansluter en premiumlagringsdisk till en virtuell dator med hög skala, etablerar Azure dataflöde enligt diskspecifikationen. Till exempel tilldelas en P50-disk ett dataflöde på 250 MB per sekund per disk. Varje storlek för virtuella datorer på hög nivå har en specifik gräns för genomflödet. Till exempel, den virtuella datorn Standard GS5 har en maximal genomströmning på 2 000 MB per sekund.

Det finns en relation mellan dataflöde och IOPS som visas i formeln nedan.

![Förhållande till IOPS och dataflöde](../articles/virtual-machines/linux/media/premium-storage-performance/image1.png)

Därför är det viktigt att bestämma det optimala dataflödet och IOPS-värdena som ditt program kräver. När du försöker optimera en, den andra också påverkas. I ett senare avsnitt, *Optimera programprestanda*, kommer vi att diskutera mer information om att optimera IOPS och Dataflöde.

## <a name="latency"></a>Svarstid

Svarstid är den tid det tar för ett program att ta emot en enda begäran, skicka den till lagringsdiskarna och skicka svaret till klienten. Detta är ett kritiskt mått på ett programs prestanda utöver IOPS och Dataflöde. Svarstiden för en premiumlagringsdisk är den tid det tar att hämta informationen för en begäran och kommunicera den tillbaka till ditt program. Premium Storage ger konsekventa låga svarstider. Premium diskar är utformade för att ge ensiffriga millisekunder svarstider för de flesta IO-åtgärder. Om du aktiverar ReadOnly-värdcache på premiumlagringsdiskar kan du få mycket lägre lässvarstid. Vi kommer att diskutera Disk Caching mer i detalj i senare avsnitt om *optimering av programprestanda*.

När du optimerar ditt program för att få högre IOPS och Dataflöde, kommer det att påverka svarstiden för ditt program. När du har justerat programmets prestanda utvärderar du alltid programmets svarstid för att undvika oväntat beteende med hög latens.

Följande kontrollplansåtgärder på hanterade diskar kan innebära förflyttning av disken från en lagringsplats till en annan. Detta är iscensatt via bakgrundskopia av data som kan ta flera timmar att slutföra, vanligtvis mindre än 24 timmar beroende på mängden data i diskarna. Under den tiden kan ditt program uppleva högre läsfördröjning än vanligt eftersom vissa läsningar kan omdirigeras till den ursprungliga platsen och kan ta längre tid att slutföra. Det finns ingen inverkan på skrivfördröjningen under den här perioden.

- Uppdatera lagringstypen.
- Koppla från och koppla en disk från en virtuell dator till en annan.
- Skapa en hanterad disk från en virtuell hårddisk.
- Skapa en hanterad disk från en ögonblicksbild.
- Konvertera ohanterade diskar till hanterade diskar.

## <a name="performance-application-checklist-for-disks"></a>Checklista för prestandaprogram för diskar

Det första steget i utformningen av högpresterande program som körs på Azure Premium Storage är att förstå prestandakraven för ditt program. När du har samlat in prestandakrav kan du optimera ditt program för att uppnå den mest optimala prestandan.

I föregående avsnitt förklarade vi de gemensamma resultatindikatorerna, IOPS, Dataflöde och Svarstid. Du måste identifiera vilka av dessa prestandaindikatorer som är avgörande för ditt program för att leverera önskad användarupplevelse. Till exempel spelar höga IOPS mest roll för OLTP-program som bearbetar miljontals transaktioner på en sekund. Medan högt dataflöde är avgörande för Data Warehouse-program som bearbetar stora mängder data på en sekund. Extremt låg latens är avgörande för realtidsprogram som live video streaming webbplatser.

Mät sedan de maximala prestandakraven för ditt program under hela dess livstid. Använd exempelchecklistan nedan som en början. Registrera de maximala prestandakraven under normala, högsta och lediga timmar arbetsbelastningsperioder. Genom att identifiera krav för alla arbetsbelastningar kommer du att kunna fastställa det övergripande prestandakravet för ditt program. Till exempel, den normala arbetsbelastningen för en e-handel webbplats kommer att vara de transaktioner som den betjänar under de flesta dagar i ett år. Den högsta arbetsbelastningen på webbplatsen kommer att vara de transaktioner den betjänar under semesterperioden eller särskilda försäljningsevenemang. Topparbetsbelastningen upplevs vanligtvis under en begränsad period, men kan kräva att ditt program skalar två eller flera gånger sin normala drift. Ta reda på kraven på 50 percentil, 90 percentil och 99 percentil. Detta hjälper till att filtrera bort eventuella extremvärden i prestandakraven och du kan fokusera dina ansträngningar på att optimera för rätt värden.

## <a name="application-performance-requirements-checklist"></a>Checklista för krav på programprestanda

| **Prestandakrav** | **50 Percentil** | **90 Percentil** | **99 Percentil** |
| --- | --- | --- | --- |
| Max. Transaktioner per sekund | | | |
| % Avläsningsåtgärder | | | |
| % skrivåtgärder | | | |
| % slumpmässiga åtgärder | | | |
| % Sekventiell verksamhet | | | |
| IO-begäran storlek | | | |
| Genomsnittligt dataflöde | | | |
| Max. Dataflöde | | | |
| Min. Svarstid | | | |
| Genomsnittlig svarstid | | | |
| Max. Processor | | | |
| Genomsnittlig CPU | | | |
| Max. Minne | | | |
| Genomsnittligt minne | | | |
| Ködjup | | | |

> [!NOTE]
> Du bör överväga att skala dessa siffror baserat på förväntad framtida tillväxt av ditt program. Det är en bra idé att planera för tillväxt i förväg, eftersom det kan vara svårare att ändra infrastrukturen för att förbättra prestanda senare.

Om du har ett befintligt program och vill flytta till Premium Storage skapar du först checklistan ovan för det befintliga programmet. Skapa sedan en prototyp av ditt program på Premium Storage och utforma programmet baserat på riktlinjer som beskrivs i *Optimera programprestanda* i ett senare avsnitt av det här dokumentet. I nästa artikel beskrivs de verktyg du kan använda för att samla in prestandamätningar.

### <a name="counters-to-measure-application-performance-requirements"></a>Räknare för att mäta krav på programprestanda

Det bästa sättet att mäta prestandakrav för ditt program, är att använda prestandaövervakningsverktyg som tillhandahålls av serverns operativsystem. Du kan använda PerfMon för Windows och iostat för Linux. Dessa verktyg fånga räknare som motsvarar varje åtgärd förklaras i ovanstående avsnitt. Du måste samla in värdena för dessa räknare när programmet kör sina normala arbetsbelastningar, topp- och lågtimmarsarbetsbelastningar.

PerfMon-räknarna är tillgängliga för processor, minne och varje logisk disk och fysisk disk på servern. När du använder premiumlagringsdiskar med en virtuell dator är de fysiska diskräknarna för varje premiumlagringsdisk och logiska diskräknare är för varje volym som skapas på premiumlagringsdiskarna. Du måste samla in värdena för diskarna som är värdar för programarbetsbelastningen. Om det finns en mappning mellan logiska och fysiska diskar kan du referera till fysiska diskräknare. I annat fall referera till de logiska diskräknarna. På Linux genererar iostat-kommandot en cpu- och diskanvändningsrapport. Diskutnyttjanderapporten innehåller statistik per fysisk enhet eller partition. Om du har en databasserver med data och loggar på separata diskar samlar du in dessa data för båda diskarna. I tabellen Nedan beskrivs räknare för diskar, processorer och minne:

| Räknare | Beskrivning | Perfmon | Iostat (på andra sätt) |
| --- | --- | --- | --- |
| **IOPS eller transaktioner per sekund** |Antal I/O-begäranden som utfärdats till lagringsdisken per sekund. |Diskläsningar/sek <br> Diskskrivningar per sekund |Tps <br> r/s <br> w/s |
| **Diskläsningar och skrivningar** |% av läs- och skrivåtgärder som utförs på disken. |% diskläsningstid <br> Procent diskskrivningstid |r/s <br> w/s |
| **Dataflöde** |Mängden data som läss från eller skrivs till disken per sekund. |Diskläs byte per sekund <br> Byte för diskskrivning/sek |kB_read/s <br> kB_wrtn/s |
| **Svarstid** |Total tid för att slutföra en disk-IO-begäran. |Genomsnittlig disk sek/läsning <br> Genomsnittlig disk sek/skrivning |Väntar <br> svctm (svenska) |
| **IO-storlek** |Storleken på I/O begär problem till lagringsdiskarna. |Genomsnittligt diskbyte/läsning <br> Genomsnittlig diskbyte/skrivning |avgrq-sz |
| **Ködjup** |Antal utestående I/O-begäranden som väntar på att läsas från eller skrivas till lagringsdisken. |Aktuell diskkölängd |avgqu-sz |
| **Max. Minne** |Mängden minne som krävs för att köra programmet smidigt |% använda dedikerade byte |Använd vmstat |
| **Max. Cpu** |Belopp CPU krävs för att köra programmet smidigt |% processortid |%util |

Läs mer om [iostat](https://linux.die.net/man/1/iostat) och [PerfMon](https://msdn.microsoft.com/library/aa645516.aspx).



## <a name="optimize-application-performance"></a>Optimera programmets prestanda

De viktigaste faktorerna som påverkar prestanda för ett program som körs på Premium Storage är IO-begäranden, VM-storlek, Diskstorlek, Antal diskar, diskcachening, flertrådning och ködjup. Du kan styra några av dessa faktorer med rattar som tillhandahålls av systemet. De flesta program kanske inte ger dig möjlighet att ändra IO-storlek och ködjup direkt. Om du till exempel använder SQL Server kan du inte välja IO-storlek och ködjup. SQL Server väljer de optimala IO-storleks- och ködjupsvärdena för att få ut så mycket prestanda som mest. Det är viktigt att förstå effekterna av båda typerna av faktorer på programmets prestanda, så att du kan etablera lämpliga resurser för att uppfylla prestandabehov.

I det här avsnittet läser du checklistan för programkrav som du har skapat för att identifiera hur mycket du behöver för att optimera programmets prestanda. Baserat på detta kommer du att kunna avgöra vilka faktorer från det här avsnittet du behöver ställa in. Om du vill se effekterna av varje faktor på programmets prestanda kör du benchmarkingverktyg på programinställningarna. Se benchmarking-artikeln, länkad i slutet, för steg för att köra vanliga benchmarkingverktyg på virtuella datorer i Windows och Linux.

### <a name="optimize-iops-throughput-and-latency-at-a-glance"></a>Optimera IOPS, dataflöde och svarstid i korthet

Tabellen nedan sammanfattar prestandafaktorer och de steg som krävs för att optimera IOPS, dataflöde och svarstid. De avsnitt som följer på den här sammanfattningen beskriver varje faktor är mycket mer djupgående.

Mer information om vm-storlekar och iops-, dataflöde och svarstid som är tillgängliga för varje typ av virtuell dator finns i [Storlekar på virtuella linux-datorer](../articles/virtual-machines/linux/sizes.md) eller [Windows VM-storlekar](../articles/virtual-machines/windows/sizes.md).

| &nbsp; | **IOPS** | **Dataflöde** | **Svarstid** |
| --- | --- | --- | --- |
| **Exempel på ett scenario** |Oltp-program för företag som kräver mycket höga transaktioner per sekund. |Enterprise Data-lagringsprogram som bearbetar stora mängder data. |Nära realtidsprogram som kräver omedelbara svar på användarnas önskemål, som onlinespel. |
| Prestandafaktorer | &nbsp; | &nbsp; | &nbsp; |
| **IO-storlek** |Mindre IO-storlek ger högre IOPS. |Större IO-storlek för att ge högre dataflöde. | &nbsp;|
| **Storlek på virtuell dator** |Använd en VM-storlek som erbjuder IOPS som är större än ditt programkrav. |Använd en VM-storlek med dataflödesgräns som är större än ditt programkrav. |Använd en vm-storlek som erbjuder skalbegränsningar som är större än ditt programkrav. |
| **Diskstorlek** |Använd en diskstorlek som erbjuder IOPS som är större än ditt programkrav. |Använd en diskstorlek med dataflödesgränsen som är större än ditt programkrav. |Använd en diskstorlek som erbjuder skalbegränsningar som är större än ditt programkrav. |
| **Begränsningar för vm- och diskskala** |IOPS-gränsen för den valda vm-storleken bör vara större än den totala IOPS som drivs av lagringsdiskar som är kopplade till den. |Dataflödesgränsen för den valda vm-storleken bör vara större än det totala dataflödet som drivs av premiumlagringsdiskar som är kopplade till den. |Skalningsgränser för den valda vm-storleken måste vara större än de totala skalgränserna för anslutna premiumlagringsdiskar. |
| **Disk Caching** |Aktivera ReadOnly Cache på premiumlagringsdiskar med Läs tunga åtgärder för att få högre Read IOPS. | &nbsp; |Aktivera ReadOnly Cache på premiumlagringsdiskar med färdiga tunga åtgärder för att få mycket låga läs-fördröjningar. |
| **Disk Striping** |Använd flera diskar och rand dem tillsammans för att få en kombinerad högre IOPS och Dataflöde gräns. Den kombinerade gränsen per virtuell dator bör vara högre än de kombinerade gränserna för kopplade premiumdiskar. | &nbsp; | &nbsp; |
| **Randstorlek** |Mindre randstorlek för slumpmässigt litet IO-mönster som ses i OLTP-applikationer. Använd till exempel stripe-storleken på 64 KB för SQL Server OLTP-program. |Större randstorlek för sekventiellt stort IO-mönster som visas i Data Warehouse-program. Använd till exempel 256 KB-stripe-storlek för SQL Server Data warehouse-program. | &nbsp; |
| **Flertrådsteknik** |Använd flertrådsteknik för att skicka ett högre antal begäranden till Premium Storage som leder till högre IOPS och Dataflöde. På SQL Server anger du till exempel ett högt MAXDOP-värde för att tilldela fler processorer till SQL Server. | &nbsp; | &nbsp; |
| **Ködjup** |Större ködjup ger högre IOPS. |Större ködjup ger högre dataflöde. |Mindre ködjup ger lägre svarstider. |

## <a name="nature-of-io-requests"></a>Typ av IO-förfrågningar

En IO-begäran är en enhet för indata-/utdataåtgärd som ditt program kommer att utföra. Identifiera vilken typ av IO-begäranden, slumpmässiga eller sekventiella, läsa eller skriva, små eller stora, hjälper dig att avgöra prestandakraven för ditt program. Det är viktigt att förstå vilken typ av IO-begäranden har, att fatta rätt beslut när du utformar din programinfrastruktur. IOs måste fördelas jämnt för att uppnå bästa möjliga prestanda.

IO storlek är en av de viktigaste faktorerna. I/O-storleken är storleken på den begäran om indata-/utdataåtgärd som genereras av ditt program. I/o-storleken har en betydande inverkan på prestanda, särskilt på IOPS och bandbredd som programmet kan uppnå. Följande formel visar förhållandet mellan IOPS, IO-storlek och Bandbredd/dataflöde.  
    ![](media/premium-storage-performance/image1.png)

Vissa program kan du ändra sin IO-storlek, medan vissa program inte. SQL Server bestämmer till exempel själva den optimala I/O-storleken och ger inte användarna några rattar för att ändra den. Å andra sidan tillhandahåller Oracle en parameter som kallas [DB\_BLOCK\_SIZE](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) med vilken du kan konfigurera I/O-begärandens storlek på databasen.

Om du använder ett program, som inte tillåter dig att ändra IO-storleken, använder du riktlinjerna i den här artikeln för att optimera prestanda-KPI:n som är mest relevant för ditt program. Exempel:

* Ett OLTP-program genererar miljontals små och slumpmässiga IO-begäranden. För att hantera dessa typer av IO-begäranden måste du utforma programinfrastrukturen för att få högre IOPS.  
* Ett datalagringsprogram genererar stora och sekventiella IO-begäranden. Om du vill hantera dessa typer av I/O-begäranden måste du utforma programinfrastrukturen för att få högre bandbredd eller dataflöde.

Om du använder ett program, som gör att du kan ändra IO-storleken, använder du den här tumregeln för IO-storleken utöver andra prestandariktlinjer.

* Mindre IO-storlek för att få högre IOPS. Till exempel 8 KB för ett OLTP-program.  
* Större IO-storlek för att få högre bandbredd/dataflöde. Till exempel 1024 KB för ett informationslagerprogram.

Här är ett exempel på hur du kan beräkna IOPS och Dataflöde/Bandbredd för ditt program. Överväg ett program med en P30-disk. Den maximala IOPS och Dataflöde / bandbredd en P30-disk kan uppnå är 5000 IOPS och 200 MB per sekund respektive. Nu, om ditt program kräver maximal IOPS från P30-disken och du använder en mindre IO-storlek som 8 KB, den resulterande bandbredd du kommer att kunna få är 40 MB per sekund. Men om ditt program kräver maximalt dataflöde/bandbredd från P30-disken och du använder en större IO-storlek som 1024 KB blir den resulterande IOPS mindre, 200 IOPS. Ställ därför in IO-storleken så att den uppfyller både programmets IOPS- och Dataflödes-/bandbreddskrav. I följande tabell sammanfattas de olika IO-storlekarna och motsvarande IOPS och Dataflöde för en P30-disk.

| Ansökan krav | I/O-storlek | IOPS | Dataflöde/bandbredd |
| --- | --- | --- | --- |
| Maximalt IOPS |8 kB |5 000 |40 MB per sekund |
| Maximalt dataflöde |1024 KB |200 |200 MB per sekund |
| Max dataflöde + hög IOPS |64 kB |3,200 |200 MB per sekund |
| Max IOPS + högt genomflöde |32 KB |5 000 |160 MB per sekund |

Om du vill få IOPS och bandbredd högre än det maximala värdet för en enda premiumlagringsdisk använder du flera premiumdiskar som är randiga tillsammans. Till exempel rand två P30-diskar för att få en kombinerad IOPS på 10.000 IOPS eller en kombinerad genomströmning på 400 MB per sekund. Som förklaras i nästa avsnitt måste du använda en VM-storlek som stöder den kombinerade disken IOPS och Dataflöde.

> [!NOTE]
> När du ökar antingen IOPS eller Dataflöde den andra också ökar, se till att du inte träffar dataflöde eller IOPS gränser för disken eller den virtuella datorn när du ökar antingen en.

Om du vill se effekterna av I/O-storlek på programmets prestanda kan du köra benchmarkingverktyg på din virtuella dator och diskar. Skapa flera testkörningar och använd olika IO-storlekar för varje körning för att se effekten. Mer information finns i benchmarkingartikeln, länkad i slutet.

## <a name="high-scale-vm-sizes"></a>Vm-storlekar i hög skala

När du börjar utforma ett program, en av de första sakerna att göra är, välja en virtuell dator som värd för ditt program. Premium Storage levereras med storskaliga VM-storlekar som kan köra program som kräver högre beräkningskraft och en hög lokal disk-I/O-prestanda. Dessa virtuella datorer ger snabbare processorer, ett högre förhållande mellan minne och kärna och en SSD (Solid State Drive) för den lokala disken. Exempel på virtuella datorer med hög skala som stöder Premium Storage är virtuella datorer i DS- och GS-serien.

Virtuella datorer med hög skala finns i olika storlekar med ett annat antal CPU-kärnor, minne, operativsystem och tillfällig diskstorlek. Varje vm-storlek har också maximalt antal datadiskar som du kan koppla till den virtuella datorn. Därför påverkar den valda vm-storleken hur mycket bearbetning, minne och lagringskapacitet som är tillgänglig för ditt program. Det påverkar också beräknings- och lagringskostnaden. Nedan finns till exempel specifikationerna för den största VM-storleken i en DS-serie och en GS-serie:

| Storlek på virtuell dator | Processorkärnor | Minne | VM diskstorlekar | Max. datadiskar | Cachestorlek | IOPS | IO-begränsningar för bandbreddscachen |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS14 |16 |112 GB |OS = 1023 GB <br> Lokal SSD = 224 GB |32 |576 GB |50 000 IOPS <br> 512 MB per sekund |4 000 IOPS och 33 MB per sekund |
| Standard_GS5 |32 |448 GB |OS = 1023 GB <br> Lokal SSD = 896 GB |64 |4224 GB |80 000 IOPS <br> 2 000 MB per sekund |5 000 IOPS och 50 MB per sekund |

Om du vill visa en fullständig lista över alla tillgängliga Azure VM-storlekar läser du [storlekarna](../articles/virtual-machines/windows/sizes.md) för virtuella datorer för Windows eller [Linux VM](../articles/virtual-machines/linux/sizes.md). Välj en vm-storlek som kan uppfylla och skala till dina önskade programprestandakrav. Dessutom bör du ta hänsyn till följande viktiga överväganden när du väljer VM-storlekar.

*Skalbegränsningar*  
De maximala IOPS-gränserna per virtuell dator och disk är olika och oberoende av varandra. Se till att programmet kör IOPS inom gränserna för den virtuella datorn samt de premiumdiskar som är kopplade till den. Annars uppstår begränsning av programprestanda.

Anta till exempel att ett programkrav är högst 4 000 IOPS. För att uppnå detta etablerar du en P30-disk på en DS1 VM. P30-disken kan leverera upp till 5 000 IOPS. DS1-datorn är dock begränsad till 3 200 IOPS. Följaktligen begränsas programprestanda av den virtuella datorns gräns på 3 200 IOPS och prestanda försämras. För att förhindra den här situationen väljer du en virtuell dator och diskstorlek som båda uppfyller programkraven.

*Kostnad för drift*  
I många fall är det möjligt att din totala driftskostnad med Premium Storage är lägre än att använda standardlagring.

Tänk dig till exempel ett program som kräver 16 000 IOPS. För att uppnå den här\_prestandan behöver du en standard D14 Azure IaaS VM, som kan ge en maximal IOPS på 16 000 med 32 standardlagring 1 TB-diskar. Varje 1 TB standardlagringsdisk kan uppnå maximalt 500 IOPS. Den beräknade kostnaden för den här virtuella datorn per månad kommer att vara $1 570. Den månatliga kostnaden för 32 standard lagring diskar kommer att vara $ 1.638. Den beräknade totala månadskostnaden kommer att vara $ 3.208.

Men om du var värd för samma program på Premium Storage behöver du en mindre VM-storlek och färre premiumlagringsdiskar, vilket minskar den totala kostnaden. En\_virtuell standarddS13-dator kan uppfylla 16 000 IOPS-krav med fyra P30-diskar. DS13-datorn har en maximal IOPS på 25 600 och varje P30-disk har en maximal IOPS på 5 000. Totalt kan den här konfigurationen uppnå 5 000 x 4 = 20 000 IOPS. Den beräknade kostnaden för den här virtuella datorn per månad kommer att vara $1,003. Den månatliga kostnaden för fyra P30 premium lagring diskar kommer att vara $ 544,34. Den beräknade totala månadskostnaden kommer att vara $ 1.544.

Tabellen nedan sammanfattar kostnadsfördelningen för det här scenariot för standard- och Premium-lagring.

| &nbsp; | **Standard** | **Premium** |
| --- | --- | --- |
| **Kostnad för virtuell dator per månad** |$1,570.58 (Standard\_D14) |$1,003.66 (Standard\_DS13) |
| **Kostnad för diskar per månad** |$1,638.40 (32 x 1 TB diskar) |$544.34 (4 x P30 diskar) |
| **Total kostnad per månad** |$3,208.98 |1 544,34 $34 |

*Linux-distributioner*  

Med Azure Premium Storage får du samma prestandanivå för virtuella datorer som kör Windows och Linux. Vi stöder många smaker av Linux distributioner, och du kan se hela listan [här](../articles/virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Det är viktigt att notera att olika distributioner är bättre lämpade för olika typer av arbetsbelastningar. Du kommer att se olika prestandanivåer beroende på vilken arbetsbelastning som körs på. Testa Linux-distributioner med din ansökan och välj den som fungerar bäst.

När du kör Linux med Premium Storage kontrollerar du de senaste uppdateringarna om nödvändiga drivrutiner för att säkerställa hög prestanda.

## <a name="premium-storage-disk-sizes"></a>Premium lagring diskstorlekar

Azure Premium Storage erbjuder en mängd olika storlekar så att du kan välja en som bäst passar dina behov. Varje diskstorlek har en annan skalgräns för IOPS, bandbredd och lagring. Välj rätt Premium Storage Disk storlek beroende på applikationskrav och den storskaliga VM-storleken. Tabellen nedan visar diskstorlekar och deras kapacitet. P4-, P6-, P15-, P60-, P70- och P80-storlekarna stöds för närvarande endast för hanterade diskar.

[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Hur många diskar du väljer beror på vilken diskstorlek du väljer. Du kan använda en enda P50-disk eller flera P10-diskar för att uppfylla dina programkrav. Ta hänsyn till de överväganden som anges nedan när du gör valet.

*Skalningsgränser (IOPS och dataflöde)*  
IOPS- och Dataflödesgränserna för varje Premium-diskstorlek skiljer sig från skalbegränsningarna för den virtuella datorn. Kontrollera att den totala IOPS och Dataflöde från diskarna är inom skalgränser för den valda VM-storleken.

Om ett programkrav till exempel är högst 250 MB/sek Dataflöde och du använder en DS4 VM med en enda P30-disk. DS4-datorn kan ge upp till 256 MB/sek Dataflöde. En enda P30-disk har dock dataflödesgränsen på 200 MB/sek. Följaktligen kommer programmet att begränsas till 200 MB/sek på grund av diskgränsen. För att övervinna den här gränsen, etablera mer än en datadiskar till den virtuella datorn eller ändra storlek på diskarna till P40 eller P50.

> [!NOTE]
> Läsningar som betjänas av cachen ingår inte i disken IOPS och Dataflöde, vilket inte omfattas av diskgränser. Cachen har sin separata IOPS- och Dataflödesgräns per virtuell dator.
>
> Till exempel, inledningsvis dina läsningar och skrivningar är 60MB/sec och 40MB/sec respektive. Med tiden värms cachen upp och tjänar mer och mer av läsningarna från cachen. Sedan kan du få högre skrivdataflöde från disken.

*Antal diskar*  
Bestäm hur många diskar du behöver genom att bedöma programkrav. Varje vm-storlek har också en gräns för antalet diskar som du kan koppla till den virtuella datorn. Vanligtvis är detta dubbelt så många kärnor. Kontrollera att den virtuella datorns storlek du väljer kan stödja antalet diskar som behövs.

Kom ihåg att Premium Storage-diskarna har högre prestanda jämfört med standardlagringsdiskar. Om du migrerar ditt program från Azure IaaS VM med standardlagring till Premium Storage behöver du därför förmodligen färre premiumdiskar för att uppnå samma eller högre prestanda för ditt program.

## <a name="disk-caching"></a>Diskcachelagring

Virtuella datorer med hög skala som utnyttjar Azure Premium Storage har en cachelagringsteknik på flera nivåer som kallas BlobCache. BlobCache använder en kombination av RAM för virtuella datorer och lokala SSD för cachelagring. Den här cachen är tillgänglig för beständiga diskar för Premium Storage och de virtuella datorerna. Som standard är den här cacheinställningen inställd på Read/Write for OS-diskar och ReadOnly för datadiskar som finns på Premium Storage. Med diskcachening aktiverat på Premium Storage-diskarna kan de virtuella datorerna i hög skala uppnå extremt höga prestandanivåer som överstiger den underliggande diskens prestanda.

> [!WARNING]
> Diskcachening stöds inte för diskar 4 TiB och större. Om flera diskar är anslutna till den virtuella datorn stöder varje disk som är mindre än 4 TiB cachelagring.
>
> När du ändrar cacheinställningen för en Azure-disk så frånkopplas och återansluts måldisken. Om det är operativsystemdisken startas den virtuella datorn om. Stoppa alla program/tjänster som kan påverkas av det här avbrottet innan du ändrar inställningen för diskcachelagring.

Mer information om hur BlobCache fungerar finns i blogginlägget inside [Azure Premium Storage.](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/)

Det är viktigt att aktivera cache på rätt uppsättning diskar. Om du ska aktivera diskcachelagring på en premiumdisk eller inte beror på vilket arbetsbelastningsmönster som disken hanterar. Tabellen nedan visar standardcachen för os- och datadiskar.

| **Disktyp** | **Standardweacheinställning** |
| --- | --- |
| OS-disk |ReadWrite |
| Datadisk |ReadOnly |

Följande är de rekommenderade diskcacheinställningarna för datadiskar,

| **Inställning för diskcachelagring** | **rekommendation om när du ska använda den här inställningen** |
| --- | --- |
| Inget |Konfigurera värdcache som Ingen för skriv-bara och skriv-tunga diskar. |
| ReadOnly |Konfigurera värdcache som ReadOnly för skrivskyddade och skrivskyddade diskar. |
| ReadWrite |Konfigurera värdcachen som ReadWrite endast om programmet hanterar skrivningsbaserade data till beständiga diskar när det behövs. |

*ReadOnly*  
Genom att konfigurera ReadOnly-cachelagring på Premium Storage-datadiskar kan du uppnå låg läsfördröjning och få mycket hög Läs IOPS och Dataflöde för ditt program. Detta beror på två skäl,

1. Läsningar som utförs från cacheminnet, som finns på VM-minnet och lokala SSD, är mycket snabbare än läsningar från datadisken, som finns på Azure blob-lagring.  
1. Premium Storage räknar inte de läsningar som visas från cacheminnet, mot disken IOPS och Dataflöde. Därför kan ditt program uppnå högre totalt IOPS och Dataflöde.

*ReadWrite*  
Som standard har OS-diskarna ReadWrite-cachelagring aktiverad. Vi har nyligen lagt till stöd för ReadWrite cachelagring på datadiskar också. Om du använder ReadWrite-cachelagring måste du ha ett korrekt sätt att skriva data från cache till beständiga diskar. SQL Server hanterar till exempel skrivning av cachelagrade data till de beständiga lagringsdiskarna på egen hand. Om du använder ReadWrite-cache med ett program som inte hanterar beständiga data som krävs kan data gå förlorade om den virtuella datorn kraschar.

*Inget*  
För närvarande stöds **Ingen** endast på datadiskar. Det stöds inte på OS-diskar. Om du ställer in **Ingen** på en OS-disk åsidosätts det internt och ställer in den på **ReadOnly**.

Som ett exempel kan du tillämpa dessa riktlinjer på SQL Server som körs på Premium Storage genom att göra följande,

1. Konfigurera "ReadOnly" cache på premium lagring diskar som är värd för datafiler.  
   a.  Den snabba avläsningar från cachen sänker SQL Server-frågetiden eftersom datasidor hämtas mycket snabbare från cacheminnet jämfört med direkt från datadiskarna.  
   b.  Servering läser från cache, innebär att det finns ytterligare dataflöde tillgänglig från premium datadiskar. SQL Server kan använda det här ytterligare dataflödet för att hämta fler datasidor och andra åtgärder som säkerhetskopiering/återställning, batchbelastningar och indexrekningar.  
1. Konfigurera "Ingen" cache på premium lagring diskar som är värd för loggfiler.  
   a.  Loggfiler har främst skrivtunga operationer. Därför drar de inte nytta av ReadOnly-cachen.

## <a name="optimize-performance-on-linux-vms"></a>Optimera prestanda på virtuella Linux-datorer

För alla premium-SSD-enheter eller ultradiskar med cache inställd på **ReadOnly** eller **Ingen**måste du inaktivera "hinder" när du monterar filsystemet. Du behöver inte hinder i det här scenariot eftersom skrivningarna till premiumlagringsdiskar är hållbara för dessa cacheinställningar. När skrivbegäran har slutförts har data skrivits till det beständiga arkivet. Om du vill inaktivera "hinder" använder du någon av följande metoder. Välj den för filsystemet:
  
* Använd monteringsalternativet `barrier=none` **för reiserFS.** (Om du vill `barrier=flush`aktivera hinder använder du .)
* För **ext3/ext4**, för att `barrier=0` inaktivera hinder, använd monteringsalternativet. (Om du vill `barrier=1`aktivera hinder använder du .)
* Använd monteringsalternativet `nobarrier` för **XFS.** (Om du vill `barrier`aktivera hinder använder du .)
* För premiumlagringsdiskar med cache inställd på **ReadWrite**aktiverar du hinder för skrivhållbarhet.
* För att volymetiketter ska finnas kvar efter att du har startat om den virtuella datorn måste du uppdatera /etc/fstab med UUID-referenserna (Universally Unique Identifier) till diskarna. Mer information finns i [Lägga till en hanterad disk till en Virtuell Linux.](../articles/virtual-machines/linux/add-disk.md)

Följande Linux-distributioner har validerats för premium-SSD-enheter. För bättre prestanda och stabilitet med premium-SSD-enheter rekommenderar vi att du uppgraderar dina virtuella datorer till en av dessa versioner eller nyare. 

Vissa versioner kräver de senaste Linux Integration Services (LIS), v4.0, för Azure. Om du vill hämta och installera en distribution följer du länken i följande tabell. Vi lägger till bilder i listan när vi slutför valideringen. Våra valideringar visar att prestanda varierar för varje bild. Prestanda beror på arbetsbelastningsegenskaper och dina bildinställningar. Olika bilder är inställda för olika typer av arbetsbelastningar.

| Distribution | Version | Kärna som stöds | Information |
| --- | --- | --- | --- |
| Ubuntu | 12.04 eller nyare| 3.2.0-75.110+ | &nbsp; |
| Ubuntu | 14.04 eller nyare| 3.13.0-44.73+  | &nbsp; |
| Debian | 7.x, 8.x eller nyare| 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12 eller nyare| 3.12.36-38.1+ | &nbsp; |
| SUSE | SLES 11 SP4 eller nyare| 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+ eller nyare| 3.18.4+ | &nbsp; |
| CentOS | 6.5, 6.6, 6.7, 7.0 eller nyare| &nbsp; | [LIS4 krävs](https://www.microsoft.com/download/details.aspx?id=55106) <br> *Se anteckning i nästa avsnitt* |
| CentOS | 7.1+ eller nyare| 3.10.0-229.1.2.el7+ | [LIS4 rekommenderas](https://www.microsoft.com/download/details.aspx?id=55106) <br> *Se anteckning i nästa avsnitt* |
| Red Hat Enterprise Linux (RHEL) | 6,8+, 7,2+, eller nyare | &nbsp; | &nbsp; |
| Oracle | 6,0+, 7,2+, eller nyare | &nbsp; | UEK4 eller RHCK |
| Oracle | 7.0-7.1 eller nyare | &nbsp; | UEK4 eller RHCK med[LIS4](https://www.microsoft.com/download/details.aspx?id=55106) |
| Oracle | 6.4-6.7 eller nyare | &nbsp; | UEK4 eller RHCK med[LIS4](https://www.microsoft.com/download/details.aspx?id=55106) |

### <a name="lis-drivers-for-openlogic-centos"></a>LIS-drivrutiner för OpenLogic CentOS

Om du kör virtuella öppna centos-datorer kör du följande kommando för att installera de senaste drivrutinerna:

```
sudo yum remove hypervkvpd  ## (Might return an error if not installed. That's OK.)
sudo yum install microsoft-hyper-v
sudo reboot
```

I vissa fall kommandot ovan kommer att uppgradera kärnan också. Om en kernel-uppdatering krävs kan du behöva köra ovanstående kommandon igen efter omstart för att helt installera microsoft-hyper-v-paketet.


## <a name="disk-striping"></a>Diskremsning

När en virtuell dator med hög skala är ansluten med flera beständiga premiumlagringsdiskar kan diskarna skalas bort för att aggregera sina IOPs, bandbredd och lagringskapacitet.

I Windows kan du använda lagringsutrymmen för att ta med diskar. Du måste konfigurera en kolumn för varje disk i en pool. Annars kan den totala prestandan för stripe-volym vara lägre än förväntat, på grund av ojämn fördelning av trafik över diskarna.

Viktigt: Med hjälp av Serverhanterarens användargränssnitt kan du ange det totala antalet kolumner upp till 8 för en stripe-volym. När du ansluter fler än åtta diskar använder du PowerShell för att skapa volymen. Med PowerShell kan du ange antalet kolumner som är lika med antalet diskar. Om det till exempel finns 16 diskar i en enda randuppsättning. ange 16 kolumner i parametern *NumberOfColumns* i cmdleten *New-VirtualDisk* PowerShell.

På Linux använder du MDADM-verktyget för att stripe-diskar tillsammans. Detaljerade steg på striping diskar på Linux hänvisa till [Konfigurera Software RAID på Linux](../articles/virtual-machines/linux/configure-raid.md).

*Randstorlek*  
En viktig konfiguration i disk striping är randstorleken. Stripe-storleken eller blockstorleken är den minsta bit av data som programmet kan adressera på en stripe-volym. Vilken stripe-storlek du konfigurerar beror på vilken typ av program och dess begäranmönster. Om du väljer fel randstorlek kan det leda till IO-feljustering, vilket leder till försämrad prestanda för ditt program.

Om till exempel en I/o-begäran som genereras av ditt program är större än diskränderns storlek, skriver lagringssystemet det över stripe-enhetsgränser på mer än en disk. När det är dags att komma åt dessa data måste den söka över mer än en stripe-enheter för att slutföra begäran. Den kumulativa effekten av ett sådant beteende kan leda till betydande prestandaförsämring. Å andra sidan, om IO-begärandens storlek är mindre än randstorleken, och om den är slumpmässig till sin natur, kan IO-begäranden lägga upp på samma disk som orsakar en flaskhals och slutligen försämrar IO-prestanda.

Beroende på vilken typ av arbetsbelastning ditt program körs väljer du en lämplig randstorlek. För slumpmässiga små IO-begäranden använder du en mindre randstorlek. Medan för stora sekventiella IO-begäranden använder en större randstorlek. Ta reda på rekommendationerna för randstorlek för programmet som du kommer att köra på Premium Storage. För SQL Server konfigurerar du stripe-storlek på 64 kB för OLTP-arbetsbelastningar och 256 KB för datalagringsarbetsbelastningar. Läs [metodtips för prestanda för SQL Server på virtuella Azure-datorer](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md#disks-guidance) om du vill veta mer.

> [!NOTE]
> Du kan ta fram högst 32 premiumlagringsdiskar på en VIRTUELLD DS-serie och 64 premiumlagringsdiskar på en virtuell dator i GS-serien.

## <a name="multi-threading"></a>Flertrådning

Azure har utformat Premium Storage-plattformen för att vara enormt parallell. Därför uppnår ett flertrådat program mycket högre prestanda än ett entrådigt program. Ett flertrådat program delar upp sina uppgifter över flera trådar och ökar effektiviteten i körningen genom att använda den virtuella datorn och diskresurserna maximalt.

Om ditt program till exempel körs på en enda virtuell kärna med två trådar kan processorn växla mellan de två trådarna för att uppnå effektivitet. Medan en tråd väntar på en disk IO för att slutföra, kan processorn växla till den andra tråden. På detta sätt kan två trådar åstadkomma mer än en enda tråd skulle. Om den virtuella datorn har mer än en kärna minskar den drifttiden ytterligare eftersom varje kärna kan utföra aktiviteter parallellt.

Du kanske inte kan ändra hur ett off-the-shelf program implementerar enstaka tråd eller flertrådning. SQL Server kan till exempel hantera flera processorer och flera kärnor. SQL Server bestämmer dock under vilka förhållanden det kommer att utnyttja en eller flera trådar för att bearbeta en fråga. Den kan köra frågor och skapa index med flera trådar. För en fråga som innebär att ansluta stora tabeller och sortera data innan de återvänder till användaren, kommer SQL Server använder sannolikt flera trådar. En användare kan dock inte styra om SQL Server kör en fråga med en enda tråd eller flera trådar.

Det finns konfigurationsinställningar som du kan ändra för att påverka den här flertråds- eller parallellbearbetningen av ett program. Till exempel, i fallet med SQL Server är det den maximala graden av parallellism konfiguration. Med den här inställningen MAXDOP kan du konfigurera det maximala antalet processorer som SQL Server kan använda vid parallell bearbetning. Du kan konfigurera MAXDOP för enskilda frågor eller indexåtgärder. Detta är fördelaktigt när du vill balansera resurser i ditt system för ett prestandakritiskt program.

Anta till exempel att ditt program som använder SQL Server kör en stor fråga och en indexåtgärd samtidigt. Låt oss anta att du ville att indexåtgärden skulle vara mer högpresterande jämfört med den stora frågan. I så fall kan du ange att MAXDOP-värdet för indexåtgärden ska vara högre än MAXDOP-värdet för frågan. På så sätt har SQL Server fler processorer som den kan utnyttja för indexåtgärden jämfört med antalet processorer som den kan avsätta till den stora frågan. Kom ihåg att du inte styr antalet trådar som SQL Server ska använda för varje åtgärd. Du kan styra det maximala antalet processorer som dedikeras för flertrådsteknik.

Läs mer om [Grader av parallellism](https://technet.microsoft.com/library/ms188611.aspx) i SQL Server. Ta reda på sådana inställningar som påverkar flertrådning i ditt program och deras konfigurationer för att optimera prestanda.

## <a name="queue-depth"></a>Ködjup

Ködjupet eller kölängden eller köstorleken är antalet väntande I/O-begäranden i systemet. Värdet för ködjupet avgör hur många I/O-åtgärder som ditt program kan rada upp, vilka lagringsdiskarna ska bearbeta. Det påverkar alla tre programprestandaindikatorer som vi diskuterade i den här artikeln dvs.

Ködjup och flertrådning är nära besläktade. Värdet Ködjup anger hur mycket flertrådning som kan uppnås av programmet. Om ködjupet är stort kan programmet köra fler åtgärder samtidigt, med andra ord mer flertrådsteknik. Om ködjupet är litet, även om programmet är flertrådat, har det inte tillräckligt många begäranden uppradade för samtidig körning.

Vanligtvis kan du inte ändra ködjupet från hyllan, för om det anges felaktigt kommer det att göra mer skada än nytta. Program ställer in rätt värde för ködjup för att få optimal prestanda. Det är dock viktigt att förstå det här konceptet så att du kan felsöka prestandaproblem med ditt program. Du kan också observera effekterna av ködjup genom att köra benchmarkingverktyg på ditt system.

Vissa program innehåller inställningar för att påverka ködjupet. Till exempel maxdop -inställningen (maximal parallellitet) i SQL Server förklaras i föregående avsnitt. MAXDOP är ett sätt att påverka ködjup och flertrådning, även om det inte direkt ändrar värdet för ködjup för SQL Server.

*Högt ködjup*  
Ett högt ködjup ställer upp fler åtgärder på disken. Disken känner till nästa begäran i kön i förväg. Därför kan disken schemalägga åtgärder i förväg och bearbeta dem i en optimal sekvens. Eftersom programmet skickar fler begäranden till disken kan disken bearbeta mer parallella IOs. I slutändan kommer programmet att kunna uppnå högre IOPS. Eftersom programmet bearbetar fler begäranden ökar även programmets totala dataflöde.

Vanligtvis kan ett program uppnå maximalt dataflöde med 8-16 + utestående IOs per ansluten disk. Om ett ködjup är ett, är programmet inte driver tillräckligt IOs till systemet, och det kommer att bearbeta mindre mängd under en viss period. Med andra ord, mindre genomströmning.

I SQL Server informerar du SQL Server om du till exempel anger MAXDOP-värdet för en fråga till "4" sql server om att den kan använda upp till fyra kärnor för att köra frågan. SQL Server avgör vad som är bäst ködjupsvärde och antalet kärnor för frågekörningen.

*Optimalt ködjup*  
Mycket högt ködjupsvärde har också sina nackdelar. Om ködjupsvärdet är för högt försöker programmet köra mycket hög IOPS. Om inte programmet har beständiga diskar med tillräcklig etablerad IOPS kan detta påverka programdämnaderna negativt. Följande formel visar relationen mellan IOPS, svarstid och ködjup.  
    ![](media/premium-storage-performance/image6.png)

Du bör inte konfigurera ködjup till något högt värde, men till ett optimalt värde, som kan leverera tillräckligt med IOPS för programmet utan att påverka svarstider. Om till exempel programfördröjningen måste vara 1 millisekund är ködjupet som krävs för att uppnå 5 000 IOPS, QD = 5000 x 0,001 = 5.

*Ködjup för randig volym*  
För en stripe-volym, upprätthålla ett tillräckligt högt ködjup så att varje disk har ett toppködjup individuellt. Tänk dig till exempel ett program som skickar ett ködjup på 2 och det finns fyra diskar i randen. De två IO-begäranden går till två diskar och återstående två diskar kommer att vara inaktiva. Konfigurera därför ködjupet så att alla diskar kan vara upptagna. Formel nedan visar hur du bestämmer ködjupet för randiga volymer.  
    ![](media/premium-storage-performance/image7.png)

## <a name="throttling"></a>Begränsning

Azure Premium Storage-avsättningar anges antalet IOPS och Dataflöde beroende på den virtuella datorns storlekar och diskstorlekar du väljer. När ditt program försöker köra IOPS eller Dataflöde över dessa gränser för vad den virtuella datorn eller disken kan hantera, kommer Premium Storage att begränsa den. Detta manifesteras i form av försämrad prestanda i ditt program. Detta kan innebära högre svarstid, lägre dataflöde eller lägre IOPS. Om Premium Storage inte begränsar sig kan ditt program misslyckas helt genom att överskrida vad dess resurser kan uppnå. Så, för att undvika prestandaproblem på grund av begränsning, alltid etablera tillräckliga resurser för ditt program. Ta hänsyn till vad vi diskuterade i avsnitten VM-storlekar och diskstorlekar ovan. Benchmarking är det bästa sättet att ta reda på vilka resurser du behöver för att vara värd för ditt program.

## <a name="next-steps"></a>Nästa steg
