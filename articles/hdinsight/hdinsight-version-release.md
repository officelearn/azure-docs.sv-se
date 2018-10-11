---
title: Översikt över HDInsight 4.0 (förhandsversion) – Azure
description: Jämför HDInsight 3.6 med HDInsight 4.0 när det gäller funktioner, begränsningar och uppgraderingsrekommendationer.
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: overview
ms.date: 09/24/2018
ms.openlocfilehash: 152a145601dcf4282ec0a3a3b6ebcf37bd11848b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46992974"
---
# <a name="hdinsight-40-overview-preview"></a>Översikt över HDInsight 4.0 (förhandsversion)

Azure HDInsight är en av de mest populära tjänsterna bland företagskunder för analys med Hadoop med öppen källkod och Spark på Azure. HDInsight (HDI) 4.0 är en molndistribution av Hadoop-komponenter från [Hortonworks Data Platform (HDP) 3.0](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/relnotes.html). Den här artikeln innehåller information om den senaste versionen av Azure HDInsight och hur du uppgraderar.

## <a name="whats-new-in-hdi-40"></a>Vad är nytt i HDI 4.0?

### <a name="hive-30-and-llap"></a>Hive 3.0 och LLAP

Hive LLAP (Low-Latency Analytical Processing) använder permanenta frågeservrar och minnesintern cachelagring för att leverera snabba SQL-frågeresultat för data i fjärrmolnlagring. Hive-LLAP utnyttjar en uppsättning beständiga daemons som kör delar av Hive-frågor. Frågekörningen på LLAP liknar Hive utan LLAP, med arbetaruppgifter som körs i LLAP-daemons i stället för containrar.

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

Du behöver inte längre oroa dig över att av misstag komma åt Hive-transaktionstabeller direkt från Spark, vilket kan ge motsägande resultat, duplicerade data eller skadade data. I HDI 4.0 förvaras Spark-tabeller och Hive-tabeller i separata metaarkiv. Använd anslutningsappen för Hive-informationslager för att registrera Hive-transaktionstabeller som externa Spark-tabeller.

Läs mer om [Apache Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/spark-overview/content/analyzing_data_with_apache_spark.html).


### <a name="oozie"></a>Oozie

Apache Oozie 4.3.1 ingår i HDI 4.0 med följande ändringar:

* Oozie kör inte längre Hive-åtgärder. Hive CLI har tagits bort och ersatts med BeeLine.

* Du kan exkludera oönskade beroenden från ”share lib” genom att ta med ett exkluderingsmönster i din **job.properties**-fil.

Läs mer om [Apache Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/patch_oozie.html).

## <a name="how-to-upgrade-to-hdi-40"></a>Uppgradera till HDI 4.0

Som vid alla större uppdateringar är det viktigt att du testar dina komponenter innan du implementerar den senaste versionen i en produktionsmiljö. HDI 4.0 är tillgängligt och du kan börja uppgradera, men HDI 3.6 är standardalternativet för att förhindra oavsiktliga missöden.

Det finns ingen uppgraderingsväg som stöds från tidigare versioner av HDI till HDI 4.0. Eftersom metaarkivet och blobdataformatet har ändrats är HDI 4.0 inte kompatibelt med tidigare versioner. Det är viktigt att du håller den nya HDI 4.0-miljön avskild från din befintliga produktionsmiljö. Om du distribuerar HDI 4.0 till din aktuella miljö uppgraderas ditt metaarkiv, och uppgraderingen kan inte ångras.  

## <a name="limitations"></a>Begränsningar

* HDI 4.0 stöder inte MapReduce. Använd Tez i stället. Läs mer om [Apache Tez](https://tez.apache.org/).

* Hive-vyn finns inte längre i HDI 4.0. 

## <a name="next-steps"></a>Nästa steg

* [Dokumentation om Azure HDInsight](index.yml)
* [Viktig information](hdinsight-release-notes.md)
