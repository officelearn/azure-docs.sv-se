---
title: Övervaka kluster prestanda – Azure HDInsight
description: Övervaka hälso tillstånd och prestanda för Apache Hadoop kluster i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: c6e60474f74a23add429bf13ca7744afb8e8e1a3
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74777610"
---
# <a name="monitor-cluster-performance-in-azure-hdinsight"></a>Övervaka kluster prestanda i Azure HDInsight

Övervakning av hälso tillstånd och prestanda för ett HDInsight-kluster är viktigt för att upprätthålla optimal prestanda och resursutnyttjande. Övervakningen kan också hjälpa dig att identifiera och lösa kluster konfigurations fel och användar kod problem.

I följande avsnitt beskrivs hur du övervakar och optimerar belastningen på dina kluster, Apache Hadoop garn köer och identifiera problem med lagrings begränsning.

## <a name="monitor-cluster-load"></a>Övervaka kluster belastning

Hadoop-kluster kan ge optimala prestanda när belastningen på klustret är jämnt fördelad på alla noder. Detta gör att bearbetnings aktiviteter kan köras utan att begränsas av RAM-, processor-eller disk resurser på enskilda noder.

För att få en överblick på noderna i klustret och deras inläsningar loggar du in på [Ambari-WEBBgränssnittet](hdinsight-hadoop-manage-ambari.md)och väljer sedan fliken **värdar** . Dina värdar visas i listan med sina fullständigt kvalificerade domän namn. Varje värds drift status visas med en färgad hälso indikator:

| Färg | Beskrivning |
| --- | --- |
| Röd | Minst en huvud komponent på värden är inte tillgänglig. Hovra för att se en knapp beskrivning som visar påverkade komponenter. |
| Orange | Minst en sekundär komponent på värden är avstängd. Hovra för att se en knapp beskrivning som visar påverkade komponenter. |
| Gul | Ambari-servern har inte tagit emot något pulsslag från värden i mer än 3 minuter. |
| Grön | Normalt kör tillstånd. |
 
Du ser också kolumner som visar antalet kärnor och mängden RAM-minne för varje värd, samt disk användning och belastnings medelvärde.

![Fliken Apache Ambari-värdar – översikt](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-hosts-tab.png)

Välj något av värd namnen för en detaljerad titt på komponenter som körs på värden och deras mått. Måtten visas som en valbar tids linje för processor användning, belastning, disk användning, minnes användning, nätverks användning och antal processer.

![Översikt över Apache Ambari-värd information](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-host-details.png)

Se [Hantera HDInsight-kluster med hjälp av Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md) för information om att ställa in aviseringar och visa mått.

## <a name="yarn-queue-configuration"></a>Konfiguration av garn kön

Hadoop har olika tjänster som körs över sin distribuerade plattform. GARN (ännu en annan resurs Negotiator) samordnar dessa tjänster och allokerar kluster resurser för att säkerställa att all belastning är jämnt fördelad i klustret.

GARN delar upp de två ansvars områdena för JobTracker, resurs hantering och schemaläggning av jobb i två daemoner: en global Resource Manager och en ApplicationMaster per program (AM).

Resource Manager är en *ren Scheduler*och endast arbitrates tillgängliga resurser mellan alla konkurrerande program. Resource Manager säkerställer att alla resurser alltid används, optimerar för olika konstanter, till exempel service avtal, kapacitets garantier och så vidare. ApplicationMaster förhandlar resurser från Resource Manager och fungerar med NodeManager (s) för att köra och övervaka behållarna och deras resursförbrukning.

När flera klienter delar ett stort kluster finns det konkurrens för klustrets resurser. CapacityScheduler är en anslutnings plan som hjälper till resurs delning genom att köa begär Anden. CapacityScheduler stöder också *hierarkiska köer* för att säkerställa att resurserna delas mellan underordnade köer i en organisation, innan köer för andra program kan använda kostnads fria resurser.

GARN gör att vi kan allokera resurser till dessa köer och visar om alla dina tillgängliga resurser har tilldelats. Om du vill visa information om dina köer loggar du in på Ambari-webbgränssnittet och väljer sedan **garn Queue Manager** på den översta menyn.

![Apache Ambari garn Queue Manager](./media/hdinsight-key-scenarios-to-monitor/apache-yarn-queue-manager.png)

Sidan garn Queue Manager visar en lista över dina köer till vänster, tillsammans med den procent andel av kapaciteten som tilldelats var och en.

![Informations sida för garn Queue Manager](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

Om du vill ha en mer detaljerad titt på dina köer går du till Ambari-instrumentpanelen och väljer **garn** tjänsten i listan till vänster. Sedan väljer du **Resource Manager-gränssnitt** under den aktiva noden under List menyn **snabb länkar** .

![Länkar till Resource Manager-webbgränssnitt](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu-link.png)

I Resource Manager-ANVÄNDARGRÄNSSNITTET väljer du **Scheduler** på menyn till vänster. Du ser en lista över dina köer under *program köer*. Här kan du se kapaciteten som används för var och en av dina köer, hur väl jobben distribueras mellan dem och om några jobb är resurs begränsningar.

![Användar gränssnitts menyn Apache HAdoop Resource Manager](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

## <a name="storage-throttling"></a>Lagrings begränsning

Ett klusters prestanda Flask hals kan ske på lagrings nivå. Den här typen av flask hals är oftast på grund av *spärrning* av indata/utdata (i/o)-åtgärder, vilket sker när dina pågående aktiviteter skickar mer i/o än lagrings tjänsten kan hantera. Den här blockeringen skapar en kö med IO-begäranden som väntar på att bearbetas tills aktuell IOs har bearbetats. Blocken är på grund av *lagrings begränsning*, som inte är en fysisk gräns, utan i stället en gräns som har angetts av lagrings tjänsten med ett service avtal (SLA). Den här gränsen säkerställer att ingen enskild klient eller klient organisation kan monopolisera tjänsten. SLA begränsar antalet IOs per sekund (IOPS) för Azure Storage – mer information finns i [Azure Storage skalbarhets-och prestanda mål](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Om du använder Azure Storage, för information om övervakning av lagrings problem, inklusive begränsning, se [övervaka, diagnostisera och felsök Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Om klustrets lagrings plats är Azure Data Lake Storage (ADLS) är din begränsning förmodligen förmodligen på grund av bandbredds gränserna. Du kan identifiera begränsningen i det här fallet genom att iaktta begränsnings fel i aktivitets loggarna. Information om ADLS finns i avsnittet begränsning för lämplig tjänst i de här artiklarna:

* [Vägledning för prestanda justering för Apache Hive på HDInsight och Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [Vägledning för prestanda justering för MapReduce på HDInsight och Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Vägledning för prestanda justering för Apache Storm på HDInsight och Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="next-steps"></a>Nästa steg

Besök följande länkar om du vill ha mer information om hur du felsöker och övervakar dina kluster:

* [Analysera HDInsight-loggar](hdinsight-debug-jobs.md)
* [Felsöka appar med Apache Hadoop garn loggar](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Aktivera heap-dum par för Apache Hadoop tjänster på Linux-baserade HDInsight](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
