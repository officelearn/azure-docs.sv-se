---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/24/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4960ee485ac8c6b233eacc569cdac6748481887d
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2018
ms.locfileid: "50746807"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Azure Premium Storage: Design för hög prestanda

Den här artikeln innehåller riktlinjer för att skapa program med hög prestanda med hjälp av Azure Premium Storage. Du kan använda instruktionerna i det här dokumentet som kombineras med bästa praxis för prestanda gäller för tekniker som används av ditt program. För att visa riktlinjerna kan använda vi SQL Server på Premium Storage som ett exempel i hela dokumentet.

Medan vi bemöter prestanda scenarier för Storage-skiktet i den här artikeln behöver du optimera programnivån. Om du är värd för en SharePoint-servergrupp i Azure Premium Storage, kan du till exempel använda SQL Server-exempel från den här artikeln för att optimera databasservern. Dessutom kan optimera SharePoint-servergruppen webbserver och programserver att få de flesta prestanda.

Den här artikeln kommer att besvara följande vanliga frågor om hur du optimerar programprestanda på Azure Premium Storage

* Så här att mäta programprestanda?  
* Varför inte ser du hög prestanda?  
* Vilka faktorer påverkar programprestanda på Premium Storage?  
* Hur dessa faktorer påverkar prestanda för ditt program i Premium Storage?  
* Hur kan du optimera för IOPS, bandbredd och latens?  

Vi har angett dessa riktlinjer specifikt för Premium Storage eftersom arbetsbelastningar som körs på Premium-lagring med hög prestanda som är känsliga. Vi har lagt till exempel där det är lämpligt. Du kan också använda några av dessa riktlinjer för program som körs på virtuella IaaS-datorer med Standard Storage-diskar.

> [!NOTE]
> Ibland är något som verkar vara problem med prestandan faktiskt en flaskhalsar i nätverket. I sådana situationer bör du optimera din [nätverksprestanda](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md).
> Du bör kontrollera att den virtuella datorn har stöd för accelererat nätverk. Om den finns, kan du aktivera den även efter distribution på både [windows](../articles/virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) och [linux](../articles/virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms) virtuella datorer.

Innan du börjar, om du är nybörjare till Premium Storage, läsa den [Premium Storage: lagring med höga prestanda för Azure-Datorbelastningar](../articles/virtual-machines/windows/premium-storage.md) och [skalbarhet för lagring av Azure- och prestandamål](../articles/storage/common/storage-scalability-targets.md)artiklar.

## <a name="application-performance-indicators"></a>Nyckeltal för program

Vi utvärdera om ett program fungerar bra eller inte använder indikatorer som prestanda, hur snabbt en bearbetning av en användarbegäran, hur mycket data som ett program bearbetar per begäran, hur många begäranden bearbetas ett program i en specifik tidsperiod, hur lång tid en användare har väntetiden för att få ett svar när du skickar sin begäran. Termerna för de här nyckeltal är IOPS, dataflöde eller bandbredd och latens.

I det här avsnittet diskuterar vi vanliga nyckeltal i samband med Premium Storage. I följande avsnitt, samlar in programkrav, lära du dig att mäta dessa nyckeltal för ditt program. Senare i Optimera programmets prestanda lär du dig att de faktorer som påverkar dessa Prestandaindikatorer och rekommendationer för att optimera dem.

## <a name="iops"></a>IOPS

IOPS är antalet begäranden som programmet skickar till diskar med lagringsutrymme i en sekund. En i/o-åtgärd gick att läsa eller skriva sekventiella eller slumpmässig. OLTP-program som en onlinebutiker webbplats behöver bearbeta begäranden för många samtidiga användare direkt. Användare-förfrågningarna insert och uppdatera beräkningsintensiva databastransaktioner som programmet måste bearbeta snabbt. Därför kräver OLTP program mycket hög IOPS. Sådana program hantera miljontals små och slumpmässiga i/o-begäranden. Om du har ett sådant program, måste du utforma programinfrastruktur flerkanaliga IOPS. I senare avsnitt *optimera programprestanda*, beskrivs i detalj alla faktorer som du måste tänka på att få hög IOPS.

När du kopplar en premium-lagringsdisk till hög nivå VM, Azure-regler automatiskt ett garanterad antal IOPS enligt specifikationen disk. Till exempel etablerar en P50-disk 7500 IOPS. Varje hög skala VM-storleken har också en viss IOPS-gräns som den kan klara. En Standard virtuell GS5-dator har till exempel 80 000 IOPS begränsa.

## <a name="throughput"></a>Dataflöde

Dataflöde eller bandbredd är mängden data som ditt program skickar till diskar med lagringsutrymme i ett visst intervall. Om ditt program fungerar med stora i/o som är i/o-åtgärder, kräver hög genomströmning. Data warehouse program tenderar att utfärda genomsökning beräkningsintensiva aktiviteter som åtkomst till stora delar av data i taget och ofta utföra massåtgärder. Med andra ord kräver sådana program som högre dataflöde. Om du har ett sådant program, måste du utforma sin infrastruktur för att optimera för dataflöde. I nästa avsnitt diskuterar vi i detalj på faktorer som du måste justera för att uppnå detta.

När du koppla en premium-lagringsdisk till en hög skala VM, Azure-regler dataflöde enligt specifikationen för disken. Till exempel etablerar en P50-disk 250 MB per sekund disk dataflöde. Varje hög skala VM-storleken har också som specifika dataflödesgräns som den kan klara. Standard virtuella GS5-datorer har exempelvis ett maximalt dataflöde på 2 000 MB per sekund. 

Det finns en relation mellan dataflöde och IOPS, enligt nedanstående formel.

![](media/premium-storage-performance/image1.png)

Det är därför viktigt att fastställa optimal dataflöde och IOPS-värden som krävs för ditt program. När du försöker optimera en hämtar den andra också påverkas. I ett senare avsnitt *optimera programprestanda*, diskuteras i mer information om hur du optimerar IOPS och dataflöden.

## <a name="latency"></a>Svarstid

Svarstiden är den tid det tar ett program för att ta emot en begäran, skicka den till storage-diskar och skicka svar till klienten. Det här är ett viktigt mått på ett programs prestanda förutom IOPS och dataflöden. Svarstiden för en premium-lagringsdisk är den tid det tar att hämta information för en begäran och kommunicera tillbaka till programmet. Premium Storage erbjuder genomgående korta svarstider. Om du aktiverar cachelagring på premium-lagringsdiskar ReadOnly-värden, får du mycket kortare svarstider för läsning. Vi diskuterar diskcachelagring i detalj i senare avsnitt på *optimera programprestanda*.

När du optimerar ditt program kan få högre IOPS och dataflöde, påverkar svarstiden för programmet. Utvärdera alltid svarstiden för programmet för att undvika oväntade fördröjningar beteende efter justering om programmets prestanda.

## <a name="gather-application-performance-requirements"></a>Samla in prestanda programkrav

Det första steget i utforma högpresterande program som körs på Azure Premium Storage är att förstå prestandakraven för ditt program. Du kan optimera ditt program för att uppnå bästa möjliga prestanda när du samlar in prestandakrav.

I det föregående avsnittet beskrivs vi vanliga nyckeltal, IOPS, dataflöde och svarstid. Du måste identifiera vilka av dessa nyckeltal är viktiga för ditt program för att leverera önskad användarupplevelsen. Till exempel hög IOPS som betyder mest för OLTP program bearbeta flera miljoner transaktioner på en sekund. Högt dataflöde är kritiska för Data Warehouse-program som bearbetar stora mängder data på en sekund. Extremt låg svarstid är avgörande för realtidsprogram som live videoströmningstjänster webbplatser.

