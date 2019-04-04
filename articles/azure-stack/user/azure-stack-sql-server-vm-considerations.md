---
title: Följ rekommenderade säkerhetsmetoder för SQL Server och öka prestanda på virtuella datorer i Azure Stack | Microsoft Docs
description: Den här artikeln innehåller metodtips för SQL server för att öka prestanda och optimera SQL Server i virtuella datorer i Azure Stack.
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
ms.date: 04/02/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 03a354a7d670033fa86ebbb094710a836b6219c4
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58879072"
---
# <a name="sql-server-best-practices-to-optimize-performance-in-azure-stack"></a>Metodtips för SQL server att optimera prestanda i Azure Stack

Den här artikeln innehåller metodtips för SQL server för att optimera SQL Server och förbättra prestanda i Microsoft Azure Stack-datorer. När du kör SQL Server på Azure Stack-datorer kan använda samma databas prestandajustering alternativ gäller för SQL Server i en lokal server-miljö. Prestanda för en relationsdatabas i ett Azure Stack-moln beror på många faktorer. Faktorer är family storleken på en virtuell dator och konfigurationen för datadiskar.

När du skapar SQL Server-avbildningar, [Överväg att etablera virtuella datorer i Azure Stack portal](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). Ladda ned SQL IaaS-tillägget från Marketplace-hantering i Azure Stack Admin Portal och ladda ned ditt val av SQL VM virtuella hårddiskar (VHD). Dessa inkluderar SQL2014SP2 och SQL2016SP1 SQL2017.

> [!NOTE]  
> När artikeln beskriver hur du etablerar en SQL Server-dator med hjälp av den globala Azure-portalen, gäller riktlinjerna även Azure Stack med följande skillnader: SSD är inte tillgänglig för operativsystemdisken, hanterade diskar är inte tillgängliga och det finns mindre skillnader i lagringskonfiguration.

Hämta den *bästa* prestanda för SQL Server på Azure Stack virtuella datorer ligger fokus i den här artikeln. Om din arbetsbelastning är mindre systemresurser, kanske du inte kräver varje rekommenderade optimering. Överväg att dina prestandabehov och arbetsbelastningmönster medan du utvärderar de här rekommendationerna.

> [!NOTE]  
> Prestandavägledning för SQL Server på Azure virtuella datorer, finns i [i den här artikeln](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).

## <a name="checklist-for-sql-server-best-practices"></a>Checklista för Metodtips för SQL server

Följande checklista är optimala prestanda för SQL Server på Azure Stack-datorer:


|Område|Optimeringar|
|-----|-----|
|Storlek på virtuell dator |[DS3](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) eller högre för SQL Server Enterprise edition.<br><br>[DS2](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) eller högre för SQL Server Standard edition och Web edition.|
|Storage |Använda en VM-familj som stöder [premiumlagring](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-acs-differences).|
|Diskar |Använder minst två datadiskar (en för loggfiler) och en för datafilen och TempDB och välja diskens storlek baserat på dina kapacitetsbehov. Ange standard sökvägar till dessa diskar under SQL Server-installationen.<br><br>Undvik att använda operativsystemet eller temporära diskar för databaslagring eller loggning.<br>Stripe-flera Azure-datadiskar för att få ökad i/o-dataflöde med hjälp av lagringsutrymmen.<br><br>Formatera med dokumenterade allokering storlekar.|
|I/O|Aktivera omedelbara filen initiering av datafiler.<br><br>Begränsa automatisk storleksökning för databaser med förhållandevis små fast steg (64 MB - 256 MB).<br><br>Inaktivera automatiska storleksminskningen för databasen.<br><br>Konfigurera säkerhetskopiering och databasen standardsökvägar på datadiskar, inte operativsystemdisken.<br><br>Aktivera låsta sidor.<br><br>Gäller SQL Server servicepack och kumulativa uppdateringar.|
|Funktionsspecifika|Säkerhetskopiera direkt till blob storage (om det stöds av SQL Server-version som används).|
|||

Mer information om *hur* och *varför* för att göra dessa optimeringar, granska informationen och riktlinjerna i följande avsnitt.

## <a name="virtual-machine-size-guidance"></a>Vägledning för VM-storlek

För resultatdrivna program, följande [storlekar för virtuella datorer](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) rekommenderas:

- **SQL Server Enterprise edition:** DS3 eller högre

- **SQL Server Standard edition och Web edition:** DS2 eller högre

Det finns ingen prestandaskillnad mellan den DS och DS_v2 family serien för virtuella datorer med Azure Stack.

## <a name="storage-guidance"></a>Riktlinjer för Storage

DS-serien (tillsammans med DSv2-serien) virtuella datorer i Azure Stack ger det operativsystem disk- och diskgenomflödet (IOPS). En virtuell dator från DS eller DSv2-serien tillhandahåller upp till 1 000 IOPS för operativsystemdisken och upp till 2 300 IOPS per disken, oavsett typ eller storleken på den valda disken.

Diskgenomflöde bestäms unikt utifrån family serie för virtuella datorer. Du kan [i den här artikeln](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) att identifiera data diskdataflöde per virtuell dator familj serien.

