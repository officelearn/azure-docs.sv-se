# <a name="azure-premium-storage-design-for-high-performance"></a>Azure Premium Storage: Design för hög prestanda

Den här artikeln innehåller riktlinjer för att bygga program med Azure Premium-lagring med hög prestanda. Du kan använda instruktionerna i det här dokumentet som kombineras med prestandarelaterade metodtips gäller för tekniker som används av ditt program. För att illustrera riktlinjerna har vi använt SQL Server körs på Premium-lagring som exempel i hela dokumentet.

Medan vi adress prestanda scenarier för lagringsskikt i den här artikeln, behöver du optimera Applikationsnivån. Om du är värd för en SharePoint-grupp på Azure Premium-lagring, kan du till exempel använda SQL Server-exempel från den här artikeln för att optimera databasservern. Dessutom optimera SharePoint-servergruppen webbservern och få de flesta prestanda-programserver.

Den här artikeln hjälper dig att besvara följande frågor om hur du optimerar prestanda på Azure Premium-lagring

* Hur mäter programmets prestanda?  
* Varför du inte ser förväntade högpresterande?  
* Vilka faktorer som påverkar ditt programprestanda på Premium-lagring?  
* Hur påverkar prestanda för programmet på Premium-lagring av dessa faktorer?  
* Hur kan optimeras för IOPS, bandbredd och fördröjning?  

Vi har angett dessa riktlinjer för Premium-lagring eftersom arbetsbelastningar som körs på Premium-lagring är mycket känslig prestanda. Vi har samlat exempel där det behövs. Du kan också använda några av dessa riktlinjer för att program som körs på virtuella IaaS-datorer med standardlagring diskar.

Innan du börjar, om du är nybörjare till Premium-lagring, läsa den [Premium-lagring: högpresterande lagring för arbetsbelastningar på virtuella Azure](../articles/virtual-machines/windows/premium-storage.md) och [Azure Storage skalbarhets- och prestandamål](../articles/storage/common/storage-scalability-targets.md)artiklar.

## <a name="application-performance-indicators"></a>Nyckeltal för programmet
Vi bedöma om ett program fungerar bra eller inte använda prestanda symboler som, hur snabbt ett bearbetning av en användarbegäran, hur mycket data som bearbetar ett program per begäran, hur många begäranden som bearbetas ett program i en specifik tidsperiod, hur lång tid en användare har väntetiden för att få svar efter att ha skickat begäran. Termerna för dessa nyckeltal är IOPS, genomflöde eller bandbredd och svarstid.

I det här avsnittet diskuteras vanliga nyckeltal i samband med Premium-lagring. I följande avsnitt, samlar in programkrav lära du dig att mäta dessa nyckeltal för ditt program. Du kommer information om de faktorer som påverkar dessa nyckeltal och rekommendationer för att optimera dem senare i Optimera programprestanda.

## <a name="iops"></a>IOPS
IOPS är antalet begäranden som programmet skickar till diskar med lagringsutrymme i en sekund. Ett i/o-åtgärden gick att läsa eller skriva sekventiellt eller slumpmässigt. OLTP-program som en online retail-webbplats måste du bearbeta begäranden för många samtidiga användare omedelbart. Användaren begär är insert och uppdatera beräkningsintensiva databastransaktioner som programmet måste bearbeta snabbt. Därför kräver OLTP program mycket hög IOPS. Dessa program hanterar miljontals små och slumpmässiga i/o-begäranden. Om du har ett sådant program, måste du utforma infrastruktur för programmet att optimera för IOPS. I avsnittet senare *optimera programprestanda*, diskuterar vi i detalj de faktorer som du måste tänka på att få hög IOPS.

När du kopplar en premium storage disk till hög nivå VM, Azure tillhandahåller du en garanterad antalet IOPS enligt specifikationen disk. Till exempel etablerar en disk p 50 7500 IOPS. Varje hög skala VM-storlek har också en specifik IOPS-gräns som den kan klara. Till exempel har en Standard GS5 VM 80 000 IOPS begränsa.

## <a name="throughput"></a>Dataflöde
Genomströmning eller bandbredd är mängden data som programmet skickar till diskar med lagringsutrymme i ett visst intervall. Om ditt program fungerar i/o-åtgärder med större stora i/o, kräver hög genomströmning. Data warehouse program tenderar att utfärda beräkningsintensiva genomsökningen som åtkomst till stora delar av data i taget och utföra vanliga massåtgärder. Med andra ord kräver sådana program högre genomströmning. Om du har ett sådant program, måste du utforma infrastruktur för att optimera för genomströmning. I nästa avsnitt diskuterar vi i detalj de faktorer som du måste justera för att åstadkomma detta.

När du bifoga en premium storage disk till en virtuell dator med hög skala Azure tillhandahåller genomströmning enligt specifikationen för disken. Till exempel etablerar en disk p 50 250 MB per andra disken genomflöde. Varje hög skala VM-storlek har också som särskild genomströmningen gräns som den kan klara. Standard GS5 VM har till exempel en maximalt dataflöde 2 000 MB per sekund. 

Det finns en relation mellan genomflöde och IOPS enligt nedanstående formel.

![](media/premium-storage-performance/image1.png)

Det är därför viktigt att fastställa optimal genomströmning och IOPS-värden som krävs för ditt program. När du försöker att optimera en hämtar andra också påverkas. I ett senare avsnitt *optimera programprestanda*, diskuteras i mer information om hur du optimerar IOPS och genomflöde.

## <a name="latency"></a>Svarstid
Svarstiden är den tid det tar ett program för att ta emot en begäran, skickar den till diskar med lagringsutrymme och skicka svar till klienten. Detta är en kritisk mått av ett programs prestanda förutom IOPS och genomflöde. Svarstiden för en disk för premium-lagring är den tid det tar att hämta information för en begäran och lämna den tillbaka till ditt program. Premium-lagring ger genomgående korta svarstiderna. Om du aktiverar cachelagring på premium lagringsdiskar ReadOnly-värden, kan du hämta mycket lägre skrivskyddade latens. Diskuteras diskcachelagring i detalj i senare avsnitt på *optimera programprestanda*.

När du optimerar ditt program att hämta högre IOPS och genomflöde påverkas svarstiden för programmet. Efter justering om programmets prestanda kan du alltid utvärdera svarstiden för programmet för att undvika oväntade fördröjningar beteende.

## <a name="gather-application-performance-requirements"></a>Samla in kraven på programmets prestanda
Det första steget i utforma högpresterande program som körs på Azure Premium-lagring är att förstå prestandakraven för ditt program. Du kan optimera ditt program för att uppnå den mest optimala prestandan när du samlar in prestandakrav.

I föregående avsnitt beskrivs vi de vanliga nyckeltal IOPS, genomflöde och svarstid. Du måste identifiera vilken av dessa nyckeltal är viktiga i programmet för att leverera den önskade användarupplevelsen. Till exempel viktigaste höga IOPS OLTP program bearbetning av miljontals transaktioner på en sekund. Hög genomströmning är viktig för Data Warehouse-program som bearbetar stora mängder data på en sekund. Mycket låg latens är avgörande för realtidsprogram som direktuppspelad video streaming webbplatser.

