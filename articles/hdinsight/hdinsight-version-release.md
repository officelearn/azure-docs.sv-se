---
title: Översikt över HDInsight 4.0 – Azure
description: Jämför HDInsight 3.6 med HDInsight 4.0 när det gäller funktioner, begränsningar och uppgraderingsrekommendationer.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: d0fd9999abc4a67ded0f66977e1a3ba5310c87be
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383037"
---
# <a name="azure-hdinsight-40-overview"></a>Översikt över Azure HDInsight 4.0

Azure HDInsight är en av de mest populära tjänsterna bland företagskunder för Apache Hadoop och Apache Spark. HDInsight 4.0 är en molndistribution av Apache Hadoop-komponenter. Den här artikeln innehåller information om den senaste versionen av Azure HDInsight och hur du uppgraderar.

## <a name="whats-new-in-hdinsight-40"></a>Vad är nytt i HDInsight 4.0?

### <a name="apache-hive-30-and-low-latency-analytical-processing"></a>Apache Hive 3.0 och analytisk bearbetning med låg latens

Apache Hive low-lateency analytical processing (LLAP) använder beständiga frågeservrar och cachelagring i minnet. Den här processen ger snabba SQL-frågeresultat på data i fjärrmolnlagring. Hive LLAP använder en uppsättning beständiga demoner som kör fragment av Hive-frågor. Frågekörningen på LLAP liknar Hive utan LLAP, med arbetaruppgifter som körs i LLAP-daemons i stället för containrar.

Fördelar med Hive LLAP:

* Förmåga att göra djupa SQL-analyser utan att offra prestanda och anpassningsförmåga. Till exempel komplexa kopplingar, undersekvenser, fönsterfunktioner, sortering, användardefinierade funktioner och komplexa aggregeringar.

* Du kan köra interaktiva frågor mot data i samma lagring som data förbereds i. Det gör att data inte behöver flyttas från lagringen till en annan motor för analysbearbetning.

* Cachelagring av frågeresultat gör att tidigare beräknade frågeresultat kan återanvändas. Den här cachen sparar tid och resurser som används för att köra de klusteraktiviteter som krävs för frågan.

### <a name="hive-dynamic-materialized-views"></a>Dynamiska materialiserade vyer i Hive

Hive stöder nu dynamiska materialiserade vyer eller förberäkning av relevanta sammanfattningar. Vyerna påskyndar frågebearbetning i informationslager. Materialiserade vyer kan lagras internt i Hive, och kan använda LLAP-acceleration sömlöst.

### <a name="hive-transactional-tables"></a>Transaktionstabeller i Hive

HDI 4.0 innehåller Apache Hive 3. Hive 3 kräver atomicitet, konsekvens, isolering och hållbarhetsefterlevnad för transaktionstabeller som finns i Hive-lagret. ACID-kompatibla tabeller och tabelldata nås och hanteras av Hive. Data i register för att skapa, hämta, uppdatera och ta bort (CRUD) måste vara i ORC-filformat (Optimized Row Column). Infoga-bara tabeller stöder alla filformat.

* ACID v2 har prestandaförbättringar både för lagringsformat och för körningsmotorn.

* ACID är aktiverat som standard för att ge fullständigt stöd för datauppdateringar.

* Förbättrade ACID-funktioner innebär att du kan göra uppdateringar och borttagningar på radnivå.

* Inga kostnader för prestanda.

* Ingen bucketgruppering krävs.

* Spark kan läsa och skriva till Hive ACID-tabeller via Hive Warehouse-anslutningsappen.

Läs mer om [Apache Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/hive-overview/content/hive_whats_new_in_this_release_hive.html).

### <a name="apache-spark"></a>Apache Spark

Apache Spark hämtar uppdaterbara tabeller och ACID-transaktioner med Hive Warehouse-anslutningsappen. Med Hive Warehouse-anslutningsappen kan du registrera Hive-transaktionstabeller som externa tabeller i Spark för att få tillgång till fullständiga transaktionsfunktioner. Tidigare versioner hade endast stöd för manipulering av tabellpartitioner. Hive Warehouse Connector stöder även strömmande dataramar.  Den här processen strömmar läser och skriver i transaktionella och strömmande Hive-tabeller från Spark.

Spark-utförare kan ansluta direkt till Hive LLAP-daemons för att hämta och uppdatera data transaktionsmässigt, vilket låter Hive behålla kontrollen över data.

Med Apache Spark i HDInsight 4.0 kan du:

* köra träning av maskininlärningsmodeller över samma transaktionstabell som används för rapportering
* använda ACID-transaktioner för att lägga till kolumner från Spark ML i en Hive-tabell på ett säkert sätt
* köra ett Spark-strömningsjobb på ändringsflödet från en Hive-strömningstabell
* skapa ORC-filer direkt från ett strukturerat Spark-strömningsjobb.

Du behöver inte längre oroa dig för att av misstag försöka komma åt Hive transaktionstabeller direkt från Spark. Resulterar i inkonsekventa resultat, dubblettdata eller skadade data. I HDInsight 4.0 förvaras Spark-tabeller och Hive-tabeller i separata metabutiker. Använd anslutningsappen för Hive-informationslager för att registrera Hive-transaktionstabeller som externa Spark-tabeller.

Läs mer om [Apache Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/spark-overview/content/analyzing_data_with_apache_spark.html).

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie 4.3.1 ingår i HDI 4.0 med följande ändringar:

* Oozie kör inte längre Hive-åtgärder. Hive CLI har tagits bort och ersatts med BeeLine.

* Du kan exkludera oönskade beroenden från ”share lib” genom att ta med ett exkluderingsmönster i din **job.properties**-fil.

Läs mer om [Apache Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/patch_oozie.html).

## <a name="how-to-upgrade-to-hdinsight-40"></a>Så här uppgraderar du till HDInsight 4.0

Testa dina komponenter noggrant innan du implementerar den senaste versionen i en produktionsmiljö. HDInsight 4.0 är tillgängligt för dig att påbörja uppgraderingsprocessen. HDInsight 3.6 är standardalternativet för att förhindra oavsiktliga missöden.

Det finns ingen uppgraderingsväg som stöds från tidigare versioner av HDInsight till HDInsight 4.0. Eftersom metabutiks- och blob-dataformat har ändrats är 4.0 inte kompatibelt med tidigare versioner. Det är viktigt att du håller din nya HDInsight 4.0-miljö åtskild från din nuvarande produktionsmiljö. Om du distribuerar HDInsight 4.0 till din nuvarande miljö uppgraderas Metastore permanent.  

## <a name="limitations"></a>Begränsningar

* HDInsight 4.0 stöder inte MapReduce för Apache Hive. Använd Apache Tez i stället. Läs mer om [Apache Tez](https://tez.apache.org/).
* HDInsight 4.0 stöder inte Apache Storm.
* Hive View är inte längre tillgängligt i HDInsight 4.0.
* Shell-tolk i Apache Zeppelin stöds inte i Spark- och Interactive Query-kluster.
* Det går inte att *inaktivera* LLAP i ett Spark-LLAP-kluster. Du kan bara stänga av LLAP.
* Azure Data Lake Storage Gen2 kan inte spara Jupyter-anteckningsböcker i ett Spark-kluster.

## <a name="next-steps"></a>Nästa steg

* [Azure HDInsight-dokumentation](index.yml)
* [Utgåvan](hdinsight-release-notes.md)
