---
title: Översikt över arkitekturen
description: Ger en översikt över arkitektur, komponenter och processer som används av Azure Backup-tjänsten.
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: b093c6702bb26fe537622727fe1b623141bf4160
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273623"
---
# <a name="azure-backup-architecture-and-components"></a>Azure Backup arkitektur och komponenter

Du kan använda [Azure Backup-tjänsten](backup-overview.md) för att säkerhetskopiera data till Microsoft Azure-molnplattformen. Den här artikeln sammanfattar Azure Backup arkitektur, komponenter och processer.

## <a name="what-does-azure-backup-do"></a>Vad gör Azure Backup?

Azure Backup säkerhetskopierar data, datortillstånd och arbetsbelastningar som körs på lokala datorer och Azure-instanser (Virtual Machine). Det finns ett antal Azure Backup-scenarier.

## <a name="how-does-azure-backup-work"></a>Hur fungerar Azure Backup?

Du kan säkerhetskopiera datorer och data med hjälp av ett antal metoder:

- **Säkerhetskopiera lokala maskiner:**
  - Du kan säkerhetskopiera lokala Windows-datorer direkt till Azure med hjälp av AZURE Backup Microsoft Azure Recovery Services (MARS)-agenten. Linux-maskiner stöds inte.
  - Du kan säkerhetskopiera lokala datorer till en säkerhetskopieringsserver – antingen DPM (System Center Data Protection Manager) eller Microsoft Azure Backup Server (MABS). Du kan sedan säkerhetskopiera säkerhetskopieringsservern till ett Recovery Services-valv i Azure.

- **Säkerhetskopiera virtuella Azure-datorer:**
  - Du kan säkerhetskopiera virtuella Azure-datorer direkt. Azure Backup installerar ett tillägg för säkerhetskopiering till Azure VM-agenten som körs på den virtuella datorn. Det här tillägget säkerhetskopierar hela den virtuella datorn.
  - Du kan säkerhetskopiera specifika filer och mappar på den virtuella Azure-datorn genom att köra MARS-agenten.
  - Du kan säkerhetskopiera virtuella Azure-datorer till DE MABS som körs i Azure, och du kan sedan säkerhetskopiera MABS till ett Recovery Services-valv.

Läs mer om [vad du kan säkerhetskopiera](backup-overview.md) och om [säkerhetskopieringsscenarier som stöds](backup-support-matrix.md).

## <a name="where-is-data-backed-up"></a>Var säkerhetskopieras data?

Azure Backup lagrar säkerhetskopierade data i ett Recovery Services-valv. Ett valv är en enhet för onlinelagring i Azure som används för att lagra data, till exempel säkerhetskopior, återställningspunkter och principer för säkerhetskopiering.

Recovery Services-valv har följande funktioner:

