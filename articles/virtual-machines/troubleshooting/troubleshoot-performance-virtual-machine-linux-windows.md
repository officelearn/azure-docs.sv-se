---
title: Felsöka prestanda för virtuella Azure-datorer på Linux eller Windows
description: I den här artikeln beskrivs allmän felsökning av generiska prestandafelsökningar för virtuella datorer genom övervakning och observation av flaskhalsar och möjliga åtgärder för problem som kan uppstå.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: v-miegge
ms.openlocfilehash: 176b0634fe2c7ee2f47162e439c4ea16bde77a8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75772626"
---
# <a name="troubleshoot-azure-virtual-machine-performance-on-linux-or-windows"></a>Felsöka prestanda för virtuella Azure-datorer på Linux eller Windows

I den här artikeln beskrivs allmän felsökning av generiska prestandafelsökningar för virtuella datorer genom övervakning och observation av flaskhalsar och möjliga åtgärder för problem som kan uppstå. Förutom övervakning kan du också använda Perfinsights som kan ge en rapport med bästa praxis rekommendationer och viktiga flaskhalsar runt IO / CPU / Minne. Perfinsights är tillgängligt för både [Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) och [Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux) VM i Azure.

Den här artikeln kommer att gå igenom med hjälp av övervakning för att diagnostisera prestandaflaskhalsar.

## <a name="enabling-monitoring"></a>Aktivera övervakning

### <a name="azure-iaas-virtual-machine-monitoring"></a>Övervakning av virtuella datorer i Azure IAAS

Om du vill övervaka den virtuella gästdatorn använder du Azure VM-övervakningen, som aviserar dig till vissa resursvillkor på hög nivå. Information om du vill kontrollera om du har aktiverat vm-diagnostiken finns i [översikt över Azure Resource logs](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs). Om du ser följande har du troligen inte diagnostiken aktiverad:

![Övervakning är inte aktiverat](media/troubleshoot-performance-virtual-machine-linux-windows/1-virtual-machines-monitoring-not-enabled.png)
 
### <a name="enable-vm-diagnostics-through-microsoft-azure-portal"></a>Aktivera VM-diagnostik via Microsoft Azure Portal

Så här aktiverar du VM-diagnostik:

1. Gå till den virtuella datorn
2. Klicka på **Inställningar för diagnostik**
3. Välj lagringskontot och klicka på **Aktivera övervakning på gästnivå**.

   ![Klicka på Inställningar och sedan Diagnostik](media/troubleshoot-performance-virtual-machine-linux-windows/2-virtual-machines-diagnostics.png)

Du kan kontrollera lagringskontot som används för diagnostikinställning från fliken **Agent** under **Diagnostikinställningar**.

![Kontrollera lagringskonto](media/troubleshoot-performance-virtual-machine-linux-windows/3-check-storage-account.png)

### <a name="enable-storage-account-diagnostics-through-azure-portal"></a>Aktivera diagnostik för lagringskonto via Azure Portal

Lagring är en mycket viktig nivå när vi tänker analysera IO-prestanda för en virtuell dator i Azure. För lagringsrelaterade mått måste vi aktivera diagnostik som ett ytterligare steg. Detta kan också aktiveras, om vi bara vill analysera lagringsrelaterade räknare.

1. Identifiera vilket lagringskonto (eller konton) din virtuella dator använder genom att välja den virtuella datorn. Klicka på **Inställningar**och sedan på **Diskar:**

   ![Klicka på Inställningar och sedan Diskar](media/troubleshoot-performance-virtual-machine-linux-windows/4-storage-disks-disks-selection.png)

2. Gå till lagringskontot (eller kontona) för den virtuella datorn i portalen och arbeta igenom följande steg:

   1. Klicka på översikten för lagringskontot som du hittade med steget ovan.
   2. Standardmått visas. 

    ![Standardmått](media/troubleshoot-performance-virtual-machine-linux-windows/5-default-metrics.png)

3. Klicka på något av måtten, som visar ett annat blad med fler alternativ för att konfigurera och lägga till mått.

   ![Lägga till mått](media/troubleshoot-performance-virtual-machine-linux-windows/6-add-metrics.png)

Så här konfigurerar du dessa alternativ:

1.  Välj **Mått**.
2.  Välj **resurs** (lagringskonto).
3.  Markera **namnområdet**
4.  Välj **Mått**.
5.  Välj typ av **aggregering**
6.  Du kan fästa den här vyn på instrumentpanelen.

## <a name="observing-bottlenecks"></a>Observera flaskhalsar