> [!NOTE]  
> Välj en virtuell dator för DS-serien eller DSv2-serien som tillhandahåller den högsta möjliga IOPS för operativsystemet disk- och datadiskar för produktionsarbetsbelastningar.

När du skapar ett lagringskonto i Azure Stack, har geo-replikering-alternativet ingen effekt eftersom den här funktionen inte är tillgänglig i Azure Stack.

## <a name="disks-guidance"></a>Vägledning för diskar

Det finns tre huvudsakliga disktyper på en virtuell dator i Azure Stack:

- **Operativsystemets disk:** När du skapar en virtuell dator i Azure Stack plattformen bifogar minst en disk (märkta som den **C** enhet) till den virtuella datorn för operativsystemets disk. Den här disken är en virtuell Hårddisk som lagras som en sidblobb i lagring.

- **Temporär disk:** Azure Stack-datorer innehåller en annan disk kallas den temporära disken (märkta som den **D** enhet). Det här är en disk på den nod som kan användas för tillfälliga utrymmet.

- **Datadiskar:** Du kan koppla ytterligare diskar till din virtuella dator som datadiskar och diskarna sparas i lagring som sidblobar.

I följande avsnitt beskrivs rekommendationer för att använda dessa olika diskar.

### <a name="operating-system-disk"></a>Operativsystemdisk

En operativsystemdisk är en virtuell Hårddisk som du kan starta och montera som en som kör version av ett operativsystem och är märkta som **C** enhet.

### <a name="temporary-disk"></a>Temporär disk

Temporär lagring-enhet, märkta som den **D** enhet, behålls inte. Spara inte några data som du inte vill förlora, till exempel din användardatabasfiler eller användaren transaktionsloggfiler, på den **D** enhet.

Vi rekommenderar att du lagrar TempDB på en datadisk som innehåller högst upp till 2 300 IOPS per datadisk för varje datadisk.

### <a name="data-disks"></a>Datadiskar

- **Använda datadiskar för data och loggfiler.** Om du inte använder disk striping kan använda två datadiskar från en virtuell dator som har stöd för Premium-lagring, där en disk som innehåller filerna och den andra innehåller data och TempDB-filer. Varje datadisk har en rad IOPS och bandbredd (MBIT/s) beroende på VM-familj enligt beskrivningen i [VM-storlekar som stöds i Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes). Om du använder en disk striping-teknik, till exempel lagringsutrymmen kan du placera alla data och loggfiler lagras på samma enhet (inklusive TempDB). Den här konfigurationen ger dig det maximala antalet IOPS som är tillgängliga för SQL Server du använder, oavsett vilken fil behov dem vid given tidpunkt.

> [!NOTE]  
> När du etablerar en SQL Server-dator i portalen kan har du möjlighet att redigera din konfiguration för lagring. Beroende på din konfiguration konfigurerar en eller flera diskar i Azure Stack. Flera diskar kombineras till en enskild lagringspool. Både data och loggfiler filerna finnas tillsammans i den här konfigurationen.