- Valv gör det enkelt att ordna dina säkerhetskopierade data, samtidigt som hanteringen minimeras.
- I varje Azure-prenumeration kan du skapa upp till 500 valv.
- Du kan övervaka säkerhetskopierade objekt i ett valv, inklusive virtuella Azure-datorer och lokala datorer.
- Du kan hantera arkivåtkomst med [Azure-rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
- Du anger hur data i valvet ska replikeras för redundans:
  - **Lokalt redundant lagring (LRS):** För att skydda mot fel i ett datacenter kan du använda LRS. LRS replikerar data till en lagringsskalaenhet. [Läs mer](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs).
  - **Geo-redundant lagring (GRS):** För att skydda mot regionomfattande avbrott kan du använda GRS. GRS replikerar dina data till en sekundär region. [Läs mer](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).
  - Som standard använder Recovery Services-valv GRS.

## <a name="backup-agents"></a>Säkerhetskopieringsagenter

Azure Backup tillhandahåller olika säkerhetskopieringsagenter, beroende på vilken typ av dator som säkerhetskopieras:

**Agent** | **Detaljer**
--- | ---
**MARS-agent** | <ul><li>Körs på enskilda lokala Windows Server-datorer för att säkerhetskopiera filer, mappar och systemtillståndet.</li> <li>Körs på virtuella Azure-datorer för att säkerhetskopiera filer, mappar och systemtillståndet.</li> <li>Körs på DPM/MABS-servrar för att säkerhetskopiera DPM/MABS lokala lagringsdisken till Azure.</li></ul>
**Azure VM-tillägg** | Körs på virtuella Azure-datorer för att säkerhetskopiera dem till ett valv.

## <a name="backup-types"></a>Typer av säkerhetskopiering

I följande tabell beskrivs de olika typerna av säkerhetskopior och när de används:

**Typ av säkerhetskopiering** | **Detaljer** | **Användning**
--- | --- | ---
**Full** | En fullständig säkerhetskopia innehåller hela datakällan. Tar mer nätverksbandbredd än differentiella eller inkrementella säkerhetskopior. | Används för inledande säkerhetskopiering.
**Differentiell** |  En differentiell säkerhetskopiering lagrar blocken som har ändrats sedan den första fullständiga säkerhetskopieringen. Använder en mindre mängd nätverk och lagring och behåller inte överflödiga kopior av oförändrade data.<br/><br/> Ineffektivt eftersom datablock som är oförändrade mellan senare säkerhetskopior överförs och lagras. | Används inte av Azure Backup.
**Inkrementell** | En inkrementell säkerhetskopia lagrar bara de datablock som har ändrats sedan den föregående säkerhetskopieringen. Hög lagring och nätverkseffektivitet. <br/><br/> Med inkrementell säkerhetskopiering behöver du inte komplettera med fullständiga säkerhetskopior. | Används av DPM/MABS för säkerhetskopiering av diskar och används i alla säkerhetskopior till Azure. Används inte för säkerhetskopiering av SQL Server.

## <a name="sql-server-backup-types"></a>Säkerhetstyper för SQL Server

I följande tabell beskrivs de olika typer av säkerhetskopior som används för SQL Server-databaser och hur ofta de används:

**Typ av säkerhetskopiering** | **Detaljer** | **Användning**
--- | --- | ---
**Fullständig säkerhetskopia** | En fullständig säkerhetskopia av databas säkerhetskopierar hela databasen. Den innehåller alla data i en viss databas eller i en uppsättning filgrupper eller filer. En fullständig säkerhetskopia innehåller också tillräckligt med loggar för att återställa dessa data. | Du kan endast utlösa en fullständig säkerhetskopiering per dag.<br/><br/> Du kan välja att göra en fullständig säkerhetskopia på ett dagligt eller veckovis intervall.
**Differentiell säkerhetskopia** | En differentiell säkerhetskopiering baseras på den senaste, tidigare fullständiga säkerhetskopieringen av fullständiga data.<br/><br/> Den fångar bara de data som har ändrats sedan den fullständiga säkerhetskopian. |  Du kan endast utlösa en differentiell säkerhetskopia per dag.<br/><br/> Du kan inte konfigurera en fullständig säkerhetskopia och en differentiell säkerhetskopia samma dag.
**Säkerhetskopiering av transaktionslogg** | Med en loggsäkerhetskopia kan en återställning som baseras på tidpunkt utföras upp till en specifik sekund. | Du kan som mest konfigurera loggsäkerhetskopior var 15:e minut.

### <a name="comparison-of-backup-types"></a>Jämförelse av typer av säkerhetskopiering

Lagringsförbrukning, återställningstidsmål (RTO) och nätverksförbrukning varierar för varje typ av säkerhetskopiering. Följande bild visar en jämförelse av typerna för säkerhetskopiering:

- Datakälla A består av 10 lagringsblock, A1-A10, som säkerhetskopieras varje månad.
- Block A2, A3, A4 och A9 ändras under den första månaden, och block A5 ändras nästa månad.
- För differentiella säkerhetskopior säkerhetskopieras ändrade block A2, A3, A4 och A9 under den andra månaden. Den tredje månaden säkerhetskopieras samma block igen, tillsammans med ändrade A5-block. De ändrade blocken fortsätter att säkerhetskopieras tills nästa fullständiga säkerhetskopiering sker.
- För inkrementella säkerhetskopior markeras blocken A2, A3, A4 och A9 under den andra månaden som ändrade och överförda. I den tredje månaden markeras och överförs endast ändrade A5-block.

![Bild som visar jämförelser av säkerhetskopieringsmetoder](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Funktioner för säkerhetskopiering

I följande tabell sammanfattas de funktioner som stöds för de olika typerna av säkerhetskopiering:

**Funktion** | **Direkt säkerhetskopiering av filer och mappar (med MARS-agent)** | **Säkerhetskopiering av Azure VM** | **Maskiner eller appar med DPM/MABS**
--- | --- | --- | ---
Säkerhetskopiera till valvet | ![Ja][green] | ![Ja][green] | ![Ja][green]
Säkerhetskopiera till DPM/MABS-disk och sedan till Azure | | | ![Ja][green]
Komprimera data som skickas för säkerhetskopiering | ![Ja][green] | Ingen komprimering används vid överföring av data. Lagringen är uppblåst något, men restaureringen går snabbare.  | ![Ja][green]
Kör inkrementell säkerhetskopiering |![Ja][green] |![Ja][green] |![Ja][green]
Säkerhetskopiera deduplicated diskar | | | ![Delvis][yellow]<br/><br/> Endast för DPM/MABS-servrar som distribueras lokalt.

![Tabellnyckel](./media/backup-architecture/table-key.png)

## <a name="backup-policy-essentials"></a>Väsentliga principer för säkerhetskopieringsprincip

- En princip för säkerhetskopiering skapas per valv.
- En princip för säkerhetskopiering kan skapas för säkerhetskopiering av följande arbetsbelastningar
  - Azure VM
  - SQL i Azure VM
  - Azure-filresurs
- En princip kan tilldelas många resurser. En Azure VM-principer för säkerhetskopiering kan användas för att skydda många virtuella Azure-datorer.
- En policy består av två komponenter
  - Schema: När du ska ta säkerhetskopian
  - Kvarhållning: Hur länge varje säkerhetskopia ska behållas.
- Schema kan definieras som "dagligen" eller "veckovis" med en viss tidpunkt.
- Kvarhållning kan definieras för "dagligen", "veckovis", "månadsvis", "årliga" säkerhetskopieringspunkter.
- "varje vecka" avser en säkerhetskopia på en viss dag i veckan, "månadsvis" betyder en säkerhetskopia på en viss dag i månaden och "årlig" hänvisar till en säkerhetskopia på en viss dag på året.
- Kvarhållning för "månatliga", "årliga" säkerhetskopieringspunkter kallas "LongTermRetention".
- När ett valv skapas skapas också en princip för Azure VM-säkerhetskopior som kallas "DefaultPolicy" och kan användas för att säkerhetskopiera virtuella Azure-datorer.

## <a name="architecture-built-in-azure-vm-backup"></a>Arkitektur: Inbyggd Säkerhetskopiering av Azure VM

1. När du aktiverar säkerhetskopiering för en Virtuell Azure körs en säkerhetskopia enligt det schema du anger.
1. Under den första säkerhetskopieringen installeras ett säkerhetskopieringstillägg på den virtuella datorn om den virtuella datorn körs.
    - För virtuella Windows-datorer installeras VMSnapshot-tillägget.
    - För virtuella Linux-datorer installeras VMSnapshot Linux-tillägget.
1. Tillägget tar en ögonblicksbild på lagringsnivå.
    - För virtuella Windows-datorer som körs koordinerar säkerhetskopiering med VSS (Windows Volume Shadow Copy Service) för att ta en appkonsekvent ögonblicksbild av den virtuella datorn. Som standard tar Säkerhetskopieringen fullständiga VSS-säkerhetskopior. Om säkerhetskopiering inte kan ta en appkonsekvent ögonblicksbild, tar det en filkonsekvent ögonblicksbild.
    - För virtuella Linux-datorer tar säkerhetskopieringen en ögonblicksbild av filer. För appkonsekventa ögonblicksbilder måste du manuellt anpassa skript före/efter.
    - Säkerhetskopiering optimeras genom att säkerhetskopiera varje VM-disk parallellt. För varje disk som säkerhetskopieras läser Azure Backup blocken på disken och lagrar endast de ändrade data.
1. När ögonblicksbilden har tagits överförs data till valvet.
    - Endast datablock som har ändrats sedan den senaste säkerhetskopieringen kopieras.
    - Data är inte krypterade. Azure Backup kan säkerhetskopiera virtuella Azure-datorer som krypterats med hjälp av Azure Disk Encryption.
    - Ögonblicksbilddata kanske inte kopieras omedelbart till valvet. Vid rusningstid kan säkerhetskopieringen ta några timmar. Den totala säkerhetskopieringstiden för en virtuell dator är mindre än 24 timmar för principer för daglig säkerhetskopiering.
1. När data har skickats till valvet skapas en återställningspunkt. Som standard behålls ögonblicksbilder i två dagar innan de tas bort. Den här funktionen gör det möjligt att återställa åtgärder från dessa ögonblicksbilder, vilket minskar återställningstiderna. Det minskar den tid som krävs för att omvandla och kopiera data tillbaka från valvet. Se [Azure Backup Instant Restore Capability](https://docs.microsoft.com/azure/backup/backup-instant-restore-capability).

Du behöver inte uttryckligen tillåta internetanslutning för att säkerhetskopiera dina virtuella Azure-datorer.

![Säkerhetskopiering av virtuella Azure-datorer](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>Arkitektur: Direkt säkerhetskopiering av lokala Windows Server-datorer eller Azure VM-filer eller -mappar

1. Om du vill konfigurera scenariot hämtar och installerar du MARS-agenten på datorn. Du väljer sedan vad du vill säkerhetskopiera, när säkerhetskopieringar ska köras och hur länge de ska behållas i Azure.
1. Den första säkerhetskopian körs enligt dina inställningar för säkerhetskopiering.
1. MARS-agenten använder VSS för att ta en ögonblicksbild av de volymer som valts för säkerhetskopiering.
    - MARS-agenten använder bara Windows-systemskrivningsåtgärden för att fånga ögonblicksbilden.
    - Eftersom agenten inte använder något vss-program fångar den inte appkonsekventa ögonblicksbilder.
1. När du har tagit ögonblicksbilden med VSS skapar MARS-agenten en virtuell hårddisk (VHD) i den cachemapp som du angav när du konfigurerade säkerhetskopian. Agenten lagrar också kontrollsummar för varje datablock.
1. Inkrementella säkerhetskopior körs enligt det schema du anger, såvida du inte kör en säkerhetskopiering på begäran.
1. I inkrementella säkerhetskopior identifieras ändrade filer och en ny virtuell hårddisk skapas. Den virtuella hårddisken komprimeras och krypteras och skickas sedan till valvet.
1. När den inkrementella säkerhetskopian är klar slås den nya virtuella hårddisken samman med den virtuella hårddisk som skapats efter den första replikeringen. Den här sammanslagna virtuella hårddisken ger det senaste tillståndet som ska användas för jämförelse för pågående säkerhetskopiering.

![Säkerhetskopiering av lokala Windows Server-datorer med MARS-agent](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Arkitektur: Säkerhetskopiera till DPM/MABS

1. Du installerar DPM- eller MABS-skyddsagenten på datorer som du vill skydda. Du lägger sedan till datorerna i en DPM-skyddsgrupp.
    - För att skydda lokala datorer måste DPM- eller MABS-servern finnas lokalt.
    - För att skydda virtuella Azure-datorer måste MABS-servern finnas i Azure och köras som en virtuell Azure-dator.
    - Med DPM/MABS kan du skydda säkerhetskopieringsvolymer, resurser, filer och mappar. Du kan också skydda en dators systemtillstånd (bare metal) och du kan skydda specifika appar med appmedvetna säkerhetskopieringsinställningar.
1. När du ställer in skydd för en dator eller app i DPM/MABS väljer du att säkerhetskopiera till den lokala disken MABS/DPM för korttidslagring och till Azure för onlineskydd. Du anger också när säkerhetskopieringen till lokal DPM/MABS-lagring ska köras och när säkerhetskopieringen online till Azure ska köras.
1. Disken för den skyddade arbetsbelastningen säkerhetskopieras till de lokala MABS/DPM-diskarna, enligt det schema du angav.
1. DPM/MABS-diskarna säkerhetskopieras till valvet av MARS-agenten som körs på DPM/MABS-servern.

![Säkerhetskopiering av datorer och arbetsbelastningar som skyddas av DPM eller MABS](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Lagring av virtuella Azure-datorer

Virtuella Azure-datorer använder diskar för att lagra sina operativsystem, appar och data. Varje Azure VM har minst två diskar: en disk för operativsystemet och en tillfällig disk. Virtuella Azure-datorer kan också ha datadiskar för appdata. Diskar lagras som virtuella hårddiskar.

- Virtuella hårddiskar lagras som sidblobar i standard- eller premiumlagringskonton i Azure:
  - **Standardlagring:** Tillförlitligt diskstöd till låg kostnad för virtuella datorer som kör arbetsbelastningar som inte är känsliga för svarstid. Standardlagring kan använda SSD-diskar (Standardminnesdiskar) eller hdd-diskar (standard hårddisk).
  - **Premium-lagring:** Stöd för högpresterande disk. Använder premium-SSD-diskar.
- Det finns olika prestandanivåer för diskar:
  - **Standard HDD-disk:** Uppbackad av hårddiskar och används för kostnadseffektiv lagring.
  - **Standard SSD-disk:** Kombinerar element av premium SSD-diskar och vanliga hårddiskar. Erbjuder mer konsekvent prestanda och tillförlitlighet än hårddisk, men ändå kostnadseffektivt.
  - **Premium SSD-disk:** Backas upp av SSD:er och ger hög prestanda och låg latens för virtuella datorer som kör I/O-intensiva arbetsbelastningar.
- Diskar kan hanteras eller ohanteras:
  - **Ohanterat diskar:** Traditionell typ av diskar som används av virtuella datorer. För dessa diskar skapar du ett eget lagringskonto och anger det när du skapar disken. Du måste sedan ta reda på hur du maximerar lagringsresurserna för dina virtuella datorer.
  - **Hanterade diskar:** Azure skapar och hanterar lagringskonton för dig. Du anger diskstorlek och prestandanivå och Azure skapar hanterade diskar åt dig. När du lägger till diskar och skalar virtuella datorer hanterar Azure lagringskontona.

Mer information om disklagring och tillgängliga disktyper för virtuella datorer finns i följande artiklar:

- [Azure-hanterade diskar för virtuella Windows-datorer](../virtual-machines/windows/managed-disks-overview.md)
- [Azure-hanterade diskar för virtuella Linux-datorer](../virtual-machines/linux/managed-disks-overview.md)
- [Tillgängliga disktyper för virtuella datorer](../virtual-machines/windows/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Säkerhetskopiera och återställa virtuella Azure-datorer med premiumlagring

Du kan säkerhetskopiera virtuella Azure-datorer med hjälp av premiumlagring med Azure Backup:

- Under säkerhetskopieringen av virtuella datorer med premiumlagring skapar säkerhetskopieringstjänsten en tillfällig mellanlagringsplats med namnet *AzureBackup-* i lagringskontot. Storleken på mellanlagringsplatsen är lika med storleken på ögonblicksbilden av återställningspunkten.
- Kontrollera att premiumlagringskontot har tillräckligt med ledigt utrymme för att rymma den tillfälliga mellanlagringsplatsen. Mer information finns i [Skalbarhetsmål för premiumsida blob storage-konton](../storage/blobs/scalability-targets-premium-page-blobs.md). Ändra inte mellanlagringsplatsen.
- När säkerhetskopieringsjobbet är klart tas mellanlagringsplatsen bort.
- Priset på lagring som används för mellanlagringsplatsen är förenligt med [prissättning för premiumlagring.](../virtual-machines/windows/disks-types.md#billing)

När du återställer virtuella Azure-datorer med hjälp av premiumlagring kan du återställa dem till premium- eller standardlagring. Vanligtvis återställer du dem till premiumlagring. Men om du bara behöver en delmängd av filer från den virtuella datorn kan det vara kostnadseffektivt att återställa dem till standardlagring.

### <a name="back-up-and-restore-managed-disks"></a>Säkerhetskopiera och återställa hanterade diskar

Du kan säkerhetskopiera virtuella Azure-datorer med hanterade diskar:

- Du säkerhetskopierar virtuella datorer med hanterade diskar på samma sätt som du gör någon annan Azure VM. Du kan säkerhetskopiera den virtuella datorn direkt från inställningarna för den virtuella datorn eller aktivera säkerhetskopiering för virtuella datorer i valvet för Återställningstjänster.
- Du kan säkerhetskopiera virtuella datorer på hanterade diskar via RestorePoint-samlingar som är byggda ovanpå hanterade diskar.
- Azure Backup stöder också säkerhetskopiering av virtuella datorer med hanterade diskar som krypterades med hjälp av Azure Disk Encryption.

När du återställer virtuella datorer med hanterade diskar kan du återställa till en komplett virtuell dator med hanterade diskar eller till ett lagringskonto:

- Under återställningsprocessen hanterar Azure de hanterade diskarna. Om du använder alternativet lagringskonto hanterar du lagringskontot som skapas under återställningsprocessen.
- Om du återställer en hanterad virtuell dator som är krypterad kontrollerar du att den virtuella datorns nycklar och hemligheter finns i nyckelvalvet innan du startar återställningsprocessen.

## <a name="next-steps"></a>Nästa steg

- Granska supportmatrisen om du vill [veta mer om funktioner och begränsningar som stöds för scenarier för säkerhetskopiering](backup-support-matrix.md).
- Konfigurera säkerhetskopiering för något av följande scenarion:
  - [Säkerhetskopiera virtuella Azure-datorer](backup-azure-arm-vms-prepare.md).
  - [Säkerhetskopiera Windows-datorer direkt](tutorial-backup-windows-server-to-azure.md) utan en reservserver.
  - [Konfigurera MABS](backup-azure-microsoft-azure-backup.md) för säkerhetskopiering till Azure och säkerhetskopiera sedan arbetsbelastningar till MABS.
  - [Konfigurera DPM](backup-azure-dpm-introduction.md) för säkerhetskopiering till Azure och säkerhetskopiera sedan arbetsbelastningar till DPM.

[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png
