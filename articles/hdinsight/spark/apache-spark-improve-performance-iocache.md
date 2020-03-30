---
title: Apache Spark-prestanda – Azure HDInsight IO-cache (förhandsversion)
description: Lär dig mer om Azure HDInsight IO-cache och hur du använder den för att förbättra Apache Spark-prestanda.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/23/2019
ms.openlocfilehash: 43875b87d26f144b85454077fd3c044c820132bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75494985"
---
# <a name="improve-performance-of-apache-spark-workloads-using-azure-hdinsight-io-cache"></a>Förbättra prestanda för Apache Spark-arbetsbelastningar med Azure HDInsight IO-cache

IO Cache är en datacachetjänst för Azure HDInsight som förbättrar prestanda för Apache Spark-jobb. IO Cache fungerar också med [Apache TEZ](https://tez.apache.org/) och [Apache Hive](https://hive.apache.org/) arbetsbelastningar, som kan köras på [Apache Spark](https://spark.apache.org/) kluster. IO Cache använder en cachelagringskomponent med öppen källkod som heter RubiX. RubiX är en lokal diskcache för användning med stordataanalysmotorer som kommer åt data från molnlagringssystem. RubiX är unikt bland cachelagringssystem, eftersom det använder SSD-enheter (Solid State Drives) i stället för att reservera driftminne för cachelagring. IO-cachetjänsten startar och hanterar RubiX-metadataservrar på varje arbetsnod i klustret. Det konfigurerar också alla tjänster i klustret för transparent användning av RubiX-cache.

De flesta SSD-enheter ger mer än 1 GByte per sekund av bandbredd. Den här bandbredden, kompletterad med operativsystemets minnesfilcache, ger tillräckligt med bandbredd för att läsa in motorer för beräkning av stordata, till exempel Apache Spark. Driftminnet är tillgängligt för Apache Spark att bearbeta kraftigt minnesberoende uppgifter, till exempel blandningar. Med exklusiv användning av driftminne tillåter Apache Spark att uppnå optimal resursanvändning.  

> [!Note]  
> IO Cache använder för närvarande RubiX som en cachelagringskomponent, men detta kan ändras i framtida versioner av tjänsten. Använd IO-cachegränssnitt och ta inga beroenden direkt på RubiX-implementeringen.
>I/O-cache stöds bara med Azure BLOB Storage just nu.

## <a name="benefits-of-azure-hdinsight-io-cache"></a>Fördelar med Azure HDInsight IO-cache

Med IO-cache ger en prestandaökning för jobb som läser data från Azure Blob Storage.

Du behöver inte göra några ändringar i Spark-jobben för att prestandan ska öka när du använder IO-cache. När I/O-cache är inaktiverad läser den här Spark-koden data på distans från Azure Blob Storage: `spark.read.load('wasbs:///myfolder/data.parquet').count()`. När IO-cache aktiveras orsakar samma kodrad en cachelagrad läsning via IO-cache. På följande läsningar läses data lokalt från SSD. Arbetsnoder i HDInsight-klustret är utrustade med lokalt anslutna, dedikerade SSD-enheter. HDInsight IO-cache använder dessa lokala SSD:er för cachelagring, vilket ger lägsta latensnivå och maximerar bandbredden.

## <a name="getting-started"></a>Komma igång

Azure HDInsight IO-cache inaktiveras som standard i förhandsversionen. I/O-cache är tillgängligt på Azure HDInsight 3.6+ Spark-kluster, som kör Apache Spark 2.3.  Så här aktiverar du I/O-cache på HDInsight 4.0:

1. Från en webbläsare navigerar du till `https://CLUSTERNAME.azurehdinsight.net`, var `CLUSTERNAME` är namnet på klustret.

1. Välj **IO-cachetjänsten** till vänster.

1. Välj **Åtgärder** (**Tjänståtgärder** i HDI 3.6) och **Aktivera**.

    ![Aktivera IO Cache-tjänsten i Ambari](./media/apache-spark-improve-performance-iocache/ambariui-enable-iocache.png "Aktivera IO Cache-tjänsten i Ambari")

1. Bekräfta omstart av alla berörda tjänster i klustret.

> [!NOTE]  
> Även om förloppsindikatorn visar aktiverad aktiveras inte I/O-cachen förrän du startar om de andra berörda tjänsterna.

## <a name="troubleshooting"></a>Felsökning
  
Du kan få diskutrymmesfel som kör Spark-jobb när du har aktiverat I/C-cache. Dessa fel uppstår eftersom Spark också använder lokal disklagring för att lagra data under blandningsåtgärder. Gnistan kan ta på SSD-utrymme när IO-cache är aktiverat och utrymmet för Spark-lagring minskar. Mängden utrymme som används av IO-cachen är standard hälften av det totala SSD-utrymmet. Diskutrymmesanvändningen för IO-cache kan konfigureras i Ambari. Om du får diskutrymmesfel minskar du mängden SSD-utrymme som används för IO-cache och startar om tjänsten. Så här ändrar du den utrymme som angetts för I/O-cache:

1. I Apache Ambari väljer du **HDFS-tjänsten** till vänster.

1. Välj flikarna **Configs** och **Advanced.**

    ![Redigera hdfs avancerad konfiguration](./media/apache-spark-improve-performance-iocache/ambariui-hdfs-service-configs-advanced.png "Redigera hdfs avancerad konfiguration")

1. Bläddra nedåt och expandera området **Anpassad kärnplats.**

1. Leta reda på egenskapen **hadoop.cache.data.fullness.percentage**.

1. Ändra värdet i rutan.

    ![Redigera i/i-cachefullendhetsprocent](./media/apache-spark-improve-performance-iocache/ambariui-cache-data-fullness-percentage-property.png "Redigera i/i-cachefullendhetsprocent")

1. Välj **Spara** längst upp till höger.

1. Välj **Starta om** > **omstart av alla berörda**.

    ![Apache Ambari startar om alla drabbade](./media/apache-spark-improve-performance-iocache/ambariui-restart-all-affected.png "Starta om alla som påverkas")

1. Välj **Bekräfta starta om alla**.

Om det inte fungerar inaktiverar du I/O-cache.

## <a name="next-steps"></a>Efterföljande moment

Läs mer om IO Cache, inklusive prestandariktmärken i det här blogginlägget: [Apache Spark-jobb får upp till 9x snabbare med HDInsight IO-cache](https://azure.microsoft.com/blog/apache-spark-speedup-with-hdinsight-io-cache/)
