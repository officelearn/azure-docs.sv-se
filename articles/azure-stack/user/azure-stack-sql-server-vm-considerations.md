---
title: Bästa praxis för SQL Server på virtuella datorer i Azure Stack prestanda
description: Innehåller metodtips för att optimera prestanda för SQL Server i Microsoft Azure Stack virtuella datorer.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.author: mabrigg
ms.reviewer: anajod
ms.openlocfilehash: 00c67503f5b9e0027cbb62520e392f56420a75e6
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34701403"
---
# <a name="optimize-sql-server-performance"></a>Optimera prestanda för SQL Server

Den här artikeln innehåller anvisningar för att optimera prestanda för SQL Server i Microsoft Azure-stacken virtuella datorer. När du kör SQL Server i Azure-stacken virtuella datorer kan använda samma databas prestandajustering alternativ gäller för SQL Server i en lokal server-miljö. Prestanda i en relationsdatabas i ett moln med Azure Stack beror på många faktorer. Faktorer är family storleken på en virtuell dator och konfigurationen av datadiskar.

När du skapar SQL Server-avbildningar, [överväga etablering av virtuella datorer i Azure-stacken portal](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). Hämta SQL IaaS-tillägget från Marketplace-hantering på administrationsportalen för Azure-stacken och ladda ned ditt val av SQL virtuella virtuella hårddiskar (VHD). Dessa inkluderar SQL2014SP2, SQL2016SP1 och SQL2017.

> [!NOTE]  
> När artikeln beskriver hur du etablerar en SQL Server-datorn med hjälp av den globala Azure-portalen, anvisningarna gäller även Azure Stack med följande skillnader: SSD är inte tillgänglig för operativsystemets disk, hanterade diskar är inte tillgängliga, och Det finns mindre skillnader i lagringskonfiguration.

Hämtar den *bästa* prestanda för SQL Server på Azure-stacken virtuella datorer har fokus i den här artikeln. Om din arbetsbelastning är mindre systemresurser, kanske inte kräver varje rekommenderade optimering. Överväg att dina prestandabehov och mönster för arbetsbelastningen som du utvärdera de här rekommendationerna.

> [!NOTE]  
> För prestanda anvisningar för SQL Server på virtuella Azure-datorer, se [i den här artikeln](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).

## <a name="before-you-begin"></a>Innan du börjar
Följande checklista är optimala prestanda för SQL Server på Azure-stacken virtuella datorer:


|Område|Optimeringar|
|-----|-----|
|Virtuell datorstorlek |[DS3](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) eller högre för SQL Server Enterprise edition.<br><br>[DS2](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) eller högre för SQL Server Standard edition och Web edition.|
|Storage |En familj av virtuell dator som har stöd för [premiumlagring](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-acs-differences).|
|Diskar |Välj diskens storlek baserat på dina kapacitetsbehov använder minst två diskar (ett för loggfiler) och en för datafilen och TempDB. Ange standard sökvägar till dessa diskar under SQL Server-installationen.<br><br>Undvik att använda operativsystem eller tillfälliga diskar för databaslagring eller loggning.<br>Stripe-flera Azure datadiskar för att få ökad i/o-genomströmning med hjälp av lagringsutrymmen.<br><br>Formatera med dokumenterade allokering storlekar.|
|I/O|Aktivera omedelbara filen initiering av datafiler.<br><br>Begränsa autogrow för databaser med rimligen små fast steg (64 MB - 256 MB).<br><br>Inaktivera autoshrink på databasen.<br><br>Konfigurera säkerhetskopiering och databasen standardsökvägar på datadiskar, inte operativsystemdisken.<br><br>Aktivera låsta sidor.<br><br>Använda SQL Server servicepack och kumulativa uppdateringar.|
|Funktionsspecifika|Säkerhetskopiera direkt till blob storage (om det stöds av SQL Server-version som används).|
|||

Mer information om *hur* och *varför* för att göra dessa anpassningar kan granska information och riktlinjer som tillhandahålls i följande avsnitt.

## <a name="virtual-machine-size-guidance"></a>Virtuell storlek vägledning

För prestanda program, följande [storlekar för virtuella datorer](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) rekommenderas:

- **SQL Server Enterprise edition:** DS3 eller högre

- **SQL Server Standard edition och Web edition:** DS2 eller högre

Med Azure-stacken finns det ingen prestandaskillnad mellan DS och DS_v2 virtuella family serien.

## <a name="storage-guidance"></a>Vägledning för lagring

DS-serien (tillsammans med DSv2-serien) virtuella datorer i Azure-stacken innehåller den maximala operativsystemet disk- och diskgenomflödet (IOPS). En virtuell dator från DS eller DSv2-serien ger upp till 1 000 IOPS för operativsystemets disk och upp till 2 300 IOPS per datadisk, oavsett typ eller storleken på den valda disken.

Disk datagenomströmning bestäms unikt baserat på virtuella family serien. Du kan [referera till den här artikeln](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) att identifiera disken datagenomströmning per virtuell dator family serie.

