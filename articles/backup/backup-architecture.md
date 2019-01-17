---
title: Azure Backup-arkitektur
description: Översikt över arkitektur, komponenter och processer som används av Azure Backup-tjänsten.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: raynew
ms.openlocfilehash: d3e6a17ba9d0712d921d8e0a1d0bcbcd68ce5cfb
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360326"
---
# <a name="azure-backup-architecture"></a>Azure Backup-arkitektur

Du kan använda den [Azure Backup-tjänsten](backup-overview.md) att säkerhetskopiera data till Microsoft Azure-molnet. Den här artikeln sammanfattas Azure Backup-arkitektur, komponenter och processer. 


## <a name="what-does-azure-backup-do"></a>Vad är Azure Backup?

Azure Backup säkerhetskopierar data, datorn och arbetsbelastningar som körs på lokala datorer och virtuella Azure-datorer. Det finns ett antal scenarier för Azure Backup.

- **Säkerhetskopiera lokala datorer**:
    - Du kan säkerhetskopiera lokala datorer direkt till Azure med Azure Backup.
    - Du kan skydda lokala datorer med System Center Data Protection Manager (DPM) eller Microsoft Azure Backup Server (MABS) och sedan i sin tur säkerhetskopiera skyddade data på DPM/MABS till Azure med Azure Backup.
- **Säkerhetskopiera virtuella Azure-datorer**:
    - Du kan säkerhetskopiera virtuella Azure-datorer direkt med Azure Backup.
    - Du kan skydda virtuella datorer i Azure med DPM- eller MABS som körs i Azure och sedan i sin tur säkerhetskopiera skyddade data på DPM/MABS-data med Azure Backup.

Läs mer om [vad du kan säkerhetskopiera](backup-overview.md), och [stöds säkerhetskopieringsscenarier](backup-support-matrix.md).


## <a name="where-is-data-backed-up"></a>Där data säkerhetskopieras?

Azure Backup-lagrar säkerhetskopierade data i ett Recovery Services-valv. Ett valv är en onlinelagringsentitet i Azure, som används för att lagra data, som säkerhetskopior, återställningspunkter och principer för säkerhetskopiering.