När vi är igenom den första installationsprocessen för nödvändiga mått och efter att aktivera diagnostik för VM och relaterat lagringskonto kan vi övergå till analysfasen.

### <a name="accessing-the-monitoring"></a>Tillgång till övervakningen

Välj den Virtuella Azure-datorn som du vill undersöka och välj **Övervakning**.

![Välj övervakning](media/troubleshoot-performance-virtual-machine-linux-windows/7-select-monitoring.png)
 
### <a name="timelines-of-observation"></a>Observationstidslinjer

Om du vill identifiera om du har några flaskhalsar i resursen läser du dina data. Om din reda på att datorn har kört bra, men det har rapporterats att prestanda nyligen har försämrats, granska ett tidsintervall med data som omfattar prestandamåttdata innan den rapporterade ändrades, under och efter problemet.

### <a name="check-for-cpu-bottleneck"></a>Sök efter cpuflaskhals

![Sök efter CPU Flaskhals](media/troubleshoot-performance-virtual-machine-linux-windows/8-cpu-bottleneck-time-range.png)

1. Redigera diagrammet.
2. Ställ in tidsintervallet.
3. Du måste sedan lägga till i räknaren: CPU Procent Gäst OS
4. Spara.

### <a name="cpu-observe-trends"></a>CPU observera trender

När du tittar på prestandaproblem, vara medveten om trenderna och förstå om de påverkar dig. I nästa avsnitt använder vi övervakningsdiagrammen från portalen för att visa trender. De kan också vara användbara för korsreferenser för resursbeteenden under samma tidsperiod. Om du vill anpassa diagrammen klickar du på [Azure Monitor-dataplattform](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform).

Spiking - Spiking kan vara relaterade till en schemalagd aktivitet / känd händelse. Om du kan identifiera aktiviteten bestämmer du om aktiviteten körs på den prestandanivå som krävs. Om prestanda är acceptabelt behöver du kanske inte öka resurserna.

Spike up och Constant – Indikerar ofta en ny arbetsbelastning. Om det inte är en erkänd arbetsbelastning aktiverar du övervakning i den virtuella datorn för att ta reda på vilken process (eller processer) som orsakar beteendet. När processen har identifierats bestämmer du om den ökade förbrukningen orsakas av ineffektiv kod eller normal förbrukning. Om normal förbrukning, besluta om processen fungerar på den prestandanivå som krävs.

Konstant – Bestäm om den virtuella datorn alltid har körts på den här nivån eller om den bara har körts på den nivån sedan diagnostiken har aktiverats. Om så är fallet, identifiera processen (eller processer) som orsakar problemet, och överväga att lägga till mer av den resursen.

Stadigt ökande – En konstant ökning av förbrukningen är ofta antingen ineffektiv kod eller en process som tar på sig mer användararbetsbelastning.

### <a name="high-cpu-utilization-remediation"></a>Reparation av hög CPU-utnyttjande

Om ditt program eller din process inte körs på rätt prestandanivå och du ser 95 % + CPU-användningskonstant, kan du utföra någon av följande uppgifter:

* För omedelbar lättnad - Öka storleken på den virtuella datorn till en storlek med fler kärnor
* Förstå problemet – hitta program/process och felsöka därefter.

Om du har ökat den virtuella datorn och processorn fortfarande kör 95 %, avgör du om den här inställningen ger bättre prestanda eller högre programgenomflöde till en acceptabel nivå. Om inte, felsöka det enskilda programmet\processen.

Du kan använda Perfinsights för [Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) eller [Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux) för att analysera vilken process som driver CPU-förbrukningen. 

## <a name="check-for-memory-bottleneck"></a>Kontrollera om det finns flaskhals i minnet

Så här visar du måtten:

1. Lägg till ett avsnitt.
2. Lägg till en panel.
3. Öppna galleriet.
4. Markera minnesanvändningen och dra. När panelen är dockad högerklickar du och väljer **6x4**.

### <a name="memory-observe-trends"></a>Minne observera trender

Minnesanvändningen visar hur mycket minne som förbrukas med den virtuella datorn. Förstå trenden och om den mappar till den tid då du ser problem. Du bör alltid ha mer än 100 MB tillgängligt minne.

Spike och Konstant/Konstant konstant förbrukning - Hög minnesanvändning kanske inte är orsaken till dåliga prestanda, eftersom vissa program som relationsdatabasmotorer allokerar en stor mängd minne, och det här utnyttjandet kanske inte är betydande. Men om det finns flera minneshungriga program kan du se dåliga prestanda från minneskonkurrens som orsakar trimning och växling/byte till disk. Denna dåliga prestanda är ofta en märkbar orsak till programmets prestandapåverkan.