> [!NOTE]  
> Välj en virtuell dator för DS-serien eller DSv2-serien för högsta möjliga IOPS på operativsystemet diskar och data för produktionsarbetsbelastningar.

När du skapar ett lagringskonto i Azure-stacken, har alternativet geo-replikering ingen effekt eftersom den här funktionen inte är tillgänglig i Azure-stacken.

## <a name="disks-guidance"></a>Vägledning för diskar

Det finns tre huvudsakliga disktyper på en virtuell dator i Azure Stack:

- **Operativsystemdisken:** när du skapar en virtuell dator i Azure-stacken plattformen kopplas till minst en disk (märkta som den **C** enhet) till den virtuella datorn för ditt operativsystemdisken. Den här disken är en VHD som lagras som en sidblobb i lagringen.

- **Diskutrymme:** Azure Stack virtuella datorer innehåller en annan disk kallas tillfällig disken (märkta som den **D** enhet). Det här är en disk på den nod som kan användas för arbetsyta.

- **Datadiskar:** du kan koppla ytterligare diskar till den virtuella datorn som datadiskar och diskarna lagras i lagring som sidblobar.

I följande avsnitt beskrivs rekommendationer för att använda dessa olika diskar.

### <a name="operating-system-disk"></a>Operativsystemdisk

En operativsystemdisk är en virtuell Hårddisk som du kan starta och montera som kör version av ett operativsystem och etiketteras som **C** enhet.

### <a name="temporary-disk"></a>Diskutrymme

Enhetens tillfällig lagring märkta som den **D** enhet, sparas inte. Lagra inte några data är du villig att förlora, till exempel din användardatabasfiler eller användaren transaktionsloggfiler på den **D** enhet.

Vi rekommenderar att du lagrar TempDB på en datadisk som varje datadisk ger högst upp till 2 300 IOPS per datadisk.

### <a name="data-disks"></a>Datadiskar

- **Använd datadiskar för data och loggfiler.** Om du inte använder disk striping kan använda två hårddiskar från en virtuell dator som har stöd för Premium-lagring, där en disk som innehåller filerna och den andra innehåller data och filer på TempDB. Varje datadisk innehåller ett antal IOPS och bandbredd (MB/s) beroende på virtuell dator-familjen, enligt beskrivningen i [storlekar för virtuella datorer stöds i Azure-stacken](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes). Om du använder en disk striping-teknik, till exempel lagringsutrymmen, placera alla data och loggfilen filer på samma enhet (inklusive TempDB). Den här konfigurationen ger det maximala antalet IOPS som är tillgängliga för SQL Server för att använda, oavsett vilken filen kräver dem när som helst viss.

> [!NOTE]  
> När du etablerar en virtuell dator i SQL Server i portalen har du möjlighet att redigera din konfiguration för lagring. Beroende på din konfiguration konfigurerar Azure Stack en eller flera diskar. Flera diskar kombineras till en enskild lagringspool. Både data och loggfilen filer finnas tillsammans i den här konfigurationen.

