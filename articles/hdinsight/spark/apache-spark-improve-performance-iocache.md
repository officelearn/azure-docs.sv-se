---
title: Förbättra prestanda för Apache Spark-arbetsbelastningar med Azure HDInsight-i/o-Cache (förhandsversion)
description: Läs mer om Azure HDInsight-i/o-Cache och hur du använder den för att förbättra prestanda för Apache Spark.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 10/15/2018
ms.openlocfilehash: 5422f4796d2451b652222332799d8ad1a9fc4803
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/12/2019
ms.locfileid: "59525603"
---
# <a name="improve-performance-of-apache-spark-workloads-using-azure-hdinsight-io-cache-preview"></a>Förbättra prestanda för Apache Spark-arbetsbelastningar med Azure HDInsight-i/o-Cache (förhandsversion)

I/o-Cache är en cachelagring tjänst för Azure HDInsight som förbättrar prestandan för Apache Spark-jobb. I/o-Cache fungerar även med [Apache TEZ](https://tez.apache.org/) och [Apache Hive](https://hive.apache.org/) arbetsbelastningar, som kan köras på [Apache Spark](https://spark.apache.org/) kluster. I/o-Cache används en öppen källkod och cachelagring komponent som kallas RubiX. RubiX är en lokal diskcache för användning med big data analytics motorer som kommer åt data från molnlagringssystem. RubiX är unika bland cachelagring system, eftersom den använder Solid-State-hårddiskar (SSD) i stället för att reservera operativ minne för cachelagring. I/o-Cache-tjänsten startar och hanterar RubiX Metadata servrar på varje worker-nod i klustret. Den konfigurerar också alla tjänster i klustret för transparent användning av RubiX cache.

De flesta SSD ger mer än 1 GByte per sekund av bandbredd. Den här bandbredd, kompletterat med operativsystemet InMemory-fil-cache, ger tillräckligt med bandbredd för att läsa in stordata bearbetningsmotorer beräkning, till exempel Apache Spark. Operativ minne lämnas tillgänglig för Apache Spark kan bearbeta mycket minne-beroende aktiviteter, till exempel shuffles. Om du har exklusiv användning av operativsystem minne kan Apache Spark att uppnå optimal Resursanvändning.  

>[!Note]  
>I/o-Cache används för närvarande RubiX som en cachelagring komponent, men detta kan ändras i framtida versioner av tjänsten. Använd Cache-i/o-gränssnitt och inte tar några beroenden direkt på RubiX-implementering.

## <a name="benefits-of-azure-hdinsight-io-cache"></a>Fördelarna med Azure HDInsight-i/o-Cache

Med hjälp av i/o-Cache ger högre prestanda för jobb som läser data från Azure Blob Storage.

Du behöver göra ändringar i Spark-jobb för att se prestanda ökar när du använder i/o-Cache. När-i/o-Cache är inaktiverat den här Spark-koden skulle läsa data via en fjärranslutning från Azure Blob Storage: `spark.read.load('wasbs:///myfolder/data.parquet').count()`. När-i/o-Cache har aktiverats kan orsakar samma kodrad en cachelagrad läsning via i/o-Cache. På följande läsningar läses data lokalt från SSD. Arbetarnoder i HDInsight-kluster är utrustade med lokalt anslutna, dedikerad SSD-enheterna. HDInsight-i/o-Cache använder dessa lokala SSD-enheterna för cachelagring, vilket ger lägsta svarstid och maximerar bandbredd.

## <a name="getting-started"></a>Komma igång

Azure HDInsight-i/o-Cache är inaktiverat som standard i en förhandsversion. I/o-Cache är tillgängligt på Azure HDInsight 3.6 + Spark-kluster som kör Apache Spark 2.3.  Om du vill aktivera i/o-Cache, gör du följande:

1. Välj ditt HDInsight-kluster i [Azure-portalen](https://portal.azure.com).

1. I den **översikt** (som öppnas som standard när du väljer kluster) väljer **Ambari hem** under **Klusterinstrumentpaneler**.

1. Välj den **-i/o-Cache** tjänsten till vänster.

1. Välj **åtgärder** och **aktivera**.

    ![Aktivera tjänsten i/o-Cache i Ambari](./media/apache-spark-improve-performance-iocache/ambariui-enable-iocache.png "att aktivera tjänsten i/o-Cache i Ambari")

1. Bekräfta omstart av alla påverkade tjänster i klustret.

>[!NOTE]  
> Även om förloppsindikatorn visar aktiverad, är inte faktiskt i/o-cachen aktiverad, förrän du startar om andra tjänster påverkas.

## <a name="troubleshooting"></a>Felsökning
  
Du kan få utrymme diskfel kör Spark-jobb när du har aktiverat i/o-Cache. Dessa fel inträffa Spark använder också lokalt diskutrymme för att lagra data under blandning av åtgärder. Spark får slut på utrymme för SSD när i/o-cachen aktiverad och minskar utrymmet för Spark-lagring. Mängden utrymme som används av i/o-Cache standard till hälften av de totalt SSD-utrymmet. Diskutrymmesanvändning för i/o-Cache kan konfigureras i Ambari. Om det uppstår fel utrymme på disken kan minska mängden SSD-utrymme som används för i/o-cachen och starta om tjänsten. Om du vill ändra utrymmet Ställ in för i/o-Cache, gör du följande:

1. I Apache Ambari, väljer du den **HDFS** tjänsten till vänster.

1. Välj den **Peeringkonfigurationer** och **Avancerat** flikar.

    ![Redigera HDFS avancerad konfiguration](./media/apache-spark-improve-performance-iocache/ambariui-hdfs-service-configs-advanced.png "redigera HDFS avancerad konfiguration")

1. Rulla nedåt och expandera den **anpassad core-site** området.

1. Leta upp egenskapen **hadoop.cache.data.fullness.percentage**.

1. Ändra värdet i rutan.

    ![Redigera i/o-Cache Fullness procent](./media/apache-spark-improve-performance-iocache/ambariui-cache-data-fullness-percentage-property.png "redigera IO Cache Fullness-procent")

1. Välj **spara** i det övre högra hörnet.

1. Välj **starta om** > **starta om alla berörda**.

    ![Starta om alla berörda](./media/apache-spark-improve-performance-iocache/ambariui-restart-all-affected.png "starta om alla berörda")

1. Välj **bekräfta omstart alla**.

Om det inte fungerar kan du inaktivera-i/o-Cache.

## <a name="next-steps"></a>Nästa steg

- Läs mer om i/o-Cache, inklusive prestandamått i det här blogginlägget: [Apache Spark-jobb få upp till 9 x snabbare med HDInsight-i/o-Cache](https://azure.microsoft.com/blog/apache-spark-speedup-with-hdinsight-io-cache/)
