---
title: "StorSimple 8000-serien lösning: översikt | Microsoft Docs"
description: "Beskriver StorSimple skiktning, enhet, virtuell enhet, tjänster och lagringshantering och beskriver viktiga termer som används i StorSimple."
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: 7144d218-db21-4495-88fb-e3b24bbe45d1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/10/2017
ms.author: v-sharos@microsoft.com
ms.openlocfilehash: 10f4b6ec29c2d9539fa7fe61c96581755e630822
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2017
---
# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>StorSimple 8000-serien: en hybridlagringslösning för molnet
## <a name="overview"></a>Översikt
Välkommen till Microsoft Azure StorSimple, en integrerad lagringslösning som hanterar lagringsuppgifter mellan lokala enheter och Microsoft Azure-molnlagring. StorSimple är en, kostnadseffektiv och enkelt hanterbara area network (SAN) lagringslösning som eliminerar många problem och kostnader som är associerade med enterprise lagring och dataskydd. Den använder den egna StorSimple 8000-serien enheten, kan integreras med molntjänster och innehåller en uppsättning av verktyg för en sömlös vy över alla företagslagring, inklusive lagringsutrymmet i molnet. (Distributionsinformationen för StorSimple som publiceras på webbplatsen Microsoft Azure gäller endast StorSimple 8000 series-enheter. Om du använder en serieenhet för StorSimple 5000/7000-, gå till [StorSimple hjälp](http://onlinehelp.storsimple.com/).)

StorSimple använder [lagringsnivåer](#automatic-storage-tiering) att hantera lagrade data över olika lagringsmedium. Aktuella arbetsminnet är lagras lokalt på solid state-hårddiskar (SSD) data som används mindre ofta lagras på hårddiskar (HDD) och arkiveringsdata skickas till molnet. Dessutom använder StorSimple deduplicering och komprimering för att minska mängden lagringsutrymme som förbrukar data. Mer information finns på [Deduplicering och komprimering](#deduplication-and-compression). Mer information om andra viktiga termer och begrepp som används i dokumentationen för StorSimple 8000-serien, gå till [StorSimple terminologi](#storsimple-terminology) i slutet av den här artikeln.

Förutom lagringshantering, StorSimple Dataskyddsfunktioner kan du skapa på begäran och schemalagda säkerhetskopieringar och lagra dem lokalt eller i molnet. Säkerhetskopieringar vidtas i form av inkrementell ögonblicksbilder, vilket innebär att de kan skapas och återställs snabbt. Molnögonblicksbilder kan vara ytterst viktigt i katastrofåterställning eftersom de ersätta sekundära lagringssystem (till exempel bandsäkerhetskopiering) och att du kan återställa data till ditt datacenter eller till en annan om det behövs.

![ikon för videoklipp](./media/storsimple-overview/video_icon.png) Titta på videon för en snabb introduktion till Microsoft Azure StorSimple.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/StorSimple-Hybrid-Cloud-Storage-Solution/player]

## <a name="why-use-storsimple"></a>Varför använda StorSimple?
I följande tabell beskrivs några av de främsta fördelarna med Microsoft Azure StorSimple.

| Funktion | Fördelar |
| --- | --- |
| Transparent integrering |Använder iSCSI-protokollet för att länka loggas data lagringsutrymmen. Detta garanterar att data som lagras i molnet på datacentret, eller på fjärrservrar visas kan lagras på en enda plats. |
| Minskad lagringskostnader |Allokerar tillräckligt lokal eller molnet för att möta aktuella behov och utökar molnlagring bara när det behövs. Ytterligare minskar lagringsbehov och utgifter genom att eliminera redundant versioner av samma data (deduplicering) och med hjälp av komprimering. |
| Förenklad lagringshantering |Innehåller system Administrationsverktyg för att konfigurera och hantera data som lagras lokalt på en fjärrserver, och i molnet. Dessutom kan du hantera säkerhetskopiering och återställa funktioner från en snapin-modulen Microsoft Management Console (MMC).|
| Förbättrad återställning och efterlevnad |Kräver inte utökade återställningstid. I stället återställer data när det behövs. Det innebär att normal drift kan fortsätta med minimala störningar. Dessutom kan du konfigurera principer för att ange scheman för säkerhetskopiering och datalagring. |
| Data mobility |Data som överförs till Microsoft Azure-molntjänster kan nås från andra platser för återställning och migrering. Du kan dessutom använda StorSimple för att konfigurera StorSimple moln installationer på virtuella datorer (VM) som körs i Microsoft Azure. De virtuella datorerna kan sedan använda virtuella enheter åtkomst till lagrade data för test- eller återställning. |
| Verksamhetskontinuitet |Användarna StorSimple 5000 7000-serien kan migrera data till en enhet för StorSimple 8000-serien. |
| Tillgänglighet i Azure Government-Portal |StorSimple finns i den offentliga Azure-portalen. Mer information finns i [distribuera din lokala StorSimple-enhet i Government Portal](storsimple-8000-deployment-walkthrough-gov-u2.md). |
| Dataskydd och tillgänglighet |StorSimple 8000-serien stöder zonen Redundant lagring (ZRS), förutom lokalt Redundant lagring (LRS) och Geo-redundant lagring (GRS). Referera till [i den här artikeln på Azure Storage redundansalternativ](https://azure.microsoft.com/documentation/articles/storage-redundancy/) ZRS information. |
| Stöd för kritiska program |StorSimple kan du identifiera volymerna lokalt Fäst vilket i sin tur garanterar att data som krävs av kritiska program inte nivåer till molnet. Lokalt fästa volymer omfattas inte av molnet fördröjningar eller problem med nätverksanslutningen. Mer information om lokalt fästa volymer finns [använda Enhetshanteraren för StorSimple-tjänsten för att hantera volymer](storsimple-8000-manage-volumes-u2.md). |
| Låg latens och hög prestanda |Du kan skapa moln installationer som utnyttjar hög prestanda, låg latens funktioner i Azure premium-lagring. Läs mer om StorSimple premium molnet installationer [distribuera och hantera en enhet med StorSimple moln i Azure](storsimple-8000-cloud-appliance-u2.md). |


## <a name="storsimple-components"></a>StorSimple-komponenter
Microsoft Azure StorSimple-lösningen innehåller följande komponenter:

* **Microsoft Azure StorSimple-enhet** – en lokal hybrid-lagringsmatris som innehåller SSD och hårddiskar, tillsammans med funktioner för automatisk redundans och redundanta styrenheter. Domänkontrollanterna hantera lagring skiktning, placera för närvarande används (eller varm) data på lokal lagring (i enheten eller lokala servrar), när mindre ofta använda data till molnet.
* **StorSimple-enhet för molnet** – kallas även den virtuella StorSimple-enhet detta är en programvaruversion av StorSimple-enhet som replikerar arkitekturen och de flesta funktionerna i den fysiska hybrid lagringsenheten. Moln-enheten StorSimple körs på en enskild nod i en virtuell Azure-dator. Premium virtuella enheter, vilket utnyttja Azure premium-lagring finns tillgängliga i uppdatering 2 och senare.
* **StorSimple Enhetshanteraren service** – ett tillägg för Azure-portalen där du kan hantera en StorSimple-enhet eller StorSimple moln installation från ett enda webbgränssnitt. Du kan använda Enhetshanteraren för StorSimple-tjänsten för att skapa och hantera tjänster, visa och hantera enheter, Visa aviseringar, hantera volymer, och visa och hantera principer för säkerhetskopiering och katalogen för säkerhetskopieringen.
* **Windows PowerShell för StorSimple** – ett kommandoradsgränssnitt som du kan använda för att hantera StorSimple-enhet. Windows PowerShell för StorSimple har funktioner som gör att du kan registrera din StorSimple-enhet, konfigurera nätverksgränssnittet på din enhet, installera vissa typer av uppdateringar, felsöka enheten genom att öppna supportsessionen och ändra enhet tillstånd. Du kan komma åt Windows PowerShell för StorSimple genom att ansluta till seriekonsolen eller genom att använda Windows PowerShell-fjärrkommunikation.
* **Azure PowerShell StorSimple-cmdlets** – en samling med Windows PowerShell-cmdlets som gör det möjligt att automatisera aktiviteter och servicenivåer och migrering från kommandoraden. Mer information om Azure PowerShell-cmdlets för StorSimple, gå till den [cmdlet-referens för](/powershell/module/azure/?view=azuresmps-3.7.0#azure).
* **StorSimple Snapshot Manager** – en MMC-snapin-modul som använder volymen grupper och tjänsten Windows Volume Shadow Copy för att generera programkonsekvent säkerhetskopiering. Du kan dessutom använda StorSimple Snapshot Manager för att skapa scheman för säkerhetskopiering och klona eller återställa volymer.
* **StorSimple-kortet för SharePoint** – ett verktyg som utökar transparent lagring och dataskydd i Microsoft Azure StorSimple till SharePoint-servern anläggningar samtidigt StorSimple lagring kan visas och hanteras från SharePoint Central -Administrationsportalen.

Diagrammet nedan ger en övergripande bild av Microsoft Azure StorSimple-arkitektur och komponenter.

![StorSimple-arkitektur](./media/storsimple-overview/overview-big-picture.png)

I följande avsnitt beskrivs var och en av dessa komponenter i detalj och förklara hur lösningen ordnar data allokerar lagringsutrymme och underlättar lagringshantering och dataskydd. Det sista avsnittet innehåller definitioner för några av de viktiga termer och begrepp som är kopplade till StorSimple-komponenter och deras hantering.

## <a name="storsimple-device"></a>StorSimple-enhet
Microsoft Azure StorSimple-enheten är en lokal hybrid-lagringsmatris som innehåller primära lagring och iSCSI-åtkomst till data som lagras på den. Den hanterar kommunikationen med lagringsutrymmet i molnet och hjälper dig för att kontrollera säkerhet och sekretess för alla data som lagras på Microsoft Azure StorSimple-lösning.

StorSimple-enheten inkluderar SSD och hårddiskar hårddiskar samt stöd för klustring och automatisk redundans. Den innehåller en delad processor, delad lagring och två speglade styrenheter. Varje styrenhet innehåller följande:

* Anslutning till en värddator
* Upp till sex nätverksportar att ansluta till det lokala nätverket (LAN)
* Övervakning av maskinvara
* Icke-obeständigt minne (NVRAM) som bevarar information även om strömmen bryts
* Klustermedveten uppdatering för att hantera programuppdateringar på servrar i ett redundanskluster så att uppdateringar har minimal eller ingen effekt på tjänstetillgänglighet
* Klustertjänst, som fungerar som en backend-kluster tillhandahåller hög tillgänglighet och minimera de negativa effekterna som kan uppstå om en Hårddisk eller SSD misslyckas eller kopplas från

Endast en domänkontrollant är aktiv när som helst i tid. Om den aktiva styrenheten misslyckas aktiveras den nya enheten automatiskt.

Mer information finns på [StorSimple status och maskinvarukomponenter](storsimple-8000-monitor-hardware-status.md).

## <a name="storsimple-cloud-appliance"></a>StorSimple Cloud Appliance
Du kan använda StorSimple för att skapa en moln-installation som replikerar arkitektur och funktionerna i den fysiska hybrid lagringsenheten. StorSimple moln installation (även kallat i StorSimple virtuell installation) körs på en enskild nod i en virtuell Azure-dator. (En moln-installation kan bara skapas på en virtuell Azure-dator. Du kan inte skapa en på en StorSimple-enhet eller en lokal server.)

Moln-installation innehåller följande funktioner:

* Den fungerar som en fysisk installation och kan erbjuda ett iSCSI-gränssnitt för virtuella datorer i molnet.
* Du kan skapa ett obegränsat antal installationer i molnet i molnet och aktivera dem på och av efter behov.
* Det kan hjälpa dig att simulera lokala miljöer i katastrofåterställning, utveckling och testscenarier och kan hjälpa dig med objektsnivå från säkerhetskopior.

StorSimple moln installation är tillgängliga i två modeller: enheten 8010 (kallades tidigare 1100 modellen) och 8020 enheten. 8010-enhet har en maximal kapacitet för 30 TB. Enheten 8020 som utnyttjar Azure premium-lagring har en maximal kapacitet för 64 TB. (I lokala nivåerna Azure premium storage lagrar data på SSD medan standardlagring lagrar data på hårddiskar.) Observera att du måste ha ett Azure premium storage-konto för att använda premium-lagring. Mer information om premium-lagring, gå till [Premium-lagring: högpresterande lagring för arbetsbelastningar på virtuella Azure](../virtual-machines/windows/premium-storage.md).

Mer information om StorSimple moln-enhet går du till [distribuera och hantera en enhet med StorSimple moln i Azure](storsimple-8000-cloud-appliance-u2.md).

## <a name="storsimple-device-manager-service"></a>StorSimple enheten Manager-tjänsten
Microsoft Azure StorSimple innehåller ett webbaserat användargränssnitt (StorSimple Device Manager-tjänsten) som gör det möjligt att centralt hantera datacenter och molnlagring. Du kan använda Enhetshanteraren för StorSimple-tjänsten för att utföra följande uppgifter:

* Konfigurera inställningar för StorSimple-enheter.
* Konfigurera och hantera säkerhetsinställningar för StorSimple-enheter.
* Konfigurera autentiseringsuppgifter för molnet och egenskaper.
* Konfigurera och hantera volymer på en server.
* Konfigurera volym grupper.
* Säkerhetskopiera och återställa data.
* Övervaka prestanda.
* Granska systeminställningarna för och identifiera eventuella problem.

Du kan använda Enhetshanteraren för StorSimple-tjänsten för att utföra alla administrationsåtgärder utom de som kräver system stillestånd, till exempel installationen och installation av uppdateringar.

Mer information finns på [använda Enhetshanteraren för StorSimple-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

## <a name="windows-powershell-for-storsimple"></a>Windows PowerShell för StorSimple
Windows PowerShell för StorSimple innehåller ett kommandoradsgränssnitt som kan användas för att skapa och hantera Microsoft Azure StorSimple-tjänsten och konfigurera och övervaka StorSimple-enheter. Det är ett kommandoradsgränssnitt för Windows PowerShell-baserade som innehåller dedikerade cmdletar för att hantera din StorSimple-enhet. Windows PowerShell för StorSimple har funktioner som gör att du kan:

* Registrera en enhet.
* Konfigurera nätverksgränssnittet på en enhet.
* Installera vissa typer av uppdateringar.
* Felsöka din enhet genom att öppna supportsessionen.
* Ändra enhetens tillstånd.

Du kan komma åt Windows PowerShell för StorSimple från seriekonsolen (på en värddator som är anslutna direkt till enheten) eller på distans med hjälp av Windows PowerShell-fjärrkommunikation. Observera att vissa Windows PowerShell för StorSimple uppgifter, till exempel första enhetsregistreringen, kan bara utföras på seriekonsolen.

Mer information finns på [använda Windows PowerShell för StorSimple att administrera din enhet](storsimple-8000-windows-powershell-administration.md).

## <a name="azure-powershell-storsimple-cmdlets"></a>Azure PowerShell StorSimple-cmdlets
Azure PowerShell StorSimple-cmdlets är en samling med Windows PowerShell-cmdlets som gör det möjligt att automatisera aktiviteter och servicenivåer och migrering från kommandoraden. Mer information om Azure PowerShell-cmdlets för StorSimple, gå till den [cmdlet-referens för](/powershell/module/azure/?view=azuresmps-3.7.0).

## <a name="storsimple-snapshot-manager"></a>StorSimple Snapshot Manager
StorSimple Snapshot Manager är en snapin-modul i Microsoft Management Console (MMC) som du kan använda för att skapa enhetliga, point-in-time-säkerhetskopior av lokala och molndata. Snapin-modulen körs på en Windows Server – baserad värd. Du kan använda StorSimple Snapshot Manager till:

* Konfigurera, säkerhetskopiera och ta bort volymer.
* Konfigurera volym grupper så säkerhetskopierade data är programkonsekventa.
* Hantera principer för säkerhetskopiering så att data säkerhetskopieras enligt ett förutbestämt schema och lagras på en angiven plats (lokalt eller i molnet).
* Återställa volymer och enskilda filer.

Säkerhetskopieringar avbildas som ögonblicksbilder som registrerar bara ändringarna sedan den senaste ögonblicksbilden togs och kräver mindre lagringsutrymme än fullständiga säkerhetskopieringar. Du kan skapa scheman för säkerhetskopiering eller vidta omedelbara säkerhetskopieringar efter behov. Du kan dessutom använda StorSimple Snapshot Manager upprätta bevarandeprinciper som styr hur många ögonblicksbilder ska sparas. Om du senare behöver återställa data från en säkerhetskopiering, StorSimple Snapshot Manager kan välja du från katalogen i lokala eller molnögonblicksbilder. 

Om en olycka inträffar, eller om du behöver återställa data för en annan orsak, återställer StorSimple Snapshot Manager det inkrementellt när det behövs. Återställning av data kräver inte att stänga av hela systemet när du återställer en fil, ersätta utrustning eller flytta åtgärder till en annan plats.

Mer information finns på [vad är StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>StorSimple-adapter för SharePoint
Microsoft Azure StorSimple innehåller StorSimple-kortet för SharePoint, en valfri komponent som utökar transparent StorSimple lagring och data protection-funktionerna till SharePoint-servergrupper. Kortet fungerar med en Remote Blob Storage (RBS)-provider och RBS för SQL Server-funktionen, så att du kan flytta Blobbar till en server säkerhetskopieras av Microsoft Azure StorSimple-system. Microsoft Azure StorSimple sedan lagrar BLOB-data lokalt eller i molnet, baserat på användning.

StorSimple-kortet för SharePoint hanteras i Central Administration av SharePoint-portalen. Följaktligen SharePoint management förblir centraliserad och all lagring som ser ut att vara i SharePoint-gruppen.

Mer information finns på [StorSimple-kortet för SharePoint](storsimple-adapter-for-sharepoint.md). 

## <a name="storage-management-technologies"></a>Tekniker för hantering av lagring
Förutom den dedikerade virtuella StorSimple-enheten, virtuella enheten och andra komponenter använder Microsoft Azure StorSimple följande tekniker för program att ger snabb åtkomst till data och minska användningen av lagringsutrymme:

* [Automatisk lagringsnivåer](#automatic-storage-tiering) 
* [Tunn allokering](#thin-provisioning) 
* [Deduplicering och komprimering](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>automatisk lagringsnivåer
Microsoft Azure StorSimple ordnar automatiskt data i logiska nivåer, baserat på aktuell användning, ålder och relation till andra data. Data som är mest aktiva lagras lokalt, medan mindre aktiva och inaktiva data migreras automatiskt till molnet. Följande diagram illustrerar den här metoden för lagring.

![Lagringsnivåer för StorSimple](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

Om du vill aktivera snabb åtkomst lagrar StorSimple mycket aktiv data (varm data) på SSD-enheter på StorSimple-enhet. Den lagrar data som används ibland (varm data) på hårddiskar i enheten eller på servrar på datacentret. Flyttas inaktiva data, säkerhetskopierade data och data som behålls för arkivering eller efterlevnad ändamål till molnet. 

> [!NOTE]
> I uppdatering 2 eller senare, kan du ange en lokalt Fäst volym, i vilket fall data finns kvar på den lokala enheten och är inte nivåer till molnet. 


StorSimple anpassar och ordnar om data och ändra lagring tilldelningar som användningsmönster. Viss information kan till exempel bli mindre aktiva över tid. Vartefter det blir mindre progressivt aktiva flyttas den från SSD till hårddiskar och sedan till molnet. Om samma data blir aktiva igen, flyttas den tillbaka till lagringsenheten.

Lagringsnivåer lagringsprocessen inträffar följande:

1. En administratör ställer in ett Microsoft Azure cloud storage-konto.
2. Administratören använder seriekonsolen och StorSimple Device Manager-tjänsten (som körs i Azure portal) att konfigurera servern enhets- och skapa volymerna och data protection-principer. Lokala datorer (till exempel filservrar) använda den Internet Small Computer System Interface (iSCSI) för att komma åt StorSimple-enhet.
3. Inledningsvis lagrar StorSimple data på enheten snabba SSD-nivån.
4. När SSD-nivån närmar sig kapacitet, StorSimple deduplicates komprimerar de äldsta datablock och flyttar dem till hårddisknivån.
5. StorSimple krypterar de äldsta datablock som nivåkapacitet metoder Hårddisk och skickar dem på ett säkert sätt till Microsoft Azure storage-konto via HTTPS.
6. Microsoft Azure skapar flera kopior av data i sitt datacenter och i en fjärransluten datacenter, se till att data kan återställas om en olycka inträffar.
7. När servern begär data som lagras i molnet, StorSimple returnerar det sömlöst och lagrar en kopia på SSD-nivån av StorSimple-enhet.

#### <a name="how-storsimple-manages-cloud-data"></a>Hur hanterar StorSimple molndata

StorSimple deduplicates kundinformation över alla ögonblicksbilder och primära data (data som skrivs av värdar). Deduplicering är bra för lagringseffektivitet, gör frågan om ”vad är i molnet” komplicerade. Nivåindelad primära data och ögonblicksbilddata överlappar varandra. Ett enda segment av data i molnet kan användas som nivåindelade primära data och också refereras av flera ögonblicksbilder. Varje ögonblicksbild i molnet säkerställer att en kopia av alla tidpunkt i data är låst i molnet tills denna ögonblicksbild tas bort.

Data raderas från molnet endast när det inte finns några referenser till dessa data. Till exempel, om vi tar en ögonblicksbild i molnet för alla data som är i StorSimple-enheten och ta sedan bort primära data vi visas den _primära_ släppa omedelbart. Den _molnet data_ som innehåller nivåindelade data och säkerhetskopior, förblir densamma. Det beror på att det finns en ögonblicksbild refererar fortfarande molninformationen. När molnet ögonblicksbild tas bort (och andra ögonblicksbild som refererar till samma data), cloud förbrukning således. Innan vi tar bort molndata, kontrollera att inga fortfarande hänvisar till dessa data. Den här processen kallas _skräpinsamling_ och är en bakgrundstjänst körs på enheten. Borttagning av molndata är inte omedelbar som skräp samling tjänsten söker efter andra referenser till dessa data innan tas bort. Skräpinsamling hastighet beror på det totala antalet ögonblicksbilder och sammanställa information. Molninformationen rensas vanligtvis mindre än en vecka.


### <a name="thin-provisioning"></a>Tunn allokering
Tunn allokering är en virtualiseringsteknik som tillgängligt lagringsutrymme visas överskrider fysiska resurser. I stället för att reservera tillräckligt med lagringsutrymme i förväg använder StorSimple tunn allokering för att allokera bara tillräckligt med utrymme för att uppfylla krav som ställs. Elastisk komponenternas egenskaper molnlagring underlättar den här metoden eftersom StorSimple kan öka eller minska lagringsutrymmet i molnet för att möta föränderliga behov.

> [!NOTE]
> Lokalt fästa volymer som inte tunt etablerats. Lagringsutrymme som allokerats till en lokal endast volym etableras i sin helhet när volymen har skapats.


### <a name="deduplication-and-compression"></a>Deduplicering och komprimering
Microsoft Azure StorSimple använder deduplicering och data komprimering för att ytterligare minska utrymmeskraven.

Deduplicering minskar den övergripande informationen genom att eliminera redundans i uppsättningen av lagrade data. När information ändras, StorSimple ignorerar oförändrade data och samlar in bara ändringarna. Dessutom minskar StorSimple mängden lagrade data genom att identifiera och ta bort onödig information. 

> [!NOTE]
> Data på lokalt fästa volymer är inte dedupliceras eller komprimerad. Säkerhetskopior av lokalt fästa volymer är dock deduplicerad och komprimeras.


## <a name="storsimple-workload-summary"></a>Översikt över arbetsbelastningar för StorSimple
En sammanfattning av StorSimple-arbetsbelastningar som stöds visas i tabellen nedan.

| Scenario | Arbetsbelastning | Stöds | Begränsningar | Version |
| --- | --- | --- | --- | --- |
| Samarbete |Fildelning |Ja | |Alla versioner |
| Samarbete |Distribuerade fildelning |Ja | |Alla versioner |
| Samarbete |SharePoint |Ja* |Stöds endast med lokalt fästa volymer |Uppdatering 2 och senare |
| Arkivering |Enkel arkivering |Ja | |Alla versioner |
| Virtualisering |Virtuella datorer |Ja* |Stöds endast med lokalt fästa volymer |Uppdatering 2 och senare |
| Databas |SQL |Ja* |Stöds endast med lokalt fästa volymer |Uppdatering 2 och senare |
| Video övervakning |Video övervakning |Ja* |När StorSimple-enhet är dedikerad enbart för arbetsbelastningen som stöds |Uppdatering 2 och senare |
| Säkerhetskopiering |Säkerhetskopiering av primära mål |Ja* |När StorSimple-enhet är dedikerad enbart för arbetsbelastningen som stöds |Uppdatering 3 och senare |
| Säkerhetskopiering |Säkerhetskopiering av sekundära mål |Ja* |När StorSimple-enhet är dedikerad enbart för arbetsbelastningen som stöds |Uppdatering 3 och senare |

*Ja &#42; -Lösningen riktlinjer och begränsningar som ska användas.*

Följande arbetsbelastningar stöds inte av StorSimple 8000-serien enheter. Om distribuerats på StorSimple, kommer dessa arbetsbelastningar resultera i en konfiguration som inte stöds.

* Medicinska avbildning
* Exchange
* VDI
* Oracle
* SAP
* Stordata
* Innehållsdistribution
* Start från SCSI

Nedan följer en lista över infrastrukturkomponenter StorSimple som stöds.

| Scenario | Arbetsbelastning | Stöds | Begränsningar | Version |
| --- | --- | --- | --- | --- |
| Allmänt |Express Route |Ja | |Alla versioner |
| Allmänt |DataCore FC |Ja* |Stöds med DataCore SANsymphony |Alla versioner |
| Allmänt |DFSR |Ja* |Stöds endast med lokalt fästa volymer |Alla versioner |
| Allmänt |Indexering |Ja* |För nivåindelade volymer endast metadata indexering stöds (inga data).<br>För lokalt fästa volymer stöds fullständig indexering. |Alla versioner |
| Allmänt |Antivirusprogram |Ja* |För nivåindelade volymer stöds endast genomsökning på Öppna och Stäng.<br> Fullständig genomsökning har stöd för lokalt fästa volymer. |Alla versioner |

*Ja &#42; -Lösningen riktlinjer och begränsningar som ska användas.*

Nedan följer en lista över andra program som används med StorSimple för att skapa lösningar.

| Arbetsbelastningstyp | Programvara som används med StorSimple | Versioner som stöds|Länka till lösningsguide| 
| --- | --- | --- | --- |
| Mål för säkerhetskopian |Veeam |Veeam v 9 och senare |[StorSimple som ett mål med Veaam](storsimple-configure-backup-target-veeam.md)|
| Mål för säkerhetskopian |Veritas Backup Exec |Backup Exec 16 och senare |[StorSimple som ett mål med Backup Exec](storsimple-configure-backup-target-using-backup-exec.md)|
| Mål för säkerhetskopian |Veritas NetBackup |NetBackup 7.7.x och senare  |[StorSimple som ett mål med NetBackup](storsimple-configure-backuptarget-netbackup.md)|
| Globala fildelning <br></br> Samarbete |Talon  |[StorSimple med Talon](https://www.talonstorage.com/products/fast-deployment-azure-storsimple) | |

## <a name="storsimple-terminology"></a>Terminologi för StorSimple
Innan du distribuerar Microsoft Azure StorSimple-lösningen rekommenderar vi att du läser följande termer och definitioner.

### <a name="key-terms-and-definitions"></a>Viktiga termer och definitioner
| Termen (förkortning eller förkortning) | Beskrivning |
| --- | --- |
| åtkomstkontrollpost (ACR) |En post som är kopplade till en volym på din Microsoft Azure StorSimple-enhet som bestämmer vilka värdar som kan ansluta till den. Bestämning baseras på iSCSI-kvalificerade namn (IQN) för de värdar som ansluter till din StorSimple-enhet (som ingår i ACR). |
| AES 256 |En 256-bitars Advanced Encryption Standard (AES) algoritm för att kryptera data som flyttas till och från molnet. |
| storlek på allokeringsenhet (Australien) |Den minsta mängden diskutrymme som kan tilldelas en fil i ditt Windows-filsystem. Om en filstorlek som inte är en jämn multipel av klusterstorleken extra utrymme måste användas för att lagra fil (upp till nästa multipel av klusterstorleken) ledde till förlorade utrymme och fragmentering på hårddisken. <br>Rekommenderade Australien för Azure StorSimple-volymer är 64 KB eftersom den fungerar med dedupliceringsalgoritmer. |
| automatisk lagringsnivåer |Flytta automatiskt mindre aktiva data från SSD till hårddiskar och sedan till en nivå i molnet och aktivera hantering av alla lagring från ett centralt gränssnitt. |
| Säkerhetskopieringskatalogen |En samling av säkerhetskopieringar, oftast relaterade av programtyp som användes. Den här samlingen visas i bladet säkerhetskopieringskatalog för tjänsten StorSimple Enhetshanteraren Användargränssnittet. |
| säkerhetskopiering katalogfil |En fil som innehåller en lista över tillgängliga ögonblicksbilder som för närvarande lagras i säkerhetskopian av databasen för StorSimple Snapshot Manager. |
| princip för säkerhetskopiering |Ett urval av volymer, typ av säkerhetskopiering och en tidsplan som hjälper dig att skapa säkerhetskopior enligt ett fördefinierat schema. |
| binära stora objekt (BLOB) |En samling för binära data som lagras som en enda enhet i ett system för hantering av databasen. Blobbar är vanligtvis bilder, ljud eller andra multimedia objekt, men ibland binära körbara koden lagras som en BLOB. |
| Challenge Handshake Authentication Protocol (CHAP) |Ett protokoll som används för att autentisera peer för en anslutning, baserat på peer dela ett lösenord eller hemlighet. CHAP kan vara enkelriktade eller ömsesidigt. Enkelriktade CHAP autentiserar målet en initierare. Samtidig CHAP kräver att målet autentisera initieraren och att initieraren autentiserar målet. |
| Klona |En dubblett av en volym. |
| Moln som en nivå (CaaT) |Molnlagring integrerat som en nivå i lagringsarkitekturen så att all lagring som ser ut att vara en del av en enterprise-lagringsnätverk. |
| molntjänstleverantör (CSP) |En leverantör av tjänster för molntjänster. |
| ögonblicksbild i molnet |En tidpunkt i kopia av volymens data som lagras i molnet. En ögonblicksbild i molnet är detsamma som en ögonblicksbild replikeras på ett annat, externt lagringssystem. Molnögonblicksbilder är särskilt användbart i katastrofåterställning. |
| Krypteringsnyckel för molnlagring |Ett lösenord eller en nyckel som används av din StorSimple-enhet för att få åtkomst till krypterade data som skickas av enheten till molnet. |
| klustermedveten uppdatering |Hantera programuppdateringar på servrar i ett redundanskluster så att uppdateringar har minimal eller ingen effekt på tjänstetillgänglighet. |
| DataPath |En samling funktionella enheter som utför åtgärder sammankopplade databearbetning. |
| Inaktivera |En permanent åtgärd som bryter anslutningen mellan StorSimple-enheten och tjänsten associerade molnet. Molnögonblicksbilder av enheten kan kvar efter den här processen och klonas eller användas för katastrofåterställning. |
| diskspegling |Replikering av logisk diskvolymer på olika hårddiskar eller enheter i realtid för att säkerställa kontinuerlig tillgänglighet. |
| dynamisk diskspegling |Replikering av logisk diskvolymer på diskar. |
| dynamiska diskar |En volym diskformat som använder den logiska disken (LDM Manager) för att lagra och hantera data över flera fysiska diskar. Dynamiska diskar kan förstoras för att ge mer ledigt utrymme. |
| Utökade Bunch av diskar (EBOD) hölje |En sekundär inneslutning av din Microsoft Azure StorSimple-enhet som innehåller extra hårddisk diskar för ytterligare lagringsutrymme. |
| FAT-etablering |En konventionell lagringsetablering i vilka storage allokeras utrymmet baserat på förväntat behov (och är vanligtvis utanför det aktuella behovet). Se även *tunn allokering*. |
| hårddisken (HDD) |En enhet som använder roterar skivorna för att lagra data. |
| hybridmolnlagring |Lagringsarkitektur som använder lokala och extern resurser, inklusive lagringsutrymmet i molnet. |
| Internet Small Computer System Interface (iSCSI) |En nätverk standard Internet Protocol IP-baserad lagring för att länka data lagring utrustning eller lokaler. |
| iSCSI-initierare |En programvarukomponent som gör att en värddator som kör Windows för att ansluta till en extern iSCSI-baserad lagring. |
| iSCSI kvalificerade namn (IQN) |Ett unikt namn som identifierar en iSCSI-mål eller initierare. |
| iSCSI-mål |En programvarukomponent som tillhandahåller centraliserad iSCSI-diskundersystem i SAN-nätverk. |
| Live arkivering |En lagring metod där arkiveringsdata är tillgänglig hela tiden (inte lagras utanför kontoret på band, till exempel). Microsoft Azure StorSimple använder live arkivering. |
| lokalt Fäst volym |en volym som finns på enheten och aldrig nivåer till molnet. |
| lokal ögonblicksbild |En tidpunkt i kopia av volymens data som lagras på Microsoft Azure StorSimple-enheten. |
| Microsoft Azure StorSimple |En kraftfull lösning som består av en lagringsenhet för datacenter och programvara som gör att IT-organisationer utnyttja molnlagring som om det vore datacenter lagring. StorSimple förenklar dataskydd och datahantering samtidigt minska kostnaderna. Lösningen konsoliderar primära lagring, arkivera, säkerhetskopiering och katastrofåterställning återställning (DR) via sömlös integration med molnet. Genom att kombinera hantering för SAN-lagring och molnet data på en plattform i företagsklass, aktivera hastighet, enkel och tillförlitlighet för alla lagringsrelaterade StorSimple-enheter. |
| Ström och kylning modul (PCM) |Maskinvarukomponenter för din StorSimple-enhet som består av strömförsörjningar och fläkten, därför namnet ström och kylning modulen. Primär höljet på enheten som har två 764W PCMs EBOD höljet har två 580 w vid PCMs. |
| Primär enhet |Huvudsakliga höljet av StorSimple-enheten som innehåller programmet plattform domänkontrollanterna. |
| Återställning tid mål för Återställningstid |Den maximala tidsperiod som ska vara förbrukad innan en affärsprocess eller system har återställts efter en katastrof. |
| serieansluten SCSI (SAS) |En typ av hårddisken (HDD). |
| krypteringsnyckel för tjänstdata |En nyckel som är tillgängliga för alla nya StorSimple-enheter som registreras med tjänsten StorSimple Enhetshanteraren. Konfigurationsdata som överförs mellan service Manager för StorSimple-enhet och enheten krypteras med en offentlig nyckel och kan sedan dekrypteras endast på enheten med en privat nyckel. Krypteringsnyckel för tjänstdata kan tjänsten för att erhålla den privata nyckeln för dekryptering. |
| Nyckel för tjänstregistrering |En nyckel som kan registrera StorSimple-enhet med StorSimple Device Manager-tjänsten så att den visas i Azure-portalen för ytterligare hanteringsåtgärder för. |
| Small Computer System Interface (SCSI) |En uppsättning standarder för att ansluta datorer och överföra data mellan dem fysiskt. |
| Solid-State-hårddisk (SSD) |En disk som innehåller inga rörliga delar; till exempel flash-enhet. |
| Storage-konto |En uppsättning autentiseringsuppgifter som är kopplad till ditt lagringskonto för en viss molntjänstleverantören. |
| StorSimple-adapter för SharePoint |En komponent i Microsoft Azure StorSimple som utökar transparent StorSimple lagring och dataskydd till SharePoint-servergrupper. |
| StorSimple enheten Manager-tjänsten |En utökning av Azure-portalen där du kan hantera dina Azure StorSimple lokala och virtuella enheter. |
| StorSimple Snapshot Manager |En Microsoft Management Console (MMC) snapin-modulen för hantering av säkerhetskopiering och återställning i Microsoft Azure StorSimple. |
| ta säkerhetskopia |En funktion som gör att användaren kan ta en interaktiv säkerhetskopia av en volym. Det är något annat sätt att göra en manuell säkerhetskopiering för en volym i stället för med en automatisk säkerhetskopiering via en definierad princip. |
| Tunn allokering |En metod för att optimera effektiviteten som används i tillgängligt lagringsutrymme i lagringssystem. I tunn allokering allokeras lagring mellan flera användare baserat på det minsta utrymmet som krävs för varje användare vid en given tidpunkt. Se även *fat etablering*. |
| Skiktning |Ordna data i logiska grupper baserat på aktuell användning, ålder och relation till andra data. StorSimple ordnar automatiskt data i nivåer. |
| Volym |Logiska lagringsutrymmen som visas i form av enheter. StorSimple-volymer motsvarar de volymer som monterats av värden, inklusive de som identifieras med hjälp av iSCSI- och en StorSimple-enhet. |
| volymbehållare |En gruppering av volymerna och de inställningar som gäller för dem. Alla volymer på StorSimple-enhet är grupperade i volymbehållare. Volymen behållaren inställningar inkluderar storage-konton, krypteringsinställningar för data som skickas till molnet med associerade krypteringsnycklar och bandbredd för åtgärder som rör molnet. |
| volymen grupp |StorSimple Snapshot Manager en volym grupp är en samling av volymer som har konfigurerats för att underlätta uppgifter. |
| Tjänsten Volume Shadow Copy (VSS) |En Windows Server operativsystemtjänst som underlättar programkonsekvens genom att kommunicera med VSS-medvetna program att samordna skapandet av inkrementell ögonblicksbilder. VSS säkerställer att programmen är tillfälligt inaktiv när ögonblicksbilder tas. |
| Windows PowerShell för StorSimple |Ett Windows PowerShell-baserade kommandoradsgränssnitt används för att tillämpa och hantera din StorSimple-enhet. Det här gränssnittet har ytterligare dedikerad cmdlets som är inriktad på att hantera en StorSimple-enhet utan att några av de grundläggande funktionerna i Windows PowerShell. |

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [StorSimple-säkerhet](storsimple-8000-security.md).