Stadigt ökande förbrukning - En möjlig ansökan "uppvärmning", är denna konsumtion vanligt bland databasmotorer startar. Det kan dock också vara ett tecken på en minnesläcka i ett program. Identifiera programmet och förstå om beteendet förväntas.

Sida eller Swap File Usage – Kontrollera om du använder Windows\) Växlingsfilen (finns `/dev/sdb`på D: eller Linux Swap-filen (finns på) används flitigt. Om du inte har något på dessa volymer förutom dessa filer, kontrollera om det finns höga läs-/skrivningar på dessa diskar. Det här problemet är ett tecken på minnesstjeringstillstånd.

### <a name="high-memory-utilization-remediation"></a>Reparation av hög minnesanvändning

Lös hög minnesanvändning genom att utföra någon av följande uppgifter:

* För omedelbar lättnad eller Page eller Swap File Usage - Öka den virtuella datorns storlek till en med mer minne, sedan övervaka.
* Förstå problemet – hitta program/processer och felsöka för att identifiera högförbrukande minnesprogram.
* Om du känner till programmet kontrollerar du om minnesallokeringen kan begränsas.

Om du efter uppgradering till en större virtuell dator upptäcker att du fortfarande har en konstant stadig ökning fram till 100 %, identifierar programmet/processen och felsöker.

Du kan använda Perfinsights för [Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) eller [Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux) för att analysera vilken process som driver minnesförbrukningen. 

## <a name="check-for-disk-bottleneck"></a>Sök efter flaskhals på disk

Kontrollera lagringsundersystemet för den virtuella datorn genom att kontrollera diagnostiken på Azure VM-nivå med hjälp av räknarna i VM-diagnostik och även diagnostik för lagringskonto.

För inom VM specifik felsökning, kan du använda Perfinsights för [Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) eller [Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux), vilket kan hjälpa till att analysera vilken process som driver IO: s. 

Observera att vi inte har räknare för zon redundanta och Premium Storage-konton. För frågor som rör dessa räknare, ta upp ett stöd fall.

### <a name="viewing-storage-account-diagnostics-in-monitoring"></a>Visa diagnostik för lagringskonto i övervakningen

Om du vill arbeta med nedanstående objekt går du till lagringskontot för den virtuella datorn i portalen:

![Visa diagnostik för lagringskonto i övervakning](media/troubleshoot-performance-virtual-machine-linux-windows/9-virtual-machine-storage-account.png)

1. Redigera övervakningsdiagrammet.
2. Ställ in tidsintervallet.
3. Lägg till räknarna som beskrivs i stegen nedan.
4. Spara ändringarna.

### <a name="disk-observe-trends-standard-storage-only"></a>Disk observera trender (endast standardlagring)

Om du vill identifiera problem med lagring kan du titta på prestandamåtten från lagringskontodiagnostiken och VM-diagnostiken.

För varje kontroll nedan letar du efter viktiga trender när problemen uppstår inom problemets tidsintervall.

#### <a name="check-azure-storage-availability--add-the-storage-account-metric-availability"></a>Kontrollera Azure-lagringstillgänglighet – Lägg till lagringskontomåttet: tillgänglighet

Om du ser en minskning av tillgängligheten kan det finnas ett problem med plattformen, kontrollera [Azure-status](https://azure.microsoft.com/status/). Om inget problem visas där tar du upp en ny supportbegäran.

#### <a name="check-for-azure-storage-timeout---add-the-storage-account-metrics"></a>Sök efter tidsgränsen för Azure-lagring – Lägg till mått för lagringskonto:

* ClientTimeOutError
* ServerTimeOutError
* AverageE2ELatency
* AverageServerLatency
* TotalRequests

Värden i mätvärdena *TimeOutError anger att en I/O-åtgärd tog för lång tid och tog tidsinställd. Genom att arbeta igenom nästa steg kan du identifiera möjliga orsaker.

AverageServerLatency ökar samtidigt på TimeOutErrors kan vara ett plattformsproblem. Ta upp en ny supportbegäran i den här situationen.

AverageE2ELatency representerar klientfördröjning. Kontrollera hur IOPS utförs av programmet. Leta efter en ökning eller ständigt hög TotalRequests-mått. Det här måttet representerar IOPS. Om du börjar nå gränserna för lagringskontot eller en enda virtuell hårddisk kan svarstiden vara relaterad till begränsning.

#### <a name="check-for-azure-storage-throttling---add-the-storage-account-metrics-throttlingerror"></a>Sök efter Azure-lagringsbegränsning – Lägg till mått för lagringskonto: Begränsningsbegränsning

Värden för begränsning anger att du begränsas på lagringskontonivå, vilket innebär att du når IOPS-gränsen för kontot. Du kan avgöra om du når tröskelvärdet för IOPs genom att kontrollera måttet **TotalRequests**.

Observera att varje virtuell hårddisk har en gräns på 500 IOPS eller 60 MBits, men är bunden av den kumulativa gränsen på 20000 IOPS per lagringskonto.

Med det här måttet kan du inte avgöra vilken blob som orsakar begränsningen och vilka som påverkas av den. Du får dock antingen iops- eller ingress-/utgående gränser för lagringskontot.

Om du vill identifiera om du når IOPS-gränsen går du till diagnostiken för lagringskonto och kontrollerar TotalRequests och vill se om du närmar dig 20 000 TotalRequests. Identifiera antingen en ändring i mönstret, om du ser gränsen för första gången eller om den här gränsen inträffar vid en viss tidpunkt.

Med nya diskerbjudanden under Standardlagring kan IOPS- och Dataflödesgränserna skilja sig åt, men den kumulativa gränsen för standardlagringskontot är 20000 IOPS(Premium-lagring har olika gränser på konto- eller disknivå). Läs mer om olika standarderbjudanden för lagringsdiskar och per diskgränser:

* [Skalbarhets- och prestandamål för VM-diskar i Windows](https://docs.microsoft.com/azure/virtual-machines/windows/disk-scalability-targets).

#### <a name="references"></a>Referenser

* [Skalbarhets- och prestandamål för premium-sidblobblagringskonton](../../storage/blobs/scalability-targets-premium-page-blobs.md)

Lagringskontots bandbredd mäts med måtten Storage Account: TotalIngress och TotalEgress. Du har olika tröskelvärden för bandbredd beroende på typ av redundans och regioner.

* [Skalbarhets- och prestandamål för standardlagringskonton](../../storage/common/scalability-targets-standard-account.md)

Kontrollera totalingress och totalegress mot ingående och utgående gränser för redundanstypen och regionen för lagringskonto.

Kontrollera dataflödesgränser för de virtuella hårddiskar som är kopplade till den virtuella datorn. Lägg till vm-måtten Disk läsa och skriva.

Nya diskerbjudanden under Standardlagring har olika IOPS- och dataflödesgränser (IOPS visas inte per virtuell hårddisk). Titta på data för att se om du är den träffa gränserna för kombinerade dataflöde MB för VHD (s) på VM-nivå med diskläsning och skriv, sedan optimera din VM-lagringskonfiguration för att skala förbi enda VHD gränser. Läs mer om olika standarderbjudanden för lagringsdiskar och per diskgränser:

* [Skalbarhets- och prestandamål för VM-diskar i Windows](https://docs.microsoft.com/azure/virtual-machines/windows/disk-scalability-targets).

### <a name="high-disk-utilizationlatency-remediation"></a>Hög diskanvändning/svarstidsåtgärd

Minska klientsvarstiden och optimera VM IO för att skala tidigare VHD-gränser

* [Optimera I/o för Windows i Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-performance-best-practices/)

* [Optimera I/o för Linux i Azure](https://blogs.msdn.microsoft.com/igorpag/2014/10/23/azure-storage-secrets-and-linux-io-optimizations/)

#### <a name="reduce-throttling"></a>Minska begränsning

Om du når de övre gränserna för lagringskonton balanserar du om de virtuella hårddiskarna mellan lagringskonton. Se [Azure Storage Scalability och Performance Targets](https://azure.microsoft.com/documentation/articles/storage-scalability-targets/).

### <a name="increase-throughput-and-reduce-latency"></a>Öka dataflödet och minska svarstiden

Om du har ett svarskänsligt program och kräver högt dataflöde migrerar du dina virtuella hårddiskar till Azure Premium-lagring med den virtuella datorn för DS- och GS-serien.

I de här artiklarna beskrivs de specifika scenarierna:

* [Migrera till Azure Premium Storage](https://azure.microsoft.com/documentation/articles/storage-migration-to-premium-storage/)

* [Använda Azure Premium Storage med SQL Server](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-use-premium-storage/)

## <a name="next-steps"></a>Nästa steg

Om du behöver mer hjälp när som helst i den här artikeln kontaktar du Azure-experterna på [MSDN Azure- och Stack Overflow-forumen](https://azure.microsoft.com/support/forums/).

Du kan också arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj **Hämta support**.
