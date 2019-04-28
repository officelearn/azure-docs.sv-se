---
title: SAP HANA Azure Backup på filnivå | Microsoft Docs
description: Det finns två viktiga säkerhetskopiering möjligheter för SAP HANA på Azure virtual machines, den här artikeln beskriver SAP HANA Azure Backup på filnivå
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: fc35077e00bc6322a815a52ca6ab3571a4e06d3d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60937806"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>SAP HANA Azure Backup på filnivå

## <a name="introduction"></a>Introduktion

Det här är en del av en serie med tre delar av relaterade artiklar om säkerhetskopiering av SAP HANA. [Säkerhetskopieringsguide för SAP HANA på Azure Virtual Machines](./sap-hana-backup-guide.md) ger en översikt och information om att komma igång och [SAP HANA-säkerhetskopia baserat på ögonblicksbilder av lagring](./sap-hana-backup-storage-snapshots.md) täcker ögonblicksbildbaserade backup alternativet.

Olika typer av virtuella datorer i Azure kan ett annat antal virtuella hårddiskar anslutna. Mer information finns dokumenterade i [storlekar för Linux-datorer i Azure](../../linux/sizes.md). Vi använde en GS5 Azure VM där 64 anslutna datadiskar för tester som anges i den här dokumentationen. För stora SAP HANA-system, kan ett stort antal diskar vara upptaget för data och loggfiler, eventuellt i kombination med programvara striping för optimal disk-i/o-dataflöde. Mer information om föreslagna diskkonfigurationer för distribution av SAP HANA på virtuella Azure-datorer finns i artikeln [SAP HANA på Azure handboken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations). Rekommendationer inklusive diskutrymme rekommendationer för samt lokala säkerhetskopior.

Det finns ingen säkerhetskopiering SAP HANA-integrering med Azure Backup-tjänsten just nu. Standardmetoden för att hantera säkerhetskopiering/återställning på filnivå är med filbaserad säkerhetskopiering via SAP HANA-Studio eller via SAP HANA SQL-uttryck. Se [System vyer referens och SAP HANA SQL](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf) för mer information.

![Den här bilden visar dialogrutan säkerhetskopiering menyalternativets i SAP HANA-Studio](media/sap-hana-backup-file-level/image022.png)

Den här bilden visar dialogrutan säkerhetskopiering menyalternativets i SAP HANA-Studio. När du väljer typ &quot;filen&quot; en har att ange en sökväg i filsystemet där SAP HANA skriver de säkerhetskopiera filerna. Återställningen fungerar på samma sätt.

Det här alternativet låter enkelt och direkt framåt, finns men det några överväganden. Som tidigare nämnts, har en begränsning på antalet datadiskar som kan bifogas i en Azure-dator. Det kanske inte kapacitet för att lagra säkerhetskopiorna för SAP HANA i filsystem för den virtuella datorn, beroende på storleken på dataflödeskrav databasen och disk som kan handla om programvara striping över flera datadiskar. Olika alternativ för att flytta dessa säkerhetskopiorna och hantera begränsningarna som filen storlek och prestanda vid hantering av flera terabyte med data, finns senare i den här artikeln.

Ett annat alternativ, vilket ger större frihet om total kapacitet är Azure blob storage. En enda blob är också begränsad till 1 TB, är den totala kapaciteten för en enda blob-behållare för närvarande 500 TB. Dessutom kan det ger kunderna möjlighet att välja s.k. &quot;lågfrekvent&quot; blob-lagring som har en kostnadsfördel. Se [Azure Blob Storage: Frekvent och lågfrekvent lagringsnivå](../../../storage/blobs/storage-blob-storage-tiers.md) mer information om lågfrekvent åtkomstnivå av blob storage.

För ytterligare säkerhet, använder du ett lagringskonto med geo-replikerade säkerhetskopiorna för SAP HANA. Se [Azure Storage-replikering](../../../storage/common/storage-redundancy.md) mer information om replikering av lagringskontot.

