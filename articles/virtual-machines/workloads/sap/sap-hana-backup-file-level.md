---
title: SAP HANA Azure Backup på fil nivå | Microsoft Docs
description: Det finns två huvudsakliga säkerhets kopierings möjligheter för SAP HANA på virtuella Azure-datorer. den här artikeln handlar om SAP HANA Azure Backup på filnivå
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: 8860c943dafdb9d166510519d0fb058f523537b3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70078901"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>SAP HANA Azure Backup på filnivå

## <a name="introduction"></a>Introduktion

Detta ingår i en serie med tre delar av relaterade artiklar på SAP HANA säkerhets kopiering. [Säkerhets kopierings guiden för SAP HANA på Azure Virtual Machines](./sap-hana-backup-guide.md) ger en översikt och information om hur du kommer igång, och [SAP HANA säkerhets kopiering som baseras på ögonblicks bilder av lagrings enheter](./sap-hana-backup-storage-snapshots.md) täcker alternativet lagrings ögonblicks bild.

Olika VM-typer i Azure tillåter ett annat antal virtuella hård diskar anslutna. De exakta detaljerna dokumenteras i [storlekar för virtuella Linux-datorer i Azure](../../linux/sizes.md). För de tester som hänvisas till i den här dokumentationen använde vi en virtuell Azure-GS5 som tillåter 64 anslutna data diskar. För större SAP HANA system kan ett stort antal diskar redan utföras för data-och loggfiler, eventuellt i kombination med program striping för optimalt disk-i/o-dataflöde. Mer information om de föreslagna disk konfigurationerna för SAP HANA distributioner på virtuella Azure-datorer finns i artikeln [SAP HANA i Azure Operations Guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations). Rekommendationerna är inklusive rekommendationer för disk utrymme för lokala säkerhets kopior.

Det finns ingen SAP HANA säkerhets kopierings integrering tillgänglig med Azure Backups tjänsten för tillfället. Standard sättet att hantera säkerhets kopiering/återställning på filnivå är med en filbaserad säkerhets kopia via SAP HANA Studio eller via SAP HANA SQL-uttryck. Mer information finns i [referens för SAP HANA SQL och system views](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf) .

![Den här bilden visar dialog rutan för meny alternativet Säkerhetskopiera i SAP HANA Studio](media/sap-hana-backup-file-level/image022.png)

Den här bilden visar dialog rutan för meny alternativet Säkerhetskopiera i SAP HANA Studio. När du väljer &quot;typ fil&quot; måste en sökväg anges i fil systemet där SAP HANA skriver säkerhetskopieringsfilerna. Återställ fungerar på samma sätt.

Även om det här alternativet låter enkelt och rakt framåt, finns det några saker att tänka på. Som tidigare nämnts har en virtuell Azure-dator en begränsning av antalet data diskar som kan kopplas. Det kanske inte finns kapacitet att lagra SAP HANA säkerhets kopior på den virtuella datorns fil system, beroende på storleken på databas-och disk data flödes kraven, vilket kan innebära program striping över flera data diskar. Olika alternativ för att flytta de här säkerhetskopierade filerna och hantera fil storleks begränsningar och prestanda vid hantering av terabyte data finns längre fram i den här artikeln.

Ett annat alternativ, som ger mer frihet om total kapacitet, är Azure Blob Storage. Även om en enskild BLOB också är begränsad till 1 TB, är den totala kapaciteten för en enskild BLOB-behållare för närvarande 500 TB. Dessutom ger kunderna möjlighet att välja så kallade &quot;cool&quot; Blob Storage, som har en kostnads fördel. Se [Azure-Blob Storage: Frekventa och låg frekventa](../../../storage/blobs/storage-blob-storage-tiers.md) lagrings nivåer för information om cool Blob Storage.

För ytterligare säkerhet använder du ett geo-replikerat lagrings konto för att lagra säkerhets kopiorna för SAP HANA. Mer information om replikering av lagrings konton finns i [Azure Storage replikering](../../../storage/common/storage-redundancy.md) .

