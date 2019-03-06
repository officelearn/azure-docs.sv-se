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
ms.openlocfilehash: 034f7f6d8636ced748987c9b0e584790205c0083
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57435311"
---
# <a name="azure-backup-architecture"></a>Azure Backup-arkitektur

Med [Azure Backup-tjänsten](backup-overview.md) kan du säkerhetskopiera data till Microsoft Azure-molnet. Den här artikeln sammanfattas Azure Backup-arkitektur, komponenter och processer. 


## <a name="what-does-azure-backup-do"></a>Vad är Azure Backup?

Azure Backup säkerhetskopierar data, datorn och arbetsbelastningar som körs på lokala datorer och virtuella Azure-datorer. Det finns ett antal scenarier för Azure Backup.

## <a name="how-does-azure-backup-work"></a>Hur fungerar Azure Backup?

Du kan säkerhetskopiera datorer och data med hjälp av ett antal metoder.

- **Säkerhetskopiera lokala datorer**:
    - Du kan säkerhetskopiera lokala Windows-datorer direkt till Azure med hjälp av Azure Backup Microsoft Azure Recovery Services MARS-agenten. Linux-datorer stöds inte.
    - Du kan säkerhetskopiera lokala datorer till en sekundär server (System Center Data Protection Manager (DPM) eller Microsoft Azure Backup Server (MABS)) och sedan i sin tur säkerhetskopiera backup server till ett Azure Backup Recovery Services-valv i Azure.
- **Säkerhetskopiera virtuella Azure-datorer**:
    - Du kan säkerhetskopiera virtuella Azure-datorer direkt. Azure Backup installerar ett tillägg för säkerhetskopiering till Azure VM-agenten som körs på den virtuella datorn ska göra detta. Säkerhetskopierar hela VM.
    - Du kan säkerhetskopiera vissa filer och mappar på den virtuella Azure-datorn genom att köra MARS-agenten.
    - Du kan säkerhetskopiera virtuella Azure-datorer till MABS som körs i Azure och sedan i sin tur säkerhetskopiera MABS till valvet.

Läs mer om [vad du kan säkerhetskopiera](backup-overview.md), och [stöds säkerhetskopieringsscenarier](backup-support-matrix.md).


## <a name="where-is-data-backed-up"></a>Där data säkerhetskopieras?

Azure Backup-lagrar säkerhetskopierade data i ett Recovery Services-valv. Ett valv är en onlinelagringsentitet i Azure som används för att lagra data, som säkerhetskopior, återställningspunkter och principer för säkerhetskopiering.

- Valv är det enkelt att organisera dina säkerhetskopierade data när du minimerar hanteringskostnaden.
- Du kan skapa upp till 500 valv i varje Azure-prenumeration.
- Du kan övervaka säkerhetskopierade objekt i ett valv, inklusive virtuella datorer i Azure och lokala datorer.
- Du kan hantera valvåtkomst med Azure [rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
- Du anger hur data i valvet replikeras för redundans:
    - **LRS**: Du kan använda lokalt redundant lagring (LRS) för att skyddar mot fel i ett datacenter. LRS replikerar data till en lagringsskalningsenhet. [Läs mer](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs).
    - **GRS**: Du kan använda geo-redundant lagring (GRS): Skyddar mot regionomfattande avbrott. Den replikerar dina data till en sekundär region. [Läs mer](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs). 
    - Som standard använder GRS i Recovery Services-valv för säkerhetskopiering. 




### <a name="backup-agents"></a>Backup-agenter

Azure Backup innehåller olika agenter, beroende på vilken typ av säkerhetskopiering.

**Agent** | **Detaljer** 
--- | --- 
**Microsoft Azure Recovery Services MARS-agenten** | (1) körs på enskilda lokala Windows-servrar för säkerhetskopiering av filer, mappar och systemtillstånd<br/><br/> 2) körs på virtuella Azure-datorer för säkerhetskopiering av filer, mappar och systemtillstånd.<br/><br/>  3) körs på DPM/MABS-servrar för att säkerhetskopiera DPM/MABS lokal Lagringsdisken till Azure. 
**Azure VM-tillägg** | Körs på Azure Virtual Machines säkerhetskopierar dem till ett valv.