- Med Recovery Services-valv är det enkelt att organisera dina säkerhetskopierade data samtidigt som du minimerar hanteringskostnaden.
- Du kan skapa upp till 500 Recovery Services-valv i varje Azure-prenumeration. 
- Du kan övervaka säkerhetskopierade objekt i ett valv, inklusive virtuella datorer i Azure och lokala datorer.
- Du kan hantera valvåtkomst med Azure [rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
- Du anger hur data i valvet replikeras för redundans:
    - **LRS**: Du kan använda lokalt redundant lagring (LRS) för att skyddar mot fel i ett datacenter. LRS replikerar data till en lagringsskalningsenhet. [Läs mer](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs).
    - **GRS**: Du kan använda geo-redundant lagring (GRS): Skyddar mot regionomfattande avbrott. Den replikerar dina data till en sekundär region. [Läs mer](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs). 
    - Som standard använder GRS i Recovery Services-valv för säkerhetskopiering. 



## <a name="how-does-azure-backup-work"></a>Hur fungerar Azure Backup?

Azure Backup kör säkerhetskopieringsjobb baserat på en standard- eller anpassad princip för säkerhetskopiering. Hur där Azure Backup gör en säkerhetskopia beror på scenariot.

**Scenario** | **Detaljer** 
--- | ---
**Direkt säkerhetskopiering av lokala datorer** | Om du vill säkerhetskopiera direkt lokala datorer, använder Azure Backup Microsoft Azure Recovery Services MARS-agenten. Agenten installeras på varje dator som du vill säkerhetskopiera. <br/><br/> Den här typen av säkerhetskopiering är inte tillgängligt för lokala Linux-datorer. 
**Direkt säkerhetskopiering av virtuella Azure-datorer** | Om du vill säkerhetskopiera virtuella Azure-datorer direkt har en Azure VM-tillägg installerats på den virtuella datorn första gången en säkerhetskopiering körs för den virtuella datorn. 
**Säkerhetskopiering av datorer och appar som skyddas av DPM- eller MABS** | I det här scenariot säkerhetskopieras machine/app först till DPM- eller MABS lokal lagring. Sedan har data i DPM/MABS säkerhetskopierats till valvet av Azure Backup. Lokala datorer skyddas av DPM/MABS som körs lokalt. Virtuella Azure-datorer kan skyddas av DPM/MABS som körs i Azure.

[Få en översikt](backup-overview.md), och se [vad stöds](backup-support-matrix.md) för varje scenario.


### <a name="backup-agents"></a>Backup-agenter

Azure Backup innehåller olika agenter, beroende på vilken typ av säkerhetskopiering.

**Agent** | **Detaljer** 
--- | --- 
**Microsoft Azure Recovery Services MARS-agenten** | Den här agenten körs på enskilda lokala Windows-servrar för säkerhetskopiering av filer, mappar och systemtillstånd<br/><br/> Den här agenten körs på DPM/MABS-servrar för säkerhetskopiering av DPM/MABS lokal Lagringsdisken. Datorer och appar som säkerhetskopieras lokalt till den här DPM/MABS-disk.
**Azure VM-tillägg** | Om du vill säkerhetskopiera virtuella Azure-datorer, läggs ett tillägg för säkerhetskopiering till Azure VM-agenten som körs på de virtuella datorerna. 


## <a name="backup-types"></a>Typer av säkerhetskopiering

**Typ av säkerhetskopiering** | **Detaljer** | **Användning**
--- | --- | ---
**Fullständig** | En säkerhetskopia innehåller hela datakällan.<br/><br/> Fullständig säkerhetskopiering tar mer bandbredd i nätverket. | Används för den första säkerhetskopieringen.
**Differentiell** |  Lagrar de block som har ändrats sedan den första fullständiga säkerhetskopieringen. Använder en mindre mängd nätverk och lagring och längre inte redundanta kopior av oförändrade data.<br/><br/> Ineffektiv eftersom datablock oförändrade mellan efterföljande säkerhetskopieringar överförs och lagras. | Används inte av Azure Backup.
**Inkrementell** | Hög effektivitet för lagring och nätverk. Lagrar endast datablock som har ändrats sedan föregående säkerhetskopia. <br/><br/> Inget behov av att med inkrementell säkerhetskopiering, det finns inget behov av att komplettera med fullständiga säkerhetskopieringar. | Används av DPM/MABS för säkerhetskopiering till disk, och i alla säkerhetskopieringar till Azure.

### <a name="comparison"></a>Jämförelse

Förbrukning av lagringsutrymme, för återställningstid (RTO) och nätverksförbrukning varierar för varje typ av säkerhetskopiering. Följande bild visar en jämförelse av säkerhetskopieringstyper.
- Datakällan som en består av 10 blockerar A1 – A10, som säkerhetskopieras varje månad.
- Block A2, A3, A4 och A9 ändras under den första månaden, och block A5 ändras nästa månad.
- För differentiella säkerhetskopieringar säkerhetskopieras i den andra månaden ändrade block A2, A3, A4 och A9. Den tredje månaden säkerhetskopieras samma block igen, tillsammans med ändrade A5-block. De ändrade blocken fortsätter att säkerhetskopieras tills nästa fullständiga säkerhetskopiering sker.
- För inkrementell säkerhetskopiering, efter att en fullständig säkerhetskopiering i den första månaden får block A2, A3, A4 och A9 markerade som ändrats och överförs till den andra månaden. I den tredje månaden markeras och överförs endast ändrade A5-block. 

![bild som visar jämförelser av metoder för säkerhetskopiering](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Backup-funktioner

I följande tabell sammanfattas funktioner för olika typer av säkerhetskopiering.

**Funktion** | **Lokala Windows-datorer (direkt)** | **Azure VMs** | **Datorer/appar med DPM/MABS**
--- | --- | --- | ---
Säkerhetskopiera till valvet | ![Ja][green] | ![Ja][green] | ![Ja][green] 
Säkerhetskopiering till disk för DPM/MABS än Azure | | | ![Ja][green] 
Komprimera data som skickas för säkerhetskopiering | ![Ja][green] | Ingen komprimering används vid överföring av data. Lagring är fylld något, men återställningen går snabbare.  | ![Ja][green] 
Köra inkrementell säkerhetskopiering |![Ja][green] |![Ja][green] |![Ja][green] 
Säkerhetskopiera deduplicerade diskar | | | ![Delvis][yellow]<br/><br/> För DPM/MABS distribueras-servrar endast lokalt. 

![tabellförklaring](./media/backup-architecture/table-key.png)


## <a name="architecture-direct-backup-of-on-premises-windows-machines"></a>Arkitektur: Direkt säkerhetskopiering av lokala Windows-datorer

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


![Säkerhetskopiering av virtuella Azure-datorer](./media/backup-architecture/architecture-azure-vm.png)


## <a name="architecture-back-up-to-dpmmabs"></a>Arkitektur: Säkerhetskopiera till DPM/MABS

1. Du installerar DPM- eller MABS-skyddsagenten på datorer som du vill skydda och lägga till datorer i en skyddsgrupp i DPM.
    - DPM- eller MABS-server måste vara finnas lokalt för att skydda lokala datorer.
    - DPM- eller MABS-server måste finnas i Azure, som körs som en Azure-dator för att skydda virtuella datorer i Azure.
    - Med hjälp av DPM/MABS kan du skydda Säkerhetskopiera volymer, resurser, filer och -mappen. Du kan skydda datorer system tillstånd/utan operativsystem och skydda specifika appar med app-anpassade inställningar för säkerhetskopiering.
2. När du ställer in skydd för en dator eller en app i DPM väljer du för att säkerhetskopiera till den lokala DPM-disken för kortsiktig lagring och till Azure (online protection). Du kan ange när säkerhetskopian till lokal DPM/MABS-lagring ska köras och när onlinesäkerhetskopiering till Azure ska köras.
3. Disken för skyddad arbetsbelastning säkerhetskopieras till de lokala DPM-diskarna och till Azure, i enlighet med det schema som du har angett.
4. Onlinesäkerhetskopiering till valvet hanteras av MARS-agenten som körs på DPM/MABS-server.

![Säkerhetskopiering av datorer/arbetsbelastningar som skyddas av DPM- eller MABS](./media/backup-architecture/architecture-dpm-mabs.png)







## <a name="azure-vm-storage"></a>Azure VM-lagring

- Virtuella Azure-datorer använder diskar för att lagra sitt operativsystem, appar och data.
- Azure virtuella datorer har minst två diskar. En för operativsystemet och en tillfällig disk. De kan också ha datadiskar för appdata. Diskarna lagras som virtuella hårddiskar.
- Virtuella hårddiskar lagras som sidblobar i standard- eller premium storage-konton i Azure.
    - Standard-lagring: Tillförlitlig, billig disksupport för virtuella datorer som kör arbetsbelastningar som inte är känslig för fördröjning. Standard-lagring kan använda standard SSD-diskar eller standard SSD-diskar).
    - Premium-lagring: Högpresterande disksupport. Använder premium SSD-diskar.
- Det finns olika prestandanivåer för diskar:
    - Standard HDD-disk: Backas upp av hårddiskar och används för kostnadseffektiv lagring.
    - Standard SSD-disk: Kombinerar elementet premium SSD-diskar och standard HDD-diskar, erbjuder mer konsekventa prestanda och tillförlitlighet än HDD, men fortfarande kostnadseffektiv.
    - Premium SSD-disk: Backas upp av SSD, med höga prestanda och låg fördröjning för virtuella datorer som kör i/o-intensiva arbetsbelastningar.
- Vara kan hanterade eller ohanterade diskar:
    - Ohanterade diskar: Traditionella typer av diskar som används av virtuella datorer. För dessa diskar, skapa ditt eget lagringskonto och ange det när du skapar disken. Du måste ta reda på hur du maximerar lagringsresurser för dina virtuella datorer.
    - Hanterade diskar: Azure hanterar skapa och hantera lagringskonton för dig. Du anger disk storlek och nivå och skapar Azure, hantera diskar för dig. Azure hanterar lagring när du lägger till diskar och skala virtuella datorer.

Läs mer:

- Mer information om disklagring för [Windows](../virtual-machines/windows/about-disks-and-vhds.md) och [Linux](../virtual-machines/linux/about-disks-and-vhds.md) virtuella datorer.
- Lär dig mer om [standard](../virtual-machines/windows/standard-storage.md) och [premium](../virtual-machines/windows/premium-storage.md) lagring.


### <a name="backing-up-and-restoring-azure-vms-with-premium-storage"></a>Säkerhetskopiera och återställa virtuella Azure-datorer med premium storage 

Du kan säkerhetskopiera virtuella Azure-datorer med premium storage med Azure Backup:

- När du säkerhetskopierar virtuella datorer med premium storage skapar Backup-tjänsten en tillfällig mellanlagringsplats med namnet ”AzureBackup-”, i lagringskontot. Storleken på mellanlagringsplatsen är lika stor som återställningspunktens ögonblicksbild.
- Kontrollera att premium storage-konto har tillräckligt med ledigt utrymme för den tillfälliga mellanlagringsplatsen får platsen. [Läs mer](../virtual-machines/windows/premium-storage.md#scalability-and-performance-targets). Ändra inte mellanlagringsplatsen.
- När säkerhetskopieringen är klar tas mellanlagringsplatsen bort.
- Priset för lagringen som används för mellanlagringsplatsen är konsekvent med [priser för premium storage](../virtual-machines/windows/premium-storage.md#pricing-and-billing).

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
    - [Säkerhetskopiera Windows-datorer direkt](tutorial-backup-windows-server-to-azure.md), utan en reservserver.
    - [Konfigurera MABS](backup-azure-microsoft-azure-backup.md) för säkerhetskopiering till Azure och sedan säkerhetskopiera arbetsbelastningar till MABS.
    - [Konfigurera DPM](backup-azure-dpm-introduction.md) för säkerhetskopiering till Azure och sedan säkerhetskopiera arbetsbelastningar till DPM.


[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png

