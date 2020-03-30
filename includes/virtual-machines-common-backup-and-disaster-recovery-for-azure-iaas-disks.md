---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/05/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: aa7ddb75017a532b436b9a5cfc71d1a7c2832cb6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77179132"
---
I den här artikeln beskrivs hur du planerar för säkerhetskopiering och haveriberedskap (DR) av virtuella IaaS-datorer (VMs) och diskar i Azure. Det här dokumentet omfattar både hanterade och ohanterade diskar.

Först täcker vi de inbyggda feltoleransfunktionerna i Azure-plattformen som hjälper till att skydda mot lokala fel. Vi diskuterar sedan katastrofscenarierna som inte helt täcks av de inbyggda funktionerna. Vi visar också flera exempel på arbetsbelastningsscenarier där olika säkerhetskopierings- och DR-överväganden kan tillämpas. Vi granskar sedan möjliga lösningar för DR av IaaS diskar.

## <a name="introduction"></a>Introduktion

Azure-plattformen använder olika metoder för redundans och feltolerans för att skydda kunder från lokaliserade maskinvarufel. Lokala fel kan inkludera problem med en Azure Storage-serverdator som lagrar en del av data för en virtuell disk eller fel på en SSD eller hårddisk på den servern. Sådana isolerade maskinvarukomponentfel kan inträffa under normala åtgärder.

Azure-plattformen är utformad för att vara motståndskraftig mot dessa fel. Större katastrofer kan resultera i fel eller otillgänglighet för många lagringsservrar eller till och med ett helt datacenter. Även om dina virtuella datorer och diskar normalt skyddas från lokaliserade fel, krävs ytterligare åtgärder för att skydda din arbetsbelastning från regionomfattande oåterkalleliga fel, till exempel en större katastrof, som kan påverka din virtuella dator och diskar.

Förutom risken för plattformsfel kan problem med ett kundprogram eller data uppstå. En ny version av programmet kan till exempel oavsiktligt göra en ändring av de data som gör att den bryts. I så fall kanske du vill återställa programmet och data till en tidigare version som innehåller det senast fungerande tillståndet. Detta kräver att du underhåller regelbundna säkerhetskopior.

För regional haveriberedskap måste du säkerhetskopiera IaaS VM-diskar till en annan region.

Innan vi tittar på alternativ för säkerhetskopiering och DR ska vi sammanfatta några metoder som är tillgängliga för hantering av lokaliserade fel.

### <a name="azure-iaas-resiliency"></a>Azure IaaS återhämtningsförmåga

*Återhämtning* avser toleransen för normala fel som uppstår i maskinvarukomponenter. Återhämtning är möjligheten att återställa från fel och fortsätta att fungera. Det handlar inte om att undvika fel, utan om att reagera på fel på ett sätt som förhindrar avbrott eller dataförluster. Målet med elasticiteten är att återställa programmet till ett fullt fungerande tillstånd efter fel. Virtuella Azure-datorer och -diskar är utformade för att vara motståndskraftiga mot vanliga maskinvarufel. Låt oss titta på hur Azure IaaS-plattformen ger den här återhämtningen.

En virtuell dator består huvudsakligen av två delar: en beräkningsserver och beständiga diskar. Båda påverkar feltoleransen för en virtuell dator.

Om Azure-beräkningsvärdservern som rymmer din virtuella dator uppstår ett maskinvarufel, vilket är sällsynt, är Azure utformat för att automatiskt återställa den virtuella datorn på en annan server. Om det här scenariot startas om datorn och den virtuella datorn kommer tillbaka efter en tid. Azure identifierar automatiskt sådana maskinvarufel och kör återställningar för att säkerställa att kundens virtuella dator är tillgänglig så snart som möjligt.

När det gäller IaaS-diskar är datans hållbarhet avgörande för en beständig lagringsplattform. Azure-kunder har viktiga affärsprogram som körs på IaaS, och de är beroende av datas varaktighet. Azure designar skydd för dessa IaaS-diskar, med tre redundanta kopior av data som lagras lokalt. Dessa kopior ger hög hållbarhet mot lokala fel. Om en av maskinvarukomponenterna som innehåller disken misslyckas påverkas inte den virtuella datorn, eftersom det finns ytterligare två kopior som stöder diskbegäranden. Det fungerar bra, även om två olika maskinvarukomponenter som stöder en disk misslyckas samtidigt (vilket är sällsynt). 

