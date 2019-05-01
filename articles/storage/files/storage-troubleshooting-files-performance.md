---
title: Azure filer prestanda felsökningsguide
description: Kända problem med prestanda med Azure premium-filresurser (förhandsversion) och associerade lösningar.
services: storage
author: jeffpatt24
ms.service: storage
ms.topic: article
ms.date: 04/25/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 767473a037bf890756df68719698c3872fed6a9c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577922"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Felsöka Azure Files-prestandaproblem

Den här artikeln innehåller några vanliga problem som rör premium för Microsoft Azure-filresurser (förhandsversion). Det ger möjliga orsaker och lösningar när problemen uppstår.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Lång svarstid och lågt dataflöde Allmänt prestandaproblem

### <a name="cause-1-share-experiencing-throttling"></a>Orsak 1: Dela filsystemanvändningen begränsning

Standardkvoten på en resurs är 100 GiB som ger 100 baslinje IOPS (med potentiella kunna tillhandahålla upp till 300 i en timme). Läs mer på etablera och dess förhållande till IOPS, den [etableras resurser](storage-files-planning.md#provisioned-shares) avsnitt av Planeringsguiden.

Du kan utnyttja Azure-mått i portalen för att bekräfta om din resurs har begränsats.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **alla tjänster** och söker sedan efter **mått**.

1. Välj **Mått**.

1. Välj ditt lagringskonto som resursen.

1. Välj **filen** som namnområdet som mått.

1. Välj **transaktioner** som mått.

1. Lägg till ett filter för **ResponseType** och kontrollera om alla begäranden har en svarskod **SuccessWithThrottling**.

![Mått-alternativ för filresurser är premium](media/storage-troubleshooting-premium-fileshares/metrics.png)

### <a name="solution"></a>Lösning

- Öka dela etablerad kapacitet genom att ange en högre kvot på nätverksresursen.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>Orsak 2: Metadata/namnområde arbetsbelastning

Om flesta av dina begäranden är blir metadata centric (t.ex createfile/openfile/closefile/queryinfo/querydirectory) och sedan svarstiden sämre jämfört med om du vill läsa/skriva-åtgärder.

Du kan använda samma steg som ovan för att bekräfta om de flesta av dina begäranden är metadata centrerad. Förutom i stället för att lägga till ett filter för **ResponseType**, lägga till ett filter för **API-namn**.

![Filter för API-namn i dina mått](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Lösning

- Kontrollera om programmet kan ändras för att minska antalet metadataåtgärder.

### <a name="cause-3-single-threaded-application"></a>Orsak 3: Single-threaded program

Om programmet som används av kunden är enkla trådar kan resultera detta i betydligt lägre IOPS/dataflöde än det högsta möjliga baserat på dina etablerade resursstorleken.

### <a name="solution"></a>Lösning

- Öka programmet parallellitet genom att öka antalet trådar.
- Växla till program där det är möjligt att parallellitet. Till exempel för kopieringsåtgärder, kunder kan använda AzCopy eller RoboCopy från Windows-klienter eller **parallella** på Linux-klienter.

## <a name="very-high-latency-for-requests"></a>Mycket lång svarstid för begäranden

### <a name="cause"></a>Orsak

Virtuella klientdatorer kan finnas i en annan region än premium-filresurs.

### <a name="solution"></a>Lösning

- Kör programmet från en virtuell dator som finns i samma region som filresursen premium.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>Klienten kunde inte uppnå största möjliga dataflöde som stöds av nätverket

En möjlig orsak till detta är en brist fo SMB stöd för flera kanaler. För närvarande stöder premium-filer endast en kanal så det finns bara en anslutning från VM-klienten till servern. Den här enda anslutningen är knuten till en enda kärna på klienten virtuell dator, så det maximala dataflöden som kan uppnås från en virtuell dator är bunden av en enda kärna.

### <a name="workaround"></a>Lösning

- Hämta en virtuell dator med en större kärna kan hjälpa att förbättra genomflödet.
- Kör klientprogrammet från flera virtuella datorer kommer att öka dataflödet.
- Använda REST API: er där det är möjligt.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>Dataflödet på Linux-klienter är betydligt lägre jämfört med Windows-klienter.

### <a name="cause"></a>Orsak

Det här är ett känt problem med implementeringen av SMB-klienten på Linux.

### <a name="workaround"></a>Lösning

- Belastningen fördelas på flera virtuella datorer
- På samma virtuella dator, använder flera monteringspunkter med **nosharesock** alternativet och spridning belastningen mellan dessa monteringspunkter.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Hög fördröjning för metadata tunga arbetsbelastning som involverar den omfattande första/sista åtgärder.

### <a name="cause"></a>Orsak

Avsaknaden av stöd för katalogleasing.

### <a name="workaround"></a>Lösning

- Du bör undvika överdriven inledande/avslutande referens till samma katalog inom en kort tidsperiod.
- Öka tidsgränsen för cachen directory posten för virtuella Linux-datorer genom att ange **actimeo =<sec>**  som ett alternativ för montering. Som standard är det en sekund, så att ett större värde som tre eller fem kan hjälpa dig att.
- Uppgradera kernel 4.20 eller högre för virtuella Linux-datorer.

## <a name="low-iops-on-centosrhel"></a>Låg IOPS på CentOS/RHEL

### <a name="cause"></a>Orsak

I/o-djup som är större än ett stöds inte på CentOS/RHEL.

### <a name="workaround"></a>Lösning

- Uppgradera till CentOS 8 / RHEL 8.
- Ändra till Ubuntu.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>Darrig/saw-tooth mönster för IOPS

### <a name="cause"></a>Orsak

Klientprogrammet överskrider upprepade gånger baslinje IOPS. För närvarande finns ingen serversidan Utjämning av belastning för begäranden, så om klienten överskrider baslinje IOPS, begränsas av tjänsten. Den begränsning kan resultera i klienten upplever ett darrig/saw-tooth IOPS-mönster. I det här fallet kan genomsnittlig IOPS som uppnås genom att klienten vara lägre än baslinjen IOPS.

### <a name="workaround"></a>Lösning

- Minska belastning för begäranden från klientprogram, så att resursen inte begränsas.
- Öka kvoten för resursen så att resursen inte begränsas.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Långa DirectoryOpen/DirectoryClose anrop

### <a name="cause"></a>Orsak

Om antalet DirectoryOpen/DirectoryClose anrop är ett av de främsta API-anrop och du inte tror att klienten kan vara vilket gör att många anrop som det kan vara ett problem med antivirusprogram installerat på Virtuella Azure-klientdatorer.

### <a name="workaround"></a>Lösning

- En korrigering av det här problemet finns i den [April plattform uppdatering för Windows](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform).

## <a name="file-creation-is-slower-than-expected"></a>Skapa en fil går långsammare än förväntat

### <a name="cause"></a>Orsak

Arbetsbelastningar som förlitar sig på att skapa ett stort antal filer visas inte en betydande skillnad mellan prestanda med premium-filresurser och standard-filresurser.

### <a name="workaround"></a>Lösning

- Ingen.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Långsamma prestanda från Windows 8.1 eller Server 2012 R2

### <a name="cause"></a>Orsak

Högre än förväntat svarstid åtkomst till Azure Files för i/o-intensiva arbetsbelastningar.

### <a name="workaround"></a>Lösning

- Installera de tillgängliga [snabbkorrigering](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1).