- **Disk striping:** Du kan lägga till ytterligare datadiskar och använda disk striping mer dataflöde. Att fastställa antalet datadiskar som du behöver analysera antalet IOPS och bandbredd som krävs för loggfilerna och för dina data och TempDB-filer. Observera att IOPS-gränserna är per datadisk utifrån i serien för virtuella datorer och inte baserat på storleken på virtuella datorn. Nätverk bandbreddsgränser, men baseras på storleken på virtuella datorn. Se tabellerna på [VM-storlekar i Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) för mer information. Använd följande riktlinjer:

  - För Windows Server 2012 eller senare, Använd [lagringsutrymmen](https://technet.microsoft.com/library/hh831739.aspx) med följande riktlinjer:

    1. Inställt interfoliering (stripe-storlek) på 64 KB (65 536 byte) för online transaktionsbearbetning (OLTP) arbetsbelastningar och 256 KB (262 144 byte) för Datalagerhantering att undvika att prestanda påverkas på grund av partitionen misspassning. Detta måste anges med PowerShell.

    2. Ange kolumnantal = antal fysiska diskar. Använda PowerShell när du konfigurerar mer än åtta diskar (inte Server Manager UI).

       Exempelvis skapar en ny lagringspool med interfoliering storlek 64 KB och antalet kolumner till 2 med följande PowerShell:

       ```powershell  
       $PoolCount = Get-PhysicalDisk -CanPool $True
       $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

       New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false
       ```

- Bestämma antalet diskar som är associerade med din lagringspool baserat på dina förväntningar för belastningen. Tänk på att olika virtuella datorstorlekar tillåter olika antal anslutna datadiskar. Mer information finns i [VM-storlekar som stöds i Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes).
- För att få högsta möjliga IOPS för datadiskar rekommendationen är att lägga till det maximala antalet datadiskar som stöds av din [VM-storlek](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) och använda disk striping.
- **Storlek på NTFS-allokeringsenhet:** Formaterar datadisken och rekommenderas det att du använder en storlek på allokeringsenhet 64 KB för data och loggfiler samt TempDB.
- **Disk management praxis:** När du tar bort en datadisk, stoppa SQL Server-tjänsten under tiden. Dessutom inte ändra inställningar för cachelagring på diskarna eftersom det inte ger några prestandaförbättringar.

> [!WARNING]  
> Det gick inte att stoppa SQL-tjänsten vid de här åtgärderna kan orsaka databasfel.

## <a name="io-guidance"></a>I/o-vägledning

- Överväg att aktivera initieringen av omedelbar fil att minska den tid som krävs för inledande filen allokering. Om du vill dra nytta av omedelbar filen initieringen du bevilja tjänstkontot för SQL Server (MSSQLSERVER) med **SE_MANAGE_VOLUME_NAME** och lägger till den i den **utföra underhållsaktiviteter** säkerhet princip. Om du använder en plattformsavbildning för SQL Server för Azure standard-tjänstkontot (**NT Service\MSSQLSERVER**) inte har lagts till till den **utföra underhållsaktiviteter** säkerhetsprincip. Initieringen av omedelbar fil är med andra ord inte aktiverad i en SQL Server Azure-plattformsavbildning. När du lägger till SQL Server-tjänstkontot till den **utföra underhållsaktiviteter** säkerhetsprinciper, starta om SQL Server-tjänsten. Det kan finnas säkerhetsöverväganden vid användning av den här funktionen. Mer information finns i [databasen filen initieringen](https://msdn.microsoft.com/library/ms175935.aspx).
- **Automatisk storleksökning** är en reservplan vid oväntad storleksökning. Hantera inte dina data och loggfiler tillväxt på basis dagliga med automatisk storleksökning. Om du använder automatisk storleksökning, före växa filen med den **storlek** växla.
- Se till att **storleksminskning** är inaktiverat för att undvika onödig som kan påverka prestanda negativt.
- Konfigurera säkerhetskopiering och databasen standardsökvägar. Använd rekommendationer i den här artikeln och gör ändringar i egenskapsfönstret för servern. Anvisningar finns i [visa eller ändra de platser som standard för Data och loggfiler (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Följande skärmbild visar var du vill göra dessa ändringar:

    > ![Visa eller ändra standardplatserna](./media/sql-server-vm-considerations/image1.png)

- Aktivera låsta sidor att minska i/o och några sidindelning aktiviteter. Mer information finns i [aktivera den låsa sidor i minnet alternativet (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

- Överväg att komprimera några filer när du överför in/ut av Azure Stack, inklusive säkerhetskopior.

## <a name="feature-specific-guidance"></a>Funktionsspecifika vägledning

Vissa distributioner kan uppnå ytterligare prestandafördelarna med hjälp av mer avancerade tekniker i konfigurationen. I följande lista beskrivs vissa SQL Server-funktioner som kan hjälpa dig att få bättre prestanda:

- **Säkerhetskopiera till Azure** **lagring.** När du utför säkerhetskopiering för SQL Server som körs på Azure Stack-datorer, kan du använda SQL Server-säkerhetskopiering till URL: en. Den här funktionen är tillgänglig från och med SQL Server 2012 SP1 CU2 och rekommenderas för säkerhetskopiering till de anslutna datadiskarna.

    När din säkerhetskopiering eller återställning med hjälp av Azure storage, Följ rekommendationerna i [SQL Server säkerhetskopiering till URL: en metodtips och felsökning](https://msdn.microsoft.com/library/jj919149.aspx) och [återställer från säkerhetskopior lagras i Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure?view=sql-server-2017). Du kan även automatisera dessa säkerhetskopior med hjälp av [automatisk säkerhetskopiering för SQL Server i Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup).

-   **Säkerhetskopiera till Azure Stack-lagring.** Du kan säkerhetskopiera till Azure Stack-lagring på ett liknande sätt som med säkerhetskopiering till Azure Storage. När du skapar en säkerhetskopia i SQL Server Management Studio (SSMS) kan behöva du ange konfigurationsinformation manuellt. Du kan inte använda SSMS för att skapa lagringsbehållaren eller signatur för delad åtkomst. SSMS ansluter bara till Azure-prenumerationer, inte Azure Stack-prenumerationer. I stället måste du skapa lagringskontot, behållaren och signatur för delad åtkomst i Azure Stack-portalen eller med PowerShell.

    När du placerar information i dialogrutan SQL Server-säkerhetskopiering:

    ![SQL Server-säkerhetskopiering](./media/sql-server-vm-considerations/image3.png)

    > [!NOTE]  
    > Signatur för delad åtkomst är SAS-token från Azure Stack-portalen, utan ledande '?' i strängen. Om du använder kopieringsfunktionen från portalen kan du behöva ta bort ledande '?' att arbeta i SQL Server-token.

    När du har mål för säkerhetskopian som ställer in och konfigurerats i SQL Server, du kan sedan säkerhetskopiera till Azure Stack blob storage.

## <a name="next-steps"></a>Nästa steg

[Med hjälp av tjänster eller att skapa appar för Azure Stack](azure-stack-considerations.md)