Det gick att placera dedikerade virtuella hård diskar för SAP HANA säkerhets kopieringar i ett dedikerat lagrings konto med geo-replikering. Eller en annan kan kopiera de virtuella hård diskar som behåller SAP HANA säkerhets kopieringar till ett geo-replikerat lagrings konto, eller till ett lagrings konto i en annan region.

## <a name="azure-backup-agent"></a>Azure Backup-Agent

Azure Backup erbjuder alternativet att inte bara säkerhetskopiera fullständiga virtuella datorer, utan även filer och kataloger via säkerhets kopierings agenten, som måste installeras på gäst operativ systemet. Men den här agenten stöds bara i Windows (se [säkerhetskopiera en Windows-Server eller-klient till Azure med hjälp av distributions modellen för Resource Manager](../../../backup/backup-configure-vault.md)).

En lösning är att först kopiera SAP HANA säkerhetskopierade filer till en virtuell Windows-dator på Azure (till exempel via SAMBA-resurs) och sedan använda Azure Backup-agenten därifrån. Även om det är tekniskt möjligt, skulle den kunna lägga till komplexitet och sakta ned säkerhets kopieringen eller återställningen en bit på grund av kopieringen mellan Linux och den virtuella Windows-datorn. Vi rekommenderar inte att du följer den här metoden.

## <a name="azure-blobxfer-utility-details"></a>Information om Azure blobxfer-verktyget

