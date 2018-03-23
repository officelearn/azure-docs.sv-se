---
title: SAP HANA Azure Backup på filnivå | Microsoft Docs
description: Det finns två huvudsakliga säkerhetskopiering möjligheter för SAP HANA på Azure virtual machines, den här artikeln beskriver SAP HANA Azure Backup på filnivå
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 3/13/2017
ms.author: rclaus
ms.openlocfilehash: 5db0ceb1648b5afa278e1cbe1c42fce8033bfdc1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="sap-hana-azure-backup-on-file-level"></a>SAP HANA Azure Backup på filnivå

## <a name="introduction"></a>Introduktion

Detta är en del av en serie i tre delar av relaterade artiklar på SAP HANA-säkerhetskopia. [Säkerhetskopiering guide för SAP HANA på Azure Virtual Machines](./sap-hana-backup-guide.md) innehåller en översikt och information om att komma igång och [SAP HANA säkerhetskopiering baserat på lagring ögonblicksbilder](./sap-hana-backup-storage-snapshots.md) omfattar det ögonblicksbild-baserad säkerhetskopiering lagringsalternativet.

Tittar på Azure VM-storlekar, kan en se att en GS5 tillåter 64 anslutna diskar. För stora SAP HANA vidtagit ett stort antal diskar redan för data och loggfiler, eventuellt i kombination med programvarubaserad RAID för disken i/o-genomflöde. Frågan sedan är var du vill lagra SAP HANA säkerhetskopierade filer, vilket kan fylla diskarna bifogade data över tid? Se [storlekar för virtuella Linux-datorer i Azure](../../linux/sizes.md) för tabellerna i Azure VM-storlek.

Det finns ingen säkerhetskopiering SAP HANA-integrering med Azure Backup-tjänsten just nu. Det vanliga sättet att hantera säkerhetskopiering/återställning på filnivå är med en filbaserad säkerhetskopia via SAP HANA Studio eller via SAP HANA SQL-instruktioner. Se [System vyer referens och SAP HANA SQL](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf) för mer information.

![Den här bilden visar dialogrutan säkerhetskopiering menyalternativets i SAP HANA Studio](media/sap-hana-backup-file-level/image022.png)

Den här bilden visar dialogrutan säkerhetskopiering menyalternativets i SAP HANA Studio. När du väljer typen &quot;filen&quot; ett måste ange en sökväg i filsystemet där SAP HANA skriver de säkerhetskopiera filerna. Återställningen fungerar på samma sätt.

Det här alternativet låter enkel och direkt framåt, finns men det några överväganden. Som tidigare nämnts, har en begränsning av antalet datadiskar som kan bifogas en Azure VM. Det kanske inte kapacitet att lagra säkerhetskopiorna för SAP HANA på filsystem för den virtuella datorn, beroende på storleken på databas och disk genomströmning krav, som kan handla om programvara RAID med striping över flera diskar. Olika alternativ för att flytta dessa säkerhetskopior, och hantera filen storlek restriktioner och prestanda vid hantering av terabyte data, tillhandahålls nedan.

Ett annat alternativ som ger större frihet om total kapacitet är Azure blob storage. Även om en enda blob är också begränsad till 1 TB, är den totala kapaciteten för en enda blob-behållare för närvarande 500 TB. Dessutom kan den ger kunder möjlighet att välja s.k. &quot;kall&quot; blob-lagring, som har en kostnad. Se [Azure Blob Storage: frekvent och kall lagringsnivåer](../../../storage/blobs/storage-blob-storage-tiers.md) för ytterligare information om kall blob-lagring.

Använd ett geo-replikerade storage-konto för ytterligare säkerhet för SAP HANA säkerhetskopieringar. Se [Azure Storage-replikering](../../../storage/common/storage-redundancy.md) mer information om replikering av lagringskonton.

En kunde placera dedikerade virtuella hårddiskar för SAP HANA säkerhetskopieringar i en dedikerad backup storage-konto som är georeplikerad. Annars en gick att kopiera de virtuella hårddiskar som behåller SAP HANA-säkerhetskopior till ett geo-replikerade lagringskonto eller till ett lagringskonto som är i en annan region.

