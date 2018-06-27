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
ms.openlocfilehash: 03db1bf84e200d8b66f0395cbd96813e2248eefe
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34806374"
---
# <a name="backup-and-disaster-recovery-for-azure-iaas-disks"></a>Säkerhetskopiering och katastrofåterställning återställning för Azure IaaS-diskar

Den här artikeln beskriver hur du planerar för säkerhetskopiering och katastrofåterställning (DR) med IaaS virtuella maskiner (VMs) och diskar i Azure. Det här dokumentet beskriver både hanterade och ohanterade diskar.

Först måste upp vi inbyggda feltolerans i Azure-plattformen som skyddar mot lokala fel. Sedan tar vi upp katastrofåterställning-scenarier som inte omfattas av inbyggda funktioner. Vi kan också visa flera exempel på arbetsbelastningsscenarier där olika säkerhetskopiering och Katastrofåterställning överväganden kan använda. Vi går sedan igenom möjliga lösningar för Katastrofåterställning för IaaS-diskar. 

## <a name="introduction"></a>Introduktion

Azure-plattformen använder olika metoder för redundans och feltolerans för att skydda kunder från lokaliserade maskinvarufel. Lokala fel kan innehålla problem med en Azure Storage server-dator på servern som innehåller en del av data för en virtuell disk eller fel i en SSD och Hårddisk. Sådana isolerade maskinvarufel för komponenten kan inträffa under normal drift. 

Azure-plattformen är avsedd att vara motståndskraftiga mot dessa fel. Större katastrofer kan resultera i fel eller inaccessibility av många lagringsservrar eller även ett helt datacenter. Även om dina virtuella datorer och diskar skyddas normalt från lokaliserade fel, krävs ytterligare steg för att skydda din arbetsbelastning från region hela katastrofalt fel, till exempel en större katastrof som kan påverka den virtuella datorn och diskar.

Utöver möjligheten att plattform fel kan uppstå problem med en kund program eller data. En ny version av programmet kan till exempel oavsiktligt gör en ändring i de data som orsakar att. I så fall kanske du vill återställa programmet och data till en tidigare version som innehåller det senaste fungerande tillståndet. Detta kräver att underhålla regelbundna säkerhetskopieringar.

För regional katastrofåterställning, måste du säkerhetskopiera IaaS VM-diskar till en annan region. 

Innan vi tittar på säkerhetskopiering och Katastrofåterställning alternativ Sammanfattningsvis vi några metoder för att hantera lokaliserade fel.

### <a name="azure-iaas-resiliency"></a>Azure IaaS-återhämtning

*Återhämtning* refererar till tolerans för vanliga fel som uppstår i maskinvarukomponenter. Återhämtning är möjligheten att återställa från fel och fortsätter att fungera. Det är inte om att undvika fel, men svarar på fel i ett sätt som förhindrar avbrott eller dataförlust. Målet med elasticiteten är att återställa programmet till ett fullt fungerande tillstånd efter fel. Virtuella Azure-datorer och diskar som är avsedda att vara motståndskraftiga mot vanliga maskinvarufel. Nu ska vi titta på hur Azure IaaS-plattformen ger den här återhämtning.

En virtuell dator består i huvudsak av två delar: en beräkningsserver och beständiga diskarna. Både påverkar feltolerans för en virtuell dator.

Om Azure compute värdservern som innehåller den virtuella datorn får ett maskinvarufel som är sällsynt, för Azure att automatiskt återställa den virtuella datorn på en annan server. Om det här scenariot, din datorn startas om och den virtuella datorn startas igen efter en stund. Azure automatiskt identifierar sådana maskinvarufel och kör återställningar för att säkerställa kunden VM är tillgängligt så snart som möjligt.

Om IaaS-diskar hållbarhet av data som är viktig för en beständig lagring plattform. Azure-kunder har viktiga business-program körs med IaaS och de är beroende av beständiga data. Azure Designer skydd för diskarna IaaS med tre redundanta kopior av data som lagras lokalt. Dessa kopior av ange för hög hållbarhet mot lokala fel. Om en av de maskinvarukomponenter som innehåller disken misslyckas påverkas inte den virtuella datorn, eftersom det finns två ytterligare kopior som stöd för disk-begäranden. Det fungerar bra, även om två olika maskinvarukomponenter som har stöd för en disk som inte samtidigt (som är ovanligt). 