Om du vill lagra kataloger och filer i Azure Storage kan du använda CLI eller PowerShell eller utveckla ett verktyg med någon av [Azure SDK: erna](https://azure.microsoft.com/downloads/). Det finns också ett verktyg som är redo att använda, AzCopy, för att kopiera data till Azure Storage. (mer information finns i [överföra data med kommando rads verktyget AzCopy](../../../storage/common/storage-use-azcopy.md)).

Därför användes blobxfer för att kopiera SAP HANA säkerhetskopierade filer. Det är öppen källkod som används av många kunder i produktions miljöer och är tillgängliga på [GitHub](https://github.com/Azure/blobxfer). Med det här verktyget kan du kopiera data direkt till antingen Azure Blob Storage eller Azure-filresursen. Den erbjuder också en mängd användbara funktioner som MD5-hash eller automatisk parallellitet när du kopierar en katalog med flera filer.

## <a name="sap-hana-backup-performance"></a>SAP HANA säkerhets kopierings prestanda

![Den här skärm bilden är av SAP HANA säkerhets kopierings konsolen i SAP HANA Studio](media/sap-hana-backup-file-level/image023.png)

Den här skärm bilden är av SAP HANA säkerhets kopierings konsolen i SAP HANA Studio. Det tog ungefär 42 minuter att säkerhetskopiera 230 GB på en enskild Azure standard Storage-disk som är ansluten till den virtuella HANA-datorn med XFS-filsystem.

![Den här skärm bilden är av YaST på den virtuella test datorn för SAP HANA](media/sap-hana-backup-file-level/image024.png)

Den här skärm bilden är av YaST på den virtuella test datorn för SAP HANA. En kan se en enskild disk på 1 TB för SAP HANA säkerhets kopia som nämnts tidigare. Det tog cirka 42 minuter att säkerhetskopiera 230 GB. Dessutom var 5 200 GB-diskar anslutna och programvaru-RAID-md0 som skapats, med randning ovanpå dessa fem Azure-datadiskar.

![Upprepa samma säkerhets kopiering på programvaru-RAID med striping över fem anslutna Azure standard Storage-datadiskar](media/sap-hana-backup-file-level/image025.png)

Upprepa samma säkerhets kopiering på programvaru-RAID med striping över fem anslutna Azure standard Storage-datadiskar som tog säkerhets kopierings tiden från 42 minuter till 10 minuter. Diskarna har kopplats utan cachelagring till den virtuella datorn. Det är därför självklart hur viktiga disk skrivnings data flöde är för säkerhets kopierings tiden. En kan sedan växla till Azure Premium Storage för att ytterligare påskynda processen för optimala prestanda. I allmänhet bör Azure Premium Storage användas för produktions system.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>Kopiera SAP HANA säkerhetskopierade filer till Azure Blob Storage

Ett annat alternativ för att snabbt lagra SAP HANA säkerhetskopierade filer är Azure Blob Storage. En enda BLOB-behållare har en gräns på 500 TB, tillräckligt för vissa mindre SAP HANA system, med hjälp av M32ts, M32ls, M64ls och GS5 VM-typer för Azure för att behålla tillräckligt med SAP HANA säkerhets kopieringar. Kunder kan &quot;välja mellan&quot; frekvent och &quot;kall&quot; blob-lagring (se [Azure Blob Storage: Frekventa och låg frekventa](../../../storage/blobs/storage-blob-storage-tiers.md)lagrings nivåer).

Med blobxfer-verktyget är det enkelt att kopiera SAP HANA säkerhetskopierade filer direkt till Azure Blob Storage.

![Här kan du se filerna för en fullständig SAP HANA fil säkerhets kopiering](media/sap-hana-backup-file-level/image026.png)

Här kan du se filerna för en fullständig SAP HANA fil säkerhets kopiering. Det finns fyra filer och den största som är ungefär 230 GB.

![Det tog ungefär 3000 sekunder att kopiera 230 GB till en BLOB-behållare för Azure standard Storage-konto](media/sap-hana-backup-file-level/image027.png)

Om du inte använder MD5-hash i det första testet tog det ungefär 3000 sekunder att kopiera 230 GB till ett Azure standard Storage-konto BLOB-behållare.

![I den här skärm bilden kan en se hur den ser ut på Azure Portal](media/sap-hana-backup-file-level/image028.png)

I den här skärm bilden kan en se hur den ser ut på Azure Portal. En BLOB-behållare &quot;med namnet SAP-HANA&quot; -backups har skapats och innehåller fyra blobbar som representerar SAP HANA säkerhets kopierings filer. En av dem har en storlek på ungefär 230 GB.

Säkerhets kopierings konsolen för HANA Studio tillåter att en maximal fil storlek på HANA-säkerhetskopieringsfiler begränsas. I exempel miljön förbättrade prestanda genom att göra det möjligt att ha flera mindre säkerhetskopieringsfiler, i stället för en stor 230 GB-fil.

![Om du anger storleks gränsen för säkerhets kopiering på HANA-&#39;sidan går det inte att förbättra säkerhets kopierings tiden](media/sap-hana-backup-file-level/image029.png)

Om du anger storleks gränsen för säkerhets kopiering på HANA-&#39;sidan går det inte att förbättra säkerhets kopierings tiden, eftersom filerna skrivs i tur och ordning som de visas i bilden. Gränsen för fil storlek har angetts till 60 GB, så säkerhets kopieringen skapade fyra stora datafiler i stället för en enda fil med 230 GB. Det är nödvändigt att använda flera säkerhetskopieringsfiler för att säkerhetskopiera HANA-databaser som utnyttjar minnet för större virtuella Azure-datorer som M64s, M64ms, M128s och M128ms.

![För att testa parallellitet i blobxfer-verktyget, ställdes den maximala fil storleken för HANA-säkerhetskopiering sedan till 15 GB](media/sap-hana-backup-file-level/image030.png)

För att testa parallellitet i blobxfer-verktyget, angavs den maximala fil storleken för HANA-säkerhetskopiering sedan till 15 GB, vilket resulterade i 19 säkerhetskopieringsfiler. Den här konfigurationen tog tid för blobxfer att kopiera 230 GB till Azure Blob Storage från 3000 sekunder till 875 sekunder.

Detta beror på en gräns på 60 MB/SEK för skrivning av en Azure-blob. Parallellitet via flera blobbar löser Flask halsen, men det finns en minnes läcka: öka prestanda för blobxfer-verktyget för att kopiera alla dessa HANA-säkerhetskopierade filer till Azure Blob Storage sätter belastning både på den virtuella HANA-datorn och nätverket. Driften av HANA-systemet påverkas.

## <a name="blob-copy-of-dedicated-azure-data-disks-in-backup-software-raid"></a>BLOB-kopia av dedikerade Azure-datadiskar i säkerhets kopierings program RAID

Till skillnad från säkerhets kopieringen av den manuella säkerhets kopian av virtuella datorer, finns det ingen säkerhets kopia av alla data diskar på en virtuell dator för att spara hela SAP-installationen, inklusive HANA-data, HANA-loggfiler och konfigurationsfiler. I stället är tanken att ha dedikerad programvaru-RAID med striping i flera Azure Data-VHD: er för lagring av en fullständig SAP HANA fil säkerhets kopiering. En kopierar bara de här diskarna, som har SAP HANA säkerhets kopian. De kan enkelt behållas i ett dedikerat Hana-lagrings konto för säkerhets kopiering &quot;eller bifogas&quot; till en dedikerad VM-hantering för ytterligare bearbetning.

![Alla virtuella hård diskar som ingår har kopierats med kommandot * * start-azurestorageblobcopy * * PowerShell](media/sap-hana-backup-file-level/image031.png)

När säkerhets kopieringen till den lokala program varan RAID slutfördes kopierades alla virtuella hård diskar med hjälp av kommandot **Start-azurestorageblobcopy** PowerShell (se [Start-azurestorageblobcopy](/powershell/module/azure.storage/start-azurestorageblobcopy)). Eftersom det bara påverkar det dedikerade fil systemet för att behålla säkerhets kopiorna, finns det inga problem med att SAP HANA data-eller logg fils konsekvens på disken. En fördel med det här kommandot är att det fungerar när den virtuella datorn förblir online. För att vara säker på att ingen process skriver till säkerhets kopie stripe-uppsättningen, se till att demontera den innan BLOB-kopian och montera den igen efteråt. Eller en kan använda ett lämpligt sätt för &quot;att&quot; låsa fil systemet. Till exempel, via xfs\_Freeze för fil systemet xfs.

![Den här skärm bilden visar en lista över blobar i behållaren för virtuella hård diskar på Azure Portal](media/sap-hana-backup-file-level/image032.png)

Den här skärm bilden visar en lista över blobar &quot;i&quot; behållaren för virtuella hård diskar på Azure Portal. Skärm bilden visar de fem virtuella hård diskarna som var kopplade till den virtuella SAP HANA Server-datorn för att bevara SAP HANA säkerhets kopierings filer. Den visar även de fem kopiorna som togs via kommandot BLOB Copy.

![I test syfte anslöts kopiorna av RAID-diskarna för SAP HANA säkerhets kopierings program vara till den virtuella App Server-datorn](media/sap-hana-backup-file-level/image033.png)

I test syfte anslöts kopiorna av RAID-diskarna för SAP HANA säkerhets kopierings program vara till den virtuella App Server-datorn.

![Den virtuella App Server-datorn stängdes för att koppla disk kopiorna](media/sap-hana-backup-file-level/image034.png)

Den virtuella App Server-datorn stängdes för att bifoga disk kopiorna. När den virtuella datorn har startats identifierades diskarna och RAID-enheterna korrekt (monterade via UUID). Endast monterings punkten saknades, som skapades via YaST-partitioner. Därefter visas SAP HANA säkerhets kopia av säkerhets kopian på operativ Systems nivå.

## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>Kopiera SAP HANA säkerhetskopierade filer till NFS-resursen

För att minska den potentiella påverkan på SAP HANA systemet från ett prestanda-eller disk utrymmes perspektiv kan det vara en bra idé att lagra SAP HANA säkerhets kopior på en NFS-resurs. Tekniskt sett fungerar det, men det innebär att använda en andra virtuell Azure-dator som värd för NFS-resursen. Det får inte vara en liten VM-storlek på grund av bandbredden för den virtuella datorn. Det skulle vara meningsfullt att stänga av den &quot;virtuella&quot; säkerhets kopian och bara ta den för att köra den SAP HANA säkerhets kopieringen. Om du skriver på en NFS-resurs används belastningen på nätverket och det påverkar SAP HANA systemet, men bara hanteringen av säkerhetskopieringsfilerna Efteråt &quot;på den&quot; virtuella säkerhets kopian påverkar inte SAP HANA systemet alls.

![En NFS-resurs från en annan virtuell Azure-dator monterades på den virtuella SAP HANA-servern](media/sap-hana-backup-file-level/image035.png)

För att verifiera NFS-användnings fallet monterades en NFS-resurs från en annan virtuell Azure-dator till den virtuella SAP HANA-servern. Ingen särskild NFS-justering tillämpades.

![Det tog 1 timme och 46 minuter att säkerhetskopiera direkt](media/sap-hana-backup-file-level/image036.png)

NFS-resursen var en fast stripe-uppsättning, som den som finns på SAP HANA-servern. Det tog dock 1 timme och 46 minuter att göra säkerhets kopieringen direkt på NFS-resursen i stället för 10 minuter, vid skrivning till en lokal stripe-uppsättning.

![Det alternativa är inte mycket snabbare vid 1 timme och 43 minuter](media/sap-hana-backup-file-level/image037.png)

Alternativet att göra en säkerhets kopia till en lokal stripe-uppsättning och kopiera till NFS-resursen på operativ system nivå (ett enkelt **CP-AVR** kommando) var inte mycket snabbare. Det tog 1 timme och 43 minuter.

Det fungerar, men prestanda är inte lämpligt för säkerhets kopierings testet 230 GB. Det skulle se ännu sämre ut i flera terabyte.

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>Kopiera SAP HANA säkerhets kopierings filer till Azure Files

Det går att montera en Azure Files resurs inuti en virtuell Azure Linux-dator. I artikeln [hur du använder Azure File Storage med Linux](../../../storage/files/storage-how-to-use-files-linux.md) får du information om hur du gör det. Tänk på att det för närvarande finns en kvot gräns på 5 TB för en Azure-filresurs och en fil storleks gräns på 1 TB per fil. Se [Azure Storage skalbarhets-och prestanda mål](../../../storage/common/storage-scalability-targets.md) för information om lagrings gränser.

Testerna har visat att SAP HANA säkerhets kopieringen för närvarande&#39;inte fungerar direkt med den här typen av CIFS-montering. Den anges också i [SAP anmärkning 1820529](https://launchpad.support.sap.com/#/notes/1820529) att CIFS inte rekommenderas.

![Den här bilden visar ett fel i dialog rutan säkerhets kopiering i SAP HANA Studio](media/sap-hana-backup-file-level/image038.png)

Den här bilden visar ett fel i dialog rutan säkerhets kopiering i SAP HANA Studio när du försöker säkerhetskopiera direkt till en CIFS-monterad Azure-filresurs. Därför måste en standard SAP HANA säkerhets kopiering till ett VM-filsystem först och sedan kopiera säkerhetskopieringsfilerna från dit till Azure File Service.

![Den här bilden visar att det tog cirka 929 sekunder att kopiera 19 SAP HANA säkerhetskopierade filer](media/sap-hana-backup-file-level/image039.png)

Den här bilden visar att det tog cirka 929 sekunder att kopiera 19 SAP HANA säkerhets kopierings filer med en total storlek på ungefär 230 GB till Azure-filresursen.

![Käll katalog strukturen på den virtuella SAP HANA-datorn kopierades till Azure-filresursen](media/sap-hana-backup-file-level/image040.png)

I den här skärm bilden kan en se att käll katalog strukturen på den virtuella SAP HANA-datorn kopierades till Azure-filresursen: en\_katalog\_(\_Hana backup FSL 15gb) och 19 enskilda säkerhetskopieringsfiler.

Att lagra SAP HANA säkerhets kopierings filer på Azure Files kan vara ett intressant alternativ i framtiden när SAP HANA fil säkerhets kopieringar stöder det direkt. Eller när det blir möjligt att montera Azure Files via NFS och den maximala kvot gränsen är betydligt högre än 5 TB.

## <a name="next-steps"></a>Nästa steg
* [Säkerhets kopierings guiden för SAP HANA på Azure Virtual Machines](sap-hana-backup-guide.md) ger en översikt och information om att komma igång.
* [SAP HANA säkerhets kopiering baserat på lagrings ögonblicks bilder](sap-hana-backup-storage-snapshots.md) beskriver alternativet lagrings ögonblicks bild-baserad säkerhets kopiering.
* Information om hur du upprättar hög tillgänglighet och planerar för haveri beredskap för SAP HANA på Azure (stora instanser) finns i [SAP HANA (stora instanser) hög tillgänglighet och haveri beredskap på Azure](hana-overview-high-availability-disaster-recovery.md).
