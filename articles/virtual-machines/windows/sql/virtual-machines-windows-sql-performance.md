---
title: Prestandarelaterade Metodtips för SQL Server i Azure | Microsoft Docs
description: Innehåller metodtips för att optimera prestanda för SQL Server i virtuella Microsoft Azure-datorer.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/20/2018
ms.author: jroth
ms.openlocfilehash: 2aa066caf6239f29038228c3c91607d913e70682
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="performance-best-practices-for-sql-server-in-azure-virtual-machines"></a>Prestandametodtips för SQL Server på virtuella Azure-datorer

## <a name="overview"></a>Översikt

Den här artikeln innehåller metodtips för att optimera prestanda för SQL Server i Microsoft Azure-dator. När du kör SQL Server i Azure Virtual Machines, rekommenderar vi att du fortsätter med den samma databas alternativen för prestandajustering som gäller för SQL Server i lokal server-miljö. Prestanda i en relationsdatabas i ett offentligt moln beror dock på många faktorer, till exempel storleken på en virtuell dator och konfigurationen av datadiskar.

När du skapar SQL Server-avbildningar, [överväga etablering dina virtuella datorer i Azure portal](virtual-machines-windows-portal-sql-server-provision.md). SQL Server-datorer som etablerats på portalen med Resource Manager följer bästa praxis.

Den här artikeln fokuserar på att få den *bästa* prestanda för SQL Server på virtuella Azure-datorer. Om din arbetsbelastning är mindre systemresurser, kanske inte kräver varje optimering nedan. Överväg att dina prestandabehov och mönster för arbetsbelastningen som du utvärdera de här rekommendationerna.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="quick-check-list"></a>Snabbkontrollen lista

Följande är en snabb kontroll lista för optimala prestanda av SQL Server på Azure Virtual Machines:

| Område | Optimeringar |
| --- | --- |
| [VM-storlek](#vm-size-guidance) |[DS3](../sizes-memory.md) eller högre för SQL Enterprise edition.<br/><br/>[DS2](../sizes-memory.md) eller högre för SQL Standard och webb-utgåvor. |
| [Storage](#storage-guidance) |Använd [Premiumlagring](../premium-storage.md). Standardlagring rekommenderas endast för utveckling och testning.<br/><br/>Behåll den [lagringskonto](../../../storage/common/storage-create-storage-account.md) och SQL Server-VM i samma region.<br/><br/>Inaktivera Azure [geo-redundant lagring](../../../storage/common/storage-redundancy.md) (geo-replikering) på lagringskontot. |
| [Diskar](#disks-guidance) |Använd minst 2 [P30 diskar](../premium-storage.md#scalability-and-performance-targets) (1 för loggfiler, 1 för datafiler och TempDB).<br/><br/>Undvik att använda operativsystem eller tillfälliga diskar för databaslagring eller loggning.<br/><br/>Aktivera cachelagring för läsning på diskarna som värd för filer och datafiler i TempDB.<br/><br/>Aktivera inte cachelagring på diskarna som är värd för loggfilen.<br/><br/>Viktigt: Stoppa SQL Server-tjänsten när du ändrar inställningar för cachelagring för en virtuell dator i Azure-disken.<br/><br/>Stripe-flera Azure datadiskar för att få ökad i/o-genomströmning.<br/><br/>Formatera med dokumenterade allokering storlekar. |
| [I/O](#io-guidance) |Aktivera komprimering för databas-sidan.<br/><br/>Aktivera omedelbara filen initiering av datafiler.<br/><br/>Begränsa autogrow på databasen.<br/><br/>Inaktivera autoshrink på databasen.<br/><br/>Flytta alla databaser till datadiskar, inklusive systemdatabaser.<br/><br/>Flytta SQL Server fel logg- och spårningsfiler kataloger till datadiskar.<br/><br/>Konfigurera säkerhetskopiering och databasen standardsökvägar.<br/><br/>Aktivera låsta sidor.<br/><br/>Tillämpa korrigeringar för SQL Server-prestanda. |
| [Funktionsspecifika](#feature-specific-guidance) |Säkerhetskopiera direkt till blob storage. |

Mer information om *hur* och *varför* för att göra dessa anpassningar kan granska information och riktlinjer som anges i följande avsnitt.

## <a name="vm-size-guidance"></a>Vägledning för VM-storlek

För känsliga program för prestanda, bör du använda följande [storlekar för virtuella datorer](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json):

* **SQL Server Enterprise Edition**: DS3 eller högre
* **SQL Server Standard och webb-utgåvor**: DS2 eller högre

## <a name="storage-guidance"></a>Vägledning för lagring

Stöd för virtuella datorer av DS-serien (tillsammans med DSv2-serien och GS-serien) [Premiumlagring](../premium-storage.md). Premium Storage rekommenderas för alla produktionsarbetsbelastningar.

> [!WARNING]
> Standardlagring har olika svarstider och bandbredd och rekommenderas endast för arbetsbelastningar för utveckling och testning. Produktionsarbetsbelastningar bör använda Premium-lagring.

Vi rekommenderar dessutom att du skapar ditt Azure storage-konto i samma datacenter som din SQL Server-datorer att minska överföringen fördröjningar. När du skapar ett lagringskonto, inaktivera geo-replikering som konsekvent skrivåtgärder ordning över flera diskar inte är säkert. I stället konfigurerar en SQL Server disaster recovery-tekniken mellan två Azure-datacenter. Mer information finns i [hög tillgänglighet och Haveriberedskap för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Vägledning för diskar

Det finns tre huvudsakliga disktyper på en virtuell dator i Azure:

* **OS-disken**: när du skapar en virtuell dator i Azure, plattformen ska kopplas till minst en disk (märkta som den **C** enhet) till den virtuella datorn för ditt operativsystemdisken. Den här disken är en VHD som lagras som en sidblobb i lagringen.
* **Diskutrymme**: Azure Virtual Machines innehåller en annan disk kallas tillfällig disken (märkta som den **D**: enheten). Det här är en disk på den nod som kan användas för arbetsyta.
* **Datadiskar**: du kan också koppla ytterligare diskar till den virtuella datorn som datadiskar och de lagras i lagring som sidblobar.

I följande avsnitt beskrivs rekommendationer för att använda dessa olika diskar.

### <a name="operating-system-disk"></a>Operativsystemdisk

En operativsystemdisk är en virtuell Hårddisk som du kan starta och montera som kör version av ett operativsystem och etiketteras som **C** enhet.

Standard cachelagring principen på operativsystemdisken är **Skrivskyddstyp**. För känsliga program för prestanda rekommenderar vi att du använder datadiskar i stället för operativsystemets disk. Se avsnittet på Datadiskar nedan.

### <a name="temporary-disk"></a>Diskutrymme

Enhetens tillfällig lagring märkta som den **D**: enheten, sparas inte till Azure blob storage. Lagra inte din användardatabasfiler eller användaren transaktionsloggfiler på den **D**: enheten.

D-serien, Dv2-serien och G-serien virtuella datorer är den tillfälliga enheten på dessa virtuella datorer SSD-baserad. Om din arbetsbelastning gör tunga användning av TempDB (t.ex. för temporära objekt eller komplexa kopplingar) lagrar TempDB på den **D** enhet kan leda till högre TempDB genomflöde och lägre TempDB latens.

För virtuella datorer som har stöd för Premium-lagring (DS-serien, DSv2-serien och GS-serien) kan rekommenderar vi att du lagrar TempDB på en disk som har stöd för Premium-lagring med skrivskyddade cachelagring aktiverad. Det finns ett undantag till den här rekommendationen; Om TempDB-användning är write-intensiva kan du uppnå högre prestanda genom att lagra TempDB på lokalt **D** enhet som också SSD-baserad på dessa datorstorlekar.

### <a name="data-disks"></a>Datadiskar

* **Använd datadiskar för data och loggfiler**: Om du inte använder disk striping, använda 2 Premium-lagring [P30 diskar](../premium-storage.md#scalability-and-performance-targets) där en disk som innehåller loggfilerna och den andra innehåller data och filer på TempDB. Varje disk i Premium-lagring finns ett antal IOPs och bandbredd (MB/s) beroende på dess storlek, enligt beskrivningen i följande artikel: [med Premium-lagring för diskar](../premium-storage.md). Om du använder en disk striping teknik, till exempel lagringsutrymmen, måste du placera alla filer på samma enhet.

   > [!NOTE]
   > När du etablerar en SQL Server-VM i portalen har du möjlighet att redigera din lagringskonfiguration. Beroende på din konfiguration konfigurerar Azure du en eller flera diskar. Flera diskar kombineras till en enskild lagringspool med striping. Både data och loggfilen filer finnas tillsammans i den här konfigurationen. Mer information finns i [lagringskonfigurationen för SQL Server-datorer](virtual-machines-windows-sql-server-storage-configuration.md).

* **Disk-Striping**: för snabbare dataflöde kan du lägga till ytterligare datadiskar och använda Disk Striping. För att fastställa antalet datadiskar som du behöver analysera antalet IOPS och bandbredd som krävs för din loggfilerna och dina data och filer på TempDB. Observera att olika storlekar på VM har olika begränsningar för antalet IOPs och bandbredd som stöds, se tabellerna på IOPS per [VM-storlek](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Använd följande riktlinjer:

  * Windows 8/Windows Server 2012 eller senare, Använd [lagringsutrymmen](https://technet.microsoft.com/library/hh831739.aspx) med följande riktlinjer:

      1. Ange interleave (stripe-storlek) till 64 KB (65 536 byte) för OLTP-arbetsbelastningar och 256 KB (262 144 byte) för data datalagring arbetsbelastningar för att undvika inverkan på prestanda på grund av partitionen feljusteringarna. Detta måste anges med PowerShell.
      1. Ange kolumnantal = antalet fysiska diskar. Använd PowerShell när du konfigurerar mer än 8 diskar (inte Server Manager UI). 

    Följande PowerShell skapas en ny lagringspool med överlagring storleken till 64 KB och antalet kolumner till 2:

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * För Windows 2008 R2 eller tidigare, kan du använda dynamiska diskar (OS stripe-volymer) och stripe-storlek är alltid 64 KB. Observera att det här alternativet används inte i Windows 8 och Windows Server 2012. Mer information finns i instruktionen support vid [Virtual Disk Service övergår till Windows Storage Management API](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

  * Om du använder [Storage Spaces Direct (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) med ett scenario som [SQL Server-instanser för redundanskluster](virtual-machines-windows-portal-sql-create-failover-cluster.md), måste du konfigurera en enskild pool. Observera att även om olika volymer kan skapas på den enda poolen, de kommer alla delar samma egenskaper, till exempel samma princip för cachelagring. 

  * Bestämma antalet diskar som är associerade med din lagringspool utifrån belastningen förväntningar. Tänk på att olika storlekar på VM tillåter olika antal anslutna datadiskar. Mer information finns i [storlekar för virtuella datorer](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Om du inte använder Premium-lagring (scenarier för utveckling och testning), rekommenderar vi att lägga till det maximala antalet datadiskar som stöds av din [VM-storlek](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) och använda Disk Striping.

* **Princip för cachelagring av**: hårddiskar för Premium-lagring, aktivera cachelagring för läsning på datadiskar som är värd för datafiler och TempDB-datafiler. Om du inte använder Premium-lagring, aktivera inte någon cachelagring på eventuella hårddiskar. Anvisningar för hur du konfigurerar cachelagring på disk, finns i följande artiklar. Den klassiska distributionsmodellen (ASM) finns: [Set AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) och [Set AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx). Azure Resource Manager-distributionsmodellen finns: [Set AzureRMOSDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk?view=azurermps-4.4.1) och [Set AzureRMVMDataDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmdatadisk?view=azurermps-4.4.1).

  > [!WARNING]
  > Stoppa tjänsten SQL Server när du ändrar cache-inställningen för Virtuella Azure-diskar för att undvika risken för databasen är skadad.

* **Storlek på allokeringsenhet NTFS**: formaterar datadisken och det rekommenderas att du använder en storlek på allokeringsenhet 64 KB för data och loggfiler samt TempDB.

* **Disk management metodtips**: när bort en datadisk eller ändra dess cachetyp stoppa SQL Server-tjänsten under ändringen. När cachelagringsinställningarna ändras för OS-disk, Azure stoppar den virtuella datorn, ändrar cache och startar om den virtuella datorn. Inställningar för cachelagring av en datadisk som ändras, den virtuella datorn har inte stoppats, men datadisken kopplas bort från den virtuella datorn under ändringen och sedan anbringas på nytt.

  > [!WARNING]
  > Det gick inte att stoppa tjänsten SQL Server under dessa åtgärder kan orsaka databasfel.

## <a name="io-guidance"></a>I/o-vägledning

* Premium-lagring bäst resultat uppnås när du parallelize programmet och förfrågningar. Premium-lagring är avsedd för scenarier där ködjupet för i/o är större än 1, så ser lite eller ingen prestandavinster för Enkeltrådig seriella begäranden (även om de är beräkningsintensiva lagring). Exempelvis kan detta påverka Enkeltrådig testresultaten för prestanda analysverktyg som SQLIO.

* Överväg att använda [databasen sidan komprimering](https://msdn.microsoft.com/library/cc280449.aspx) som kan förbättra prestanda för i/o-intensiv arbetsbelastning. Komprimering av data kan dock öka processorförbrukningen på databasservern.

* Överväg att aktivera instant file initieringen att minska den tid som krävs för inledande filen allokering. Om du vill dra nytta av instant file initieringen du bevilja tjänstkontot för SQL Server (MSSQLSERVER) med SE_MANAGE_VOLUME_NAME och lägger till den i den **utföra underhållsaktiviteter** säkerhetsprincip. Om du använder en SQL Server-plattformsavbildning för Azure service-kontot av standardtyp (NT Service\MSSQLSERVER) är inte läggs till i **utföra underhållsaktiviteter** säkerhetsprincip. Med andra ord är instant file initieringen inte aktiverat i en SQL Server Azure plattformsavbildning. När du lägger till SQL Server-tjänstkontot till den **utföra underhållsaktiviteter** säkerhetsprinciper, starta om SQL Server-tjänsten. Det kan finnas säkerhetsöverväganden vid användning av den här funktionen. Mer information finns i [databasen File Initialization](https://msdn.microsoft.com/library/ms175935.aspx).

* **automatisk storleksökning** betraktas som bara beredskap för oväntat tillväxt. Hantera inte dina data och loggfilen tillväxt för varje dagliga autogrow. Om automatisk storleksökning används före växa filen med växeln storlek.

* Kontrollera att **autoshrink** inaktiveras för att undvika onödig som kan påverka prestanda negativt.

* Flytta alla databaser till datadiskar, inklusive systemdatabaser. Mer information finns i [flytta systemdatabaser](https://msdn.microsoft.com/library/ms345408.aspx).

* Flytta SQL Server fel logg- och spårningsfiler kataloger till datadiskar. Detta kan göras i Konfigurationshanteraren för SQL Server genom att högerklicka på SQL Server-instansen och välja egenskaper. Inställningar för fel logg- och spårningsfiler filen kan ändras i den **Startparametrar** fliken. Katalogen Dump har angetts i den **Avancerat** fliken. Följande skärmbild visar var du vill leta efter fel loggen Start-parameter.

    ![Skärmbild för SQL-felloggen](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* Konfigurera säkerhetskopiering och databasen standardsökvägar. Använd rekommendationerna i den här artikeln och göra ändringar i egenskapsfönstret för servern. Instruktioner finns i [visa eller ändra de platser som standard för Data och loggfiler (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Följande skärmbild visar var du vill göra dessa ändringar.

    ![SQL Data logg-och säkerhetskopiering](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* Aktivera låsta sidor att minska i/o och sidindelning aktiviteter. Mer information finns i [aktiverar Lås sidor i minnet (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

* Installera Service Pack 1 Cumulative Update 10 om du kör SQL Server 2012. Den här uppdateringen innehåller korrigering för låga prestanda på i/o när du kör väljer till tillfällig tabell-satsen i SQL Server 2012. Mer information finns i det här [knowledge base-artikel](http://support.microsoft.com/kb/2958012).

* Överväg att komprimera några filer när du överför in/ut Azure.

## <a name="feature-specific-guidance"></a>Funktionsspecifika vägledning

Vissa distributioner kan uppnå ytterligare prestandafördelarna med mer avancerad konfiguration metoder. I följande lista visar några SQL Server-funktioner som kan hjälpa dig att få bättre prestanda:

* **Säkerhetskopiering till Azure storage**: när du utför säkerhetskopieringar för SQL Server körs i virtuella Azure-datorer, kan du använda [SQL Server-säkerhetskopiering till URL: en](https://msdn.microsoft.com/library/dn435916.aspx). Den här funktionen är tillgänglig från och med SQL Server 2012 SP1 CU2 och rekommenderas för säkerhetskopiera bifogade data-diskar. När du säkerhetskopiering/återställning till/från Azure storage, Följ rekommendationerna som anges i [SQL Server-säkerhetskopiering till URL: en bästa praxis och felsöka och återställa från säkerhetskopior lagras i Azure Storage](https://msdn.microsoft.com/library/jj919149.aspx). Du kan också automatisera dessa säkerhetskopior med hjälp av [automatisk säkerhetskopiering för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md).

    Du kan använda innan SQL Server 2012, [SQL Server-säkerhetskopiering till Azure-verktyget](https://www.microsoft.com/download/details.aspx?id=40740). Det här verktyget kan hjälpa till att öka genomflödet för säkerhetskopiering med flera säkerhetskopiering stripe-mål.

* **SQL Server-datafiler i Azure**: den här nya funktionen [SQL Server-datafiler i Azure](https://msdn.microsoft.com/library/dn385720.aspx), är tillgängliga från och med SQL Server 2014. Kör SQL Server med datafiler i Azure visar jämförbara prestandaegenskaper som med Azure datadiskar.

## <a name="next-steps"></a>Nästa steg

Metodtips för säkerhet, se [säkerhetsaspekter för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-security.md).

Granska andra artiklar för virtuell dator med SQL Server på [SQL Server på Azure virtuella datorer – översikt](virtual-machines-windows-sql-server-iaas-overview.md). Om du har frågor om virtuella SQL Server-datorer kan du läsa [Vanliga frågor](virtual-machines-windows-sql-server-iaas-faq.md).
