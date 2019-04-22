---
title: Översikt över HDInsight 4.0 – Azure
description: Jämför HDInsight 3.6 med HDInsight 4.0 när det gäller funktioner, begränsningar och uppgraderingsrekommendationer.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.topic: overview
ms.date: 04/15/2019
ms.openlocfilehash: 553f50897afaaf9c677e84f9cfffbff7d2c1e607
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59679687"
---
# <a name="hdinsight-40-overview"></a>Översikt över HDInsight 4.0

Azure HDInsight är en av de mest populära tjänsterna bland företagskunder för analys med Apache Hadoop med öppen källkod och Apache Spark på Azure. HDInsight 4.0 är en molndistribution av Apache Hadoop-komponenterna från den [Hortonworks Data Platform (HDP) 3.0](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/relnotes.html). Den här artikeln innehåller information om den senaste versionen av Azure HDInsight och hur du uppgraderar.

## <a name="whats-new-in-hdinsight-40"></a>Vad är nytt i HDInsight 4.0?

### <a name="apache-hive-30-and-llap"></a>Apache Hive 3.0 och LLAP

Apache Hive LLAP (Low-Latency Analytical Processing) använder permanenta frågeservrar och minnesintern cachelagring för att leverera snabba SQL-frågeresultat för data i fjärrmolnlagring. Hive-LLAP utnyttjar en uppsättning beständiga daemons som kör delar av Hive-frågor. Frågekörningen på LLAP liknar Hive utan LLAP, med arbetaruppgifter som körs i LLAP-daemons i stället för containrar.

Fördelar med Hive LLAP:

* Du kan utföra djupa SQL-analyser, till exempel komplexa anslutningar underfrågor, fönsterfunktioner, sorteringar, användardefinierade funktioner och komplexa sammanställningar, utan att offra prestanda och skalbarhet.

* Du kan köra interaktiva frågor mot data i samma lagring som data förbereds i. Det gör att data inte behöver flyttas från lagringen till en annan motor för analysbearbetning.

* Cachelagring av frågeresultat gör att tidigare beräknade frågeresultat kan återanvändas, vilket sparar tid och resurser som har använts för att köra de klusteraktiviteter som krävs för frågan.

### <a name="hive-dynamic-materialized-views"></a>Dynamiska materialiserade vyer i Hive

Nu har Hive stöd för dynamiska materialiserade vyer, eller förbearbetning av relevanta sammanfattningar, som används för att öka hastigheten på frågebearbetningen i informationslager. Materialiserade vyer kan lagras internt i Hive, och kan använda LLAP-acceleration sömlöst.

### <a name="hive-transactional-tables"></a>Transaktionstabeller i Hive

I HDI 4.0 ingår Apache Hive 3, som kräver ACIP-kompatibilitet (atomicitet, konsekvens, isolering och varaktighet) för transaktionstabeller i Hive-informationslagret. ACID-kompatibla tabeller och tabelldata nås och hanteras av Hive. Data i CRUD-tabeller (skapa, hämta, uppdatera och ta bort) måste vara i ORC-filformat, tabeller för endast infogning stöder alla filformat.

* ACID v2 har prestandaförbättringar både för lagringsformat och för körningsmotorn. 

* ACID är aktiverat som standard för att ge fullständigt stöd för datauppdateringar.

* Förbättrade ACID-funktioner innebär att du kan göra uppdateringar och borttagningar på radnivå.

* Inga kostnader för prestanda.

* Ingen bucketgruppering krävs.

* Spark kan läsa och skriva till Hive ACID-tabeller via Hive Warehouse-anslutningsappen.

Läs mer om [Apache Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/hive-overview/content/hive_whats_new_in_this_release_hive.html).

### <a name="apache-spark"></a>Apache Spark

Apache Spark hämtar uppdaterbara tabeller och ACID-transaktioner med Hive Warehouse-anslutningsappen. Med Hive Warehouse-anslutningsappen kan du registrera Hive-transaktionstabeller som externa tabeller i Spark för att få tillgång till fullständiga transaktionsfunktioner. Tidigare versioner hade endast stöd för manipulering av tabellpartitioner. Hive Warehouse-anslutningsappen stöder också Streaming DataFrames för att strömma läsning och skrivning till Hive-transaktionstabeller och strömmande Hive-tabeller från Spark.

Spark-utförare kan ansluta direkt till Hive LLAP-daemons för att hämta och uppdatera data transaktionsmässigt, vilket låter Hive behålla kontrollen över data.

Med Apache Spark i HDInsight 4.0 kan du:

* köra träning av maskininlärningsmodeller över samma transaktionstabell som används för rapportering
* använda ACID-transaktioner för att lägga till kolumner från Spark ML i en Hive-tabell på ett säkert sätt
* köra ett Spark-strömningsjobb på ändringsflödet från en Hive-strömningstabell
* skapa ORC-filer direkt från ett strukturerat Spark-strömningsjobb.

Du behöver inte längre oroa dig över att av misstag komma åt Hive-transaktionstabeller direkt från Spark, vilket kan ge motsägande resultat, duplicerade data eller skadade data. I HDInsight 4.0 hålls tabeller för Spark och Hive-tabeller i separata Metastores. Använd anslutningsappen för Hive-informationslager för att registrera Hive-transaktionstabeller som externa Spark-tabeller.

Läs mer om [Apache Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/spark-overview/content/analyzing_data_with_apache_spark.html).


### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie 4.3.1 ingår i HDI 4.0 med följande ändringar:

* Oozie kör inte längre Hive-åtgärder. Hive CLI har tagits bort och ersatts med BeeLine.

* Du kan exkludera oönskade beroenden från ”share lib” genom att ta med ett exkluderingsmönster i din **job.properties**-fil.

Läs mer om [Apache Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/patch_oozie.html).

## <a name="how-to-upgrade-to-hdinsight-40"></a>Uppgradera till HDInsight 4.0

Som vid alla större uppdateringar är det viktigt att du testar dina komponenter innan du implementerar den senaste versionen i en produktionsmiljö. HDInsight 4.0 är tillgängliga för dig för att påbörja uppgraderingen, men HDInsight 3.6 är alternativet för att förhindra oavsiktlig missöden.

Det finns inga uppgraderingsvägen som stöds från tidigare versioner av HDInsight till HDInsight 4.0. HDInsight 4.0 är inte kompatibel med tidigare versioner eftersom Metaarkiv och blob dataformat har ändrats. Det är viktigt att du behåller den nya miljön HDInsight 4.0 separat från den befintliga produktionsmiljön. Om du distribuerar HDInsight 4.0 till din aktuella miljö din Metaarkiv uppgraderas och kan inte ångras.  

## <a name="limitations"></a>Begränsningar

* HDInsight 4.0 har inte stöd för MapReduce. Använd Apache Tez i stället. Läs mer om [Apache Tez](https://tez.apache.org/).
* HDInsight 4.0 har inte stöd för Apache Storm. 
* Hive-vyn finns inte längre i HDInsight 4.0. 
* Shell-tolken i Apache Zeppelin stöds inte i Spark och i Interaktiva frågekluster.
* Det går inte att *inaktivera* LLAP i ett Spark-LLAP-kluster. Du kan bara inaktivera LLAP.
* Azure Data Lake Storage Gen2 kan inte spara Juypter-anteckningsböcker i ett Spark-kluster.

## <a name="next-steps"></a>Nästa steg

* [Dokumentation om Azure HDInsight](index.yml)
* [Viktig information](hdinsight-release-notes.md)