Därefter Mät maximala prestandakraven för ditt program under hela sin livslängd. Använd exemplet checklista nedan som en start. Registrera kraven för maximala prestanda under normal, belastning och låg belastning på nätverket arbetsbelastning punkter. Genom att identifiera krav för alla arbetsbelastningar nivåer, kommer du att kunna fastställa den övergripande kraven på prestanda för programmet. Till exempel blir webbplatser för e-handel normal arbetsbelastning de transaktioner som den erbjuder under större delen av tiden till ett år. Arbetsbelastningen belastning på webbplatsen kommer att transaktioner som den erbjuder under köpstarka jul eller särskilda försäljning händelser. Belastning arbetsbelastningen är vanligtvis erfarna under en begränsad tid, men kan kräva att ditt program att skala två eller fler gånger dess normal drift. Ta reda på 50: e percentilen, 90: e percentilen och 99 percentil krav. Detta hjälper filtrerar ut eventuella avvikare i prestandakraven och du kan fokusera arbetet på Optimera för rätt värden.

**Checklista för programmets prestanda krav**

| **Krav på prestanda** | **50: e percentilen** | **90: e percentilen** | **99 percentil** |
| --- | --- | --- | --- |
| Max. Transaktioner per sekund | | | |
| % Läsåtgärder | | | |
| % Skrivåtgärder | | | |
| % Slumpmässiga åtgärder | | | |
| % Sekventiella operationer | | | |
| Storlek för i/o-begäran | | | |
| Genomsnittlig genomströmning | | | |
| Max. Dataflöde | | | |
| Min. Svarstid | | | |
| Genomsnittlig svarstid | | | |
| Max. Processor | | | |
| Processorgenomsnitt | | | |
| Max. Minne | | | |
| Genomsnittlig minne | | | |
| Ködjup | | | |

> [!NOTE]
> Du bör skalning talen utifrån förväntade tillväxt i ditt program. Det är en bra idé att planera för tillväxt i förväg, eftersom det kan vara svårare att ändra infrastrukturen för att förbättra prestanda senare.
>
>

Om du har ett befintligt program och vill övergå till Premium-lagring, först skapa checklista ovan för det befintliga programmet. Sedan skapar en prototyp av programmet på Premium-lagring och utforma program baserat på riktlinjer som beskrivs i *optimera programprestanda* i ett senare avsnitt av det här dokumentet. I nästa avsnitt beskrivs de verktyg som du kan använda för att samla in prestandamått.