En kunde placera dedikerade virtuella hårddiskar för säkerhetskopior av SAP HANA i en dedikerad backup storage-konto som är geo-replikerade. Annars en gick att kopiera de virtuella hårddiskar som behåller SAP HANA-säkerhetskopior till ett geo-replikerade lagringskonto eller till ett lagringskonto som är i en annan region.

## <a name="azure-backup-agent"></a>Azure backup-agenten

Azure backup erbjuder alternativet att inte bara säkerhetskopiera fullständig virtuella datorer, men filer och kataloger via backup-agenten som måste vara installerad på gästoperativsystemet. Men den här agenten stöds endast på Windows (se [säkerhetskopiera en Windows Server eller klient till Azure med hjälp av Resource Manager-distributionsmodellen](../../../backup/backup-configure-vault.md)).

En lösning är att först kopiera säkerhetskopieringsfilerna för SAP HANA till en virtuell Windows-dator på Azure (till exempel via SAMBA resurs) och sedan använda Azure backup-agenten därifrån. Det är tekniskt möjligt, skulle det lägger till komplexitet och långsammare säkerhetskopieringen eller återställningen ganska lite på grund av kopiering mellan Linux och Windows-VM. Det rekommenderas inte att följa den här metoden.

## <a name="azure-blobxfer-utility-details"></a>Information om Azure blobxfer-verktyget