Mät sedan kraven för maximala prestanda för ditt program under hela dess livslängd. Använd exemplet checklistan nedan som en start. Anteckna den maximala prestandakrav under normal, tider med hög och låg belastning på nätverket arbetsbelastning punkter. Genom att identifiera krav för alla arbetsbelastningar nivåer, kommer du att kunna fastställa den övergripande kraven på prestanda för ditt program. Till exempel debiteras normala arbetsbelastningen för webbplatser för e-handel transaktioner fungerar under större delen av tiden under ett år. Den högsta arbetsbelastningen på webbplatsen kommer att transaktioner fungerar under Rean eller särskilda försäljning händelser. Den högsta arbetsbelastningen är vanligtvis erfarna under en begränsad period, men kan kräva att ditt program och skala två eller fler gånger dess normal drift. Ta reda på vilken 50: e percentilen, 90: e percentilen och 99: e percentilen. Detta hjälper till att filtrera bort några avvikare i prestandakraven och du kan fokusera på att optimera för rätt värden.

### <a name="application-performance-requirements-checklist"></a>Checklista för programmet prestanda krav

| **Prestandakrav** | **50: e percentilen** | **90: e percentilen** | **99: e percentilen** |
| --- | --- | --- | --- |
| Max. Transaktioner per sekund | | | |
| % Läsåtgärder | | | |
| % Skrivåtgärder | | | |
| Slumpmässig %-åtgärder | | | |
| Sekventiella %-åtgärder | | | |
| Storlek för i/o-begäran | | | |
| Genomsnittligt dataflöde | | | |
| Max. Dataflöde | | | |
| Min. Svarstid | | | |
| Genomsnittlig svarstid | | | |
| Max. Processor | | | |
| Processorgenomsnitt | | | |
| Max. Minne | | | |
| Genomsnittligt minne | | | |
| Ködjup | | | |

> [!NOTE]
> Du bör överväga att skala dessa siffror utifrån förväntade tillväxt i ditt program. Det är en bra idé att planera för tillväxt i förväg, eftersom det kan vara svårare att ändra infrastrukturen för att förbättra prestanda senare.

Om du har ett befintligt program och vill flytta till Premium Storage, skapa först checklista ovan för att det befintliga programmet. Sedan skapar en prototyp av ditt program på Premium Storage och utforma appen baserat på riktlinjer som beskrivs i *optimera programprestanda* i ett senare avsnitt i det här dokumentet. I nästa avsnitt beskrivs de verktyg som du kan använda för att samla in prestandamått.

