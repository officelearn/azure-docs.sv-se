---
title: Övervaka klusterprestanda – Azure HDInsight
description: Så här övervakar du hälso- och prestanda för Apache Hadoop-kluster i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/09/2020
ms.openlocfilehash: 75ac5a7fc352f877573d79a004d8da761c6f1cef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082888"
---
# <a name="monitor-cluster-performance-in-azure-hdinsight"></a>Övervaka klusterprestanda i Azure HDInsight

Övervakning av hälsa och prestanda för ett HDInsight-kluster är viktigt för att upprätthålla optimal prestanda och resursutnyttjande. Övervakning kan också hjälpa dig att identifiera och åtgärda klusterkonfigurationsfel och problem med användarkod.

I följande avsnitt beskrivs hur du övervakar och optimerar belastningen på dina kluster, Apache Hadoop YARN-köer och identifierar problem med lagringsbegränsning.

## <a name="monitor-cluster-load"></a>Belastning på kluster för övervakare

Hadoop-kluster kan leverera den mest optimala prestandan när belastningen på klustret är jämnt fördelad över alla noder. Detta gör att bearbetningsaktiviteterna kan köras utan att begränsas av RAM-, CPU- eller diskresurser på enskilda noder.

Om du vill få en hög nivå titta på noderna i klustret och deras inläsning, logga in på [Ambari Web UI](hdinsight-hadoop-manage-ambari.md)och välj sedan fliken **Värdar.** Dina värdar listas med deras fullständigt kvalificerade domännamn. Varje värdens driftstatus visas med en färgad hälsoindikator:

| Color | Beskrivning |
| --- | --- |
| Röd | Minst en huvudkomponent på värden är nere. Hovra för att se ett verktygstips som visar berörda komponenter. |
| Orange | Minst en sekundär komponent på värden är nere. Hovra för att se ett verktygstips som visar berörda komponenter. |
| Gul | Ambari Server har inte fått ett hjärtslag från värden i mer än 3 minuter. |
| Grön | Normalt körtillstånd. |

Du ser också kolumner som visar antalet kärnor och mängden RAM-minne för varje värd och diskanvändning och inläsningsgenomsnitt.

![Apache Ambari värdar flik översikt](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-hosts-tab.png)

Välj något av värdnamnen för en detaljerad titt på komponenter som körs på den värden och deras mått. Måtten visas som en valbar tidslinje för CPU-användning, belastning, diskanvändning, minnesanvändning, nätverksanvändning och antal processer.

![Apache Ambari värdinformation översikt](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-host-details.png)

Se [Hantera HDInsight-kluster med hjälp av webbgränssnittet i Apache Ambari](hdinsight-hadoop-manage-ambari.md) för mer information om hur du ställer in aviseringar och visningsmått.

## <a name="yarn-queue-configuration"></a>GARN-kökonfiguration

Hadoop har olika tjänster som körs över sin distribuerade plattform. YARN (Yet Another Resource Negotiator) samordnar dessa tjänster och allokerar klusterresurser för att säkerställa att all belastning fördelas jämnt över klustret.

YARN delar upp de två ansvarsområdena för JobTracker, resurshantering och finplanering/övervakning, i två daemons: en global Resource Manager och en ApplicationMaster (AM) per program.

Resurshanteraren är en *ren schemaläggare*och avlar endast tillgängliga resurser mellan alla konkurrerande program. Resurshanteraren ser till att alla resurser alltid används, optimera för olika konstanter som SLA, kapacitetsgarantier och så vidare. ApplicationMaster förhandlar om resurser från Resurshanteraren och arbetar med NodeManager(s) för att köra och övervaka behållarna och deras resursförbrukning.

När flera klienter delar ett stort kluster finns det konkurrens om klustrets resurser. CapacityScheduler är en pluggbar schemaläggare som hjälper till med resursdelning genom att köa begäranden. CapacityScheduler stöder också *hierarkiska köer* för att säkerställa att resurser delas mellan underköerna i en organisation, innan andra program köer tillåts använda lediga resurser.

YARN tillåter oss att allokera resurser till dessa köer och visar dig om alla dina tillgängliga resurser har tilldelats. Om du vill visa information om dina köer loggar du in på webbgränssnittet i Ambari och väljer sedan **YARN Queue Manager** på den övre menyn.

![Apache Ambari GARN Köhanterare](./media/hdinsight-key-scenarios-to-monitor/apache-yarn-queue-manager.png)

Sidan YARN Queue Manager visar en lista över dina köer till vänster, tillsammans med den procentandel av kapaciteten som tilldelats var och en.