För att lagra kataloger och filer på Azure storage kan någon använda CLI eller PowerShell eller utveckla ett verktyg med någon av de [Azure SDK: er](https://azure.microsoft.com/downloads/). Det finns också en färdiga att använda verktyget AzCopy, för att kopiera data till Azure storage, men det är bara Windows (se [överföra data med kommandoradsverktyget Azcopy](../../../storage/common/storage-use-azcopy.md)).

Därför användes blobxfer för att kopiera säkerhetskopieringsfilerna för SAP HANA. Den är öppen källkod som används av många kunder i produktionsmiljöer, och är tillgängliga på [GitHub](https://github.com/Azure/blobxfer). Det här verktyget kan en för att kopiera data direkt till Azure blob storage eller Azure-filresurs. Den erbjuder även ett antal användbara funktioner, till exempel md5-hash eller automatisk parallellitet när du kopierar en katalog med flera filer.

## <a name="sap-hana-backup-performance"></a>SAP HANA prestanda vid säkerhetskopiering

![Den här skärmbilden är av SAP HANA säkerhetskopieringskonsol i SAP HANA-Studio](media/sap-hana-backup-file-level/image023.png)

Den här skärmbilden har SAP HANA säkerhetskopieringskonsol i SAP HANA-Studio. Det tog ungefär 42 minuter för att utföra säkerhetskopieringen på 230 GB på en enda Azure standardlagring disk som är ansluten till HANA VM med hjälp av XFS filsystem.

![Den här skärmbilden är av YaST på den Virtuella testdatorn i SAP HANA](media/sap-hana-backup-file-level/image024.png)

Den här skärmbilden har YaST på den Virtuella testdatorn i SAP HANA. En kan se en disk 1 TB för SAP HANA-säkerhetskopia som tidigare nämnts. Det tog ungefär 42 minuter till säkerhetskopiering 230 GB. Dessutom fem 200 GB-diskar var kopplat och programvara RAID md0 skapas med striping ovanpå dessa fem Azure-datadiskar.

![Upprepa samma säkerhetskopieringen av programvara anslutna RAID med striping längs fem datadiskar för Azure standard-lagring](media/sap-hana-backup-file-level/image025.png)

Upprepa samma säkerhetskopieringen av programvara ansluten RAID med striping längs fem Azure standardlagring datadiskar tas säkerhetskopierades från 42 minuter till 10 minuter. Diskarna bifogades utan att cachelagra till den virtuella datorn. Så det är uppenbart hur viktigt disk genomströmning för skrivning avser säkerhetskopierades. Något gick sedan växla till Azure Premium Storage för att ytterligare skynda på processen för optimala prestanda. Azure Premium Storage kan i allmänhet bör användas för produktionssystem.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>Kopiera säkerhetskopieringsfilerna för SAP HANA till Azure blob storage

Ett annat alternativ att snabbt lagra säkerhetskopiorna för SAP HANA är Azure blob storage. En enda blob-behållare har en gräns på 500 TB, räcker för vissa mindre SAP HANA-system, användning M32ts, M32ls, M64ls och virtuella GS5-datorer typer av Azure, för att hålla tillräckligt med SAP HANA-säkerhetskopieringar. Kunderna kan välja mellan &quot;frekvent&quot; och &quot;kalla&quot; blob-lagring (se [Azure Blob Storage: Frekvent och lågfrekvent lagringsnivå](../../../storage/blobs/storage-blob-storage-tiers.md)).

Med verktyget blobxfer är det enkelt att kopiera de säkerhetskopiera filerna för SAP HANA direkt till Azure blob storage.

![Här ser ett filer för en fullständig säkerhetskopia för SAP HANA](media/sap-hana-backup-file-level/image026.png)

Här ser ett filer för en fullständig säkerhetskopia för SAP HANA. Det finns fyra filer och den största som har ungefär 230 GB.

![Det tog ungefär 3000 sekunder att kopiera 230 GB till en Azure standard storage-konto blob-behållare](media/sap-hana-backup-file-level/image027.png)

Inte använder md5-hash i det första testet, tog det ungefär 3000 sekunder att kopiera 230 GB till en Azure standard storage-konto blob-behållare.

![I den här skärmbilden kan en se hur den ser ut på Azure portal](media/sap-hana-backup-file-level/image028.png)

I den här skärmbilden ser en hur den ser ut på Azure portal. En blobbehållare med namnet &quot;sap-hana-säkerhetskopior&quot; har skapats och innehåller fyra objekt, som representerar de säkerhetskopiera filerna för SAP HANA. En av dem har en storlek på ungefär 230 GB.

HANA-Studio säkerhetskopieringskonsol går det att begränsa max filstorlek på HANA säkerhetskopior. I exemplet-miljö bättre prestanda genom att göra det möjligt att ha flera mindre säkerhetskopierade filer i stället för en stor 230 GB-fil.

![Ange den säkerhetskopiera filen storleksgränsen för HANA på klientsidan&#39;t förbättra säkerhetskopieringstiden](media/sap-hana-backup-file-level/image029.png)

Ange den säkerhetskopiera filen storleksgränsen för HANA på klientsidan&#39;t förbättra tid för säkerhetskopiering eftersom filerna skrivs sekventiellt enligt den här bilden. Gränsen för filstorlek har angetts till 60 GB så säkerhetskopian skapas fyra stora datafiler i stället för 230 GB enskild fil. Med hjälp av flera säkerhetskopior är nödvändigt för att säkerhetskopiera HANA-databaser som använder minnet för större virtuella Azure-datorer som M64s, M64ms, M128s och M128ms.

![Om du vill testa parallellitet i verktyget blobxfer angavs sedan den maximala filstorleken för HANA säkerhetskopior till 15 GB](media/sap-hana-backup-file-level/image030.png)

Om du vill testa parallellitet i verktyget blobxfer angavs sedan den maximala filstorleken för HANA säkerhetskopior till 15 GB, vilket resulterade i 19 säkerhetskopior. Den här konfigurationen tas tiden för blobxfer att kopiera 230 GB till Azure blob storage från 3000 sekunder till 875 sekunder.

Resultatet är på grund av högst 60 MB per sekund för att skriva en Azure-blob. Parallellitet via flera blobar löser flaskhalsen, men det finns en nackdel: ökar prestanda i blobxfer-verktyget för att kopiera alla HANA säkerhetskopiorna till Azure blob storage placerar belastningen på både HANA VM och nätverket. Driften av HANA-system blir påverkas.

## <a name="blob-copy-of-dedicated-azure-data-disks-in-backup-software-raid"></a>BLOB-kopia av dedikerade Azure datadiskar i säkerhetskopieringsprogrammet RAID

Till skillnad från disksäkerhetskopiering manuell VM data i den här metoden en loggar säkerhetskopiera alla datadiskar på en virtuell dator för att spara den hela SAP-installationen, inklusive HANA-data, HANA inte filer och konfigurationsfiler. I stället är tanken att ha dedikerade programvaru-RAID med striping över flera Azure data VHD: er för att lagra en fullständig säkerhetskopia för SAP HANA. En kopieras bara dessa diskar, vilket har SAP HANA-säkerhetskopia. De kan enkelt vara sparas i ett särskilt konto för HANA-lagring av säkerhetskopior, eller kopplade till en dedikerad &quot;säkerhetskopiera management VM&quot; för vidare bearbetning.

![Alla virtuella hårddiskar som ingår kopierades med hjälp av den ** start-azurestorageblobcopy ** PowerShell-kommando](media/sap-hana-backup-file-level/image031.png)

När säkerhetskopieringen till den lokala programvaran-RAID har slutförts visas alla virtuella hårddiskar som ingår kopierades med hjälp av den **start azurestorageblobcopy** PowerShell-kommando (se [Start AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy)). Eftersom det påverkar endast dedikerade filsystemet för att behålla de säkerhetskopiera filerna, finns det inga orolig för konsekvens för SAP HANA-data eller log-fil på disken. En fördel med det här kommandot är att den fungerar samtidigt som den virtuella datorn fortfarande är online. Om du vill vara säker på att ingen process skriver till säkerhetskopiering stripe-uppsättning, måste du demontera innan blob-kopia och montera den igen efteråt. En kan också använda ett lämpligt sätt att &quot;Lås&quot; filsystemet. Till exempel via xfs\_Lås för XFS filsystem.

![Den här skärmbilden visar listan över blobar i behållaren virtuella hårddiskar på Azure portal](media/sap-hana-backup-file-level/image032.png)

Den här skärmbilden visar en lista över blobbar i den &quot;virtuella hårddiskar&quot; behållare på Azure portal. Skärmbilden visar de fem VHD: er, som var ansluten till en virtuell dator som fungerar som programvaru-RAID att spara säkerhetskopiorna för SAP HANA för SAP HANA-servern. Den visar även de fem kopiorna vidtogs via kommandot Kopiera blob.

![I testsyfte var kopior av SAP HANA säkerhetskopieringsprogrammet RAID-diskar kopplat till appservern VM](media/sap-hana-backup-file-level/image033.png)

I testsyfte bifogades kopior av SAP HANA säkerhetskopieringsprogrammet RAID-diskar till appservern VM.

![App-serverns virtuella datorn stängdes av för att ansluta disken kopierar](media/sap-hana-backup-file-level/image034.png)

App-serverns virtuella datorn stängdes av att ansluta disken kopierar. När du har startat den virtuella datorn, diskar och RAID har identifierats på rätt sätt (monterad via UUID). Endast monteringspunkten saknades, som har skapats via YaST partitioner. SAP HANA säkerhetskopian kopior blev efteråt synliga på OS-nivå.

## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>Kopiera SAP HANA säkerhetskopieringsfiler till NFS-resurs

Om du vill minska den potentiella påverkan på SAP HANA-system från en prestanda eller diskutrymme perspektiv, kan en Överväg att lagra de säkerhetskopiera filerna för SAP HANA på en NFS-resurs. Tekniskt sett det fungerar, men det innebär att med hjälp av en andra virtuell Azure-dator som värd för NFS-resurs. Det får inte vara en mindre VM-storlek på grund av Virtuella nätverkets bandbredd. Det skulle vara klokt sedan för att stänga det här &quot;säkerhetskopiera VM&quot; och bara ta med dem för att köra SAP HANA-säkerhetskopia. Skriva på en NFS resursen ligger belastningen på nätverket och påverkar SAP HANA-system, men bara hantera därefter de säkerhetskopiera filerna på den &quot;säkerhetskopiera VM&quot; inte skulle påverka SAP HANA-system alls.

![En NFS-resurs från en annan virtuell Azure-dator har monterats till SAP HANA-server-dator](media/sap-hana-backup-file-level/image035.png)

För att verifiera NFS användningsfall, monterades en NFS-resurs från en annan virtuell dator i Azure till SAP HANA-server-dator. Det fanns inga särskilda NFS justering tillämpas.

![Det tog 1 timme och 46 minuter att utföra säkerhetskopieringen direkt](media/sap-hana-backup-file-level/image036.png)

NFS-resurs har en snabb stripe-uppsättning, t.ex. en på SAP HANA-servern. Det tog dock 1 timme och 46 minuter för att utföra säkerhetskopieringen direkt på NFS-resursens i stället för 10 minuter, när skrivning till en lokal stripe värdet.

![Alternativt kan du inte mycket snabbare på 1 timme och 43 minuter](media/sap-hana-backup-file-level/image037.png)

Alternativt kan du göra en säkerhetskopia till en lokal stripe-uppsättning och kopiera till NFS-resurs på operativsystemnivå (en enkel **cp - avr** kommandot) inte mycket snabbare. Det tog 1 timme och 43 minuter.

Så att det fungerar, men prestanda inte bra för testet 230 GB säkerhetskopiering. Det skulle se ut ännu värre för flera terabyte.

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>Kopiera säkerhetskopieringsfilerna för SAP HANA till Azure Files

Det går att montera en Azure Files-resurs i en virtuell Linux-dator. Artikeln [hur du använder Azure File storage med Linux](../../../storage/files/storage-how-to-use-files-linux.md) innehåller information om hur du gör den. Tänk på att det finns för närvarande en kvot på 5 TB av en Azure-filresurs och begränsningar för filstorleken på 1 TB per fil. Se [skalbarhet för lagring av Azure- och prestandamål](../../../storage/common/storage-scalability-targets.md) information om gränser.

Tester har dock visas som SAP HANA-säkerhetskopiering inte&#39;t som för närvarande fungerar direkt med den här typen av CIFS monteringspunkter. Anges också i [SAP anteckning 1820529](https://launchpad.support.sap.com/#/notes/1820529) som CIFS rekommenderas inte.

![Den här bilden visar ett fel i dialogrutan säkerhetskopiering i SAP HANA-Studio](media/sap-hana-backup-file-level/image038.png)

Den här bilden visar ett fel i dialogrutan säkerhetskopiering i SAP HANA Studio när du försöker säkerhetskopiera direkt till en monterad CIFS Azure-filresurs. Ett har att göra en standard säkerhetskopiering för SAP HANA till ett VM-filsystem först och sedan kopiera de säkerhetskopiera filerna därifrån till Azure file service.

![Den här bilden visar att det tog ungefär 929 sekunder för att kopiera 19 SAP HANA-säkerhetskopior](media/sap-hana-backup-file-level/image039.png)

Den här bilden visar att det tog ungefär 929 sekunder för att kopiera 19 SAP HANA säkerhetskopieringsfiler med en total storlek på ungefär 230 GB till Azure-filresursen.

![Källa katalogstrukturen på SAP HANA VM har kopierats till Azure-filresursen](media/sap-hana-backup-file-level/image040.png)

I den här skärmbilden en kan se att källan katalogstrukturen på SAP HANA VM har kopierats till Azure-filresursen: en katalog (hana\_säkerhetskopiering\_fsl\_15 gb) och 19 enskilda säkerhetskopior.

Lagra säkerhetskopior av SAP HANA på Azure files kan vara ett intressant alternativ i framtiden när säkerhetskopior av SAP HANA-filer stöder den direkt. Eller när det blir möjligt att montera Azure files via NFS och den högsta kvotgränsen är betydligt högre än 5 TB.

## <a name="next-steps"></a>Nästa steg
* [Säkerhetskopieringsguide för SAP HANA på Azure Virtual Machines](sap-hana-backup-guide.md) ger en översikt och information om att komma igång.
* [SAP HANA-säkerhetskopia baserat på ögonblicksbilder av lagring](sap-hana-backup-storage-snapshots.md) beskriver ögonblicksbildbaserade backup alternativet.
* Läs hur du etablerar hög tillgänglighet och planera för katastrofåterställning av SAP HANA på Azure (stora instanser) i [SAP HANA (stora instanser) hög tillgänglighet och katastrofåterställning recovery på Azure](hana-overview-high-availability-disaster-recovery.md).