## <a name="backup-types"></a>Typer av säkerhetskopiering

**Typ av säkerhetskopiering** | **Detaljer** | **Användning**
--- | --- | ---
**Fullständig** | En säkerhetskopia innehåller hela datakällan. Fullständig säkerhetskopiering tar mer bandbredd i nätverket. | Används för den första säkerhetskopieringen.
**Differentiell** |  Lagrar de block som har ändrats sedan den första fullständiga säkerhetskopieringen. Använder en mindre mängd nätverk och lagring och längre inte redundanta kopior av oförändrade data.<br/><br/> Ineffektiv eftersom datablock oförändrade mellan efterföljande säkerhetskopieringar överförs och lagras. | Används inte av Azure Backup.
**Inkrementell** | Hög effektivitet för lagring och nätverk. Lagrar endast datablock som har ändrats sedan föregående säkerhetskopia. <br/><br/> Inget behov av att med inkrementell säkerhetskopiering, det finns inget behov av att komplettera med fullständiga säkerhetskopieringar. | Används av DPM/MABS för säkerhetskopiering till disk, och i alla säkerhetskopieringar till Azure.

## <a name="sql-server-backup-types"></a>Typer av SQL Server-säkerhetskopiering

**Typ av säkerhetskopiering** | **Detaljer** | **Användning**
--- | --- | ---
**Fullständig säkerhetskopiering** | En fullständig säkerhetskopia av databas säkerhetskopierar hela databasen. Den innehåller alla data i en specifik databas eller en uppsättning filgrupper eller tillräckligt många loggar att återställa dessa data och filer. | Du kan endast utlösa en fullständig säkerhetskopiering per dag.<br/><br/> Du kan välja att skapa en fullständig säkerhetskopiering en gång per dag eller per vecka.
**Differentiell säkerhetskopia** | En differentiell säkerhetskopia baseras på den senaste föregående fullständiga säkerhetskopieringen av data.<br/><br/> Den samlar in data som ändrats sedan den fullständiga säkerhetskopian. |  Du kan endast utlösa en differentiell säkerhetskopia per dag.<br/><br/> Du kan inte konfigurera en fullständig säkerhetskopia och en differentiell säkerhetskopia samma dag.
**Säkerhetskopiering av transaktionsloggen** | Med en loggsäkerhetskopia kan en återställning som baseras på tidpunkt utföras upp till en specifik sekund. | Du kan som mest konfigurera loggsäkerhetskopior var 15:e minut.


### <a name="comparison"></a>Jämförelse

Förbrukning av lagringsutrymme, för återställningstid (RTO) och nätverksförbrukning varierar för varje typ av säkerhetskopiering. Följande bild visar en jämförelse av säkerhetskopieringstyper.
- Datakällan som en består av 10 blockerar A1 – A10, som säkerhetskopieras varje månad.
- Block A2, A3, A4 och A9 ändras under den första månaden, och block A5 ändras nästa månad.
- För differentiella säkerhetskopieringar säkerhetskopieras i den andra månaden ändrade block A2, A3, A4 och A9. Den tredje månaden säkerhetskopieras samma block igen, tillsammans med ändrade A5-block. De ändrade blocken fortsätter att säkerhetskopieras tills nästa fullständiga säkerhetskopiering sker.
- För inkrementell säkerhetskopiering, efter att en fullständig säkerhetskopiering i den första månaden får block A2, A3, A4 och A9 markerade som ändrats och överförs till den andra månaden. I den tredje månaden markeras och överförs endast ändrade A5-block. 

