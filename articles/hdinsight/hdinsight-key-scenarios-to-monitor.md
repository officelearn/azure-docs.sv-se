---
title: Övervaka klusterprestanda - Azure HDInsight
description: Så här övervakar du ett HDInsight-kluster för kapacitet och prestanda.
services: hdinsight
author: maxluk
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: arindamc
ms.openlocfilehash: 22b9a63ab595b4d0b003d999bcd0f131e50aeabf
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53383943"
---
# <a name="monitor-cluster-performance"></a>Övervaka klusterprestanda

Övervaka hälsotillstånd och prestanda för ett HDInsight-kluster är viktigt för att upprätthålla optimala prestanda och användning av resurser. Övervakning kan också hjälpa dig att identifiera och åtgärda konfigurationsfel för klustret och kodfel för användaren.

I följande avsnitt beskrivs hur du övervakar och optimera belastningen på dina kluster, Apache Hadoop YARN-köer och identifiera lagring begränsning problem.

## <a name="monitor-cluster-load"></a>Övervaka kluster belastning

Hadoop-kluster kan leverera bästa möjliga prestanda när belastningen på klustret är jämnt fördelat över alla noder. På så sätt kan de bearbetningsuppgifter som ska köras utan begränsas av RAM-minne, CPU eller diskresurserna för enskilda noder.

Om du vill titta på hög nivå på noderna i klustret och deras läser in, logga in på den [Ambari-Webbgränssnittet](hdinsight-hadoop-manage-ambari.md)och välj sedan den **värdar** fliken. Dina värdar anges med sina fullständiga domännamn. Varje opererar värdstatus visas som en indikator för färgade hälsotillstånd:

| Färg | Beskrivning |
| --- | --- |
| Röd | Minst en master komponent på värden har stoppats. Hovra om du vill se en knappbeskrivning som visar komponenter som påverkas. |
| Orange | Minst en underordnad komponent på värden har stoppats. Hovra om du vill se en knappbeskrivning som visar komponenter som påverkas. |
| Gul | Ambari-servern har inte tagit emot ett pulsslag från värden under mer än 3 minuter. |
| Grön | Normal körs tillstånd. |

Du får också se kolumner som visar antalet kärnor och mängden RAM-minne för varje värd och diskanvändning och Läs in genomsnittlig.

![Fliken proxyvärdar](./media/hdinsight-key-scenarios-to-monitor/hosts-tab.png)

Välj någon av värdnamnen för detaljerad information om komponenter som körs på värden och deras mått. Mått som visas som en valbar tidslinje för CPU-användning, load, diskanvändning, minnesanvändning, nätverksanvändning och antal processer.

![värd-information](./media/hdinsight-key-scenarios-to-monitor/host-details.png)

Se [hantera HDInsight-kluster med hjälp av Apache Ambari-Webbgränssnittet](hdinsight-hadoop-manage-ambari.md) mer information om att ställa in aviseringar och visa mått.

## <a name="yarn-queue-configuration"></a>Konfiguration för YARN-kö

Hadoop har olika tjänster som körs i en distribuerad plattform. YARN (ännu en annan Resource Negotiator) samordnar de här tjänsterna och allokerar klusterresurser för att säkerställa att all belastning är jämnt fördelat över klustret.

YARN delar in två ansvaret för JobTracker, resurshantering och schemaläggning/övervakning,-jobb i två daemons: en global Resource Manager och en programspecifika ApplicationMaster (AM).

Resource Manager är en *ren scheduler*, och endast hanterar tillgängliga resurser mellan alla konkurrerande program. Resource Manager ser till att alla resurser som alltid är i användning, optimering för olika konstanter, till exempel serviceavtal, garantier för kapacitet och så vidare. ApplicationMaster förhandlar resurser från Resource Manager och fungerar med NodeManager(s) att köra och övervaka behållarna och deras resursförbrukning.

När flera klienter delar ett stort kluster, finns konkurrens om klustrets resurser. CapacityScheduler är en modulär scheduler som hjälper dig att resursdelning av jobbköer upp begäranden. Det stöder också CapacityScheduler *hierarkiska köer* att säkerställa att resurser som delas mellan de underordnade köerna för en organisation, innan andra program köer har tillåtelse att använda kostnadsfria resurser.

YARN ger oss möjlighet att allokera resurser till dessa köer och visar om alla tillgängliga resurser tilldelas. Om du vill visa information om din köer, logga in på Ambari-Webbgränssnittet och välj sedan **YARN köhanteraren** på den översta menyn.

![YARN köhanteraren](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager.png)

YARN köhanteraren sidan visar en lista över dina köer till vänster, tillsammans med procent av kapaciteten som tilldelas varje.

![YARN köhanteraren informationssida](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

För mer detaljerad information om din köer från Ambari-instrumentpanelen väljer du den **YARN** tjänsten från listan till vänster. Sedan under den **snabblänkar** listmenyn, väljer **Resource Manager UI** under din aktiva noden.

![Länk för Resource Manager UI-menyn](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

Välj i Resource Manager-UI **Scheduler** på den vänstra menyn. Du ser en lista över dina köer under *köar programmet*. Här kan du se den kapacitet som används för var och en av dina köer, hur väl jobb som är fördelade mellan dem, och om alla jobb är begränsad av resursen.

![Länk för Resource Manager UI-menyn](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui.png)

## <a name="storage-throttling"></a>Begränsning av lagring

Flaskhalsar i ett kluster kan inträffa på lagringsnivå. Den här typen av flaskhals är oftast på grund av att *blockerar* indata/utdata (I/O)-åtgärder som händer när din pågående aktiviteter skickar flera i/o än storage-tjänsten kan hantera. Den här blockeringen skapar en kö av i/o-begäranden som väntar på att bearbetas förrän efter aktuella IOs bearbetas. Block som är på grund av *storage begränsning*, som inte är en fysisk gräns, men i stället en gräns sätts av storage-tjänsten med ett servicenivåavtal (SLA). Den här gränsen säkerställer att ingen enskild kund eller klient kan monopolisera tjänsten. SERVICEAVTALET begränsar antalet IOs per sekund (IOPS) för Azure Storage - mer information, se [skalbarhet för lagring av Azure- och prestandamål](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Om du använder Azure Storage, för information om övervakning av storage-relaterade problem, bland annat begränsningar, finns [övervaka, diagnostisera och Felsök Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Om ditt kluster lagringsenheten är Azure Data Lake Store (ADLS), beror din begränsning sannolikt på bandbreddsgränser. Begränsning kan i det här fallet identifieras av får begränsningsfel i loggarna för uppgiften. ADLS, finns i avsnittet begränsningar på lämplig tjänst i de här artiklarna:

* [Prestandajusteringsvägledning för Apache Hive på HDInsight och Azure Data Lake Store](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [Prestandajusteringsvägledning för MapReduce på HDInsight och Azure Data Lake Store](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Prestandajusteringsvägledning för Apache Storm på HDInsight och Azure Data Lake Store](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="next-steps"></a>Nästa steg

Besök följande länkar för mer information om felsökning och övervakning av ditt kluster:

* [Analysera HDInsight-loggar](hdinsight-debug-jobs.md)
* [Felsöka appar med Apache Hadoop YARN-loggar](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Aktivera heap dumps för Apache Hadoop-tjänster på Linux-baserat HDInsight](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