Skapa en checklista liknar ditt befintliga program för den aktuella typen. Med hjälp av Benchmarking verktyg du simulera arbetsbelastningar och mäta prestanda hos prototyp-programmet. Se avsnittet om [Benchmarking](#benchmarking) vill veta mer. Genom att göra så att du kan fastställa om Premium-lagring kan matcha eller överskrider dina prestandakrav för programmet. Sedan kan du implementera samma riktlinjer för ditt produktionsprogram.

### <a name="counters-to-measure-application-performance-requirements"></a>Räknare för att mäta prestanda programkrav
Det bästa sättet att mäta prestandakraven för ditt program är att använda övervakning av programprestanda verktyg som tillhandahålls av operativsystemet på servern. Du kan använda PerfMon för Windows och iostat för Linux. Dessa verktyg avbilda räknare som motsvarar varje mått som beskrivs i avsnittet ovan. När programmet körs dess normal, belastning och låg belastning på nätverket arbetsbelastningar måste du samla in värdena för dessa räknare.

PerfMon-räknare är tillgängliga för processor, minne och varje logisk disk och fysisk disk för servern. När du använder premiumdiskar för lagring med en virtuell dator fysisk disk är för varje disk för premium-lagring och räknare för logisk disk är för varje volym som har skapats på diskar med premium-lagring. Du måste hämta värden för de diskar som värd för din arbetsbelastning i programmet. Om det finns en en-till-en-mappning mellan logiska och fysiska diskar, kan du referera till räknare för fysiska diskar; Annars finns räknare för logiska diskar. På Linux skapar kommandot iostat en rapport över processor- och diskresurser. Diskanvändningsrapporten innehåller statistik per fysisk enhet eller partition. Om du har en databasserver med dess data och loggfiler på separata diskar kan du samla in informationen för båda diskarna. Tabellen nedan beskrivs räknare för diskar, processor och minne:

| Räknare | Beskrivning | PerfMon | Iostat |
| --- | --- | --- | --- |
| **IOPS eller transaktioner per sekund** |Antalet i/o-begäranden som utfärdats till Lagringsdisken per sekund. |Diskläsningar/sek <br> Diskskrivningar/sek |transaktionsprogram <br> r/s <br> w/s |
| **Diskläsningar och skrivningar** |% av läsåtgärder och skrivåtgärder utföras på disken. |Läs Disktid i procent <br> Skriv Disktid i procent |r/s <br> w/s |
| **Dataflöde** |Mängd data läses från eller skrivs till disken per sekund. |Disk – lästa byte/sek <br> Disk – skrivna byte/sek |kB_read/s <br> kB_wrtn/s |
| **Svarstid** |Total tid för att slutföra en disk-i/o-begäran. |Medel s/diskläsning <br> Medel s/diskskrivning |await <br> svctm |
| **I/o-storlek** |Storleken på i/o-begäranden problem att diskar med lagringsutrymme. |Genomsnittligt antal byte/diskläsning <br> Genomsnittlig Disk byte/skrivning |avgrq sz |
| **Ködjup** |Antal utestående i/o-begäranden väntar på att läsas från eller skrivas till lagringsdisk. |Aktuell diskkölängd |avgqu sz |
| **Max. Minne** |Mängden minne som krävs för att köra programmet smidigt |% Använda dedikerade byte |Använd vmstat |
| **Max. CPU** |Mängden CPU som krävs för att köra programmet smidigt |% Processortid |% util |

Lär dig mer om [iostat](https://linux.die.net/man/1/iostat) och [PerfMon](https://msdn.microsoft.com/library/aa645516.aspx).

## <a name="optimizing-application-performance"></a>Optimera programprestanda för
De viktigaste faktorerna som påverkar prestanda för ett program som körs på Premium-lagring är natur av i/o-begäranden storlek på Virtuellt minne, diskutrymme, antalet diskar diskcachelagring, Multithreading och ködjup. Du kan kontrollera vissa av dessa faktorer med rattar som tillhandahålls av systemet. De flesta program kanske inte ger ett alternativ för att ändra i/o-storlek och ködjup direkt. Om du använder SQL Server kan välja du inte i/o-storlek och kön djup. SQL Server väljer optimala IO storlek och kön djup värden som ska få de flesta prestanda. Det är viktigt att förstå effekterna av båda typerna av faktorer på ditt programprestanda så att du kan etablera lämpliga resurser för att uppfylla prestandakraven.

I hela det här avsnittet avse program krav checklista som du skapade för att identifiera hur mycket du behöver att optimera programprestanda för ditt. Baserat på som kommer du att kunna avgöra vilka faktorer från det här avsnittet måste du justera. Om du vill diskvittne effekterna av varje faktor på programmets prestanda, köra benchmarking verktyg på programinstallationen. Referera till den [Benchmarking](#Benchmarking) avsnittet i slutet av den här artikeln steg för att köra vanliga benchmarking verktyg i Windows och Linux virtuella datorer.

### <a name="optimizing-iops-throughput-and-latency-at-a-glance"></a>Optimera IOPS, genomflöde och svarstid i korthet

Tabellen nedan sammanfattar prestandafaktorer och stegen för att optimera IOPS, genomflöde och svarstid. Den här sammanfattningen i följande avsnitt beskriver varje faktorn är mycket mer djup.

Mer information på VM-storlekar och på IOPS, genomflöde och svarstid som är tillgängliga för varje typ av VM finns [Linux VM-storlekar](../articles/virtual-machines/linux/sizes.md) eller [Windows VM-storlekar](../articles/virtual-machines/windows/sizes.md).

| &nbsp; | **IOPS** | **Dataflöde** | **Svarstid** |
| --- | --- | --- | --- |
| **Exempelscenario** |Enterprise OLTP-program som kräver mycket hög transaktioner per andra hastighet. |Enterprise Data warehousing programmet bearbetning stora mängder data. |Nära realtidsprogram som kräver omedelbar svar på användarförfrågningar som onlinespel. |
| Prestandafaktorer | &nbsp; | &nbsp; | &nbsp; |
| **I/o-storlek** |Mindre i/o-storlek ger högre IOPS. |Större i/o-storlek till ger högre genomströmning. | &nbsp;|
| **VM-storlek** |Använda en VM-storlek som erbjuder IOPS som är större än din programkrav. |Använda en VM-storlek med genomflödet gräns som är större än din programkrav. |Använda en VM-storlek att erbjudanden skala gränser som är större än din programkrav. |
| **Diskens storlek** |Använd storleken för en disk som erbjuder IOPS som är större än din programkrav. |Använd storleken för en disk med genomflödet gräns som är större än din programkrav. |Använd en diskstorlek så att erbjudanden skala gränser som är större än din programkrav. |
| **VM- och Skalningsgränser för Disk** |IOPS-gräns på VM-storlek valt ska vara större än IOPS totalt styrs av premium storage diskar som är anslutna till den. |Genomströmning gränsen på VM-storlek valt ska vara större än totala genomflödet som drivs av premium storage diskar som är anslutna till den. |Gränser för VM-storlek valt måste vara större än totala gränser för bifogade premium storage diskar. |
| **Cachelagring på disk** |Aktivera ReadOnly-cachen på diskar med premium-lagring med tunga läsåtgärder få högre Läs IOPS. | &nbsp; |Aktivera ReadOnly-cachen på diskar med premium-lagring med redo tunga åtgärder för att hämta Läs mycket låg latens. |
| **Disk-Striping** |Använda flera diskar och stripe-dem tillsammans för att få en kombinerad högre IOPS och genomströmning gräns. Observera att kombinerade gränsen per VM ska vara högre än bifogade premiumdiskar kombinerade gränser. | &nbsp; | &nbsp; |
| **Stripe-storlek** |Mindre stripe-storlek för slumpmässiga små i/o-mönster visas i OLTP-program. T.ex. använda stripe storlek på 64KB för SQL Server OLTP-program. |Större stripe-storlek för sekventiella stora i/o-mönster i datalagret program. T.ex. använda 256KB stripe-storlek för SQL Server-Data warehouse program. | &nbsp; |
| **Flertrådsteknik** |Använd flertrådsteknik push högre antal begäranden till Premium-lagring som kommer att leda till högre IOPS och genomflöde. Ange till exempel ett högt värde för MAXDOP att allokera fler processorer till SQL Server på SQL Server. | &nbsp; | &nbsp; |
| **Ködjup** |Större ködjup ger högre IOPS. |Större ködjup ger högre genomströmning. |Mindre ködjup ger lägre latens. |

## <a name="nature-of-io-requests"></a>Typ av i/o-begäranden
Ett i/o-begäran är en enhet av i/o-åtgärd som programmet kommer att utföra. Identifierar typ av i/o-begäranden, slumpmässiga eller sekventiella, hjälper läsa eller skriva små eller stora, dig att avgöra prestandakraven för ditt program. Det är viktigt att förstå arten av i/o-begäranden att fatta rätt beslut när du designar infrastrukturen för programmet.

I/o-storleken är en av de viktigaste faktorerna. I/o-storleken är storleken på indata-/ utdata-begäran som skapats av ditt program. I/o-storlek har en betydande inverkan på prestanda särskilt på IOPS och bandbredd som programmet är möjlig att uppnå. Följande formel visar relationen mellan IOPS, i/o-storlek och bandbredd/genomflöde.  
    ![](media/premium-storage-performance/image1.png)

Vissa program kan du ändra deras i/o-storlek, medan vissa program inte. SQL Server anger optimala i/o-storlek sig själv och ger inte användare med alla rattar för att ändra den. Å andra sidan Oracle innehåller en parameter med namnet [DB\_BLOCKERA\_storlek](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) med vilket du kan konfigurera i/o-begäran storleken på databasen.

Om du använder ett program, som inte tillåter att ändra i/o-storlek, använda riktlinjerna i den här artikeln för att optimera prestanda KPI: er som är mest relevant för ditt program. Exempel:

* En OLTP-program genererar miljontals små och slumpmässiga i/o-begäranden. Om du vill hantera dessa typ av i/o-begäranden måste du utforma din infrastruktur för att få högre IOPS.  
* Ett informationslager program genererar stora och sekventiella i/o-begäranden. Om du vill hantera dessa typ av i/o-begäranden måste du utforma din infrastruktur för att få högre bandbredd eller dataflöde.

Om du använder ett program som gör att du kan ändra i/o-storlek, Använd den här tumregel för i/o-storlek utöver andra riktlinjer för prestanda

* Mindre i/o-storlek få högre IOPS. Till exempel 8 KB för ett OLTP-program.  
* I/o större få högre bandbredd/genomflöde. Till exempel 1024 KB för ett data warehouse-program.

Här är ett exempel på hur du kan beräkna IOPS och genomströmning/bandbredd för ditt program. Överväg att ett program som använder en P30 disk. Det maximala antalet IOPS och genomströmning/bandbredd en P30 disk kan uppnå är 5 000 IOPS och 200 MB per sekund respektive. Om ditt program kräver den högsta IOPS från P30 disken och du använder en mindre i/o-storlek som 8 KB, är den resulterande bandbredd som du kommer att kunna hämta nu 40 MB per sekund. Om ditt program kräver maximal dataflödet/bandbredd från P30 disk, och du använder en större i/o-storlek som 1024 KB, resulterande IOPS är dock mindre, 200 IOPS. Finjustera därför i/o-storlek så att den uppfyller båda programmets IOPS och genomströmning/bandbredd. Tabellen nedan sammanfattar olika i/o-storlekar och deras motsvarande IOPS och genomströmning för en P30 disk.

| Programkrav | I/o-storlek | IOPS | Genomströmning/bandbredd |
| --- | --- | --- | --- |
| Max IOPS |8 kB |5 000 |40 MB per sekund |
| Max genomflöde |1 024 kB |200 |200 MB per sekund |
| Max Throughput + höga IOPS |64 kB |3,200 |200 MB per sekund |
| Max IOPS + högt genomflöde |32 KB |5 000 |160 MB per sekund |

Använda flera premiumdiskar stripe används tillsammans för att få IOPS och bandbredd som är högre än det högsta värdet för en enskild premium storage disk. Till exempel stripe två P30 diskar för att hämta en kombinerad IOPS i 10 000 IOPS eller en kombinerad genomströmning på 400 MB per sekund. Som beskrivs i nästa avsnitt, måste du använda en VM-storlek som har stöd för det kombinerade IOPS och genomflöde.

> [!NOTE]
> Om du ökar IOPS eller genomströmning andra ökar även kontrollera att du inte träffar genomströmning eller IOPS-gränser för disk- eller VM om du vill öka någon.
>
>

Du kan köra benchmarking verktyg på den Virtuella diskar för att diskvittne inverkan på programmets prestanda av i/o-storlek. Skapa flera testkörningar och använda olika i/o-storlek för varje kör för att se effekten. Referera till den [Benchmarking](#Benchmarking) avsnittet i slutet av den här artikeln för mer information.

## <a name="high-scale-vm-sizes"></a>Hög skalning VM-storlekar
När du startar ett program, är en av de första sakerna att göra, Välj en virtuell dator som värd för ditt program. Premium-lagring har hög skala VM-storlekar som kan köra program som kräver högre datorkraft och en hög lokal disk i/o-prestanda. Dessa virtuella datorer ger snabbare processorer, minne till core förhållandet och en Solid-State Drive (SSD) för den lokala disken. Exempel på hög skala virtuella datorer stöder Premium-lagring är DS, DSv2 och GS-serien virtuella datorer.

Hög skalning för virtuella datorer finns i olika storlekar med ett annat antal CPU-kärnor, minne, OS och tillfällig diskstorleken. Varje VM-storlek har också maximala antalet datadiskar som kan bifogas till den virtuella datorn. Därför valt VM-storlek påverkar hur mycket bearbetning, minne, och lagringskapacitet är tillgängligt för ditt program. Det påverkar också beräkningen och lagring kostnad. Nedan ser du till exempel specifikationerna för den största VM-storleken i DS-serien, DSv2-serien och GS-serien:

| Storlek på virtuell dator | Processorkärnor | Minne | Storlekar för Virtuella diskar | Max. Datadiskar | Cachestorlek | IOPS | Gränser för Cache-i/o-bandbredd |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS14 |16 |112 GB |OS = 1 023 GB <br> Lokal SSD = 224 GB |32 |576 GB |50 000 IOPS <br> 512 MB per sekund |4 000 IOPS och 33 MB per sekund |
| Standard_GS5 |32 |448 GB |OS = 1 023 GB <br> Lokal SSD = 896 GB |64 |4224 GB |80 000 IOPS <br> 2 000 MB per sekund |5 000 IOPS och 50 MB per sekund |

Om du vill visa en fullständig lista över alla tillgängliga storlekar på Virtuella Azure avser [Windows VM-storlekar](../articles/virtual-machines/windows/sizes.md) eller [Linux VM-storlekar](../articles/virtual-machines/linux/sizes.md). Välj en VM-storlek som kan uppfylla och skala till dina prestandakrav för önskat program. Utöver detta, beakta följande viktiga överväganden när du väljer VM-storlekar.

*Gränser*  
Högsta IOPS-gränser per virtuell dator och per disk är olika och oberoende av varandra. Kontrollera att programmet aktiverar IOPS inom gränserna för den virtuella datorn, samt premiumdiskar som är kopplade till den. Annars får programprestanda begränsning.

Anta exempelvis ett program som krävs är högst 4 000 IOPS. För att åstadkomma detta kan du etablera en P30 disk på en virtuell dator DS1. P30 disken kan leverera upp till 5 000 IOPS. DS1-VM är dock begränsad till 3,200 IOPS. Följaktligen programprestanda kommer att begränsas av VM-gränsen på 3,200 IOPS och det blir försämrade prestanda. Om du vill förhindra detta, väljer du en storlek för VM- och diskresurser som kommer båda uppfyller kraven för application.

*Kostnaden för åtgärden*  
I många fall är det möjligt att den totala kostnaden för åtgärden med Premium-lagring är lägre än med standardlagring.

Tänk dig ett program som kräver 16 000 IOPS. För att uppnå prestanda, behöver du en Standard\_D14 Azure IaaS VM, som kan ge högsta IOPS för 16000 med 32 standardlagring 1 TB diskar. Varje 1TB standardlagring disk kan uppnå högst 500 IOPS. Den beräknade kostnaden för den här virtuella datorn per månad blir $1,570. Månadskostnaden för 32 standardlagring diskar blir $1,638. Den totala månatliga uppskattade kostnaden tas $3,208.

Men om du finns samma program på Premium-lagring, måste en mindre VM-storlek och färre premium storage diskar, vilket minskar den sammanlagda kostnaden. En Standard\_DS13 VM kan uppfyller 16 000 IOPS med hjälp av fyra P30 diskar. Den virtuella datorn DS13 har högsta IOPS för 25,600 och varje P30 disk har högsta IOPS för 5 000. Generellt sett den här konfigurationen kan uppnå 5 000 x 4 = 20 000 IOPS. Den beräknade kostnaden för den här virtuella datorn per månad blir $1,003. Månadskostnaden för fyra P30 premium lagringsdiskar blir $544.34. Den totala månatliga uppskattade kostnaden tas $1,544.

Tabellen nedan sammanfattar kostnaden fördelningen av det här scenariot för Standard- och Premium-lagring.

| &nbsp; | **Standard** | **Premium** |
| --- | --- | --- |
| **Kostnaden för VM per månad** |$1,570.58 (standard\_D14) |$1,003.66 (standard\_DS13) |
| **Av diskarnas kostnad per månad** |$1,638.40 (32 x 1 TB diskar) |$544.34 (4 x P30 diskar) |
| **Övergripande kostnader per månad** |$3,208.98 |$1,544.34 |

*Linux-distributioner*  

Med Azure Premium Storage får samma nivå av prestanda för virtuella datorer som kör Windows och Linux. Vi stöder många varianter av Linux-distributioner och du kan se den fullständiga listan [här](../articles/virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Det är viktigt att notera att olika distributioner lämpar sig bättre för olika typer av arbetsbelastningar. Du ser olika nivåer av prestanda beroende på distro din arbetsbelastning körs på. Testa Linux-distributioner med ditt program och välja den som passar dig bäst.

När du kör Linux med Premium-lagring, kontrollerar du de senaste uppdateringarna om nödvändiga drivrutiner för att säkerställa hög prestanda.

## <a name="premium-storage-disk-sizes"></a>Premium-lagring diskstorlekar
Azure Premium Storage erbjuder sju diskstorlekar för närvarande. Varje diskstorleken har en annan skala gräns för IOPS, bandbredd och lagring. Välja rätt Premium-lagring diskstorleken beroende på kraven för application och hög skala VM-storlek. Tabellen nedan visar sju diskar storlekar och deras funktioner. P4 och P6 storlek är för närvarande stöds endast för hanterade diskar.

| Premium diskar typ  | P4    | P6    | P10   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| Diskstorlek           | 32 GB | 64 GB | 128 GB| 512 GB            | 1 024 GB (1 TB)    | 2 048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOPS per disk       | 120   | 240   | 500   | 2 300              | 5000              | 7500              | 7500              | 
| Dataflöde per disk | 25 MB per sekund  | 50 MB per sekund  | 100 MB per sekund | 150 MB per sekund | 200 MB per sekund | 250 MB per sekund | 250 MB per sekund | 


Hur många diskar som du väljer beror på disken storlek som väljs. Du kan använda en enskild p 50 disk eller flera P10 diskar för att uppfylla dina programkrav. Beakta överväganden för användarkonton nedan när du gör valet.

*Gränser (IOPS och genomströmning)*  
IOPS och genomströmning gränserna för varje Premium-diskstorleken är olika och oberoende från VM skala gränser. Se till att totala IOPS och genomströmning från diskarna som är inom skalningsgränserna för den valda VM-storleken.

Om exempelvis en programkrav är högst 250 MB/s genomströmning och du använder en DS4 virtuell dator med en enda P30 disk. DS4 VM kan ge upp till 256 MB/s genomströmning. Men har en enda disk P30 genomströmning högst 200 MB per sekund. Programmet kommer därför begränsade på 200 MB per sekund på grund av disk-gränsen. Om du vill lösa den här gränsen etablera flera datadiskar till den virtuella datorn eller ändra storlek på diskarna till P40 eller p 50.

> [!NOTE]
> Läsningar som hanteras av cachen ingår inte i disken IOPS och genomströmning och därför inte omfattas av disk-gränser. Cachen har separata IOPS och genomströmning gränsen per virtuell dator.
>
> Till exempel är ursprungligen din läsningar och skrivningar 60MB per sekund och 40MB per sekund. Över tiden, cachen värmer upp och fungerar i läsningar från cacheminnet. Sedan kan du skriva högre genomströmning från disken.
>
>

*Antal diskar*  
Bestämma antalet diskar som du behöver genom att bedöma programkrav. Varje VM-storlek har också en gräns för antalet diskar som du kan ansluta till den virtuella datorn. Detta är vanligtvis två gånger antal kärnor. Se till att antalet diskar som behövs har stöd för VM-storlek som du väljer.

Kom ihåg att diskarna för Premium-lagring har högre prestanda jämfört med standardlagring diskar. Om du migrerar programmet från Azure IaaS-VM med standardlagring till Premium-lagring måste du därför kommer troligen färre premiumdiskar att uppnå samma eller högre prestanda för ditt program.

## <a name="disk-caching"></a>Cachelagring på disk
Hög skalning VMs som utnyttjar Azure Premium-lagring har en flera nivåer cachelagring teknik som kallas BlobCache. BlobCache använder en kombination av den virtuella datorn RAM-minne och lokala SSD för cachelagring. Det här cacheminnet är tillgängligt för Premium-lagring beständiga diskar och Virtuella lokala diskar. Det här cache-inställningen är som standard att läsa eller skriva för OS-diskar och ReadOnly för datadiskar som finns på Premium-lagring. Med diskcachelagring aktiverad på diskar med Premium-lagring, uppnå hög skala VMs mycket hög prestanda som överskrider den underliggande diskprestanda.

> [!WARNING]
> Ändra cache-inställningen för ett Azure-disken kopplas från och nytt bifogar måldisken. Om det är operativsystemdisk, startas den virtuella datorn. Stoppa alla program/tjänster som kan påverkas av den här avbrott innan du ändrar diskcache-inställningen.
>
>

Mer information om hur BlobCache fungerar finns i inre [Azure Premium Storage](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/) blogginlägg.

Det är viktigt att aktivera cachen på rätt uppsättning diskar. Om du ska aktivera cachelagring på disk på en disk i premium eller inte beror på arbetsbelastningen mönstret disken hanterar. Tabellen nedan visar standard inställningar för cachelagring för Operativsystemet och datadiskarna.

| **Typ av disk** | **Standardinställning för Cache** |
| --- | --- |
| OS-disk |ReadWrite |
| Datadisk |Ingen |

Följande är inställningar för cachelagring av rekommenderas för datadiskar,

| **Disken cacheinställning** | **Rekommendation om när du ska använda den här inställningen** |
| --- | --- |
| Ingen |Konfigurera värd-cachen eftersom ingen för lässkyddad och skrivintensiv diskar. |
| Skrivskyddad |Konfigurera värd-cache som ReadOnly för skrivskyddade och läsa / skriva-diskar. |
| ReadWrite |Konfigurera värd-cache som ReadWrite bara om programmet hanterar korrekt cachelagrade data skrivs till beständiga diskar vid behov. |

*Skrivskyddad*  
Genom att konfigurera ReadOnly cachelagring på Premium-lagring data diskar kan du uppnå Läs fördröjning och hämta mycket hög Läs IOPS och genomströmning för ditt program. Detta är på grund av två skäl

1. Läser utföras från cache, som finns i det Virtuella minnet och lokala SSD, är mycket snabbare än läsning från datadisken som finns i Azure blob storage.  
2. Premium-lagring räknas inte läsningar från cache, mot disken IOPS och genomflöde. Därför är tillämpningsprogrammet möjlig att uppnå högre IOPS totalt och genomflöde.

*ReadWrite*  
Som standard har OS-diskar ReadWrite cachelagring aktiverat. Vi har lagt till stöd för ReadWrite cachelagring på data samt diskar. Om du använder ReadWrite cachelagring, måste du ha ett korrekt sätt att skriva data från cache till beständiga diskar. Till exempel SQL Server hanterar cachelagrade data skrivs till beständiga lagringsdiskarna på sin egen. Med ett program som inte hanterar beständighet nödvändiga data ReadWrite cache kan leda till förlust av data om den virtuella datorn kraschar.

T.ex, du kan använda dessa riktlinjer för SQL Server som körs på Premium-lagring genom att göra följande:

1. Konfigurera ”ReadOnly” cache på premium storage diskar som är värd för filer.  
   a.  Fast läser från cache lägre Frågetid SQL Server eftersom datasidor är mycket snabbare hämtas från cachen jämfört med direkt från data diskar.  
   b.  Hanterar läser från cache, innebär ytterligare genomflöde är tillgänglig från premium datadiskar. SQL Server kan använda den här ytterligare genomflöde mot hämta mer datasidor och andra åtgärder som säkerhetskopiering/återställning batch belastningar och index återskapas.  
2. Konfigurera ”None” cachelagras på premium lagringsdiskar värd loggfilerna.  
   a.  Loggfilerna har främst skrivintensiv åtgärder. Därför kan får de inte ReadOnly-cachen.

## <a name="disk-striping"></a>Disk-Striping
När en hög skala VM är kopplade till flera premium beständiga lagringsdiskar kan diskarna vara stripe tillsammans för att sammanställa sina IOPs och bandbredd lagringskapacitet.

I Windows, kan du använda lagringsutrymmen till stripe-diskar tillsammans. Du måste konfigurera en kolumn för varje disk i en pool. Annars kan prestandan stripe-volym vara lägre än förväntat pga en ojämn fördelning av trafik över diskar.

Viktigt: Använda Server Manager UI kan ange du det totala antalet kolumner upp till 8 för stripe-volymer. När du bifogar mer än 8 diskar, kan du använda PowerShell för att skapa volymen. Med hjälp av PowerShell, kan du ange antalet kolumner lika med antalet diskar. Till exempel om det finns 16 diskar i en enda stripe-uppsättning; Ange 16 kolumner i den *NumberOfColumns* parameter för den *New-VirtualDisk* PowerShell-cmdlet.

Använd verktyget MDADM stripe-diskar tillsammans på Linux. Detaljerade anvisningar på striping diskar på Linux finns [konfigurera programvara RAID på Linux](../articles/virtual-machines/linux/configure-raid.md).

*Stripe-storlek*  
En viktig konfiguration i disken striping är stripe-storlek. Stripe-storlek eller blockstorleken är den minsta datasegmentet som programmet kan åtgärda på stripe-volymer. Stripe-storlek som du konfigurerar beror på vilken typ av program och dess mönster i begäran. Om du väljer fel stripe-storlek kan leda det till att i/o-feljusteringarna, vilket leder till försämrade prestanda för programmet.

Till exempel om en i/o-begäran som genererats av ditt program är större än den stripe-diskstorleken skriver lagringssystemet den över stripe enhet gränser på mer än en disk. När det är dags att komma åt dessa data har att söka i mer än en stripe-enheter för att slutföra begäran. Den kumulativa effekten av sådana beteende kan leda till stor prestandaförsämring. Å andra sidan, kan om i/o-begäran är mindre än Stripestorleken på och om det är slumpmässigt i/o-begäranden uppgå på samma disk orsakar en flaskhals och slutligen försämring av i/o-prestanda.

Beroende på typ av arbetsbelastning som programmet körs, väljer du en lämplig stripe-storlek. Använda en mindre stripe-storlek för slumpmässiga små i/o-begäranden. Medan för stora sekventiella i/o-begäranden för att använda en större stripe. Ta reda på stripe storlek rekommendationer för programmet körs på Premium-lagring. Konfigurera stripe storlek på 64KB för OLTP-arbetsbelastningar och 256KB för data datalagring arbetsbelastningar för SQL Server. Se [prestandarelaterade Metodtips för SQL Server på Azure Virtual Machines](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md#disks-guidance) vill veta mer.

> [!NOTE]
> Du kan stripe-tillsammans högst 32 premium storage på DS-serien VM och 64 premium storage diskar på GS-serien VM.
>
>

## <a name="multi-threading"></a>Flertrådsteknik
Premium-lagring plattform ska massivt parallell har utformats för Azure. Ett flertrådat program uppnår därför mycket högre prestanda än en enkeltrådig program. Ett flertrådat program delar upp sina uppgifter över flera trådar och ökar effektiviteten för körningen genom att använda VM- och diskresurser resurser till högsta.

Till exempel om tillämpningsprogrammet körs på en enda kärna VM med två trådar kan Processorn växla mellan två trådar för att uppnå effektivitet. När en tråd väntar på en disk-i/o för att slutföra kan Processorn växla till den andra tråden. På så sätt kan kan två trådar göra mer än en enkel tråd. Om den virtuella datorn har mer än en kärna, minskar ytterligare körtiden eftersom varje kärna kan köra uppgifter parallellt.

Du kanske inte kan ändra hur ett färdigt program implementerar enda trådade eller flertrådsteknik. SQL Server är kan hantera flera processorer och flera kärnor. Dock beslutar SQL Server under vilka förhållanden den använder en eller flera trådar för att bearbeta en fråga. Den kan köra frågor och skapa index med flertrådsteknik. SQL Server att sannolikt använda flera trådar för en fråga som inbegriper stora tabeller och sortera data innan det returneras till användaren. En användare kan dock styra om SQL Server kör en fråga med hjälp av en enskild tråd eller flera trådar.

Det finns inställningar som du kan ändra påverkar detta flertrådsteknik eller parallell bearbetning av ett program. Det kan till exempel är maximal grad av parallellitet konfiguration vid SQL Server. Den här inställningen som kallas MAXDOP, kan du konfigurera det maximala antalet processorer som SQL Server kan använda när parallell bearbetning. Du kan konfigurera MAXDOP för enskilda frågor eller Indexåtgärder. Det här är bra när du vill utjämna resurser i systemet för prestanda kritiska program.

Anta exempelvis att ditt program som använder SQL Server körs på en stor fråga och ett indexåtgärden på samma gång. Låt oss anta att du vill att indexåtgärden ska vara mer performant jämfört med stora frågan. I så fall kan ange du MAXDOP värde indexåtgärden vara högre än värdet MAXDOP för frågan. På så sätt kan har SQL Server fler processorer som den kan använda för indexåtgärden jämfört med antalet processorer som den kan dedikera i stora frågan. Kom ihåg att du inte styr antalet trådar som ska användas för varje åtgärd i SQL Server. Du kan kontrollera det maximala antalet processorer som är dedikerad för flertrådsteknik.

Lär dig mer om [grader parallellitet](https://technet.microsoft.com/library/ms188611.aspx) i SQL Server. Ta reda på sådana inställningar som påverkar flertrådsteknik i ditt program och deras konfigurationer för att optimera prestanda.

## <a name="queue-depth"></a>Ködjup
Ködjup eller Kölängd eller köstorlek är antalet väntande i/o-begäranden i systemet. Värdet för ködjup bestämmer hur många i/o-åtgärder som programmet kan Rada upp som diskar med lagringsutrymme ska bearbetas. Den påverkar alla tre program-nyckeltal som beskrevs i d.v.s. på den här artikeln, IOPS, genomflöde och svarstid.

Kö djup och flertrådsteknik är nära relaterade. Ködjup värdet anger hur mycket flertrådsteknik som kan uppnås av programmet. Om det ködjup är stor, programmet kan köra flera åtgärder samtidigt, med andra ord mer flertrådsteknik. Om det ködjup är litet, även om programmet är flertrådade, har inte tillräckligt med begäranden som väntar på samtidig körning.

Vanligtvis av hyllan program inte är tillåtet att ändra ködjupet eftersom om angetts felaktigt den gör mer skada än bra. Program ska värdet rätt för ködjup för att få bästa möjliga prestanda. Det är viktigt att förstå detta begrepp så att du kan felsöka problem med prestanda med ditt program. Du kan också se effekterna av ködjup genom att köra benchmarking verktyg på datorn.

Vissa program ange inställningar för att påverka det ködjup. Till exempel beskrivs inställningen MAXDOP (högsta grad av parallellitet) i SQL Server i föregående avsnitt. MAXDOP är ett sätt att påverka ködjup och flertrådsteknik, även om den inte direkt ändra ködjup värdet av SQL Server.

*Hög ködjup*  
En hög ködjup skrivs flera åtgärder på disken. Disken vet nästa begäran i kön i förväg. Följaktligen disken schemalägga åtgärder i förväg och bearbeta dem i en optimal sekvens. Eftersom programmet är mer begäranden skickas till disken, kan disken bearbeta flera parallella IOs. Slutligen kommer programmet att kunna uppnå högre IOPS. Eftersom fler begäranden bearbetning, ökar även det totala genomflödet i programmet.

Vanligtvis ett program kan uppnå maximalt dataflöde med 8-16 + utestående I/o per ansluten disk. Om ett ködjup är en, tillräckligt med IOs att systemet gör inte att program och mindre mängd bearbetas under en viss period. Med andra ord mindre genomflöde.

Till exempel i SQL Server meddelar MAXDOP värdet för en fråga till ”4” SQL Server som den kan använda upp till fyra kärnor för att köra frågan. SQL Server avgör vad är bästa kön djup värdet och antalet kärnor för körningen av frågan.

*Optimalt ködjup*  
Mycket hög kön djup värdet har även dess nackdelar. Om kön djup värdet är för högt, försöker programmet enhet mycket hög IOPS. Om programmet har beständiga diskar med tillräckligt etablerade IOPS, kan detta påverka programväntetider. Följande formel visar relationen mellan IOPS, svarstid och ködjup.  
    ![](media/premium-storage-performance/image6.png)

Du bör inte konfigurera ködjup till ett högt värde, men ett optimalt värde som kan ge tillräckligt med IOPS för programmet utan att påverka svarstider. Till exempel om programmet fördröjning måste vara 1 millisekund, ködjup som krävs för att uppnå 5 000 IOPS är QD = 5000 x 0,001 = 5.

*Ködjup för stripe-volym*  
Upprätthålla ett tillräckligt högt ködjup för stripe-volymer så att alla diskar har en topp ködjup individuellt. Anta till exempel att ett program som skickar ett ködjup på 2 och det finns 4 diskar i stripe. Två i/o-begäranden skickas till två diskar och återstående två diskar blir inaktiv. Därför konfigurera ködjupet så att alla diskar som kan vara upptaget. Formeln nedan visar hur du fastställer ködjup på stripe-volymer.  
    ![](media/premium-storage-performance/image7.png)

## <a name="throttling"></a>Begränsning
Azure bestämmelserna Premium-lagring antal IOPS och genomflöde för beroende på VM-storlekar och storlekar för diskar som du väljer. När programmet försöker enheten IOPS eller dataflöde ovan dessa gränser vad virtuell dator eller disk kan hantera den med begränsning av Premium-lagring. Detta visar i form av försämrade prestanda i ditt program. Detta kan innebära högre latens, sänka genomströmning eller lägre IOPS. Om Premium-lagring inte begränsning, misslyckas programmet helt av mer än vad dess resurser är möjligt att uppnå. Så om du vill undvika prestandaproblem på grund av begränsning alltid etablera tillräckligt med resurser för ditt program. Ta hänsyn till vi diskuterade i VM-storlekar och Disk storlekar ovan. Prestandamätningar är det bästa sättet att ta reda på vilka resurser måste du vara värd för programmet.

## <a name="benchmarking"></a>Prestandamätningar
Prestandamätningar är processen att simulera olika arbetsbelastningar för ditt program och mäta programprestanda för varje arbetsbelastning. Med hjälp av stegen som beskrivs i ett tidigare avsnitt, du har samlat in programkrav för prestanda. Du kan fastställa prestandanivåer som programmet kan uppnå med Premium-lagring genom att köra benchmarking verktyg på virtuella datorer som värdar för programmet. I det här avsnittet får du exempel på en Standard DS14 VM etablerats med Azure Premium Storage diskar prestandamätningar.

Vi har använt gemensamma benchmarking verktyg Iometer och FIO, för Windows och Linux respektive. Dessa verktyg starta flera trådar simulera en produktion som arbetsbelastning och mäta systemets prestanda. Med hjälp av verktygen kan du också konfigurera parametrar som block storlek och kön djup, vilket normalt inte kan ändra för ett program. Detta ger dig större flexibilitet att driva maximala prestanda på hög nivå VM etablerats med premiumdiskar för olika typer av arbetsbelastningar för program. Läs mer om varje benchmarking verktyg finns [Iometer](http://www.iometer.org/) och [FIO](http://freecode.com/projects/fio).

Om du vill följa exemplen nedan, skapa en Standard DS14 virtuell dator och koppla 11 Premium-lagring diskar till den virtuella datorn. Konfigurera 10 diskar med cachelagring som ”None”-värden och stripe-dem till en volym med namnet NoCacheWrites 11 diskar. Konfigurera cachelagring som ”skrivskyddad” på den återstående disken värden och skapa en volym med namnet CacheReads med den här disken. Med den här inställningen kommer du att kunna se den maximala prestandan för läsning och skrivning Standard DS14 VM. Detaljerade anvisningar om hur du skapar en virtuell dator DS14 med premiumdiskar går du till [skapa och använda en Premium-lagring konto för en virtuell dator datadisk](../articles/virtual-machines/windows/premium-storage.md).

*Värma upp cachen*  
Disken med cachelagring av ReadOnly-värden kommer att kunna ge högre IOPS än gränsen som disk. För att få den här högsta läsprestanda från cache värden måste först du värmt upp cache för den här disken. Detta säkerställer att läsa IOs vilket benchmarking verktyg kommer att driva på CacheReads volym faktiskt träffar i cachen och inte disken direkt. Cacheträffar resultatet i ytterligare IOPS från den enda cachen aktiverat disk.

> **Viktigt:**  
> Du måste värmt upp cachen innan du kör prestandatester, varje gång VM startas.
>
>

#### <a name="iometer"></a>Iometer
[Hämta verktyget Iometer](http://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download) på den virtuella datorn.

*Testa fil*  
Iometer använder en testfil som lagras på volymen där du kör testet benchmarking. Den enheter läser och skriver den här Testfilen för att mäta disken IOPS och genomflöde. Iometer skapar den här Testfilen om du inte har angett något. Skapa en fil för test av 200GB kallas iobw.tst på CacheReads och NoCacheWrites volymer.

*Specifikationer för Access*  
Specifikationer, begär i/o-storlek, % läsning/skrivning, % slumpmässiga/sekventiella konfigureras på fliken ”specifikationer Access” i Iometer. Skapa en åtkomst-specifikation för var och en av de scenarier som beskrivs nedan. Skapa åtkomst specifikationer och ”spara” med ett lämpligt namn som – RandomWrites\_8 kB RandomReads\_8 kB. Välj den motsvarande specifikationen när du kör Testscenario.

Ett exempel på åtkomst specifikationer för högsta IOPS skriva scenario visas nedan,  
    ![](media/premium-storage-performance/image8.png)

*Specifikationer för högsta IOPS-Test*  
Använda mindre storlek på begäran för att demonstrera högsta IOPs. Använd 8 kB begära storlek och skapa specifikationer för slumpmässiga skrivningar och läsningar.

| Specifikation för åtkomst | Begära storlek | Slumpmässiga % | Läs % |
| --- | --- | --- | --- |
| RandomWrites\_8 kB |8 KB |100 |0 |
| RandomReads\_8 kB |8 KB |100 |100 |

*Maximalt dataflöde Test specifikationer*  
Använd större begäran för att demonstrera maximalt dataflöde. Använd 64K begära storlek och skapa specifikationer för slumpmässiga skrivningar och läsningar.

| Specifikation för åtkomst | Begära storlek | Slumpmässiga % | Läs % |
| --- | --- | --- | --- |
| RandomWrites\_64 kB |64 KB |100 |0 |
| RandomReads\_64 kB |64 KB |100 |100 |

*Kör testet Iometer*  
Utför stegen nedan värmt upp cache

1. Skapa två åtkomst specifikationer med värden som visas nedan,

   | Namn | Begära storlek | Slumpmässiga % | Läs % |
   | --- | --- | --- | --- |
   | RandomWrites\_1 MB |1MB |100 |0 |
   | RandomReads\_1 MB |1MB |100 |100 |
2. Kör testet Iometer för att initiera cache disk med följande parametrar. Använd tre trådar för målvolymen och ett ködjup på 128. Ange varaktighet ”körningstiden” testet till 2hrs på fliken ”testa inställningar”.

   | Scenario | Målvolymen | Namn | Varaktighet |
   | --- | --- | --- | --- |
   | Initiera Cache-Disk |CacheReads |RandomWrites\_1 MB |2hrs |
3. Kör testet Iometer för uppvärmning cache disk med följande parametrar. Använd tre trådar för målvolymen och ett ködjup på 128. Ange varaktighet ”körningstiden” testet till 2hrs på fliken ”testa inställningar”.

   | Scenario | Målvolymen | Namn | Varaktighet |
   | --- | --- | --- | --- |
   | Varm upp Cache-Disk |CacheReads |RandomReads\_1 MB |2hrs |

När cachen disk är varmkörts fortsätter du med testscenarier som anges nedan. Gör minst tre trådar för att köra testet Iometer **varje** mål volym. För varje arbetstråd målvolymen, ange ködjup och markerar ett av de sparade test-specifikationerna som visas i tabellen nedan, att köra motsvarande Testscenario. Förväntat resultat visas också för IOPS och dataflöde när du kör testerna. För alla scenarier är används en små i/o-storlek på 8KB och en hög ködjup på 128.

| Testscenario | Målvolymen | Namn | Resultat |
| --- | --- | --- | --- |
| Max. Läs IOPS |CacheReads |RandomWrites\_8 kB |50 000 IOPS |
| Max. Skriva IOPS |NoCacheWrites |RandomReads\_8 kB |64 000 IOPS |
| Max. Kombinerade IOPS |CacheReads |RandomWrites\_8 kB |100 000 IOPS |
| NoCacheWrites |RandomReads\_8 kB | &nbsp; | &nbsp; |
| Max. Läsa MB per sekund |CacheReads |RandomWrites\_64 kB |524 MB per sekund |
| Max. Skriva MB per sekund |NoCacheWrites |RandomReads\_64 kB |524 MB per sekund |
| Kombinerade MB per sekund |CacheReads |RandomWrites\_64 kB |1 000 MB per sekund |
| NoCacheWrites |RandomReads\_64 kB | &nbsp; | &nbsp; |

Nedan visas skärmdumpar av Iometer testresultat för kombinerade IOPS och genomflöde.

*Kombinerade läsningar och skrivningar högsta IOPS*  
![](media/premium-storage-performance/image9.png)

*Kombinerade läsningar och skrivningar maximalt dataflöde*  
![](media/premium-storage-performance/image10.png)

### <a name="fio"></a>FIO
FIO är en populär verktyg som benchmark lagring på virtuella Linux-datorer. Det har möjlighet att välja andra i/o-storlekar, sekventiella eller åtkomsttider och skrivåtgärder. Det skapas trådar och processer för att utföra angivna i/o-åtgärder. Du kan ange vilken typ av i/o-åtgärder som varje arbetstråd måste utföra med hjälp av jobbfiler. Vi har skapat en fil per scenariot som illustreras i exemplen nedan. Du kan ändra specifikationerna i jobbfilerna för att mäta olika arbetsbelastningar som körs på Premium-lagring. Vi använder en Standard DS 14 VM som körs i exempel **Ubuntu**. Använd samma inställningar som beskrivs i början av den [prestandamätningar avsnittet](#Benchmarking) och Varm in cachen innan du kör benchmark-tester.

Innan du börjar [hämta FIO](https://github.com/axboe/fio) och installera den på den virtuella datorn.

Kör följande kommando för Ubuntu,

```
apt-get install fio
```

Vi använder fyra trådar för att föra skrivåtgärder och fyra trådar för intresseväckande läsåtgärder på diskarna. Skriv arbetare körning trafik på volymen ”nocache”, som har 10 diskar med värdet ”None”-cache. Läs arbetare körning trafik på volymen ”readcache”, som har 1 disk med cache inställd på ”ReadOnly”.

*Maximal skrivåtgärder IOPS*  
Skapa jobbfilen för med följande specifikationer få högsta skriva IOPS. Ge den namnet ”fiowrite.ini”.

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

Observera följande viktiga saker som följer designriktlinjer för som beskrivs i föregående avsnitt. Dessa specifikationer som är nödvändiga för att ge högsta IOPS  

* En hög ködjup på 256.  
* En liten blockstorlek 8 kB.  
* Flera trådar utför slumpmässiga skrivningar.

Kör följande kommando för att startar testet FIO i 30 sekunder  

```
sudo fio --runtime 30 fiowrite.ini
```

När testet körs, kommer du kunna se antalet skriva IOPS den virtuella datorn och levererar premiumdiskar. I exemplet nedan visas DS14 VM levererar sin maximala skrivåtgärder IOPS högst 50 000 IOPS.  
    ![](media/premium-storage-performance/image11.png)

*Maximal Läs IOPS*  
Skapa jobbfilen för med följande specifikationer få högsta IOPS för läsning. Ge den namnet ”fioread.ini”.

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

Observera följande viktiga saker som följer designriktlinjer för som beskrivs i föregående avsnitt. Dessa specifikationer som är nödvändiga för att ge högsta IOPS

* En hög ködjup på 256.  
* En liten blockstorlek 8 kB.  
* Flera trådar utför slumpmässiga skrivningar.

Kör följande kommando för att startar testet FIO i 30 sekunder

```
sudo fio --runtime 30 fioread.ini
```

När testet körs, kommer du kunna se antalet lästa IOPS den virtuella datorn och levererar premiumdiskar. I exemplet nedan visas DS14 VM leverera med fler än 64 000 Läs IOPS. Detta är en kombination av disken och cache-prestanda.  
    ![](media/premium-storage-performance/image12.png)

*Maximal läsning och skrivning IOPS*  
Skapa jobbfilen med följande specifikationer få maximala kombinerade läsa och skriva IOPS. Ge den namnet ”fioreadwrite.ini”.

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

Observera följande viktiga saker som följer designriktlinjer för som beskrivs i föregående avsnitt. Dessa specifikationer som är nödvändiga för att ge högsta IOPS

* En hög ködjup på 128.  
* En liten blockstorlek 4KB.  
* Flera trådar som utför slumpmässig läsning och skrivning.

Kör följande kommando för att startar testet FIO i 30 sekunder

```
sudo fio --runtime 30 fioreadwrite.ini
```

När testet körs, kommer du att kunna se antalet kombinerade läsa och skriva IOPS på den virtuella datorn och levererar premiumdiskar. I exemplet nedan visas levererar DS14 VM med mer än 100 000 kombinerade läsa och skriva IOPS. Detta är en kombination av disken och cache-prestanda.  
    ![](media/premium-storage-performance/image13.png)

*Maximalt kombineras genomflöde*  
Om du vill hämta maximalt kombinerade läsa och skriva genomflöde, Använd en större blockstorlek och stora ködjup med flera trådar som utför läsningar och skrivningar. Du kan använda en blockstorlek på 64KB och ködjup på 128.

## <a name="next-steps"></a>Nästa steg
Lär dig mer om Azure Premium Storage:

* [Premium Storage: Lagring med höga prestanda för Azure Virtual Machines-arbetsbelastningar](../articles/virtual-machines/windows/premium-storage.md)  

Läs artiklarna på bästa praxis för prestanda för SQL Server för SQL Server-användare:

* [Metodtips för prestanda för SQLServer på virtuella Azure-datorer](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md)
* [Azure Premium Storage ger högsta prestanda för SQL Server i Azure VM](http://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)
