---
title: Prestandariktlinjer för SQL Server i Azure | Microsoft-dokument
description: Innehåller riktlinjer för att optimera SQL Server-prestanda i virtuella microsoft Azure-datorer.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/18/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 880f1c601cf4132fdec9e5d25b1bf1f2ff175ab7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650545"
---
# <a name="performance-guidelines-for-sql-server-in-azure-virtual-machines"></a>Prestandariktlinjer för SQL Server i Azure Virtual Machines

## <a name="overview"></a>Översikt

Den här artikeln innehåller vägledning för att optimera SQL Server-prestanda i Microsoft Azure Virtual Machine. När du kör SQL Server på virtuella Azure-datorer rekommenderar vi att du fortsätter använda samma justeringsalternativ för databasprestanda som gäller för SQL Server i en lokal servermiljö. Prestanda för en relationsdatabas i ett offentligt moln beror dock på många faktorer, till exempel storleken på en virtuell dator och konfigurationen av datadiskar.

[SQL Server-avbildningar som etablerats i Azure-portalen](quickstart-sql-vm-create-portal.md) följer metodtips för allmän lagringskonfiguration (mer information om hur lagring konfigureras finns i [Lagringskonfiguration för virtuella SQL Server-datorer](virtual-machines-windows-sql-server-storage-configuration.md)). Efter etablering kan du överväga att använda andra optimeringar som beskrivs i den här artikeln. Basera dina val på din arbetsbelastning och verifiera genom testning.

> [!TIP]
> Det finns vanligtvis en kompromiss mellan att optimera för kostnader och optimera för prestanda. Den här artikeln fokuserar på att få *bästa* prestanda för SQL Server på virtuella Azure-datorer. Om din arbetsbelastning är mindre krävande kanske du inte behöver någon optimering som anges nedan. Beakta dina prestandabehov, kostnader och arbetsbelastningsmönster när du utvärderar dessa rekommendationer.

## <a name="quick-check-list"></a>Snabbkontrolllista

Följande är en snabbkontroll lista för optimal prestanda för SQL Server på Azure Virtual Machines:

| Område | Optimeringar |
| --- | --- |
| [Storlek på virtuell dator](#vm-size-guidance) | - Använd VM-storlekar med 4 eller fler vCPU som [E4S_v3](../../ev3-esv3-series.md) eller högre, eller [DS12_v2](../../dv2-dsv2-series-memory.md) eller högre.<br/><br/> - [Es, Eas, Ds och Das-serien](../../sizes-general.md) erbjuder det optimala förhållandet mellan minne och vCPU som krävs för OLTP-arbetsbelastningsprestanda. <br/><br/> - [M-serien](../../m-series.md) erbjuder det högsta förhållandet minne och vCPU som krävs för verksamhetskritiska prestanda och är idealisk för datalagerarbetsbelastningar. <br/><br/> - Samla in målarbetsbelastningens [IOPS-](../premium-storage-performance.md#iops)och [dataflödeskrav](../premium-storage-performance.md#throughput) vid högtrafik genom att följa [checklistan för programprestandakrav](../premium-storage-performance.md#application-performance-requirements-checklist) och välj sedan den [vm-storlek](../sizes-general.md) som kan skalas till din arbetsbelastnings prestandakrav. [latency](../premium-storage-performance.md#latency)|
| [Lagring](#storage-guidance) | - För detaljerad testning av SQL Server-prestanda på Virtuella Azure-datorer med TPC-E och TPC_C riktmärken, se bloggen [Optimera OLTP-prestanda](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). <br/><br/> - Använd [premium SSD för](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794) bästa pris / prestanda fördelar. Konfigurera [ReadOnly-cache](../premium-storage-performance.md#disk-caching) för datafiler och ingen cache för loggfilen. <br/><br/> - Använd [Ultra Diskar](../disks-types.md#ultra-disk) om mindre än 1 ms lagringsfördyningar krävs av arbetsbelastningen. <br/><br/> - Samla in lagringsfördröjningskrav för SQL Server-data, logg- och Temp DB-filer genom [att övervaka programmet](../premium-storage-performance.md#application-performance-requirements-checklist) innan du väljer disktypen. Om <1ms lagringsfördymmare krävs, använd sedan Ultra Diskar, annars använd premium SSD. Om låga svarstider endast krävs för loggfilen och inte för datafiler, [etablerar du Ultra-disken](../disks-enable-ultra-ssd.md) på erforderliga IOPS- och dataflödesnivåer endast för loggfilen. <br/><br/> -  [Premium-filresurser](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) rekommenderas som delad lagring för en SQL Server-redundansklusterinstans. Premium-filresurser stöder inte cachelagring och erbjuder begränsad prestanda jämfört med premium-SSD-diskar. Välj premium-SSD-hanterade diskar över premiumfilresurser för fristående SQL-instanser. men utnyttja premiumfilresurser för redundansklusterindelad lagring för enkelt underhåll och flexibel skalbarhet. <br/><br/> - Standardlagring rekommenderas endast för utvecklings- och teständamål eller för säkerhetskopieringsfiler och bör inte användas för produktionsarbetsbelastningar. <br/><br/> - Behåll [lagringskontot](../../../storage/common/storage-create-storage-account.md) och VIRTUELLA SQL Server i samma region.<br/><br/> - Inaktivera Azure [geo-redundant lagring](../../../storage/common/storage-redundancy.md) (geo-replikering) på lagringskontot.  |
| [Diskar](#disks-guidance) | - Använd minst 2 [premium SSD-diskar](../disks-types.md#premium-ssd) (1 för loggfil och 1 för datafiler). <br/><br/> - För arbetsbelastningar som kräver <1 ms IO-svarstider aktiverar du skrivaccelerator för M-serien och överväger att använda Ultra SSD-diskar för Es- och DS-serier. <br/><br/> - Aktivera [skrivskyddad cachelagring](../premium-storage-performance.md#disk-caching) på disken(erna) som är värd för datafilerna.<br/><br/> - Lägg till ytterligare 20% premium IOPS /dataflöde kapacitet än din arbetsbelastning kräver när [du konfigurerar lagring för SQL Server-data, logg och TempDB filer](virtual-machines-windows-sql-server-storage-configuration.md) <br/><br/> - Undvik att använda operativsystem eller tillfälliga diskar för databaslagring eller loggning.<br/><br/> - Aktivera inte cachelagring på diskar som är värdar för loggfilen.  **Viktigt:** Stoppa SQL Server-tjänsten när du ändrar cacheinställningarna för en Azure VM-disk.<br/><br/> - Stripe flera Azure-datadiskar för att få ökat lagringsdataflöde.<br/><br/> - Format med dokumenterade allokeringsstorlekar. <br/><br/> - Placera TempDB på den `D:\` lokala SSD-enheten för verksamhetskritiska SQL Server-arbetsbelastningar (efter att ha valt rätt VM-storlek). Om du skapar den virtuella datorn från Azure-portalen eller Azure-snabbstartsmallar och [placerar Temp DB på den lokala disken](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583) behöver du ingen ytterligare åtgärd. för alla andra fall följ stegen i bloggen för [att använda SSD för att lagra TempDB](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) för att förhindra fel efter omstarter. Om kapaciteten för den lokala enheten inte är tillräcklig för temp DB-storleken placerar du Temp DB på en [lagringspool som tas bort](../premium-storage-performance.md) på premium-SSD-diskar med [skrivskyddad cachelagring](../premium-storage-performance.md#disk-caching). |
| [I/o](#io-guidance) |- Aktivera komprimering av databassidan.<br/><br/> - Aktivera omedelbar filinitiering för datafiler.<br/><br/> - Begränsa databasens automatiska tillväxt.<br/><br/> - Inaktivera autoshrink av databasen.<br/><br/> - Flytta alla databaser till datadiskar, inklusive systemdatabaser.<br/><br/> - Flytta SQL Server fellogg och spåra filkataloger till datadiskar.<br/><br/> - Konfigurera standardplatser för säkerhetskopiering och databasfiler.<br/><br/> - [Aktivera låsta sidor i minnet](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows?view=sql-server-2017).<br/><br/> - Använd prestandakorrigeringar för SQL Server. |
| [Funktionsspecifik](#feature-specific-guidance) | - Backa direkt till bloblagring.<br/><br/>- Använd [säkerhetskopior av ögonblicksbilder](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) för databaser som är större än 12 TB. <br/><br/>- Använd flera Temp DB filer, 1 fil per kärna, upp till 8 filer.<br/><br/>- Ställ in max serverminne på 90% eller upp till 50 GB kvar för operativsystemet. <br/><br/>- Aktivera mjuk NUMA. |

Mer information om *hur* och *varför* du gör dessa optimeringar finns i informationen och vägledningen i följande avsnitt.

## <a name="vm-size-guidance"></a>Vägledning för VM-storlek

Börja med att samla in kraven på cpu, minne och lagringsdataflöde för arbetsbelastningen vid rusningstid. \LogicalDisk\Diskläsningar/Sek och \LogicalDisk\Diskskrivningar/sek prestandaräknare kan användas för att samla in läs- och skriv-IOPS-krav och \LogicalDisk\Disk bytes/sek-räknaren kan användas för att samla in [lagringsdataflödeskrav](../premium-storage-performance.md#disk-caching) för data-, logg- och Temp DB-filer. När IOPS och dataflödeskrav på topp har definierats utvärderar du VM-storlekar som erbjuder den kapaciteten. Om din arbetsbelastning till exempel kräver 20 K läst IOPS och 10K skriva IOPS på topp, kan du antingen välja E16s_v3 (med upp till 32 K cachade och 25600 oanslutna IOPS) eller M16_s (med upp till 20 K cachade och 10K oanslutna IOPS) med 2 P30-diskar. Se till att förstå både dataflöde och IOPS-krav för arbetsbelastningen eftersom virtuella datorer har olika skalningsgränser för IOPS och dataflöde.<br/><br/>[DSv_3](../../dv3-dsv3-series.md) och [Es_v3-serien](../../ev3-esv3-series.md) finns på allmän hårdvara med Intel Haswell- eller Broadwell-processorer. [M-serien](../../m-series.md) erbjuder det högsta antalet vCPU och minne för de största SQL Server-arbetsbelastningarna och finns på minnesoptimerad maskinvara med Skylake-processorfamilj. Dessa VM-serien stöder premiumlagring, vilket rekommenderas för bästa prestanda med läscache på värdnivå. Både Es_v3- och M-serien finns också i [begränsade kärnstorlekar](../../windows/constrained-vcpu.md), vilket sparar pengar för arbetsbelastningar med lägre beräknings- och höglagringskapacitetskrav. 

## <a name="storage-guidance"></a>Minnesriktlinjer

Detaljerad testning av SQL Server-prestanda på virtuella Azure-datorer med TPC-E och TPC_C riktmärken finns i bloggen [Optimera OLTP-prestanda](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). 

Azure blob-cache med premium SSD-enheter rekommenderas för alla produktionsarbetsbelastningar. 

> [!WARNING]
> Standard hdds och SSD-enheter har varierande svarstider och bandbredd och rekommenderas endast för utvecklings-/testarbetsbelastningar. Produktionsarbetsbelastningar bör använda premium-SSD:er.

Dessutom rekommenderar vi att du skapar ditt Azure-lagringskonto i samma datacenter som dina virtuella SQL Server-datorer för att minska överföringsfördröjningar. När du skapar ett lagringskonto kan du inaktivera georeplikering eftersom konsekvent skrivordning över flera diskar inte garanteras. Överväg i stället att konfigurera en SQL Server-teknik för haveriberedskap mellan två Azure-datacenter. Mer information finns i [Hög tillgänglighet och haveriberedskap för SQL Server i virtuella Azure-datorer](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Vägledning för diskar

Det finns tre huvuddisktyper på en virtuell Azure-dator:

* **OS-disk:** När du skapar en Virtuell Azure-dator bifogas minst en disk (märkt som **C-enheten)** till den virtuella datorn för operativsystemdisken. Den här disken är en virtuell hårddisk som lagras som en sidblob i lagring.
* **Temporär disk:** Virtuella Azure-datorer innehåller en annan disk som kallas temporär disk (märkt som **D:** drive). Det här är en disk på noden som kan användas för reputrymme.
* **Datadiskar:** Du kan också koppla ytterligare diskar till den virtuella datorn som datadiskar, och dessa lagras i lagring som sidblobar.

I följande avsnitt beskrivs rekommendationer för att använda dessa olika diskar.

### <a name="operating-system-disk"></a>Operativsystemdisk

En operativsystemdisk är en virtuell hårddisk som du kan starta och montera som **C** en version av ett operativsystem som körs och som är märkt som C-enhet.

Standardcacheningsprincipen på operativsystemets disk är **Läs/Skriv**. För prestandakänsliga program rekommenderar vi att du använder datadiskar i stället för operativsystemdisken. Se avsnittet om datadiskar nedan.

### <a name="temporary-disk"></a>Tillfällig disk

Den tillfälliga lagringsenheten, **D** märkt som D-enheten, sparas inte i Azure-bloblagring. Lagra inte användardatabasfiler eller användartransaktionsloggfiler på D:-enheten. **D**

Placera TempDB på den `D:\` lokala SSD-enheten för verksamhetskritiska SQL Server-arbetsbelastningar (efter att ha valt rätt VM-storlek). Om du skapar den virtuella datorn från Azure-portalen eller Azure-snabbstartsmallar och [placerar Temp DB på den lokala disken](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583)behöver du ingen ytterligare åtgärd. för alla andra fall följ stegen i bloggen för [att använda SSD för att lagra TempDB](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) för att förhindra fel efter omstarter. Om kapaciteten för den lokala enheten inte är tillräcklig för temp DB-storleken placerar du Temp DB på en [lagringspool som tas bort](../premium-storage-performance.md) på premium-SSD-diskar med [skrivskyddad cachelagring](../premium-storage-performance.md#disk-caching).

För virtuella datorer som stöder premium-SSD-enheter kan du även lagra TempDB på en disk som stöder premium-SSD-enheter med läscacheaktiverad.


### <a name="data-disks"></a>Datadiskar

* **Använd premium SSD-diskar för data och loggfiler:** Om du inte använder disklistning använder du två premium-SSD-diskar där den ena disken innehåller loggfilen och den andra innehåller data. Varje premium SSD ger ett antal IOPS och bandbredd (MB / s) beroende på dess storlek, som visas i artikeln, [Välj en disktyp](../disks-types.md). Om du använder en diskremsningsteknik, till exempel Lagringsutrymmen, uppnår du optimal prestanda genom att ha två pooler, en för loggfilerna och den andra för datafilerna. Om du planerar att använda SQL Server redundansklusterinstanser (FCI) måste du dock konfigurera en pool eller använda [premiumfilresurser](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) i stället.

   > [!TIP]
   > - Testresultat på olika disk- och arbetsbelastningskonfigurationer finns i följande blogginlägg: [Riktlinjer för lagringskonfiguration för SQL Server på Azure VM](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/).
   > - För verksamhetskritiska prestanda för SQL-servrar som behöver ~ 50 000 IOPS kan du överväga att ersätta 10 -P30-diskar med en Ultra SSD. Mer information finns i följande blogginlägg: [Verksamhetskritiska prestanda med Ultra SSD](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/).

   > [!NOTE]
   > När du etablerar en virtuell SQL Server-dator i portalen kan du redigera lagringskonfigurationen. Beroende på din konfiguration konfigurerar Azure en eller flera diskar. Flera diskar kombineras till en enda lagringspool med striping. Både data- och loggfilerna finns tillsammans i den här konfigurationen. Mer information finns i [Lagringskonfiguration för virtuella SQL Server-datorer](virtual-machines-windows-sql-server-storage-configuration.md).

* **Disk striping:** För mer dataflöde kan du lägga till ytterligare datadiskar och använda Disk Striping. För att bestämma antalet datadiskar måste du analysera antalet IOPS och bandbredd som krävs för loggfilerna och för dina data- och TempDB-filer. Observera att olika vm-storlekar har olika gränser för antalet IOPs och bandbredd som stöds, se tabellerna på IOPS per [VM-storlek](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Använd följande riktlinjer:

  * För Windows 8/Windows Server 2012 eller senare använder du [Lagringsutrymmen](https://technet.microsoft.com/library/hh831739.aspx) med följande riktlinjer:

      1. Ställ in interleave (stripe-storlek) till 64 KB (65 536 byte) för OLTP-arbetsbelastningar och 256 KB (262 144 byte) för datalagringsarbetsbelastningar för att undvika prestandapåverkan på grund av partitionsförskjutning. Detta måste ställas in med PowerShell.
      2. Ange kolumnantal = antal fysiska diskar. Använd PowerShell när du konfigurerar fler än 8 diskar (inte Serverhanterarens användargränssnitt). 

    Följande PowerShell skapar till exempel en ny lagringspool med mellanlagringsstorleken till 64 kB och antalet kolumner till 2:

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * För Windows 2008 R2 eller tidigare kan du använda dynamiska diskar (OS-randiga volymer) och stripe-storleken är alltid 64 KB. Det här alternativet är inaktuellt från och med Windows 8/Windows Server 2012. Information finns i supportsatsen på [Virtual Disk Service övergår till Windows Storage Management API](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

  * Om du använder [S2D (Storage Spaces Direct)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) med [SQL Server Redundansklusterinstanser](virtual-machines-windows-portal-sql-create-failover-cluster.md)måste du konfigurera en enda pool. Även om olika volymer kan skapas på den enskilda poolen, kommer de alla att dela samma egenskaper, till exempel samma cachelagringsprincip.

  * Bestäm antalet diskar som är associerade med lagringspoolen baserat på dina belastningsförväntningar. Tänk på att olika vm-storlekar tillåter olika antal anslutna datadiskar. Mer information finns i [Storlekar för virtuella datorer](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Om du inte använder premium-SSD-enheter (utvecklings-/testscenarier) är rekommendationen att lägga till det maximala antalet datadiskar som stöds av [vm-storleken](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) och använda Disk striping.

* **Cachelagringsprincip:** Observera följande rekommendationer för cachelagringsprincipen beroende på din lagringskonfiguration.

  * Om du använder separata diskar för data- och loggfiler aktiverar du läscachelagring på datadiskarna som är värdar för dina datafiler och TempDB-datafiler. Detta kan resultera i en betydande prestandafördel. Aktivera inte cachelagring på disken som håller loggfilen eftersom detta medför en mindre minskning av prestanda.

  * Om du använder disklistning i en enda lagringspool kan de flesta arbetsbelastningar dra nytta av att läsa cachelagring. Om du har separata lagringspooler för logg- och datafiler aktiverar du endast läsa cachelagring i lagringspoolen för datafilerna. I vissa tunga skrivarbetsbelastningar kan bättre prestanda uppnås utan cachelagring. Detta kan endast bestämmas genom testning.

  * De tidigare rekommendationerna gäller för premium-SSD-enheter. Om du inte använder premium-SSD-enheter ska du inte aktivera cachelagring på några datadiskar.

  * Instruktioner om hur du konfigurerar diskcachelagring finns i följande artiklar. För den klassiska (ASM) distributionsmodellen se: [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) och [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx). För distributionsmodellen för Azure Resource Manager finns [i: Set-AzOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) och [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdatadisk).

     > [!WARNING]
     > Stoppa SQL Server-tjänsten när du ändrar cacheinställningen för Azure VM-diskar för att undvika risken för eventuella skadade databaser.

* **NTFS allokeringsenhet storlek:** När du formaterar datadisken, rekommenderar vi att du använder en 64 KB allokeringsenhet storlek för data och loggfiler samt TempDB. Om TempDB placeras på den temporära disken (D:\ enhet) den prestanda som uppnås genom att utnyttja denna enhet överväger behovet av en 64K allokeringsenhet storlek. 

* **Metodtips för diskhantering:** När du tar bort en datadisk eller ändrar dess cachetyp stoppar du SQL Server-tjänsten under ändringen. När cachelagringsinställningarna ändras på OS-disken stoppar Azure den virtuella datorn, ändrar cachetypen och startar om den virtuella datorn. När cacheinställningarna för en datadisk ändras stoppas inte den virtuella datorn, men datadisken tas bort från den virtuella datorn under ändringen och fästs sedan igen.

  > [!WARNING]
  > Om SQL Server-tjänsten inte stoppas under dessa åtgärder kan det leda till att databasen skadas.


## <a name="io-guidance"></a>I/O-vägledning

* De bästa resultaten med premium SSD uppnås när du parallelliserar ditt program och önskemål. Premium SSD:er är utformade för scenarier där IO-ködjupet är större än 1, så du kommer att se små eller inga prestandavinster för seriella begäranden med enkel tråd (även om de är lagringsintensiva). Detta kan till exempel påverka testresultaten med en tråd för prestandaanalysverktyg, till exempel SQLIO.

* Överväg att använda [komprimering](https://msdn.microsoft.com/library/cc280449.aspx) av databassidan eftersom det kan bidra till att förbättra prestanda för I/O-intensiva arbetsbelastningar. Datakomprimeringen kan dock öka processorförbrukningen på databasservern.

* Överväg att aktivera direktfilinitualisering för att minska den tid som krävs för den ursprungliga filallokeringen. Om du vill dra nytta av direktinitiering av filer beviljar du SQL Server-tjänstkontot (MSSQLSERVER) med SE_MANAGE_VOLUME_NAME och lägger till det i säkerhetsprincipen **Utför volymunderhållsuppgifter.** Om du använder en SQL Server-plattformsavbildning för Azure läggs inte standardtjänstkontot (NT Service\MSSQLSERVER) till i säkerhetsprincipen **Utför volymunderhållsuppgifter.** Med andra ord är direktfilinitiering inte aktiverad i en SQL Server Azure-plattformsavbildning. När du har lagt till SQL Server-tjänstkontot i säkerhetsprincipen **Utför volymunderhållsuppgifter** startar du om SQL Server-tjänsten. Det kan finnas säkerhetsaspekter för att använda den här funktionen. Mer information finns i [Initiering av databasfil](https://msdn.microsoft.com/library/ms175935.aspx).

* **autogrow** anses vara endast en beredskap för oväntad tillväxt. Hantera inte dina data och logga tillväxt på en daglig basis med autogrow. Om automatisk tillväxt används förväxning av filen med hjälp av storleksväxeln.

* Se till att **autoshrink** är inaktiverat för att undvika onödiga omkostnader som kan påverka prestanda negativt.

* Flytta alla databaser till datadiskar, inklusive systemdatabaser. Mer information finns i [Flytta systemdatabaser](https://msdn.microsoft.com/library/ms345408.aspx).

* Flytta SQL Server-felloggen och spåra filkataloger till datadiskar. Detta kan göras i SQL Server Configuration Manager genom att högerklicka på SQL Server-instansen och välja egenskaper. Inställningarna för felloggen och spårningsfilen kan ändras på fliken **Startparametrar.** Dumpkatalogen anges på fliken **Avancerat.** Följande skärmbild visar var du ska leta efter startparametern för felloggen.

    ![Skärmbild av SQL ErrorLog](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* Konfigurera standardplatser för säkerhetskopiering och databasfil. Använd rekommendationerna i den här artikeln och gör ändringarna i fönstret Serveregenskaper. Instruktioner finns i [Visa eller ändra standardplatser för data och loggfiler (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Följande skärmdump visar var du ska göra dessa ändringar.

    ![SQL Data Log- och Backup-filer](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* Aktivera låsta sidor för att minska I/O och eventuella personsökningsaktiviteter. Mer information finns [i Aktivera alternativet Lås sidor i minnet (Windows).](https://msdn.microsoft.com/library/ms190730.aspx)

* Om du kör SQL Server 2012 installerar du kumulativ uppdatering 10 i Service Pack 1. Den här uppdateringen innehåller korrigeringen för dåliga prestanda på I/O när du kör välj till tillfälligt tabelluttryck i SQL Server 2012. Mer information finns i den här [kunskapsbasartikeln](https://support.microsoft.com/kb/2958012).

* Överväg att komprimera datafiler när du överför in/ut från Azure.

## <a name="feature-specific-guidance"></a>Funktionsspecifik vägledning

Vissa distributioner kan uppnå ytterligare prestandafördelar med mer avancerade konfigurationstekniker. I följande lista framhäver några SQL Server-funktioner som kan hjälpa dig att uppnå bättre prestanda:

### <a name="back-up-to-azure-storage"></a>Säkerhetskopiera till Azure Storage
När du utför säkerhetskopior för SQL Server som körs i virtuella Azure-datorer kan du använda [SQL Server Backup till URL](https://msdn.microsoft.com/library/dn435916.aspx). Den här funktionen är tillgänglig från och med SQL Server 2012 SP1 CU2 och rekommenderas för säkerhetskopiering till anslutna datadiskar. När du säkerhetskopierar/återställer till/från Azure-lagring följer du rekommendationerna i [SQL Server Backup till metodtips för URL och felsöker och återställer från säkerhetskopior som lagras i Azure Storage](https://msdn.microsoft.com/library/jj919149.aspx). Du kan också automatisera dessa säkerhetskopior med [automatisk säkerhetskopiering för SQL Server i Virtuella Azure-datorer](virtual-machines-windows-sql-automated-backup.md).

Innan SQL Server 2012 kan du använda [SQL Server Backup till Azure Tool](https://www.microsoft.com/download/details.aspx?id=40740). Det här verktyget kan bidra till att öka säkerhetskopieringen med hjälp av flera mål för säkerhetskopieringsredom.

### <a name="sql-server-data-files-in-azure"></a>SQL Server-datafiler i Azure

Den här nya funktionen, [SQL Server Data Files i Azure](https://msdn.microsoft.com/library/dn385720.aspx), är tillgänglig från och med SQL Server 2014. Om du kör SQL Server med datafiler i Azure visas jämförbara prestandaegenskaper som att använda Azure-datadiskar.

### <a name="failover-cluster-instance-and-storage-spaces"></a>Redundansklusterinstans och lagringsutrymmen

Om du använder Lagringsutrymmen avmarkerar du kryssrutan **Lägg till all kvalificerad lagring i klustret**när du lägger till noder i **klustret** på sidan Bekräftelse . 

![Avmarkera berättigad lagring](media/virtual-machines-windows-sql-performance/uncheck-eligible-cluster-storage.png)

Om du använder Lagringsutrymmen och inte **avmarkerar Lägg till all kvalificerad lagring i klustret**kopplar Windows bort de virtuella diskarna under klustringsprocessen. Därför visas de inte i Diskhanteraren eller Utforskaren förrän lagringsutrymmena tas bort från klustret och återansluts med PowerShell. Lagringsutrymmen grupperar flera diskar i lagringspooler. Mer information finns i [Lagringsutrymmen](/windows-server/storage/storage-spaces/overview).

## <a name="next-steps"></a>Nästa steg

Mer information om lagring och prestanda finns i [Riktlinjer för lagringskonfiguration för SQL Server på Azure VM](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)

Metodtips för säkerhet finns i [Säkerhetsöverväganden för SQL Server i virtuella Azure-datorer](virtual-machines-windows-sql-security.md).

Granska andra SQL Server Virtual Machine-artiklar på [SQL Server på Azure Virtual Machines Overview](virtual-machines-windows-sql-server-iaas-overview.md). Om du har frågor om virtuella SQL Server-datorer kan du läsa [Vanliga frågor](virtual-machines-windows-sql-server-iaas-faq.md).
