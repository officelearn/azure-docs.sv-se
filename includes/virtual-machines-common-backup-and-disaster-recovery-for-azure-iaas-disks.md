---
title: ta med fil
description: ta med fil
services: storage
author: luywang
ms.service: storage
ms.topic: include
ms.date: 06/05/2018
ms.author: luywang
ms.custom: include file
ms.openlocfilehash: 59a04ca5a546fff1043e1e157491b218f693d0f0
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2019
ms.locfileid: "56333824"
---
# <a name="backup-and-disaster-recovery-for-azure-iaas-disks"></a>Återställning för säkerhetskopiering och haveriberedskap för Azure IaaS-diskar

Den här artikeln beskriver hur du planerar för säkerhetskopiering och haveriberedskap (DR) av IaaS-datorer (VM) och diskar i Azure. Det här dokumentet omfattar både hanterade och ohanterade diskar.

Först måste beskriver vi de inbyggda feltolerans i Azure-plattformen som skyddar mot lokala fel. Sedan tar vi upp katastrofscenarier som inte omfattas av de inbyggda funktionerna. Vi visar också flera exempel på arbetsbelastningsscenarier där olika säkerhetskopiering och Katastrofåterställning överväganden kan använda. Vi granskar möjliga lösningar för Haveriberedskap för IaaS-diskar.

## <a name="introduction"></a>Introduktion

Azure-plattformen använder olika metoder för redundans och feltolerans för att skydda kunder mot lokaliserade maskinvarufel. Lokala fel kan innehålla problem med en Azure Storage server-datorn på den servern som innehåller en del av data för en virtuell disk eller fel i en SSD och HDD. Sådana isolerade komponenten maskinvarufel kan inträffa under normal drift.

Azure-plattformen är utformad för att vara motståndskraftiga mot de här felen. Större katastrofer kan resultera i fel eller inaccessibility av många lagringsservrar eller även en hela datacenter. Även om dina virtuella datorer och diskar skyddas vanligtvis vid lokaliserade fel, krävs ytterligare steg för att skydda din arbetsbelastning från regionomfattande oåterkalleligt fel, till exempel en större katastrof som kan påverka dina virtuella datorer och diskar.

Förutom om risken för plattformen fel kan uppstå problem med en kundprogram eller data. Exempelvis kan en ny version av ditt program oavsiktligt gör en ändring i de data som orsakar den att avbryta. I så fall kanske du vill återställa programmet och data till en tidigare version som innehåller den senast kända statusen. Detta kräver att underhålla regelbundna säkerhetskopieringar.

För regional haveriberedskap, måste du säkerhetskopiera dina IaaS VM-diskar till en annan region.

Innan vi tittar på säkerhetskopiering och Katastrofåterställning alternativ, vi återblick över några metoder för hantering av lokaliserade fel.

### <a name="azure-iaas-resiliency"></a>Azure IaaS-återhämtningskapacitet

*Återhämtning* avser toleransen för vanliga fel som inträffar i maskinvarukomponenter. Elasticitet är möjligheten att återställa från fel och fortsätta att fungera. Det handlar inte om undvika fel, men svarar på fel i ett sätt som förhindrar avbrott eller dataförluster. Målet med elasticiteten är att återställa programmet till ett fullt fungerande tillstånd efter fel. Azure-datorer och diskar är utformade för att vara motståndskraftiga mot vanliga maskinvarufel. Nu ska vi titta på hur Azure IaaS-plattformen ger den här återhämtning.

En virtuell dator består främst av två delar: en server för beräkning och beständiga diskar. Båda påverkar feltolerans för en virtuell dator.

Om Azure compute-värdservern där den virtuella datorn uppstår ett maskinvarufel som är sällsynt, har Azure utformats att automatiskt återställa den virtuella datorn på en annan server. Om det här scenariot, din datorn startas om och den virtuella datorn startas igen efter en stund. Azure automatiskt identifierar sådana maskinvarufel och kör återställningar för att säkerställa kunden virtuell dator är tillgänglig så snart som möjligt.

Tillförlitlighet gällande data är viktigt för en plattform för beständig lagring om IaaS-diskar. Azure-kunder har viktiga program som körs på IaaS och de är beroende av data är permanenta. Azure Designer skydd för dessa IaaS-diskar med tre redundanta kopior av data som lagras lokalt. Dessa kopior a. för hög tålighet mot lokala fel Om en av de maskinvarukomponenter som innehåller din disk misslyckas påverkas inte den virtuella datorn, eftersom det finns två ytterligare kopior på disk supportförfrågningar. Det fungerar bra, även om två olika maskinvarukomponenter som har stöd för en disk går ned på samma gång (vilket är ovanligt). 

