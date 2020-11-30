---
title: Rikt linjer för prestanda för SQL Server i Azure | Microsoft Docs
description: Innehåller rikt linjer för att optimera SQL Server prestanda i Microsoft Azure Virtual Machines.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/18/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 6a6b39d540427b7c3400fded62431c914db23bb3
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327329"
---
# <a name="performance-guidelines-for-sql-server-on-azure-virtual-machines"></a>Prestandariktlinjer för SQL Server på virtuella Azure-datorer
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Den här artikeln innehåller rikt linjer för att optimera SQL Server prestanda i Microsoft Azure Virtual Machines.

## <a name="overview"></a>Översikt

 När du kör SQL Server på Azure-Virtual Machines rekommenderar vi att du fortsätter att använda samma databas prestanda justerings alternativ som gäller för SQL Server i lokala Server miljöer. Prestanda för en relationsdatabas i ett offentligt moln beror dock på många faktorer, till exempel storleken på en virtuell dator och konfigurationen av datadiskar.

[SQL Server avbildningar som tillhandahålls i Azure Portal följer de](sql-vm-create-portal-quickstart.md) allmänna metod tipsen för lagrings konfiguration (mer information om hur lagrings utrymmet konfigureras finns i [lagrings konfiguration för virtuella](storage-configuration.md)datorer i SQL Server). Efter etableringen bör du överväga att använda andra optimeringar som diskuteras i den här artikeln. Basera dina val på arbets belastningen och kontrol lera genom att testa.

> [!TIP]
> Det finns vanligt vis en kompromiss mellan optimering av kostnader och optimering för prestanda. Den här artikeln fokuserar på att få *bästa möjliga* prestanda för SQL Server på Azure Virtual Machines. Om din arbets belastning är mindre krävande kanske du inte behöver varje optimering i listan nedan. Överväg dina prestanda behov, kostnader och arbets belastnings mönster när du utvärderar dessa rekommendationer.

## <a name="quick-checklist"></a>Snabb check lista

Följande är en snabb check lista för optimala prestanda för SQL Server på Azure Virtual Machines:

| Område | Optimeringar |
| --- | --- |
| [VM-storlek](#vm-size-guidance) | -Använd VM-storlekar med 4 eller fler vCPU som [E4S_v3](../../../virtual-machines/ev3-esv3-series.md) eller högre eller [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md) eller högre.<br/><br/> - [ES, EAS, DS och Das-serien](../../../virtual-machines/sizes-general.md) ger optimalt minne till vCPU-förhållandet som krävs för OLTP-arbetsbelastningens prestanda. <br/><br/> - [M-serien](../../../virtual-machines/m-series.md) erbjuder det högsta förhållandet mellan minne och vCPU som krävs för uppdrags kritiska prestanda och är perfekt för arbets belastningar för data lager. <br/><br/> – Samla in mål arbets Belastningens [IOPS](../../../virtual-machines/premium-storage-performance.md#iops), [data flöde](../../../virtual-machines/premium-storage-performance.md#throughput)  och [latens](../../../virtual-machines/premium-storage-performance.md#latency) krav vid hög belastnings tider genom att följa check listan för [program prestanda krav](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist) och välj sedan den [VM-storlek](../../../virtual-machines/sizes-general.md) som kan skalas till arbets Belastningens prestanda krav.|
| [Storage](#storage-guidance) | – För detaljerad testning av SQL Server prestanda i Azure Virtual Machines med TPC-E och TPC_C benchmarks, se bloggen [optimera OLTP-prestanda](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). <br/><br/> – Använd [Premium-SSD](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794) för bästa pris/prestanda-fördelar. Konfigurera [ReadOnly cache](../../../virtual-machines/premium-storage-performance.md#disk-caching) för datafiler och ingen cache för logg filen. <br/><br/> – Använd [Ultra disks](../../../virtual-machines/disks-types.md#ultra-disk) om mindre än 1 MS-lagrings fördröjning krävs av arbets belastningen. Mer information finns i [migrera till Ultra disk](storage-migrate-to-ultradisk.md) . <br/><br/> – Samla in krav på lagrings fördröjning för SQL Server data, logg och tillfälliga DB-filer genom att [övervaka programmet](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist) innan du väljer disk typen. Om <1ms lagrings fördröjning krävs, använder du Ultra disks, annars använder du Premium SSD. Om låg latens bara krävs för logg filen och inte för datafiler, [etablerar du den Ultra-disken](../../../virtual-machines/disks-enable-ultra-ssd.md) vid nödvändiga IOPS-och data flödes nivåer för logg filen. <br/><br/> -  [Premium-filresurser](failover-cluster-instance-premium-file-share-manually-configure.md) rekommenderas som delad lagring för en SQL Server instans av redundanskluster. Premium-filresurser stöder inte cachelagring, och erbjuder begränsade prestanda jämfört med Premium SSD-diskar. Välj Premium SSD – hanterade diskar över Premium-filresurser för fristående SQL-instanser; men Använd Premium-filresurser för delad lagring av redundanskluster för att förenkla underhåll och flexibel skalbarhet. <br/><br/> – Standard lagring rekommenderas endast för utvecklings-och test syfte eller för säkerhets kopiering av filer och bör inte användas för produktions arbets belastningar. <br/><br/> -Behåll [lagrings kontot](../../../storage/common/storage-account-create.md) och SQL Server VM i samma region.<br/><br/> -Inaktivera Azure [Geo-redundant lagring](../../../storage/common/storage-redundancy.md) (geo-replikering) på lagrings kontot.  |
| [Diskar](#disks-guidance) | – Använd minst 2 [Premium SSD-diskar](../../../virtual-machines/disks-types.md#premium-ssd) (1 för logg filen och 1 för datafiler). <br/><br/> – För arbets belastningar som kräver <1 MS IO-fördröjning aktiverar du Skriv Accelerator för M-serien och använder Ultra SSD diskar för ES och DS-serien. <br/><br/> -Aktivera [skrivskyddad cachelagring](../../../virtual-machines/premium-storage-performance.md#disk-caching) på diskarna som är värdar för datafilerna.<br/><br/> – Lägg till ytterligare 20% Premium IOPS/data flödes kapacitet än vad arbets belastningen kräver när du [konfigurerar lagring för SQL Server data-, logg-och tempdb-filer](storage-configuration.md) <br/><br/> – Undvik att använda operativ system eller temporära diskar för databas lagring eller loggning.<br/><br/> – Aktivera inte cachelagring på disk (er) som är värd för logg filen.  **Viktigt**: stoppa SQL Server-tjänsten när du ändrar cache-inställningarna för en Azure Virtual Machines-disk.<br/><br/> – Stripa flera Azure-datadiskar för att få större lagrings data flöde.<br/><br/> -Format med dokumenterade fördelnings storlekar. <br/><br/> -Placera TempDB på den lokala SSD- `D:\` enheten för verksamhets kritiska SQL Server arbets belastningar (efter att ha valt rätt storlek på virtuell dator). Om du skapar den virtuella datorn från Azure Portal eller Azures snabb starts mallar och [placerar Temp-databasen på den lokala disken](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583) behöver du inte vidta några ytterligare åtgärder. i alla andra fall följer du stegen i bloggen för  [att använda SSD för att lagra tempdb](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) för att förhindra problem efter omstarter. Om den lokala enhetens kapacitet inte räcker för den tillfälliga databas storleken ska du placera Temp DB i en lagringspool som är [stripad](../../../virtual-machines/premium-storage-performance.md) på Premium SSD-diskar med [skrivskyddad cachelagring](../../../virtual-machines/premium-storage-performance.md#disk-caching). |
| [I/O](#io-guidance) |-Aktivera komprimering av databas sidan.<br/><br/> -Aktivera omedelbar fil initiering för datafiler.<br/><br/> – Begränsa den ökande storleken på databasen.<br/><br/> -Inaktivera autokrympning av databasen.<br/><br/> – Flytta alla databaser till data diskar, inklusive system databaser.<br/><br/> – Flytta SQL Server fel logg och spåra fil kataloger till data diskar.<br/><br/> – Konfigurera standard platser för säkerhets kopiering och databas fil.<br/><br/> - [Aktivera låsta sidor i minnet](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows?view=sql-server-2017).<br/><br/> -Använd SQL Server prestanda korrigeringar. |
| [Funktions-/regionsspecifika](#feature-specific-guidance) | – Säkerhetskopiera direkt till Azure Blob Storage.<br/><br/>-Använd [säkerhets kopior av fil ögonblicks bilder](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) för databaser som är större än 12 TB. <br/><br/>-Använd flera tillfälliga DB-filer, 1 fil per kärna, upp till 8 filer.<br/><br/>– Ange max server minne på 90% eller upp till 50 GB kvar för operativ systemet. <br/><br/>-Aktivera mjuk NUMA. |

Mer information om *hur* och *varför* du kan göra dessa optimeringar finns i informationen och rikt linjerna i följande avsnitt.

## <a name="vm-size-guidance"></a>Vägledning för VM-storlek

Börja med att samla in krav på processor, minne och lagring av data flöde för arbets belastningen vid hög belastnings tider. Prestanda räknare för \LogicalDisk\Disk läsningar/s och \LogicalDisk\Disk skrivningar/s kan användas för att samla in Läs-och skriv IOPS-krav och \LogicalDisk\Disk byte/s-räknaren kan användas för att samla in [krav på lagrings data flöde](../../../virtual-machines/premium-storage-performance.md#disk-caching) för data-, logg-och temp DB-filer. Efter att IOPS-och data flödes krav vid hög storlek definierats, erbjuder utvärdera VM-storlekar den kapaciteten. Om din arbets belastning till exempel kräver 20 K Läs IOPS och 10 000 Skriv åtgärder med hög belastning kan du antingen välja E16s_v3 (med upp till 32 kB cache-lagrad och 25600 Uncached IOPS) eller M16_s (med upp till 20 K cachelagrat och 10 000 Uncached IOPS) med 2 P30 diskar. Se till att förstå både genomflödet och IOPS-krav för arbets belastningen eftersom virtuella datorer har olika skalnings gränser för IOPS och data flöde.<br/><br/>[DSv_3](../../../virtual-machines/dv3-dsv3-series.md) och [Es_v3-serien](../../../virtual-machines/ev3-esv3-series.md) finns i maskin vara för generell användning med Intel Haswell-eller Broadwell-processorer. [M-serien](../../../virtual-machines/m-series.md) erbjuder högst antal vCPU och minne för de största SQL Server arbets belastningarna och finns på minnesoptimerade maskin vara med Skylake processor familj. Den här VM-serien har stöd för Premium Storage, vilket rekommenderas för bästa prestanda med Läs-cache på värdnivå. Både Es_v3-och M-serien är också tillgängliga i [begränsade kärn storlekar](../../../virtual-machines/constrained-vcpu.md), vilket sparar pengar för arbets belastningar med lägre prestanda för beräkning och hög lagring. 

## <a name="storage-guidance"></a>Minnesriktlinjer

För detaljerad testning av SQL Server prestanda i Azure Virtual Machines med TPC-E och TPC_C benchmarks, se bloggen [optimera OLTP-prestanda](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). 

Azure Blob-cache med Premium-SSD rekommenderas för alla produktions arbets belastningar. 

> [!WARNING]
> Standard hård diskar och SSD har varierande latens och bandbredd och rekommenderas bara för arbets belastningar för utveckling/testning. Arbets belastningar för produktion bör använda Premium-SSD.

Dessutom rekommenderar vi att du skapar ditt Azure Storage-konto i samma data Center som SQL Server virtuella datorer för att minska överförings fördröjningarna. När du skapar ett lagrings konto är det inte säkert att inaktivera geo-replikering som konsekvent Skriv ordning på flera diskar. I stället kan du konfigurera en SQL Server katastrof återställnings teknik mellan två Azure-datacenter. Mer information finns i [Hög tillgänglighet och haveriberedskap för SQL Server på Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md).

## <a name="disks-guidance"></a>Vägledning för diskar

Det finns tre typer av huvud diskar på virtuella Azure-datorer:

* **OS-disk**: när du skapar en virtuell Azure-dator ansluter plattformen minst en disk (märkt som **C** -enheten) till den virtuella datorn för operativ system disken. Den här disken är en virtuell hård disk som lagras som en sid-BLOB i lagring.
* **Temporär disk**: virtuella Azure-datorer innehåller en annan disk som kallas temporär disk (märkt som **D**: Drive). Det här är en disk på noden som kan användas för scratch Space.
* **Data diskar**: du kan också koppla ytterligare diskar till den virtuella datorn som data diskar och de lagras i lagrings utrymmet som Page blobbar.

I följande avsnitt beskrivs rekommendationer för att använda de olika diskarna.

### <a name="operating-system-disk"></a>Operativsystemdisk

En operativ system disk är en virtuell hård disk som du kan starta och montera som en aktiv version av ett operativ system och som är märkt som **C** -enheten.

Standard principen för cachelagring på operativ system disken är **läsa/skriva**. För prestanda känsliga program rekommenderar vi att du använder data diskar i stället för operativ system disken. Se avsnittet på data diskar nedan.

### <a name="temporary-disk"></a>Tillfällig disk

Den tillfälliga lagrings enheten, märkt som **D** -enheten, är inte beständig i Azure Blob Storage. Lagra inte dina användar databas filer eller loggfiler för användar transaktioner på enheten **D**:.

Placera TempDB på den lokala SSD- `D:\` enheten för verksamhets kritiska SQL Server arbets belastningar (efter att ha valt rätt storlek på virtuell dator). Om du skapar den virtuella datorn från Azure Portal eller Azures snabb starts mallar och [placerar Temp-databasen på den lokala disken](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583)behöver du inte vidta några ytterligare åtgärder. i alla andra fall följer du stegen i bloggen för  [att använda SSD för att lagra tempdb](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) för att förhindra problem efter omstarter. Om den lokala enhetens kapacitet inte räcker för den tillfälliga databas storleken ska du placera Temp DB i en lagringspool som är [stripad](../../../virtual-machines/premium-storage-performance.md) på Premium SSD-diskar med [skrivskyddad cachelagring](../../../virtual-machines/premium-storage-performance.md#disk-caching).

För virtuella datorer som stöder Premium-SSD kan du också lagra TempDB på en disk som har stöd för Premium-SSD med Read caching Enabled.


### <a name="data-disks"></a>Datadiskar

* **Använd Premium SSD-diskar för data-och loggfiler**: om du inte använder disk ränder använder du två Premium SSD-diskar där en disk innehåller logg filen och den andra innehåller data. Varje Premium SSD innehåller ett antal IOPS och bandbredd (MB/s) beroende på dess storlek, som visas i artikeln, [Välj en disktyp](../../../virtual-machines/disks-types.md). Om du använder en disk Rands teknik, till exempel lagrings utrymmen, uppnår du optimala prestanda genom att ha två pooler, en för loggfilerna och den andra för datafilerna. Men om du planerar att använda SQL Server-instanser för redundanskluster (FCI), måste du konfigurera en pool eller använda [Premium-filresurser](failover-cluster-instance-premium-file-share-manually-configure.md) i stället.

   > [!TIP]
   > - För test resultat av olika konfigurationer av diskar och arbets belastningar kan du läsa följande blogg inlägg: [rikt linjer för lagrings konfiguration för SQL Server på Azure Virtual Machines](/archive/blogs/sqlserverstorageengine/storage-configuration-guidelines-for-sql-server-on-azure-vm).
   > - För uppdrags kritiska prestanda för SQL-servrar som behöver ~ 50 000 IOPS bör du överväga att ersätta 10-P30 diskar med en Ultra SSD. Mer information finns i följande blogg inlägg: [verksamhets kritisk prestanda med Ultra SSD](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/).

   > [!NOTE]
   > När du etablerar en SQL Server VM i portalen har du möjlighet att redigera lagrings konfigurationen. Beroende på din konfiguration konfigurerar Azure en eller flera diskar. Flera diskar kombineras till en enda lagringspool med striping. Både data-och loggfilerna finns tillsammans i den här konfigurationen. Mer information finns i [lagrings konfiguration för SQL Server virtuella datorer](storage-configuration.md).

* **Disk-striping**: för mer data flöde kan du lägga till ytterligare data diskar och använda disk ränder. Om du vill fastställa antalet data diskar måste du analysera antalet IOPS och bandbredd som krävs för dina loggfiler, och för dina data och TempDB-fil (er). Observera att olika storlekar på virtuella datorer har olika begränsningar för antalet IOPs och bandbredd som stöds, se tabellerna på IOPS per [VM-storlek](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Använd följande riktlinjer:

  * För Windows 8/Windows Server 2012 eller senare använder du [lagrings utrymmen](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11)) med följande rikt linjer:

      1. Ange överlagring (stripe-storlek) till 64 KB (65 536 byte) för OLTP-arbetsbelastningar och 256 KB (262 144 byte) för arbets belastningar för data lager för att undvika prestanda påverkan på grund av feljustering av partitionen. Detta måste anges med PowerShell.
      2. Ange kolumn antal = antal fysiska diskar. Använd PowerShell när du konfigurerar fler än 8 diskar (inte Serverhanteraren UI). 

    Följande PowerShell skapar till exempel en ny lagringspool med Överlagrings storleken till 64 KB och antalet kolumner som motsvarar mängden fysisk disk i lagringspoolen:

    ```powershell
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
    
    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" `
        -PhysicalDisks $PhysicalDisks | New- VirtualDisk -FriendlyName "DataFiles" `
        -Interleave 65536 -NumberOfColumns $PhysicalDisks .Count -ResiliencySettingName simple `
        –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
        -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
        -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * För Windows 2008 R2 eller tidigare kan du använda dynamiska diskar (OS Striped-volymer) och stripe-storleken är alltid 64 KB. Det här alternativet är föråldrat från och med Windows 8/Windows Server 2012. Mer information finns i support policyn på [Virtual Disk Service över gång till Windows Storage Management API](/windows/win32/w8cookbook/vds-is-transitioning-to-wmiv2-based-windows-storage-management-api).

  * Om du använder [Lagringsdirigering (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) med [SQL Server kluster instanser för växling vid fel](failover-cluster-instance-storage-spaces-direct-manually-configure.md)måste du konfigurera en enda pool. Även om olika volymer kan skapas på samma pool, kommer alla att dela samma egenskaper, till exempel samma princip för cachelagring.

  * Fastställ antalet diskar som är kopplade till lagringspoolen baserat på dina belastnings förväntningar. Tänk på att olika storlekar på virtuella datorer tillåter olika antal anslutna data diskar. Mer information finns i [storlekar för virtuella datorer](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Om du inte använder Premium-SSD (utvecklings-och test scenarier) är rekommendationen att lägga till det maximala antalet data diskar som stöds av [storleken](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) på den virtuella datorn och använda disk ränder.

* **Princip för cachelagring**: Observera följande rekommendationer för cachelagring av principer beroende på lagrings konfigurationen.

  * Om du använder separata diskar för data-och loggfiler aktiverar du cachelagring på data diskarna som är värdar för dina datafiler och TempDB-datafiler. Detta kan resultera i en betydande prestanda förmån. Aktivera inte cachelagring på disken som innehåller logg filen eftersom detta orsakar en mindre minskning av prestanda.

  * Om du använder disk ränder i en enda lagringspool kommer de flesta arbets belastningarna att ha nytta av cachelagring av läsningar. Om du har separata lagringspooler för logg-och datafilerna aktiverar du enbart cachelagring på lagringspoolen för datafilerna. I vissa tunga Skriv arbets belastningar kan bättre prestanda uppnås utan cachelagring. Detta kan bara bestämmas genom testning.

  * De tidigare rekommendationerna gäller för Premium-SSD. Om du inte använder Premium-SSD ska du inte aktivera cachelagring på några data diskar.

  * Instruktioner för hur du konfigurerar diskcachelagring finns i följande artiklar. För den klassiska (ASM) distributions modellen se: [set-AzureOSDisk](/previous-versions/azure/jj152847(v=azure.100)) och [set-AzureDataDisk](/previous-versions/azure/jj152851(v=azure.100)). Azure Resource Manager distributions modell finns i: [set-AzOSDisk](/powershell/module/az.compute/set-azvmosdisk) och [set-AzVMDataDisk](/powershell/module/az.compute/set-azvmdatadisk).

     > [!WARNING]
     > Stoppa SQL Server-tjänsten när du ändrar cache-inställningen för Azure Virtual Machines disks för att undvika risken för att databasen skadas.

* **Storlek på NTFS-allokeringsenhet**: när du formaterar data disken rekommenderar vi att du använder en storlek på 64 KB-allokeringsenhet för data-och loggfiler samt tempdb. Om TempDB placeras på den temporära disken (D:\ enhet) den prestanda som uppnås genom att använda den här enheten drar av behovet av en storlek på en 64-allokeringsenhet. 

* **Metod tips för disk hantering**: när du tar bort en data disk eller ändrar dess cachestorlek stoppar du SQL Server tjänsten under ändringen. När cacheinställningar ändras på OS-disken stoppar Azure den virtuella datorn, ändrar cacheplatsen och startar om den virtuella datorn. När cache-inställningarna för en datadisk ändras, stoppas inte den virtuella datorn, men datadisken kopplas bort från den virtuella datorn under ändringen och sedan återkopplas.

  > [!WARNING]
  > Om du inte stoppar SQL Server tjänsten under dessa åtgärder kan databasen skadas.


## <a name="io-guidance"></a>I/O-vägledning

* De bästa resultaten med Premium-SSD uppnås när du parallellisera ditt program och begär Anden. Premium-SSD är utformade för scenarier där IO-ködjup är större än 1, så att du kan se små eller inga prestanda vinster för en enkel tråds seriella begär Anden (även om de är minnes krävande). Detta kan till exempel påverka de enkla test resultaten för prestanda analys verktyg, till exempel SQLIO.

* Överväg att använda [databasens sid komprimering](/sql/relational-databases/data-compression/data-compression) för att förbättra prestanda för i/O-intensiva arbets belastningar. Data komprimeringen kan dock öka processor förbrukningen på databas servern.

* Överväg att aktivera omedelbar fil initiering för att minska den tid som krävs för den första filallokeringen. Om du vill dra nytta av omedelbar fil initiering beviljar du tjänst kontot SQL Server (MSSQLSERVER) med SE_MANAGE_VOLUME_NAME och lägger till det i säkerhets principen **utföra volym underhålls aktiviteter** . Om du använder en SQL Server plattforms avbildning för Azure läggs inte standard tjänst kontot (NT Service\MSSQLSERVER) till i säkerhets principen **utför volym underhålls aktiviteter** . Med andra ord är omedelbar fil initiering inte aktive rad i en SQL Server Azures plattforms avbildning. När du har lagt till SQL Server tjänst kontot i säkerhets principen **utföra volym underhålls aktiviteter** startar du om SQL Server tjänsten. Det kan finnas säkerhets överväganden för att använda den här funktionen. Mer information finns i [databas filens initiering](/sql/relational-databases/databases/database-instant-file-initialization).

* Tänk på att **autoväxer** enbart anses vara en katastrof för oväntad tillväxt. Hantera inte dina data och logga in på en dags-till-dags-basis med automatisk tillväxt. Om automatisk utökning används kan du i förväg förstora filen med storleks växeln.

* Se till att **autokrympning** är inaktiverat för att undvika onödiga kostnader som kan påverka prestanda negativt.

* Flytta alla databaser till data diskar, inklusive system databaser. Mer information finns i [Flytta system databaser](/sql/relational-databases/databases/move-system-databases).

* Flytta SQL Server fel logg och spåra fil kataloger till data diskar. Det kan du göra i Konfigurationshanteraren för SQL Server genom att högerklicka på SQL Server instansen och välja egenskaper. Inställningarna för fel logg och spårnings fil kan ändras på fliken **Start parametrar** . Katalogen dump anges på fliken **Avancerat** . Följande skärm bild visar var du ska leta efter start parametern för fel loggen.

    ![Skärm bild för SQL-logg](./media/performance-guidelines-best-practices/sql_server_error_log_location.png)

* Konfigurera standard platser för säkerhets kopiering och databas fil. Använd rekommendationerna i den här artikeln och gör ändringarna i fönstret Server egenskaper. Instruktioner finns i [Visa eller ändra standard platserna för data-och loggfiler (SQL Server Management Studio)](/sql/database-engine/configure-windows/view-or-change-the-default-locations-for-data-and-log-files). Följande skärm bild visar var du kan göra dessa ändringar.

    ![SQL-datalogg-och säkerhets kopierings filer](./media/performance-guidelines-best-practices/sql_server_default_data_log_backup_locations.png)
* Aktivera låsta sidor för att minska IO-och växlings aktiviteter. Mer information finns i [Aktivera alternativet Lås sidor i minnet (Windows)](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows).

* Om du kör SQL Server 2012 installerar du Service Pack 1 kumulativ uppdatering 10. Uppdateringen innehåller en korrigering av dåliga prestanda för I/O när du kör Välj i tillfälligt tabell uttryck i SQL Server 2012. Mer information finns i den här [artikeln i kunskaps basen](https://support.microsoft.com/kb/2958012).

* Överväg att komprimera datafiler när du överför in/ut ur Azure.

## <a name="feature-specific-guidance"></a>Funktions bestämd vägledning

Vissa distributioner kan få ytterligare prestanda för delar med mer avancerade konfigurations tekniker. I följande lista beskrivs några SQL Server funktioner som kan hjälpa dig att uppnå bättre prestanda:

### <a name="back-up-to-azure-storage"></a>Säkerhetskopiera till Azure Storage
När du säkerhetskopierar SQL Server som körs i Azure Virtual Machines kan du använda [SQL Server säkerhets kopiering till URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url). Den här funktionen är tillgänglig från och med SQL Server 2012 SP1-CU2 och rekommenderas för säkerhets kopiering till anslutna data diskar. När du säkerhetskopierar/återställer till/från Azure Storage följer du rekommendationerna som anges i [SQL Server säkerhetskopiera till URL-rekommenderade metoder och fel sökning och återställning från säkerhets kopior som lagras i Azure Storage](/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting). Du kan också automatisera säkerhets kopieringarna med [Automatisk säkerhets kopiering för SQL Server på Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/automated-backup-sql-2014.md).

Innan du SQL Server 2012 kan du använda [SQL Server säkerhets kopiering till Azure-verktyget](https://www.microsoft.com/download/details.aspx?id=40740). Det här verktyget kan hjälpa till att öka säkerhets kopieringen genom att använda flera säkerhets kopierings stripe-mål.

### <a name="sql-server-data-files-in-azure"></a>SQL Server datafiler i Azure

Den här nya funktionen [SQL Server datafiler i Azure](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure)är tillgänglig från och med SQL Server 2014. Att köra SQL Server med datafiler i Azure visar jämförbara prestanda egenskaper som att använda Azure Data disks.

### <a name="failover-cluster-instance-and-storage-spaces"></a>Instans av kluster för växling vid fel och lagrings utrymmen

Om du använder lagrings utrymmen, när du lägger till noder i klustret på **bekräftelse** sidan, avmarkerar du kryss rutan **Lägg till alla tillgängliga lagrings enheter i klustret**. 

![Avmarkera berättigat lagrings utrymme](./media/performance-guidelines-best-practices/uncheck-eligible-cluster-storage.png)

Om du använder lagrings utrymmen och inte avmarkerar **Lägg till alla tillgängliga lagrings enheter i klustret**, kopplar Windows bort de virtuella diskarna under kluster processen. Därför visas de inte i disk hanteraren eller i Utforskaren förrän lagrings utrymmena har tagits bort från klustret och återkopplas med hjälp av PowerShell. Lagrings utrymmen grupper flera diskar i lagringspooler. Mer information finns i [lagrings utrymmen](/windows-server/storage/storage-spaces/overview).

## <a name="multiple-instances"></a>Flera instanser 

Tänk på följande rekommendationer när du distribuerar flera SQL Server-instanser till en enda virtuell dator: 

- Ange det maximala Server minnet för varje SQL Server instans, och se till att det finns minne kvar för operativ systemet. Se till att uppdatera minnes begränsningarna för SQL Server instanserna om du ändrar hur mycket minne som allokeras till den virtuella datorn. 
- Ha separata LUN för data, loggar och TempDB eftersom de har olika arbets belastnings mönster och du inte vill att de ska påverka varandra. 
- Testa din miljö noggrant under tung produktions-liknande arbets belastningar för att säkerställa att den kan hantera högsta arbets belastnings kapacitet inom din program service avtal. 

Tecken på överbelastade system kan inkludera, men är inte begränsade till, arbets trådens överbelastning, långsamma svars tider och/eller stoppade dispatcher system minne. 




## <a name="next-steps"></a>Nästa steg

Mer information om lagring och prestanda finns i [rikt linjer för lagrings konfiguration för SQL Server på Azure Virtual Machines](/archive/blogs/sqlserverstorageengine/storage-configuration-guidelines-for-sql-server-on-azure-vm)

Rekommenderade säkerhets metoder finns i [säkerhets överväganden för SQL Server på Azure Virtual Machines](security-considerations-best-practices.md).

Granska andra SQL Server virtuella dator artiklar på [SQL Server på Azure Virtual Machines-översikt](sql-server-on-azure-vm-iaas-what-is-overview.md). Om du har frågor om virtuella SQL Server-datorer kan du läsa [Vanliga frågor](frequently-asked-questions-faq.md).