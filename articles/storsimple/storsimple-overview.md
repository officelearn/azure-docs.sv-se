---
title: StorSimple 8000-serien lösning översikt | Microsoft-dokument
description: Beskriver StorSimple-nivåindelning, enhet, virtuell enhet, tjänster och lagringshantering och introducerar viktiga termer som används i StorSimple.
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
ms.author: timlt
ms.openlocfilehash: 2a6650cac975c575415a329361da00d4fbfcaa9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68965118"
---
# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>StorSimple 8000-serien: en hybridmolnlagringslösning

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Översikt
Välkommen till Microsoft Azure StorSimple, en integrerad lagringslösning som hanterar lagringsuppgifter mellan lokala enheter och Microsoft Azure-molnlagring. StorSimple är en effektiv, kostnadseffektiv och lätthanterbar SAN-lösning (Storage Area Network) som eliminerar många av de problem och utgifter som är förknippade med företagslagring och dataskydd. Den tillverkarspecifika enheten StorSimple 8000 används, och lösningen kan integreras med molntjänster. Du får en uppsättning hanteringsverktyg och en enhetlig vy av hela företagslagringen inklusive molnlagring. (StorSimple-distributionsinformationen som publiceras på Microsoft Azure-webbplatsen gäller endast För StorSimple 8000-seriens enheter. Om du använder en StorSimple 5000/7000-serieenhet går du till [StorSimple Help](http://onlinehelp.storsimple.com/).)

StorSimple använder [lagringsnivåer för](#automatic-storage-tiering) att hantera lagrade data över olika lagringsmedia. Den aktuella arbetsuppsättningen lagras lokalt på SSD-enheter (Solid State Drives), data som används mindre ofta lagras på hårddiskar (HÅRDDISKAR) och arkiveringsdata överförs till molnet. Dessutom använder StorSimple deduplicering och komprimering för att minska mängden lagringsutrymme som data förbrukar. Mer information finns i [Deduplicering och komprimering](#deduplication-and-compression). För definitioner av andra viktiga termer och begrepp som används i Dokumentationen till StorSimple 8000-serien går du till [StorSimple-terminologin](#storsimple-terminology) i slutet av den här artikeln.

Förutom lagringshantering kan du med StorSimple-dataskyddsfunktioner skapa säkerhetskopiering på begäran och schemalagda säkerhetskopior och sedan lagra dem lokalt eller i molnet. Säkerhetskopior tas i form av inkrementella ögonblicksbilder, vilket innebär att de kan skapas och återställas snabbt. Ögonblicksbilder av molnet kan vara mycket viktiga i katastrofåterställningsscenarier eftersom de ersätter sekundära lagringssystem (till exempel säkerhetskopiering av band) och gör att du kan återställa data till ditt datacenter eller till alternativa platser om det behövs.

![videoikon](./media/storsimple-overview/video_icon.png) Titta på videon för en snabb introduktion till Microsoft Azure StorSimple.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/StorSimple-Hybrid-Cloud-Storage-Solution/player]

## <a name="why-use-storsimple"></a>Varför använda StorSimple?
I följande tabell beskrivs några av de viktigaste fördelarna som Microsoft Azure StorSimple tillhandahåller.

| Funktion | Fördelar |
| --- | --- |
| Transparent integration |Använder iSCSI-protokollet för att osynligt länka datalagringsanläggningar. Detta säkerställer att data som lagras i molnet, i datacentret eller på fjärrservrar verkar lagras på en enda plats. |
| Minskade lagringskostnader |Allokerar tillräckligt med lokal lagring eller molnlagring för att uppfylla aktuella krav och utökar molnlagring endast när det behövs. Det minskar ytterligare lagringskrav och kostnader genom att eliminera redundanta versioner av samma data (deduplicering) och genom att använda komprimering. |
| Förenklad lagringshantering |Tillhandahåller systemadministrationsverktyg för att konfigurera och hantera data som lagras lokalt, på en fjärrserver och i molnet. Dessutom kan du hantera säkerhetskopierings- och återställningsfunktioner från en MMC-snapin-modul (Microsoft Management Console).|
| Förbättrad haveriberedskap och efterlevnad |Kräver inte förlängd återställningstid. I stället återställs data som det behövs. Detta innebär att normal drift kan fortsätta med minimala störningar. Dessutom kan du konfigurera principer för att ange säkerhetskopieringsscheman och datakvarhållning. |
| Datamobilitet |Data som överförs till Microsoft Azure-molntjänster kan nås från andra webbplatser för återställnings- och migreringsändamål. Dessutom kan du använda StorSimple för att konfigurera StorSimple Cloud Appliances på virtuella datorer (VMs) som körs i Microsoft Azure. De virtuella datorerna kan sedan använda virtuella enheter för att komma åt lagrade data för test- eller återställningsändamål. |
| Verksamhetskontinuitet |Gör att StorSimple 5000-7000-serien användare att migrera sina data till en StorSimple 8000-serien enhet. |
| Tillgänglighet i Azure Government Portal |StorSimple är tillgängligt i Azure Government Portal. Mer information finns i [Distribuera din lokala StorSimple-enhet i regeringsportalen](storsimple-8000-deployment-walkthrough-gov-u2.md). |
| Dataskydd och tillgänglighet |StorSimple 8000-serien stöder zon redundant lagring (ZRS), förutom lokalt redundant lagring (LRS) och geo-redundant lagring (GRS). Se [den här artikeln om Azure Storage redundansalternativ](https://azure.microsoft.com/documentation/articles/storage-redundancy/) för ZRS-information. |
| Stöd för kritiska program |Med StorSimple kan du identifiera lämpliga volymer som lokalt fästa, vilket i sin tur säkerställer att data som krävs av kritiska program inte nivåindeleras till molnet. Lokalt fästa volymer är inte föremål för molndatärenden eller anslutningsproblem. Mer information om lokalt fästa volymer finns i [Använda Tjänsten StorSimple Device Manager för att hantera volymer](storsimple-8000-manage-volumes-u2.md). |
| Låg latens och hög prestanda |Du kan skapa molninstallationer som drar nytta av de högpresterande, låga svarstidsfunktionerna i Azure premium-lagring. Mer information om StorSimple premium molninstallationer finns i [Distribuera och hantera en StorSimple Cloud Appliance i Azure](storsimple-8000-cloud-appliance-u2.md). |


## <a name="storsimple-components"></a>StorSimple komponenter
Microsoft Azure StorSimple-lösningen innehåller följande komponenter:

* **Microsoft Azure StorSimple-enhet** – en lokal hybridlagringsmatris som innehåller SSD och hårddiskar, tillsammans med redundanta styrenheter och automatiska redundansfunktioner. Styrenheterna hanterar lagringsnivåering och placerar för närvarande använda (eller heta) data på lokal lagring (i enheten eller lokala servrar), samtidigt som de flyttar mindre ofta använda data till molnet.
* **StorSimple Cloud Appliance** – även känd som StorSimple Virtual Appliance, detta är en programvaruversion av StorSimple-enheten som replikerar arkitekturen och de flesta funktionerna i den fysiska hybridlagringsenheten. StorSimple Cloud Appliance körs på en enda nod i en virtuell Azure-dator. Premium virtuella enheter, som drar nytta av Azure premium-lagring, är tillgängliga i uppdatering 2 och senare.
* **StorSimple Device Manager-tjänsten** – ett tillägg av Azure-portalen som gör att du kan hantera en StorSimple-enhet eller StorSimple Cloud Appliance från ett enda webbgränssnitt. Du kan använda Tjänsten StorSimple Device Manager för att skapa och hantera tjänster, visa och hantera enheter, visa aviseringar, hantera volymer och visa och hantera principer för säkerhetskopiering och säkerhetskopieringskatalogen.
* **Windows PowerShell för StorSimple** – ett kommandoradsgränssnitt som du kan använda för att hantera StorSimple-enheten. Windows PowerShell för StorSimple har funktioner som gör att du kan registrera din StorSimple-enhet, konfigurera nätverksgränssnittet på enheten, installera vissa typer av uppdateringar, felsöka enheten genom att komma åt supportsessionen och ändra enheten Statligt. Du kan komma åt Windows PowerShell för StorSimple genom att ansluta till seriekonsolen eller med hjälp av Windows PowerShell-omsmotning.
* **Azure PowerShell StorSimple-cmdlets** – en samling Windows PowerShell-cmdlets som gör att du kan automatisera servicenivå och migreringsuppgifter från kommandoraden. Mer information om Azure PowerShell-cmdlets för StorSimple finns i [cmdlet-referensen](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0#azure).
* **StorSimple Snapshot Manager** – en MMC-snapin-modul som använder volymgrupper och Windows Volume Shadow Copy Service för att generera programkonsekventa säkerhetskopior. Dessutom kan du använda StorSimple Snapshot Manager för att skapa säkerhetskopieringsscheman och klona eller återställa volymer.
* **StorSimple Adapter för SharePoint** – ett verktyg som på ett transparent sätt utökar Microsoft Azure StorSimple-lagring och dataskydd till SharePoint Server-jordbruksföretag, samtidigt som StorSimple-lagring kan visas och hanteras från SharePoint Central Administration-portalen.

Diagrammet nedan ger en vy på hög nivå av Microsoft Azure StorSimple-arkitekturen och komponenterna.

![StorSimple-arkitektur](./media/storsimple-overview/overview-big-picture.png)

I följande avsnitt beskrivs var och en av dessa komponenter mer i detalj och hur lösningen ordnar data, allokerar lagring och underlättar lagringshantering och dataskydd. Det sista avsnittet innehåller definitioner för några av de viktiga termer och begrepp som rör StorSimple-komponenter och deras hantering.

## <a name="storsimple-device"></a>StorSimple-enhet
Microsoft Azure StorSimple-enheten är en lokal hybridlagringsmatris som ger primär lagring och iSCSI-åtkomst till data som lagras på den. Den hanterar kommunikation med molnlagring och bidrar till att säkerställa säkerhet och konfidentialitet för alla data som lagras på Microsoft Azure StorSimple-lösningen.

StorSimple-enheten innehåller SSD-enheter och hårddiskar, samt stöd för klustring och automatisk redundans. Den innehåller en delad processor, delad lagring och två speglade styrenheter. Varje styrenhet innehåller följande:

* Anslutning till en värddator
* Upp till sex nätverksportar för anslutning till det lokala nätverket (LAN)
* Övervakning av maskinvara
* Icke-flyktigt slumpmässigt åtkomstminne (NVRAM), som behåller information även om strömmen avbryts
* Klustermedveten uppdatering för att hantera programuppdateringar på servrar i ett redundanskluster så att uppdateringarna har minimal eller ingen effekt på tjänstens tillgänglighet
* Klustertjänst, som fungerar som ett backend-kluster, ger hög tillgänglighet och minimerar eventuella negativa effekter som kan uppstå om en hårddisk eller SSD misslyckas eller tas offline

Endast en styrenhet är aktiv vid någon tidpunkt. Om den aktiva styrenheten misslyckas aktiveras den andra handkontrollen automatiskt.

Mer information finns i [StorSimples maskinvarukomponenter och status](storsimple-8000-monitor-hardware-status.md).

## <a name="storsimple-cloud-appliance"></a>StorSimple Cloud Appliance
Du kan använda StorSimple för att skapa en molninstallation som replikerar arkitekturen och funktionerna för den fysiska hybridlagringsenheten. StorSimple Cloud Appliance (kallas även StorSimple Virtual Appliance) körs på en enda nod i en virtuell Azure-dator. (En molninstallation kan bara skapas på en virtuell Azure-dator. Du kan inte skapa en på en StorSimple-enhet eller en lokal server.)

Molninstallationen har följande funktioner:

* Det fungerar som en fysisk apparat och kan erbjuda ett iSCSI-gränssnitt till virtuella datorer i molnet.
* Du kan skapa ett obegränsat antal molninstallationer i molnet och slå på och stänga av dem efter behov.
* Det kan hjälpa till att simulera lokala miljöer i katastrofåterställning, utveckling och testscenarier och kan hjälpa till med hämtning på objektnivå från säkerhetskopior.

StorSimple Cloud Appliance finns i två modeller: 8010-enheten (tidigare känd som 1100-modellen) och 8020-enheten. 8010-enheten har en maximal kapacitet på 30 TB. 8020-enheten, som drar nytta av Azure premium-lagring, har en maximal kapacitet på 64 TB. (På lokala nivåer lagrar Azure premium-lagring data på SSD-enheter medan standardlagring lagrar data på hårddiskar.) Observera att du måste ha ett Azure premium-lagringskonto för att kunna använda premiumlagring.

Mer information om StorSimple Cloud Appliance finns i [Distribuera och hantera en StorSimple Cloud Appliance i Azure](storsimple-8000-cloud-appliance-u2.md).

## <a name="storsimple-device-manager-service"></a>StorSimple Device Manager-tjänsten
Microsoft Azure StorSimple tillhandahåller ett webbaserat användargränssnitt (StorSimple Device Manager-tjänsten) som gör att du kan hantera datacenter och molnlagring centralt. Du kan använda Tjänsten StorSimple Device Manager för att utföra följande uppgifter:

* Konfigurera systeminställningar för StorSimple-enheter.
* Konfigurera och hantera säkerhetsinställningar för StorSimple-enheter.
* Konfigurera molnautentiseringsuppgifter och -egenskaper.
* Konfigurera och hantera volymer på en server.
* Konfigurera volymgrupper.
* Säkerhetskopiera och återställa data.
* Övervaka prestanda.
* Granska systeminställningarna och identifiera eventuella problem.

Du kan använda Tjänsten StorSimple Device Manager för att utföra alla administrationsuppgifter utom de som kräver driftstopp, till exempel inledande installation och installation av uppdateringar.

Mer information finns i [Använda Tjänsten StorSimple Device Manager för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

## <a name="windows-powershell-for-storsimple"></a>Windows PowerShell för StorSimple
Windows PowerShell för StorSimple tillhandahåller ett kommandoradsgränssnitt som du kan använda för att skapa och hantera Tjänsten Microsoft Azure StorSimple och konfigurera och övervaka StorSimple-enheter. Det är ett Windows PowerShell-baserat kommandoradsgränssnitt som innehåller dedikerade cmdlets för hantering av din StorSimple-enhet. Windows PowerShell för StorSimple har funktioner som gör att du kan:

* Registrera en enhet.
* Konfigurera nätverksgränssnittet på en enhet.
* Installera vissa typer av uppdateringar.
* Felsöka enheten genom att komma åt supportsessionen.
* Ändra enhetens tillstånd.

Du kan komma åt Windows PowerShell för StorSimple från en seriell konsol (på en värddator som är ansluten direkt till enheten) eller på distans med hjälp av Windows PowerShell-omsprutning. Observera att vissa Windows PowerShell för StorSimple-uppgifter, till exempel inledande enhetsregistrering, endast kan göras på seriekonsolen.

Mer information finns i [Använda Windows PowerShell för StorSimple för att administrera enheten](storsimple-8000-windows-powershell-administration.md).

## <a name="azure-powershell-storsimple-cmdlets"></a>Azure PowerShell StorSimple-cmdlets
Azure PowerShell StorSimple-cmdletar är en samling Windows PowerShell-cmdletar som gör att du kan automatisera servicenivå och migreringsuppgifter från kommandoraden. Mer information om Azure PowerShell-cmdlets för StorSimple finns i [cmdlet-referensen](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0).

## <a name="storsimple-snapshot-manager"></a>StorSimple Snapshot Manager
StorSimple Snapshot Manager är en MMC-snapin-modul (Microsoft Management Console) som du kan använda för att skapa konsekventa, punkt-i-tid-säkerhetskopior av lokala data och molndata. Snapin-modulen körs på en Windows Server-baserad värd. Du kan använda StorSimple Snapshot Manager för att:

* Konfigurera, säkerhetskopiera och ta bort volymer.
* Konfigurera volymgrupper för att säkerställa att säkerhetskopierade data är programkonsekvent.
* Hantera principer för säkerhetskopiering så att data säkerhetskopieras enligt ett förutbestämt schema och lagras på en angiven plats (lokalt eller i molnet).
* Återställ volymer och enskilda filer.

Säkerhetskopior fångas som ögonblicksbilder, som registrerar endast ändringarna sedan den senaste ögonblicksbilden togs och kräver mycket mindre lagringsutrymme än fullständiga säkerhetskopior. Du kan skapa säkerhetskopieringsscheman eller vidta omedelbara säkerhetskopior efter behov. Dessutom kan du använda StorSimple Snapshot Manager för att fastställa bevarandeprinciper som styr hur många ögonblicksbilder som ska sparas. Om du sedan behöver återställa data från en säkerhetskopia kan du välja i katalogen med lokala ögonblicksbilder eller ögonblicksbilder i molnet. 

Om en katastrof inträffar eller om du behöver återställa data av en annan anledning återställs den stegvis som den behövs. Återställning av data kräver inte att du stänger av hela systemet medan du återställer en fil, ersätter utrustning eller flyttar åtgärder till en annan plats.

Mer information finns i [Vad är StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>StorSimple-adapter för SharePoint
Microsoft Azure StorSimple innehåller StorSimple-kortet för SharePoint, en valfri komponent som på ett transparent sätt utökar Lagrings- och dataskyddsfunktioner för StorSimple till SharePoint-servergrupper. Kortet fungerar med en RBS-provider (Remote Blob Storage) och SQL Server RBS-funktionen, så att du kan flytta BLOBs till en server som backas upp av Microsoft Azure StorSimple-systemet. Microsoft Azure StorSimple lagrar sedan BLOB-data lokalt eller i molnet, baserat på användning.

StorSimple-kortet för SharePoint hanteras från SharePoint Central Administration-portalen. Därför förblir SharePoint-hantering centraliserad och all lagring verkar finnas i SharePoint-servergruppen.

Mer information finns i [StorSimple-kortet för SharePoint](storsimple-adapter-for-sharepoint.md). 

## <a name="storage-management-technologies"></a>Teknik för lagringshantering
Förutom den dedikerade StorSimple-enheten, den virtuella enheten och andra komponenter använder Microsoft Azure StorSimple följande programvarutekniker för att ge snabb åtkomst till data och minska lagringsförbrukningen:

* [Automatisk lagringsnivå](#automatic-storage-tiering) 
* [Tunn allokering](#thin-provisioning) 
* [Deduplicering och komprimering](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>Automatisk lagringsnivå
Microsoft Azure StorSimple ordnar automatiskt data på logiska nivåer baserat på aktuell användning, ålder och relation till andra data. Data som är mest aktiva lagras lokalt, medan mindre aktiva och inaktiva data automatiskt migreras till molnet. Följande diagram illustrerar den här lagringsmetoden.

![Lagringsnivåer för StorSimple](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

För att möjliggöra snabb åtkomst lagrar StorSimple mycket aktiva data (heta data) på SSD:er i StorSimple-enheten. Den lagrar data som används ibland (varma data) på hårddiskar i enheten eller på servrar i datacentret. Den flyttar inaktiva data, säkerhetskopieringsdata och data som lagras för arkivering eller efterlevnad till molnet. 

> [!NOTE]
> I Uppdatering 2 eller senare kan du ange en volym som lokalt fäst, i vilket fall data finns kvar på den lokala enheten och inte är nivåindelade till molnet. 


StorSimple justerar och ordnar om data- och lagringstilldelningar när användningsmönstren ändras. Viss information kan till exempel bli mindre aktiv med tiden. När den blir progressivt mindre aktiv migreras den från SSD:er till hårddiskar och sedan till molnet. Om samma data blir aktiva igen migreras de tillbaka till lagringsenheten.

Lagringsdelningsprocessen sker på följande sätt:

1. En systemadministratör konfigurerar ett Microsoft Azure-molnlagringskonto.
2. Administratören använder seriekonsolen och StorSimple Device Manager-tjänsten (som körs i Azure-portalen) för att konfigurera enheten och filservern, skapa volymer och dataskyddsprinciper. Lokala datorer (till exempel filservrar) använder Internet Small Computer System Interface (iSCSI) för att komma åt StorSimple-enheten.
3. Inledningsvis lagrar StorSimple data på enhetens snabba SSD-nivå.
4. När SSD-nivån närmar sig kapacitet, uppduplicerar StorSimple och komprimerar de äldsta datablocken och flyttar dem till hårddisknivån.
5. När hdd-nivån närmar sig kapacitet krypterar StorSimple de äldsta datablocken och skickar dem säkert till Microsoft Azure-lagringskontot via HTTPS.
6. Microsoft Azure skapar flera repliker av data i sitt datacenter och i ett fjärrdatacenter, vilket säkerställer att data kan återställas om en katastrof inträffar.
7. När filservern begär data som lagras i molnet returnerar StorSimple den sömlöst och lagrar en kopia på SSD-nivån på StorSimple-enheten.

#### <a name="how-storsimple-manages-cloud-data"></a>Så här hanterar StorSimple molndata

StorSimple deduplicerar kunddata över alla ögonblicksbilder och primära data (data skrivna av värdar). Även om deduplicering är bra för lagringseffektivitet, gör det frågan om "vad som finns i molnet" komplicerat. De nivåindelade primära data och ögonblicksbilddata överlappar varandra. En enda bit data i molnet kan användas som nivåindelade primära data och även refereras av flera ögonblicksbilder. Varje ögonblicksbild i molnet säkerställer att en kopia av alla point-in-time-data är låst i molnet tills ögonblicksbilden tas bort.

Data tas bara bort från molnet när det inte finns några referenser till dessa data. Om vi till exempel tar en ögonblicksbild av molnet av alla data som finns i StorSimple-enheten och sedan tar bort vissa primära data, ser vi att den _primära data_ släpps omedelbart. _Molndata_ som innehåller nivåindelade data och säkerhetskopior, förblir densamma. Detta beror på att det finns en ögonblicksbild som fortfarande refererar till molndata. När ögonblicksbilden av molnet har tagits bort (och alla andra ögonblicksbilder som refererade till samma data) sjunker molnförbrukningen. Innan vi tar bort molndata kontrollerar vi att inga ögonblicksbilder fortfarande refererar till dessa data. Den här processen kallas _skräpinsamling_ och är en bakgrundstjänst som körs på enheten. Borttagning av molndata är inte omedelbart eftersom skräpinsamlingstjänsten söker efter andra referenser till dessa data före borttagningen. Hastigheten på skräpinsamlingen beror på det totala antalet ögonblicksbilder och den totala informationen. Vanligtvis rensas molndata på mindre än en vecka.


### <a name="thin-provisioning"></a>Tunn allokering
Tunn etablering är en virtualiseringsteknik där tillgängligt lagringsutrymme verkar överskrida fysiska resurser. I stället för att reservera tillräckligt med lagringsutrymme i förväg använder StorSimple tunn etablering för att allokera tillräckligt med utrymme för att uppfylla aktuella krav. Den elastiska karaktären hos molnlagring underlättar den här metoden eftersom StorSimple kan öka eller minska molnlagringen för att möta förändrade krav.

> [!NOTE]
> Lokalt fästa volymer är inte tunt etablerade. Lagring som allokerats till en lokal volym etableras i sin helhet när volymen skapas.


### <a name="deduplication-and-compression"></a>Deduplicering och komprimering
Microsoft Azure StorSimple använder deduplicering och datakomprimering för att ytterligare minska lagringskraven.

Deduplicering minskar den totala mängden data som lagras genom att eliminera redundans i den lagrade datauppsättningen. När informationen ändras ignorerar StorSimple oförändrade data och samlar bara in ändringarna. Dessutom minskar StorSimple mängden lagrade data genom att identifiera och ta bort onödig information. 

> [!NOTE]
> Data på lokalt fästa volymer är inte deduplicated eller komprimerade. Säkerhetskopior av lokalt fästa volymer dedupliceras och komprimeras.


## <a name="storsimple-workload-summary"></a>Sammanfattning av StorSimple-arbetsbelastning
En sammanfattning av de StorSimple-arbetsbelastningar som stöds tas i tabell nedan.

| Scenario | Arbetsbelastning | Stöds | Begränsningar | Version |
| --- | --- | --- | --- | --- |
| Samarbete |Fildelning |Ja | |Alla versioner |
| Samarbete |Distribuerad fildelning |Ja | |Alla versioner |
| Samarbete |SharePoint |Ja* |Stöds endast med lokalt fästa volymer |Uppdatering 2 och senare |
| Arkivering |Enkel filarkivering |Ja | |Alla versioner |
| Virtualisering |Virtuella datorer |Ja* |Stöds endast med lokalt fästa volymer |Uppdatering 2 och senare |
| Databas |SQL |Ja* |Stöds endast med lokalt fästa volymer |Uppdatering 2 och senare |
| Videoövervakning |Videoövervakning |Ja* |Stöds när StorSimple-enheten endast är avsedd för den här arbetsbelastningen |Uppdatering 2 och senare |
| Säkerhetskopiering |Primär säkerhetskopiering av mål |Ja* |Stöds när StorSimple-enheten endast är avsedd för den här arbetsbelastningen |Uppdatering 3 och senare |
| Säkerhetskopiering |Sekundär säkerhetskopiering av mål |Ja* |Stöds när StorSimple-enheten endast är avsedd för den här arbetsbelastningen |Uppdatering 3 och senare |

*Ja&#42; - Riktlinjer och begränsningar för lösningar bör tillämpas.*

Följande arbetsbelastningar stöds inte av Enheter i StorSimple 8000-serien. Om de distribueras på StorSimple resulterar dessa arbetsbelastningar i en konfiguration som inte stöds.

* Medicinsk bildbehandling
* Exchange
* Vdi
* Oracle
* SAP
* Stordata
* Distribution av innehåll
* Starta från SCSI

Följande är en lista över de Infrastrukturkomponenter som stöds av StorSimple.

| Scenario | Arbetsbelastning | Stöds | Begränsningar | Version |
| --- | --- | --- | --- | --- |
| Allmänt |Express Route |Ja | |Alla versioner |
| Allmänt |DataCore FC |Ja* |Stöds med DataCore SANsymphony |Alla versioner |
| Allmänt |DFSR |Ja* |Stöds endast med lokalt fästa volymer |Alla versioner |
| Allmänt |Indexering |Ja* |För nivåindelade volymer stöds endast metadataindexering (inga data).<br>För lokalt fästa volymer stöds fullständig indexering. |Alla versioner |
| Allmänt |Virusskydd |Ja* |För nivåindelade volymer stöds endast genomsökning på öppna och stänga.<br> För lokalt fästa volymer stöds fullständig genomsökning. |Alla versioner |

*Ja&#42; - Riktlinjer och begränsningar för lösningar bör tillämpas.*

Följande är en lista över andra program som används med StorSimple för att bygga lösningar.

| Typ av arbetsbelastning | Programvara som används med StorSimple | Versioner som stöds|Länk till lösningsguide| 
| --- | --- | --- | --- |
| Mål för säkerhetskopiering |Veeam (en) |Veeam v 9 och senare |[StorSimple som ett säkerhetskopieringsmål med Veaam](storsimple-configure-backup-target-veeam.md)|
| Mål för säkerhetskopiering |Veritas Backup Exec |Backup Exec 16 och senare |[StorSimple som ett säkerhetskopieringsmål med Backup Exec](storsimple-configure-backup-target-using-backup-exec.md)|
| Mål för säkerhetskopiering |Veritas NetBackup |NetBackup 7.7.x och senare  |[StorSimple som ett säkerhetskopieringsmål med NetBackup](storsimple-configure-backuptarget-netbackup.md)|
| Global fildelning <br></br> Samarbete |Talon  |[StorSimple med Talon](https://www.talonstorage.com/products/archive/fast-deployment-azure-storsimple) | |

## <a name="storsimple-terminology"></a>StorSimple terminologi
Innan du distribuerar din Microsoft Azure StorSimple-lösning rekommenderar vi att du granskar följande termer och definitioner.

### <a name="key-terms-and-definitions"></a>Viktiga termer och definitioner
| Term (Akronym eller förkortning) | Beskrivning |
| --- | --- |
| åtkomstkontrollpost (ACR) |En post som är associerad med en volym på din Microsoft Azure StorSimple-enhet som avgör vilka värdar som kan ansluta till den. Bestämningen baseras på iSCSI Qualified Name (IQN) för värdarna (som finns i ACR) som ansluter till din StorSimple-enhet. |
| AES-256 |En 256-bitars AES-algoritm (Advanced Encryption Standard) för kryptering av data när den flyttas till och från molnet. |
| allokeringsenhetsstorlek (AUS) |Den minsta mängd diskutrymme som kan allokeras för att lagra en fil i Windows-filsystemen. Om en filstorlek inte är en jämn multipel av klusterstorleken måste extra utrymme användas för att hålla filen (upp till nästa multipel av klusterstorleken) vilket resulterar i förlorat utrymme och fragmentering av hårddisken. <br>De rekommenderade AUS för Azure StorSimple-volymerna är 64 KB eftersom det fungerar bra med dedupliceringsalgoritmerna. |
| automatiserad lagringsnivå |Flytta automatiskt mindre aktiva data från SSD-enheter till hårddiskar och sedan till en nivå i molnet och sedan aktivera hantering av all lagring från ett centralt användargränssnitt. |
| katalog för säkerhetskopiering |En samling säkerhetskopior, vanligtvis relaterade av programtypen som användes. Den här samlingen visas i bladet Säkerhetskopieringskatalog i tjänstgränssnittet i StorSimple Device Manager. |
| katalogfil för säkerhetskopiering |En fil som innehåller en lista över tillgängliga ögonblicksbilder som för närvarande lagras i Säkerhetskopiorna i StorSimple Snapshot Manager. |
| princip för säkerhetskopiering |Ett urval av volymer, typ av säkerhetskopiering och en tidsplan som gör att du kan skapa säkerhetskopior enligt ett fördefinierat schema. |
| binära stora objekt (BLOBs) |En samling binära data som lagras som en enda entitet i ett databashanteringssystem. BLOBs är vanligtvis bilder, ljud eller andra multimediaobjekt, även om binär körbar kod ibland lagras som en BLOB. |
| CHAP (Challenge Handshake Authentication Protocol) |Ett protokoll som används för att autentisera peer-datorn för en anslutning, baserat på peer-delning av ett lösenord eller hemligt. CHAP kan vara enkelriktad eller ömsesidig. Med enkelriktad CHAP autentiserar målet en initierare. Ömsesidig CHAP kräver att målet autentiserar initieraren och att initieraren autentiserar målet. |
| clone |En dubblettkopia av en volym. |
| Moln som en nivå (CaaT) |Molnlagring integrerad som en nivå i lagringsarkitekturen så att all lagring verkar vara en del av ett företagslagringsnätverk. |
| molntjänstleverantör (CSP) |En leverantör av molntjänster. |
| ögonblicksbild av molnet |En punkt-i-tid-kopia av volymdata som lagras i molnet. En ögonblicksbild i molnet motsvarar en ögonblicksbild som replikeras på ett annat lagringssystem utanför platsen. Ögonblicksbilder av molnet är särskilt användbara i katastrofåterställningsscenarier. |
| krypteringsnyckel för molnlagring |Ett lösenord eller en nyckel som används av StorSimple-enheten för att komma åt krypterade data som skickas av enheten till molnet. |
| klustermedveten uppdatering |Hantera programuppdateringar på servrar i ett redundanskluster så att uppdateringarna får minimal eller ingen effekt på tjänstens tillgänglighet. |
| datasöka |En samling funktionella enheter som utför sammankopplade databehandlingsåtgärder. |
| inaktivera |En permanent åtgärd som bryter anslutningen mellan StorSimple-enheten och den associerade molntjänsten. Ögonblicksbilder av molnet av enheten finns kvar efter den här processen och kan klonas eller användas för haveriberedskap. |
| diskspegling |Replikering av logiska diskvolymer på separata hårddiskar i realtid för att säkerställa kontinuerlig tillgänglighet. |
| dynamisk diskspegling |Replikering av logiska diskvolymer på dynamiska diskar. |
| dynamiska diskar |Ett diskvolymformat som använder LDM (Logical Disk Manager) för att lagra och hantera data på flera fysiska diskar. Dynamiska diskar kan förstoras för att ge mer ledigt utrymme. |
| Utökat paket med diskar (EBOD) kapsling |En sekundär hölje för din Microsoft Azure StorSimple-enhet som innehåller extra hårddiskar för ytterligare lagring. |
| fett provisionering |En konventionell lagringsetablering där lagringsutrymme fördelas baserat på förväntade behov (och ligger vanligtvis utanför det nuvarande behovet). Se även *tunn etablering*. |
| hårddisk (HDD) |En enhet som använder roterande skivor för att lagra data. |
| hybrid molnlagring |En lagringsarkitektur som använder lokala resurser och resurser utanför platsen, inklusive molnlagring. |
| Internet litet datorsystemgränssnitt (iSCSI) |En IP-baserad lagringsnätverksstandard (Internet Protocol) för länkning av datalagringsutrustning eller datalagringsanläggningar. |
| iSCSI-initierare |En programvarukomponent som gör det möjligt för en värddator som kör Windows att ansluta till ett externt iSCSI-baserat lagringsnätverk. |
| iSCSI-kvalificerat namn (IQN) |Ett unikt namn som identifierar ett iSCSI-mål eller initierare. |
| iSCSI-mål |En programvarukomponent som tillhandahåller centraliserade iSCSI-diskundersystem i lagringsnätverk. |
| levande arkivering |En lagringsmetod där arkiveringsdata är tillgängliga hela tiden (den lagras inte på band, till exempel). Microsoft Azure StorSimple använder direktarkivering. |
| lokalt fäst volym |en volym som finns på enheten och som aldrig är nivåinrättad till molnet. |
| lokal ögonblicksbild |En punkt-i-tid-kopia av volymdata som lagras på Microsoft Azure StorSimple-enheten. |
| Microsoft Azure StorSimple |En kraftfull lösning som består av en datacenterlagringsinstallation och programvara som gör det möjligt för IT-organisationer att utnyttja molnlagring som om det vore datacenterlagring. StorSimple förenklar dataskydd och datahantering samtidigt som kostnaderna minskas. Lösningen konsoliderar primär lagring, arkiv, säkerhetskopiering och haveriberedskap (DR) genom sömlös integrering med molnet. Genom att kombinera SAN-lagring och molndatahantering på en plattform i företagsklass möjliggör StorSimple-enheter snabbhet, enkelhet och tillförlitlighet för alla lagringsrelaterade behov. |
| Ström- och kylmodul (PCM) |Maskinvarukomponenter i din StorSimple-enhet som består av nätaggregat och kylfläkt, därav namnet Power and Cooling module. Den primära inneslutningen av enheten har två 764W PCMs medan EBOD-höljet har två 580W PCMs. |
| primär kapsling |Huvudhöljet på din StorSimple-enhet som innehåller programplattformsstyrenheterna. |
| mål för återhämtningstid (RTO) |Den maximala tid som ska förbrukas innan en affärsprocess eller ett system återställs helt efter en katastrof. |
| seriell bifogad SCSI (SAS) |En typ av hårddisk (HDD). |
| krypteringsnyckel för tjänstdata |En nyckel som görs tillgänglig för alla nya StorSimple-enheter som registrerar sig med StorSimple Device Manager-tjänsten. Konfigurationsdata som överförs mellan Tjänsten StorSimple Device Manager och enheten krypteras med en offentlig nyckel och kan sedan dekrypteras endast på enheten med hjälp av en privat nyckel. Med krypteringsnyckel för tjänstdata kan tjänsten hämta den här privata nyckeln för dekryptering. |
| tjänstregistreringsnyckel |En nyckel som hjälper till att registrera StorSimple-enheten med Tjänsten StorSimple Device Manager så att den visas i Azure-portalen för ytterligare hanteringsåtgärder. |
| Scsi (Small Computer System Interface) |En uppsättning standarder för fysiskt anslutande datorer och skicka data mellan dem. |
| SSD (Solid State Drive) |En disk som inte innehåller några rörliga delar. till exempel ett usb-minne. |
| storage account |En uppsättning åtkomstautentiseringsuppgifter som är länkade till ditt lagringskonto för en viss molntjänstleverantör. |
| StorSimple-adapter för SharePoint |En Microsoft Azure StorSimple-komponent som på ett transparent sätt utökar Lagring och dataskydd för StorSimple till SharePoint-servergrupper. |
| StorSimple Device Manager-tjänsten |Ett tillägg av Azure-portalen som gör att du kan hantera dina Azure StorSimple lokala och virtuella enheter. |
| StorSimple Snapshot Manager |En MMC-snapin-modul (Microsoft Management Console) för hantering av säkerhetskopiering och återställning i Microsoft Azure StorSimple. |
| ta backup |En funktion som gör att användaren kan ta en interaktiv säkerhetskopia av en volym. Det är ett alternativt sätt att ta en manuell säkerhetskopiering av en volym i stället för att ta en automatisk säkerhetskopiering via en definierad princip. |
| tunn avsättning |En metod för att optimera effektiviteten med vilken det tillgängliga lagringsutrymmet används i lagringssystem. Vid tunn etablering allokeras lagringen mellan flera användare baserat på det minsta utrymme som krävs av varje användare vid en given tidpunkt. Se även *fettetablering*. |
| Nivåindelning |Ordna data i logiska grupperingar baserat på aktuell användning, ålder och relation till andra data. StorSimple ordnar automatiskt data på nivåer. |
| volym |Logiska lagringsutrymmen presenteras i form av enheter. StorSimple volymer motsvarar de volymer som monteras av värden, inklusive de som upptäcks med hjälp av iSCSI och en StorSimple-enhet. |
| volymbehållare |En gruppering av volymer och de inställningar som gäller för dem. Alla volymer i StorSimple-enheten är grupperade i volymbehållare. Volymbehållare inställningar inkluderar lagringskonton, krypteringsinställningar för data som skickas till molnet med tillhörande krypteringsnycklar och bandbredd som förbrukas för åtgärder som involverar molnet. |
| volymgrupp |I StorSimple Snapshot Manager är en volymgrupp en samling volymer som konfigurerats för att underlätta säkerhetskopiering. |
| Tjänsten För skuggkopia av volym (VSS) |En Windows Server-operativsystemtjänst som underlättar programmets konsekvens genom att kommunicera med VSS-medvetna program för att samordna skapandet av inkrementella ögonblicksbilder. VSS säkerställer att programmen är tillfälligt inaktiva när ögonblicksbilder tas. |
| Windows PowerShell för StorSimple |Ett Windows PowerShell-baserat kommandoradsgränssnitt som används för att styra och hantera din StorSimple-enhet. Samtidigt som du behåller några av de grundläggande funktionerna i Windows PowerShell har det här gränssnittet ytterligare dedikerade cmdlets som är inriktade på att hantera en StorSimple-enhet. |

## <a name="next-steps"></a>Nästa steg
Läs mer om [StorSimple-säkerhet](storsimple-8000-security.md).