![bild som visar jämförelser av metoder för säkerhetskopiering](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Backup-funktioner

I följande tabell sammanfattas funktioner för olika typer av säkerhetskopiering.

**Funktion** | **Lokala Windows-datorer (direkt)** | **Virtuella Azure-datorer** | **Datorer/appar med DPM/MABS**
--- | --- | --- | ---
Säkerhetskopiera till valvet | ![Ja][green] | ![Ja][green] | ![Ja][green] 
Säkerhetskopiering till disk för DPM/MABS än Azure | | | ![Ja][green] 
Komprimera data som skickas för säkerhetskopiering | ![Ja][green] | Ingen komprimering används vid överföring av data. Lagring är fylld något, men återställningen går snabbare.  | ![Ja][green] 
Köra inkrementell säkerhetskopiering |![Ja][green] |![Ja][green] |![Ja][green] 
Säkerhetskopiera deduplicerade diskar | | | ![Delvis][yellow]<br/><br/> För DPM/MABS distribueras-servrar endast lokalt. 

![tabellförklaring](./media/backup-architecture/table-key.png)





## <a name="architecture-direct-backup-of-azure-vms"></a>Arkitektur: Direkt säkerhetskopiering av virtuella Azure-datorer

1. När du aktiverar säkerhetskopiering för en Azure virtuell dator körs en säkerhetskopia i enlighet med det schema du anger.
2. Under den första säkerhetskopieringen installeras en säkerhetskopieringstillägget på den virtuella datorn om den körs.
    - För Windows-datorer i VMSnapshot installeras tillägget.
    - Tillägget VMSnapshot Linux för virtuella Linux-datorer är installerad.
3. Tillägget tar en nivå ögonblicksbild för lagring. 
    - För Windows-datorer som kör samordnar säkerhetskopiering med VSS för att ta en programkonsekvent ögonblicksbild av den virtuella datorn. Som standard tar Backup Fullständig VSS-säkerhetskopiering. Om säkerhetskopieringen är det går inte att ta en programkonsekvent ögonblicksbild, tar en filkonsekvent ögonblicksbild.
    - För Linux virtuella datorer Backup tar du en filkonsekvent säkerhetskopiering. Du måste manuellt anpassa före/efter-skript för appkonsekventa ögonblicksbilder.
    - Backup optimeras genom att säkerhetskopiera varje VM-disk parallellt. För varje disk som säkerhetskopieras, Azure Backup läser block på disken och lagrar bara de ändrade data. 
4. När ögonblicksbilden tas data har överförts till valvet. 
    - Endast datablock som har ändrats sedan den senaste säkerhetskopieringen kopieras.
    - Data är inte krypterade. Azure Backup kan säkerhetskopiera virtuella Azure-datorer som är krypterade med hjälp av Azure Disk Encryption (ADE).
    - Ögonblicksbilddata kan inte kopieras direkt till valvet. Det kan ta några timmar vid Högbelastningstider. Total tid för säkerhetskopiering för en virtuell dator vara mindre att 24 timmar för principer för daglig säkerhetskopiering.
5. När data har skickats till valvet, tas ögonblicksbilden bort och skapa en återställningspunkt.

Observera att virtuella Azure-datorer behöver Internetåtkomst för kontroll kommandon. Om du säkerhetskopierar arbetsbelastningar på den virtuella datorn (till exempel SQL Server-säkerhetskopia) måste tillbaka data också tillgång till internet. 

![Säkerhetskopiering av virtuella Azure-datorer](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-machinesazure-vm-filesfolders"></a>Arkitektur: Direkt säkerhetskopiering av en lokal Windows datorer och Azure VM-filer/mappar

1. Om du vill konfigurera scenariot, ladda ned du och installera Microsoft Azure Recovery Services MARS-agenten på datorn, väljer säkerhetskopiera när säkerhetskopieringar ska köras och hur länge de ska behållas i Azure.
2. Den första säkerhetskopieringen körs i enlighet med inställningarna för säkerhetskopiering.
3. MARS-agenten använder tjänsten Windows Volume Shadow Copy (VSS) för att ta en point-in-time-ögonblicksbild av de volymer som valts för säkerhetskopiering.
    - MARS-agenten endast använder Windows System skriva att samla in ögonblicksbilden.
    - Agenten använder inte programmet VSS-skrivare och därför samla in inte appkonsekventa ögonblicksbilder.
3. Efter att ögonblicksbilden med VSS MARS-agenten skapar en virtuell Hårddisk i cache-mappen som du angav när du har konfigurerat säkerhetskopiering, och lagrar kontrollsummor skapas för varje datablock. 
4. Inkrementell säkerhetskopiering köras i enlighet med det schema som du anger, såvida du inte kör en ad hoc-säkerhetskopiering.
5. Ändrade filer identifieras i inkrementella säkerhetskopieringar och en ny virtuell Hårddisk har skapats. Det har komprimeras och krypteras och skickas till valvet.
6. När den inkrementella säkerhetskopieringen är klar samman den nya virtuella Hårddisken med den virtuella Hårddisken skapas efter den inledande replikeringen att tillhandahålla det senaste tillståndet som ska användas för jämförelse för pågående säkerhetskopiering. 

![Säkerhetskopiering av en lokal Windows server med MARS-agenten](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Arkitektur: Säkerhetskopiera till DPM/MABS

1. Du installerar DPM- eller MABS-skyddsagenten på datorer som du vill skydda och lägga till datorer i en skyddsgrupp i DPM.
    - DPM- eller MABS-server måste vara finnas lokalt för att skydda lokala datorer.
    - MABS-server måste finnas i Azure, som körs som en Azure-dator för att skydda virtuella datorer i Azure.
    - Med hjälp av DPM/MABS kan du skydda Säkerhetskopiera volymer, resurser, filer och -mappen. Du kan skydda datorer system tillstånd/utan operativsystem och skydda specifika appar med app-anpassade inställningar för säkerhetskopiering.
2. När du ställer in skydd för en dator eller en app i DPM/MABS, väljer du för att säkerhetskopiera till den lokala MABS/DPM-disken för kortsiktig lagring och till Azure (online protection). Du kan ange när säkerhetskopian till lokal DPM/MABS-lagring ska köras och när onlinesäkerhetskopiering till Azure ska köras.
3. Disken för skyddad arbetsbelastning har säkerhetskopierats till de lokala MABS/DPM-diskarna i enlighet med det schema som du har angett.
4. MARS-agenten som körs på DPM/MABS-server säkerhetskopieras DPM/MABS-diskar till valvet.

![Säkerhetskopiering av datorer/arbetsbelastningar som skyddas av DPM- eller MABS](./media/backup-architecture/architecture-dpm-mabs.png)







## <a name="azure-vm-storage"></a>Azure VM-lagring

- Virtuella Azure-datorer använder diskar för att lagra sitt operativsystem, appar och data.
- Azure virtuella datorer har minst två diskar. En för operativsystemet och en tillfällig disk. De kan också ha datadiskar för appdata. Diskarna lagras som virtuella hårddiskar.
- Virtuella hårddiskar lagras som sidblobar i standard- eller premium storage-konton i Azure.
    - Standard-lagring: Tillförlitlig, billig disksupport för virtuella datorer som kör arbetsbelastningar som inte är känslig för fördröjning. Standardlagring kan använda standarddiskar SSD eller HDD standarddiskar.
    - Premium-lagring: Högpresterande disksupport. Använder premium SSD-diskar.
- Det finns olika prestandanivåer för diskar:
    - Standard HDD-disk: Backas upp av hårddiskar och används för kostnadseffektiv lagring.
    - Standard SSD-disk: Kombinerar elementet premium SSD-diskar och standard HDD-diskar, erbjuder mer konsekventa prestanda och tillförlitlighet än HDD, men fortfarande kostnadseffektiv.
    - Premium SSD-disk: Backas upp av SSD, med höga prestanda och låg fördröjning för virtuella datorer som kör i/o-intensiva arbetsbelastningar.
- Vara kan hanterade eller ohanterade diskar:
    - Ohanterade diskar: Traditionella typer av diskar som används av virtuella datorer. För dessa diskar, skapa ditt eget lagringskonto och ange det när du skapar disken. Du måste ta reda på hur du maximerar lagringsresurser för dina virtuella datorer.
    - Hanterade diskar: Azure hanterar skapa och hantera lagringskonton för dig. Du anger disk storlek och nivå och skapar Azure, hantera diskar för dig. Azure hanterar lagring när du lägger till diskar och skala virtuella datorer.

Läs mer:

- Mer information om disklagring för [Windows](../virtual-machines/windows/managed-disks-overview.md) och [Linux](../virtual-machines/linux/managed-disks-overview.md) virtuella datorer.
- Lär dig mer om de tillgängliga [-disktyper](../virtual-machines/windows/disks-types.md) som standard och premium.


### <a name="backing-up-and-restoring-azure-vms-with-premium-storage"></a>Säkerhetskopiera och återställa virtuella Azure-datorer med premium storage 

Du kan säkerhetskopiera virtuella Azure-datorer med premium storage med Azure Backup:

- När du säkerhetskopierar virtuella datorer med premium storage skapar Backup-tjänsten en tillfällig mellanlagringsplats med namnet ”AzureBackup-”, i lagringskontot. Storleken på mellanlagringsplatsen är lika stor som återställningspunktens ögonblicksbild.
- Kontrollera att premium storage-konto har tillräckligt med ledigt utrymme för den tillfälliga mellanlagringsplatsen får platsen. [Läs mer](../storage/common/storage-scalability-targets.md#premium-storage-account-scale-limits). Ändra inte mellanlagringsplatsen.
- När säkerhetskopieringen är klar tas mellanlagringsplatsen bort.
- Priset för lagringen som används för mellanlagringsplatsen är konsekvent med [priser för premium storage](../virtual-machines/windows/disks-types.md#billing).

När du återställer virtuella Azure-datorer med premium storage kan återställa du dem till premium eller standard-lagring. Vanligtvis vill du återställa till premium, men det kan vara kostnadseffektivt att återställa till standard om du behöver bara en delmängd av filerna från den virtuella datorn.

### <a name="backing-up-and-restoring-managed-disks"></a>Säkerhetskopiera och återställa hanterade diskar

Du kan säkerhetskopiera virtuella Azure-datorer med hanterade diskar.
- Du säkerhetskopierar virtuella datorer med hanterade diskar på samma sätt som du gör eventuella andra virtuella Azure-datorer. Du kan säkerhetskopiera den virtuella datorn direkt från virtuella datorinställningar eller du kan aktivera säkerhetskopiering för virtuella datorer i Recovery Services-valvet.
- Du kan säkerhetskopiera virtuella datorer på hanterade diskar via RestorePoint-samlingar som är byggda ovanpå hanterade diskar.
- Azure Backup stöder också säkerhetskopiering av virtuella datorer som har krypterats med Azure Disk encryption (ADE) för hanterade diskar.

När du återställer virtuella datorer med hanterade diskar kan återställa du till en hel virtuell dator med hanterade diskar, eller till ett lagringskonto.
- Azure hanterar de hanterade diskarna under återställningsprocessen, och med alternativet för storage-konto som du hanterar lagringskontot som skapas under återställningen.
- Om du återställer en hanterad virtuell dator som är krypterad ska VM nycklar och hemligheter Avsluta i nyckelvalvet innan du startar återställningsprocessen.




## <a name="next-steps"></a>Nästa steg

- [Granska](backup-support-matrix.md) av stödmatrisen om du vill veta mer om funktioner som stöds och begränsningar för olika scenarier för säkerhetskopiering.
- Konfigurera säkerhetskopiering för ett av scenarierna:
    - [Säkerhetskopiera virtuella Azure-datorer](backup-azure-arm-vms-prepare.md)
    - [Säkerhetskopiera Windows-datorer direkt](tutorial-backup-windows-server-to-azure.md) utan en reservserver.
    - [Konfigurera MABS](backup-azure-microsoft-azure-backup.md) för säkerhetskopiering till Azure och säkerhetskopiera sedan arbetsbelastningar till MABS.
    - [Konfigurera DPM](backup-azure-dpm-introduction.md) för säkerhetskopiering till Azure och säkerhetskopiera sedan arbetsbelastningar till DPM.


[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png

