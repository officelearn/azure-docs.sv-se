---
title: Azure Backup-arkitektur
description: Översikt över arkitektur, komponenter och processer som används av Azure Backup-tjänsten.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: raynew
ms.openlocfilehash: 98ffe145103b4be04014627ed04d04dcf7542015
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60647451"
---
# <a name="azure-backup-architecture"></a>Azure Backup-arkitektur

Du kan använda den [Azure Backup-tjänsten](backup-overview.md) att säkerhetskopiera data till Microsoft Azure-molnplattformen. Den här artikeln sammanfattas Azure Backup-arkitektur, komponenter och processer. 

## <a name="what-does-azure-backup-do"></a>Vad är Azure Backup?

Azure Backup säkerhetskopierar data, datorn och arbetsbelastningar som körs på lokala datorer och instanser av Azure virtuella datorer (VM). Det finns ett antal scenarier för Azure Backup.

## <a name="how-does-azure-backup-work"></a>Hur fungerar Azure Backup?

Du kan säkerhetskopiera datorer och data med hjälp av ett antal metoder:

- **Säkerhetskopiera lokala datorer**:
    - Du kan säkerhetskopiera lokala Windows-datorer direkt till Azure med hjälp av Azure Backup Microsoft Azure Recovery Services MARS-agenten. Linux-datorer stöds inte.
    - Du kan säkerhetskopiera lokala datorer till en sekundär server (System Center Data Protection Manager (DPM) eller Microsoft Azure Backup Server (MABS)). Du kan sedan säkerhetskopiera backup-servern till ett Recovery Services-valv i Azure.

- **Säkerhetskopiera virtuella Azure-datorer**:
    - Du kan säkerhetskopiera virtuella Azure-datorer direkt. Azure Backup installerar ett tillägg för säkerhetskopiering till Azure VM-agenten som körs på den virtuella datorn. Det här tillägget säkerhetskopierar hela VM.
    - Du kan säkerhetskopiera vissa filer och mappar på den virtuella Azure-datorn genom att köra MARS-agenten.
    - Du kan säkerhetskopiera virtuella Azure-datorer till MABS som körs i Azure och du kan sedan lagra MABS till ett Recovery Services-valv.

Läs mer om [vad du kan säkerhetskopiera](backup-overview.md) och [stöds säkerhetskopieringsscenarier](backup-support-matrix.md).

## <a name="where-is-data-backed-up"></a>Där data säkerhetskopieras?

Azure Backup lagrar säkerhetskopierade data i ett Recovery Services-valv. Ett valv är en-onlinelagringsentitet i Azure som används för att lagra data, till exempel säkerhetskopior, återställningspunkter och principer för säkerhetskopiering.

Recovery Services-valv har följande funktioner:

- Valv är det enkelt att organisera dina säkerhetskopierade data när du minimerar hanteringskostnaden.
- Du kan skapa upp till 500 valv i varje Azure-prenumeration.
- Du kan övervaka säkerhetskopierade objekt i ett valv, inklusive virtuella datorer i Azure och lokala datorer.
- Du kan hantera valvåtkomst med Azure [rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
- Du anger hur data i valvet replikeras för redundans:
    - **Lokalt redundant lagring (LRS)**: Du kan använda LRS för att skydda mot fel i ett datacenter. LRS replikerar data till en lagringsskalningsenhet. [Läs mer](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs).
    - **GEO-redundant lagring (GRS)**: Du kan använda GRS för att skydda mot regionomfattande avbrott. GRS replikerar dina data till en sekundär region. [Läs mer](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs). 
    - Som standard använder Recovery Services-valv GRS. 

## <a name="backup-agents"></a>Backup-agenter

Azure Backup innehåller olika backup-agenter, beroende på vilken typ av dator säkerhetskopieras:

**Agent** | **Detaljer** 
--- | --- 
**MARS-agenten** | <ul><li>Körs på enskilda lokala Windows Server-datorer för säkerhetskopiering av filer, mappar och systemtillståndet.</li> <li>Körs på virtuella Azure-datorer för säkerhetskopiering av filer, mappar och systemtillståndet.</li> <li>Körs på DPM/MABS-servrar för att säkerhetskopiera DPM/MABS lokal Lagringsdisken till Azure.</li></ul> 
**Azure VM-tillägg** | Körs på Azure Virtual Machines säkerhetskopierar dem till ett valv.

## <a name="backup-types"></a>Typer av säkerhetskopiering

I följande tabell beskrivs de olika typerna av säkerhetskopieringar och när de används:

**Typ av säkerhetskopiering** | **Detaljer** | **Användning**
--- | --- | ---
**Fullständig** | En fullständig säkerhetskopia innehåller hela datakällan. Tar mer bandbredd i nätverket än differentiell eller inkrementell säkerhetskopiering. | Används för den första säkerhetskopieringen.
**Differentiell** |  En differentiell säkerhetskopiering lagrar de block som har ändrats sedan den första fullständiga säkerhetskopieringen. Använder en mindre mängd nätverk och lagring och hålla inte redundanta kopior av oförändrade data.<br/><br/> Ineffektiv eftersom datablock som har inte ändrats mellan senare säkerhetskopieringar överförs och lagras. | Används inte av Azure Backup.
**Inkrementell** | En inkrementell säkerhetskopia lagrar endast datablock som har ändrats sedan föregående säkerhetskopia. Hög effektivitet för lagring och nätverk. <br/><br/> Det finns inget behov av att komplettera med fullständiga säkerhetskopieringar med inkrementell säkerhetskopiering. | Används av DPM/MABS för säkerhetskopiering till disk, och i alla säkerhetskopieringar till Azure.

## <a name="sql-server-backup-types"></a>Typer av SQL Server-säkerhetskopiering

I följande tabell beskrivs de olika typerna av säkerhetskopieringar som används för SQL Server-databaser och hur ofta de används:

**Typ av säkerhetskopiering** | **Detaljer** | **Användning**
--- | --- | ---
**Fullständig säkerhetskopiering** | En fullständig säkerhetskopia av databas säkerhetskopierar hela databasen. Den innehåller alla data i en specifik databas eller i en uppsättning filgrupper eller filer. En fullständig säkerhetskopia innehåller också tillräckligt med loggarna om du vill återställa dessa data. | Du kan endast utlösa en fullständig säkerhetskopiering per dag.<br/><br/> Du kan välja att göra en fullständig säkerhetskopiering med dagliga och veckovisa intervall.
**Differentiell säkerhetskopia** | En differentiell säkerhetskopiering baseras på de senaste, föregående fullständig säkerhetskopiering.<br/><br/> Den samlar in data som ändrats sedan den fullständiga säkerhetskopian. |  Du kan endast utlösa en differentiell säkerhetskopia per dag.<br/><br/> Du kan inte konfigurera en fullständig säkerhetskopia och en differentiell säkerhetskopia samma dag.
**Säkerhetskopiering av transaktionsloggen** | Med en loggsäkerhetskopia kan en återställning som baseras på tidpunkt utföras upp till en specifik sekund. | Du kan som mest konfigurera loggsäkerhetskopior var 15:e minut.

### <a name="comparison-of-backup-types"></a>Jämförelse av typer av säkerhetskopiering

Förbrukning av lagringsutrymme, för återställningstid (RTO) och nätverksförbrukning varierar för varje typ av säkerhetskopiering. Följande bild visar en jämförelse av typerna av säkerhetskopiering:

- Består datakälla A av 10 storage block, A1 – A10, som säkerhetskopieras varje månad.
- Block A2, A3, A4 och A9 ändras under den första månaden, och block A5 ändras nästa månad.
- För differentiella säkerhetskopieringar säkerhetskopieras i den andra månaden ändrade block A2, A3, A4 och A9. Den tredje månaden säkerhetskopieras samma block igen, tillsammans med ändrade A5-block. De ändrade blocken fortsätter att säkerhetskopieras tills nästa fullständiga säkerhetskopiering sker.
- För inkrementell säkerhetskopiering i den andra månaden block A2, A3, A4 och A9 markerade som ändrats och överförs. I den tredje månaden markeras och överförs endast ändrade A5-block. 

![bild som visar jämförelser av metoder för säkerhetskopiering](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Backup-funktioner

I följande tabell sammanfattas funktionerna som stöds för de olika typerna av säkerhetskopiering:

**Funktion** | **Lokala Windows Server-datorer (direkt)** | **Virtuella Azure-datorer** | **Datorer eller appar med DPM/MABS**
--- | --- | --- | ---
Säkerhetskopiera till valvet | ![Ja][green] | ![Ja][green] | ![Ja][green] 
Säkerhetskopiera till DPM/MABS-disk, sedan till Azure | | | ![Ja][green] 
Komprimera data som skickas för säkerhetskopiering | ![Ja][green] | Ingen komprimering används vid överföring av data. Lagring är fylld något, men återställningen går snabbare.  | ![Ja][green] 
Köra inkrementell säkerhetskopiering |![Ja][green] |![Ja][green] |![Ja][green] 
Säkerhetskopiera deduplicerade diskar | | | ![Delvis][yellow]<br/><br/> För DPM/MABS distribueras-servrar endast lokalt. 

![tabellnyckel](./media/backup-architecture/table-key.png)

## <a name="architecture-direct-backup-of-azure-vms"></a>Arkitektur: Direkt säkerhetskopiering av virtuella Azure-datorer

1. När du aktiverar säkerhetskopiering för en Azure virtuell dator körs en säkerhetskopiering enligt det schema du anger.
1. Under den första säkerhetskopieringen installeras en säkerhetskopieringstillägget på den virtuella datorn om Virtuellt datorn körs.
    - VMSnapshot-tillägget har installerats för Windows-datorer.
    - Tillägget VMSnapshot Linux för virtuella Linux-datorer är installerad.
1. Tillägget tar en ögonblicksbild för lagring på servernivå. 
    - För Windows-datorer som kör samordnar säkerhetskopiering med tjänsten Windows Volume Shadow Copy (VSS) att ta en programkonsekvent ögonblicksbild av den virtuella datorn. Som standard tar Backup Fullständig VSS-säkerhetskopiering. Om säkerhetskopieringen är det går inte att ta en programkonsekvent ögonblicksbild, tar en filkonsekvent ögonblicksbild.
    - Säkerhetskopieringen tar en filkonsekvent ögonblicksbild för virtuella Linux-datorer. Du måste manuellt anpassa före/efter-skript för appkonsekventa ögonblicksbilder.
    - Backup optimeras genom att säkerhetskopiera varje VM-disk parallellt. För varje disk som säkerhetskopieras, Azure Backup läser block på disken och lagrar bara de ändrade data. 
1. När ögonblicksbilden tas data har överförts till valvet. 
    - Endast datablock som ändrats sedan den senaste säkerhetskopieringen kopieras.
    - Data är inte krypterade. Azure Backup kan säkerhetskopiera virtuella Azure-datorer som har krypterats med hjälp av Azure Disk Encryption.
    - Ögonblicksbilddata kan inte kopieras direkt till valvet. Vid Högbelastningstider, kan säkerhetskopieringen ta några timmar. Total tid för säkerhetskopiering för en virtuell dator ska vara mindre än 24 timmar för principer för daglig säkerhetskopiering.
1. När data skickas till valvet, tas ögonblicksbilden bort och en återställningspunkt skapas.

Virtuella Azure-datorer behöver Internetåtkomst för kontroll kommandon. Om du säkerhetskopierar arbetsbelastningar på den virtuella datorn (till exempel SQL Server database-säkerhetskopior), måste backend-data också tillgång till internet. 

![Säkerhetskopiering av virtuella Azure-datorer](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>Arkitektur: Direkt säkerhetskopiering av lokala Windows Server-datorer eller Virtuella Azure-filer eller mappar

1. Om du vill konfigurera scenariot måste du hämta och installera MARS-agenten på datorn. Sedan välja du vad du ska säkerhetskopiera när säkerhetskopieringar ska köras och hur länge de ska behållas i Azure.
1. Den första säkerhetskopieringen körs enligt inställningarna för säkerhetskopiering.
1. MARS-agenten använder VSS för att ta en point-in-time-ögonblicksbild av de volymer som valts för säkerhetskopiering.
    - MARS-agenten använder bara Windows system Skrivåtgärden för att samla in ögonblicksbilden.
    - Eftersom agenten inte använder programmet VSS-skrivare, registrerar inte den appkonsekventa ögonblicksbilder.
1. Efter att ögonblicksbilden med VSS skapar en virtuell hårddisk (VHD) i cache-mappen som du angav när du har konfigurerat säkerhetskopieringen i MARS-agenten. Agenten lagras också kontrollsummor för varje datablock.
1. Inkrementell säkerhetskopiering köras enligt det schema som du anger, såvida du inte kör en ad hoc-säkerhetskopiering.
1. Ändrade filer identifieras i inkrementella säkerhetskopieringar och en ny virtuell Hårddisk har skapats. Den virtuella Hårddisken komprimeras och krypteras, och sedan det skickas till valvet.
1. När den inkrementella säkerhetskopieringen är klar samman den nya virtuella Hårddisken med den virtuella Hårddisken som skapats efter den inledande replikeringen. Den här sammanfogade virtuella Hårddisken innehåller det senaste tillståndet som ska användas för jämförelse för pågående säkerhetskopiering.

![Säkerhetskopiering av lokala Windows Server-datorer med MARS-agenten](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Arkitektur: Säkerhetskopiera till DPM/MABS

1. Du installerar DPM- eller MABS-skyddsagenten på datorer som du vill skydda. Du sedan lägga till datorer i en skyddsgrupp i DPM.
    - DPM- eller MABS-server måste vara finnas lokalt för att skydda lokala datorer.
    - MABS-server måste finnas i Azure, som körs som en Azure-dator för att skydda virtuella datorer i Azure.
    - Med DPM/MABS kan du skydda säkerhetskopierade volymer, resurser, filer och mappar. Du kan även skydda systemtillståndet för en dator (utan operativsystem) och du kan skydda specifika appar med app-anpassade inställningar för säkerhetskopiering.
1. När du ställer in skydd för en dator eller en app i DPM/MABS, väljer du för att säkerhetskopiera till den lokala MABS/DPM-disken för kortsiktig lagring och till Azure för onlineskydd. Du kan ange när säkerhetskopian till lokal DPM/MABS-lagring ska köras och när onlinesäkerhetskopiering till Azure ska köras.
1. Disken för skyddad arbetsbelastning har säkerhetskopierats till lokala MABS/DPM-diskar, enligt det schema som du har angett.
4. MARS-agenten som körs på DPM/MABS-server säkerhetskopieras DPM/MABS-diskar till valvet.

![Säkerhetskopiering av datorer och arbetsbelastningar som skyddas av DPM- eller MABS](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Azure VM-lagring

Virtuella Azure-datorer använder diskar för att lagra sitt operativsystem, appar och data. Varje virtuell Azure-dator har minst två diskar: en disk för operativsystemet och en tillfällig disk. Virtuella Azure-datorer kan också ha datadiskar för appdata. Diskarna lagras som virtuella hårddiskar.

- VHD lagras som sidblobar i standard- eller premium storage-konton i Azure:
    - **Standard-lagring:** Tillförlitlig, billig disksupport för virtuella datorer som kör arbetsbelastningar som inte är känslig för fördröjning. Standard-lagring kan använda standard Solid State-hårddisk (SSD) eller standard hårddisken (HDD) diskar.
    - **Premium-lagring:** Högpresterande disksupport. Använder premium SSD-diskar.
- Det finns olika prestandanivåer för diskar:
    - **Standard HDD-disk:** Backas upp av hårddiskar och används för kostnadseffektiv lagring.
    - **Standard SSD-disk:** En kombination av premiumdiskar för SSD och HDD-standarddiskar. Erbjuder mer konsekventa prestanda och tillförlitlighet än HDD, men fortfarande kostnadseffektiv.
    - **Premium SSD-disk:** Backas upp av SSD-enheter och ger hög prestanda och låg fördröjning för virtuella datorer som kör I/O-intensiva arbetsbelastningar.
- Vara kan hanterade eller ohanterade diskar:
    - **Ohanterade diskar:** Traditionella typen av diskar som används av virtuella datorer. För dessa diskar, skapa ditt eget lagringskonto och ange det när du skapar disken. Sedan måste du ta reda på hur du maximerar lagringsresurser för dina virtuella datorer.
    - **Hanterade diskar:** Azure skapar och hanterar lagringskonton åt dig. Du anger storlek och nivå för disk och Azure skapar hanterade diskar. När du lägger till diskar och skala virtuella datorer, hanterar Azure storage-konton.

Mer information om och vilka ledigt diskutrymme för virtuella datorer finns i följande artiklar:

- [Azure-hanterade diskar för virtuella Windows-datorer](../virtual-machines/windows/managed-disks-overview.md)
- [Azure-hanterade diskar för virtuella Linux-datorer](../virtual-machines/linux/managed-disks-overview.md)
- [Tillgängliga disktyper för virtuella datorer](../virtual-machines/windows/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Säkerhetskopiera och återställa virtuella Azure-datorer med premium storage 

Du kan säkerhetskopiera virtuella Azure-datorer med premium storage med Azure Backup:

- Under processen med att säkerhetskopiera virtuella datorer med premium storage skapar Backup-tjänsten en tillfällig mellanlagringsplats med namnet *AzureBackup -*, i lagringskontot. Storleken på mellanlagringsplatsen är lika med storleken på återställningspunktens ögonblicksbild.
- Kontrollera att premium storage-konto har tillräckligt med ledigt utrymme för den tillfälliga mellanlagringsplatsen får platsen. [Läs mer](../storage/common/storage-scalability-targets.md#premium-performance-storage-account-scale-limits). Ändra inte mellanlagringsplatsen.
- När säkerhetskopieringen är klar tas mellanlagringsplatsen bort.
- Priset för lagringen som används för mellanlagringsplatsen är konsekvent med [priser för premium storage](../virtual-machines/windows/disks-types.md#billing).

När du återställer virtuella Azure-datorer med premium storage kan återställa du dem till premium eller standard-lagring. Vanligtvis vill du återställa dem till premium storage. Men om du behöver bara en delmängd av filerna från den virtuella datorn kan det vara kostnadseffektivt att återställa dem till standardlagring.

### <a name="back-up-and-restore-managed-disks"></a>Säkerhetskopiera och återställa hanterade diskar

Du kan säkerhetskopiera virtuella Azure-datorer med hanterade diskar:

- Du säkerhetskopierar virtuella datorer med hanterade diskar på samma sätt som du gör eventuella andra virtuella Azure-datorer. Du kan säkerhetskopiera den virtuella datorn direkt från virtuella datorinställningar eller du kan aktivera säkerhetskopiering för virtuella datorer i Recovery Services-valvet.
- Du kan säkerhetskopiera virtuella datorer på hanterade diskar via RestorePoint-samlingar som är byggda ovanpå hanterade diskar.
- Azure Backup stöder också säkerhetskopiering av virtuella datorer med hanterade diskar som krypterats med hjälp av Azure Disk Encryption.

När du återställer virtuella datorer med hanterade diskar kan återställa du till en hel virtuell dator med hanterade diskar eller till ett lagringskonto:

- Azure hanterar de hanterade diskarna under återställningsprocessen. Om du använder kontoalternativet kan du hantera det lagringskonto som har skapats under återställningsprocessen.
- Om du återställer en hanterad virtuell dator som är krypterad, kontrollera att den Virtuella datorns nycklar och hemligheter finnas i nyckelvalvet innan du startar återställningsprocessen.

## <a name="next-steps"></a>Nästa steg

- Granska stödmatris till [Lär dig mer om funktioner som stöds och begränsningar för säkerhetskopieringsscenarier](backup-support-matrix.md).
- Konfigurera säkerhetskopiering för en av dessa scenarier:
    - [Säkerhetskopiera virtuella Azure-datorer](backup-azure-arm-vms-prepare.md).
    - [Säkerhetskopiera Windows-datorer direkt](tutorial-backup-windows-server-to-azure.md) utan en reservserver.
    - [Konfigurera MABS](backup-azure-microsoft-azure-backup.md) för säkerhetskopiering till Azure och säkerhetskopiera sedan arbetsbelastningar till MABS.
    - [Konfigurera DPM](backup-azure-dpm-introduction.md) för säkerhetskopiering till Azure och säkerhetskopiera sedan arbetsbelastningar till DPM.


[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png

