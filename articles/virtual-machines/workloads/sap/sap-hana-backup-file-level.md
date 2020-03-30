---
title: SAP HANA Azure Backup på filnivå | Microsoft-dokument
description: Det finns två stora säkerhetskopieringsmöjligheter för SAP HANA på virtuella Azure-datorer, den här artikeln täcker SAP HANA Azure Backup på filnivå
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: 93b67936166eb73db5e9a15db42c2c6135794108
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271388"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>SAP HANA Azure Backup på filnivå

## <a name="introduction"></a>Introduktion

Den här artikeln är en relaterad artikel till [backup guide för SAP HANA på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide), som ger en översikt och information om hur du kommer igång och mer information om Azure Backup tjänst och ögonblicksbilder lagring. 

Olika VM-typer i Azure tillåter ett annat antal virtuella hårddiskar som är anslutna. Den exakta informationen dokumenteras i [Storlekar för virtuella Linux-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes). För de tester som avses i den här dokumentationen använde vi en GS5 Azure VM, som tillåter 64 anslutna datadiskar. För större SAP HANA-system kan ett betydande antal diskar redan tas för data- och loggfiler, eventuellt i kombination med programvaruremsning för optimal disk-IO-dataflöde. Mer information om föreslagna diskkonfigurationer för SAP HANA-distributioner på virtuella Azure-datorer finns i artikeln [SAP HANA Azure-konfigurationer för lagring av virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage). Rekommendationerna är inklusive rekommendationer diskutrymme för lokala säkerhetskopior också.

Det vanliga sättet att hantera säkerhetskopiering /återställning på filnivå är med en filbaserad säkerhetskopiering via SAP HANA Studio eller via SAP HANA SQL-satser. Mer information finns i artikeln [SAP HANA SQL och System Views Reference](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf).

![Den här bilden visar dialogrutan för menyalternativet för säkerhetskopiering i SAP HANA Studio](media/sap-hana-backup-file-level/backup-menue-dialog.png)

Den här bilden visar dialogrutan för menyalternativet för säkerhetskopiering i SAP HANA Studio. När man &quot;väljer&quot; typfil måste man ange en sökväg i filsystemet där SAP HANA skriver säkerhetskopiorna. Återställning fungerar på samma sätt.

Även om detta val låter enkelt och rakt fram, det finns vissa överväganden. En Virtuell Azure-dator har en begränsning av antalet datadiskar som kan kopplas. Det kanske inte finns kapacitet att lagra SAP HANA-säkerhetskopior på den virtuella datorns filsystem, beroende på databasens storlek och diskdataflödeskrav, vilket kan innebära att programvara stripar över flera datadiskar. Olika alternativ för att flytta dessa säkerhetskopieringsfiler och hantera filstorleksbegränsningar och prestanda vid hantering av terabyte data finns senare i den här artikeln.

Ett annat alternativ, som ger mer frihet när det gäller total kapacitet, är Azure blob storage. Medan en enda blob också är begränsad till 1 TB, är den totala kapaciteten för en enda blob-behållare för närvarande 500 TB. Dessutom ger det kunderna möjlighet att välja &quot;&quot; så kallad cool blob storage, som har en kostnadsfördel. Mer information om lågbloblagring finns i [Azure Blob-lagring: åtkomstnivåer för frekvent, lågfrekvent och arkiv.](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal)

