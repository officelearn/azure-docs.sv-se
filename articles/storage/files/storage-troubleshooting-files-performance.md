---
title: Felsökningsguide för Prestanda i Azure Files
description: Kända prestandaproblem med Azure-filresurser och tillhörande lösningar.
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 09e55abcd97317b87f8a272afa51c6b4ace572e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598093"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Felsöka prestandaproblem för Azure Files

I den här artikeln visas några vanliga problem relaterade till Azure-filresurser. Det ger potentiella orsaker och lösningar när dessa problem uppstår.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Problem med hög latens, låg dataflöde och allmänna prestanda

### <a name="cause-1-share-experiencing-throttling"></a>Orsak 1: Dela upplever strypning

Standardkvoten för en premiumresurs är 100 GiB, vilket ger 100 baslinje IOPS (med en potential att brista upp till 300 för en timme). Mer information om etablering och dess relation till IOPS finns i avsnittet Etablerade resurser i [planeringsguiden.](storage-files-planning.md#understanding-provisioning-for-premium-file-shares)

För att bekräfta om din resurs begränsas kan du använda Azure-mått i portalen.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **Alla tjänster** och sök sedan efter **mått**.

1. Välj **Mått**.

1. Välj ditt lagringskonto som resurs.

1. Välj **Arkiv** som måttnamnområde.

1. Välj **Transaktioner** som mått.

1. Lägg till ett filter för **ResponseType** och kontrollera om några begäranden har en svarskod för **SuccessWithThrottling** (för SMB) eller **ClientThrottlingError** (för REST).

![Alternativ för mått för premiumfilaktier](media/storage-troubleshooting-premium-fileshares/metrics.png)

> [!NOTE]
> Information om hur du får en avisering om en filresurs begränsas läser du [Så här skapar du en avisering om en filresurs begränsas](#how-to-create-an-alert-if-a-file-share-is-throttled).

### <a name="solution"></a>Lösning

- Öka resursens etablerade kapacitet genom att ange en högre kvot på din andel.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>Orsak 2: Tung arbetsbelastning för metadata/namnområde

Om majoriteten av dina begäranden är metadatacentrerade (till exempel createfile/openfile/closefile/queryinfo/querydirectory) blir svarstiden sämre jämfört med läs-/skrivåtgärder.

Om du vill bekräfta om de flesta av dina begäranden är metadatacentrerade kan du använda samma steg som ovan. Utom i stället för att lägga till ett filter för **ResponseType**lägger du till ett filter för **API-namn**.

![Filtrera efter API-namn i dina mått](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Lösning

- Kontrollera om programmet kan ändras för att minska antalet metadataåtgärder.
- Lägg till en virtuell hårddisk på filresursen och montera VHD över SMB från klienten för att utföra filåtgärder mot data. Den här metoden fungerar för scenarier med enstaka skrivare och flera läsare och gör att metadataåtgärder kan vara lokala, vilket ger prestanda som liknar en lokal direktansluten lagring.

### <a name="cause-3-single-threaded-application"></a>Orsak 3: Program med enkeltrådad

Om programmet som används av kunden är enkeltrådat kan detta resultera i betydligt lägre IOPS/dataflöde än det högsta möjliga baserat på din etablerade resursstorlek.

### <a name="solution"></a>Lösning

- Öka applikationsparallialismen genom att öka antalet trådar.
- Byt till applikationer där parallellism är möjlig. För kopieringsåtgärder kan kunder till exempel använda AzCopy eller RoboCopy från Windows-klienter eller det **parallella** kommandot på Linux-klienter.

## <a name="very-high-latency-for-requests"></a>Mycket hög latens för begäranden

### <a name="cause"></a>Orsak

Klientens virtuella dator kan finnas i en annan region än filresursen.

### <a name="solution"></a>Lösning

- Kör programmet från en virtuell dator som finns i samma region som filresursen.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>Klienten kan inte uppnå maximalt dataflöde som stöds av nätverket

En potentiell orsak till detta är en brist på SMB flera kanaler stöd. För närvarande stöder Azure-filresurser endast en kanal, så det finns bara en anslutning från klientens virtuella dator till servern. Den här enskilda anslutningen är knuten till en enda kärna på klientens virtuella dator, så det maximala dataflödet som kan uppnås från en virtuell dator är bundet av en enda kärna.

### <a name="workaround"></a>Lösning

- Att skaffa en virtuell dator med en större kärna kan bidra till att förbättra dataflödet.
- Om du kör klientprogrammet från flera virtuella datorer ökar dataflödet.

- Använd REST API:er där det är möjligt.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>Dataflödet på Linux-klienter är betydligt lägre jämfört med Windows-klienter.

### <a name="cause"></a>Orsak

Detta är ett känt problem med genomförandet av SMB-klient på Linux.

### <a name="workaround"></a>Lösning

- Sprid belastningen över flera virtuella datorer.
- På samma virtuella dator använder du flera **monteringspunkter** utan att dela upp alternativet och sprida belastningen över dessa monteringspunkter.
- På Linux kan du prova montering med **alternativet nostrictsync** för att undvika att tvinga SMB-färg på varje **fsync-anrop.** För Azure-filer stör det här alternativet inte datakonsekvens, men kan resultera i inaktuella filmetadata i kataloglistan (**ls -l-kommando).** Direkt fråga metadata för filen **(stat** kommando) kommer att returnera de mest uppdaterade filen metadata.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Höga svarstider för metadata tunga arbetsbelastningar med omfattande öppna / stänga åtgärder.

### <a name="cause"></a>Orsak

Brist på stöd för kataloglån.

### <a name="workaround"></a>Lösning

- Undvik om möjligt överdriven öppning/stängningshandtag på samma katalog inom en kort tidsperiod.
- För virtuella Linux-datorer ökar du tidsgränsen för kataloginmatningscachen genom att ange **actimeo=\<sek>** som ett monteringsalternativ. Som standard är det en sekund, så ett större värde som tre eller fem kan hjälpa.
- Uppgradera kärnan till 4,20 eller högre för virtuella Linux-datorer.

## <a name="low-iops-on-centosrhel"></a>Låg IOPS på CentOS/RHEL

### <a name="cause"></a>Orsak

IO-djup som är större än ett stöds inte på CentOS/RHEL.

### <a name="workaround"></a>Lösning

- Uppgradera till CentOS 8 / RHEL 8.
- Byt till Ubuntu.

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Långsam filkopiering till och från Azure-filer i Linux

Om du upplever långsam filkopiering till och från Azure-filer kan du ta en titt på [avsnittet Långsam filkopiering till och från Azure-filer i Linux](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux) i Felsökningsguiden för Linux.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>Nervositet/sågtandsmönster för IOPS

### <a name="cause"></a>Orsak

Klientprogrammet överskrider konsekvent baslinje IOPS. För närvarande finns det ingen service sida utjämning av begäran belastning, så om klienten överskrider baslinjen IOPS, kommer det att få begränsas av tjänsten. Att begränsning kan resultera i att klienten upplever en skakis / såg-tand IOPS mönster. I det här fallet kan den genomsnittliga IOPS som uppnåtts av klienten vara lägre än baslinjen IOPS.

### <a name="workaround"></a>Lösning

- Minska belastningen för begäran från klientprogrammet så att resursen inte begränsas.
- Öka kvoten för resursen så att resursen inte begränsas.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Överdrivna DirectoryOpen/DirectoryClose-anrop

### <a name="cause"></a>Orsak

Om antalet DirectoryOpen/DirectoryClose-anrop är ett av de vanligaste API-anropen och du inte förväntar dig att klienten ska ringa så många samtal, kan det vara ett problem med antivirusprogrammet installerat på Azure-klientens virtuella dator.

### <a name="workaround"></a>Lösning

- En korrigering för det här problemet finns i [aprilplattformsuppdateringen för Windows](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform).

## <a name="file-creation-is-slower-than-expected"></a>Skapandet av filer är långsammare än förväntat

### <a name="cause"></a>Orsak

Arbetsbelastningar som är beroende av att skapa ett stort antal filer kommer inte att se någon väsentlig skillnad mellan resultatet för premiumfilresurser och standardfilresurser.

### <a name="workaround"></a>Lösning

- Inga.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Långsam prestanda från Windows 8.1 eller Server 2012 R2

### <a name="cause"></a>Orsak

Högre svarstid än förväntat åtkomst till Azure-filer för IO-intensiva arbetsbelastningar.

### <a name="workaround"></a>Lösning

- Installera den tillgängliga [snabbkorrigeringen](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1).

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Så här skapar du en avisering om en filresurs begränsas

1. I [Azure-portalen](https://portal.azure.com)klickar du på **Övervaka**. 

2. Klicka på Aviseringar och sedan på **+ Ny varningsregel**. **Alerts**

3. Klicka på **Välj** om du vill välja den **lagringskonto/filresurs** som innehåller den filresurs som du vill avisera på och klicka sedan på **Klar**. Om namnet på lagringskontot till exempel är contoso väljer du contoso/file-resursen.

4. Klicka på **Lägg till** om du vill lägga till ett villkor.

5. Du kommer att se en lista över signaler som stöds för lagringskontot, välj måttet **Transaktioner.**

6. På bladet **Konfigurera signallogik** går du till dimensionen **Svarstyp,** klickar på **listrutan Dimensionsvärden** och väljer **SuccessWithThrottling** (för SMB) eller **ClientThrottlingError** (för REST). 

  > [!NOTE]
  > Om dimensionsvärdet SuccessWithThrottling eller ClientThrottlingError inte visas betyder det att resursen inte har begränsats.  Om du vill lägga **+** till dimensionsvärdet klickar du på **listrutan Bredvid listrutan Dimensionsvärden,** skriver **SuccessWithThrottling** eller **ClientThrottlingError**, klickar på **OK** och upprepar sedan steg #6.

7. Gå till dimensionen **Fildelning,** klicka på **listrutan Dimensionsvärden** och markera de filresurser som du vill avisera på. 

  > [!NOTE]
  > Om filresursen är en standardfilresurs är listrutan dimensionsvärden tom eftersom mått per aktie inte är tillgängliga för standardfilresurser. Begränsningsaviseringar för standardfilresurser utlöses om någon filresurs i lagringskontot begränsas och aviseringen inte identifierar vilken filresurs som begränsades. Eftersom mått per aktie inte är tillgängliga för standardfilresurser är rekommendationen att ha en filresurs per lagringskonto. 

8. Definiera **aviseringsparametrar** (tröskelvärde, operator, aggregeringsgranularitet och frekvens) som används för att utvärdera måttaviseringsregeln och klicka på **Klar**.

  > [!TIP]
  > Om du använder ett statiskt tröskelvärde kan måttdiagrammet hjälpa till att fastställa ett rimligt tröskelvärde om filresursen för närvarande begränsas. Om du använder ett dynamiskt tröskelvärde visar måttdiagrammet de beräknade tröskelvärdena baserat på de senaste uppgifterna.

9. Lägg till en **åtgärdsgrupp** (e-post, SMS osv.) i aviseringen antingen genom att välja en befintlig åtgärdsgrupp eller skapa en ny åtgärdsgrupp.

10. Fyll i **aviseringsinformationen** som **varningsregelnamn**, **Beskrivning** och **allvarlighetsgrad**.

11. Klicka på **Skapa aviseringsregel** om du vill skapa aviseringen.

Mer information om hur du konfigurerar aviseringar i Azure Monitor finns [i Översikt över aviseringar i Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).
