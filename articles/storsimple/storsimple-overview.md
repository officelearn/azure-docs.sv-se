---
title: StorSimple 8000-serien, lösningsöversikt | Microsoft Docs
description: Beskriver StorSimple lagringsnivåer, enhet, virtuell enhet, tjänster och lagringshantering och introducerar viktiga termer som används i StorSimple.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 7144d218-db21-4495-88fb-e3b24bbe45d1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/10/2017
ms.author: v-sharos@microsoft.com
ms.openlocfilehash: 946b5a568d98367daec0244968b962618f22ae76
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2018
ms.locfileid: "42060669"
---
# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>StorSimple 8000-serien: en hybridlösning för molnlagring
## <a name="overview"></a>Översikt
Välkommen till Microsoft Azure StorSimple, en integrerad lagringslösning som hanterar lagringsuppgifter mellan lokala enheter och molnlagring i Microsoft Azure. StorSimple är en effektiv, kostnadseffektiv och hanterbar enkelt storage area network (SAN) lösning som eliminerar många av de problem och kostnader som är förknippade med företagets lagring och dataskydd. Den använder egna enhet i StorSimple 8000-serien, kan integreras med molntjänster och tillhandahåller en uppsättning hanteringsverktyg för en sömlös vy av hela företagslagringen inklusive molnlagring. (Distributionsinformationen för StorSimple som publiceras på webbplatsen Microsoft Azure gäller endast StorSimple 8000 series-enheter. Om du använder en enhet i StorSimple 5000/7000-serien, går du till [StorSimple hjälp](http://onlinehelp.storsimple.com/).)

StorSimple använder [lagringsnivåer](#automatic-storage-tiering) att hantera lagrade data över olika lagringsmedia. Aktuella arbetsminnet är lagras lokalt på solid state-hårddiskar (SSD), data som används mindre ofta lagras på hårddiskar (HDD) och arkivdata skickas till molnet. StorSimple använder dessutom avduplicering och komprimering för att minska mängden lagring som data förbrukar. Mer information går du till [Avduplicering och komprimering](#deduplication-and-compression). Mer information om andra viktiga termer och begrepp som används i dokumentationen för StorSimple 8000-serien går du till [StorSimple terminologi](#storsimple-terminology) i slutet av den här artikeln.

Förutom lagringshantering, StorSimple funktioner för dataskydd som gör att du kan skapa på begäran och schemalagda säkerhetskopieringar och lagra dem lokalt eller i molnet. Säkerhetskopior tas i form av inkrementella ögonblicksbilder, vilket innebär att de kan skapas och återställa snabbt. Ögonblicksbilder av molnet kan vara ytterst viktigt i katastrofåterställning eftersom de ersätter sekundära lagringssystem (till exempel bandsäkerhetskopiering) och att du kan återställa data till ditt datacenter eller till en annan om det behövs.

![ikon för video](./media/storsimple-overview/video_icon.png) Titta på videon och få en snabb introduktion till Microsoft Azure StorSimple.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/StorSimple-Hybrid-Cloud-Storage-Solution/player]

## <a name="why-use-storsimple"></a>Varför använda StorSimple?
I följande tabell beskrivs några av de viktigaste fördelarna med Microsoft Azure StorSimple tillhandahåller.

| Funktion | Fördelar |
| --- | --- |
| Transparent integration |Använder iSCSI-protokollet för att länka loggas data lagringsutrymmen. Detta garanterar att data som lagras i molnet i datacentret, eller visas som ska lagras på en plats på fjärrservrar. |
| Lägre lagringskostnader |Allokerar tillräckligt med lokal eller molnlagring aktuella behov och utökar molnlagring vid behov. Det ytterligare minskar lagringskrav och pengar genom att eliminera redundant versioner av samma data (deduplicering) och genom att använda komprimering. |
| Förenklad lagringshantering |Tillhandahåller system verktyg för fjärrserveradministration för att konfigurera och hantera data som lagras lokalt på en fjärransluten server och i molnet. Dessutom kan du hantera säkerhetskopiering och återställa funktioner från en snapin-modulen Microsoft Management Console (MMC).|
| Förbättrad haveriberedskap och efterlevnad |Kräver inte utökade återställningstid. I stället återställer data när det behövs. Det innebär att normal drift kan fortsätta med minimala störningar. Dessutom kan du konfigurera principer för att ange scheman för säkerhetskopiering och kvarhållning av data. |
| Lätt att flytta data |Data som överförs till Microsoft Azure molntjänster kan nås från andra platser för återställning och migrering. Du kan dessutom använda StorSimple för att konfigurera StorSimple Cloud Appliances på virtuella datorer (VM) som körs i Microsoft Azure. De virtuella datorerna kan sedan använda virtuella enheter för att få åtkomst till lagrade data för test- eller återställning. |
| Verksamhetskontinuitet |Tillåter StorSimple 5000-7000-serien användare att migrera sina data till en enhet i StorSimple 8000-serien. |
| Tillgänglighet i Azure Government-portalen |StorSimple är tillgängliga i Azure Government-portalen. Mer information finns i [distribuera din lokala StorSimple-enhet i Government Portal](storsimple-8000-deployment-walkthrough-gov-u2.md). |
| Dataskydd och tillgänglighet |StorSimple 8000-serien har stöd för Zonredundant lagring (ZRS), förutom lokalt Redundant lagring (LRS) och Geo-redundant lagring (GRS). Referera till [den här artikeln om redundansalternativ för Azure Storage](https://azure.microsoft.com/documentation/articles/storage-redundancy/) ZRS-information. |
| Stöd för verksamhetskritiska program |StorSimple kan du identifiera rätt volymer som lokalt fixerade vilket i sin tur garanterar att data som krävs av kritiska program inte är nivåindelad till molnet. Lokalt fixerade volymer omfattas inte av molnet fördröjningar eller problem med nätverksanslutningen. Mer information om lokalt fixerade volymer finns i [använda StorSimple Device Manager-tjänsten för att hantera volymer](storsimple-8000-manage-volumes-u2.md). |
| Låg fördröjning och höga prestanda |Du kan skapa molninstallationer som drar nytta av hög prestanda, låg latens funktioner i Azure premium storage. Läs mer om StorSimple premium-molninstallationer [distribuera och hantera en StorSimple-Molninstallation i Azure](storsimple-8000-cloud-appliance-u2.md). |


## <a name="storsimple-components"></a>StorSimple-komponenter
Microsoft Azure StorSimple-lösningen innehåller följande komponenter:

* **Microsoft Azure StorSimple-enheten** – en lokal hybridlagringsmatrisen som innehåller SSD och HDD, tillsammans med funktioner för automatisk redundans och redundanta styrenheter. Styrenheterna hantera lagring lagringsnivåer, placera för närvarande används (eller frekvent) data på en lokal lagringsenhet (i enheten eller lokala servrar), vid förflyttning av mindre ofta använda data till molnet.
* **StorSimple Cloud Appliance** – även känt som StorSimple Virtual Appliance, detta är en programvaruversion av StorSimple-enheten som replikerar arkitekturen och de flesta funktionerna i den fysiska hybrid lagringsenheten. StorSimple-Molninstallationen körs på en enskild nod i en Azure virtuell dator. Virtuella Premium-enheter, som kan utnyttja Azure premium storage, är tillgängliga i uppdatering 2 och senare.
* **StorSimple Device Manager-tjänsten** – ett tillägg till Azure portal som kan du hantera en StorSimple-enhet eller en StorSimple-Molninstallationen från ett enda webbgränssnitt. Du kan använda StorSimple Device Manager-tjänsten för att skapa och hantera tjänster, visa och hantera enheter, Visa aviseringar, hantera volymer, och visa och hantera säkerhetskopieringsprinciper och säkerhetskopieringskatalogen.
* **Windows PowerShell för StorSimple** – ett kommandoradsgränssnitt som du kan använda för att hantera StorSimple-enheten. Windows PowerShell för StorSimple har funktioner som gör att du kan registrera din StorSimple-enhet, konfigurera nätverksgränssnittet på din enhet, installera vissa typer av uppdateringar, felsöka enheten genom att gå till supportsession och ändra enheten tillstånd. Du kan komma åt Windows PowerShell för StorSimple genom att ansluta till seriekonsolen eller genom att använda Windows PowerShell-fjärrkommunikation.
* **Azure PowerShell StorSimple-cmdletar** – en samling av Windows PowerShell-cmdletar som gör det möjligt att automatisera uppgifter för servicenivå och migrering från kommandoraden. Mer information om Azure PowerShell-cmdlets för StorSimple går du till den [cmdlet-referens för](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0#azure).
* **StorSimple Snapshot Manager** – en MMC-snapin-modul som använder volymgrupper och tjänsten Windows Volume Shadow Copy för att skapa programkonsekventa säkerhetskopior. Du kan dessutom använda StorSimple Snapshot Manager för att skapa scheman för säkerhetskopiering och kloning eller återställa volymer.
* **StorSimple Adapter för SharePoint** – ett verktyg som transparent utökar Microsoft Azure StorSimple-lagring och dataskydd till SharePoint Server anläggningar samtidigt kan visas och hanterbar StorSimple-lagring från SharePoint Central Administrationsportalen.

Diagrammet nedan innehåller en översikt över Microsoft Azure StorSimple-arkitektur och komponenter.

![StorSimple-arkitektur](./media/storsimple-overview/overview-big-picture.png)

I följande avsnitt beskrivs var och en av dessa komponenter i detalj och förklarar hur lösningen ordnar data allokerar lagring och underlättar lagringshantering och dataskydd. Det sista avsnittet innehåller definitioner för några av de viktiga termer och begrepp för StorSimple-komponenter och deras hantering.

## <a name="storsimple-device"></a>StorSimple-enhet
Microsoft Azure StorSimple-enheten är en lokal hybridlagringsmatrisen som primär lagring och iSCSI-åtkomst till data som lagras på den. Den hanterar kommunikation med molnlagring och bidrar till att säkerställa säkerheten och sekretessen för alla data som lagras på Microsoft Azure StorSimple-lösningen.

StorSimple-enheten inkluderar SSD och hårddiskar HDD, samt stöd för klustring och automatisk växling vid fel. Den innehåller en delad processor, delad lagring och två speglade styrenheter. Varje styrenhet ger följande:

* Anslutning till en värddator
* Upp till sex nätverksportar att ansluta till det lokala nätverket (LAN)
* Övervakning av maskinvara
* Icke-obeständigt minne (NVRAM), som lagrar information även om strömmen bryts
* Klustermedveten uppdatering för att hantera programuppdateringar på servrar i ett failover-kluster så att uppdateringarna har minimal eller ingen effekt på tjänstens tillgänglighet
* Klustertjänst som fungerar som ett backend-kluster, vilket ger hög tillgänglighet och minimera eventuella negativa effekter som kan uppstå om en Hårddisk eller SSD misslyckas eller är i offlineläge

Endast en domänkontrollant är aktiv när som helst i tid. Om den aktiva kontrollenheten misslyckas kan aktiveras den andra styrenheten automatiskt.

Mer information går du till [StorSimple maskinvarukomponenter och status](storsimple-8000-monitor-hardware-status.md).

## <a name="storsimple-cloud-appliance"></a>StorSimple Cloud Appliance
Du kan använda StorSimple för att skapa en molninstallation som replikerar arkitekturen och funktionerna i den fysiska hybrid lagringsenheten. StorSimple Cloud Appliance (även kallat StorSimple Virtual Appliance) körs på en enskild nod i en Azure virtuell dator. (En molninstallation kan bara skapas på en Azure-dator. Du kan inte skapa en på en StorSimple-enhet eller en lokal server.)

Molninstallationen har följande funktioner:

* Den fungerar som en fysisk installation och kan erbjuda ett iSCSI-gränssnitt för virtuella datorer i molnet.
* Du kan skapa ett obegränsat antal molninstallationer i molnet och aktivera dem och inaktivera efter behov.
* Det kan hjälpa att simulera i lokala miljöer i haveriberedskap, utveckling och test-scenarier och kan hjälpa dig med hämtning på objektnivå från säkerhetskopior.

StorSimple-Molninstallationen finns i två modeller: enheten 8010 (kallades tidigare 1100 modellen) och 8020-enheten. 8010-enhet har en maxkapacitet på 30 TB. Enheten 8020 som utnyttjar Azure premium storage har en maxkapacitet på 64 TB. (I lokala nivåerna Azure premium storage lagrar data på SSD: er medan standardlagring lagrar data på hårddiskar.) Observera att du måste ha ett Azure premium storage-konto du använder premium storage. Mer information om premium-lagring, går du till [Premium Storage: lagring med höga prestanda för Azure-Datorbelastningar](../virtual-machines/windows/premium-storage.md).

Mer information om StorSimple-Molninstallationen går du till [distribuera och hantera en StorSimple-Molninstallation i Azure](storsimple-8000-cloud-appliance-u2.md).

## <a name="storsimple-device-manager-service"></a>StorSimple Device Manager-tjänst
Microsoft Azure StorSimple ger ett webbaserat användargränssnitt (StorSimple Device Manager-tjänsten) som låter dig centralt hantera datacenter och molnlagring. Du kan använda StorSimple Device Manager-tjänsten för att utföra följande uppgifter:

* Konfigurera systeminställningar för StorSimple-enheter.
* Konfigurera och hantera säkerhetsinställningar för StorSimple-enheter.
* Konfigurera molnautentiseringsuppgifter och egenskaper.
* Konfigurera och hantera volymer på en server.
* Konfigurera volymgrupper.
* Säkerhetskopiera och återställa data.
* Övervaka prestanda.
* Granska inställningarna och identifierar möjliga problem.

Du kan använda StorSimple Device Manager-tjänsten för att utföra alla administrationsuppgifter utom de som kräver system ned tid, till exempel inledande konfiguration och installation av uppdateringar.

Mer information går du till [använda StorSimple Device Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

## <a name="windows-powershell-for-storsimple"></a>Windows PowerShell för StorSimple
Windows PowerShell för StorSimple tillhandahåller ett kommandoradsgränssnitt som du kan använda för att skapa och hantera Microsoft Azure StorSimple-tjänsten och konfigurera och övervaka StorSimple-enheter. Det är ett kommandoradsgränssnitt för Windows PowerShell-baserade, som innehåller dedicerade cmdlets för att hantera din StorSimple-enhet. Windows PowerShell för StorSimple har funktioner som gör att du kan:

* Registrera en enhet.
* Konfigurera nätverksgränssnittet på en enhet.
* Installera vissa typer av uppdateringar.
* Felsöka enheten genom att gå till supportsessionen.
* Ändra enhetens tillstånd.

Du kan komma åt Windows PowerShell för StorSimple från en seriell konsol (på en värddator som är anslutna direkt till enheten) eller via en fjärranslutning med hjälp av Windows PowerShell-fjärrkommunikation. Observera att vissa Windows PowerShell för StorSimple-aktiviteter, till exempel initiala enhetsregistreringen kan bara utföras på seriekonsolen.

Mer information går du till [Använd Windows PowerShell för StorSimple att administrera din enhet](storsimple-8000-windows-powershell-administration.md).

## <a name="azure-powershell-storsimple-cmdlets"></a>Azure PowerShell StorSimple-cmdletar
StorSimple för Azure PowerShell-cmdlet: ar är en samling av Windows PowerShell-cmdletar som gör det möjligt att automatisera uppgifter för servicenivå och migrering från kommandoraden. Mer information om Azure PowerShell-cmdlets för StorSimple går du till den [cmdlet-referens för](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0).

## <a name="storsimple-snapshot-manager"></a>StorSimple Snapshot Manager
StorSimple Snapshot Manager är en snapin-modul i Microsoft Management Console (MMC) som du kan använda för att skapa enhetliga, point-in-time-säkerhetskopior av lokala och data i molnet. Snapin-modulen körs på en Windows Server – baserad värd. Du kan använda StorSimple Snapshot Manager till:

* Konfigurera, säkerhetskopiera och ta bort volymer.
* Konfigurera volymgrupper så säkerhetskopierade data är konsekventa.
* Hantera principer för säkerhetskopiering så att data säkerhetskopieras enligt ett förutbestämt schema och lagras på en angiven plats (lokalt eller i molnet).
* Återställa volymer och enskilda filer.

Säkerhetskopieringar som avbildas som ögonblicksbilder som registrera bara ändringarna sedan den senaste ögonblicksbilden togs och kräver mycket mindre lagringsutrymme än fullständiga säkerhetskopieringar. Du kan skapa scheman för säkerhetskopiering eller göra säkerhetskopior av omedelbart efter behov. Du kan dessutom använda StorSimple Snapshot Manager för att upprätta principer för kvarhållning som styr hur många ögonblicksbilder kommer att sparas. Om du senare behöver återställa data från en säkerhetskopiering, StorSimple Snapshot Manager kan välja du från katalogen i lokala eller ögonblicksbilder av molnet. 

Om en olycka inträffar, eller om du behöver återställa data för en annan orsak, återställer StorSimple Snapshot Manager det stegvis när det behövs. Återställning av data kräver inte att stänga av hela systemet medan du återställa en fil, ersätta utrustning eller flytta åtgärder till en annan plats.

Mer information går du till [vad är StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>StorSimple-adapter för SharePoint
Microsoft Azure StorSimple innehåller StorSimple Adapter för SharePoint, en valfri komponent som transparent utökar funktionerna för StorSimple-lagring och data protection till SharePoint-servergrupper. Kortet fungerar med en fjärransluten Blob Storage (RBS)-provider och funktionen SQL Server RBS så att du kan flytta BLOB-objekt till en server som backas upp av Microsoft Azure StorSimple-systemet. Microsoft Azure StorSimple sedan lagrar BLOB-data lokalt eller i molnet, baserat på användning.

StorSimple Adapter för SharePoint hanteras i Central Administration av SharePoint-portalen. Följaktligen SharePoint-hantering fortfarande centraliserad och all lagring verkar vara i SharePoint-servergruppen.

Mer information går du till [StorSimple Adapter för SharePoint](storsimple-adapter-for-sharepoint.md). 

## <a name="storage-management-technologies"></a>Tekniker för hantering av lagring
Förutom den dedikerade StorSimple-enheten, virtuella enheten och andra komponenter använder Microsoft Azure StorSimple följande programvara tekniker att ge snabb åtkomst till data och för att minska lagringsanvändningen:

* [Automatisk lagringsnivåer](#automatic-storage-tiering) 
* [Tunn allokering](#thin-provisioning) 
* [Avduplicering och komprimering](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>Automatisk lagringsnivåer
Microsoft Azure StorSimple ordnar automatiskt data i logiska nivåer, baserat på aktuell användning, ålder och relation till andra data. Data som är mest aktiva lagras lokalt, medan mindre aktiva och inaktiva data automatiskt migreras till molnet. Följande diagram illustrerar den här metoden för lagring.

![Lagringsnivåer för StorSimple](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

Om du vill aktivera snabb åtkomst, lagrar StorSimple mycket aktiv data (frekventa data) på SSD-enheter i StorSimple-enheten. Den lagrar data som används ibland (värmt data) på hårddiskar i enheten eller på servrar på datacentret. Inaktiva data säkerhetskopierade data flyttas och data kvar för arkivering eller efterlevnadssyften till molnet. 

> [!NOTE]
> I uppdatering 2 eller senare, du kan ange en lokalt Fäst volym, i vilket fall data finns kvar på den lokala enheten och är inte nivåer till molnet. 


StorSimple justerar och samlar data och lagring tilldelningar som användningsmönster ändras. Viss information kan till exempel bli mindre aktiva över tid. Så fort de blir progressivt mindre aktiva, har den migrerats från SSD: er till hårddiskar och sedan till molnet. Om samma data blir aktiva igen kan flyttas den tillbaka till lagringsenheten.

Lagringsnivåer lagringsprocessen sker enligt följande:

1. En administratör ställer in ett Microsoft Azure cloud storage-konto.
2. Administratören använder seriekonsolen och StorSimple Device Manager-tjänsten (som körs i Azure portal) att konfigurera enheten och servern, skapa volymer och dataskyddsprinciper. Lokala datorer (till exempel filservrar) använda den Internet Small Computer System Interface (iSCSI) för att få åtkomst till StorSimple-enheten.
3. Först lagrar StorSimple data på enheten snabba SSD-nivån.
4. När SSD-nivån närmar sig kapacitet, StorSimple deduplicates och komprimerar de äldsta datablock och flyttar dem till hårddisknivån.
5. Som nivåkapacitet metoder HDD StorSimple krypterar de äldsta datablock och skickar dem på ett säkert sätt till Microsoft Azure storage-konto via HTTPS.
6. Microsoft Azure skapar flera replikeringar av data i sitt datacenter och i ett fjärranslutet datacenter att säkerställa att data kan återställas om en olycka inträffar.
7. När servern begär data som lagras i molnet, StorSimple tillbaka det smidigt och lagrar en kopia på SSD-nivån av StorSimple-enhet.

#### <a name="how-storsimple-manages-cloud-data"></a>Hur StorSimple hanterar data i molnet

StorSimple deduplicates kunddata i alla ögonblicksbilder och den primära data (data som skrivits av värdar). Deduplicering är perfekt för lagringseffektivitet, gör frågan ”vad är i molnet” komplicerat. Den primära nivåindelade data och ögonblicksbilddata överlappar varandra. Ett enda segment av data i molnet kan användas som nivåindelade primärdata och också refereras av flera ögonblicksbilder. Varje ögonblicksbild av molndata säkerställer att en kopia av alla point-in-time-data är låst till molnet tills den ögonblicksbilden tas bort.

Data är bara bort från molnet när det finns några referenser till dessa data. Exempelvis kan vi börjar med en molnögonblicksbild av alla data som är i StorSimple-enheten och sedan ta bort vissa primära data, så visas den _primärdata_ släppa omedelbart. Den _molndata_ vilket omfattar nivåindelade data och säkerhetskopior, förblir densamma. Det beror på att det finns en ögonblicksbild som fortfarande refererar molndata. Efter molnet ögonblicksbild tas bort och alla ögonblicksbilder som refereras till samma data, förbrukning släpper i molnet. Innan vi tar bort data i molnet kan kontrollera vi att inga ögonblicksbilder fortfarande referera till dessa data. Den här processen kallas _skräpinsamling_ och körs en bakgrundstjänst på enheten. Borttagning av data i molnet är inte omedelbar eftersom tjänsten skräpinsamling samling söker efter andra referenser till dessa data före borttagningen. Skräpinsamling hastighet beror på det totala antalet ögonblicksbilder och den totala mängden data. Molndata rensas normalt på mindre än en vecka.


### <a name="thin-provisioning"></a>Tunn allokering
Tunn allokering är en virtualiseringsteknik som tillgängligt lagringsutrymme visas att överskrida fysiska resurser. I stället för att reservera tillräckligt med utrymme i förväg, använder StorSimple tunn allokering för att allokera tillräckligt utrymme för att uppfylla krav som ställs. Elastiska natur molnlagring underlättar den här metoden eftersom StorSimple kan öka eller minska lagring i molnet för att möta föränderliga behov.

> [!NOTE]
> Lokalt fixerade volymer är inte tunt allokerad. Lagringsutrymme som allokerats till en endast lokal volym etableras i sin helhet när volymen har skapats.


### <a name="deduplication-and-compression"></a>Avduplicering och komprimering
Microsoft Azure StorSimple använder deduplicering och data komprimering för att ytterligare minska utrymmeskraven.

Deduplicering minskar det totala mängden data som lagras genom att eliminera redundans i lagrade datauppsättningen. När informationen ändras StorSimple ignorerar ej ändrade data och samlar in bara ändringarna. StorSimple minskar också mängden lagrade data genom att identifiera och ta bort onödig information. 

> [!NOTE]
> Data på lokalt fixerade volymer är inte deduplicerats eller komprimeras. Säkerhetskopior av lokalt fixerade volymer är dock dedupliceras och komprimeras.


## <a name="storsimple-workload-summary"></a>Översikt över StorSimple-arbetsbelastningar
En sammanfattning av StorSimple-arbetsbelastningar som stöds visas i tabellen nedan.

| Scenario | Arbetsbelastning | Stöds | Begränsningar | Version |
| --- | --- | --- | --- | --- |
| Samarbete |Fildelning |Ja | |Alla versioner |
| Samarbete |Distribuerade fildelning |Ja | |Alla versioner |
| Samarbete |SharePoint |Ja* |Stöds endast med lokalt fixerade volymer |Update 2 och senare |
| Arkivering |Enkel arkivering |Ja | |Alla versioner |
| Virtualisering |Virtuella datorer |Ja* |Stöds endast med lokalt fixerade volymer |Update 2 och senare |
| Databas |SQL |Ja* |Stöds endast med lokalt fixerade volymer |Update 2 och senare |
| Video övervakning |Video övervakning |Ja* |När StorSimple-enheten är dedikerad enbart för den här arbetsbelastningen som stöds |Update 2 och senare |
| Backup |Primärt mål säkerhetskopiering |Ja* |När StorSimple-enheten är dedikerad enbart för den här arbetsbelastningen som stöds |Update 3 och senare |
| Backup |Säkerhetskopiering av sekundära mål |Ja* |När StorSimple-enheten är dedikerad enbart för den här arbetsbelastningen som stöds |Update 3 och senare |

*Ja&#42; -lösningen riktlinjer och begränsningar som ska användas.*

Följande arbetsbelastningar stöds inte av enheter i StorSimple 8000-serien. Om de har distribuerats på StorSimple resulterar dessa arbetsbelastningar i en konfiguration som inte stöds.

* Medicinska avbildning
* Exchange
* VDI
* Oracle
* SAP
* Stordata
* Innehållsdistribution
* Start från SCSI

Följande är en lista över infrastrukturkomponenter StorSimple som stöds.

| Scenario | Arbetsbelastning | Stöds | Begränsningar | Version |
| --- | --- | --- | --- | --- |
| Allmänt |Express Route |Ja | |Alla versioner |
| Allmänt |DataCore FC |Ja* |Stöds med DataCore SANsymphony |Alla versioner |
| Allmänt |DFSR |Ja* |Stöds endast med lokalt fixerade volymer |Alla versioner |
| Allmänt |Indexering |Ja* |För nivåindelade volymer endast metadata indexering stöds (inga data).<br>För lokalt fästa volymer stöds fullständig indexering. |Alla versioner |
| Allmänt |Ett virusskyddsprogram |Ja* |För nivåindelade volymer stöds endast genomsökning på Öppna och Stäng.<br> Fullständig genomsökning finns stöd för lokalt fästa volymer. |Alla versioner |

*Ja&#42; -lösningen riktlinjer och begränsningar som ska användas.*

Följande är en lista med annan programvara som används med StorSimple för att bygga lösningar.

| Typer av arbetsbelastningar | Programvara som används med StorSimple | Versioner som stöds|Länka till lösningsguide| 
| --- | --- | --- | --- |
| Mål för säkerhetskopia |Veeam |Veeam v 9 och senare |[StorSimple som ett säkerhetskopieringsmål med Veaam](storsimple-configure-backup-target-veeam.md)|
| Mål för säkerhetskopia |Veritas Backup Exec |Backup Exec 16 och senare |[StorSimple som ett säkerhetskopieringsmål med Backup Exec](storsimple-configure-backup-target-using-backup-exec.md)|
| Mål för säkerhetskopia |Veritas NetBackup |NetBackup 7.7.x och senare  |[StorSimple som ett säkerhetskopieringsmål med NetBackup](storsimple-configure-backuptarget-netbackup.md)|
| Global fildelning <br></br> Samarbete |Talon  |[StorSimple med Talon](https://www.talonstorage.com/products/archive/fast-deployment-azure-storsimple) | |

## <a name="storsimple-terminology"></a>StorSimple-terminologi
Innan du distribuerar din Microsoft Azure StorSimple-lösning, rekommenderar vi att du läser följande termer och definitioner.

### <a name="key-terms-and-definitions"></a>Viktiga termer och definitioner
| Tidsperiod (förkortning eller förkortning) | Beskrivning |
| --- | --- |
| åtkomstkontrollpost (ACR) |En post som är associerade med en volym på din Microsoft Azure StorSimple-enhet som bestämmer vilka värdar som kan ansluta till den. Det är baserat på iSCSI-kvalificerade namn (IQN) för de värdar som (ingår i ACR) som ansluter till din StorSimple-enhet. |
| AES-256 |En 256-bitars Advanced Encryption Standard (AES) algoritm för att kryptera data som flyttas till och från molnet. |
| storlek på allokeringsenhet (AU) |Den minsta mängden diskutrymme som kan tilldelas en fil i din Windows-filsystem. Om en filstorlek som inte är en jämn multipel av klusterstorleken extra utrymme måste användas för att lagra filen (upp till nästa multipel av klusterstorleken) vilket resulterar i förlorad utrymme och fragmentering på hårddisken. <br>Den rekommenderade AU: er för Azure StorSimple-volymer är 64 KB eftersom den fungerar bra med dedupliceringsalgoritmer. |
| automatiserad lagringsnivåer |Flyttar automatiskt mindre aktiva data från SSD: er till hårddiskar och sedan till en nivå i molnet och sedan aktiveras hanteringen av all lagring från en central användargränssnitt. |
| Säkerhetskopieringskatalogen |En samling av säkerhetskopior, vanligen är relaterat genom vilken programtyp som har använts. Den här samlingen visas under bladet säkerhetskopieringskatalog i Användargränssnittet för StorSimple Device Manager-tjänsten. |
| Säkerhetskopieringskatalogen fil |En fil som innehåller en lista över tillgängliga ögonblicksbilder som för närvarande lagras i säkerhetskopian av databasen för StorSimple Snapshot Manager. |
| princip för säkerhetskopiering |Ett urval av volymer, typ av säkerhetskopiering och en tidsplan som hjälper dig att skapa säkerhetskopior enligt ett fördefinierat schema. |
| binära objekt (binary large) |En samling av binära data som lagras som en enda enhet i ett system för relationsdatabashantering. BLOB-lagring är vanligtvis bilder, ljud eller andra multimedia objekt, även om ibland binära körbar kod lagras som en BLOB. |
| Challenge Handshake Authentication Protocol (CHAP) |Ett protokoll som används för att autentisera peer-datorn om en anslutning, baserat på de peer-delning av ett lösenord eller en hemlighet. CHAP kan vara enkelriktade eller ömsesidig. Med enkelriktad CHAP-målet autentiserar en initierare. Samtidig CHAP kräver att målet autentisera initieraren och att initieraren autentiserar målet. |
| Klona |En kopia av en volym. |
| Molnet som en nivå (CaaT) |Molnlagring integrerat som en nivå i lagringsarkitekturen så att alla lagringsenheter verkar vara en del av ett företag lagringsnätverk. |
| molntjänstleverantör (CSP) |Leverantör av tjänster inom molnbaserad databehandling. |
| ögonblicksbild av molndata |En point-in-time-kopia av volymdata som lagras i molnet. En ögonblicksbild i molnet motsvarar en ögonblicksbild som replikerats på ett annat, externa lagringssystem. Ögonblicksbilder av molnet som är särskilt användbart i scenarier för haveriberedskap. |
| krypteringsnyckel för molnlagring |Ett lösenord eller en nyckel som används av din StorSimple-enhet för att få åtkomst till krypterade data som skickas från enheten till molnet. |
| klustermedveten uppdatering |Hantering av programuppdateringar på servrar i ett failover-kluster så att uppdateringarna har minimal eller ingen effekt på tjänstens tillgänglighet. |
| DataPath |En samling funktionella enheter som utför åtgärder för sammankopplade databearbetning. |
| inaktivera |En permanent åtgärd som bryter anslutningen mellan StorSimple-enheten och den tillhörande Molntjänsten. Molnögonblicksbilder av enheten kan förblir efter den här processen och klonas eller används för haveriberedskap. |
| diskspegling |Replikering av logisk diskvolymer på olika hårddiskar eller enheter i realtid att säkerställa kontinuerlig tillgänglighet. |
| dynamisk diskspegling |Replikering av logisk diskvolymer på dynamiska diskar. |
| dynamiska diskar |En volym diskformat som använder den logiska disken (LDM Manager) för att lagra och hantera data över flera fysiska diskar. Dynamiska diskar kan utökas för att ge mer ledigt utrymme. |
| Utökade Bunch av diskar (EBOD) hölje |En sekundär inneslutning av din Microsoft Azure StorSimple-enhet som innehåller extra hårddisken diskar för ytterligare lagringsutrymme. |
| FAT-etablering |En konventionell lagringsetablering i vilka storage allokerat utrymme baserat på förväntat behov (och är vanligtvis utöver det aktuella behovet). Se även *tunn allokering*. |
| hårddisken (HDD) |En enhet som använder roterande skivorna för att lagra data. |
| hybridlagring i molnet |En lagringsarkitektur som använder lokala och externa resurser, inklusive molnlagring. |
| Internet Small Computer System Interface (iSCSI) |Ett nätverk standard Internet Protocol IP-baserad lagring för att länka utrustning för lagring av data eller verksamhet. |
| iSCSI-initierare |En programvarukomponent som gör att en värddator som kör Windows att ansluta till en extern lagring för iSCSI-baserade nätverk. |
| iSCSI kvalificerade namn (IQN) |Ett unikt namn som identifierar en iSCSI-mål eller initierare. |
| iSCSI-mål |En programvarukomponent som tillhandahåller centraliserad iSCSI-diskundersystem i SAN-nätverk. |
| Live arkivering |En strategi för lagring där arkiveringsdata är tillgänglig hela tiden (den inte lagras utanför anläggningen på band, till exempel). Microsoft Azure StorSimple använder live arkivering. |
| lokalt fixerad volym |en volym som finns på enheten och aldrig nivåer till molnet. |
| lokal ögonblicksbild |En point-in-time-kopia av volymdata som lagras på Microsoft Azure StorSimple-enheten. |
| Microsoft Azure StorSimple |En kraftfull lösning som består av en lagringsenhet för datacenter och programvara som gör att IT-organisationer att använda molnlagring som om det vore datacenter lagring. StorSimple förenklar dataskydds- och datahantering samtidigt minska kostnaderna. Lösningen konsoliderar primär lagring, arkivering, säkerhetskopiering och katastrofåterställning recovery (DR) sömlösa integreringen med molnet. Genom att kombinera hantering för SAN-lagring och data på en plattform i företagsklass, aktivera StorSimple-enheter hastighet, enkelhet och tillförlitlighet för alla lagringsrelaterade behov. |
| Strömförsörjning och kylning modulen (PCM) |Maskinvarukomponenter i StorSimple-enheten som består av strömförsörjning och kylning fläkten, därför namnet Power och kylning modulen. Den primära inneslutningen av enheten har två 764W PCMs medan EBOD-höljet har två 580 w vid PCMs. |
| Primär hölje |Huvudhölje för StorSimple-enheten som innehåller application platform domänkontrollanterna. |
| Återställningstid (RTO) |Längsta tid som ska vara förbrukad innan en affärsprocess eller system har återställts efter en katastrof. |
| seriellt ansluten SCSI (SAS) |En typ av hårddisken (HDD). |
| krypteringsnyckeln för tjänstdata |En nyckel som gjorts tillgängliga för alla nya StorSimple-enheter som registreras med StorSimple Device Manager-tjänsten. Konfigurationsdata som överförs mellan StorSimple Device Manager-tjänsten och enheten har krypterats med en offentlig nyckel och kan sedan dekrypteras bara på enheten med en privat nyckel. Krypteringsnyckeln för tjänstdata kan tjänsten för att erhålla den privata nyckeln för dekryptering. |
| nyckel för tjänstregistrering |En nyckel som hjälper till att registrera StorSimple-enheten med StorSimple Device Manager-tjänsten så att den visas i Azure-portalen för ytterligare hanteringsåtgärder. |
| Small Computer System Interface (SCSI) |En uppsättning standarder för att ansluta datorer och överföra data mellan dem fysiskt. |
| solid state-hårddisk (SSD) |En disk som innehåller inga rörliga delar. till exempel en flash-enhet. |
| storage account |En uppsättning autentiseringsuppgifter som är länkat till ditt storage-konto för en viss molntjänstleverantör. |
| StorSimple-adapter för SharePoint |En Microsoft Azure StorSimple-komponent som transparent utökar StorSimple-lagring och dataskydd till SharePoint-servergrupper. |
| StorSimple Device Manager-tjänst |En utökning av Azure-portalen där du kan hantera din Azure StorSimple för lokala och virtuella enheter. |
| StorSimple Snapshot Manager |Microsoft Management Console (MMC) snapin-modulen för hantering av säkerhetskopiering och återställning i Microsoft Azure StorSimple. |
| Säkerhetskopiera |En funktion som gör att användaren kan göra en interaktiv säkerhetskopia av en volym. Det är ett alternativt sätt att gör en manuell säkerhetskopia av en volym i stället för att utföra en automatisk säkerhetskopiering via en definierad princip. |
| Tunn allokering |En metod för att optimera effektivitet som används i tillgängligt lagringsutrymme i lagringssystem. I tunn allokering allokeras lagringen mellan flera användare baserat på det minsta utrymmet som krävs för varje användare vid en given tidpunkt. Se även *fat etablering*. |
| lagringsnivåer |Ordnar data i logiska grupperingar baserat på aktuell användning, ålder och relation till andra data. StorSimple ordnar automatiskt data i nivåer. |
| volym |Logiska lagringsutrymmen som visas i form av enheter. StorSimple-volymer motsvarar de volymer som monterats av värden, inklusive de som identifieras med hjälp av iSCSI- och en StorSimple-enhet. |
| volymbehållare |En gruppering av volymer och de inställningar som gäller för dem. Alla volymer på StorSimple-enheten är grupperade i volymbehållare. Behållarinställningar för volymen är lagringskonton, krypteringsinställningar för data som skickas till molnet med associerade krypteringsnycklar och bandbredd som används för åtgärder som rör molnet. |
| volymen grupp |I StorSimple Snapshot Manager, en volym grupp är en samling av volymer som konfigurerats för att underlätta säkerhetskopiering bearbetning. |
| Tjänsten Volume Shadow Copy (VSS) |En Windows Server operativsystemtjänst som möjliggör programkonsekvens genom att kommunicera med VSS-medvetna program att samordna skapandet av inkrementella ögonblicksbilder. VSS säkerställer att programmen är tillfälligt inaktiv när ögonblicksbilder tas. |
| Windows PowerShell för StorSimple |Ett Windows PowerShell-baserat kommandoradsgränssnitt används för att driva och hantera din StorSimple-enhet. Samtidigt några av de grundläggande funktionerna i Windows PowerShell, har det här gränssnittet ytterligare dedicerade cmdlets som är avsedda för att hantera en StorSimple-enhet. |

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [StorSimple-säkerhet](storsimple-8000-security.md).