Om du vill ha ytterligare säkerhet kan du använda ett geod replikerat lagringskonto för att lagra SAP HANA-säkerhetskopior. Mer information om lagringsredundans och lagringsreplikering finns i [Azure Storage-redundansen.](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

Man kan placera dedikerade virtuella hårddiskar för SAP HANA-säkerhetskopior i ett dedikerat lagringskonto för säkerhetskopiering som är georenoverat. Eller så kan man kopiera de virtuella hårddiskar som håller SAP HANA-säkerhetskopior till ett geore replikerat lagringskonto eller till ett lagringskonto som finns i en annan region.

## <a name="azure-blobxfer-utility-details"></a>Information om Azure blobxfer-verktyg

Om du vill lagra kataloger och filer på Azure-lagring kan man använda CLI eller PowerShell eller utveckla ett verktyg med en av [Azure SDK:erna](https://azure.microsoft.com/downloads/). Det finns också ett färdigt verktyg, AzCopy, för att kopiera data till Azure-lagring. (se [Överföra data med kommandoradsverktyget AzCopy](../../../storage/common/storage-use-azcopy.md)).

Därför användes blobxfer för att kopiera SAP HANA-säkerhetskopior. Det är öppen källkod, som används av många kunder i produktionsmiljöer och finns på [GitHub](https://github.com/Azure/blobxfer). Med det här verktyget kan du kopiera data direkt till azure blob storage eller Azure-filresurs. Den erbjuder också en rad användbara funktioner, som MD5 hash, eller automatisk parallellism när du kopierar en katalog med flera filer.

## <a name="sap-hana-backup-performance"></a>Säkerhetskopiering av SAP HANA
I det här kapitlet diskuteras prestandaöverväganden. De siffror som uppnåtts kanske inte representerar det senaste tillståndet eftersom det finns en stadig utveckling för att uppnå bättre dataflöde till Azure-lagring. Därför bör du utföra enskilda tester för din konfiguration och Azure-region.

![Denna skärmdump är av SAP HANA backup-konsolen i SAP HANA Studio](media/sap-hana-backup-file-level/backup-console-hana-studio.png)

Den här skärmbilden visar SAP HANA-reservkonsolen i SAP HANA Studio. Det tog ungefär 42 minuter att utföra en säkerhetskopia på 230 GB på en enda Azure Standard HDD-lagringsdisk som är ansluten till DEN VIRTUELLA HÄRV FÖR HANA med XFS-filsystemet på en disk.

![Denna skärmdump är av YaST på SAP HANA test VM](media/sap-hana-backup-file-level/one-backup-disk.png)

Den här skärmbilden är av YaST på SAP HANA-testdatorn. Du kan se 1 TB enda disk för SAP HANA backup. Det tog ungefär 42 minuter att säkerhetskopiera 230 GB. Dessutom har fem 200 GB-diskar bifogats och programvara RAID md0 skapades, med striping ovanpå dessa fem Azure-datadiskar.

![Upprepa samma säkerhetskopia på programvara RAID med striping över fem anslutna Azure standard lagringsdatadiskar](media/sap-hana-backup-file-level/five-backup-disks.png)

Upprepa samma säkerhetskopia på programvara RAID med striping över fem bifogade Azure standard lagringsdatadiskar förde säkerhetskopieringstiden från 42 minuter ner till 10 minuter. Diskarna var anslutna utan cachelagring till den virtuella datorn. Den här övningen visar vikten av diskskrivningsdataflöde för att uppnå god säkerhetskopieringstid. Du kan växla till Azure Standard SSD-lagring eller Azure Premium Storage för att ytterligare påskynda processen för optimal prestanda. I allmänhet rekommenderas inte Azure-standard-HDD-lagring och användes endast för demonstration. Rekommendationen är att använda ett minimum av Azure Standard SSD-lagring eller Azure Premium Storage för produktionssystem.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>Kopiera SAP HANA-säkerhetskopior till Azure-bloblagring
Prestandanumren, varaktighetsnumren för säkerhetskopiering och kopieringsvaraktighetsnummer som nämns kanske inte representerar det senaste tillståndet för Azure-teknik. Microsoft förbättrar azure-lagringen stadigt för att leverera mer dataflöde och lägre fördröjningar. Därför är numrerar för demonstration ämnar endast. Du måste testa för dina individuella behov i Azure-regionen som du väljer för att kunna bedöma med metod är det bästa för dig.

Ett annat alternativ för att snabbt lagra SAP HANA-säkerhetskopior är Azure blob storage. En enda blob-behållare har en gräns på cirka 500 TB, tillräckligt för SAP HANA-system, med M32ts, M32ls, M64ls och GS5 VM-typer av Azure, för att hålla tillräckligt med SAP HANA-säkerhetskopior. Kunderna kan välja &quot;&quot; mellan &quot;&quot; frekvent och kall blob-lagring (se [Azure Blob storage: hot, cool och archive access tiers](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal)).

Med blobxfer-verktyget är det enkelt att kopiera SAP HANA-säkerhetskopieringsfilerna direkt till Azure blob storage.

![Här kan man se filerna i en fullständig SAP HANA-säkerhetskopia](media/sap-hana-backup-file-level/list-of-backups.png)

Du kan se filerna i en fullständig SAP HANA-säkerhetskopia. Av de fyra filerna har den största ungefär 230 GB storlek.

![Det tog ungefär 3 000 sekunder att kopiera 230 GB till en Azure-standardlagringskontoblobehållare](media/sap-hana-backup-file-level/copy-duration-blobxfer.png)

Det tog inte ungefär 3 000 sekunder att kopiera 230 GB till en Azure-standardlagringskontoblolobbehållare.

Hana Studio backup-konsolen kan en begränsa den maximala filstorleken för HANA backup-filer. I exempelmiljön förbättrades prestanda genom att ha flera mindre säkerhetskopieringsfiler i stället för en stor 230 GB-fil.

Om du ställer in storleksgränsen för säkerhetskopian på HANA-sidan&#39;inte säkerhetskopieringstiden, eftersom filerna skrivs sekventiellt. Filstorleksgränsen var inställd på 60 GB, så säkerhetskopian skapade fyra stora datafiler i stället för den ensidiga filen 230 GB. Att använda flera säkerhetskopieringsfiler kan bli en nödvändighet för säkerhetskopiering av HANA-databaser om dina säkerhetskopieringsmål har begränsningar för filstorlekar för blobstorlekar.

![För att testa parallellism av blobxfer-verktyget sattes sedan den maximala filstorleken för HANA-säkerhetskopior till 15 GB](media/sap-hana-backup-file-level/parallel-copy-multiple-backup-files.png)

För att testa parallellism av blobxfer-verktyget sattes sedan den maximala filstorleken för HANA-säkerhetskopior till 15 GB, vilket resulterade i 19 säkerhetskopieringsfiler. Den här konfigurationen tog tid för blobxfer att kopiera 230 GB till Azure blob-lagring från 3000 sekunder ner till 875 sekunder.

När du utforskar kopiering av säkerhetskopior som utförs mot lokala diskar till andra platser, till exempel Azure blob storage, tänk på att den bandbredd som används av en eventuell parallell kopieringsprocess är redovisning mot nätverks- eller lagringskvoten för din enskilda VM-typ. Därför måste du balansera varaktigheten av kopian mot nätverket och lagringsbandbredden som den normala arbetsbelastningen som körs i den virtuella datorn kräver. 


## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>Kopiera SAP HANA-säkerhetskopior till NFS-resurs

Microsoft Azure erbjuder inbyggda NFS-resurser via [Azure NetApp Files](https://azure.microsoft.com/services/netapp/). Du kan skapa olika volymer av dussintals TBs i kapacitet att lagra och hantera säkerhetskopior. Du kan också ögonblicksbild dessa volymer baserat på NetApp teknik. Azure NetApp Files (ANF) erbjuds i tre olika tjänstnivåer som ger olika lagringsdataflöde. Mer information finns i artikeln [Tjänstnivåer för Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). Du kan skapa och montera en NFS-volym från ANF enligt beskrivningen i artikeln [Snabbstart: Konfigurera Azure NetApp-filer och skapa en NFS-volym](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes?tabs=azure-portal).

Förutom att använda inbyggda NFS-volymer av Azure via ANF finns det olika möjligheter att skapa egna distributioner som tillhandahåller NFS-resurser på Azure. Alla har den nackdel som du behöver för att distribuera och hantera dessa lösningar själv. Några av dessa möjligheter dokumenteras i dessa artiklar:

- [Hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- [GlusterFS på virtuella Azure-datorer på Red Hat Enterprise Linux för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)

NFS-resurser som skapats med hjälp av ovan kan användas för att direkt köra HANA-säkerhetskopior mot eller för att kopiera säkerhetskopior som utfördes mot lokala diskar till dessa NFS-resurser.

> [!NOTE]
> SAP HANA stöder NFS v3 och NFS v4.x. Alla andra format som SMB med CIFS filsystem stöds inte för att skriva HANA säkerhetskopior mot. Se även [SAP-support #1820529](https://launchpad.support.sap.com/#/notes/1820529)

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>Kopiera SAP HANA-säkerhetskopior till Azure-filer

Det är möjligt att montera en Azure-filresurs i en Virtuell Azure Linux-dator. Artikeln [Hur du använder Azure File storage med Linux](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-linux) innehåller information om hur du utför konfigurationen. Om du har begränsningar för Azure Files eller Azure premium-filer läser du artikeln [för Azure Files skalbarhet och prestandamål](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets).

> [!NOTE]
> SMB med CIFS filsystem stöds inte av SAP HANA att skriva HANA säkerhetskopior mot. Se även [SAP-support #1820529](https://launchpad.support.sap.com/#/notes/1820529). Därför kan du bara använda den här lösningen som slutdestination för en HANA-databassäkerhetskopiering som har utförts direkt mot lokala anslutna diskar
> 

I ett test som utfördes mot Azure Files, inte Azure Premium-filer, tog det cirka 929 sekunder att kopiera 19 säkerhetskopieringsfiler med en total volym på 230 GB. Vi förväntar oss att tiden med Azure Premium Files är mycket bättre. Du måste dock komma ihåg att du måste balansera intressena hos en snabb kopia med de krav som din arbetsbelastning har på nätverksbandbredd. Eftersom varje Azure VM-typ tillämpar kvoten för nätverksbandbredd måste du hålla dig inom intervallet för den kvoten med din arbetsbelastning plus en kopia av säkerhetskopieringsfilerna.

![Denna siffra visar att det tog ungefär 929 sekunder att kopiera 19 SAP HANA backup-filer](media/sap-hana-backup-file-level/parallel-copy-to-azure-files.png)


Att lagra SAP HANA-säkerhetskopior på Azure-filer kan vara ett intressant alternativ. Speciellt med förbättrad svarstid och dataflöde för Azure Premium-filer.

## <a name="next-steps"></a>Nästa steg
* [Backup guide för SAP HANA på Azure Virtual Machines](sap-hana-backup-guide.md) ger en översikt och information om hur du kommer igång.
* Mer information om hur du upprättar hög tillgänglighet och planerar för haveriberedskap av SAP HANA på Azure (stora instanser) finns i [SAP HANA (stora instanser) hög tillgänglighet och haveriberedskap på Azure](hana-overview-high-availability-disaster-recovery.md).