![Informationssida för YARN Queue Manager](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

För en mer detaljerad titt på dina köer, från Ambari-instrumentpanelen, välj **YARN-tjänsten** från listan till vänster. Välj sedan **Ui för Resurshanteraren** under den aktiva noden under den rullgardinsmenyn **Snabblänkar.**

![Gränssnittsmenylänkar för Resurshanteraren](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu-link.png)

Välj **Scheduler** på menyn Resurshanteraren. Du ser en lista över dina köer under *Programköer*. Här kan du se kapaciteten som används för var och en av dina köer, hur väl jobben fördelas mellan dem och om några jobb är resursbegränsade.

![Menyn Apache HAdoop Resource Manager-gränssnitt](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

## <a name="storage-throttling"></a>Begränsning av lagring

Ett klusters flaskhals kan inträffa på lagringsnivå. Den här typen av flaskhals beror oftast på *att blockera* IO-åtgärder (Input/Output), vilket inträffar när dina pågående uppgifter skickar mer IO än vad lagringstjänsten kan hantera. Den här blockeringen skapar en kö med IO-begäranden som väntar på att bearbetas tills aktuella IOs har bearbetats. Blocken är på grund av *lagringsbegränsning*, vilket inte är en fysisk gräns, utan snarare en gräns som läggs ut av lagringstjänsten av ett servicenivåavtal (SLA). Den här gränsen säkerställer att ingen enskild klient eller klient kan monopolisera tjänsten. SLA begränsar antalet IOs per sekund (IOPS) för Azure Storage - för mer information, se [Skalbarhet och prestandamål för standardlagringskonton](../storage/common/scalability-targets-standard-account.md).

Om du använder Azure Storage finns information om hur du övervakar lagringsrelaterade problem, inklusive begränsning, [övervakar, diagnostiserar och felsöker Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Om klustrets säkerhetskopieringslager är Azure Data Lake Storage (ADLS) beror begränsningen troligen på bandbreddsbegränsningar. Begränsning i det här fallet kan identifieras genom att observera begränsningsfel i aktivitetsloggar. För ADLS finns i begränsningsavsnittet för rätt tjänst i följande artiklar:

* [Vägledning för prestandajustering för Apache Hive på HDInsight och Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [Vägledning för prestandajustering för MapReduce på HDInsight och Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Vägledning för prestandajustering för Apache Storm på HDInsight och Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="troubleshoot-sluggish-node-performance"></a>Felsöka prestanda för trög nod

I vissa fall kan tröghet uppstå på grund av lågt diskutrymme i klustret. Undersök med följande steg:

1. Använd [kommandot ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) för att ansluta till var och en av noderna.

1. Kontrollera diskanvändningen genom att köra något av följande kommandon:

    ```bash
    df -h
    du -h --max-depth=1 / | sort -h
    ```

1. Granska utdata och kontrollera om det finns `mnt` några stora filer i mappen eller andra mappar. Mapparna `usercache`, `appcache` och (mnt/resource/hadoop/yarn/local/usercache/hive/appcache/) innehåller vanligtvis stora filer.

1. Om det finns stora filer orsakar antingen ett aktuellt jobb filtillväxten eller så kan ett misslyckat tidigare jobb ha bidragit till problemet. Om du vill kontrollera om det här beteendet orsakas av ett aktuellt jobb kör du följande kommando:

    ```bash
    sudo du -h --max-depth=1 /mnt/resource/hadoop/yarn/local/usercache/hive/appcache/
    ```

1. Om det här kommandot anger ett visst jobb kan du välja att avsluta jobbet med hjälp av ett kommando som liknar följande:

    ```bash
    yarn application -kill -applicationId <application_id>
    ```

    Ersätt `application_id` med program-ID: et. Om inga specifika jobb anges går du till nästa steg.

1. När kommandot ovan är klart, eller om inga specifika jobb anges, tar du bort de stora filer som du har identifierat genom att köra ett kommando som liknar följande:

    ```bash
    rm -rf filecache usercache
    ```

Mer information om problem med diskutrymme finns i [Slut på diskutrymme](./hadoop/hdinsight-troubleshoot-out-disk-space.md).

> [!NOTE]  
> Om du har stora filer som du vill behålla men bidrar till problemet med det låga diskutrymmet måste du skala upp HDInsight-klustret och starta om tjänsterna. När du har slutfört den här proceduren och vänta några minuter kommer du att märka att lagringen frigörs och nodens vanliga prestanda återställs.

## <a name="next-steps"></a>Nästa steg

Besök följande länkar om du vill ha mer information om felsökning och övervakning av kluster:

* [Analysera HDInsight-loggar](hdinsight-debug-jobs.md)
* [Felsöka appar med Apache Hadoop YARN-loggar](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Aktivera heap dumpar för Apache Hadoop tjänster på Linux-baserade HDInsight](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
