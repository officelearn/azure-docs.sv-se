---
title: Övervaka klusterprestanda - Azure HDInsight | Microsoft Docs
description: Så här övervakar du ett HDInsight-kluster för kapacitet och prestanda.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 09/27/2017
ms.author: maxluk
ms.openlocfilehash: 9bf49631da58de86ffa1881bca976cab86677805
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31403754"
---
# <a name="monitor-cluster-performance"></a>Övervaka klusterprestanda

Övervaka hälsotillstånd och prestanda för ett HDInsight-kluster är viktigt för att underhålla maximala prestanda och resursutnyttjande. Övervakning kan du också adress möjliga kodning eller konfigurationsfel i klustret.

I följande avsnitt beskrivs hur du optimerar klustret lästes in, YARN kön effektivitet och lagring hjälpmedel.

## <a name="cluster-loading"></a>Kluster-inläsning

Hadoop-kluster ska utjämna inläsning mellan noder i klustret. Den här NLB förhindrar bearbetningar begränsas av RAM-minne, CPU och diskresurser.

Om du vill titta på hög nivå på noderna i klustret och deras inläsning, logga in på den [Ambari-Webbgränssnittet](hdinsight-hadoop-manage-ambari.md)och välj den **värdar** fliken. Dina värdar anges med sina fullständiga domännamn. Varje värd operativa status visas med färgade hälsa indikatorer:

| Färg | Beskrivning |
| --- | --- |
| Röd | Minst en master komponent på värden är inte tillgänglig. Hovra om du vill se en knappbeskrivning som visar komponenter som påverkas. |
| Orange | Minst en slav komponent på värden är inte tillgänglig. Hovra om du vill se en knappbeskrivning som visar komponenter som påverkas. |
| Gul | Ambari-servern har inte tagit emot ett pulsslag från värden mer än 3 minuter. |
| Grön | Normal körs tillstånd. |

Du kan också se kolumner visar antalet kärnor och mängden RAM-minne för varje värd och diskanvändning och Läs in genomsnittlig.

![Fliken proxyvärdar](./media/hdinsight-key-scenarios-to-monitor/hosts-tab.png)

Välj någon av värdnamn för en närmare titt på komponenter som körs på värden och deras mått. Mätvärdena visas som en valbar tidslinjen för CPU användning, load, diskanvändning, minnesanvändning, nätverksanvändning och antal processer.

![information om värden](./media/hdinsight-key-scenarios-to-monitor/host-details.png)

Se [hantera HDInsight-kluster med Ambari-Webbgränssnittet](hdinsight-hadoop-manage-ambari.md) mer information om att ställa in aviseringar och visa mått.

## <a name="yarn-queue-configuration"></a>YARN-kökonfigurationen

Hadoop har olika tjänster som körs över distribuerade-plattformen. YARN (ännu en annan resurs förhandlare) samordnar dessa tjänster, allokerar klusterresurser och hanterar åtkomst till en gemensam uppsättning data.

YARN delar de två ansvarsområdena av JobTracker, resurshantering och schemaläggning/övervakning, i två Daemon: en global ResourceManager och en programspecifika ApplicationMaster (AM).

Resurshanteraren är en *ren scheduler*, och endast hanterar tillgängliga resurser mellan alla konkurrerande program. Resurshanteraren säkerställer att alla resurser är alltid i användning, optimera för olika konstanter, till exempel servicenivåavtal, kapacitet garanterar och så vidare. ApplicationMaster förhandlar resurser från resurshanteraren och fungerar med NodeManager(s) att köra och övervaka behållarna och deras resursförbrukning.

När flera innehavare delar en stor klustret, finns det konkurrens om resurser i klustret. CapacityScheduler är en pluggable Schemaläggaren som hjälper dig att resursdelning av kö förfrågningar. Det stöder också CapacityScheduler *hierarkiska köer* så att resurser delas mellan underordnade köer för en organisation, innan köer för andra program kan använda frigöra resurser.

YARN gör att vi kan allokera resurser till dessa köer och visar om alla tillgängliga resurser tilldelas. Om du vill visa information om din köer, logga in på Ambari-Webbgränssnittet och välj sedan **YARN Queue Manager** på den översta menyn.

![YARN Queue Manager](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager.png)

Sidan YARN Queue Manager visar en lista över dina köer till vänster, tillsammans med procent av kapaciteten som tilldelas varje.

![YARN Queue Manager informationssidan](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

En närmare titt på din köer från Ambari-instrumentpanelen väljer den **YARN** tjänsten i listan till vänster. Sedan under den **snabblänkar** väljer **ResourceManager UI** under din aktiva noden.

![ResourceManager UI-menyn länk](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

Välj i ResourceManager UI, **Scheduler** i den vänstra menyn. Du kan visa en lista över dina köer under *programmet köer*. Här kan du se den kapacitet som används för alla dina köer, hur väl jobben distribueras mellan dem, och om alla jobb är begränsad av resursen.

![ResourceManager UI-menyn länk](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui.png)

## <a name="storage-throttling"></a>Begränsning av lagring

Flaskhalsar i ett kluster kan inträffa på lagringsnivån. Den här typen av flaskhals är oftast på grund av att *blockerar* in-/ utdata (I/O)-åtgärder som hända när dina aktiviteter som körs skicka flera i/o än lagringstjänsten kan hantera. Den här blockerar skapar en kö av i/o-begäranden som väntar på att bearbetas förrän efter aktuella IOs bearbetas. Blocken är på grund av att *lagring begränsning*, som inte är en fysisk gräns, men i stället en gräns som sätts av lagringstjänsten genom ett servicenivåavtal (SLA). Den här gränsen garanterar att ingen enskild kund eller klient dominerar tjänsten. SLA begränsar antalet IOs per sekund (IOPS) för Azure Storage - mer information, se [Azure Storage skalbarhets- och prestandamål](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Om du använder Azure Storage, information om hur du övervakar lagringsrelaterade problem, bland annat begränsning finns [övervaka, diagnostisera och felsöka Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Om ditt kluster säkerhetskopiering är Azure Data Lake lagra (ADLS), beror din begränsning sannolikt på bandbreddsgränser. Begränsning kan i det här fallet identifieras av sett bandbreddsbegränsning fel i loggarna för aktiviteten. ADLS, finns i avsnittet bandbreddsbegränsning för tjänsten i de här artiklarna:

* [Prestandajusteringsvägledning för Hive på HDInsight och Azure Data Lake Store](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [Prestandajusteringsvägledning för MapReduce på HDInsight och Azure Data Lake Store](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Prestandajusteringsvägledning för Storm på HDInsight och Azure Data Lake Store](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="next-steps"></a>Nästa steg

Finns följande länkar för mer information om felsökning och övervakning av ditt kluster:

* [Analysera HDInsight-loggar](hdinsight-debug-jobs.md)
* [Felsöka appar med YARN-loggar](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Aktivera heap Dumpar för Hadoop-tjänster på Linux-baserat HDInsight](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