Om du vill se till att du alltid har tre repliker, Azure Storage automatiskt skapas en ny kopia av data i bakgrunden om någon av tre kopierar blir otillgänglig. Därför bör det inte nödvändigt att använda RAID med Azure-diskar för feltolerans. En enkel RAID 0 konfiguration är tillräckliga för Strimling diskar, om det behövs för att skapa större volymer.

På grund av den här arkitekturen har Azure konsekvent levereras företagsklass hållbarhet för IaaS-diskar, med branschledande noll procent [årlig Felfrekvens](https://en.wikipedia.org/wiki/Annualized_failure_rate).

Lokaliserade maskinvarufel i beräkningen som värd för eller i Storage-plattformen kan ibland leda till den virtuella datorn som omfattas av är tillfälligt otillgänglig i [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) för VM-tillgänglighet. Azure tillhandahåller även ett branschledande serviceavtal för enskilda VM-instanser som använder Azure premium SSD.

Kunder kan använda för att skydda arbetsbelastningar för program från stilleståndstid på grund av en disk eller en virtuell dator är tillfälligt otillgänglig, [tillgänglighetsuppsättningar](../articles/virtual-machines/windows/manage-availability.md). Två eller flera virtuella datorer i en tillgänglighetsuppsättning tillhandahålla redundans för programmet. Azure skapar sedan dessa virtuella datorer och diskar i separata feldomäner med olika ström, nätverk och server-komponenter.

På grund av de här separata feldomäner påverkar lokaliserade maskinvarufel vanligtvis inte flera virtuella datorer i uppsättningen på samma gång. Att ha separata feldomäner ger hög tillgänglighet för ditt program. Den betraktas som en bra idé att Använd tillgänglighetsuppsättningar när hög tillgänglighet krävs. Nästa avsnitt beskriver disaster recovery-aspekt.

### <a name="backup-and-disaster-recovery"></a>Säkerhetskopiering och haveriberedskap

Katastrofåterställning är möjligheten att återställa från sällsynta, större, incidenter. De här incidenterna omfattar längre, storskaliga fel, till exempel avbrott i tjänsten som påverkar en hel region. Haveriberedskap involverar säkerhetskopiering och arkivering och kan behövas manuella ingripanden, till exempel att återställa en databas från en säkerhetskopia.

På Azure-plattformen inbyggt skydd mot lokaliserade fel kanske inte helt skydda virtuella datorer/diskar om en större katastrof orsakar storskaliga avbrott. Dessa storskaliga avbrott inkluderar katastrofartade händelser, till exempel om ett datacenter uppnås genom en orkan, jordbävning, fire, eller om det finns en enhet för storskaliga maskinvarufel. Dessutom kan uppstå det fel som beror på programmet eller problem med.

För att skydda dina IaaS-arbetsbelastningar från avbrott, bör du planera för redundans och har säkerhetskopieringar att aktivera återställning. För haveriberedskap, bör du säkerhetskopiera på en annan geografisk plats från den primära platsen. Den här metoden hjälper till att säkerställa att säkerhetskopieringen inte påverkas av samma händelse som ursprungligen påverkas av den virtuella datorn eller diskar. Mer information finns i [haveriberedskap för Azure-program](/azure/architecture/resiliency/disaster-recovery-azure-applications).

DR-överväganden kan innehålla följande aspekter:

- Hög tillgänglighet: Möjligheten för programmet att fortsätta köras i ett felfritt tillstånd utan betydande driftavbrott. Genom att *felfritt tillstånd*, det här tillståndet innebär att programmet svarar och användare kan ansluta till programmet och interagera med den. Vissa verksamhetskritiska program och databaser kan bli ombedd att alltid är tillgänglig, även om det uppstår fel på plattformen. Du kan behöva planera redundans för programmet, samt data för dessa arbetsbelastningar.

- Tillförlitlighet: I vissa fall kan den huvudsakliga överväganden är att se till att data går förlorade om en katastrof inträffar. Därför kanske du behöver en säkerhetskopia av dina data i en annan plats. Du kanske inte behöver fullständig redundans för programmet, men bara en vanlig säkerhetskopiering av diskar för dessa arbetsbelastningar.

## <a name="backup-and-dr-scenarios"></a>Scenarier för säkerhetskopiering och Haveriberedskap

Nu ska vi titta på några vanliga exempel på arbetsbelastning Programscenarier och överväganden för att planera för katastrofåterställning.

### <a name="scenario-1-major-database-solutions"></a>Scenario 1: Större databaslösningar

Tänk dig en databasserver för produktion, som SQL Server- eller Oracle, som har stöd för hög tillgänglighet. Produktionsprogram och användare är beroende av den här databasen. Återställningsplanen för det här systemet kan behöva stöd för följande krav:

- Data måste vara skyddade och återställa.
- Servern måste vara tillgängliga för användning.

Återställningsplanen kan kräva att underhålla en replik av databasen i en annan region som en säkerhetskopia. Beroende på kraven för servertillgänglighet och återställning av data kan lösningen vara från en aktiv-aktiv eller aktiv-passiv replikeringsplatsen och periodiska offline säkerhetskopiering av data. Relationsdatabaser som SQL Server- och Oracle, tillhandahåller olika alternativ för replikering. SQL Server, använder [SQL Server AlwaysOn Availability Groups](https://msdn.microsoft.com/library/hh510230.aspx) för hög tillgänglighet.

Även stöd för NoSQL-databaser, mongodb, [repliker](https://docs.mongodb.com/manual/replication/) för redundans. Repliker för hög tillgänglighet används.

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>Scenario 2: Ett kluster med redundanta virtuella datorer

Överväg att en arbetsbelastning som hanteras av ett kluster av virtuella datorer som ger redundans och belastningsutjämning. Ett exempel är ett Cassandra-kluster som distribueras i en region. Den här typen av arkitektur innehåller redan en hög nivå av redundans i den regionen. Men om du vill skydda arbetsbelastningen från en regional nivå-fel, bör du sprida klustret mellan två regioner eller för att utföra regelbundna säkerhetskopieringar till en annan region.

### <a name="scenario-3-iaas-application-workload"></a>Scenario 3: IaaS arbetsbelastning

Låt oss titta på IaaS programbelastningen. Det här programmet kan till exempel vara en typisk produktionsarbetsbelastning körs på en virtuell Azure-dator. Det kan vara en webbserver eller filserver som innehåller innehållet och andra resurser för en plats. Det kan också vara ett specialbyggt affärsprogram som körs på en virtuell dator som lagras dess data, resurser och programmets tillstånd på VM-diskarna. I det här fallet är det viktigt att se till att säkerhetskopiera med jämna mellanrum. Säkerhetskopieringsfrekvens ska baseras på typen av VM-arbetsbelastning. Till exempel om programmet körs varje dag och ändrar data, vidtas sedan säkerhetskopieringen för varje timme.

Ett annat exempel är en rapportserver som hämtar data från andra källor och genererar aggregerade rapporter. Förlusten av den här virtuella datorn eller diskar kan leda till förlust av rapporterna. Dock kan det vara möjligt att köra rapporterna och återskapa utdata. I så fall kan behöver du verkligen en förlust av data, även om rapportservern uppnås med en katastrof. Därför kan du ha en högre nivå av feltolerans för att förlora en del av data på rapportservern. I så fall kan är säkerhetskopiera mindre ofta ett alternativ för att minska kostnaderna.

### <a name="scenario-4-iaas-application-data-issues"></a>Scenario 4: Problem med IaaS-program

Problem med IaaS-program är en annan möjlighet. Tänk dig ett program som beräknar, underhåller och levererar viktiga affärsdata, till exempel information om priser. En ny version av programmet hade en programvara-bugg som felaktigt beräknas priserna och skadad befintliga commerce-data som hanteras av plattformen. Här är bästa åtgärden att återgå till den tidigare versionen av programmet och data. Du aktiverar det genom att ta regelbundna säkerhetskopieringar av systemet.

## <a name="disaster-recovery-solution-azure-backup"></a>Lösning för haveriberedskap: Azure Backup 

[Azure Backup](https://azure.microsoft.com/services/backup/) används för säkerhetskopiering och Katastrofåterställning och de fungerar med [hanterade diskar](../articles/virtual-machines/windows/managed-disks-overview.md) samt ohanterade diskar. Du kan skapa ett säkerhetskopieringsjobb med tidsbaserade säkerhetskopior, enkel återställning av virtuell dator och lagringsprinciper för säkerhetskopiering.

Om du använder [premium SSD](../articles/virtual-machines/windows/disks-types.md), [hanterade diskar](../articles/virtual-machines/windows/managed-disks-overview.md), eller andra disktyper med den [lokalt redundant lagring](../articles/storage/common/storage-redundancy-lrs.md) är särskilt viktigt att säkerhetskopiera periodiska DR. Azure Backup lagrar data i recovery services-valv för långsiktig kvarhållning. Välj den [geo-redundant lagring](../articles/storage/common/storage-redundancy-grs.md) alternativ för backup recovery services-valv. Det alternativet säkerställer att säkerhetskopieringarna replikeras till en annan Azure-region för att skydda från regionala katastrofer.

För ohanterade diskar kan du använda lokalt redundant lagring-typ för IaaS-diskar, men kontrollera att Azure Backup är aktiverat med alternativet geo-redundant lagring för recovery services-valvet.

> [!NOTE]
> Om du använder den [geo-redundant lagring](../articles/storage/common/storage-redundancy-grs.md) eller [läsåtkomst till geografiskt redundant lagring](../articles/storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) alternativet för ohanterade diskar kan du fortfarande behöver programkonsekventa ögonblicksbilder för säkerhetskopiering och Katastrofåterställning. Använd antingen [Azure Backup](https://azure.microsoft.com/services/backup/) eller [programkonsekventa ögonblicksbilder](#alternative-solution-consistent-snapshots).

 I följande tabell är en sammanfattning av lösningarna som är tillgängliga för Haveriberedskap.

| Scenario | Automatisk replikering | DR-lösning |
| --- | --- | --- |
| Premium SSD-diskar | Lokal ([lokalt redundant lagring](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Hanterade diskar | Lokal ([lokalt redundant lagring](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Ohanterade diskar i lokalt redundant lagring | Lokal ([lokalt redundant lagring](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Geo-redundant lagring med ohanterade diskar | Mellan regioner ([geo-redundant lagring](../articles/storage/common/storage-redundancy-grs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Programkonsekventa ögonblicksbilder](#alternative-solution-consistent-snapshots) |
| Ohanterade läsåtkomst till geografiskt redundant lagringsdiskar | Mellan regioner ([läsåtkomst till geografiskt redundant lagring](../articles/storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Programkonsekventa ögonblicksbilder](#alternative-solution-consistent-snapshots) |

Hög tillgänglighet uppfylls bäst med hjälp av hanterade diskar i en tillgänglighetsuppsättning tillsammans med Azure Backup. Om du använder ohanterade diskar, kan du fortfarande använda Azure Backup för Katastrofåterställning. Om det inte går att använda Azure Backup kan sedan vidta [programkonsekventa ögonblicksbilder](#alternative-solution-consistent-snapshots), enligt beskrivningen i ett senare avsnitt är en alternativ lösning för säkerhetskopiering och Katastrofåterställning.

Dina val för hög tillgänglighet, säkerhetskopiering och Katastrofåterställning på program- eller infrastruktur kan visas på följande sätt:

| Nivå |   Hög tillgänglighet   | Säkerhetskopiering eller DR |
| --- | --- | --- |
| Program | SQL Server AlwaysOn | Azure Backup |
| Infrastruktur    | Tillgänglighetsuppsättning  | GEO-redundant lagring med programkonsekventa ögonblicksbilder |

### <a name="using-azure-backup"></a>Med hjälp av Azure Backup 

[Azure Backup](../articles/backup/backup-azure-vms-introduction.md) kan säkerhetskopiera dina virtuella datorer som kör Windows eller Linux till Azure recovery services-valv. Säkerhetskopiera och återställa verksamhetskritiska data är komplicerat genom att verksamhetskritiska data måste säkerhetskopieras medan de program som producerar data körs. 

För det här problemet, innehåller Azure Backup programkonsekventa säkerhetskopior för Microsoft-arbetsbelastningar. Tjänsten volume shadow används för att se till att data skrivs korrekt till lagring. För virtuella Linux-datorer kan endast filkonsekvent säkerhetskopiering är möjligt, eftersom Linux inte har funktioner som motsvarar tjänsten volume shadow.

![Azure Backup-flöde][1]

När Azure Backup initierar en säkerhetskopiering på den schemalagda tiden, utlöser säkerhetskopieringstillägget installeras på den virtuella datorn att ta en ögonblicksbild för point-in-time. En ögonblicksbild tas tillsammans med tjänsten volume shadow för att hämta en konsekvent ögonblicksbild av diskarna i den virtuella datorn utan att behöva stänga av. Säkerhetskopieringstillägget på den virtuella datorn tömmer alla skrivningar innan du tar en konsekvent ögonblicksbild av alla diskar. Efter att ögonblicksbilden överförs data med Azure Backup till säkerhetskopieringsvalvet. Om du vill att säkerhetskopieringsprocessen effektivare tjänsten identifierar och överför endast datablock som har ändrats efter den senaste säkerhetskopieringen.

Om du vill återställa, kan du visa tillgängliga säkerhetskopior via Azure Backup och startar sedan en återställning. Du kan skapa och återställa säkerhetskopior i Azure med den [Azure-portalen](https://portal.azure.com/), genom [med hjälp av PowerShell](../articles/backup/backup-azure-vms-automation.md), eller genom att använda den [Azure CLI](/cli/azure/).

### <a name="steps-to-enable-a-backup"></a>Steg för att aktivera en säkerhetskopia

Använd följande steg för att aktivera säkerhetskopiering för dina virtuella datorer med hjälp av den [Azure-portalen](https://portal.azure.com/). Det finns en variant beroende på just ditt scenario. Referera till den [Azure Backup](../articles/backup/backup-azure-vms-introduction.md) dokumentationen för fullständig information. Azure säkerhetskopiering också [stöder virtuella datorer med hanterade diskar](https://azure.microsoft.com/blog/azure-managed-disk-backup/).

1.  Skapa ett recovery services-valv för en virtuell dator:

    a. I den [Azure-portalen](https://portal.azure.com/), Bläddra **alla resurser** och hitta **Recovery Services-valv**.

    b. På den **Recovery Services-valv** -menyn klickar du på **Lägg till** och följ stegen för att skapa ett nytt valv i samma region som den virtuella datorn. Om den virtuella datorn är i regionen USA, västra, väljer du exempelvis USA, västra för valvet.

1.  Kontrollera lagringsreplikering för valvet du skapade. Komma åt valvet under **Recovery Services-valv** och gå till **inställningar** > **Säkerhetskopieringskonfigurationen**. Se till att den **geo-redundant lagring** alternativet är markerat som standard. Det här alternativet innebär att ditt valv replikeras automatiskt till ett sekundärt datacenter. Exempelvis replikeras automatiskt ditt valv i västra USA till östra USA.

1.  Konfigurera principen för säkerhetskopiering och välj den virtuella datorn från samma användargränssnitt.

1.  Kontrollera att Backup-agenten är installerad på den virtuella datorn. Om den virtuella datorn skapas med hjälp av en Azure-galleriet bilden, installerat Backup-agenten redan. Övrigt (det vill säga om du använder en anpassad avbildning), följ instruktionerna till [installera VM-agenten på en virtuell dator](../articles/backup/backup-azure-arm-vms-prepare.md#install-the-vm-agent).

1.  Kontrollera att den virtuella datorn tillåter nätverksanslutningar för tjänsten backup ska fungera. Följ anvisningarna för [nätverksanslutningar](../articles/backup/backup-azure-arm-vms-prepare.md#establish-network-connectivity).

1.  När de föregående stegen har slutförts, körs säkerhetskopieringen med jämna mellanrum enligt vad som anges i principen för säkerhetskopiering. Om det behövs kan du utlösa den första säkerhetskopieringen manuellt från instrumentpanelen för valvet på Azure portal.

För att automatisera Azure Backup med hjälp av skript, referera till [PowerShell-cmdletar för säkerhetskopiering av virtuella datorer](../articles/backup/backup-azure-vms-automation.md).

### <a name="steps-for-recovery"></a>Steg för återställning

Om du vill reparera eller återskapa en virtuell dator kan återställa du den virtuella datorn från någon av återställningspunkter för säkerhetskopiering i valvet. Det finns ett par olika alternativ för att utföra återställningen:

-   Du kan skapa en ny virtuell dator som en point-in-time-representation av den säkerhetskopierade virtuella datorn.

-   Du kan återställa diskarna och sedan använda mallen för den virtuella datorn för att anpassa och återskapa den återställda virtuella datorn.

Mer information finns i instruktionerna för att [återställa virtuella datorer med hjälp av Azure portal](../articles/backup/backup-azure-arm-restore-vms.md). Det här dokumentet beskriver även vilka specifika åtgärder för att återställa säkerhetskopierade virtuella datorer till ett parat datacenter med hjälp av geo-redundant säkerhetskopieringsvalvet om det finns en katastrof i det primära datacentret. Azure Backup använder i så fall beräkningstjänsten från den sekundära regionen för att skapa den återställda virtuella datorn.

Du kan också använda PowerShell för [återställer en virtuell dator](../articles/backup/backup-azure-arm-restore-vms.md#restore-a-vm-during-an-azure-datacenter-disaster) eller för [skapar en ny virtuell dator från återställs diskar](../articles/backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="alternative-solution-consistent-snapshots"></a>Alternativ lösning: Programkonsekventa ögonblicksbilder

Om det inte går att använda Azure Backup kan implementera du en egen mekanism för säkerhetskopiering med ögonblicksbilder. Det är komplicerat att skapa programkonsekventa ögonblicksbilder för alla diskar som används av en virtuell dator och replikerar sedan dessa ögonblicksbilder till en annan region. Därför överväger Azure med Backup-tjänsten som ett bättre alternativ än att skapa en anpassad lösning.

Om du använder läsåtkomst till geografiskt redundant lagring/geo-redundant lagring för diskar, replikeras ögonblicksbilder automatiskt till ett sekundärt datacenter. Om du använder lokalt redundant lagring för diskar, måste du replikerar data själv. Mer information finns i [säkerhetskopiera Azure-ohanterade Virtuella datordiskar med inkrementella ögonblicksbilder](../articles/virtual-machines/windows/incremental-snapshots.md).

En ögonblicksbild är en representation av ett objekt vid en viss tidpunkt. En ögonblicksbild är fri från fakturering för inkrementell storleken på data den innehåller. Mer information finns i [skapa en blobögonblicksbild](../articles/storage/blobs/storage-blob-snapshots.md).

### <a name="create-snapshots-while-the-vm-is-running"></a>Skapa ögonblicksbilder medan Virtuellt datorn körs

Du kan ta en ögonblicksbild när som helst, om Virtuellt datorn körs, finns det fortfarande data som strömmas till diskarna. Ögonblicksbilder kan innehålla delvis åtgärder som rör sig. Även om det finns flera diskar, kan ögonblicksbilder av olika diskar ha inträffat vid olika tidpunkter. Dessa scenarier kan orsaka att ögonblicksbilderna vara ej samordnad. Bristen på samordning är särskilt problematiska för stripe-volymer vars filer kan vara skadad om ändringar som gjordes under säkerhetskopieringen.

Om du vill undvika detta måste säkerhetskopieringen implementera följande steg:

1.  Låsa alla diskar.

1.  Rensa alla väntande skrivningar.

1.  [Skapa en blobögonblicksbild](../articles/storage/blobs/storage-blob-snapshots.md) för alla diskar.

Vissa Windows-program, som SQL Server, ange en samordnad mekanism för säkerhetskopiering via en tjänsten volume shadow skapa programkonsekventa säkerhetskopior. På Linux, kan du använda ett verktyg som *fsfreeze* för att samordna diskarna. Det här verktyget förser filkonsekvent säkerhetskopiering, men inte programkonsekventa ögonblicksbilder. Den här processen är komplexa, så du bör använda [Azure Backup](../articles/backup/backup-azure-vms-introduction.md) eller en lösning för säkerhetskopiering från tredje part som redan implementerar den här proceduren.

Föregående processen resulterar i en samling för samordnad ögonblicksbilder för alla Virtuella diskar, som representerar en specifik point-in-time-vy av den virtuella datorn. Det här är en återställningspunkt för säkerhetskopiering för den virtuella datorn. Du kan upprepa processen med schemalagda intervall för att skapa regelbundna säkerhetskopieringar. Se [kopiera säkerhetskopior av till en annan region](#copy-the-snapshots-to-another-region) anvisningar för hur du kopierar ögonblicksbilderna till en annan region för Haveriberedskap.

### <a name="create-snapshots-while-the-vm-is-offline"></a>Skapa ögonblicksbilder medan den virtuella datorn är offline

Ett annat alternativ att skapa programkonsekventa säkerhetskopior är att Stäng av den virtuella datorn och ta blobögonblicksbilder av varje disk. Med blob-ögonblicksbilder är enklare än att samordna ögonblicksbilder av en aktiv virtuell dator, men det krävs ett par minuter stillestånd.

1. Stäng av den virtuella datorn.

1. Skapa en ögonblicksbild av varje virtuell hårddisk-bloben, som tar bara några sekunder.

    Du kan använda för att skapa en ögonblicksbild, [PowerShell](../articles/storage/common/storage-powershell-guide-full.md), [Azure Storage REST API](https://msdn.microsoft.com/library/azure/ee691971.aspx), [Azure CLI](/cli/azure/), eller någon av de Azure Storage-klientbibliotek som [den Storage-klientbiblioteket för .NET](https://msdn.microsoft.com/library/azure/hh488361.aspx).

1. Starta den virtuella datorn, som slutar stilleståndstid. Normalt hela processen har slutförts inom några minuter.

Den här processen ger en uppsättning programkonsekventa ögonblicksbilder för alla diskar, vilket ger en återställningspunkt för säkerhetskopiering för den virtuella datorn.

### <a name="copy-the-snapshots-to-another-region"></a>Kopiera ögonblicksbilderna till en annan region

Skapandet av ögonblicksbilder enbart kanske inte är tillräckligt för Katastrofåterställning. Du måste också replikera säkerhetskopior av filögonblicksbilder till en annan region.

Om du använder geo-redundant lagring eller read-access geo-redundant lagring för dina diskar och ögonblicksbilder replikeras automatiskt till den sekundära regionen. Det kan finnas ett par minuters fördröjning innan replikeringen. Om det primära datacentret kraschar innan ögonblicksbilderna Slutför replikerar du kan inte få åtkomst till ögonblicksbilder från det sekundära datacentret. Sannolikheten för det här är liten.

> [!NOTE]
> Endast med diskarna i en geo-redundant lagring eller read-access geo redundant skyddar inte den virtuella datorn från katastrofer i storage-konto. Du måste också skapa samordnad ögonblicksbilder eller använda Azure Backup. Detta är nödvändigt att återställa en virtuell dator till ett konsekvent tillstånd.

Om du använder lokalt redundant lagring, måste du kopiera ögonblicksbilderna till ett annat lagringskonto omedelbart när du har skapat ögonblicksbilden. Kopieringsmål kan vara ett lokalt redundant lagringskonto i en annan region, vilket resulterar i att kopiera i en avlägsen region. Du kan också kopiera ögonblicksbilden till ett läsåtkomst till geografiskt redundant lagringskonto i samma region. I det här fallet replikeras lazily ögonblicksbilden till den fjärranslutna sekundära regionen. Din säkerhetskopia är skyddat från katastrofer på den primära platsen efter kopiering och replikeringen är klar.

Om du vill kopiera dina inkrementella ögonblicksbilder för Katastrofåterställning effektivt, läser du anvisningarna i [säkerhetskopiera Azure ohanterade Virtuella datordiskar med inkrementella ögonblicksbilder](../articles/virtual-machines/windows/incremental-snapshots.md).

![Säkerhetskopiera Azure ohanterade Virtuella datordiskar med inkrementella ögonblicksbilder][2]

### <a name="recovery-from-snapshots"></a>Återställning från ögonblicksbilder

Kopiera den för att skapa en ny blob för att hämta en ögonblicksbild. Om du kopierar ögonblicksbilden från det primära kontot, kan du kopiera ögonblicksbilden över till grundläggande blob ögonblicksbild. Den här processen återställer disken till ögonblicksbilden. Den här processen kallas befordrar ögonblicksbilden. Om du kopierar ögonblicksbilden säkerhetskopieringen från ett sekundärt konto när det gäller ett konto för läsåtkomst till geografiskt redundant lagring, måste du kopiera den till en primär konto. Du kan kopiera en ögonblicksbild av [med hjälp av PowerShell](../articles/storage/common/storage-powershell-guide-full.md) eller med hjälp av AzCopy-verktyget. Mer information finns i [överföra data med kommandoradsverktyget azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy).

Du måste kopiera alla ögonblicksbilder som ingår i samma samordnad återställningspunkt för virtuella datorer med flera diskar. Du kan använda blobarna för att återskapa den virtuella datorn med hjälp av mallen för den virtuella datorn när du har kopierat ögonblicksbilderna till skrivbara VHD-BLOB.

## <a name="other-options"></a>Andra alternativ

### <a name="sql-server"></a>SQL Server

SQL Server som körs på en virtuell dator har sin egen inbyggda funktioner för att säkerhetskopiera SQL Server-databas till Azure Blob-lagring eller en fil filresurser. Om lagringskontot är geo-redundant lagring eller read-access geo-redundant lagring, kan du kan komma åt dessa säkerhetskopior i lagringskontots sekundära datacenter i händelse av en katastrof med samma begränsningar som tidigare diskuteras. Mer information finns i [säkerhetskopiera och återställa för SQL Server i Azure virtual machines](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md). Förutom att säkerhetskopiera och återställa, [SQL Server AlwaysOn-Tillgänglighetsgrupper](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md) kan upprätthålla sekundära repliker för databaser. Den här möjligheten minskar återställningstiden för katastrofåterställning.

## <a name="other-considerations"></a>Annat att tänka på

Den här artikeln har beskrivs hur du säkerhetskopierar eller ta ögonblicksbilder av dina virtuella datorer och deras diskar för att stödja katastrofåterställning och hur du använder dessa säkerhetskopior eller ögonblicksbilder för att återställa dina data. Flera personer använder mallar skapar sina virtuella datorer och annan infrastruktur i Azure med Azure Resource Manager-modellen. Du kan använda en mall för att skapa en virtuell dator som har samma konfiguration varje gång. Om du använder anpassade avbildningar för att skapa dina virtuella datorer, måste du också se till att dina avbildningar skyddas med ett läsåtkomst till geografiskt redundant storage-konto för att lagra dem.

Säkerhetskopieringsprocessen kan därför vara en kombination av två saker:

- Säkerhetskopiera data (diskar).
- Säkerhetskopiera konfigurationen (mallar och anpassade avbildningar).

Du kan behöva hantera säkerhetskopiering av både data och konfiguration beroende på av säkerhetskopieringen som du väljer, eller backup-tjänsten kan hantera alla som du.

## <a name="appendix-understanding-the-impact-of-data-redundancy"></a>Tillägg: Förstå effekten av dataredundans

För storage-konton i Azure, det finns tre typer av dataredundans som du bör tänka på angående disaster recovery: lokalt redundant, geo-redundant eller geo-redundant med läsbehörighet. 

Lokalt redundant lagring behåller tre kopior av data i samma datacenter. När den virtuella datorn skriver data, uppdateras alla tre kopior innan lyckades returneras till anroparen, så att du vet att de är identiska. Disken är skyddat från lokala fel eftersom det är inte troligt att påverkas alla tre kopior på samma gång. Lokalt redundant lagring om det finns ingen geo-redundans, så att disken inte är skyddad från kritiska fel som kan påverka en datacenter- eller enhet.

Med geo-redundant lagring och läsåtkomst till geografiskt redundant lagring, finns tre kopior av dina data kvar i den primära regionen som väljs av dig. Tre kopior av dina data finns kvar i en motsvarande sekundär region som anges av Azure. Om du lagrar data i västra USA, till exempel replikeras data till östra USA. Kopiera kvarhållning utförs asynkront, och det finns en kort fördröjning mellan uppdateringar av primära och sekundära platser. Repliker av diskarna på den sekundära platsen är konsekvent på basis av per disk (med fördröjning), men repliker av flera aktiva diskar kanske inte är synkroniserade med varandra. Om du vill ha konsekventa repliker över flera diskar, behövs för programkonsekventa ögonblicksbilder.

Den största skillnaden mellan geo-redundant lagring och läsåtkomst till geografiskt redundant lagring är att med läsåtkomst till geografiskt redundant lagring, du kan läsa den sekundära kopian när som helst. Om det finns ett problem som återger data i den primära regionen blir otillgänglig, gör Azure-teamet allt för att återställa åtkomsten. Även om primärt inte är igång, om du har läsåtkomst till geografiskt redundant lagring aktiverad, du kan komma åt data i det sekundära datacentret. Därför, om du planerar att läsa från repliken när primärt inte är tillgänglig sedan läsåtkomst till geografiskt redundant lagring bör övervägas.

Om det har visat sig vara ett betydande strömavbrott, kan Azure-teamet utlösa geo-redundans och ändra primära DNS-posterna så att den pekar till den sekundära lagringsplatsen. Om du har geo-redundant lagring eller read-access geo-redundant lagring aktiverad, kan du nu komma åt data i den region som används för att vara sekundärt. Med andra ord, om ditt lagringskonto är geo-redundant lagring och det finns ett problem, du kan komma åt sekundär lagring om det finns en geo-redundansväxling.

Mer information finns i [vad du gör om ett avbrott i Azure Storage inträffar](../articles/storage/common/storage-disaster-recovery-guidance.md).

>[!NOTE] 
>Microsoft kontrollerar om det uppstår redundans. Redundans kontrolleras inte per lagringskonto, så att det inte fastställs av enskilda kunder. Om du vill implementera haveriberedskap för specifika storage-konton eller virtuella diskar, måste du använda de metoder som beskrivs tidigare i den här artikeln.

[1]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png