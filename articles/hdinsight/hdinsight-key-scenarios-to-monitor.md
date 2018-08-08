---
title: Övervaka klusterprestanda - Azure HDInsight
description: Så här övervakar du ett HDInsight-kluster för kapacitet och prestanda.
services: hdinsight
author: maxluk
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/27/2017
ms.author: maxluk
ms.openlocfilehash: 5f0c390fb5749ec5a7dbf3ca7eb541c0aa1133e9
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2018
ms.locfileid: "39599579"
---
# <a name="monitor-cluster-performance"></a>Övervaka klusterprestanda

Övervaka hälsotillstånd och prestanda för ett HDInsight-kluster är viktigt för att upprätthålla högsta prestanda och användning av resurser. Övervakning kan du också adress möjliga kodning eller konfigurationsfel i klustret.

I följande avsnitt beskrivs hur du optimerar klustret läser in, YARN kö effektivitet och lagring hjälpmedel.

## <a name="cluster-loading"></a>Inläsning av kluster

Hadoop-kluster ska stämma överens läser in noder i klustret. Den här belastningsutjämning förhindrar bearbetningsaktiviteter begränsas av RAM-minne, CPU eller diskresurser.

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

Se [hantera HDInsight-kluster med hjälp av Ambari-Webbgränssnittet](hdinsight-hadoop-manage-ambari.md) mer information om att ställa in aviseringar och visa mått.

## <a name="yarn-queue-configuration"></a>Konfiguration för YARN-kö

Hadoop har olika tjänster som körs i en distribuerad plattform. YARN (ännu en annan Resource Negotiator) samordnar dessa tjänster, allokerar klusterresurser och hanterar åtkomst till en gemensam uppsättning data.

YARN delar in två ansvaret för JobTracker, resurshantering och schemaläggning/övervakning,-jobb i två daemons: en global ResourceManager och en programspecifika ApplicationMaster (AM).

ResourceManager är en *ren scheduler*, och endast hanterar tillgängliga resurser mellan alla konkurrerande program. ResourceManager säkerställer att alla resurser som alltid är i användning, optimering för olika konstanter, till exempel serviceavtal, garantier för kapacitet och så vidare. ApplicationMaster förhandlar resurser från ResourceManager och fungerar med NodeManager(s) att köra och övervaka behållarna och deras resursförbrukning.

När flera klienter delar ett stort kluster, finns konkurrens om klustrets resurser. CapacityScheduler är en modulär scheduler som hjälper dig att resursdelning av jobbköer upp begäranden. Det stöder också CapacityScheduler *hierarkiska köer* att säkerställa att resurser som delas mellan de underordnade köerna för en organisation, innan andra program köer har tillåtelse att använda kostnadsfria resurser.

YARN ger oss möjlighet att allokera resurser till dessa köer och visar om alla tillgängliga resurser tilldelas. Om du vill visa information om din köer, logga in på Ambari-Webbgränssnittet och välj sedan **YARN köhanteraren** på den översta menyn.

![YARN köhanteraren](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager.png)

YARN köhanteraren sidan visar en lista över dina köer till vänster, tillsammans med procent av kapaciteten som tilldelas varje.

![YARN köhanteraren informationssida](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

För mer detaljerad information om din köer från Ambari-instrumentpanelen väljer du den **YARN** tjänsten från listan till vänster. Sedan under den **snabblänkar** listmenyn, väljer **ResourceManager UI** under din aktiva noden.

![ResourceManager UI menyn länk](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

Välj i ResourceManager UI, **Scheduler** på den vänstra menyn. Du ser en lista över dina köer under *köar programmet*. Här kan du se den kapacitet som används för var och en av dina köer, hur väl jobb som är fördelade mellan dem, och om alla jobb är begränsad av resursen.

![ResourceManager UI menyn länk](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui.png)

## <a name="storage-throttling"></a>Begränsning av lagring

Flaskhalsar i ett kluster kan inträffa på lagringsnivå. Den här typen av flaskhals är oftast på grund av att *blockerar* indata/utdata (I/O)-åtgärder som händer när din pågående aktiviteter skickar flera i/o än storage-tjänsten kan hantera. Den här blockeringen skapar en kö av i/o-begäranden som väntar på att bearbetas förrän efter aktuella IOs bearbetas. Block som är på grund av *storage begränsning*, som inte är en fysisk gräns, men i stället en gräns sätts av storage-tjänsten med ett servicenivåavtal (SLA). Den här gränsen säkerställer att ingen enskild kund eller klient kan monopolisera tjänsten. SERVICEAVTALET begränsar antalet IOs per sekund (IOPS) för Azure Storage - mer information, se [skalbarhet för lagring av Azure- och prestandamål](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Om du använder Azure Storage, för information om övervakning av storage-relaterade problem, bland annat begränsningar, finns [övervaka, diagnostisera och Felsök Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Om ditt kluster lagringsenheten är Azure Data Lake Store (ADLS), beror din begränsning sannolikt på bandbreddsgränser. Begränsning kan i det här fallet identifieras av får begränsningsfel i loggarna för uppgiften. ADLS, finns i avsnittet begränsningar på lämplig tjänst i de här artiklarna:

* [Prestandajusteringsvägledning för Hive på HDInsight och Azure Data Lake Store](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [Prestandajusteringsvägledning för MapReduce på HDInsight och Azure Data Lake Store](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Prestandajusteringsvägledning för Storm på HDInsight och Azure Data Lake Store](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="next-steps"></a>Nästa steg

Besök följande länkar för mer information om felsökning och övervakning av ditt kluster:

* [Analysera HDInsight-loggar](hdinsight-debug-jobs.md)
* [Felsöka appar med YARN-loggar](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Aktivera heap dumps för Hadoop-tjänster på Linux-baserat HDInsight](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
