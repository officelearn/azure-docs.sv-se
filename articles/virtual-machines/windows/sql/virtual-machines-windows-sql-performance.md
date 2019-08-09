---
title: Rikt linjer för prestanda för SQL Server i Azure | Microsoft Docs
description: Innehåller rikt linjer för att optimera SQL Server prestanda i Microsoft Azure virtuella datorer.
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
ms.date: 09/26/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: d81c1941f114efbfd4ede559152317e907edeaea
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882394"
---
# <a name="performance-guidelines-for-sql-server-in-azure-virtual-machines"></a>Rikt linjer för prestanda för SQL Server i Azure Virtual Machines

## <a name="overview"></a>Översikt

Den här artikeln innehåller rikt linjer för att optimera SQL Server prestanda i Microsoft Azure virtuella datorn. När du kör SQL Server i Azure Virtual Machines rekommenderar vi att du fortsätter använda samma databas prestanda justerings alternativ som gäller för SQL Server i den lokala Server miljön. Prestanda för en relationsdatabas i ett offentligt moln beror dock på många faktorer, till exempel storleken på en virtuell dator och konfigurationen av datadiskar.

[SQL Server avbildningar som tillhandahålls i Azure Portal följer de](quickstart-sql-vm-create-portal.md) allmänna metod tipsen för lagrings konfiguration (mer information om hur lagrings utrymmet konfigureras finns i [lagrings konfiguration för virtuella datorer i SQL Server](virtual-machines-windows-sql-server-storage-configuration.md)). Efter etableringen bör du överväga att använda andra optimeringar som diskuteras i den här artikeln. Basera dina val på arbets belastningen och kontrol lera genom att testa.

> [!TIP]
> Det finns vanligt vis en kompromiss mellan optimering av kostnader och optimering för prestanda. Den här artikeln fokuserar på att få *bästa möjliga* prestanda för SQL Server på virtuella Azure-datorer. Om din arbets belastning är mindre krävande kanske du inte behöver varje optimering i listan nedan. Överväg dina prestanda behov, kostnader och arbets belastnings mönster när du utvärderar dessa rekommendationer.

## <a name="quick-check-list"></a>Snabb kontroll lista

Följande är en snabb kontroll lista för optimala prestanda för SQL Server på Azure Virtual Machines:

| Område | Optimeringar |
| --- | --- |
| [Storlek på virtuell dator](#vm-size-guidance) | - [DS3_v2](../sizes-general.md) eller högre för SQL Enterprise Edition.<br/><br/> - [DS2_v2](../sizes-general.md) eller högre för SQL standard-och webb versioner. |
| [Storage](#storage-guidance) | – Använd [Premium-SSD](../disks-types.md). Standard lagring rekommenderas endast för utveckling/testning.<br/><br/> -Behåll [lagrings kontot](../../../storage/common/storage-create-storage-account.md) och SQL Server VM i samma region.<br/><br/> * Inaktivera Azure [Geo-redundant lagring](../../../storage/common/storage-redundancy.md) (geo-replikering) på lagrings kontot. |
| [Diskar](#disks-guidance) | – Använd minst 2 [P30 diskar](../disks-types.md#premium-ssd) (1 för loggfiler och 1 för datafiler inklusive tempdb). Överväg att använda en Ultra SSD för arbets belastningar som kräver ~ 50 000 IOPS. <br/><br/> – Undvik att använda operativ system eller temporära diskar för databas lagring eller loggning.<br/><br/> -Aktivera cachelagring av läsning på diskarna som är värdar för datafilerna och TempDB-datafilerna.<br/><br/> – Aktivera inte cachelagring på disk (er) som är värd för logg filen.  **Viktigt**: Stoppa tjänsten SQL Server när du ändrar cache-inställningarna för en virtuell Azure-disk.<br/><br/> – Stripa flera Azure-datadiskar för att få ökat IO-genomflöde.<br/><br/> -Format med dokumenterade fördelnings storlekar. <br/><br/> -Placera tempdb på den lokala SSD `D:\` -enheten för verksamhets kritiska SQL Server arbets belastningar (efter att ha valt rätt storlek på virtuell dator). Mer information i bloggen [med SSD för att lagra tempdb](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/).  |
| [I/O](#io-guidance) |-Aktivera komprimering av databas sidan.<br/><br/> -Aktivera omedelbar fil initiering för datafiler.<br/><br/> – Begränsa den ökande storleken på databasen.<br/><br/> -Inaktivera autokrympning av databasen.<br/><br/> – Flytta alla databaser till data diskar, inklusive system databaser.<br/><br/> – Flytta SQL Server fel logg och spåra fil kataloger till data diskar.<br/><br/> – Konfigurera standard platser för säkerhets kopiering och databas fil.<br/><br/> -Aktivera låsta sidor.<br/><br/> -Använd SQL Server prestanda korrigeringar. |
| [Funktions-/regionsspecifika](#feature-specific-guidance) | – Säkerhetskopiera direkt till Blob Storage. |

Mer information om *hur* och *varför* du kan göra dessa optimeringar finns i informationen och rikt linjerna i följande avsnitt.

## <a name="vm-size-guidance"></a>Vägledning för VM-storlek

För prestanda känsliga program rekommenderar vi att du använder följande [storlekar för virtuella datorer](../sizes.md):

* **SQL Server Enterprises utgåva**: DS3_v2 eller högre
* **SQL Server standard-och webb versioner**: DS2_v2 eller högre

[DSv2-serien](../sizes-general.md#dsv2-series) VM: ar stöder Premium Storage, vilket rekommenderas för bästa prestanda. De storlekar som rekommenderas här är bas linjer, men den faktiska dator storlek som du väljer beror på arbets Belastningens krav. Virtuella datorer i DSv2-serien är allmänna virtuella datorer som är lämpliga för olika arbets belastningar, medan andra maskin storlekar är optimerade för olika arbets belastnings typer. [M-serien](../sizes-memory.md#m-series) erbjuder till exempel det högsta antalet vCPU och minne för de största SQL Server arbets belastningarna. [GS-serien](../sizes-previous-gen.md#gs-series) och [DSv2-serien 11-15](../sizes-memory.md#dsv2-series-11-15) är optimerad för stora minnes krav. Båda dessa serier är också tillgängliga i [begränsade kärn storlekar](../../windows/constrained-vcpu.md), vilket sparar pengar för arbets belastningar med lägre beräknings krav. Datorerna i [ls-serien](../sizes-storage.md) är optimerade för hög disk data flöde och IO. Det är viktigt att fundera över din speciella SQL Server arbets belastning och tillämpa den på ditt val av VM-serien och storlek.

## <a name="storage-guidance"></a>Minnesriktlinjer

DS-serien (tillsammans med DSv2-seriens och GS-serien) virtuella datorer stöder [Premium SSD](../disks-types.md). Premium-SSD rekommenderas för alla produktions arbets belastningar.

> [!WARNING]
> Standard hård diskar och SSD har varierande latens och bandbredd och rekommenderas bara för arbets belastningar för utveckling/testning. Arbets belastningar för produktion bör använda Premium-SSD.

Dessutom rekommenderar vi att du skapar ditt Azure Storage-konto i samma data Center som SQL Server virtuella datorer för att minska överförings fördröjningarna. När du skapar ett lagrings konto är det inte säkert att inaktivera geo-replikering som konsekvent Skriv ordning på flera diskar. I stället kan du konfigurera en SQL Server katastrof återställnings teknik mellan två Azure-datacenter. Mer information finns i [hög tillgänglighet och haveri beredskap för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Vägledning för diskar

Det finns tre typer av huvud diskar på en virtuell Azure-dator:

* **OS-disk**: När du skapar en virtuell Azure-dator ansluter plattformen minst en disk (märkt som **C** -enheten) till den virtuella datorn för operativ system disken. Den här disken är en virtuell hård disk som lagras som en sid-BLOB i lagring.
* **Tillfällig disk**: Virtuella Azure-datorer innehåller en annan disk som kallas temporär disk (märkt som **D**: Drive). Det här är en disk på noden som kan användas för scratch Space.
* **Data diskar**: Du kan också bifoga ytterligare diskar till den virtuella datorn som data diskar och dessa lagras i lagrings utrymmet som Page blobbar.

I följande avsnitt beskrivs rekommendationer för att använda de olika diskarna.

### <a name="operating-system-disk"></a>Operativsystemsdisk

En operativ system disk är en virtuell hård disk som du kan starta och montera som en aktiv version av ett operativ system och är märkt som **C** -enhet.

Standard principen för cachelagring på operativ system disken är **läsa/skriva**. För prestanda känsliga program rekommenderar vi att du använder data diskar i stället för operativ system disken. Se avsnittet på data diskar nedan.

### <a name="temporary-disk"></a>Tillfällig disk

Den tillfälliga lagrings enheten, märkt som **D**: Drive, är inte beständig för Azure Blob Storage. Lagra inte dina användar databas filer eller loggfiler för användar transaktioner på enheten **D**:.

För virtuella datorer i D-serien, Dv2-serien och G-serien är den tillfälliga enheten på de virtuella datorerna SSD-baserad. Om din arbets belastning använder en mycket stor mängd TempDB (t. ex. temporära objekt eller komplexa kopplingar) kan lagring av TempDB på **D** -enheten resultera i högre tempdb-genomflöde och lägre tempdb-latens. Ett exempel scenario finns i TempDB-diskussionen i följande blogg inlägg: [Rikt linjer för lagrings konfiguration för SQL Server på virtuell Azure-dator](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm).

För virtuella datorer som stöder Premium-SSD (DS-serien, DSv2-serien och GS-serien) rekommenderar vi att du lagrar TempDB på en disk som har stöd för Premium-SSD med cachelagring för läsning aktive rad.

Det finns ett undantag till den här rekommendationen: _om tempdb-användningen är Skriv intensiv kan du uppnå högre prestanda genom att lagra tempdb på den lokala **D** -enheten, som också är SSD-baserad på dessa maskin storlekar._ Mer information finns i avsnittet [using SSD med tempdb](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) -bloggen. 

### <a name="data-disks"></a>Datadiskar

* **Använd data diskar för data-och loggfiler**: Om du inte använder disk ränder använder du två Premium SSD P30-diskar där en disk innehåller de loggfiler och den andra innehåller data och TempDB (med undantag för verksamhets kritiska och skrivbara arbets belastningar som nämnts ovan). Varje Premium SSD innehåller ett antal IOPs och bandbredd (MB/s) beroende på dess storlek, som visas i artikeln, [Välj en disktyp](../disks-types.md). Om du använder en disk Rands teknik, till exempel lagrings utrymmen, uppnår du optimala prestanda genom att ha två pooler, en för loggfilerna och den andra för datafilerna. Men om du planerar att använda SQL Server-instanser för redundanskluster (FCI) måste du konfigurera en pool.

   > [!TIP]
   > - För test resultat på olika konfigurationer av diskar och arbets belastningar kan du läsa följande blogg inlägg: [Rikt linjer för lagrings konfiguration för SQL Server på virtuell Azure-dator](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/).
   > - För uppdrags kritiska prestanda för SQL-servrar som behöver ~ 50 000 IOPS bör du överväga att ersätta 10-P30 diskar med en Ultra SSD. Mer information finns i följande blogg inlägg: [Verksamhets kritiska prestanda med Ultra SSD](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/).

   > [!NOTE]
   > När du etablerar en SQL Server VM i portalen har du möjlighet att redigera lagrings konfigurationen. Beroende på din konfiguration konfigurerar Azure en eller flera diskar. Flera diskar kombineras till en enda lagringspool med striping. Både data-och loggfilerna finns tillsammans i den här konfigurationen. Mer information finns i [lagrings konfiguration för SQL Server virtuella datorer](virtual-machines-windows-sql-server-storage-configuration.md).

* **Disk skiktning**: Du kan lägga till ytterligare data diskar och använda disk ränder för mer data flöde. Om du vill fastställa antalet data diskar måste du analysera antalet IOPS och bandbredd som krävs för dina loggfiler, och för dina data och TempDB-fil (er). Observera att olika storlekar på virtuella datorer har olika begränsningar för antalet IOPs och bandbredd som stöds, se tabellerna på IOPS per [VM-storlek](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Använd följande rikt linjer:

  * För Windows 8/Windows Server 2012 eller senare använder du [lagrings utrymmen](https://technet.microsoft.com/library/hh831739.aspx) med följande rikt linjer:

      1. Ange överlagring (stripe-storlek) till 64 KB (65536 byte) för OLTP-arbetsbelastningar och 256 KB (262144 byte) för arbets belastningar för data lager för att undvika prestanda påverkan på grund av feljustering av partitionen. Detta måste anges med PowerShell.
      2. Ange kolumn antal = antal fysiska diskar. Använd PowerShell när du konfigurerar fler än 8 diskar (inte Serverhanteraren UI). 

    Följande PowerShell skapar till exempel en ny lagringspool med Överlagrings storleken till 64 KB och antalet kolumner till 2:

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * För Windows 2008 R2 eller tidigare kan du använda dynamiska diskar (OS Striped-volymer) och stripe-storleken är alltid 64 KB. Observera att det här alternativet är föråldrat från och med Windows 8/Windows Server 2012. Mer information finns i support policyn på [Virtual Disk Service över gång till Windows Storage Management API](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

  * Om du använder [Lagringsdirigering (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) med [SQL Server kluster instanser för växling vid fel](virtual-machines-windows-portal-sql-create-failover-cluster.md)måste du konfigurera en enda pool. Observera att även om olika volymer kan skapas på samma pool, delar alla samma egenskaper, till exempel samma princip för cachelagring.

  * Fastställ antalet diskar som är kopplade till lagringspoolen baserat på dina belastnings förväntningar. Tänk på att olika storlekar på virtuella datorer tillåter olika antal anslutna data diskar. Mer information finns i [storlekar för Virtual Machines](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Om du inte använder Premium-SSD (utvecklings-och test scenarier) är rekommendationen att lägga till det maximala antalet data diskar som stöds av [storleken](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) på den virtuella datorn och använda disk ränder.

* **Princip**för cachelagring: Observera följande rekommendationer för cachelagring av principer beroende på lagrings konfigurationen.

  * Om du använder separata diskar för data-och loggfiler aktiverar du cachelagring på data diskarna som är värdar för dina datafiler och TempDB-datafiler. Detta kan resultera i en betydande prestanda förmån. Aktivera inte cachelagring på disken som innehåller logg filen eftersom detta orsakar en mindre minskning av prestanda.

  * Om du använder disk ränder i en enda lagringspool kommer de flesta arbets belastningarna att ha nytta av cachelagring av läsningar. Om du har separata lagringspooler för logg-och datafilerna aktiverar du enbart cachelagring på lagringspoolen för datafilerna. I vissa tunga Skriv arbets belastningar kan bättre prestanda uppnås utan cachelagring. Detta kan bara bestämmas genom testning.

  * De tidigare rekommendationerna gäller för Premium-SSD. Om du inte använder Premium-SSD ska du inte aktivera cachelagring på några data diskar.

  * Instruktioner för hur du konfigurerar diskcachelagring finns i följande artiklar. För den klassiska distributions modellen (ASM) ser du: [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) och [set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx). För Azure Resource Manager distributions modell, se: [Set-AzOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) och [set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdatadisk).

     > [!WARNING]
     > Stoppa SQL Server tjänsten när du ändrar cache-inställningen för virtuella Azure-diskar för att undvika risken för att databasen skadas.

* **Storlek på NTFS-allokeringsenhet**: När du formaterar data disken rekommenderar vi att du använder en storlek på 64 KB-allokeringsenhet för data-och loggfiler samt TempDB.

* **Metod tips för disk hantering**: När du tar bort en data disk eller ändrar dess cachestorlek stoppar du SQL Server tjänsten under ändringen. När cacheinställningar ändras på OS-disken stoppar Azure den virtuella datorn, ändrar cacheplatsen och startar om den virtuella datorn. När cache-inställningarna för en datadisk ändras, stoppas inte den virtuella datorn, men datadisken kopplas bort från den virtuella datorn under ändringen och sedan återkopplas.

  > [!WARNING]
  > Om du inte stoppar SQL Server tjänsten under dessa åtgärder kan databasen skadas.


## <a name="io-guidance"></a>I/O-vägledning

* De bästa resultaten med Premium-SSD uppnås när du parallellisera ditt program och begär Anden. Premium-SSD är utformade för scenarier där IO-ködjup är större än 1, så att du kan se små eller inga prestanda vinster för en enkel tråds seriella begär Anden (även om de är minnes krävande). Detta kan till exempel påverka de enkla test resultaten för prestanda analys verktyg, till exempel SQLIO.

* Överväg att använda [databasens sid komprimering](https://msdn.microsoft.com/library/cc280449.aspx) för att förbättra prestanda för i/O-intensiva arbets belastningar. Data komprimeringen kan dock öka processor förbrukningen på databas servern.

* Överväg att aktivera omedelbar fil initiering för att minska den tid som krävs för den första filallokeringen. Om du vill dra nytta av omedelbar fil initiering beviljar du tjänst kontot SQL Server (MSSQLSERVER) med SE_MANAGE_VOLUME_NAME och lägger till det i säkerhets principen **utföra volym underhålls aktiviteter** . Om du använder en SQL Server plattforms avbildning för Azure läggs inte standard tjänst kontot (NT Service\MSSQLSERVER) till i säkerhets principen **utför volym underhålls aktiviteter** . Med andra ord är omedelbar fil initiering inte aktive rad i en SQL Server Azures plattforms avbildning. När du har lagt till SQL Server tjänst kontot i säkerhets principen **utföra volym underhålls aktiviteter** startar du om SQL Server tjänsten. Det kan finnas säkerhets överväganden för att använda den här funktionen. Mer information finns i [databas filens initiering](https://msdn.microsoft.com/library/ms175935.aspx).

* **autoväxer** anses vara bara en katastrof för oväntad tillväxt. Hantera inte dina data och logga in på en dags-till-dags-basis med automatisk tillväxt. Om automatisk utökning används kan du i förväg förstora filen med storleks växeln.

* Se till att autokrympning är inaktiverat för att undvika onödiga kostnader som kan påverka prestanda negativt.

* Flytta alla databaser till data diskar, inklusive system databaser. Mer information finns i [Flytta system databaser](https://msdn.microsoft.com/library/ms345408.aspx).

* Flytta SQL Server fel logg och spåra fil kataloger till data diskar. Det kan du göra i Konfigurationshanteraren för SQL Server genom att högerklicka på SQL Server instansen och välja egenskaper. Inställningarna för fel logg och spårnings fil kan ändras på fliken **Start parametrar** . Katalogen dump anges på fliken **Avancerat** . Följande skärm bild visar var du ska leta efter start parametern för fel loggen.

    ![Skärm bild för SQL-logg](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* Konfigurera standard platser för säkerhets kopiering och databas fil. Använd rekommendationerna i den här artikeln och gör ändringarna i fönstret Server egenskaper. Instruktioner finns i [Visa eller ändra standard platserna för data-och loggfiler (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Följande skärm bild visar var du kan göra dessa ändringar.

    ![SQL-datalogg-och säkerhets kopierings filer](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* Aktivera låsta sidor för att minska IO-och växlings aktiviteter. Mer information finns i [Aktivera alternativet Lås sidor i minnet (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

* Om du kör SQL Server 2012 installerar du Service Pack 1 kumulativ uppdatering 10. Uppdateringen innehåller en korrigering av dåliga prestanda för I/O när du kör Välj i tillfälligt tabell uttryck i SQL Server 2012. Mer information finns i den här [artikeln i kunskaps basen](https://support.microsoft.com/kb/2958012).

* Överväg att komprimera datafiler när du överför in/ut ur Azure.

## <a name="feature-specific-guidance"></a>Funktions bestämd vägledning

Vissa distributioner kan få ytterligare prestanda för delar med mer avancerade konfigurations tekniker. I följande lista beskrivs några SQL Server funktioner som kan hjälpa dig att uppnå bättre prestanda:

### <a name="backup-to-azure-storage"></a>Säkerhetskopiera till Azure Storage
När du säkerhetskopierar SQL Server som körs på virtuella Azure-datorer kan du använda [SQL Server säkerhets kopiering till URL](https://msdn.microsoft.com/library/dn435916.aspx). Den här funktionen är tillgänglig från och med SQL Server 2012 SP1-CU2 och rekommenderas för säkerhets kopiering till anslutna data diskar. När du säkerhetskopierar/återställer till/från Azure Storage följer du rekommendationerna som ges i [SQL Server säkerhets kopiering till URL-rekommenderade metoder och fel sökning och återställning från säkerhets kopior som lagras i Azure Storage](https://msdn.microsoft.com/library/jj919149.aspx). Du kan också automatisera säkerhets kopieringarna med [Automatisk säkerhets kopiering för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md).

Innan du SQL Server 2012 kan du använda [SQL Server säkerhets kopiering till Azure-verktyget](https://www.microsoft.com/download/details.aspx?id=40740). Det här verktyget kan hjälpa till att öka säkerhets kopieringen genom att använda flera säkerhets kopierings stripe-mål.

### <a name="sql-server-data-files-in-azure"></a>SQL Server datafiler i Azure

Den här nya funktionen [SQL Server datafiler i Azure](https://msdn.microsoft.com/library/dn385720.aspx)är tillgänglig från och med SQL Server 2014. Att köra SQL Server med datafiler i Azure visar jämförbara prestanda egenskaper som att använda Azure Data disks.

### <a name="failover-cluster-instance-and-storage-spaces"></a>Instans av kluster för växling vid fel och lagrings utrymmen

Om du använder lagrings utrymmen, när du lägger till noder i klustret på **bekräftelse** sidan, avmarkerar du kryss rutan **Lägg till alla tillgängliga lagrings enheter i klustret**. 

![Avmarkera berättigat lagrings utrymme](media/virtual-machines-windows-sql-performance/uncheck-eligible-cluster-storage.png)

Om du använder lagrings utrymmen och inte avmarkerar **Lägg till alla tillgängliga lagrings enheter i klustret**, kopplar Windows bort de virtuella diskarna under kluster processen. Därför visas de inte i disk hanteraren eller i Utforskaren förrän lagrings utrymmena har tagits bort från klustret och återkopplas med hjälp av PowerShell. Lagrings utrymmen grupper flera diskar i lagringspooler. Mer information finns i [lagrings utrymmen](/windows-server/storage/storage-spaces/overview).

## <a name="next-steps"></a>Nästa steg

Mer information om lagring och prestanda finns i [rikt linjer för lagrings konfiguration för SQL Server på virtuell Azure-dator](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)

Rekommenderade säkerhets metoder finns i [säkerhets överväganden för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-security.md).

Granska andra SQL Server virtuella dator artiklar på [SQL Server på Azure Virtual Machines-översikt](virtual-machines-windows-sql-server-iaas-overview.md). Om du har frågor om virtuella SQL Server-datorer kan du läsa [Vanliga frågor](virtual-machines-windows-sql-server-iaas-faq.md).
