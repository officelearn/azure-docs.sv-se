---
title: Fel söknings guide för Azure Files prestanda
description: Kända prestanda problem med Azure-filresurser och tillhör ande lösningar.
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 6b28d004ceacda3cec13e96ceae84d5d1ff1a2e5
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699177"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Felsöka Azure Files prestanda problem

Den här artikeln innehåller några vanliga problem som rör Azure-filresurser. Den ger potentiella orsaker och lösningar när dessa problem uppstår.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Hög latens, lågt data flöde och allmänna prestanda problem

### <a name="cause-1-share-experiencing-throttling"></a>Orsak 1: Resurs begränsning

Standard kvoten på en Premium-resurs är 100 GiB, vilket ger 100 bas linje IOPS (med möjlighet att överföra upp till 300 per timme). Mer information om etablering och dess relation till IOPS finns i avsnittet [etablerade resurser](storage-files-planning.md#provisioned-shares) i planerings guiden.

Du kan använda Azures mått i portalen för att kontrol lera om din resurs är begränsad.

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **alla tjänster** och Sök sedan efter **mått**.

1. Välj **Mått**.

1. Välj ditt lagrings konto som resurs.

1. Välj **fil** som mått namn område.

1. Välj **transaktioner** som mått.

1. Lägg till ett filter för **ResponseType** och kontrol lera om det finns några begär Anden som har svars koden **SUCCESSWITHTHROTTLING** (för SMB) eller **ClientThrottlingError** (för rest).

![Mått alternativ för Premium-fil resurser](media/storage-troubleshooting-premium-fileshares/metrics.png)

### <a name="solution"></a>Lösning

- Öka resursens etablerade kapacitet genom att ange en högre kvot på din resurs.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>Orsak 2: Hög metadata/namnrymd, tungt arbets belastning

Om majoriteten av dina begär Anden är icke-koncentriska metadata (till exempel CreateFile/OpenFile/closefile/queryinfo/querydirectory) blir svars tiden sämre jämfört med Läs-och skriv åtgärder.

Du kan använda samma steg som ovan för att kontrol lera om de flesta av dina begär Anden är koncentriska metadata. Lägg till ett filter för **API-namn**, förutom i stället för att lägga till ett filter för **ResponseType**.

![Filtrera efter API-namn i dina mått](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Lösning:

- Kontrol lera om programmet kan ändras för att minska antalet metadata-åtgärder.

### <a name="cause-3-single-threaded-application"></a>Orsak 3: Single-threaded-program

Om programmet som används av kunden är en enkel tråd kan detta leda till betydligt lägre IOPS/data flöde än vad som är möjligt utifrån den etablerade resurs storleken.

### <a name="solution"></a>Lösning

- Öka programmets parallellitet genom att öka antalet trådar.
- Växla till program där parallellitet är möjligt. För kopierings åtgärder kan kunder till exempel använda AzCopy eller RoboCopy från Windows-klienter eller **parallellt** kommando på Linux-klienter.

## <a name="very-high-latency-for-requests"></a>Mycket hög svars tid för begär Anden

### <a name="cause"></a>Orsak

Den virtuella klient datorn kan finnas i en annan region än fil resursen.

### <a name="solution"></a>Lösning

- Kör programmet från en virtuell dator som finns i samma region som fil resursen.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>Klienten kunde inte uppnå maximalt data flöde som stöds av nätverket

En möjlig orsak till detta är ett saknat SMB-stöd för flera kanaler. Azure-filresurser stöder för närvarande bara en kanal, så det finns bara en anslutning från den virtuella klient datorn till servern. Den här enskilda anslutningen peggas till en enda kärna på den virtuella klient datorn, så det maximala data flödet som kan nås från en virtuell dator binds till en enda kärna.

### <a name="workaround"></a>Lösning:

- Att hämta en virtuell dator med en större kärna kan hjälpa till att förbättra data flödet.
- Genom att köra klient programmet från flera virtuella datorer ökar du data flödet.
- Använd REST-API: er där det är möjligt.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>Data flödet på Linux-klienter är betydligt lägre jämfört med Windows-klienter.

### <a name="cause"></a>Orsak

Detta är ett känt problem med implementeringen av SMB-klienten på Linux.

### <a name="workaround"></a>Lösning:

- Sprida belastningen över flera virtuella datorer
- Använd flera monterings punkter med alternativet **nosharesock** på samma virtuella dator och sprid belastningen över dessa monterings punkter.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Hög latens för Metadatas tungt arbets belastningar som involverar omfattande öppna/stäng-åtgärder.

### <a name="cause"></a>Orsak

Saknar stöd för katalog lån.

### <a name="workaround"></a>Lösning:

- Undvik att öppna och stänga av samma katalog inom en kort tids period om det är möjligt.
- För virtuella Linux-datorer ökar du timeout-värdet för katalog post genom att ange **actimeo =\<SEK >** som monterings alternativ. Som standard är det en sekund, så ett större värde som tre eller fem kan hjälpa dig.
- För virtuella Linux-datorer uppgraderar du kernel till 4,20 eller högre.

## <a name="low-iops-on-centosrhel"></a>Låga IOPS på CentOS/RHEL

### <a name="cause"></a>Orsak

I/o-djupet är större än ett stöds inte på CentOS/RHEL.

### <a name="workaround"></a>Lösning:

- Uppgradera till CentOS 8/RHEL 8.
- Ändra till Ubuntu.

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Långsam fil kopiering till och från Azure Files i Linux

Om du upplever långsam fil kopiering till och från Azure Files kan du ta en titt på [filen för långsam fil kopiering till och från Azure Files i Linux](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux) i fel söknings guiden för Linux.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>Darr/såg-Tooth-mönster för IOPS

### <a name="cause"></a>Orsak

Klient programmet överskrider konsekventa bas linje IOPS. För närvarande finns det ingen utjämning av belastningen på tjänst sidan, så om klienten överskrider en bas linje för IOPS, kommer den att få en begränsning av tjänsten. Den begränsningen kan resultera i att klienten har ett Darr/såg-Tooth IOPS-mönster. I det här fallet kan den genomsnittliga IOPS som uppnås av klienten vara lägre än bas linjens IOPS.

### <a name="workaround"></a>Lösning:

- Minska belastningen på begäran från klient programmet, så att resursen inte får någon begränsning.
- Öka kvoten för resursen så att resursen inte får någon begränsning.

## <a name="excessive-directoryopendirectoryclose-calls"></a>För många DirectoryOpen/DirectoryClose-anrop

### <a name="cause"></a>Orsak

Om antalet DirectoryOpen/DirectoryClose-anrop är bland de främsta API-anropen och du inte förväntar dig att klienten ska göra många anrop, kan det vara ett problem med antivirus programmet som är installerat på den virtuella Azure-klientdatorn.

### <a name="workaround"></a>Lösning:

- En korrigering för det här problemet finns i [april Platform Update för Windows](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform).

## <a name="file-creation-is-slower-than-expected"></a>Filen har skapats långsammare än förväntat

### <a name="cause"></a>Orsak

Arbets belastningar som förlitar sig på att skapa ett stort antal filer kommer inte att se en stor skillnad mellan prestanda i Premium fil resurser och standard fil resurser.

### <a name="workaround"></a>Lösning:

- Ingen.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Långsamma prestanda från Windows 8,1 eller Server 2012 R2

### <a name="cause"></a>Orsak

Högre än förväntad fördröjning vid åtkomst till Azure Files för i/o-intensiva arbets belastningar.

### <a name="workaround"></a>Lösning:

- Installera den tillgängliga [snabb korrigeringen](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1).