- **Disk-striping:** för snabbare dataflöde kan du lägga till ytterligare datadiskar och använda disk striping. För att bestämma antalet datadiskar som du behöver analysera antalet IOPS och bandbredd som krävs för loggfilerna och dina data och filer på TempDB. Lägg märke till att IOPS-gränser per datadisk baserat på series-familjen för virtuell dator och inte baserat på storleken på virtuella datorn. Nätverket bandbreddsgränser, är beroende på storleken på virtuella datorn. Se tabellerna på [virtuella storlekar i Azure-stacken](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) för mer information. Använd följande riktlinjer:

    - För Windows Server 2012 eller senare, Använd [lagringsutrymmen](https://technet.microsoft.com/library/hh831739.aspx) med följande riktlinjer:

        1.  Ange interleave (stripe-storlek) till 64 KB (65 536 byte) för online transaktionsbearbetning (OLTP) arbetsbelastningar och 256 KB (262 144 byte) för data datalagring arbetsbelastningar för att undvika inverkan på prestanda på grund av partitionen feljusteringarna. Detta måste anges med PowerShell.

        2.  Ange kolumnantal = antalet fysiska diskar. Använd PowerShell när du konfigurerar mer än åtta diskar (inte Server Manager UI).

            Följande PowerShell skapas en ny lagringspool med överlagring storleken 64 KB och antalet kolumner till 2:

          ```PowerShell  
          $PoolCount = Get-PhysicalDisk -CanPool $True
          $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

          New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false
          ```

- Bestämma antalet diskar som är associerade med din lagringspool utifrån belastningen förväntningar. Tänk på att olika virtuella datorstorlekar tillåter olika antal anslutna datadiskar. Mer information finns i [storlekar för virtuella datorer stöds i Azure-stacken](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes).
- För att få högsta möjliga IOPS för datadiskar rekommendationen är att lägga till det maximala antalet datadiskar som stöds av din [storlek för virtuell dator](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) och använda disk striping.
- **Storlek på allokeringsenhet NTFS:** formaterar datadisken och det rekommenderas att du använder en storlek på allokeringsenhet 64 KB för data och loggfiler samt TempDB.
- **Disk management praxis:** när du tar bort en datadisk stoppa SQL Server-tjänsten under ändringen. Dessutom ändras inte inställningar för cachelagring på diskar när den inte ger några prestandaförbättringar.

> [!WARNING]  
> Det gick inte att stoppa SQL-tjänsten under dessa åtgärder kan orsaka databasfel.

## <a name="io-guidance"></a>I/o-vägledning

- Överväg att aktivera instant file initieringen att minska den tid som krävs för inledande filen allokering. Om du vill dra nytta av instant file initieringen du bevilja tjänstkontot för SQL Server (MSSQLSERVER) med **SE_MANAGE_VOLUME_NAME** och lägger till den i den **utföra underhållsaktiviteter** säkerhet princip. Om du använder en SQL Server-plattformsavbildning för Azure standard tjänstkonto (**NT Service\MSSQLSERVER**) är inte lagts till i den **utföra underhållsaktiviteter** säkerhetsprincip. Med andra ord är instant file initieringen inte aktiverat i en SQL Server Azure plattformsavbildning. När du lägger till SQL Server-tjänstkontot till den **utföra underhållsaktiviteter** säkerhetsprinciper, starta om SQL Server-tjänsten. Det kan finnas säkerhetsöverväganden vid användning av den här funktionen. Mer information finns i [databasen File Initialization](https://msdn.microsoft.com/library/ms175935.aspx).
- **Automatisk storleksökning** är beredskap för oväntat tillväxt. Hantera inte dina data och loggfilen tillväxt för varje dagliga autogrow. Om automatisk storleksökning används före växa filen med den **storlek** växla.
- Kontrollera att **autoshrink** inaktiveras för att undvika onödig som kan påverka prestanda negativt.
- Konfigurera säkerhetskopiering och databasen standardsökvägar. Använd rekommendationerna i den här artikeln och göra ändringar i egenskapsfönstret för servern. Instruktioner finns i [visa eller ändra de platser som standard för Data och loggfiler (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Följande skärmbild visar var du vill göra dessa ändringar:

    > ![Visa eller ändra standardplatserna](./media/sql-server-vm-considerations/image1.png)

- Aktivera låsta sidor att minska i/o och sidindelning aktiviteter. Mer information finns i [aktiverar Lås sidor i minnet (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

- Överväg att komprimera några filer när du överför in/ut i Azure-stacken, inklusive säkerhetskopior.

## <a name="feature-specific-guidance"></a>Funktionsspecifika vägledning

Vissa distributioner kan uppnå ytterligare prestandafördelarna med mer avancerad konfiguration metoder. I följande lista visar några SQL Server-funktioner som kan hjälpa dig att få bättre prestanda:

- **Säkerhetskopiera till Azure** **lagring.** När du utför säkerhetskopieringar för SQL Server körs i Azure-stacken virtuella datorer, kan du använda SQL Server-säkerhetskopiering till URL: en. Den här funktionen är tillgänglig från och med SQL Server 2012 SP1 CU2 och rekommenderas för säkerhetskopiera bifogade data-diskar.

    När en säkerhetskopiering eller återställning med hjälp av Azure storage Följ rekommendationerna i [SQL Server säkerhetskopiering till URL: en bästa praxis och felsöka](https://msdn.microsoft.com/library/jj919149.aspx) och [återställning från säkerhetskopior lagras i Microsoft Azure](https://docs.microsoft.com/en-us/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure?view=sql-server-2017). Du kan också automatisera dessa säkerhetskopior med hjälp av [automatisk säkerhetskopiering för SQL Server i Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup).

-   **Säkerhetskopiera till Azure-stacken lagring.** Du kan säkerhetskopiera till Azure-stacken lagring på ett liknande sätt som med säkerhetskopiera till Azure Storage. När du skapar en säkerhetskopia i SQL Server Management Studio (SSMS), måste du manuellt ange konfigurationsinformation. Du kan inte använda SSMS för att skapa storage-behållare eller signatur för delad åtkomst. SSMS ansluter bara till Azure-prenumerationer, inte Azure Stack prenumerationer. I stället måste du skapa lagringskonto, behållare och signatur för delad åtkomst i Azure Stack-portalen eller med PowerShell.

    När du monterar information i dialogrutan SQL Server-säkerhetskopiering:

    ![SQL Server-säkerhetskopiering](./media/sql-server-vm-considerations/image3.png)

    > [!NOTE]  
    > Signatur för delad åtkomst är SAS-token från stacken Azure-portalen utan inledande '?' i strängen. Om du använder kopieringsfunktionen från portalen måste du ta bort inledande '?' att fungera i SQL Server-token.

    När du har skapat mål för säkerhetskopian ställa in och konfigurerats i SQL Server, du kan sedan säkerhetskopiera till Azure-stacken blob storage.

## <a name="next-steps"></a>Nästa steg

[Använda tjänster eller utveckla appar för Azure-Stack](azure-stack-considerations.md)