För att säkerställa att du alltid underhåller tre repliker ger Azure Storage automatiskt upphov till en ny kopia av data i bakgrunden om en av de tre kopiorna blir otillgänglig. Därför bör det inte vara nödvändigt att använda RAID med Azure-diskar för feltolerans. En enkel RAID 0-konfiguration bör vara tillräcklig för att skala diskarna, om det behövs, för att skapa större volymer.

På grund av den här arkitekturen har Azure konsekvent levererat hållbarhet i företagsklass för IaaS-diskar, med en branschledande [nollprocentsfrekvens](https://en.wikipedia.org/wiki/Annualized_failure_rate)på årsbasis .

Lokaliserade maskinvarufel på beräkningsvärden eller i lagringsplattformen kan ibland resultera i tillfällig otillgänglighet för den virtuella datorn som omfattas av [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) för vm-tillgänglighet. Azure also provides an industry-leading SLA for single VM instances that use Azure premium SSDs.

För att skydda programarbetsbelastningar från driftstopp på grund av tillfällig otillgänglighet för en disk eller virtuell dator, kan kunder använda [tillgänglighetsuppsättningar](../articles/virtual-machines/windows/manage-availability.md). Två eller flera virtuella datorer i en tillgänglighetsuppsättning ger redundans för programmet. Azure skapar sedan dessa virtuella datorer och diskar i separata feldomäner med olika energi-, nätverks- och serverkomponenter.

På grund av dessa separata feldomäner påverkar lokaliserade maskinvarufel vanligtvis inte flera virtuella datorer i uppsättningen samtidigt. Att ha separata feldomäner ger hög tillgänglighet för ditt program. Det anses vara en god idé att använda tillgänglighetsuppsättningar när hög tillgänglighet krävs. Nästa avsnitt täcker katastrofåterställningsaspekten.

### <a name="backup-and-disaster-recovery"></a>Säkerhetskopiering och haveriberedskap

Katastrofåterställning är förmågan att återhämta sig från sällsynta, men större, incidenter. Dessa incidenter omfattar icke-tillfälliga, storskaliga fel, till exempel avbrott i tjänsten som påverkar en hel region. Haveriberedskap omfattar säkerhetskopiering och arkivering av data, och kan inkludera manuella åtgärder, till exempel återställa en databas från en säkerhetskopia.

Azure-plattformens inbyggda skydd mot lokaliserade fel kanske inte helt skyddar virtuella datorer/diskar om en större katastrof orsakar storskaliga avbrott. Dessa storskaliga avbrott inkluderar katastrofala händelser, till exempel om ett datacenter drabbas av en orkan, jordbävning, brand eller om det finns ett storskaligt maskinvaruenhetsfel. Dessutom kan du stöta på fel på grund av program- eller dataproblem.

För att skydda dina IaaS-arbetsbelastningar från avbrott bör du planera för redundans och ha säkerhetskopior för att aktivera återställning. För haveriberedskap bör du säkerhetskopiera på en annan geografisk plats bort från den primära platsen. Den här metoden hjälper till att säkerställa att säkerhetskopian inte påverkas av samma händelse som ursprungligen påverkade den virtuella datorn eller diskarna. Mer information finns i [Haveriberedskap för Azure-program](/azure/architecture/resiliency/disaster-recovery-azure-applications).

Dr-överväganden kan innehålla följande aspekter:

- Hög tillgänglighet: Möjligheten för programmet att fortsätta köras i felfritt tillstånd, utan betydande driftstopp. Med *felfritt tillstånd*innebär det här tillståndet att programmet är responsivt och att användare kan ansluta till programmet och interagera med det. Vissa verksamhetskritiska program och databaser kan krävas för att alltid vara tillgängliga, även när det finns fel i plattformen. För dessa arbetsbelastningar kan du behöva planera redundans för programmet och data.

- Datahållbarhet: I vissa fall är det viktigaste att se till att data bevaras om en katastrof inträffar. Därför kan du behöva en säkerhetskopia av dina data på en annan plats. För sådana arbetsbelastningar kanske du inte behöver fullständig redundans för programmet, utan bara en vanlig säkerhetskopia av diskarna.

## <a name="backup-and-dr-scenarios"></a>Scenarier för säkerhetskopiering och DR

Låt oss titta på några typiska exempel på scenarier för programarbetsbelastning och överväganden för planering för haveriberedskap.

### <a name="scenario-1-major-database-solutions"></a>Scenario 1: Större databaslösningar

Överväg en produktionsdatabasserver, till exempel SQL Server eller Oracle, som kan ha stöd för hög tillgänglighet. Kritiska produktionsprogram och användare är beroende av den här databasen. Haveriberedskapsplanen för det här systemet kan behöva stödja följande krav:

- Uppgifterna måste skyddas och återställas.
- Servern måste vara tillgänglig för användning.

Haveriberedskapsplanen kan kräva att du underhåller en replik av databasen i en annan region som en säkerhetskopia. Beroende på kraven för serverns tillgänglighet och dataåterställning kan lösningen variera från en aktiv aktiv eller aktiv-passiv replikplats till periodiska offlinesäkerhetskopior av data. Relationsdatabaser, till exempel SQL Server och Oracle, innehåller olika alternativ för replikering. För SQL Server använder du [SQL Server AlwaysOn-tillgänglighetsgrupper](https://msdn.microsoft.com/library/hh510230.aspx) för hög tillgänglighet.

NoSQL-databaser, som MongoDB, stöder också [repliker](https://docs.mongodb.com/manual/replication/) för redundans. Replikerna för hög tillgänglighet används.

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>Scenario 2: Ett kluster av redundanta virtuella datorer

Överväg en arbetsbelastning som hanteras av ett kluster av virtuella datorer som tillhandahåller redundans och belastningsutjämning. Ett exempel är ett Cassandra-kluster som distribueras i en region. Den här typen av arkitektur ger redan en hög grad av redundans inom den regionen. För att skydda arbetsbelastningen från ett fel på regional nivå bör du dock överväga att sprida klustret över två regioner eller göra regelbundna säkerhetskopior till en annan region.

### <a name="scenario-3-iaas-application-workload"></a>Scenario 3: IaaS-programarbetsbelastning

Låt oss titta på IaaS-programmets arbetsbelastning. Det här programmet kan till exempel vara en typisk produktionsarbetsbelastning som körs på en Virtuell Azure-dator. Det kan vara en webbserver eller filserver som innehåller innehållet och andra resurser på en webbplats. Det kan också vara ett specialbyggt affärsprogram som körs på en virtuell dator som lagrade data, resurser och programtillstånd på VM-diskarna. I det här fallet är det viktigt att se till att du tar säkerhetskopior regelbundet. Säkerhetskopieringsfrekvensen bör baseras på den virtuella datorns arbetsbelastning. Om programmet till exempel körs varje dag och ändrar data ska säkerhetskopieringen göras varje timme.

Ett annat exempel är en rapporteringsserver som hämtar data från andra källor och genererar aggregerade rapporter. Förlusten av den här virtuella datorn eller diskarna kan leda till förlust av rapporterna. Det kan dock vara möjligt att köra rapporteringsprocessen igen och återskapa utdata. I så fall har du egentligen inte en förlust av data, även om rapporteringsservern drabbas av en katastrof. Därför kan du ha en högre toleransnivå för att förlora en del av data på rapporteringsservern. I så fall är mindre frekventa säkerhetskopior ett alternativ för att minska kostnaderna.

### <a name="scenario-4-iaas-application-data-issues"></a>Scenario 4: Problem med IaaS-programdata

IaaS-programdataproblem är en annan möjlighet. Överväg ett program som beräknar, underhåller och betjänar viktiga kommersiella data, till exempel prisinformation. En ny version av ditt program hade en programvara bugg som felaktigt beräknat prissättningen och skadade befintliga handelsdata som betjänas av plattformen. Här är det bästa tillvägagångssättet att återgå till den tidigare versionen av programmet och data. För att aktivera detta, ta regelbundna säkerhetskopior av ditt system.

## <a name="disaster-recovery-solution-azure-backup"></a>Lösning för katastrofåterställning: Azure Backup 

[Azure Backup](https://azure.microsoft.com/services/backup/) används för säkerhetskopior och DR, och det fungerar med [hanterade diskar](../articles/virtual-machines/windows/managed-disks-overview.md) samt ohanterade diskar. Du kan skapa ett säkerhetskopieringsjobb med tidsbaserade säkerhetskopior, enkel återställning av virtuella datorer och principer för lagring av säkerhetskopiering.

Om du använder [premium-SSD-enheter,](../articles/virtual-machines/windows/disks-types.md) [hanterade diskar](../articles/virtual-machines/windows/managed-disks-overview.md)eller andra disktyper med alternativet [lokalt redundant lagring](../articles/storage/common/storage-redundancy-lrs.md) är det särskilt viktigt att göra periodiska DR-säkerhetskopior. Azure Backup lagrar data i ditt återställningstjänster-valv för långsiktig kvarhållning. Välj alternativet [geo redundant lagring](../articles/storage/common/storage-redundancy-grs.md) för valv för återställningstjänster för säkerhetskopiering. Det alternativet säkerställer att säkerhetskopior replikeras till en annan Azure-region för att skydda från regionala katastrofer.

För ohanterade diskar kan du använda den lokalt redundanta lagringstypen för IaaS-diskar, men se till att Azure Backup är aktiverat med det geouppsagbara lagringsalternativet för återställningstjänstvalvet.

> [!NOTE]
> Om du använder det [geouppsagbara lagrings-](../articles/storage/common/storage-redundancy-grs.md) eller alternativet för geo redundant lagring med [läsåtkomst](../articles/storage/common/storage-redundancy.md) för dina ohanterat diskar behöver du fortfarande konsekventa ögonblicksbilder för säkerhetskopiering och DR. Använd antingen [Azure Backup](https://azure.microsoft.com/services/backup/) eller [konsekventa ögonblicksbilder](#alternative-solution-consistent-snapshots).

 Följande tabell är en sammanfattning av de lösningar som är tillgängliga för DR.

| Scenario | Automatisk replikering | DR-lösning |
| --- | --- | --- |
| Premium SSD-diskar | Lokal[(lokalt redundant lagring)](../articles/storage/common/storage-redundancy-lrs.md) | [Azure-säkerhetskopiering](https://azure.microsoft.com/services/backup/) |
| Hanterade diskar | Lokal[(lokalt redundant lagring)](../articles/storage/common/storage-redundancy-lrs.md) | [Azure-säkerhetskopiering](https://azure.microsoft.com/services/backup/) |
| Ohanterliga lokalt redundanta lagringsdiskar | Lokal[(lokalt redundant lagring)](../articles/storage/common/storage-redundancy-lrs.md) | [Azure-säkerhetskopiering](https://azure.microsoft.com/services/backup/) |
| Ohanterat geouppsagbara lagringsdiskar | Korsregion ([geouppsagbar lagring)](../articles/storage/common/storage-redundancy-grs.md) | [Azure-säkerhetskopiering](https://azure.microsoft.com/services/backup/)<br/>[Konsekventa ögonblicksbilder](#alternative-solution-consistent-snapshots) |
| Ohanterat geo redundanta lagringsdiskar med läsåtkomst | Korsregion ([geo redundant lagring med läsåtkomst](../articles/storage/common/storage-redundancy.md)) | [Azure-säkerhetskopiering](https://azure.microsoft.com/services/backup/)<br/>[Konsekventa ögonblicksbilder](#alternative-solution-consistent-snapshots) |

Hög tillgänglighet uppfylls bäst med hjälp av hanterade diskar i en tillgänglighetsuppsättning tillsammans med Azure Backup. Om du använder ohanterat diskar kan du fortfarande använda Azure Backup för DR. Om du inte kan använda Azure Backup är det en alternativ lösning för säkerhetskopiering och DR att ta [konsekventa ögonblicksbilder](#alternative-solution-consistent-snapshots), enligt beskrivningen i ett senare avsnitt.

Dina alternativ för hög tillgänglighet, säkerhetskopiering och DR på program- eller infrastrukturnivåer kan representeras på följande sätt:

| Nivå |   Hög tillgänglighet   | Backup eller DR |
| --- | --- | --- |
| Program | SQL Server AlwaysOn | Azure Backup |
| Infrastruktur    | Tillgänglighetsuppsättning  | Geo redundant lagring med konsekventa ögonblicksbilder |

### <a name="using-azure-backup"></a>Använda Azure Backup 

[Azure Backup](../articles/backup/backup-azure-vms-introduction.md) kan säkerhetskopiera dina virtuella datorer som kör Windows eller Linux till Azure recovery services-valvet. Säkerhetskopiering och återställning av affärskritiska data kompliceras av det faktum att affärskritiska data måste säkerhetskopieras medan de program som producerar data körs. 

För att lösa problemet tillhandahåller Azure Backup programkonsekventa säkerhetskopior för Microsoft-arbetsbelastningar. Den använder volymskuggtjänsten för att säkerställa att data skrivs korrekt till lagring. För virtuella Linux-datorer är standardläget för konsekvens för säkerhetskopiering filkonsekvent, eftersom Linux inte har funktioner som motsvarar volymskuggtjänsten som i fallet med Windows. För Linux-datorer finns i [Programkonsekvent säkerhetskopiering av virtuella Azure Linux-datorer](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

![Azure Säkerhetskopieringsflöde][1]

När Azure Backup initierar ett säkerhetskopieringsjobb vid den schemalagda tiden utlöser det säkerhetskopieringstillägg som är installerat i den virtuella datorn för att ta en ögonblicksbild i tid. En ögonblicksbild tas i samordning med volymskuggtjänsten för att få en konsekvent ögonblicksbild av diskarna i den virtuella datorn utan att behöva stänga av den. Tillägget för säkerhetskopiering i den virtuella datorn tömmer alla skrivningar innan du tar en konsekvent ögonblicksbild av alla diskar. När du har tagit ögonblicksbilden överförs data av Azure Backup till säkerhetskopieringsvalvet. För att göra säkerhetskopieringsprocessen mer effektiv identifierar och överför tjänsten endast de datablock som har ändrats efter den senaste säkerhetskopieringen.

Om du vill återställa kan du visa tillgängliga säkerhetskopior via Azure Backup och sedan initiera en återställning. Du kan skapa och återställa Azure-säkerhetskopior via [Azure-portalen](https://portal.azure.com/), med hjälp av [PowerShell](../articles/backup/backup-azure-vms-automation.md)eller genom att använda [Azure CLI](/cli/azure/).

### <a name="steps-to-enable-a-backup"></a>Åtgärder för att aktivera en säkerhetskopia

Gör så här för att aktivera säkerhetskopior av dina virtuella datorer med hjälp av [Azure-portalen](https://portal.azure.com/). Det finns en viss variation beroende på ditt exakta scenario. Mer information finns i Azure [Backup-dokumentationen.](../articles/backup/backup-azure-vms-introduction.md) Azure Backup stöder även [virtuella datorer med hanterade diskar](https://azure.microsoft.com/blog/azure-managed-disk-backup/).

1.  Skapa ett återhämtningstjänstvalv för en virtuell dator:

    a. I [Azure-portalen](https://portal.azure.com/)bläddrar du bland **alla resurser** och hittar **Recovery Services-valv**.

    b. På **Recovery Services-valv-menyn** klickar du på **Lägg till** och följer stegen för att skapa ett nytt valv i samma region som den virtuella datorn. Om den virtuella datorn till exempel finns i regionen västra USA väljer du västra USA för valvet.

1.  Verifiera lagringsreplikeringen för det nyskapade valvet. Öppna valvet under **Recovery Services-valv** och gå till **Properties** > **Backup Configuration** > **Update**. Kontrollera att alternativet **för geo redundant lagring** är markerat som standard. Det här alternativet säkerställer att ditt valv replikeras automatiskt till ett sekundärt datacenter. Ditt valv i västra USA replikeras till exempel automatiskt till östra USA.

1.  Konfigurera säkerhetskopieringsprincipen och välj den virtuella datorn från samma användargränssnitt.

1.  Kontrollera att säkerhetskopieringsagenten är installerad på den virtuella datorn. Om din virtuella dator skapas med hjälp av en Azure-galleriavbildning är säkerhetskopieringsagenten redan installerad. Annars (det vill säga om du använder en anpassad avbildning) använder du instruktionerna för att [installera VM-agenten på en virtuell dator](../articles/backup/backup-azure-arm-vms-prepare.md#install-the-vm-agent).

1.  När föregående steg har slutförts körs säkerhetskopieringen med jämna mellanrum enligt vad som anges i säkerhetskopieringsprincipen. Om det behövs kan du utlösa den första säkerhetskopian manuellt från instrumentpanelen i valvet på Azure-portalen.

Information om hur du automatiserar Azure Backup med skript finns i [PowerShell-cmdletar för säkerhetskopiering av virtuella datorer](../articles/backup/backup-azure-vms-automation.md).

### <a name="steps-for-recovery"></a>Steg för återställning

Om du behöver reparera eller återskapa en virtuell dator kan du återställa den virtuella datorn från någon av återställningspunkterna för säkerhetskopiering i valvet. Det finns ett par olika alternativ för att utföra återställningen:

-   Du kan skapa en ny virtuell dator som en point-in-time-representation av den säkerhetskopierade virtuella datorn.

-   Du kan återställa diskarna och sedan använda mallen för den virtuella datorn för att anpassa och återskapa den återställda virtuella datorn.

Mer information finns i instruktionerna för att [använda Azure-portalen för att återställa virtuella datorer](../articles/backup/backup-azure-arm-restore-vms.md). Det här dokumentet förklarar också de specifika stegen för att återställa säkerhetskopierade virtuella datorer till ett parkopplat datacenter med hjälp av ditt geosertaundära säkerhetskopieringsvalv om det finns en katastrof i det primära datacentret. I så fall använder Azure Backup tjänsten Compute från den sekundära regionen för att skapa den återställda virtuella datorn.

Du kan också använda PowerShell för [att skapa en ny virtuell dator från återställda diskar](../articles/backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="alternative-solution-consistent-snapshots"></a>Alternativ lösning: Konsekventa ögonblicksbilder

Om du inte kan använda Azure Backup kan du implementera din egen säkerhetskopieringsmekanism med hjälp av ögonblicksbilder. Det är komplicerat att skapa konsekventa ögonblicksbilder för alla diskar som används av en virtuell dator och sedan replikera dessa ögonblicksbilder till en annan region. Därför anser Azure att använda säkerhetskopieringstjänsten som ett bättre alternativ än att skapa en anpassad lösning.

Om du använder geo redundant lagring/geo redundant lagring för diskar med läsåtkomst replikeras ögonblicksbilder automatiskt till ett sekundärt datacenter. Om du använder lokalt redundant lagring för diskar måste du replikera data själv. Mer information finns i [Säkerhetskopiera Azure-ohanterat VM-diskar med inkrementella ögonblicksbilder](../articles/virtual-machines/windows/incremental-snapshots.md).

En ögonblicksbild är en representation av ett objekt vid en viss tidpunkt. En ögonblicksbild medför fakturering för den inkrementella storleken på de data som den innehåller. Mer information finns i [Skapa en blob-ögonblicksbild](../articles/storage/blobs/storage-blob-snapshots.md).

### <a name="create-snapshots-while-the-vm-is-running"></a>Skapa ögonblicksbilder medan den virtuella datorn körs

Även om du kan ta en ögonblicksbild när som helst, om den virtuella datorn körs, finns det fortfarande data som strömmas till diskarna. Ögonblicksbilderna kan innehålla partiella åtgärder som var under flygning. Om det finns flera diskar inblandade kan ögonblicksbilder av olika diskar ha inträffat vid olika tidpunkter. Dessa scenarier kan orsaka att ögonblicksbilderna inte samordnas. Denna brist på samordning är särskilt problematisk för stripe-volymer vars filer kan vara skadade om ändringar gjordes under säkerhetskopieringen.

För att undvika den här situationen måste säkerhetskopieringen implementera följande steg:

1.  Frys alla diskar.

1.  Rensa alla väntande skrivningar.

1.  [Skapa en blob-ögonblicksbild](../articles/storage/blobs/storage-blob-snapshots.md) för alla diskar.

Vissa Windows-program, som SQL Server, tillhandahåller en samordnad säkerhetskopieringsmekanism via en volymskuggtjänst för att skapa programkonsekventa säkerhetskopior. På Linux kan du använda ett verktyg som *fsfreeze* för att samordna diskarna. Det här verktyget tillhandahåller filkonsekventa säkerhetskopior, men inte programkonsekventa ögonblicksbilder. Den här processen är komplex, så du bör överväga att använda [Azure Backup](../articles/backup/backup-azure-vms-introduction.md) eller en säkerhetskopieringslösning från tredje part som redan implementerar den här proceduren.

Den föregående processen resulterar i en samling samordnade ögonblicksbilder för alla VM-diskar, som representerar en specifik point-in-time-vy av den virtuella datorn. Detta är en återställningspunkt för säkerhetskopiering för den virtuella datorn. Du kan upprepa processen med schemalagda intervall för att skapa periodiska säkerhetskopior. Se [Kopiera säkerhetskopiorna till en annan region](#copy-the-snapshots-to-another-region) för steg för att kopiera ögonblicksbilder till en annan region för DR.

### <a name="create-snapshots-while-the-vm-is-offline"></a>Skapa ögonblicksbilder medan den virtuella datorn är offline

Ett annat alternativ för att skapa konsekventa säkerhetskopior är att stänga av den virtuella datorn och ta blob ögonblicksbilder av varje disk. Det är enklare att ta ögonblicksbilder av blob-bloss än att koordinera ögonblicksbilder av en virtuell dator som körs, men det kräver några minuters driftstopp.

1. Stäng av den virtuella datorn.

1. Skapa en ögonblicksbild av varje virtuell hårddiskblob, vilket bara tar några sekunder.

    Om du vill skapa en ögonblicksbild kan du använda [PowerShell](../articles/storage/common/storage-powershell-guide-full.md), [Azure Storage REST API](https://msdn.microsoft.com/library/azure/ee691971.aspx), Azure [CLI](/cli/azure/)eller något av Azure Storage-klientbiblioteken, till exempel [lagringsklientbiblioteket för .NET](https://msdn.microsoft.com/library/azure/hh488361.aspx).

1. Starta den virtuella datorn, som avslutar driftstoppet. Normalt avslutas hela processen inom några minuter.

Den här processen ger en samling konsekventa ögonblicksbilder för alla diskar, vilket ger en återställningspunkt för säkerhetskopiering för den virtuella datorn.

### <a name="copy-the-snapshots-to-another-region"></a>Kopiera ögonblicksbilderna till en annan region

Det kanske inte räcker att skapa ögonblicksbilder som inte är tillräckliga för DR. Du måste också replikera säkerhetskopieringarna av ögonblicksbilder till en annan region.

Om du använder geoupptundret lagring eller geo redundant lagring med läsåtkomst för diskarna replikeras ögonblicksbilderna automatiskt till den sekundära regionen. Det kan ta några minuter fördröjning innan replikeringen. Om det primära datacentret går ned innan ögonblicksbilderna slutför replikeringen kan du inte komma åt ögonblicksbilderna från det sekundära datacentret. Sannolikheten för detta är liten.

> [!NOTE]
> Att bara ha diskarna i ett geo redundant lagrings- eller läsåtkomst-geo redundant lagringskonto skyddar inte den virtuella datorn från katastrofer. Du måste också skapa koordinerade ögonblicksbilder eller använda Azure Backup. Detta krävs för att återställa en virtuell dator till ett konsekvent tillstånd.

Om du använder lokalt redundant lagring måste du kopiera ögonblicksbilderna till ett annat lagringskonto direkt efter att du har skapat ögonblicksbilden. Kopieringsmålet kan vara ett lokalt redundant lagringskonto i en annan region, vilket resulterar i att kopian finns i en fjärrregion. Du kan också kopiera ögonblicksbilden till ett geo redundant lagringskonto med läsåtkomst i samma region. I det här fallet replikeras ögonblicksbilden lättjefullt till den sekundära fjärrregionen. Säkerhetskopian skyddas från katastrofer på den primära platsen efter att kopieringen och replikeringen är klar.

Om du vill kopiera inkrementella ögonblicksbilder för DR effektivt läser du instruktionerna i [Säkerhetskopiera azure-ohanterat VM-diskar med inkrementella ögonblicksbilder](../articles/virtual-machines/windows/incremental-snapshots.md).

![Säkerhetskopiera azure ohanterat VM-diskar med inkrementella ögonblicksbilder][2]

### <a name="recovery-from-snapshots"></a>Återställning från ögonblicksbilder

Om du vill hämta en ögonblicksbild kopierar du den för att skapa en ny blob. Om du kopierar ögonblicksbilden från det primära kontot kan du kopiera ögonblicksbilden till ögonblicksbildens basblob. Den här processen återställer disken till ögonblicksbilden. Den här processen kallas att befordra ögonblicksbilden. Om du kopierar säkerhetskopian av ögonblicksbilden från ett sekundärt konto måste du kopiera den till ett primärt konto när det gäller ett geoundant lagringskonto med läsåtkomst. Du kan kopiera en ögonblicksbild [med powershell](../articles/storage/common/storage-powershell-guide-full.md) eller med hjälp av verktyget AzCopy. Mer information finns i [Överföra data med kommandoradsverktyget AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy).

För virtuella datorer med flera diskar måste du kopiera alla ögonblicksbilder som ingår i samma samordnade återställningspunkt. När du har kopierat ögonblicksbilderna till skrivbara VHD-blobbar kan du använda blobbar för att återskapa den virtuella datorn med hjälp av mallen för den virtuella datorn.

## <a name="other-options"></a>Andra alternativ

### <a name="sql-server"></a>SQL Server

SQL Server som körs i en virtuell dator har sina egna inbyggda funktioner för att säkerhetskopiera din SQL Server-databas till Azure Blob-lagring eller en filresurs. Om lagringskontot är geo redundant lagring eller geo redundant lagring med läsåtkomst kan du komma åt dessa säkerhetskopior i lagringskontots sekundära datacenter i händelse av en katastrof, med samma begränsningar som tidigare diskuterats. Mer information finns i [Säkerhetskopiera och återställa för SQL Server i virtuella Azure-datorer](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md). Förutom säkerhetskopiering och återställning kan [sql Server AlwaysOn-tillgänglighetsgrupper](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md) underhålla sekundära repliker av databaser. Denna förmåga minskar avsevärt katastrofåterställningstiden.

## <a name="other-considerations"></a>Andra överväganden

Den här artikeln har diskuterat hur du säkerhetskopierar eller tar ögonblicksbilder av dina virtuella datorer och deras diskar för att stödja haveriberedskap och hur du använder dessa säkerhetskopior eller ögonblicksbilder för att återställa dina data. Med Azure Resource Manager-modellen använder många mallar för att skapa sina virtuella datorer och andra infrastrukturer i Azure. Du kan använda en mall för att skapa en virtuell dator som har samma konfiguration varje gång. Om du använder anpassade bilder för att skapa dina virtuella datorer måste du också se till att dina avbildningar skyddas med hjälp av ett geoundant lagringskonto med läsåtkomst för att lagra dem.

Därför kan säkerhetskopieringen vara en kombination av två saker:

- Säkerhetskopiera data (diskar).
- Säkerhetskopiera konfigurationen (mallar och anpassade avbildningar).

Beroende på vilket säkerhetskopieringsalternativ du väljer kan du behöva hantera säkerhetskopian av både data och konfiguration, annars kan säkerhetskopieringstjänsten hantera allt detta åt dig.

## <a name="appendix-understanding-the-impact-of-data-redundancy"></a>Tillägg: Förståelse av effekterna av dataredundans

För lagringskonton i Azure finns det tre typer av dataredundans som du bör tänka på när det gäller haveriberedskap: lokalt redundant, geoundant eller geoundundant med läsåtkomst. 

Lokalt redundant lagring behåller tre kopior av data i samma datacenter. När den virtuella datorn skriver data uppdateras alla tre kopiorna innan lyckades returneras till anroparen, så att du vet att de är identiska. Disken är skyddad från lokala fel, eftersom det är osannolikt att alla tre kopiorna påverkas samtidigt. När det gäller lokalt redundant lagring finns det ingen georedundans, så disken skyddas inte från katastrofala fel som kan påverka ett helt datacenter eller en lagringsenhet.

Med geoupptundret lagring och geouppundret lagring med läsåtkomst lagras tre kopior av dina data i den primära regionen som du har valt. Ytterligare tre kopior av dina data lagras i en motsvarande sekundär region som anges av Azure. Om du till exempel lagrar data i västra USA replikeras data till östra USA. Kopiera kvarhållning görs asynkront och det finns en liten fördröjning mellan uppdateringar till de primära och sekundära platserna. Repliker av diskarna på den sekundära platsen är konsekventa per disk (med fördröjningen), men repliker av flera aktiva diskar kanske inte är synkroniserade med varandra. För att ha konsekventa repliker över flera diskar behövs konsekventa ögonblicksbilder.

Den största skillnaden mellan geo-redundant lagring och läsåtkomst geo-redundant lagring är att med läsåtkomst geo-redundant lagring, kan du läsa den sekundära kopian när som helst. Om det finns ett problem som återger data i den primära regionen otillgängliga, gör Azure-teamet allt för att återställa åtkomsten. Medan den primära är nere, om du har läsåtkomst geo-redundant lagring aktiverad, kan du komma åt data i det sekundära datacentret. Om du planerar att läsa från repliken medan den primära är otillgänglig bör därför läsåtkomst geo redundant lagring övervägas.

Om det visar sig vara ett betydande avbrott kan Azure-teamet utlösa en geo-redundans och ändra de primära DNS-posterna så att de pekar på sekundär lagring. Om du nu har aktiverat geo redundant lagring eller geo redundant lagring med läsåtkomst kan du komma åt data i den region som tidigare var sekundär. Med andra ord, om ditt lagringskonto är geo-redundant lagring och det finns ett problem, kan du komma åt den sekundära lagringen endast om det finns en geo-redundans.

Mer information finns i [Vad du gör om ett avbrott i Azure Storage inträffar?](../articles/storage/common/storage-disaster-recovery-guidance.md).

>[!NOTE] 
>Microsoft styr om en redundans inträffar. Redundans styrs inte per lagringskonto, så det är inte bestämt av enskilda kunder. Om du vill implementera haveriberedskap för specifika lagringskonton eller virtuella datordiskar måste du använda de tekniker som beskrivits tidigare i den här artikeln.

[1]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png