## <a name="azure-backup-agent"></a>Azure backup-agenten

Azure-säkerhetskopiering ger möjlighet att inte bara säkerhetskopiera fullständig virtuella datorer, men filer och kataloger via backup-agenten som måste installeras på gästoperativsystemet. Men från och med December 2016 agenten bara stöds på Windows (se [säkerhetskopiera en Windows Server eller klient till Azure med hjälp av Resource Manager-distributionsmodellen](../../../backup/backup-configure-vault.md)).

En lösning är att först kopiera SAP HANA säkerhetskopieringsfiler till en virtuell Windows-dator i Azure (till exempel via SAMBA-resurs) och sedan använda Azure backup-agenten därifrån. Det är tekniskt möjligt, skulle den lägger till komplexitet och långsammare säkerhetskopieringen eller återställningen ganska lite på grund av att kopiera mellan Linux och Windows-VM. Det rekommenderas inte att följa den här metoden.

## <a name="azure-blobxfer-utility-details"></a>Information om verktyget Azure blobxfer

Om du vill lagra kataloger och filer på Azure-lagring kan en använda CLI eller PowerShell eller utveckla ett verktyg med någon av de [Azure SDK](https://azure.microsoft.com/downloads/). Det finns också en färdiga att använda verktyget AzCopy, för att kopiera data till Azure storage, men det är bara Windows (se [överföra data med kommandoradsverktyget Azcopy](../../../storage/common/storage-use-azcopy.md)).

Därför användes blobxfer för att kopiera säkerhetskopieringsfilerna för SAP HANA. Det är öppen källkod som används av många kunder i produktionsmiljöer och finns på [GitHub](https://github.com/Azure/blobxfer). Det här verktyget går det att kopiera data direkt till Azure blob storage eller Azure-filresursen. Den erbjuder också ett antal användbara funktioner, till exempel md5-hash eller automatisk parallellitet när du kopierar en katalog med flera filer.

## <a name="sap-hana-backup-performance"></a>SAP HANA säkerhetskopieringsprestanda

![Den här skärmbilden har konsolen SAP HANA säkerhetskopiering i SAP HANA Studio](media/sap-hana-backup-file-level/image023.png)

Den här skärmbilden har konsolen SAP HANA säkerhetskopiering i SAP HANA Studio. Det tog ungefär 42 minuter för att utföra säkerhetskopieringen 230 GB på en enda Azure standardlagring disk som är ansluten till HANA VM XFS-filsystemet.

![Den här skärmbilden har YaST på den Virtuella testdatorn i SAP HANA](media/sap-hana-backup-file-level/image024.png)

Den här skärmbilden har YaST på den Virtuella testdatorn i SAP HANA. En kan se 1 TB enskild disk för SAP HANA-säkerhetskopiering som tidigare nämnts. Det tog ungefär 42 minuter till säkerhetskopiering 230 GB. Dessutom fem 200 GB diskar har anslutits och programvara RAID md0 skapats med striping ovanpå diskarna fem Azure data.

![Upprepa samma säkerhetskopia på programvara anslutna RAID med striping över fem Azure standardlagring datadiskar](media/sap-hana-backup-file-level/image025.png)

Upprepa samma säkerhetskopia på programvara koppla RAID med striping över fem Azure standardlagring datadiskar tas säkerhetskopieringstiden 42 minuter till 10 minuter. Diskarna bifogades utan att cachelagra till den virtuella datorn. Det är därför uppenbara hur viktigt disk genomströmning för skrivning är för tid för säkerhetskopiering. Något gick sedan växla till Azure premium-lagring till ytterligare skynda på processen för optimala prestanda. I allmänhet ska Azure premium-lagring användas för produktionssystem.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>Kopiera SAP HANA säkerhetskopieringsfiler till Azure blob storage

Från och med December 2016 är det bästa alternativet att snabbt lagra säkerhetskopiorna för SAP HANA Azure blob storage. En enda blob-behållare är begränsad till 500 TB tillräckligt för de flesta SAP HANA-system, körs i en GS5 virtuell dator på Azure för att hålla tillräckligt SAP HANA-säkerhetskopieringar. Kunderna kan välja mellan &quot;varm&quot; och &quot;kalla&quot; blob-lagring (se [Azure Blob Storage: frekvent och kall lagringsnivåer](../../../storage/blobs/storage-blob-storage-tiers.md)).

Med verktyget blobxfer är det lätt att kopiera de säkerhetskopiera filerna för SAP HANA direkt till Azure blob storage.

![Här ser en filerna för en fullständig säkerhetskopia för SAP HANA](media/sap-hana-backup-file-level/image026.png)

Här ser en filerna för en fullständig säkerhetskopia för SAP HANA. Det finns fyra filer och den största som har ungefär 230 GB.

![Det tog ungefär 3000 sekunder att kopiera 230 GB till Azure standardlagring konto blob-behållare](media/sap-hana-backup-file-level/image027.png)

Inte använder md5-hash i det första testet, som det tog ungefär 3000 sekunder att kopiera 230 GB till Azure standardlagring konto blob-behållare.

![I den här skärmbilden kan en se hur den ser ut på Azure portal](media/sap-hana-backup-file-level/image028.png)

I den här skärmbilden kan en se hur den ser ut på Azure-portalen. En blobbbehållare med namnet &quot;sap-hana-säkerhetskopior&quot; har skapats och innehåller fyra blobar som representerar de säkerhetskopiera filerna för SAP HANA. En av dem har en storlek på ungefär 230 GB.

Konsolen HANA Studio backup går det att begränsa den maximala filstorleken på HANA säkerhetskopior. I exempel-miljö bättre prestanda genom att göra det möjligt att ha flera mindre säkerhetskopierade filer i stället för en stor 230 GB-fil.

![Ange den säkerhetskopiera filen storleksgränsen på HANA på klientsidan&#39;t förbättra tid för säkerhetskopiering](media/sap-hana-backup-file-level/image029.png)

Ange den säkerhetskopiera filen storleksgränsen på HANA på klientsidan&#39;t förbättra tid för säkerhetskopiering eftersom filerna skrivs i tur och ordning som visas i bilden. Maximal filstorlek har angetts till 60 GB så säkerhetskopian skapas fyra stora datafiler i stället för 230 GB enskild fil.

![Om du vill testa parallellitet i verktyget blobxfer angavs sedan den maximala filstorleken för HANA säkerhetskopieringar till 15 GB](media/sap-hana-backup-file-level/image030.png)

Om du vill testa parallellitet i verktyget blobxfer angavs sedan den maximala filstorleken för HANA säkerhetskopieringar till 15 GB, vilket resulterade i 19 säkerhetskopior. Den här konfigurationen tas tiden för blobxfer att kopiera 230 GB till Azure blob storage från 3000 sekunder till 875 sekunder.

Detta beror på högst 60 MB per sekund för att skriva en Azure blob. Parallellitet via flera BLOB löser flaskhals, men det finns en nackdelen: öka prestanda i blobxfer-verktyget för att kopiera alla dessa HANA säkerhetskopieringsfiler till Azure blob storage placerar belastningen på både HANA VM och nätverk. HANA systemet blir påverkas.

## <a name="blob-copy-of-dedicated-azure-data-disks-in-backup-software-raid"></a>BLOB-kopia av dedikerade Azure diskar i säkerhetskopieringsprogrammet RAID

Till skillnad från disksäkerhetskopiering manuell VM data i den här metoden en loggar säkerhetskopiera alla datadiskar på en virtuell dator för att spara hela SAP installationen, inklusive HANA data HANA inte filer och konfigurationsfiler. I stället är idé att har särskilda programvarubaserad RAID med striping över flera Azure data virtuella hårddiskar för lagring av en fullständig säkerhetskopia för SAP HANA. En kopieras endast de diskar som har SAP HANA-säkerhetskopia. De enkelt kan sparas i ett dedikerat HANA backup storage-konto, eller anslutna till en dedikerad &quot;säkerhetskopiera management VM&quot; för vidare bearbetning.

![Alla virtuella hårddiskar som har kopierats med hjälp av den ** start-azurestorageblobcopy ** PowerShell-kommando](media/sap-hana-backup-file-level/image031.png)

När säkerhetskopieringen till den lokala programvarubaserad RAID slutfördes alla virtuella hårddiskar som har kopierats med hjälp av den **start azurestorageblobcopy** PowerShell-kommando (finns [Start AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy)). Eftersom det påverkar endast dedikerade filsystemet för att behålla de säkerhetskopiera filerna, finns det inga frågor om konsekvens för SAP HANA-data eller loggfil fil på disken. En fördel med det här kommandot är att det fungerar samtidigt som den virtuella datorn fortfarande är online. Om du vill vara säker på att det finns ingen process skrivs säkerhetskopiering stripe-uppsättningen, bör du demontera innan blob-kopia och montera den igen efteråt. En kan också använda ett lämpligt sätt att &quot;låsa&quot; filsystemet. Till exempel via xfs\_Lås för XFS filsystem.

![Den här skärmbilden visar en lista över blobbar i behållaren virtuella hårddiskar på Azure portal](media/sap-hana-backup-file-level/image032.png)

Den här skärmbilden visar en lista över blobbar i den &quot;virtuella hårddiskar&quot; behållare på Azure-portalen. Skärmbilden visar de virtuella hårddiskarna fem som var kopplat till SAP HANA-server VM som fungerar som programvarubaserad RAID att hålla SAP HANA säkerhetskopior. Den visar även de fem kopiorna som togs via kommandot Kopiera blob.

![I testsyfte kan var kopior av programvaran för säkerhetskopiering RAID-diskar för SAP HANA kopplat till app-servern VM](media/sap-hana-backup-file-level/image033.png)

I testsyfte kan bifogades kopior av SAP HANA säkerhetskopieringsprogrammet RAID-diskar till app-servern VM.

![App-serverns Virtuella stängdes för att ansluta disken kopieras](media/sap-hana-backup-file-level/image034.png)

App-serverns Virtuella stängdes att ansluta disken kopieras. När du har startat den virtuella datorn, diskar och RAID upptäcktes på rätt sätt (monterade via UUID). Endast monteringspunkten saknades som har skapats via YaST partitioneraren. SAP HANA säkerhetskopian kopior blev därefter visas på OS-nivå.

## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>Kopiera SAP HANA säkerhetskopieringsfiler till NFS-resurs

Om du vill minska den potentiella påverkan på SAP HANA-system från ett prestandaproblem eller disk space perspektiv, en överväga att lagra säkerhetskopiorna för SAP HANA på en NFS-resurs. Tekniskt sett fungerar, men innebär det med hjälp av en andra virtuell Azure-dator som värd för NFS-resursen. Det får inte vara en mindre VM-storlek på grund av Virtuella nätverkets bandbredd. Det skulle vara klokt sedan för att stänga av det här &quot;säkerhetskopiera VM&quot; och bara ta dig köra SAP HANA-säkerhetskopiering. Skriva på en NFS resursen placerar belastningen i nätverket och påverkar SAP HANA-systemet, men bara hantera efteråt de säkerhetskopiera filerna på den &quot;säkerhetskopiera VM&quot; inte skulle påverka systemets SAP HANA alls.

![En NFS-resurs från en annan virtuell dator i Azure har monterats till SAP HANA-servern VM](media/sap-hana-backup-file-level/image035.png)

Om du vill verifiera NFS användningsfall har en NFS-resurs från en annan virtuell dator i Azure monterade till SAP HANA-servern VM. Det fanns ingen särskild NFS justera tillämpas.

![Det tog 1 timme och 46 minuter att utföra säkerhetskopieringen direkt](media/sap-hana-backup-file-level/image036.png)

NFS-resurs har en snabb stripe-uppsättning, t.ex. en på SAP HANA-servern. Det tog dock 1 timme och 46 minuter för att utföra säkerhetskopieringen direkt på NFS-resursens i stället för tio minuter, om värdet är att skriva till en lokal stripe.

![Alternativt inte mycket snabbare på 1 timme och 43 minuter](media/sap-hana-backup-file-level/image037.png)

Med alternativet att göra en säkerhetskopia till en lokal stripe-uppsättning och kopiera till NFS-resurs på OS-nivå (en enkel **cp - avr** kommando) inte mycket snabbare. Det tog 1 timme och 43 minuter.

Så här fungerar det, men prestanda inte bra för säkerhetskopiering 230 GB. Det ser ut ännu större för flera terabyte.

## <a name="copy-sap-hana-backup-files-to-azure-file-service"></a>Kopiera SAP HANA säkerhetskopieringsfiler till tjänsten Azure file

Det är möjligt att montera en Azure-filresursen i en Azure Linux-dator. Artikeln [använda Azure File storage med Linux](../../../storage/files/storage-how-to-use-files-linux.md) innehåller information om hur du gör. Tänk på att det finns för närvarande en 5 TB kvotgräns för en Azure-filresursen och begränsningar för filstorleken på 1 TB per fil. Se [Azure Storage skalbarhets- och prestandamål](../../../storage/common/storage-scalability-targets.md) information om Lagringsgränser.

Testerna har visas dock som SAP HANA säkerhetskopiering inte&#39;t som för närvarande arbeta direkt med den här typen av CIFS monteringspunkt. Anges också i [SAP Obs 1820529](https://launchpad.support.sap.com/#/notes/1820529) som CIFS rekommenderas inte.

![Den här bilden visar ett fel i dialogrutan säkerhetskopiering i SAP HANA Studio](media/sap-hana-backup-file-level/image038.png)

Den här bilden visar ett fel i dialogrutan säkerhetskopiering i SAP HANA Studio när du försöker säkerhetskopiera direkt till en monterad CIFS Azure-filresursen. Ett har att göra en standard SAP HANA-säkerhetskopia i ett VM-filsystem först och sedan kopiera säkerhetskopieringsfilerna därifrån till tjänsten Azure file.

![Den här bilden visar det tog ungefär 929 sekunder för att kopiera 19 SAP HANA-säkerhetskopior](media/sap-hana-backup-file-level/image039.png)

Den här bilden visar att det tog ungefär 929 sekunder för att kopiera 19 SAP HANA säkerhetskopieringsfiler med en total storlek på ungefär 230 GB till Azure-filresursen.

![Källan katalogstrukturen på SAP HANA VM har kopierats till Azure-filresursen](media/sap-hana-backup-file-level/image040.png)

I den här skärmbilden en kan se att källan katalogstrukturen på SAP HANA VM har kopierats till Azure-filresursen: en katalog (hana\_säkerhetskopiering\_fsl\_15 gb) och 19 enskilda säkerhetskopior.

Lagra säkerhetskopior för SAP HANA på Azure-filer kan vara ett intressanta alternativ i framtiden när säkerhetskopiorna för SAP HANA stöder den direkt. Eller när det blir möjligt att montera Azure-filer via NFS och den största kvoten är avsevärt högre än 5 TB.

## <a name="next-steps"></a>Nästa steg
* [Säkerhetskopiering guide för SAP HANA på Azure Virtual Machines](sap-hana-backup-guide.md) ger en översikt och information om att komma igång.
* [SAP HANA säkerhetskopiering baserat på lagring ögonblicksbilder](sap-hana-backup-storage-snapshots.md) beskriver det ögonblicksbild-baserad säkerhetskopiering lagringsalternativet.
* Information om hur du upprättar och planera för katastrofåterställning för SAP HANA i Azure (stora instanser) med hög tillgänglighet finns [SAP HANA (stora instanser) hög tillgänglighet och katastrofåterställning recovery på Azure](hana-overview-high-availability-disaster-recovery.md).