För att säkerställa att du alltid har tre repliker, Azure Storage automatiskt skapas en ny kopia av data i bakgrunden om någon av tre kopierar otillgänglig. Det bör därför inte nödvändigt att använda RAID med Azure-diskar för feltolerans. En enkel RAID 0-konfigurationen ska vara tillräcklig för striping diskar, om det behövs för att skapa större volymer.

På grund av den här arkitekturen har Azure konsekvent levereras företagsklass hållbarhet för IaaS-diskar, med ett branschledande noll procent [årliga felintervall](https://en.wikipedia.org/wiki/Annualized_failure_rate).

Lokaliserade maskinvarufel på beräknade värd eller i plattformen lagring kan ibland resulterar i att den virtuella datorn som omfattas av tillfälliga den [Azure-serviceavtalet](https://azure.microsoft.com/support/legal/sla/virtual-machines/) för VM-tillgänglighet. Azure tillhandahåller också ett branschledande serviceavtal för enskild VM-instanser som använder Azure Premium SSD-diskar.

Kunder kan använda för att skydda arbetsbelastningar för program från avbrott på grund av temporär otillgänglighet en disk eller VM [tillgänglighetsuppsättningar](../articles/virtual-machines/windows/manage-availability.md). Två eller flera virtuella datorer i en tillgänglighetsuppsättning tillhandahålla redundans för programmet. Azure skapar sedan dessa virtuella datorer och diskar i separata feldomäner med olika ström, nätverk och server-komponenter. 

På grund av dessa separat feldomäner påverkar lokaliserade maskinvarufel vanligtvis inte flera virtuella datorer i uppsättningen på samma gång. Med separata feldomäner ger hög tillgänglighet för ditt program. Det har anses vara en bra idé att använda tillgänglighetsuppsättningar när hög tillgänglighet krävs. I nästa avsnitt beskrivs disaster recovery-aspekt.

### <a name="backup-and-disaster-recovery"></a>Säkerhetskopiering och katastrofåterställning

Katastrofåterställning är möjligheten att återställa från sällsynt, men större, incidenter. Dessa händelser är inte är tillfällig, storskaligt fel, till exempel avbrott i tjänsten som påverkar en hel region. Katastrofåterställning innehåller säkerhetskopiering och arkivering och kan innehålla handgrepp, t.ex en databas återställs från en säkerhetskopia.

Plattformen Azure inbyggt skydd mot fel på lokaliserade skydda inte fullständigt virtuella datorer/diskar om en större katastrof orsakar storskaliga avbrott. Dessa storskaliga avbrott innehåller katastrofer, till exempel om ett datacenter påverkas av en orkan, jordbävning, fire eller om det finns en enhet för storskaliga maskinvarufel. Dessutom kan det uppstå fel som beror på programmet eller problem.

För att skydda dina IaaS-arbetsbelastningar från avbrott, bör du planera för redundans och har att aktivera återställning av säkerhetskopior. Vid katastrofåterställning kan säkerhetskopiera du på en annan geografisk plats från den primära platsen. Den här metoden garanterar säkerhetskopian inte påverkas av samma händelse som ursprungligen påverkade VM eller diskar. Mer information finns i [katastrofåterställning för Azure-program](/azure/architecture/resiliency/disaster-recovery-azure-applications).

DR-överväganden kan innehålla följande aspekter:

- Hög tillgänglighet: möjligheten för programmet för att fortsätta köras i ett felfritt tillstånd utan betydande driftavbrott. Av *felfritt tillstånd*, detta tillstånd innebär att programmet svarar och användare kan ansluta till programmet och interagera med den. Vissa verksamhetskritiska program och databaser kan bli ombedd att alltid är tillgängliga, även om det finns fel i plattformen. Du kan behöva planera redundans för programmet, samt data för dessa arbetsbelastningar.

- Data hållbarhet: I vissa fall huvudsakliga ersättningen är att säkerställa att data går förlorade om en katastrof inträffar. Därför kanske du måste en säkerhetskopia av dina data i en annan plats. För dessa arbetsbelastningar, kanske du inte behöver fullständig redundans för programmet, men en regelbunden säkerhetskopiering av diskarna.

## <a name="backup-and-dr-scenarios"></a>Scenarier för säkerhetskopiering och Katastrofåterställning

Nu ska vi titta på några exempel på arbetsbelastning Programscenarier och överväganden för att planera för katastrofåterställning.

### <a name="scenario-1-major-database-solutions"></a>Scenario 1: Större databaslösningar

Överväg att en databasserver för produktion, t.ex. SQL Server eller Oracle, som har stöd för hög tillgänglighet. Kritiska produktionsprogram och användare beror på den här databasen. Katastrofåterställningsplanen för det här systemet kan behöva stöd för följande krav:

- Data är skyddade och återställas.
- Servern måste vara tillgängliga för användning.

Återställningsplanen kan kräva att underhålla en replik av databasen i en annan region som en säkerhetskopia. Beroende på kraven för servertillgänglighet och återställning av data kan lösningen vara från en aktiv-aktiv eller aktivt-passivt replik plats och periodiska offlinesäkerhetskopiering av data. Relationsdatabaser, till exempel SQL Server och Oracle, ger olika alternativ för replikering. SQL Server använder [SQL Server AlwaysOn-Tillgänglighetsgrupper](https://msdn.microsoft.com/library/hh510230.aspx) för hög tillgänglighet.

Det stöder också NoSQL-databaser som MongoDB, [repliker](https://docs.mongodb.com/manual/replication/) för redundans. Repliker för hög tillgänglighet används.

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>Scenario 2: Ett kluster med redundanta virtuella datorer

Överväg att en arbetsbelastning hanteras av ett kluster för virtuella datorer som ger redundans och belastningsutjämning. Ett exempel är ett Cassandra kluster som distribueras i en region. Den här typen av arkitekturen innehåller redan en hög nivå av redundans i detta område. Men om du vill skydda arbetsbelastningen på att en regional nivå, bör du sprida klustret mellan två regioner och regelbundna säkerhetskopieringar till en annan region.

### <a name="scenario-3-iaas-application-workload"></a>Scenario 3: IaaS programmet arbetsbelastning

Nu ska vi titta på arbetsbelastningen för IaaS-programmet. Det här programmet kan till exempel vara en produktionsögonblicksbild vid arbetsbelastningar som körs på en virtuell dator i Azure. Det kan vara en webbserver eller filserver håller innehållet och andra resurser för en plats. Det kan också vara ett specialbyggt affärsprogram som körs på en virtuell dator som lagras dess data, resurser och programtillstånd på VM-diskarna. I det här fallet är det viktigt att se till att du kan göra säkerhetskopior med jämna mellanrum. Frekvens för säkerhetskopiering ska baseras på typ av VM-arbetsbelastning. Till exempel om programmet körs varje dag och ändrar data, bör sedan säkerhetskopieringen tas varje timme.

Ett annat exempel är en rapportserver som hämtar data från andra källor och genererar aggregerade rapporter. Förlust av den här virtuella datorn eller diskar kan leda till förlust av rapporterna. Det kan dock vara möjligt att köra rapporterna och återskapa utdata. I så fall kan har du inte verkligen förlust av data, även om rapporteringsservern träffar med en katastrof. Därför kanske en högre nivå av feltolerans för att förlora en del av data på rapporteringsservern. I så fall är mindre ofta återkommande säkerhetskopieringar ett alternativ för att minska kostnaderna.

### <a name="scenario-4-iaas-application-data-issues"></a>Scenario 4: IaaS data programproblem

IaaS problem med programmet är en annan möjlighet. Överväg att ett program som beräknar, underhåller och levererar kritiska affärsdata, till exempel information om priser. En ny version av programmet hade ett programfel för programvara som felaktigt beräknade prissättning och skadad befintliga commerce data som hanteras av plattformen. Här är bästa loppet av åtgärden återgå till den tidigare versionen av programmet och data. Om du vill aktivera det här ta periodiska säkerhetskopieringar av systemet.

## <a name="disaster-recovery-solution-azure-backup"></a>Lösning för katastrofåterställning: Azure-säkerhetskopiering 

[Azure-säkerhetskopiering](https://azure.microsoft.com/services/backup/) används för säkerhetskopiering och Katastrofåterställning och den fungerar med [hanterade diskar](../articles/virtual-machines/windows/managed-disks-overview.md) samt [ohanterad diskar](../articles/virtual-machines/windows/about-disks-and-vhds.md#unmanaged-disks). Du kan skapa en säkerhetskopiering med tidsbaserade säkerhetskopieringar och återställande lätt VM säkerhetskopiering bevarandeprinciper. 

Om du använder [Premium SSD-diskar](../articles/virtual-machines/windows/premium-storage.md), [hanterade diskar](../articles/virtual-machines/windows/managed-disks-overview.md), eller andra disktyper med den [lokalt redundant lagring](../articles/storage/common/storage-redundancy-lrs.md) är särskilt viktigt att säkerhetskopiera periodiska DR. Azure-säkerhetskopiering lagrar data i recovery services-ventilen för långsiktig kvarhållning. Välj den [geo-redundant lagring](../articles/storage/common/storage-redundancy-grs.md) alternativ för säkerhetskopiering recovery services-valvet. Alternativet säkerställer att säkerhetskopieringar replikeras till en annan Azure-region för att skydda från regionala katastrofer.

För [ohanterad diskar](../articles/virtual-machines/windows/about-disks-and-vhds.md#unmanaged-disks), du kan använda lokalt redundant lagring-typ för IaaS-diskar, men kontrollera att Azure Backup har aktiverats med alternativet geo-redundant lagring för recovery services-valvet.

> [!NOTE]
> Om du använder den [geo-redundant lagring](../articles/storage/common/storage-redundancy-grs.md) eller [geo-redundant lagring med läsbehörighet](../articles/storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) alternativet för ohanterade diskar kan du fortfarande behöver programkonsekventa ögonblicksbilder för säkerhetskopiering och Katastrofåterställning. Använd antingen [Azure Backup](https://azure.microsoft.com/services/backup/) eller [programkonsekventa ögonblicksbilder](#alternative-solution-consistent-snapshots).

 I följande tabell är en sammanfattning av lösningarna som är tillgängliga för Katastrofåterställning.

| Scenario | Automatisk replikering | Lösning för Katastrofåterställning |
| --- | --- | --- |
| Premium SSD-diskar | Lokala ([lokalt redundant lagring](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Hanterade diskar | Lokala ([lokalt redundant lagring](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Ohanterad lokalt redundant lagringsdiskar | Lokala ([lokalt redundant lagring](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Ohanterad geo-redundant lagringsdiskar | Mellan region ([geo-redundant lagring](../articles/storage/common/storage-redundancy-grs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Programkonsekventa ögonblicksbilder](#alternative-solution-consistent-snapshots) |
| Ohanterad geo-redundant lagring med läsbehörighet diskar | Mellan region ([geo-redundant lagring med läsbehörighet](../articles/storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Programkonsekventa ögonblicksbilder](#alternative-solution-consistent-snapshots) |

Hög tillgänglighet uppfylls bäst med hjälp av hanterade diskar i en tillgänglighetsuppsättning tillsammans med Azure Backup. Om du använder ohanterade diskar kan du fortfarande använda Azure Backup för Katastrofåterställning. Om det inte går att använda Azure Backup kan sedan ta [programkonsekventa ögonblicksbilder](#alternative-solution-consistent-snapshots), enligt beskrivningen i ett senare avsnitt är en alternativ lösning för säkerhetskopiering och Katastrofåterställning.

Dina val för hög tillgänglighet, säkerhetskopiering och Katastrofåterställning på programmet eller infrastrukturen kan representeras på följande sätt:

| Nivå |   Hög tillgänglighet   | Säkerhetskopiering och Katastrofåterställning |
| --- | --- | --- |
| Program | SQL Server AlwaysOn | Azure Backup |
| Infrastruktur    | Tillgänglighetsuppsättning  | GEO-redundant lagring med programkonsekventa ögonblicksbilder |

### <a name="using-azure-backup"></a>Med hjälp av Azure-säkerhetskopiering 

[Azure-säkerhetskopiering](../articles/backup/backup-azure-vms-introduction.md) kan säkerhetskopiera dina virtuella datorer som kör Windows eller Linux till Azure recovery services-valvet. Säkerhetskopiera och återställa verksamhetskritiska data är komplicerade av att verksamhetskritiska data måste säkerhetskopieras när de program som ger data körs. 

För att lösa problemet, tillhandahåller Azure Backup programkonsekvent säkerhetskopiering för Microsoft-arbetsbelastningar. Tjänsten volume shadow används för att säkerställa att data skrivs korrekt till lagring. Endast filkonsekventa säkerhetskopior är möjligt för Linux virtuella datorer, Linux har inte funktioner som motsvarar tjänsten volume shadow.

![Azure Backup-flöde][1]

När Azure Backup initierar en säkerhetskopiering på den schemalagda tiden, utlöser reservanknytning installerad på den virtuella datorn att ta en ögonblicksbild i tidpunkt. En ögonblicksbild tas tillsammans med tjänsten volume shadow för att hämta en programkonsekvent ögonblicksbild diskar i den virtuella datorn utan att behöva stänga av. Säkerhetskopiering tillägget på den virtuella datorn tömmer alla skrivningar innan du tar en programkonsekvent ögonblicksbild av alla diskar. När du har tagit ögonblicksbilden överförs informationen med Azure Backup till säkerhetskopieringsvalvet. Om du vill att säkerhetskopieringen effektivare tjänsten identifierar och överför endast block av data som har ändrats efter den senaste säkerhetskopieringen.

Om du vill återställa kan du visa tillgängliga säkerhetskopieringar via Azure Backup och startar sedan en återställning. Du kan skapa och återställa Azure-säkerhetskopieringar via den [Azure-portalen](https://portal.azure.com/), av [med hjälp av PowerShell](../articles/backup/backup-azure-vms-automation.md), eller genom att använda den [Azure CLI](/cli/azure/). 

### <a name="steps-to-enable-a-backup"></a>Steg för att aktivera en säkerhetskopia

Använd följande steg för att aktivera säkerhetskopiering av dina virtuella datorer med hjälp av [Azure-portalen](https://portal.azure.com/). Det finns en variant beroende på exakt scenario. Referera till den [Azure Backup](../articles/backup/backup-azure-vms-introduction.md) dokumentationen för fullständig information. Azure Backup också [stöder virtuella datorer med hanterade diskar](https://azure.microsoft.com/blog/azure-managed-disk-backup/).

1.  Skapa ett recovery services-valv för en virtuell dator:

    a. I den [Azure-portalen](https://portal.azure.com/), Bläddra **alla resurser** och hitta **Recovery Services-valv**.

    b. På den **Recovery Services-valv** -menyn klickar du på **Lägg till** och följ stegen för att skapa ett nytt valv i samma region som den virtuella datorn. Till exempel om den virtuella datorn är i USA, västra region, Välj västra USA för valvet.

2.  Kontrollera storage-replikering för nyskapade valvet. Komma åt valvet under **Recovery Services-valv** och gå till **inställningar** > **konfigurering av säkerhetskopiering**. Se till att den **geo-redundant lagring** alternativet är markerat som standard. Det här alternativet innebär att ditt valv replikeras automatiskt till ett sekundärt datacenter. Ditt valv i USA, västra replikeras automatiskt till östra USA.

3.  Konfigurera principen för säkerhetskopiering och välj den virtuella datorn från samma användargränssnitt.

4.  Kontrollera att Backup-agenten är installerad på den virtuella datorn. Om den virtuella datorn har skapats med hjälp av en Azure-galleriet bild, installerats Backup-agenten redan. Annars (det vill säga om du använder en anpassad avbildning), följ instruktionerna till [installera VM-agenten på en virtuell dator](../articles/backup/backup-azure-arm-vms-prepare.md#install-the-vm-agent-on-the-virtual-machine).

5.  Kontrollera att den virtuella datorn tillåter nätverksanslutningar för säkerhetskopieringstjänsten ska fungera. Följ instruktionerna för [nätverksanslutningar](../articles/backup/backup-azure-arm-vms-prepare.md#establish-network-connectivity).

6.  När de här stegen har slutförts, körs säkerhetskopieringen med regelbundna intervall som anges i principen för säkerhetskopiering. Om det behövs kan du utlösa den första säkerhetskopieringen manuellt från valvet instrumentpanelen på Azure-portalen.

För att automatisera Azure Backup med hjälp av skript, referera till [PowerShell-cmdletar för säkerhetskopiering](../articles/backup/backup-azure-vms-automation.md).

### <a name="steps-for-recovery"></a>Steg för återställning

Om du behöver reparera eller återskapa en virtuell dator kan du återställa den virtuella datorn från någon av återställningspunkter för säkerhetskopiering i valvet. Det finns ett par olika alternativ för att utföra återställningen:

-   Du kan skapa en ny virtuell dator som en tidpunkt i representation av den virtuella datorn med säkerhetskopierade.

-   Du kan återställa diskarna och sedan använda mallen för den virtuella datorn för att anpassa och återskapa den återställda virtuella datorn. 

Mer information finns i anvisningarna för att [använda Azure portal för att återställa virtuella datorer](../articles/backup/backup-azure-arm-restore-vms.md). Det här dokumentet beskriver även hur för återställning av säkerhetskopierade virtuella datorer till en parad datacenter med hjälp av din geo-redundant säkerhetskopieringsvalvet om det finns en katastrof på det primära datacentret. I så fall använder Azure Backup beräknings-tjänsten från den sekundära regionen för att skapa den återställda virtuella datorn.

Du kan också använda PowerShell för [återställa en virtuell dator](../articles/backup/backup-azure-arm-restore-vms.md#restore-a-vm-during-an-azure-datacenter-disaster) eller för [skapar en ny virtuell dator från återställts diskar](../articles/backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="alternative-solution-consistent-snapshots"></a>Alternativ lösning: programkonsekventa ögonblicksbilder

Om det inte går att använda Azure Backup kan implementera du ditt eget mekanism för säkerhetskopiering med hjälp av ögonblicksbilder. Det är komplicerat att skapa programkonsekventa ögonblicksbilder för alla diskar som används av en virtuell dator och replikerar sedan dessa ögonblicksbilder till en annan region. Därför överväger Azure med hjälp av Backup-tjänsten som ett bättre alternativ än att skapa en anpassad lösning. 

Om du använder geo-redundant lagring/geo-redundant lagring med läsbehörighet för diskar, replikeras ögonblicksbilder automatiskt till ett sekundärt datacenter. Om du använder lokalt redundant lagring för diskar, måste du replikerar data själv. Mer information finns i [säkerhetskopiera Azure-ohanterade Virtuella diskar med inkrementell ögonblicksbilder](../articles/virtual-machines/windows/incremental-snapshots.md).

En ögonblicksbild är en representation av ett objekt vid en viss tidpunkt. En ögonblicksbild har fakturering för inkrementell storleken på data den innehåller. Mer information finns i [skapa en ögonblicksbild av blob](../articles/storage/blobs/storage-blob-snapshots.md).

### <a name="create-snapshots-while-the-vm-is-running"></a>Skapa ögonblicksbilder medan den virtuella datorn körs

Du kan ta en ögonblicksbild när som helst om den virtuella datorn körs, finns det fortfarande data som strömmas till diskarna. Ögonblicksbilder kan innehålla delvis åtgärder som rör sig. Även om det finns flera diskar, kan ögonblicksbilder av olika diskar ha inträffat vid olika tidpunkter. Dessa scenarier kan orsaka att ögonblicksbilderna vara ej samordnad. Den här bristen på samordning är särskilt problematiskt för stripe-volymer vars filer kan vara skadad om ändringar som gjordes under säkerhetskopieringen.

Om du vill undvika detta genomföra säkerhetskopieringsprocessen följande steg:

1.  Låsa alla diskar.

2.  Rensa alla väntande skrivningar.

3.  [Skapa en ögonblicksbild av blob](../articles/storage/blobs/storage-blob-snapshots.md) för alla diskar.

Vissa Windows-program, t.ex. SQL Server innehåller en samordnad säkerhetskopiering funktion via en tjänsten volume shadow skapa programkonsekventa säkerhetskopior. På Linux, kan du använda ett verktyg som *fsfreeze* för samordning av diskarna. Det här verktyget innehåller filkonsekventa säkerhetskopieringar, men inte programkonsekventa ögonblicksbilder. Den här processen är komplex eller så bör du använda [Azure Backup](../articles/backup/backup-azure-vms-introduction.md) eller en lösning för säkerhetskopiering från tredje part som redan implementerar den här proceduren.

Föregående process resulterar i en samling samordnade ögonblicksbilder för alla Virtuella diskar, som representerar en specifik tidpunkt i vy för den virtuella datorn. Det här är en återställningspunkt för säkerhetskopiering för den virtuella datorn. Du kan upprepa processen med schemalagda intervall för att skapa regelbundna säkerhetskopieringar. Se [kopiera säkerhetskopieringar till en annan region](#copy-the-snapshots-to-another-region) steg för att kopiera ögonblicksbilder till en annan region för Katastrofåterställning.

### <a name="create-snapshots-while-the-vm-is-offline"></a>Skapa ögonblicksbilder medan den virtuella datorn är offline

Ett annat alternativ för att skapa konsekvent säkerhetskopior är att stänga av den virtuella datorn och ta blob ögonblicksbilder av varje disk. Med blob ögonblicksbilder är enklare än samordning av ögonblicksbilder av en aktiv virtuell dator, men det krävs några minuter stillestånd.

1. Stäng av den virtuella datorn.

2. Skapa en ögonblicksbild av varje virtuell hårddisk blob som tar bara några sekunder.

    Du kan använda för att skapa en ögonblicksbild, [PowerShell](../articles/storage/common/storage-powershell-guide-full.md), [Azure Storage REST API](https://msdn.microsoft.com/library/azure/ee691971.aspx), [Azure CLI](/cli/azure/), eller ett Azure Storage-klientbibliotek som [den Storage-klientbibliotek för .NET](https://msdn.microsoft.com/library/azure/hh488361.aspx).

3. Starta den virtuella datorn, vilket avslutar avbrottstiden. Normalt slutförs hela processen inom några minuter.

Den här processen ger en uppsättning programkonsekventa ögonblicksbilder för alla diskar, vilket ger en återställningspunkt för säkerhetskopiering för den virtuella datorn.

### <a name="copy-the-snapshots-to-another-region"></a>Kopiera ögonblicksbilder till en annan region

Skapa ögonblicksbilder enbart kanske inte är tillräckligt för Katastrofåterställning. Du måste också replikeras säkerhetskopiorna av ögonblicksbilder till en annan region.

Om du använder geo-redundant lagring eller geo-redundant lagring med läsbehörighet för diskarna, sedan replikeras ögonblicksbilderna till den sekundära regionen automatiskt. Det kan finnas några minuter för fördröjning innan replikeringen. Om det primära datacentret kraschar innan ögonblicksbilderna Slutför replikering kan du inte kommer åt ögonblicksbilderna från sekundärt datacenter. Sannolikheten för det här är liten.

> [!NOTE] 
> Endast med diskar i en geo-redundant lagring eller geo-redundant läsbehörighet skyddar inte den virtuella datorn från katastrofer i storage-konto. Du måste också skapa samordnade ögonblicksbilder eller använda Azure Backup. Detta är nödvändigt att återställa en virtuell dator till ett konsekvent tillstånd.

Om du använder lokalt redundant lagring, måste du kopiera ögonblicksbilder till ett annat lagringskonto omedelbart efter att skapa ögonblicksbilden. Kopiera mål kan vara ett lokalt redundant lagringskonto i en annan region, vilket resulterar i att kopiera i en fjärransluten region. Du kan också kopiera ögonblicksbilden till ett geo-redundant lagring med läsbehörighet konto i samma region. I det här fallet replikeras ögonblicksbilden lazy till den fjärranslutna sekundära regionen. Säkerhetskopian är skyddat från katastrofer på den primära platsen efter kopiering och replikeringen är klar.

Om du vill kopiera ditt inkrementell ögonblicksbilder för Katastrofåterställning effektivt läser du anvisningarna i [säkerhetskopiera Azure ohanterade Virtuella diskar med inkrementell ögonblicksbilder](../articles/virtual-machines/windows/incremental-snapshots.md).

![Säkerhetskopiera Azure ohanterade Virtuella diskar med inkrementell ögonblicksbilder][2]

### <a name="recovery-from-snapshots"></a>Återställning från ögonblicksbilder

Kopiera den för att skapa en ny blob för att hämta en ögonblicksbild. Om du vill kopiera ögonblicksbilden från det primära kontot, kan du kopiera ögonblicksbilden över till grundläggande blob med ögonblicksbilden av. Den här processen återställs disken till ögonblicksbilden. Den här processen kallas befordrar ögonblicksbilden. Om du kopierar ögonblicksbild av säkerhetskopian från ett sekundärt konto när det gäller ett konto med geo-redundant lagring med läsbehörighet, måste du kopiera den till en primär konto. Du kan kopiera en ögonblicksbild av [med hjälp av PowerShell](../articles/storage/common/storage-powershell-guide-full.md) eller med hjälp av verktyget AzCopy. Mer information finns i [överföra data med kommandoradsverktyget azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy).

För virtuella datorer med flera diskar, måste du kopiera alla ögonblicksbilder som ingår i samma samordnade återställningspunkt. Du kan använda blobar för att återskapa den virtuella datorn med hjälp av mallen för den virtuella datorn när du har kopierat ögonblicksbilderna skrivbar VHD-blobbar.

## <a name="other-options"></a>Andra alternativ

### <a name="sql-server"></a>SQL Server

SQL Server som körs på en virtuell dator har sin egen inbyggda funktioner för att säkerhetskopiera SQL Server-databas till Azure Blob-lagring eller en fil i resursen. Om lagringskontot är geo-redundant lagring eller läsbehörighet geo-redundant lagring, kan komma åt dessa säkerhetskopieringar i storage-konto sekundärt datacenter vid katastrofåterställning, med samma begränsningar som tidigare diskuteras. Mer information finns i [säkerhetskopiering och återställning för SQL Server på virtuella Azure-datorer](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md). Förutom att säkerhetskopiera och återställa, [SQL Server AlwaysOn-Tillgänglighetsgrupper](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md) kan upprätthålla sekundära repliker för databaser. Det här antalet minskar disaster recovery-tid.

## <a name="other-considerations"></a>Andra överväganden

Den här artikeln har beskrivs hur du säkerhetskopierar eller ta ögonblicksbilder av dina virtuella datorer och deras diskar för att stödja katastrofåterställning och hur du använder dessa säkerhetskopior eller ögonblicksbilder du återställer data. Flera personer använder mallar för att skapa deras virtuella datorer och annan infrastruktur i Azure med Azure Resource Manager-modellen. Du kan använda en mall för att skapa en virtuell dator som har samma konfiguration varje gång. Om du använder anpassade avbildningar för att skapa dina virtuella datorer, måste du också se till att bilderna skyddas genom att använda ett konto med geo-redundant lagring med läsbehörighet för att lagra dem.

Säkerhetskopieringsprocessen kan därför vara en kombination av två saker:

- Säkerhetskopiera data (diskar).
- Säkerhetskopiera konfigurationen (mallar och anpassade avbildningar).

Beroende på säkerhetskopiering alternativ du väljer du kan behöva hantera säkerhetskopiering av både data och konfiguration eller säkerhetskopieringstjänsten kan hantera all som du.

## <a name="appendix-understanding-the-impact-of-data-redundancy"></a>Bilaga: Förstå effekten av dataredundans

För storage-konton i Azure, det finns tre typer av dataredundans som du bör tänka på avseende katastrofåterställning: lokalt redundant geo-redundant eller geo-redundant med läsbehörighet. 

Lokalt redundant lagring behåller tre kopior av data i samma datacenter. När den virtuella datorn skriver data, uppdateras alla tre kopior innan lyckas returneras till anroparen, så att du vet att de är identiska. Disken är skyddat från lokala fel eftersom det inte troligt att påverkas alla tre kopior på samma gång. Lokalt redundant lagring, om det finns ingen geo-redundans, så att disken inte är skyddat från katastrofalt fel som kan påverka en datacenter- eller enhet.

Tre kopior av dina data finns kvar i den primära region som väljs av du med geo-redundant lagring och geo-redundant lagring med läsbehörighet. Tre fler kopior av dina data finns kvar i en motsvarande sekundär region som anges av Azure. Till exempel om du lagrar data i USA, västra replikeras data till östra USA. Kopiera kvarhållning görs asynkront och det finns en kort fördröjning mellan uppdateringar av primära och sekundära platser. Repliker av diskar på den sekundära platsen är konsekvent på grundval av per disk (med fördröjning), men repliker av flera aktiva diskar kanske inte är synkroniserad med varandra. Om du vill ha konsekvent repliker över flera diskar, behövs programkonsekventa ögonblicksbilder.

Den största skillnaden mellan geo-redundant lagring och läsbehörighet geo-redundant lagring är att med geo-redundant lagring med läsbehörighet, du kan läsa den alternativa kopian när som helst. Om det finns ett problem som återger data i den primära regionen otillgänglig, gör Azure-teamet allt för att återställa åtkomsten. När den primära servern är nere, om du har läsbehörighet geo-redundant lagring aktiverad kan du komma åt data i det sekundära datacentrat. Därför, om du planerar att läsa från repliken när den primära servern inte är tillgänglig, sedan geo-redundant lagring med läsbehörighet bör övervägas.

Om det visar sig vara en betydande avbrott, kan Azure-teamet utlösa en geo-redundans och ändra de primära DNS-posterna så att den pekar till den sekundära lagringsplatsen. Om du har geo-redundant lagring eller geo-redundant lagring med läsbehörighet aktiverad, kan du nu komma åt data i den region som används för att vara sekundärt. Med andra ord om ditt lagringskonto är geo-redundant lagring och det finns ett problem, du kan komma åt den sekundära lagringsplatsen endast om det finns en geo-redundans.

Mer information finns i [vad du ska göra om ett Azure Storage-avbrott inträffar](../articles/storage/common/storage-disaster-recovery-guidance.md). 

>[!NOTE] 
>Microsoft kontrollerar om det uppstår redundans. Redundans styrs inte per lagringskonto, så att den inte är valt som enskilda kunder. Om du vill implementera haveriberedskap för specifika storage-konton eller virtuella diskar, måste du använda de tekniker som beskrivs tidigare i den här artikeln.



[1]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png