Skapa en checklista som liknar ditt befintliga program för den aktuella typen. Med hjälp av Benchmarking verktyg kan du simulera arbetsbelastningarna och mäta prestanda på prototyp programmet. Se avsnittet om [Benchmarking](#benchmarking) vill veta mer. Genom att göra så att du kan avgöra om Premium Storage kan matcha eller överträffa dina prestandakrav för programmet. Du kan sedan implementera samma riktlinjer som för dina produktionsprogram.

### <a name="counters-to-measure-application-performance-requirements"></a>Prestandaräknare för att mäta prestanda programkrav

Det bästa sättet att mäta prestandakrav för ditt program är att använda övervakning av programprestanda verktyg som tillhandahålls av operativsystemet på servern. Du kan använda PerfMon för Windows och iostat för Linux. Dessa verktyg avbilda räknare som motsvarar varje mått som beskrivs i avsnittet ovan. När programmet körs dess normal, tider med hög och låg belastning på nätverket arbetsbelastningar måste du samla in värdena för dessa räknare.

Prestandaräknarna är tillgängliga för processor, minne, och varje logisk disk och fysisk disk för din server. När du använder premium storage-diskar med en virtuell dator, fysisk disk är för varje premium storage disk och logisk diskräknare anges för varje volym som skapats på premium-lagringsdiskar. Måste du samla in värden för de diskar som värd för din arbetsbelastning. Om det finns en en-till-en-mappning mellan logiska och fysiska diskar, kan du referera till fysisk diskräknare; Annars finns de logiska disk-räknarna. På Linux genererar kommandot iostat en processor- och diskresurser rapport över minnesanvändning. Diskanvändningsrapporten innehåller statistik per fysisk enhet eller partition. Om du har en databasserver med dess data och loggfiler på separata diskar kan du samla in dessa data för båda diskarna. Tabellen nedan beskrivs räknare för diskar, processor och minne:

| Räknare | Beskrivning | PerfMon | iostat |
| --- | --- | --- | --- |
| **IOPS eller transaktioner per sekund** |Antal i/o-begäranden som utfärdats till Lagringsdisken per sekund. |Diskläsningar/sek <br> Diskskrivningar/sek |tps <br> r/s <br> w/s |
| **Diskläsningar och skrivningar** |% Läsningar- och skrivåtgärder utföras på disken. |Läs Disktid i procent <br> Skriv Disktid i procent |r/s <br> w/s |
| **Dataflöde** |Mängden data som läses från eller skrivs till disken per sekund. |Disk – lästa byte/sek <br> Disk – skrivna byte/sek |kB_read/s <br> kB_wrtn/s |
| **Svarstid** |Total tid för att slutföra en disk-i/o-begäran. |Medel s/diskläsning <br> Medel s/diskskrivning |await <br> svctm |
| **I/o-storlek** |Storleken på i/o-begäranden att diskar med lagringsutrymme. |Genomsnittlig byte/diskläsning <br> Genomsnittlig Disk byte/skrivning |avgrq sz |
| **Ködjup** |Antalet utestående i/o-begäranden att vänta på att läsa från eller skrivs till Lagringsdisken. |Aktuell diskkölängd |avgqu sz |
| **Max. Minne** |Mängden minne som krävs för att köra programmet smidigt |% Allokerade byte som används |Använda vmstat |
| **Max. CPU** |Mycket CPU som krävs för att köra programmet smidigt |Tid i procent för processor |% util |

Läs mer om [iostat](https://linux.die.net/man/1/iostat) och [PerfMon](https://msdn.microsoft.com/library/aa645516.aspx).

## <a name="optimizing-application-performance"></a>Optimera prestanda

De viktigaste faktorerna som påverkar prestanda för ett program som körs på Premium Storage är typen av i/o-begäranden, VM-storlek, diskstorleken, antalet diskar, diskcachelagring, Multithreading och ködjup. Du kan styra några av de här faktorerna med rattar som tillhandahålls av systemet. De flesta program kanske inte ger ett alternativ för att ändra i/o-storlek och ködjup direkt. Om du använder SQL Server kan välja du inte det i/o-storlek och kön djupet. SQL Server väljer optimala i/o-storlek och kö djup värdena att få de flesta prestanda. Det är viktigt att förstå effekterna av båda typerna av faktorer på programprestanda, så att du kan etablera lämpliga resurser för att uppfylla prestandabehov.

I det här avsnittet avse program krav checklistan som du skapade för att identifiera hur mycket du behöver för att optimera programprestanda. Baserat på detta kommer du att kunna avgöra vilka faktorer från det här avsnittet måste du justera. Om du vill diskvittne effekterna av varje faktor på programmets prestanda, kör du prestandamätningsverktyg på programinstallationen. Referera till den [Benchmarking](#Benchmarking) i slutet av den här artikeln för steg för att köra vanliga prestandamätningsverktyg på Windows och Linux-datorer.

### <a name="optimizing-iops-throughput-and-latency-at-a-glance"></a>Optimera IOPS, dataflöde och svarstid i korthet

Tabellen nedan sammanfattas prestandafaktorer och stegen för att optimera IOPS, dataflöde och svarstid. Den här sammanfattningen i följande avsnitt beskriver varje faktor är mycket mer på djupet.

Mer information på VM-storlekar och på IOPS, dataflöde och svarstid som är tillgängliga för varje typ av virtuell dator finns i [Linux VM-storlekar](../articles/virtual-machines/linux/sizes.md) eller [Windows VM-storlekar](../articles/virtual-machines/windows/sizes.md).

| &nbsp; | **IOPS** | **Dataflöde** | **Svarstid** |
| --- | --- | --- | --- |
| **Exempelscenario** |Enterprise OLTP-program som kräver mycket hög transaktioner per sekund hastighet. |Enterprise Data warehousing programmet bearbetning av stora mängder data. |Nära realtid program som kräver direkta svar på användarförfrågningar som onlinespel. |
| Prestandafaktorer | &nbsp; | &nbsp; | &nbsp; |
| **I/o-storlek** |Mindre i/o-storlek ger högre IOPS. |Större i/o-storlek till ger högre dataflöde. | &nbsp;|
| **Storlek på virtuell dator** |Använd en VM-storlek som erbjuder IOPS som är större än behov. |Använda en VM-storlek med dataflödesgräns som är större än behov. |Använd en VM-storlek att erbjudanden skala gränser som är större än behov. |
| **Diskstorlek** |Använd en diskstorlek som erbjuder IOPS som är större än behov. |Använd en diskstorlek med dataflödesgräns som är större än behov. |Använd en diskstorlek att erbjudanden skala gränser som är större än behov. |
| **Virtuell dator och gränser för skalning av Disk** |IOPS-gränsen för valt VM-storleken måste vara större än totalt IOPS som styrs av en premium-lagringsdiskar som är kopplade till den. |Dataflödesgräns för valt VM-storleken måste vara större än totala dataflödet som styrs av en premium-lagringsdiskar som är kopplade till den. |Gränser för skalning av VM-storleken valt måste vara större än totala skalningsgränserna för anslutna premium storage-diskar. |
| **Diskcachelagring** |Aktivera skrivskyddad Cache på premium-lagringsdiskar med tung läsåtgärder att få högre Läs IOPS. | &nbsp; |Aktivera skrivskyddad Cache på premium-lagringsdiskar med redo tung åtgärder för att få mycket låg Läs svarstider. |
| **Disk Striping** |Använda flera diskar och stripe-dem tillsammans för att få en kombinerad högre IOPS och dataflöden gräns. Observera att den kombinerade gränsen per virtuell dator ska vara högre än de kombinerade gränserna för anslutna premium-diskar. | &nbsp; | &nbsp; |
| **Stripe-storlek** |Mindre stripe-storlek för slumpmässiga små i/o-mönster i OLTP-program. T.ex. använda stripe-storlek på 64KB för SQL Server OLTP-program. |Större stripe-storlek för sekventiella stora i/o-mönster i datalagret program. T.ex. använda 256KB stripe-storlek för SQL Server Data warehouse-program. | &nbsp; |
| **Flertrådsteknik** |Använd flertrådsteknik att skicka fler begäranden till Premium Storage som leder till högre IOPS och dataflöden. Ange till exempel en hög MAXDOP-värdet för att allokera fler processorer till SQL Server på SQL Server. | &nbsp; | &nbsp; |
| **Ködjup** |Större ködjup ger högre IOPS. |Större ködjup ger högre dataflöde. |Mindre ködjup ger kortare svarstider. |

## <a name="nature-of-io-requests"></a>Typen av i/o-begäranden

En i/o-begäran är en enhet av i/o-åtgärd som kommer att utföra ditt program. Identifierar typen av i/o-begäranden, slumpmässiga eller sekventiella, läsa eller skriva, små eller stora, hjälper dig att fastställa prestandakrav för för ditt program. Det är mycket viktigt att förstå vilken typ av i/o-begäranden, att fatta rätt beslut när du utformar din infrastruktur.

I/o-storleken är en av de viktigaste faktorerna. I/o-storleken är storleken på indata/utdata-begäran som genereras av programmet. I/o-storleken har en betydande inverkan på prestanda, särskilt på IOPS och bandbredd som programmet kan uppnå. Följande formel visar relationen mellan IOPS, i/o-storlek och bandbredd/dataflöde.  
    ![](media/premium-storage-performance/image1.png)

Vissa program kan du ändra deras i/o-storlek, även om vissa program inte. SQL Server anger du den optimala i/o-storleken själva och ger inte användare med alla rattar för att ändra den. Å andra sidan Oracle erbjuder en parameter med namnet [DB\_BLOCKERA\_storlek](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) med vilket du kan konfigurera i/o-begäran på databasens storlek.

Om du använder ett program, som inte tillåter att ändra i/o-storlek, använda riktlinjerna i den här artikeln för att optimera prestanda KPI som är mest relevant för ditt program. Exempel:

* En OLTP-program genererar miljontals små och slumpmässiga i/o-begäranden. Om du vill hantera dessa typ av i/o-begäranden, måste du utforma programinfrastrukturen för att få högre IOPS.  
* Program för informationslagerhantering genererar stora och sekventiella i/o-begäranden. Om du vill hantera dessa typ av i/o-begäranden, måste du utforma programinfrastrukturen för att få högre bandbredd eller dataflöde.

Om du använder ett program, där du kan ändra i/o-storlek, använda den här tumregel för i/o-storleken utöver andra prestandariktlinjer för

* Mindre i/o-storlek för att få högre IOPS. Exempel: 8 KB för en OLTP-program.  
* Större i/o-storlek för att få högre bandbredd/dataflöde. Till exempel 1024 KB för ett data warehouse-program.

Här är ett exempel på hur du kan beräkna IOPS och dataflöde/bandbredd för ditt program. Tänk dig ett program som använder en P30-disk. Det högsta IOPS och dataflöde/bandbredd en P30-disk kan uppnå är 5000 IOPS och 200 MB per sekund respektive. Om programmet kräver den högsta IOPS från P30-disk och du använder en mindre i/o-storlek som 8 KB, är den resulterande bandbredd som du kommer att kunna hämta nu 40 MB per sekund. Men om programmet kräver den maximala dataflöde/bandbredden från P30-disk och du använder en större i/o-storlek som 1024 KB, resulterande IOPS blir mindre, 200 IOPS. Finjustera därför i/o-storlek så att den uppfyller båda programmets krav på IOPS och dataflöde/bandbredd. Tabellen nedan beskriver de olika i/o-storlekarna och deras motsvarande IOPS och dataflöden för en P30-disk.

| Programkrav | I/o-storlek | IOPS | Dataflöde/bandbredd |
| --- | --- | --- | --- |
| Max IOPS |8 kB |5 000 |40 MB per sekund |
| Högsta dataflöde |1 024 kB |200 |200 MB per sekund |
| Max Throughput + hög IOPS |64 kB |3,200 |200 MB per sekund |
| Maximal IOPS och dataflöden |32 KB |5 000 |160 MB per sekund |

Hämta IOPS och bandbredd som är högre än det högsta värdet för en enskild premiumdisk för lagring med flera premium-diskar stripe används tillsammans. Till exempel stripe två P30 diskar för att få en kombinerad IOPS över 10 000 IOPS eller ett kombinerade dataflöde på 400 MB per sekund. Enligt beskrivningen i nästa avsnitt, måste du använda en VM-storlek som har stöd för det kombinerade disk-IOPS och dataflöden.

> [!NOTE]
> När du ökar IOPS eller den andra ökar även dataflödet, kontrollera att du inte når dataflöde eller IOPS-gränserna för disk- eller virtuell dator om du vill öka någon.

Om du vill diskvittne effekterna av i/o-storleken på programmets prestanda, kan du köra prestandamätningsverktyg på virtuella datorer och diskar. Skapa flera testkörningar och använda olika i/o-storleken för varje körning för att se effekten. Referera till den [Benchmarking](#Benchmarking) i slutet av den här artikeln för mer information.

## <a name="high-scale-vm-sizes"></a>Hög skala VM-storlekar

När du startar ett program, är en av de första sakerna att göra, Välj en virtuell dator som värd för ditt program. Premium Storage kommer med hög skala VM-storlekar som kan köra program som kräver högre beräkningskraft och en hög lokal disk i/o-prestanda. Dessa virtuella datorer ger snabbare processorer, högre minne-till-kärna-förhållande och en Solid-State Drive (SSD) för den lokala disken. Exempel på hög skala virtuella datorer stöd för Premium Storage är DS, DSv2 och GS-serien virtuella datorer.

Hög skala virtuella datorer finns i olika storlekar med olika antal CPU-kärnor, minne, OS och temporär diskstorlek. Varje VM-storlek har också maximalt antal datadiskar som kan bifogas till den virtuella datorn. Därför den valda VM-storleken påverkar hur mycket bearbetning, minne och lagringskapacitet som är tillgängliga för ditt program. Det påverkar också beräkningen och lagring kostnad. Nedan visas exempelvis specifikationerna för den största Virtuella storleken i DS-serien, DSv2-serien och GS-serien:

| Storlek på virtuell dator | Processorkärnor | Minne | VM-diskstorlekar | Max. Datadiskar | Cachestorlek | IOPS | Cache-i/o-gränser för bandbredd |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS14 |16 |112 GB |OS = 1 023 GB <br> Lokal SSD = 224 GB |32 |576 GB |50 000 IOPS <br> 512 MB per sekund |4 000 IOPS och 33 MB per sekund |
| Standard_GS5 |32 |448 GB |OS = 1 023 GB <br> Lokal SSD = 896 GB |64 |4224 GB |80 000 IOPS <br> 2 000 MB per sekund |5 000 IOPS och 50 MB per sekund |

Om du vill visa en fullständig lista över alla tillgängliga Azure VM-storlekar som avser [Windows VM-storlekar](../articles/virtual-machines/windows/sizes.md) eller [Linux VM-storlekar](../articles/virtual-machines/linux/sizes.md). Välj en VM-storlek som kan uppfylla och skala till dina prestandakrav för önskat program. Utöver detta, beakta följande viktiga överväganden när du väljer VM-storlekar.

*Gränser för skalning*  
Högsta IOPS-gränser per virtuell dator och per disk är olika och oberoende av varandra. Se till att programmet är som driver IOPS inom ramen för den virtuella datorn samt premiumdiskar som är kopplade till den. I annat fall begränsas programprestanda.

Anta att ett program som krävs är högst 4 000 IOPS exempelvis. Om du vill göra detta kan etablera du en P30-disk på en DS1 virtuell dator. P30-disk kan leverera upp till 5 000 IOPS. DS1 VM är dock begränsad till 3,200 IOPS. Följaktligen programprestandan kommer att begränsas av VM-gränsen vid 3,200 IOPS och det blir försämrade prestanda. Välj en storlek på virtuell dator och disk som ska båda uppfyller kraven för programmet för att undvika detta.

*Kostnaden för åtgärden*  
I många fall är det möjligt att den totala kostnaden för åtgärden som använder Premium Storage är lägre än med Standard-lagring.

Tänk dig ett program som kräver 16 000 IOPS. För att uppnå den här prestanda du behöver en Standard\_D14 Azure IaaS-dator, vilket kan ge högsta IOPS för 16 000 med 32 standardlagring 1 TB diskar. Varje 1TB standardlagring disk kan uppnå högst 500 IOPS. Den uppskattade kostnaden för den här virtuella datorn per månad är $1,570. Månadskostnaden för 32 standardlagringsdiskar blir $1,638. Den uppskattade totala månadskostnaden blir $3,208.

Men om du värd för samma program på Premium-lagring, måste en mindre VM-storlek och färre premium storage-diskar, vilket minskar den totala kostnaden. En vanlig\_DS13 VM kan uppfylla kravet 16 000 IOPS med hjälp av fyra P30-diskar. Den DS13 virtuella datorn har en högsta IOPS för 25,600 och varje P30-disk har en högsta IOPS för 5 000. Övergripande den här konfigurationen kan uppnå 5 000 x 4 = 20 000 IOPS. Den uppskattade kostnaden för den här virtuella datorn per månad är $1,003. Månadskostnaden för fyra P30 premium storage-diskar blir $544.34. Den uppskattade totala månadskostnaden blir $1,544.

Tabellen nedan sammanfattas kostnadsdata för det här scenariot för Standard och Premium Storage.

| &nbsp; | **Standard** | **Premium** |
| --- | --- | --- |
| **Kostnaden för virtuella datorer per månad** |$1,570.58 (standard\_D14) |$1,003.66 (standard\_DS13) |
| **Av diskarnas kostnad per månad** |$1,638.40 (32 x 1 TB diskar) |$544.34 (4 x P30 diskar) |
| **Övergripande kostnad per månad** |$3,208.98 |$1,544.34 |

*Linux-distributioner*  

Med Azure Premium Storage får du samma nivå av prestanda för virtuella datorer som kör Windows och Linux. Vi stöder många varianter av Linux-distributioner, och du kan se den fullständiga listan [här](../articles/virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Det är viktigt att Observera att olika distributioner lämpar sig bättre för olika typer av arbetsbelastningar. Du kan se olika nivåer av prestanda beroende på den distribution som körs din arbetsbelastning på. Testa Linux-distributioner med ditt program och välj det som fungerar bäst.

När du kör Linux med Premium Storage kan du kontrollera de senaste uppdateringarna om nödvändiga drivrutiner för att försäkra hög prestanda.

## <a name="premium-storage-disk-sizes"></a>Premiumlagringsdiskar med storlekarna

Azure Premium Storage erbjuder åtta GA-diskstorlekar och tre diskstorlekar som för närvarande är i förhandsversion. Varje diskstorleken har en annan skala gräns för IOPS, bandbredd och lagring. Välja rätt storlek på Premium-lagringsdisk beroende på kraven för programmet och storskaliga VM-storlek. Tabellen nedan visar storlekarna som elva diskar och deras funktioner. P4, P6, P15, P60, P70 och P80 storlekarna är för närvarande endast stöd för Managed Disks.

| Typen för Premium-diskar  | P4    | P6    | P10   | P15 | P20   | P30   | P40   | P50   | P60   | P70   | P80   |
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|
| Diskstorlek           | 32 giB | 64 giB | 128 GiB| 256 GB| 512 GB            | 1 024 giB (1 TiB)    | 2 048 giB (2 TiB)    | 4 095 giB (4 TiB)    | 8 192 giB (8 TiB)    | 16 384 giB (16 TiB)    | 32 767 giB (32 GiB)    |
| IOPS per disk       | 120   | 240   | 500   | 1100 | 2 300              | 5000              | 7500              | 7500              | 12 500              | 15 000              | 20,000              |
| Dataflöde per disk | 25 MiB per sekund  | 50 MiB per sekund  | 100 MiB per sekund |125 MiB per sekund | 150 MiB per sekund | 200 MiB per sekund | 250 MiB per sekund | 250 MiB per sekund | 480 MiB per sekund | 750 MiB per sekund | 750 MiB per sekund |

Hur många diskar som du väljer beror på disken storlek väljs. Du kan använda en enda P50-disk eller flera P10-diskar för att uppfylla dina behov. Väg in överväganden för användarkonton som anges nedan när du gör valet.

*Skalningsgränserna (IOPS och dataflöde)*  
Gränserna för IOPS och dataflöden för varje Premium-diskstorleken är olika och oberoende från gränserna för skalning av virtuella datorer. Se till att det totala antalet IOPS och dataflöde från diskarna är inom skalningsgränserna för den valda VM-storleken.

Om exempelvis en programkrav är högst 250 MB/s genomströmning och du använder en DS4-VM med en enda P30-disk. DS4-VM kan ge upp till 256 MB/s genomströmning. En enskild P30-disk har dock dataflödesgräns på 200 MB per sekund. Programmet kommer därför begränsade på 200 MB per sekund på grund av disk-gränsen. Om du vill lösa den här gränsen, etablera flera datadiskar till den virtuella datorn eller ändra storlek på diskarna till P40- eller P50.

> [!NOTE]
> Läsningar som hanteras av cachen ingår inte i disken IOPS och dataflöden och kan därför inte omfattas av diskgränser. Cache har dess separata gräns för IOPS och dataflöde per virtuell dator.
>
> Till exempel är ursprungligen din läsningar och skrivningar 60MB per sekund och 40MB per sekund. Framöver kommer cachen värmer upp och sitter i läsningar från cachen. Sedan får du högre skrivning dataflöde från disken.

*Antal diskar*  
Bestämma antalet diskar som du behöver genom att uppskatta programkrav. Varje VM-storlek har också en gräns för antalet diskar som du kan koppla till den virtuella datorn. Detta är vanligtvis två gånger antalet kärnor. Se till att virtuella datorstorlek som du väljer har stöd för antalet diskar som behövs.

Kom ihåg att Premium Storage-diskar har högre prestanda jämfört med Standard Storage-diskar. Därför, om du migrerar dina program från Azure IaaS-VM med standardlagring till Premium Storage, sannolikt måste färre premium disks för att uppnå samma eller högre prestanda för ditt program.

## <a name="disk-caching"></a>Diskcachelagring

Hög skala datorer som använder Azure Premium Storage har en cachelagringsteknik för flera nivåer som kallas BlobCache. BlobCache använder en kombination av den virtuella datorn RAM-minne och lokal SSD-lagring för cachelagring. Det här cacheminnet är tillgängligt för Premium Storage beständiga diskar och de lokala VM-diskarna. Den här cache-inställningen är som standard att läsa/skriva för OS-diskar och skrivskyddad för datadiskar som finns på Premium Storage. Hög skala virtuella datorer kan uppnå extremt höga prestanda som överstiger den underliggande diskprestandan med diskcachelagring aktiverad på Premium Storage-diskar.

> [!WARNING]
> Disk-cachelagring stöds endast för diskstorlekar på upp till 4 TiB.
> Om du ändrar cache-inställningen för en Azure-disk kopplas och bifogar måldisken igen. Om det är ingen operativsystemdisk startas den virtuella datorn. Stoppa alla program/tjänster som kan påverkas av den här avbrott innan du ändrar inställningen för disk-cache.

Mer information om hur BlobCache fungerar avser insidan [Azure Premium Storage](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/) blogginlägg.

Det är viktigt att aktivera cachen på rätt uppsättning diskar. Om du ska aktivera diskcachelagring på en premiumdisk eller inte beror på mönster för arbetsbelastningen som hanterar. Tabellen nedan visar standard cacheinställningar för operativsystem och datadiskar.

| **Disktyp** | **Standardinställning för Cache** |
| --- | --- |
| OS-disk |Läs/skriv |
| Datadisk |Skrivskyddad |

Följande är rekommenderade disk cacheinställningarna för datadiskar

| **Diskcachelagringstypen inställningen** | **Rekommendation om när du ska använda den här inställningen** |
| --- | --- |
| Ingen |Konfigurera värd-cache som None för lässkyddad och skrivintensiv diskar. |
| Skrivskyddad |Konfigurera värd-cache som skrivskyddad för skrivskyddade och läs-och diskar. |
| Läs/skriv |Konfigurera värd-cache som ReadWrite endast om ditt program hanterar korrekt skriva cachelagrade data till beständiga diskar vid behov. |

*Skrivskyddad*  
Genom att konfigurera skrivskyddad cachelagring i Premium Storage-data diskar kan du uppnå Läs svarstider och få mycket hög Läs IOPS och dataflöden för ditt program. Detta är på grund av två skäl

1. Läsningar utföras från cachen som finns på VM-minne och lokal SSD, är mycket snabbare än läsningar från datadisk som finns på Azure blob storage.  
1. Premium-lagring räknas inte läsningar som hämtats från cache för disk-IOPS och dataflöde. Därför är ditt program kunna uppnå högre totalt IOPS och dataflöden.

*ReadWrite*  
Som standard har OS-diskar ReadWrite-cachelagring aktiverat. Vi har lagt till stöd för ReadWrite-cachelagring på data samt diskar. Om du använder ReadWrite-cachelagring, måste du ha ett korrekt sätt att skriva data från cachen till beständiga diskar. Till exempel SQL Server hanterar skriva cachelagrade data till beständig lagringsdiskar på egen hand. Använda ReadWrite-cache med ett program som inte hanterar spara de nödvändiga data kan leda till dataförlust om den virtuella datorn går sönder.

Exempelvis kan du kan använda dessa riktlinjer för SQL Server på Premium Storage genom att göra följande

1. Konfigurera ”skrivskyddad” cache på premium-lagringsdiskar som är värd för filer.  
   a.  Ett snabbt läser från cache lägre frågetiden SQL Server eftersom datasidor är mycket snabbare hämtas från cachen jämfört med direkt från data diskar.  
   b.  Skickar läsningar från cache innebär ytterligare dataflöde är tillgänglig från premium datadiskar. SQL Server kan använda den här ytterligare dataflöde för att hämta fler datasidor och andra åtgärder som säkerhetskopiering/återställning, batch-belastning och index återskapas.  
1. Konfigurera ”None” cache på premium-lagringsdiskar som är värd för loggfilerna.  
   a.  Loggfilerna har främst skrivintensiv åtgärder. De därför inte dra nytta av ReadOnly-cachen.

## <a name="disk-striping"></a>Disk Striping

När en hög skalbarhet som virtuell dator är ansluten med flera premium storage beständiga diskar, diskarna kan vara stripe används tillsammans för att aggregera sina IOPs, bandbredd och lagringskapacitet.

På Windows, kan du använda lagringsutrymmen till stripe diskar tillsammans. Du måste konfigurera en kolumn för varje disk i en pool. I annat fall kan prestandan stripe-volym vara lägre än förväntat pga en ojämn fördelning av trafik för diskarna.

Viktigt: Använda Server Manager UI kan ange du det totala antalet kolumner upp till 8 för en stripe-volym. När du ansluter mer än 8 diskar, kan du använda PowerShell för att skapa volymen. Med hjälp av PowerShell, kan du ange antalet kolumner lika med antalet diskar. Exempel: om det finns 16 diskar i en enda stripe-uppsättning; Ange 16 kolumner i den *NumberOfColumns* -parametern för den *New-VirtualDisk* PowerShell-cmdlet.

På Linux, använder du verktyget MDADM till stripe diskar tillsammans. Detaljerade anvisningar på striping diskar på Linux finns [konfigurera programvaru-RAID på Linux](../articles/virtual-machines/linux/configure-raid.md).

*Stripe-storlek*  
En viktig konfigurationsinformation i disken striping är Stripestorleken. Stripe-storlek eller blockstorlek är det minsta segmentet som programmet kan lösa på en stripe-volym. Stripe-storlek som du konfigurerar beror på vilken typ av program och användningsmönster begäran. Om du väljer fel stripe-storlek kan leda det till att i/o-misspassning, vilket leder till försämrade prestanda.

Till exempel om en i/o-begäran som genererats av ditt program är större än den stripe skriver lagringssystemet den över stripe enhet gränser på mer än en disk. När är det dags att komma åt dessa data har att söka i mer än en stripe-enheter för att slutföra begäran. Den ackumulerade effekten av sådant beteende kan leda till betydande prestandaförsämring. Å andra sidan, om i/o-begäran storlek är mindre än stripe-storlek och om det är slumpmässigt i/o-begäranden kan lägga till på samma disk orsakar en flaskhals och slutligen minska i/o-prestanda.

Välj en storlek på lämplig stripe beroende på vilken typ av arbetsbelastning som ditt program körs. Använd en mindre storlek på stripe för slumpmässiga små i/o-begäranden. Medan för stora sekventiella i/o begäranden för att använda en större stripe-storlek. Ta reda på stripe storleksrekommendationer för programmet körs på Premium Storage. SQL Server och konfigurera stripe storlek på 64KB för OLTP-arbetsbelastningar och 256KB för Datalagerhantering. Se [prestandametodtips för SQL Server på Azure Virtual Machines](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md#disks-guidance) vill veta mer.

> [!NOTE]
> Du kan stripe-tillsammans högst 32 premium storage-diskar på virtuella datorer i DS-serien och 64 premium storage-diskar på virtuella datorer i GS-serien.

## <a name="multi-threading"></a>Flertrådsteknik

Azure har utformats för Premium Storage-plattformen för att vara massivt parallella. Ett flertrådiga program ger därför mycket högre prestanda än en single-threaded-programmet. Ett flertrådiga program delar upp sina uppgifter över flera trådar och ökar effektiviteten för den kan körningen genom att använda resurser för virtuell dator och disk till högsta.

Om ditt program körs på en enda kärna virtuell dator med två trådar, till exempel växla CPU mellan två trådar för att uppnå effektivitet. När en tråd väntar på en disk-i/o för att slutföra, kan Processorn växla till en annan tråd. På så sätt kan kan två trådar göra mer än en tråd skulle göra. Om den virtuella datorn har mer än en kärna, minskar ytterligare körtid eftersom varje kärna kan köra uppgifter parallellt.

Du kanske inte kan ändra hur ett färdigt program implementerar enda trådade eller flertrådsteknik. SQL Server är till exempel kan hantera flera processorer och flera kärnor. Dock beslutar SQL Server under vilka omständigheter det ska använda en eller flera trådar för att bearbeta en fråga. Det kan köra frågor och skapa index med hjälp av den flertrådighet. SQL Server att sannolikt använda flera trådar för en fråga som omfattar stora tabeller och sortera data innan den returneras till användaren. En användare kan dock styra om SQL Server kör en fråga med hjälp av en tråd eller flera trådar.

Det finns inställningar som du kan ändra vilket påverkar detta flertrådsteknik eller parallell bearbetning av ett program. När det gäller SQL Server kan det exempelvis är den högsta grad av parallellitet-konfigurationen. Den här inställningen som heter MAXDOP, kan du konfigurera det maximala antalet processorer som SQL Server kan använda när parallell bearbetning. Du kan konfigurera MAXDOP för enskilda frågor eller index-åtgärder. Det här är praktiskt när du vill att balansera resurser i systemet för ett prestanda kritiska program.

Anta exempelvis att programmet använder SQL Server körs på en stor fråga och en indexåtgärden på samma gång. Låt oss anta att du vill ha indexåtgärden att fungera bättre jämfört med stora frågan. I sådana fall kan du ange MAXDOP-värdet för indexåtgärden högre än MAXDOP-värdet för frågan. På så sätt kan har SQL Server fler processorer som den kan använda för indexåtgärden jämfört med antalet processorer som den kan dedikera till stora frågan. Kom ihåg att du inte styr antalet trådar som ska användas för varje åtgärd i SQL Server. Du kan styra det maximala antalet processorer som är avsedda för flertrådsteknik.

Läs mer om [grader av parallellitet](https://technet.microsoft.com/library/ms188611.aspx) i SQL Server. Ta reda på sådana inställningar som påverkar den flertrådighet i ditt program och deras konfigurationer för att optimera prestanda.

## <a name="queue-depth"></a>Ködjup

Ködjup eller Kölängd eller köstorlek är antalet väntande i/o-begäranden i systemet. Värdet för ködjup anger hur många i/o-åtgärder som programmet kan ordna, som diskar med lagringsutrymme kommer att bearbeta. Den påverkar alla tre program-nyckeltal som beskrevs i den här artikeln d.v.s., IOPS, dataflöde och svarstid.

Kö djup och flertrådsteknik är nära relaterade. Ködjup värdet anger hur mycket den flertrådighet som kan uppnås av programmet. Om det ködjup är stor, programmet kan köra flera åtgärder samtidigt, d.v.s. Mer flertrådsteknik. Om det ködjup är litet, även om programmet är flertrådat, har inte tillräckligt med begäranden som väntar på samtidig körning.

Normalt av hyllan program tillåter dig inte att ändra ködjup, eftersom om angetts felaktigt den kommer att göra mer skada än bra. Program ska ange rätt värde för ködjup för att få optimala prestanda. Det är dock viktigt att förstå detta begrepp så att du kan felsöka prestandaproblem med ditt program. Du kan också se effekterna av ködjup genom att köra prestandamätningsverktyg på datorn.

Vissa program ange inställningar för att påverka det ködjup. Till exempel beskrivs MAXDOP-inställningen för (högsta grad av parallellitet) i SQL Server i föregående avsnitt. MAXDOP är ett sätt att påverka ködjup och flertrådsteknik, även om den inte direkt ändras värdet ködjup för SQL Server.

*Hög ködjup*  
En hög ködjup linjer upp fler åtgärder för disken. Disken vet nästa förfrågan i meddelandekön förbereds i förväg. Följaktligen disken schemalägga åtgärder förbereds i förväg och bearbeta dem i en optimal sekvens. Eftersom programmet skickar fler begäranden till disken, kan disken bearbeta flera parallella IOs. Slutligen kommer programmet att kunna uppnå högre IOPS. Eftersom bearbetning av fler begäranden, ökar även den totala genomströmningen i programmet.

Vanligtvis ett program kan uppnå maximalt dataflöde med 8-16 + utestående I/o per ansluten disk. Om ett ködjup finns en tillräckligt med IOs att systemet gör inte att program och mindre mängd bearbetas inom en viss period. Med andra ord mindre dataflöde.

Till exempel i SQL Server meddelar MAXDOP-värdet för en fråga till ”4” SQL Server att den kan använda upp till fyra kärnor för att köra frågan. SQL Server avgör vad är bästa kö djup värdet och antalet kärnor för körningen av frågan.

*Optimalt ködjup*  
Mycket hög kö djup värdet har också sin nackdelar. Om kön djup värdet är för högt, försöker programmet att driva mycket hög IOPS. Om programmet har beständiga diskar med tillräckligt med etablerad IOPS, kan detta påverka program svarstider. Följande formel visar relationen mellan IOPS, latens och ködjup.  
    ![](media/premium-storage-performance/image6.png)

Konfigurera inte ködjup till ett högt värde, men ett optimalt värde, som kan leverera tillräckligt med IOPS för programmet utan att påverka svarstider. Till exempel om svarstid för programmet måste vara 1 millisekund, ködjup som krävs för att uppnå 5 000 IOPS är, Ködjup = 5000 x 0,001 = 5.

*Ködjup för stripe-volym*  
Behålla en tillräckligt hög ködjup för stripe-volymer, så att varje disk har en högsta ködjup individuellt. Anta exempelvis att ett program som skickar ett ködjup på 2 och det finns 4 diskar i stripe. Två i/o-begäranden skickas till två diskar och återstående två diskar blir inaktiv. Därför konfigurera ködjupet så att alla diskar kan vara upptagen. Formeln nedan visar hur du fastställer ködjup på stripe-volymer.  
    ![](media/premium-storage-performance/image7.png)

## <a name="throttling"></a>Begränsning

Azure Premium Storage-regler anges antalet IOPS och dataflöden för beroende på VM-storlekar och diskstorlekar som du väljer. När ditt program försöker öka IOPS eller dataflöde över gränserna för vad den virtuella datorn eller en disk som kan hantera den med begränsning av Premium Storage. Detta visar i form av försämrade prestanda i ditt program. Det kan innebära högre latens, lägre dataflöde eller lägre IOPS. Om Premium Storage inte begränsa, misslyckas programmet helt av som överskrider vad dess resurser är möjligt att uppnå. Så, för att undvika prestandaproblem på grund av begränsningar, alltid etablera tillräckligt med resurser för ditt program. Ta hänsyn till vilka beskrivits i VM-storlekar och Disk storlekar ovan. Prestandamätningar är det bästa sättet att ta reda på vilka resurser måste du vara värd för programmet.

## <a name="benchmarking"></a>Prestandatest

Prestandamätningar är processen för att simulera olika arbetsbelastningar på ditt program och mäta programprestanda för varje arbetsbelastning. Genom att följa anvisningarna i ett tidigare avsnitt, du har samlat in programkrav för prestanda. Du kan fastställa prestandanivåer som ditt program kan uppnå med Premium Storage genom att köra prestandamätningsverktyg på de virtuella datorerna som är värd för programmet. I det här avsnittet får du exempel på prestandamätningar en Standard DS14 virtuell dator som etablerats med Azure Premium Storage-diskar.

Vi har använt common prestandamätningsverktyg Iometer och FIO, för Windows och Linux respektive. Dessa verktyg skapar flera trådar som simulerar en produktionsliknande arbetsbelastning och mäta systemets prestanda. Med hjälp av verktyg kan du också konfigurera parametrar som block storlek och kön djup, som normalt inte kan ändra för ett program. Detta ger dig större flexibilitet att driva ut maximala prestanda i hög skala virtuella datorer med premium-diskar för olika typer av arbetsbelastningar för program. Läs mer om varje benchmarking verktyg finns [Iometer](http://www.iometer.org/) och [FIO](http://freecode.com/projects/fio).

Om du vill följa exemplen nedan, skapa en Standard DS14 virtuell dator och koppla 11 Premium Storage-diskar till den virtuella datorn. Konfigurera 10 diskar med värdcachelagring som ”None” och stripe-dem till en volym med namnet NoCacheWrites 11 diskar. Konfigurera värdcachelagring som ”skrivskyddad” på den återstående disken och skapa en volym med namnet CacheReads med den här disken. Med den här inställningen, kommer du att kunna se ut maximala prestanda för läsning och skrivning från en Standard DS14 virtuell dator. Detaljerade anvisningar om hur du skapar en DS14 virtuell dator med premium disks går du till [skapa och använda ett Premium Storage-konto för en virtuell dator datadisk](../articles/virtual-machines/windows/premium-storage.md).

*Värmer upp cachen*  
Disken med ReadOnly värdcachelagring kommer att kunna ge högre IOPS än gränsen på disk. Om du vill ha den här högsta läsprestanda från värd-cachen, måste först du värmt upp cachen för den här disken. Detta säkerställer att läsa IOs vilket benchmarking verktyg kommer att öka på CacheReads volym faktiskt når cachen och inte disken direkt. Cacheträffar resulterar i ytterligare IOPS från enkel cachen aktiverad disk.

> **Viktigt:**  
> Du måste värmt upp cachen innan du kör prestandamätningar, varje gång virtuella datorn startas om.

#### <a name="iometer"></a>Iometer

[Hämta verktyget Iometer](http://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download) på den virtuella datorn.

*Testa fil*  
Iometer används en testfil som lagras på volymen där du kör testet benchmarking. Den styr läsningar och skrivningar på den här Testfilen för att mäta disken IOPS och dataflöden. Iometer skapar den här Testfilen om du inte har angett något. Skapa en testfil i 200GB med namnet iobw.tst på CacheReads och NoCacheWrites volymer.

*Specifikationer för åtkomst*  
Specifikationerna för begäran i/o-storlek, % Läs/Skriv, % slumpmässiga/sekventiella konfigureras på fliken ”åtkomst specifikationer” i Iometer. Skapa en access-specifikation för var och en av de scenarier som beskrivs nedan. Skapa åtkomst-specifikationer och ”spara” med ett lämpligt namn som – RandomWrites\_8K, RandomReads\_8 kB. Välj den motsvarande specifikationen när du kör Testscenario.

Ett exempel på åtkomst specifikationer för maximal skriva IOPS scenariot visas nedan,  
    ![](media/premium-storage-performance/image8.png)

*Högsta IOPS Test specifikationer*  
Använd mindre begärandestorlek på för att demonstrera högsta IOPs. Använd 8K begärandestorlek och skapa specifikationerna för slumpmässiga skrivningar och läsningar.

| Åtkomst-specifikation | Begärandestorlek | Slumpmässig % | Läs % |
| --- | --- | --- | --- |
| RandomWrites\_8 kB |8 KB |100 |0 |
| RandomReads\_8 kB |8 KB |100 |100 |

*Maximalt dataflöde Test specifikationer*  
Använd större begärandestorlek för att demonstrera maximalt dataflöde. Använd 64 kB begärandestorlek och skapa specifikationerna för slumpmässiga skrivningar och läsningar.

| Åtkomst-specifikation | Begärandestorlek | Slumpmässig % | Läs % |
| --- | --- | --- | --- |
| RandomWrites\_64 kB |64 KB |100 |0 |
| RandomReads\_64 kB |64 KB |100 |100 |

*Iometer testet körs*  
Utför nedanstående steg värmt upp cache

1. Skapa två åtkomst specifikationer med värden som visas nedan,

   | Namn | Begärandestorlek | Slumpmässig % | Läs % |
   | --- | --- | --- | --- |
   | RandomWrites\_1 MB |1MB |100 |0 |
   | RandomReads\_1 MB |1MB |100 |100 |
1. Köra Iometer test för att initiera disk i cachen med följande parametrar. Använd tre trådar för målvolymen och ett ködjup på 128. Ange ”körningstiden” varaktighet för testet till 2hrs på fliken ”testa inställningar”.

   | Scenario | Målvolymen | Namn | Varaktighet |
   | --- | --- | --- | --- |
   | Initiera Disk i cachen |CacheReads |RandomWrites\_1 MB |2hrs |
1. Kör testet Iometer för uppvärmning disk i cachen med följande parametrar. Använd tre trådar för målvolymen och ett ködjup på 128. Ange ”körningstiden” varaktighet för testet till 2hrs på fliken ”testa inställningar”.

   | Scenario | Målvolymen | Namn | Varaktighet |
   | --- | --- | --- | --- |
   | Varma upp Cachedisk |CacheReads |RandomReads\_1 MB |2hrs |

När cachedisk värmas upp, kan du fortsätta med test-scenarier som anges nedan. Använd minst tre trådar för för att köra testet Iometer, **varje** rikta volym. För varje arbetstråd välja målvolymen, ange ködjup och välj en av de sparade test-specifikationerna som visas i tabellen nedan, för att köra motsvarande test-scenariot. Förväntat resultat visas också för IOPS och dataflöde när du kör dessa tester. En små i/o-storlek på 8KB och en hög ködjup på 128 används för alla scenarier.

| Testscenario | Målvolymen | Namn | Resultat |
| --- | --- | --- | --- |
| Max. Lästa IOPS |CacheReads |RandomWrites\_8 kB |50 000 IOPS |
| Max. Skriva IOPS |NoCacheWrites |RandomReads\_8 kB |64 000 IOPS |
| Max. Kombinerade IOPS |CacheReads |RandomWrites\_8 kB |100 000 IOPS |
| NoCacheWrites |RandomReads\_8 kB | &nbsp; | &nbsp; |
| Max. Lästa MB/s |CacheReads |RandomWrites\_64 kB |524 MB/sek |
| Max. Skriva MB/sek |NoCacheWrites |RandomReads\_64 kB |524 MB/sek |
| Kombinerade MB/sek |CacheReads |RandomWrites\_64 kB |1 000 MB per sekund |
| NoCacheWrites |RandomReads\_64 kB | &nbsp; | &nbsp; |

Nedan visas skärmdumpar av Iometer testresultat för kombinerade scenarier för IOPS och dataflöden.

*Kombinerade läsningar och skrivningar högsta IOPS*  
![](media/premium-storage-performance/image9.png)

*Kombinerade läsningar och skrivningar maximalt dataflöde*  
![](media/premium-storage-performance/image10.png)

### <a name="fio"></a>FIO

FIO är ett populärt verktyg till benchmark lagring på virtuella Linux-datorer. Det har flexibiliteten att välja olika i/o-storlek, sekventiella eller icke-sekventiell läsning och skriver. Den tio trådar eller processer för att utföra de angivna i/o-åtgärderna. Du kan ange vilken typ av i/o-åtgärder som varje arbetstråd måste utföra med hjälp av jobbfiler. Vi har skapat en jobbet fil per scenariot som illustreras i exemplen nedan. Du kan ändra specifikationerna i jobbfilerna för att jämföra olika arbetsbelastningar som körs på Premium-lagring. I exemplen är vi använder en Standard DS 14 virtuella datorer som kör **Ubuntu**. Använd samma inställningar som beskrivs i början av den [prestandamätningar avsnittet](#Benchmarking) och varma in cachen innan du kör benchmark-tester.

Innan du börjar [hämta FIO](https://github.com/axboe/fio) och installera den på den virtuella datorn.

Kör följande kommando för Ubuntu

```
apt-get install fio
```

Vi använder fyra trådar för att driva skrivåtgärder och fyra arbetstrådar för utveckla läsåtgärder på diskarna. Skriv arbetare Driver trafik på volymen ”nocache”, som har 10 diskar med cache som ställts in på ”Ingen”. Läs arbetare Driver trafik på volymen ”readcache”, som har 1 disk med cache inställd på ”skrivskyddad”.

*Maximal skrivåtgärder (IOPS)*  
Skapa jobb-fil med följande specifikationer att få maximal skriva IOPS. Ge den namnet ”fiowrite.ini”.

```
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[writer1]
rw=randwrite
directory=/mnt/nocache
[writer2]
rw=randwrite
directory=/mnt/nocache
[writer3]
rw=randwrite
directory=/mnt/nocache
[writer4]
rw=randwrite
directory=/mnt/nocache
```

Observera följande viktiga saker som överensstämmer med designriktlinjer som beskrivs i föregående avsnitt. Dessa specifikationer är avgörande för att driva högsta IOPS  

* En hög ködjup på 256.  
* En liten blockstorlek på 8 kB.  
* Flera trådar som utför slumpmässiga skrivningar.

Kör följande kommando för att sätta igång FIO testet i 30 sekunder  

```
sudo fio --runtime 30 fiowrite.ini
```

När testet körs kan kommer du att kunna se antalet skriva IOPS den virtuella datorn och levererar Premium-diskar. I exemplet nedan visas DS14 VM leverera dess högsta skriva IOPS-gränsen på 50 000 IOPS.  
    ![](media/premium-storage-performance/image11.png)

*Lästa IOPS på högsta*  
Skapa jobb-fil med följande specifikationer att få högsta Läs IOPS. Ge den namnet ”fioread.ini”.

```
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache
```

Observera följande viktiga saker som överensstämmer med designriktlinjer som beskrivs i föregående avsnitt. Dessa specifikationer är avgörande för att driva högsta IOPS

* En hög ködjup på 256.  
* En liten blockstorlek på 8 kB.  
* Flera trådar som utför slumpmässiga skrivningar.

Kör följande kommando för att sätta igång FIO testet i 30 sekunder

```
sudo fio --runtime 30 fioread.ini
```

När testet körs, kommer du att kunna se antalet lästa IOPS den virtuella datorn och levererar Premium-diskar. I exemplet nedan visas DS14 VM leverera fler än 64 000 IOPS för läsning. Det här är en kombination av disken och cache-prestanda.  
    ![](media/premium-storage-performance/image12.png)

*Maximalt läsa och skriva IOPS*  
Skapa jobbfilen med följande specifikationer att få maximal kombineras läsa och skriva IOPS. Ge den namnet ”fioreadwrite.ini”.

```
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer2]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer3]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer4]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
```

Observera följande viktiga saker som överensstämmer med designriktlinjer som beskrivs i föregående avsnitt. Dessa specifikationer är avgörande för att driva högsta IOPS

* En hög ködjup på 128.  
* En liten storlek på 4KB.  
* Flera trådar som utför slumpmässiga läsningar och skrivningar.

Kör följande kommando för att sätta igång FIO testet i 30 sekunder

```
sudo fio --runtime 30 fioreadwrite.ini
```

När testet körs, kommer du att kunna se antalet kombinerade läsa och skriva IOPS på den virtuella datorn och levererar Premium-diskar. I exemplet nedan visas DS14 VM att leverera fler än 100 000 kombinerade läsa och skriva IOPS. Det här är en kombination av disken och cache-prestanda.  
    ![](media/premium-storage-performance/image13.png)

*Maximalt kombineras dataflöde*  
För att få maximalt kombinerade läsa och skriva dataflöde, använda en större blockstorlek och stora ködjup med flera trådar som utför läsningar och skrivningar. Du kan använda en blockstorlek på 64KB och ködjup på 128.

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Premium Storage:

* [Premium Storage: Lagring med höga prestanda för Azure Virtual Machines-arbetsbelastningar](../articles/virtual-machines/windows/premium-storage.md)  

SQL Server-användare finns i artiklarna på Prestandametodtips för SQL Server:

* [Prestandametodtips för SQLServer på Azure virtuella datorer](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md)
* [Azure Premium Storage ger högsta prestanda för SQL Server i Azure VM](http://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)
