---
title: "Arkiverade viktig information – Hadoop-komponenter på Azure HDInsight | Microsoft Docs"
description: "Arkiverade viktig information för äldre versioner av Hadoop-komponenter för Azure HDInsight."
services: hdinsight
documentationcenter: 
editor: cgronlun
manager: jhubbard
author: nitinme
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 04278aac85171601b5801b0890d14a9076060444
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="release-notes-archive-for-hadoop-components-on-azure-hdinsight"></a>Viktig information (Arkiv) för Hadoop-komponenter på Azure HDInsight

Den här artikeln innehåller information om den **äldre** uppdateringar med Azure HDInsight. Information om senare versioner finns [HDInsight viktig information](hdinsight-release-notes.md).

> [!IMPORTANT]
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight versionshantering artikel](hdinsight-component-versioning.md).



## <a name="notes-for-08302016-release-of-hdinsight"></a>Information om 2016-08-30-versionen av HDInsight
Fullständig versionsnummer för Linux-baserat HDInsight-kluster som distribueras med den här versionen:

| HDI | HDI-kluster av version | HDP | HDP Build | Ambari-version |
| --- | --- | --- | --- | --- |
| 3.2 |3.2.1000.0.8268980 |2.2 |2.2.9.1-19 |2.2.1.12-4 |
| 3.3 |3.3.1000.0.8268980 |2.3 |2.3.3.1-25 |2.2.1.12-4 |
| 3.4 |3.4.1000.0.8269383 |2.4 |2.4.2.4-5 |2.2.1.12-4 |

Fullständig versionsnummer för Windows-baserade HDInsight-kluster som distribueras med den här versionen:

| HDI | HDI-kluster av version | HDP | HDP Build |
| --- | --- | --- | --- |
| 2.1 |2.1.10.1033.2559206 |1.3 |1.3.12.0-01795 |
| 3.0 |3.0.6.1033.2559206 |2.0 |2.0.13.0-2117 |
| 3.1 |3.1.4.1033.2559206 |2.1 |2.1.16.0-2374 |
| 3.2 |3.2.7.1033.2559206 |2.2 |2.2.9.1-11 |
| 3.3 |3.3.0.1033.2559206 |2.3 |2.3.3.1-25 |


## <a name="08172016---release-of-r-server-on-hdinsight"></a>17/08/2016 - versionen av R Server på HDInsight
* R Server 8.0.5 - huvudsakligen buggfix-versionen. Finns det [R Server viktig information](https://msdn.microsoft.com/microsoft-r/notes/r-server-notes) för mer information.
* AzureML-paketet på kantnod - [R-paket](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) aktiverar R modeller som ska publiceras och används som Azure ML-webbtjänst.  Finns det [”Operationalisera en modell”](hdinsight-hadoop-r-server-overview.md#operationalize-a-model) avsnitt i vår [”översikt av R Server på HDInsight”](hdinsight-hadoop-r-server-overview.md) artikel för mer information.
* Linux-beroenden för den [uppifrån 100 populäraste R-paket](https://github.com/metacran/cranlogs) -beroendena Linux paketet är nu förinstallerat.
* Alternativet för att använda CRAN lagringsplatsen när du lägger till R-paket till datanoder. Mer information finns i [”Kom igång med R Server på HDInsight”](hdinsight-hadoop-r-server-get-started.md).
* Ökad tillförlitlighet för R Server etablering när kluster skapas.

## <a name="notes-for-08012016-release-of-hdinsight"></a>Information om 2016-08-01-versionen av HDInsight
Fullständig versionsnummer för Linux-baserat HDInsight-kluster som distribueras med den här versionen:

| HDI | HDI-kluster av version | HDP | HDP Build | Ambari-version |
| --- | --- | --- | --- | --- |
| 3.2 |3.2.1000.0.8028416 |2.2 |2.2.9.1-19 |2.2.1.12-4 |
| 3.3 |3.3.1000.0.8028416 |2.3 |2.3.3.1-25 |2.2.1.12-4 |
| 3.4 |3.4.1000.0.8053402 |2.4 |2.4.2.4-5 |2.2.1.12-4 |

Fullständig versionsnummer för Windows-baserade HDInsight-kluster som distribueras med den här versionen:

| HDI | HDI-kluster av version | HDP | HDP Build |
| --- | --- | --- | --- |
| 2.1 |2.1.10.1005.2488842 |1.3 |1.3.12.0-01795 |
| 3.0 |3.0.6.1005.2488842 |2.0 |2.0.13.0-2117 |
| 3.1 |3.1.4.1005.2488842 |2.1 |2.1.16.0-2374 |
| 3.2 |3.2.7.1005.2488842 |2.2 |2.2.9.1-11 |
| 3.3 |3.3.0.1005.2488842 |2.3 |2.3.3.1-25 |

Den här versionen innehåller följande uppdateringar:

| Rubrik | Beskrivning | Påverkas område (till exempel Service, komponenten eller SDK) | Typ av kluster (till exempel Spark, Hadoop, HBase eller Storm) | JIRA (om tillämpligt) |
| --- | --- | --- | --- | --- |
| Ändringar av 3.4 HDInsight-kluster |Standardvärden för följande hive-konfigurationer har ändrats för bättre prestanda <ul><li>`hive.vectorized.execution.reduce.enabled=true`</li><li>`hive.tez.min.partition.factor=1f`</li><li>`hive.tez.max.partition.factor=3f`</li><li>`tez.shuffle-vertex-manager.min-src-fraction=0.9`</li><li>`tez.shuffle-vertex-manager.max-src-fraction=0.95`</li><li>`tez.runtime.shuffle.connect.timeout= 30000`</li></ul> |Tjänst |Alla |Saknas |
| Följande korrigeringsfiler ingår i den här versionen |HIVE-13632, HIVE-12897,HIVE-12907,HIVE-12908,HIVE-12988,HIVE-13510,HIVE-13572,HIVE-13716,HIVE-13726,HIVE-12505,HIVE-13632,HIVE-13661,HIVE-13705,HIVE-13743,HIVE-13810,HIVE-13857,HIVE-13902,HIVE-13911,HIVE-13933 |Tjänst |Alla |Saknas |

## <a name="notes-for-07142016-release-of-hdinsight"></a>Information om 2016-07-14-versionen av HDInsight
Fullständig versionsnummer för Linux-baserat HDInsight-kluster som distribueras med den här versionen:

| HDI | HDI-kluster av version | HDP | HDP Build | Ambari-version |
| --- | --- | --- | --- | --- |
| 3.2 |3.2.1000.0.7932505 |2.2 |2.2.9.1-11 |2.2.1.12-2 |
| 3.3 |3.3.1000.0.7932505 |2.3 |2.3.3.1-18 |2.2.1.12-2 |
| 3.4 |3.4.1000.0.7933003 |2.4 |2.4.2.0 |2.2.1.12-2 |

Fullständig versionsnummer för Windows-baserade HDInsight-kluster som distribueras med den här versionen:

| HDI | HDI-kluster av version | HDP | HDP Build |
| --- | --- | --- | --- |
| 2.1 |2.1.10.989.2441725 |1.3 |1.3.12.0-01795 |
| 3.0 |3.0.6.989.2441725 |2.0 |2.0.13.0-2117 |
| 3.1 |3.1.4.989.2441725 |2.1 |2.1.16.0-2374 |
| 3.2 |3.2.7.989.2441725 |2.2 |2.2.9.1-11 |
| 3.3 |3.3.0.989.2441725 |2.3 |2.3.3.1-21 |

## <a name="notes-for-07072016-release-of-hdinsight"></a>Information om 07/07/2016-versionen av HDInsight
Fullständig versionsnummer för Linux-baserat HDInsight-kluster som distribueras med den här versionen:

| HDI | HDI-kluster av version | HDP | HDP Build |
| --- | --- | --- | --- |
| 3.2 |3.2.1000.0.7864996 |2.2 |2.2.9.1-11 |
| 3.3 |3.3.1000.0.7864996 |2.3 |2.3.3.1-18 |
| 3.4 |3.4.1000.0.7861906 |2.4 |2.4.2.0 |

Fullständig versionsnummer för Windows-baserade HDInsight-kluster som distribueras med den här versionen:

| HDI | HDI-kluster av version | HDP | HDP Build |
| --- | --- | --- | --- |
| 2.1 |2.1.10.977.2413853 |1.3 |1.3.12.0-01795 |
| 3.0 |3.0.6.977.2413853 |2.0 |2.0.13.0-2117 |
| 3.1 |3.1.4.977.2413853 |2.1 |2.1.16.0-2374 |
| 3.2 |3.2.7.977.2413853 |2.2 |2.2.9.1-11 |
| 3.3 |3.3.0.977.2413853 |2.3 |2.3.3.1-21 |

Den här versionen innehåller följande uppdateringar:

| Rubrik | Beskrivning | Påverkas område (till exempel Service, komponenten eller SDK) | Typ av kluster (till exempel Spark, Hadoop, HBase eller Storm) | JIRA (om tillämpligt) |
| --- | --- | --- | --- | --- |
| [HDInsight-verktyg för IntelliJ](hdinsight-apache-spark-intellij-tool-plugin.md) |IntelliJ IDEA plugin-program för HDInsight Spark-kluster är nu integrerat med Azure Toolkit för IntelliJ. Den stöder Azure SDK v2.9.1, senaste Java SDK: er, och innehåller alla funktioner från fristående HDInsight-Plugin för IntelliJ. |Verktyg |Spark |Saknas |
| [HDInsight-verktyg för Eclipse](hdinsight-apache-spark-eclipse-tool-plugin.md) |Azure Toolkit för Eclipse stöder nu HDInsight Spark-kluster. Det kan följande funktioner: <ul><li>Skapa och skriva ett Spark-program enkelt i Scala och Java med förstaklass redigering stöd för IntelliSense automatisk formatering, kontrollera fel, osv.</li><li>Testa Spark programmet lokalt.</li><li>Skicka jobb till HDInsight Spark-klustret och hämta resultaten.</li><li>Logga in på Azure och få åtkomst till alla Spark-kluster som är kopplade till dina Azure-prenumerationer.</li><li>Navigera alla associerade lagringsresurser i HDInsight Spark-kluster.</li></ul> |Verktyg |Spark |Saknas |

Från och med den här versionen, har vi ändrat gästen OS uppdatering principen för Linux-baserade HDInsight-kluster. Syftet med den nya principen är att avsevärt minska antalet omstarter på grund av korrigering. De nya principen korrigeringsfiler virtuella datorerna (VM) på Linux-kluster varje måndag eller torsdag som börjar vid 12: 00 UTC successiva överskrids över noderna i alla kluster. Alla angivna VM startar dock bara om högst en gång var 30 dagar på grund av gäst OS-korrigering. Dessutom kan sker den första omstarten för ett nyskapat kluster inte tidigare än 30 dagar från datumet då klustret skapades.

> [!NOTE]
> Ändringarna gäller endast för nyskapade kluster lika med eller större än den här versionen.
>
>

## <a name="notes-for-06062016-release-of-hdinsight"></a>Information om 2016-06-06-versionen av HDInsight
Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen:

| HDP | HDI-Version | Spark-Version | Ambari Build-nummer | HDP Build-nummer |
| --- | --- | --- | --- | --- |
| 2.3 |3.3.1000.0.7702215 |1.5.2 |2.2.1.8-2 |2.3.3.1-18 |
| 2.4 |3.4.1000.0.7702224 |1.6.1 |2.2.1.8-2 |2.4.2.0 |

Den här versionen innehåller följande uppdateringar:

| Rubrik | Beskrivning | Påverkas område (till exempel Service, komponenten eller SDK) | Typ av kluster (till exempel Spark, Hadoop, HBase eller Storm) | JIRA (om tillämpligt) |
| --- | --- | --- | --- | --- |
| Spark i HDInsight är allmänt tillgängligt |Den här versionen ger förbättringar i tillgänglighet, skalbarhet och produktivitet öppna datakällan Apache Spark i HDInsight. <ul><li>Branschledande tillgänglighets-SLA för 99,9%, vilket gör den lämplig för krävande arbetsbelastningar på företag.</li><li>Skalbar lagring lagret med hjälp av Azure Data Lake Store.</li><li>Produktivitetsverktyg för varje fas av datagranskning och utveckling. Jupyter-anteckningsböcker med anpassade Spark kernel aktiverar interaktiv datagranskning, integrering med BI-instrumentpaneler som Power BI och Tableau Qlik är bra för snabb Datadelning och kontinuerlig rapportering, plugin-programmet för IntelliJ är tillförlitlig alternativ för lång sikt kod artefakt utveckling och felsökning.</li></ul> |Tjänst |Spark |Saknas |
| HDInsight-verktyg för IntelliJ |Det här är ett plugin-programmet för IntelliJ IDEA för HDInsight Spark-kluster. Det kan följande funktioner:<ul><li>Skapa och skriva ett Spark-program enkelt i Scala och Java med förstaklass redigering stöd för IntelliSense automatisk formatering, kontrollera fel, osv.</li><li>Testa Spark programmet lokalt.</li><li>Skicka jobb till HDInsight Spark-klustret och hämta resultaten.</li><li>Logga in på Azure och få åtkomst till alla Spark-kluster som är kopplade till dina Azure-prenumerationer.</li><li>Navigera alla associerade lagringsresurser i HDInsight Spark-kluster.</li><li>Navigera all historik och Jobbinformation för HDInsight Spark-kluster.</li><li>Felsöka Spark jobb via fjärranslutning från din dator.</li></ul> |Verktyg |Spark |Saknas |

## <a name="notes-for-05132016-release-of-hdinsight"></a>Information om 2016-05-13-versionen av HDInsight
Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen:

* HDInsight (Windows) 2.1.10.875.2159884 (HDP 1.3.12.0-01795 - oförändrade)
* HDInsight (Windows) 3.0.6.875.2159884 (HDP 2.0.13.0-2117 - oförändrade)
* HDInsight (Windows) 3.1.4.922.2266903 (HDP 2.1.15.0-2374 - oförändrade)
* HDInsight (Windows) 3.2.7.922.2266903 (HDP 2.2.9.1-11)
* HDInsight (Windows) 3.3.0.922.2266903 (HDP 2.3.3.1-18)
* HDInsight (Linux) 3.2.1000.0.7565644 (HDP 2.2.9.1-11)
* HDInsight (Linux) 3.3.1000.0.7565644 (HDP 2.3.3.1-18)
* HDInsight (Linux) 3.4.1000.0.7548380 (HDP 2.4.2.0)

Den här versionen innehåller följande uppdateringar:

| Rubrik | Beskrivning | Påverkas område (till exempel Service, komponenten eller SDK) | Typ av kluster (till exempel Spark, Hadoop, HBase eller Storm) | JIRA (om tillämpligt) |
| --- | --- | --- | --- | --- |
| Spark versionsuppdatering och andra felkorrigeringar |Den här versionen programuppdateringarna versionen Spark i HDInsight-kluster till 1.6.1 och åtgärdar buggar som andra |Tjänst |Spark |Saknas |

## <a name="notes-for-04112016-release-of-hdinsight"></a>Information om 2016-04-11-versionen av HDInsight
Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen:

* HDInsight (Windows) 2.1.10.889.2191206 (HDP 1.3.12.0-01795 - oförändrade)
* HDInsight (Windows) 3.0.6.889.2191206 (HDP 2.0.13.0-2117 - oförändrade)
* HDInsight (Windows) 3.1.4.889.2191206 (HDP 2.1.15.0-2374 - oförändrade)
* HDInsight (Windows) 3.2.7.889.2191206 (HDP 2.2.9.1-10)
* HDInsight (Windows) 3.3.0.889.2191206 (HDP 2.3.3.1-16-oförändrat)
* HDInsight (Linux) 3.2.1000.0.7339916 (HDP 2.2.9.1-10)
* HDInsight (Linux) 3.3.1000.0.7339916 (HDP 2.3.3.1-16)
* HDInsight (Linux) 3.4.1000.0.7338911 (HDP 2.4.1.1-3)
* SDK 1.5.8

Den här versionen innehåller följande uppdateringar:

| Rubrik | Beskrivning | Påverkas område (till exempel Service, komponenten eller SDK) | Kluster-typ (till exempel Hadoop, HBase eller Storm) | JIRA (om tillämpligt) |
| --- | --- | --- | --- | --- |
| Anpassade metastore uppgraderingen utfärdar för HDI 3.4 |Skapa ett kluster misslyckas om du använder en anpassad metastore som tidigare användes på en tidigare version av en annan HDInsight-kluster. Detta beror på ett uppgradera skriptfel som nu har åtgärdats |Skapa ett kluster |Alla |Saknas |
| Livius kraschåterställning |Innehåller jobb status återhämtning för alla jobb som skickas via Livius |Tillförlitlighet |Spark på Linux |Saknas |
| Jupyter innehåll hög tillgänglighet |Ger möjlighet att spara och läsa in Jupyter-anteckningsbok innehållet till och från lagringskontot som associeras med klustret. Mer information finns i [kernlar som är tillgängliga för Jupyter-anteckningsböcker](hdinsight-apache-spark-jupyter-notebook-kernels.md). |Bärbara datorer |Spark på Linux |Saknas |
| Borttagning av hiveContext i Jupyter-anteckningsböcker |Använd `%%sql` magiskt i stället för `%%hive` Magiskt tal. SqlContext motsvarar hiveContext. Mer information finns i [kernlar som är tillgängliga för Jupyter-anteckningsböcker](hdinsight-apache-spark-jupyter-notebook-kernels.md) |Bärbara datorer |Spark-kluster på Linux |Saknas |
| Utfasning av äldre versioner av Spark |En äldre version av Spark 1.3.1 togs bort från tjänsten på 5-31 |Tjänst |Spark-kluster i Windows |Saknas |

## <a name="notes-for-03292016-release-of-hdinsight"></a>Information om 2016-03-29 versionen av HDInsight
Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen:

* HDInsight (Windows) 2.1.10.875.2159884 (HDP 1.3.12.0-01795 - oförändrade)
* HDInsight (Windows) 3.0.6.875.2159884 (HDP 2.0.13.0-2117 - oförändrade)
* HDInsight (Windows) 3.1.4.875.2159884 (HDP 2.1.15.0-2374 - oförändrade)
* HDInsight (Windows) 3.2.7.875.2159884 (HDP 2.2.9.1-7 - oförändrade)
* HDInsight (Windows) 3.3.0.875.2159884 (HDP 2.3.3.1-16)
* HDInsight (Linux) 3.2.1000.0.7193255 (HDP 2.2.9.1-8 - oförändrade)
* HDInsight (Linux) 3.3.1000.0.7193255 (HDP 2.3.3.1-7 - oförändrade)
* HDInsight (Linux) 3.4.1000.0.7195842 (HDP 2.4.1.0-327)
* SDK 1.5.8

Den här versionen innehåller följande uppdateringar:

| Rubrik | Beskrivning | Påverkas område (till exempel Service, komponenten eller SDK) | Kluster-typ (till exempel Hadoop, HBase eller Storm) | JIRA (om tillämpligt) |
| --- | --- | --- | --- | --- |
| Tillagda HDInsight 3.4 version och uppdaterade HDP versioner för alla HDInsight-kluster |Vi har lagt till HDInsight v3.4 (baserat på HDP 2.4) och har även uppdaterats andra HDP-versioner med den här versionen. HDP 2.4 viktig information som är tillgängliga [här](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html) och du hittar mer information om HDInsight-versioner [här](hdinsight-component-versioning.md). |Tjänst |Alla Linux-kluster |Saknas |
| HDInsight Premium |HDInsight finns nu i två kategorier - Standard och Premium. HDInsight Premium är för närvarande under förhandsgranskning och kan bara Hadoop och Spark-kluster på Linux. Mer information finns i [här](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium). |Tjänst |Hadoop och Spark på Linux |Saknas |
| Microsoft R Server |HDInsight Premium ger Microsoft R Server som kan ingå i Hadoop och Spark-kluster på Linux. Mer information finns i [översikt över R Server på HDInsight](hdinsight-hadoop-r-server-overview.md). |Tjänst |Hadoop och Spark på Linux |Saknas |
| Spark 1.6.0 |3.4 HDInsight-kluster innehåller nu Spark 1.6.0 |Tjänst |Spark-kluster på Linux |Saknas |
| Förbättringar i Jupyter-anteckningsbok |Jupyter-anteckningsböcker som är tillgängliga med Spark-kluster ger ytterligare Spark nu kärnor. De omfattar också förbättringar som användning av %% magic, automatisk visualisering och integrering med Python visualiseringen bibliotek (till exempel matplotlib). Mer information finns i [kernlar som är tillgängliga för Jupyter-anteckningsböcker](hdinsight-apache-spark-jupyter-notebook-kernels.md). |Tjänst |Spark-kluster på Linux |Saknas |

## <a name="notes-for-03222016-release-of-hdinsight"></a>Information om 2016-03-22 versionen av HDInsight
Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen:

* HDInsight (Windows) 2.1.10.875.2159884 (HDP 1.3.12.0-01795 - oförändrade)
* HDInsight (Windows) 3.0.6.875.2159884 (HDP 2.0.13.0-2117 - oförändrade)
* HDInsight (Windows) 3.1.4.875.2159884 (HDP 2.1.15.0-2374 - oförändrade)
* HDInsight (Windows) 3.2.7.875.2159884 (HDP 2.2.9.1-7 - oförändrade)
* HDInsight (Windows) 3.3.0.875.2159884 (HDP 2.3.3.1-16)
* HDInsight (Linux) 3.2.1000.0.7193255 (HDP 2.2.9.1-8 - oförändrade)
* HDInsight (Linux) 3.3.1000.0.7193255 (HDP 2.3.3.1-7 - oförändrade)
* SDK 1.5.8

Den här versionen innehåller följande uppdateringar:

| Rubrik | Beskrivning | Påverkas område (till exempel Service, komponenten eller SDK) | Kluster-typ (till exempel Hadoop, HBase eller Storm) | JIRA (om tillämpligt) |
| --- | --- | --- | --- | --- |
| Uppdaterade HDInsight-versioner för alla HDInsight-kluster |Vi har uppdaterat HDInsight-versioner för alla HDInsight-kluster med den här versionen |Tjänst |Alla |Saknas |

## <a name="notes-for-03102016-release-of-hdinsight"></a>Information om 2016-03-10-versionen av HDInsight
Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen:

* HDInsight (Windows) 2.1.10.859.2123216 (HDP 1.3.12.0-01795 - oförändrade)
* HDInsight (Windows) 3.0.6.859.2123216 (HDP 2.0.13.0-2117 - oförändrade)
* HDInsight (Windows) 3.1.4.859.2123216 (HDP 2.1.15.0-2374 - oförändrade)
* HDInsight (Windows) 3.2.7.859.2123216 (HDP 2.2.9.1-7)
* HDInsight (Windows) 3.3.0.859.2123216 (HDP 2.3.3.1-5 - oförändrade)
* HDInsight (Linux) 3.2.1000.7076817 (HDP 2.2.9.1-8)
* HDInsight (Linux) 3.3.1000.7076817 (HDP 2.3.3.1-7)
* SDK 1.5.8

Den här versionen innehåller följande uppdateringar:

| Rubrik | Beskrivning | Påverkas område (till exempel Service, komponenten eller SDK) | Kluster-typ (till exempel Hadoop, HBase eller Storm) | JIRA (om tillämpligt) |
| --- | --- | --- | --- | --- |
| Uppdaterade HDInsight-versioner för alla HDInsight-kluster |Vi har uppdaterat HDInsight-versioner för alla HDInsight-kluster med den här versionen |Tjänst |Alla |Saknas |

## <a name="notes-for-01272016-release-of-hdinsight"></a>Information om 2016-01-27-versionen av HDInsight
Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen:

* HDInsight (Windows) 2.1.10.817.2028315 (HDP 1.3.12.0-01795 - oförändrade)
* HDInsight (Windows) 3.0.6.817.2028315 (HDP 2.0.13.0-2117 - oförändrade)
* HDInsight (Windows) 3.1.4.817.2028315 (HDP 2.1.15.0-2374 - oförändrade)
* HDInsight (Windows) 3.2.7.817.2028315 (HDP 2.2.9.1-1)
* HDInsight (Windows) 3.3.0.817.2028315 (HDP 2.3.3.1-5 - oförändrade)
* HDInsight (Linux) 3.2.1000.4072335 (HDP 2.2.9.1-1)
* HDInsight (Linux) 3.3.1000.4072335 (HDP 2.3.3.1-1)
* SDK 1.5.8

Den här versionen innehåller följande uppdateringar:

| Rubrik | Beskrivning | Påverkas område (till exempel Service, komponenten eller SDK) | Kluster-typ (till exempel Hadoop, HBase eller Storm) | JIRA (om tillämpligt) |
| --- | --- | --- | --- | --- |
| Uppdaterade HDInsight-versioner för alla HDInsight-kluster |Vi har uppdaterat HDInsight-versioner för alla HDInsight-kluster med den här versionen |Tjänst |Alla |Saknas |

## <a name="notes-for-12022015-release-of-hdinsight"></a>Information om 02/12/2015-versionen av HDInsight
Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen:

* HDInsight (Windows) 2.1.10.763.1931434 (HDP 1.3.12.0-01795 - oförändrade)
* HDInsight (Windows) 3.0.6.763.1931434 (HDP 2.0.13.0-2117 - oförändrade)
* HDInsight (Windows) 3.1.4.763.1931434 (HDP 2.1.15.0-2374 - oförändrade)
* HDInsight (Windows) 3.2.7.763.1931434 (HDP 2.2.7.1-34 - oförändrade)
* HDInsight (Windows) 3.3.1000.0 (HDP 2.3.3.1-5)
* HDInsight (Linux) 3.2.1000.0.6392801 (HDP 2.2.7.1-34 - oförändrade)
* HDInsight (Linux) 3.3.1000.0 (HDP 2.3.3.0-3039)
* SDK 1.5.8

Den här versionen innehåller följande uppdateringar:

| Rubrik | Beskrivning | Påverkas område (till exempel Service, komponenten eller SDK) | Kluster-typ (till exempel Hadoop, HBase eller Storm) | JIRA (om tillämpligt) |
| --- | --- | --- | --- | --- |
| Tillagda HDInsight 3.3-versionen och uppdaterade HDP versioner för alla HDInsight-kluster |Vi har lagt till HDInsight v3.3 (baserat på HDP 2.3) och har även uppdaterats andra HDP-versioner med den här versionen. HDP 2.3 viktig information som är tillgängliga [här](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html) och du hittar mer information om HDInsight-versioner [här](hdinsight-component-versioning.md). |Tjänst |Alla |Saknas |

## <a name="notes-for-11302015-release-of-hdinsight"></a>Information om 2015-11/30-versionen av HDInsight
Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen:

* HDInsight (Windows) 2.1.10.757.1923908 (HDP 1.3.12.0-01795 - oförändrade)
* HDInsight (Windows) 3.0.6.757.1923908 (HDP 2.0.13.0-2117 - oförändrade)
* HDInsight (Windows) 3.1.4.757.1923908 (HDP 2.1.15.0-2374 - oförändrade)
* HDInsight (Windows) 3.2.7.757.1923908 (HDP 2.2.7.1-34)
* HDInsight (Linux) 3.2.1000.0.6392801 (HDP 2.2.7.1-34)
* SDK 1.5.8

Den här versionen innehåller följande uppdateringar:

| Rubrik | Beskrivning | Påverkas område (till exempel Service, komponenten eller SDK) | Kluster-typ (till exempel Hadoop, HBase eller Storm) | JIRA (om tillämpligt) |
| --- | --- | --- | --- | --- |
| Uppdaterade HDInsight-versioner för alla HDInsight-kluster och HDP versioner för 3.2 HDInsight-kluster (Windows och Linux) |Med den här versionen, har HDInsight och HDP versioner uppdaterats |Tjänst |Alla |Saknas |

## <a name="notes-for-10272015-release-of-hdinsight"></a>Information om 27/10/2015-versionen av HDInsight
Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen:

* HDInsight (Windows) 2.1.10.726.1866228 (HDP 1.3.12.0-01795 - oförändrade)
* HDInsight (Windows) 3.0.6.726.1866228 (HDP 2.0.13.0-2117 - oförändrade)
* HDInsight (Windows) 3.1.4.726.1866228 (HDP 2.1.15.0-2374 - oförändrade)
* HDInsight (Windows) 3.2.7.726.1866228 (HDP 2.2.7.1-33)
* HDInsight (Linux) 3.2.1000.0.6035701 (HDP 2.2.7.1-33)
* SDK 1.5.8

Den här versionen innehåller följande uppdateringar:

| Rubrik | Beskrivning | Påverkas område (till exempel Service, komponenten eller SDK) | Kluster-typ (till exempel Hadoop, HBase eller Storm) | JIRA (om tillämpligt) |
| --- | --- | --- | --- | --- |
| Uppdaterade HDInsight-versioner för alla HDInsight-kluster (Windows och Linux) |Med den här versionen, har HDInsight och HDP versioner uppdaterats |Tjänst |Alla |Saknas |
| Fast Jupyter för Windows Spark-kluster med versaler kluster |Kluster som hade DNS-namn som angetts i versaler hade problem med Jupyter-anteckningsböcker på grund av en begäran om kontroll av ursprung. Korrigering skulle ändra DNS-namn för Jupyters konfiguration till gemener. |Tjänst |HDInsight Spark (Windows) |Saknas |

## <a name="notes-for-10202015-release-of-hdinsight"></a>Information om 20/10/2015-versionen av HDInsight
Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen:

* HDInsight 2.1.10.716.1846990 (Windows) (HDP 1.3.12.0-01795 - oförändrade)
* HDInsight 3.0.6.716.1846990 (Windows) (HDP 2.0.13.0-2117 - oförändrade)
* HDInsight 3.1.4.716.1846990 (Windows) (HDP 2.1.16.0-2374)
* HDInsight 3.2.7.716.1846990 (Windows) (HDP 2.2.7.1-0004)
* HDInsight 3.2.1000.0.5930166 (Linux) (HDP 2.2.7.1-0004)
* SDK 1.5.8

Den här versionen innehåller följande uppdateringar:

| Rubrik | Beskrivning | Påverkas område (till exempel Service, komponenten eller SDK) | Kluster-typ (till exempel Hadoop, HBase eller Storm) | JIRA (om tillämpligt) |
| --- | --- | --- | --- | --- |
| HDP standardversionen ändras till HDP 2.2 |Standardversionen för HDInsight Windows-kluster har ändrats till HDP 2.2. HDInsight version 3.2 (HDP 2.2) är allmänt tillgänglig i sedan februari 2015. Den här ändringen vänder endast klustret standardversionen när ett explicit val inte har gjorts vid etablering klustret med Azure-portalen, PowerShell-cmdlets eller SDK. |Tjänst |Alla |Saknas |
| Ändringar i VM namnformat för att distribuera flera HDInsight på Linux-kluster i ett enda virtuellt nätverk |Stöd för att distribuera flera HDInsight Linux-kluster i ett enda virtuellt nätverk har lagts till i den här versionen. Som del av uppdateringen format för namn på virtuella datorer i klustret har ändrats från headnode\*, workernode\* och zookeepernode\* till hn\*, ned\*, och zk\* respektive. Det rekommenderas inte att ta ett direkt beroende på formatet för namn på virtuella datorer, eftersom detta kan ändras. Använd ”hostname -f” på den lokala datorn eller Ambari APIs för att fastställa listan över värdar och mappningen av komponenter till värdar. Du hittar mer information i [https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/hosts.md](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/hosts.md) och [https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/host-components.md](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/host-components.md). |Tjänst |HDInsight-kluster på Linux |Saknas |
| Konfigurationsändringar |Följande konfigurationer är nu aktiverad för 3.1 HDInsight-kluster: <ul><li>tez.yarn.ATS.Enabled och yarn.log.server.url. Detta gör tidslinjen programservern och Log-server för att kunna leverera loggar.</li></ul>Följande konfigurationer har ändrats för 3.2 HDInsight-kluster: <ul><li>mapreduce.fileoutputcommitter.Algorithm.version har angetts till 2. På så sätt kan du använda version 2 av FileOutputCommitter.</li></ul> |Tjänst |Alla |Saknas |

## <a name="notes-for-09092015-release-of-hdinsight"></a>Information om 09/09/2015-versionen av HDInsight
Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen:

* HDInsight 2.1.10.675.1768697 (HDP 1.3.12.0-01795 - oförändrade)
* HDInsight 3.0.6.675.1768697 (HDP 2.0.13.0-2117 - oförändrade)
* HDInsight 3.1.4.675.1768697 (HDP 2.1.15.0-2334 - oförändrade)
* HDInsight 3.2.6.675.1768697 (HDP 2.2.6.1-0012 - oförändrade)
* SDK 1.5.8

Den här versionen innehåller följande uppdateringar:

| Rubrik | Beskrivning | Påverkas område (till exempel Service, komponenten eller SDK) | Kluster-typ (till exempel Hadoop, HBase eller Storm) | JIRA (om tillämpligt) |
| --- | --- | --- | --- | --- |
| Uppdaterade HDInsight-versioner för alla HDInsight-kluster |Med den här versionen har HDInsight-versioner uppdaterats |Tjänst |Alla |Saknas |

## <a name="notes-for-07312015-release-of-hdinsight"></a>Information om 07/31/2015-versionen av HDInsight
Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen:

* HDInsight 2.1.10.640.1695824 (HDP 1.3.12.0-01795 - oförändrade)
* HDInsight 3.0.6.640.1695824 (HDP 2.0.13.0-2117 - oförändrade)
* HDInsight 3.1.4.640.1695824 (HDP 2.1.15.0-2334 - oförändrade)
* HDInsight 3.2.6.640.1695824 (HDP 2.2.6.1-0012 - oförändrade)
* SDK 1.5.8

Den här versionen innehåller följande uppdateringar:

| Rubrik | Beskrivning | Påverkas område (till exempel Service, komponenten eller SDK) | Kluster-typ (till exempel Hadoop, HBase eller Storm) | JIRA (om tillämpligt) |
| --- | --- | --- | --- | --- |
| Åtgärda Spark-kluster nod återställning av avbildning arbetsflöde |Ett fel som orsakade Spark klusternoder att inte återställa efter avbildningsåterställning fast |Tjänst |Spark |Saknas |

## <a name="notes-for-07312015-release-of-hdinsight"></a>Information om 07/31/2015-versionen av HDInsight
Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen:

* HDInsight 2.1.10.635.1684502 (HDP 1.3.12.0-01795 - oförändrade)
* HDInsight 3.0.6.635.1684502 (HDP 2.0.13.0-2117 - oförändrade)
* HDInsight 3.1.4.635.1684502 (HDP 2.1.15.0-2334 - oförändrade)
* HDInsight 3.2.6.635.1684502 (HDP 2.2.6.1-0012 - oförändrade)
* SDK 1.5.8

Den här versionen innehåller följande uppdateringar:

| Rubrik | Beskrivning | Påverkas område (till exempel Service, komponenten eller SDK) | Kluster-typ (till exempel Hadoop, HBase eller Storm) | JIRA (om tillämpligt) |
| --- | --- | --- | --- | --- |
| Uppdaterade HDInsight-versioner för alla HDInsight-kluster |Med den här versionen har HDInsight-versioner uppdaterats |Tjänst |Alla |Saknas |

## <a name="notes-for-07072015-release-of-hdinsight"></a>Information om 07/07/2015-versionen av HDInsight
Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen:

* HDInsight 2.1.10.610.1630216 (HDP 1.3.12.0-01795 - oförändrade)
* HDInsight 3.0.6.610.1630216 (HDP 2.0.13.0-2117 - oförändrade)
* HDInsight 3.1.4.610.1630216 (HDP 2.1.15.0-2334 - oförändrade)
* HDInsight 3.2.4.610.1630216 (HDP 2.2.6.1-0012)
* SDK 1.5.8

Den här versionen innehåller följande uppdateringar:

| Rubrik | Beskrivning | Påverkas område (till exempel Service, komponenten eller SDK) | Kluster-typ (till exempel Hadoop, HBase eller Storm) | JIRA (om tillämpligt) |
| --- | --- | --- | --- | --- |
| Uppdaterade HDP versioner för 3.2 HDInsight-kluster |Med den här versionen distribuerar HDInsight 3.2 HDP 2.2.6.1-0012 |Tjänst |Alla |Saknas |

## <a name="notes-for-06262015-release-of-hdinsight"></a>Information om 06/26/2015-versionen av HDInsight
Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen:

* HDInsight 2.1.10.601.1610731 (HDP 1.3.12.0-01795 - oförändrade)
* HDInsight 3.0.6.601.1610731 (HDP 2.0.13.0-2117 - oförändrade)
* HDInsight 3.1.4.601.1610731 (HDP 2.1.15.0-2334 - oförändrade)
* HDInsight 3.2.4.601.1610731 (HDP 2.2.6.1-0011)
* SDK 1.5.8

Den här versionen innehåller följande uppdateringar:

<table border="1">
<tr>
<th>Rubrik</th>
<th>Beskrivning</th>
<th>Påverkas område (till exempel Service, komponenten eller SDK)</p></th>
<th>Kluster-typ (till exempel Hadoop, HBase eller Storm)</th>
<th>JIRA (om tillämpligt)</th>
</tr>
<tr>
<td>Uppdaterade HDP versioner för 3.2 HDInsight-kluster</td>
<td>Med den här versionen distribuerar HDInsight 3.2 HDP 2.2.6.1</td>
<td>Tjänst</td>
<td>Alla</td>
<td>Saknas</td>
</tr>
</table>

## <a name="notes-for-06182015-release-of-hdinsight"></a>Information om 06/18/2015-versionen av HDInsight
Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen:

* HDInsight 2.1.10.596.1601657 (HDP 1.3.12.0-01795 - oförändrade)
* HDInsight 3.0.6.596.1601657 (HDP 2.0.13.0-2117 - oförändrade)
* HDInsight 3.1.4.596.1601657 (HDP 2.1.15.0-2334)
* HDInsight 3.2.4.596.1601657 (HDP 2.2.6.1-0002)
* SDK 1.5.8

Den här versionen innehåller följande uppdateringar:

<table border="1">
<tr>
<th>Rubrik</th>
<th>Beskrivning</th>
<th>Påverkas område (till exempel Service, komponenten eller SDK)</p></th>
<th>Kluster-typ (till exempel Hadoop, HBase eller Storm)</th>
<th>JIRA (om tillämpligt)</th>
</tr>
<tr>
<td>Ytterligare öppnade HTTPS-portar</td>
<td>Molntjänsten öppnar nu 5 portar 8001 8005 på klustret t.ex. vid https://<clustername>.azurehdinsight.net:8001/. Begäranden till dessa URL: er autentiseras med samma grundläggande autentisering lösenord mekanism som port 443. De här portarna binda till samma port på den aktiva headnode. Skriptåtgärder kan användas för att lyssna på dessa portar på det headnode och skicka till utanför klustret kundtjänst.</td>
<td>Molntjänsten</td>
<td>Alla</td>
<td>Saknas</td>
</tr>
<tr>
<td>Tillfälligt MapReduce blanda fel för HDInsight 3.2</td>
<td>Korrigering för ett sällsynt, återkommande konkurrenstillstånd i kartan minska blanda stora kluster, vilket resulterar i misslyckade tillfällig aktiviteter. Se <a href="https://issues.apache.org/jira/browse/MAPREDUCE-6361" target="_blank">MAPREDUCE 6361</a> för mer information.</td>
<td>Hadoop kärnor</td>
<td>Alla</td>
<td><a href="https://issues.apache.org/jira/browse/MAPREDUCE-6361" target="_blank">MAPREDUCE 6361</a></td>
</tr>
<tr>
<td>Flytta till den senaste Azure Java SDK 2.2 för HDInsight 3.2</td>
<td>Flytta till senaste versionen av Azure SDK för Java som används av WASB-drivrutinen. Den senaste SDK: N har några korrigeringar och viktig information om den finns på https://github.com/Azure/azure-storage-java/blob/master/ChangeLog.txt.</td>
<td>Hadoop kärnor</td>
<td>Alla</td>
<td><a href="https://issues.apache.org/jira/browse/HADOOP-11959" target="_blank">HADOOP-11959</a></td>
</tr>
<tr>
<td>Flytta till HDP 2.1.15 för 3.1 HDInsight-kluster</td>
<td>Hortonworks viktig information för versionen är tillgängliga <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.15-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.15.html" target="_blank">här</a>.</td>
<td>HDP</td>
<td>Alla</td>
<td>Saknas</td>
</tr>
</table>

## <a name="notes-for-06042015-release-of-hdinsight"></a>Information om 06/04/2015-versionen av HDInsight
Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen:

* HDInsight 2.1.10.583.1575584 (HDP 1.3.12.0-01795 - oförändrade)
* HDInsight 3.0.6.583.1575584 (HDP 2.0.13.0-2117 - oförändrade)
* HDInsight 3.1.3.583.1575584 (HDP 2.1.12.1-0003 - oförändrade)
* HDInsight 3.2.4.583.1575584 (HDP 2.2.6.1-1)
* SDK 1.5.8

Den här versionen innehåller följande uppdateringar:

<table border="1">
<tr>
<th>Rubrik</th>
<th>Beskrivning</th>
<th>Påverkas område (till exempel Service, komponenten eller SDK)</p></th>
<th>Kluster-typ (till exempel Hadoop, HBase eller Storm)</th>
<th>JIRA (om tillämpligt)</th>
</tr>
<tr>
<td>Korrigering för 502 Felaktig gateway-fel för Storm-kluster</td>
<td>Ett fel som påverkar jobbet API som orsakade att webbplatsen ska vara ned efter en omstart för att rätta till den här versionen.</td>
<td>Tjänst</td>
<td>Storm</td>
<td>Saknas</td>
</tr>
</table>

## <a name="notes-for-06012015-release-of-hdinsight"></a>Information om 06/01/2015-versionen av HDInsight
Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen:

* HDInsight 2.1.10.577.1563827 (HDP 1.3.12.0-01795 - oförändrade)
* HDInsight 3.0.6.577.1563827 (HDP 2.0.13.0-2117 - oförändrade)
* HDInsight 3.1.3.577.1563827 (HDP 2.1.12.1-0003 - oförändrade))
* HDInsight 3.2.4.577.1563827 (HDP 2.2.6.0-2800 - oförändrade)
* SDK 1.5.8

Den här versionen innehåller följande uppdateringar:

<table border="1">
<tr>
<th>Rubrik</th>
<th>Beskrivning</th>
<th>Påverkas område (till exempel Service, komponenten eller SDK)</p></th>
<th>Kluster-typ (till exempel Hadoop, HBase eller Storm)</th>
<th>JIRA (om tillämpligt)</th>
</tr>
<tr>
<td>Olika felkorrigeringar</td>
<td>Uppdateringen åtgärdar buggar som rör klusteretablering.</td>
<td>Tjänst</td>
<td>Alla klustertyper av</td>
<td>Saknas</td>
</tr>
</table>

## <a name="notes-for-05272015-release-of-hdinsight"></a>Information om 05/27/2015-versionen av HDInsight
Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen:

* HDInsight 3.2.4.570.1554102 (HDP 2.2.6.0-2800)
* Andra versioner av klustret och SDK distribueras inte som en del av den här versionen.

Den här versionen innehåller följande uppdateringar:

<table border="1">
<tr>
<th>Rubrik</th>
<th>Beskrivning</th>
<th>Påverkas område (till exempel Service, komponenten eller SDK)</p></th>
<th>Kluster-typ (till exempel Hadoop, HBase eller Storm)</th>
<th>JIRA (om tillämpligt)</th>
</tr>
<tr>
<td>HDP 2.2 uppdatering</td>
<td>Den här versionen av HDInsight 3.2 innehåller HDP 2.2.6 och ger flera viktiga felkorrigeringar för HDInsight. Fullständig viktig information finns på <a href="http://dev.hortonworks.com.s3.amazonaws.com/HDPDocuments/HDP2/HDP-2.2.6/HDP_RelNotes_v226/index.html">HDP 2.2.6 viktig information</a>.</td>
<td>HDP</td>
<td>Alla klustertyper av</td>
<td>Saknas</td>
</tr>
<tr>
<td>Ändra till minne standardkonfigurationen Yarn behållare</td>
<td>I den här uppdateringen har standard tillgängliga minnet till YARN behållare (yarn.nodemanager.resource.memory mb och yarn.scheduler.maximum allokering mb), startas av noden Manager utökats till 5632MB. Tidigare detta har minskat till 4608MB, men baserat på olika jobbkörningar, det nya värdet måste ge bättre tillförlitlighet och prestanda för de flesta jobb, därför är ett bättre standardvärde. Som vanligt, om du har kraftigt beroende på den här minneskonfigurationen ange explicit när klustret skapas.</td>
<td>HDP</td>
<td>Alla klustertyper av</td>
<td>Saknas</td>
</tr>
<tr>
<td>Standard Config paritet för HBase och Storm-kluster</td>
<td>Den här uppdateringen återställer Hbase och Storm-kluster för att använda samma värden för YARN konfigurationerna som Hadoop-kluster. Detta görs för paritet mellan alla typer av klustret.</td>
<td>HDP</td>
<td>HBase, Storm</td>
<td>Saknas</td>
</tr>
</table>

## <a name="notes-for-05202015-release-of-hdinsight"></a>Information om 20/05/2015-versionen av HDInsight
Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen:

* HDInsight 2.1.10.564.1542093 (HDP 1.3.12.0-01795 - oförändrade)
* HDInsight 3.0.6.564.1542093 (HDP 2.0.13.0-2117 - oförändrade)
* HDInsight 3.1.3.564.1542093 (HDP 2.1.12.1-0003)
* HDInsight 3.2.4.564.1542093 (HDP 2.2.4.6-2)
* SDK 1.5.8

Den här versionen innehåller följande uppdateringar:

<table border="1">
<tr>
<th>Rubrik</th>
<th>Beskrivning</th>
<th>Påverkas område (till exempel Service, komponenten eller SDK)</p></th>
<th>Kluster-typ (till exempel Hadoop, HBase eller Storm)</th>
<th>JIRA (om tillämpligt)</th>
</tr>
<tr>
<td>SCP.NET EventHub-Support</td>
<td>Uppdaterade klusterpaket för Storm på HDInsight gör SCP.NET nya funktioner. Nu har du tillgång till nya API: er i topologin builder som gör det enklare att använda EventHubSpout eller Java Spouts. Du måste uppdatera SCP.NET klienten SDK om du vill arbeta med nya kluster som kontrakten har uppdaterats. Användnings- och release notes (inklusive felkorrigeringar) finns information om de nya API: erna i filen Viktigt i SCP.NET NuGet-paketet.</td>
<td>VS Tooling</td>
<td>Storm 3.2 HDInsight-kluster</td>
<td>Saknas</td>
</tr>
<tr>
<td>JDBC-drivrutinen</td>
<td>Uppdatera drivrutinen till version SQL Server som stöds i sqljdbc_4.1.5605.100.</td>
<td>Metastore</td>
<td>Alla</td>
<td>Saknas</td>
</tr>
</table>

## <a name="notes-for-04272015-release-of-hdinsight"></a>Anteckningar för version 2015-04/27 för HDInsight
Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen:

* HDInsight 2.1.10.537.1486660 (HDP 1.3.12.0-01795 - oförändrade)
* HDInsight 3.0.6.537.1486660 (HDP 2.0.13.0-2117 - oförändrade)
* HDInsight 3.1.3.537.1486660 (HDP 2.1.12.0-2329 - oförändrade)
* HDInsight 3.2.3.537.1486660 (HDP 2.2.2.2-4)
* SDK 1.5.8

Den här versionen innehåller följande uppdateringar:

<table border="1">
<tr>
<th>Rubrik</th>
<th>Beskrivning</th>
<th>Påverkas område (till exempel Service, komponenten eller SDK)</p></th>
<th>Kluster-typ (till exempel Hadoop, HBase eller Storm)</th>
<th>JIRA (om tillämpligt)</th>
</tr>
<tr>
<td>Åtgärda DLL-beroendet</td>
<td>Tar bort HDInsight beroende på enhet Test Framework.</td>
<td>SDK</td>
<td>Hadoop</td>
<td>Saknas</td>
</tr>
<tr>
<td>Buggfix för konkurrenstillstånd</td>
<td>Ett kluster skapa begäran nu väntar på PUT-förfrågan godkänns innan avsökning status</td>
<td>SDK</td>
<td>Hadoop</td>
<td>Saknas</td>
</tr>
</table>

## <a name="notes-for-04142015-release-of-hdinsight"></a>Information om 2015-04/14-versionen av HDInsight
Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen:

* HDInsight 2.1.10.521.1453250 (HDP 1.3.12.0-01795 - oförändrade)
* HDInsight 3.0.6.521.1453250 (HDP 2.0.13.0-2117 - oförändrade)
* HDInsight 3.1.3.521.1453250 (HDP 2.1.12.0-2329 - oförändrade)
* HDInsight 3.2.3.525.1459730 (HDP 2.2.2.2-2)
* SDK 1.5.6

Den här versionen innehåller följande uppdateringar:

<table border="1">
<tr>
<th>Rubrik</th>
<th>Beskrivning</th>
<th>Påverkas område (till exempel Service, komponenten eller SDK)</p></th>
<th>Kluster-typ (till exempel Hadoop, HBase eller Storm)</th>
<th>JIRA (om tillämpligt)</th>
</tr>
<tr>
<td>Tez felkorrigeringar</td>
<td>Korrigeringar för Apache TEZ 2214 och TEZ 1923 ingår i den här versionen av HDI 3.2. Dessa krävs för vissa Hive-frågor på Tez som kräver för att blanda en stor mängd data.
</td>
<td>HDP</td>
<td>Hadoop</td>
<td><a href="https://issues.apache.org/jira/browse/TEZ-2214">TEZ 2214</a></br><a href="https://issues.apache.org/jira/browse/TEZ-1923">TEZ 1923</a></td>
</tr>
</table>

## <a name="notes-for-04062015-release-of-hdinsight"></a>Information om 2015-04/06-versionen av HDInsight
Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen:

* HDInsight 2.1.10.521.1453250 (HDP 1.3.12.0-01795 - oförändrade)
* HDInsight 3.0.6.521.1453250 (HDP 2.0.13.0-2117 - oförändrade)
* HDInsight 3.1.3.521.1453250 (HDP 2.1.12.0-2329 - oförändrade)
* HDInsight 3.2.3.521.1453250 (HDP 2.2.2.2-1)
* SDK 1.5.6

Den här versionen innehåller följande uppdateringar:

<table border="1">
<tr>
<th>Rubrik</th>
<th>Beskrivning</th>
<th>Påverkas område (till exempel Service, komponenten eller SDK)</p></th>
<th>Kluster-typ (till exempel Hadoop, HBase eller Storm)</th>
<th>JIRA (om tillämpligt)</th>
</tr>
<tr>
<td>HDInsight .NET SDK 1.5.6</td>
<td>Uppdateringar för att ta bort vissa interna klasser för HDInsight på Linux.</td>
<td>SDK</td>
<td>Hadoop</td>
<td>Saknas</td>
</tr>
<tr>
<td>Avro Library 1.5.6</td>
<td>Lägga till <b>KnownTypeAttribute</b> för metoden <b>GetAllKnownTypes</b>. Fast NullReferenceException när typen är null för GetAllKnownTypes metod.</td>
<td>SDK</td>
<td>Hadoop</td>
<td>Saknas</td>
</tr>
<tr>
<td>Felkorrigeringar</td>
<td>Olika felkorrigeringar till tjänsten</td>
<td>Tjänst</td>
<td>Alla</td>
<td>Saknas</td>
</tr>
</table>

## <a name="notes-for-04012015-release-of-hdinsight"></a>Information om 04/01/2015-versionen av HDInsight
Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen:

* HDInsight 2.1.10.513.1431705 (HDP 1.3.12.0-01795)
* HDInsight 3.0.6.513.1431705 (HDP 2.0.13.0-2117)
* HDInsight 3.1.3.513.1431705 (HDP 2.1.12.0-2329)
* HDInsight 3.2.3.513.1431705 (HDP 2.2.2.1-2600)
* SDK 1.5.5

Den här versionen innehåller följande uppdateringar:

<table border="1">
<tr>
<th>Rubrik</th>
<th>Beskrivning</th>
<th>Påverkas område (till exempel Service, komponenten eller SDK)</p></th>
<th>Kluster-typ (till exempel Hadoop, HBase eller Storm)</th>
<th>JIRA (om tillämpligt)</th>
</tr>
<tr>
<td>Möjligheten att aktivera/inaktivera remote desktop autentiseringsuppgifter för Windows-kluster via .NET SDK</td>
<td>Programmässiga stöd för att aktivera eller inaktivera RDP-autentiseringsuppgifter för Windows-kluster.</td>
<td>SDK</td>
<td>Alla</td>
<td>Saknas</td>
</tr>
<tr>
<td>Möjligheten att aktivera remote desktop autentiseringsuppgifter på kluster när de har etablerats</td>
<td>Programmässiga stöd för att aktivera remote desktop autentiseringsuppgifter när klustret skapas. Detta tar bort tvåstegsprocess för först etablering klustret och sedan aktivera Fjärrskrivbord.</td>
<td>SDK</td>
<td>Alla</td>
<td>Saknas</td>
</tr>
<tr>
<td>Uppgraderade Python till 2.7.8</td>
<td>Uppgraderade Python på HDInsight-kluster till Python 2.7.8, som innehåller vissa viktiga säkerhet korrigeringsfiler för HDInsight version 2.1, 3.0, 3.1 och 3.2</td>
<td>Tjänst</td>
<td>Alla</td>
<td>Saknas</td>
</tr>
<tr>
<td>YARN konfigurationsändring</td>
<td>Ändrade YARN configuration yarn.resourcemanager.max slutförts-program till 1000 för alla typer av klustret för HDInsight version 3.1 och 3.2. Det här värdet styr bara listan över slutförda program i YARN-Användargränssnittet. För att få information om program som har skickats innan listan med program som visas i Användargränssnittet, kan du gå direkt till servern historik.</td>
<td>YARN</td>
<td>Alla</td>
<td>Saknas</td>
</tr>
<tr>
<td>Storleksändring av noder i ett HBase-kluster</td>
<td>HBase-kluster kan nu storleksändring av noder (uppåt och nedåt) för HDInsight version 3.1 och 3.2</td>
<td>Tjänst</td>
<td>HBase</td>
<td>Saknas</td>
</tr>
<tr>
<td>JDBC-uppgradering</td>
<td>SQL JDBC-drivrutinen har uppgraderats till version sqljdbc_4.0.2206.100 för HDInsight version 3.2. Den här versionen innehåller viktiga säkerhetsförbättringar.</td>
<td>HDP</td>
<td>Alla</td>
<td>Saknas</td>
</tr>
<tr>
<td>Uppdatering för JVM-konfiguration</td>
<td>Uppdaterade JVM configuration networkaddress.cache.ttl med 300 sekunder från standardvärdet-1 för HDInsight version 3.1 och 3.2. Konfigurationsvärdet anger cachelagringsprincipen för lyckad namnsökningar från namntjänsten. Det löser ett fel som rör växande och minska HBase-kluster.</td>
<td>Tjänst</td>
<td>HBase</td>
<td>Saknas</td>
</tr>
<tr>
<td>Uppgradera till Azure Storage SDK för Java 2.0</td>
<td>HDInsight version 3.2 uppgraderas för att använda den senaste versionen av Azure Storage SDK för Java. Innehåller flera viktiga felkorrigeringar över aktuella 0.6.0 version.</td>
<td>HDP</td>
<td>Alla</td>
<td>Saknas</td>
</tr>
<tr>
<td>Uppgraderas till Apache WASB källkod</td>
<td>HDInsight version 3.2 nu använder senast kod för WASB filesystem drivrutinen från Apache Hadoop. Med den här ändringen paketerad drivrutinens WASB nu som en separat jar. Detta är endast en ändring av paketering och innehåller inte några ändringar beteendet hos WASB-drivrutinen. Namnet på den här JAR-filen är hadoop-azure-2.6.0.jar.</td>
<td>HDP</td>
<td>Alla</td>
<td>Saknas</td>
</tr>
<tr>
<td>JAR-filnamnet uppdateringar i HDInsight 3.2</td>
<td>Den här uppdateringen till HDInsight version 3.2 innehåller flera felkorrigeringar och några interna burkar paketeras som en del av HDP har uppgraderats. Dessa JAR-filesare interna HDP paketet och inte för direkt användning av kundprogram. Program ska paketet sin egen version av burkar så att en uppgradering till HDP interna burkar inte bryts kundprogram.</td>
<td>HDP</td>
<td>Alla</td>
<td>Saknas</td>
</tr>
</table>

## <a name="notes-for-03032015-release-of-hdinsight"></a>Information om 03-03/2015-versionen av HDInsight
Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen:

* HDInsight 2.1.10.488.1375841 (HDP 1.3.9.0-01351 - oförändrade)
* HDInsight 3.0.6.488.1375841 (HDP 2.0.9.0-2097 - oförändrade)
* HDInsight 3.1.3.488.1375841 (HDP 2.1.10.0-2290 - oförändrade)
* HDInsight 3.2.3.488.1375841 (HDP-2.2.10.0-2340 - oförändrade)
* SDK 1.5.0 (unchanged)

Den här versionen innehåller följande uppdatering:

<table border="1">
<tr>
<th>Rubrik</th>
<th>Beskrivning</th>
<th>Påverkas område (till exempel service, komponenten eller SDK)</p></th>
<th>Kluster-typ (till exempel Hadoop, HBase eller Storm)</th>
<th>JIRA</th>
</tr>
<tr>
<td>Förbättrad tillförlitlighet</td>
<td>Vi har gjort korrigeringar som gör att tjänsten för att bättre skala med den ökade belastningen med avseende på klustret skapas.</td>
<td>Tjänst</td>
<td>Alla</td>
<td>Saknas</td>
</tr>
</table>

## <a name="notes-for-02182015-release-of-hdinsight"></a>Information om 02-18/2015-versionen av HDInsight
Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen:

* HDInsight 2.1.10.471.1342507 (HDP 1.3.9.0-01351 - oförändrade)
* HDInsight 3.0.6.471.1342507 (HDP 2.0.9.0-2097 - oförändrade)
* HDInsight 3.1.3.471.1342507 (HDP 2.1.10.0-2290 - oförändrade)
* HDInsight 3.2.3.471.1342507 (HDP-2.2.10.0-2340)
* SDK 1.5.0

Den här versionen innehåller följande uppdateringar:

<table border="1">
<tr>
<th>Rubrik</th>
<th>Beskrivning</th>
<th>Påverkas område (till exempel Service, komponenten eller SDK)</p></th>
<th>Kluster-typ (till exempel Hadoop, HBase eller Storm)</th>
<th>JIRA (om tillämpligt)</th>
</tr>
<tr>
<td>3.2 HDInsight-kluster</td>
<td>Hadoop 2.6/HDP2.2 är tillgänglig med HDInsight 3.2-kluster. Den innehåller viktiga uppdateringar för alla komponenter som öppen källkod. Mer information finns i vad är nytt i HDInsight och <a href ="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html" target="_blank">HDP 2.2.0.0 viktig information</a>.</td>
<td>Med öppen källkod</td>
<td>Alla</td>
<td>Saknas</td>
</tr>
<tr>
<td>HDInsight på Linux (förhandsversion)</td>
<td>Kluster kan distribueras körs på Ubuntu Linux. Mer information finns i komma igång med HDInsight på Linux.</td>
<td>Tjänst</td>
<td>Hadoop</td>
<td>Saknas</td>
</tr>
<tr>
<td>Storm allmän tillgänglighet</td>
<td>Apache Storm-kluster är allmänt tillgänglig. Se komma igång med Storm i HDInsight för mer information.</td>
<td>Tjänst</td>
<td>Storm</td>
<td>Saknas</td>
</tr>
<tr>
<td>Storlekar för virtuella datorer</td>
<td>Azure HDInsight är tillgänglig på flera typer av virtuella datorer och storlekar. HDInsight kan också använda A2 till A7-storlekar som byggts för allmänna ändamål. Noder i D-serien som funktionen SSD-enheter och 60 procent snabbare processorer och A8 och A9-storlekar som har InfiniBand-stöd för snabba nätverksfunktioner.</td>
<td>Tjänst</td>
<td>Alla</td>
<td>Saknas</td>
</tr>
<tr>
<td>Skalning av klustret</td>
<td>Du kan ändra antalet datanoder som för ett HDInsight-kluster som körs utan att behöva ta bort eller skapa den igen. För närvarande har Hadoop-fråga och Apache Storm-klustertyper denna möjlighet, men stöd för Apache HBase typ av kluster är snart att följa. Mer information finns i Hantera HDInsight-kluster.</td>
<td>Tjänst</td>
<td>Hadoop, Storm</td>
<td>Saknas</td>
</tr>
<tr>
<td>Visual Studio-tooling</td>
<td>Förutom fullständig verktygsuppsättning för Apache Storm har verktygsuppsättning för Apache Hive i Visual Studio uppdaterats för att inkludera slutförs, lokala validering och förbättrat stöd för felsökning. Mer information finns i komma igång med HDInsight Hadoop-verktyg för Visual Studio.</td>
<td>Tooling</td>
<td>Hadoop</td>
<td>Saknas</td>
</tr>
<td>Hadoop-koppling för Azure Cosmos DB</td>
<td>Med Hadoop-anslutningen för Azure Cosmos DB kan utföra du komplicerade aggregeringar, analys och ändringar över dina schemat mindre JSON-dokument som lagras i Azure Cosmos DB samlingar eller databasen konton. Mer information och en genomgång finns kör Hadoop-jobb med hjälp av Azure Cosmos DB och HDInsight.</td>
<td>Tjänst</td>
<td>Hadoop</td>
<td>Saknas</td>
</tr>
<tr>
<td>Felkorrigeringar</td>
<td>Vi har gjort olika mindre felkorrigeringar för HDInsight-tjänster. Ingen kund-riktade funktionsändringar förväntas.</td>
<td>Tjänst</td>
<td>Alla</td>
<td>Saknas</td>
</tr>
</table>

## <a name="notes-for-02062015-release-of-hdinsight"></a>Information om 02-06/2015-versionen av HDInsight
Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen:

* HDInsight 2.1.10.463.1325367 (HDP 1.3.9.0-01351 - oförändrade)
* HDInsight 3.0.6.463.1325367 (HDP 2.0.9.0-2097 - oförändrade)
* HDInsight 3.1.2.463.1325367 (HDP 2.1.10.0-2290)
* EJ TILLÄMPLIGT SDK

Den här versionen innehåller följande uppdateringar:

<table border="1">
<tr>
<th>Rubrik</th>
<th>Beskrivning</th>
<th>Påverkas område (till exempel Service, komponenten eller SDK)</p></th>
<th>Kluster-typ (till exempel Hadoop, HBase eller Storm)</th>
<th>JIRA (om tillämpligt)</th>
</tr>
<tr>
<td>Felkorrigeringar</td>
<td>Vi har gjort olika mindre felkorrigeringar för HDInsight-tjänster. Ingen kund-riktade funktionsändringar förväntas.</td>
<td>Tjänst</td>
<td>Alla</td>
<td>Saknas</td>
</tr>
<tr>
<td>HDP 2.1 Underhåll uppdatering</td>
<td>HDInsight 3.1 uppdateras för att distribuera HDP 2.1.10.0. Mer information finns i <a href ="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.10/bk_releasenotes_hdp_2.1/content/ch_relnotes-HDP-2.1.10.html" target="_blank">viktig information HDP-2.1.10</a>. </td>
<td>Med öppen källkod</td>
<td>Alla</td>
<td>Saknas</td>
</tr>
<tr>
<td>HDP binära uppdateringar</td>
<td>Det finns några JAR-filer i HBase där filen namn har uppdaterats. Dessa JAR-filer som används internt av HBase, så det inte är den förväntade att kunder har ett beroende på namnen på dessa JAR-filer. Exempel på dessa är:
<ul>
<li>./lib/jetty-6.1.26.hwx.JAR</li>
<li>./lib/jetty-sslengine-6.1.26.hwx.JAR</li>
<li>./lib/jetty-Util-6.1.26.hwx.JAR</li>
</ul>
</td>
<td>Med öppen källkod</td>
<td>HBase</td>
<td>Saknas</td>
</tr>
</table>

## <a name="notes-for-1292015-release-of-hdinsight"></a>Anteckningar för 29/1/2015-versionen av HDInsight
Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen:

* HDInsight 2.1.10.455.1309616 (HDP 1.3.9.0-01351 - oförändrade)
* HDInsight 3.0.6.455.1309616 (HDP 2.0.9.0-2097 - oförändrade)
* HDInsight 3.1.2.455.1309616 (HDP 2.1.9.0-2196 - oförändrade)
* EJ TILLÄMPLIGT SDK

Den här versionen innehåller följande uppdatering:

<table border="1">

<tr>
<th>Rubrik</th>
<th>Beskrivning</th>
<th>Påverkas område (till exempel Service, komponenten eller SDK)</p></th>
<th>Kluster-typ (till exempel Hadoop, HBase eller Storm)</th>
<th>JIRA (om tillämpligt)</th>
</tr>
<tr>
<td>Felkorrigeringar</td>
<td>Vi har gjort några viktiga felkorrigeringar som förbättrar tillförlitligheten för HDInsight-kluster under Azure uppgraderingar.</td>
<td>Tjänst</td>
<td>Alla</td>
<td>Saknas</td>
</tr>
</table>

## <a name="notes-for-152015-release-of-hdinsight"></a>Anteckningar för 1-5/2015-versionen av HDInsight
Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen:

* HDInsight 2.1.10.420.1246118 (HDP 1.3.9.0-01351 - oförändrade)
* HDInsight 3.0.6.420.1246118 (HDP 2.0.9.0-2097 - oförändrade)
* HDInsight 3.1.2.420.1246118 (HDP 2.1.9.0-2196 - oförändrade)

Den här versionen innehåller följande uppdateringar:

<table border="1">

<tr>
<th>Rubrik</th>
<th>Beskrivning</th>
<th>Komponent</th>
<th>Typ av kluster</th>
<th>JIRA (om tillämpligt)</th>
</tr>
<tr>
<td>Exempel för Twitter trendanalys och Mahout-baserade filmrekommendationer</td>
<td><p>HDInsight fråga konsolen har två ytterligare exempel i den här versionen:</p>

<p><b>Trendanalys för Twitter</b><br>
Offentliga API: er som tillhandahålls av webbplatser som Twitter är en användbar datakälla för att analysera och förstå populära trender. I den här kursen lär du dig hur du använder Hive för att hämta en lista över Twitter-användare som skickade de flesta tweets som innehåller ett visst ord. </p>

<p><b>Mahout film rekommendation</b><br>
Apache Mahout är ett Apache Hadoop-machine learning-biblioteket. Mahout innehåller algoritmer för bearbetning av data (till exempel filtrera, klassificering och kluster). Använd en rekommendation motor för att generera filmrekommendationer baserat på filmer som dina vänner har sett i den här självstudiekursen.</p></td>
<td>Frågan konsolen</td>
<td>Hadoop</td>
<td>Saknas</td>
</tr>
<tr>
<td>Ändra standardvärdet för Hive-konfiguration: hive.auto.convert.join.noconditionaltask.size</td>
<td><p>Den här storleken konfigurationen gäller automatiskt konverterade kartan kopplingar. Värdet som representerar summan av de tabeller som kan konverteras till hash-mappningar som ryms i minnet. I en tidigare version ökar det här värdet från standardvärdet 10 MB till 128 MB. Det nya värdet 128 MB orsakades misslyckas på grund av bristande minne. Den här versionen återställer standardvärdet tillbaka till 10 MB. Kunder kan fortfarande välja att åsidosätta det här värdet när klustret skapas baserat på sina frågor och storlekar för tabellen. Läs mer om den här inställningen och hur du åsidosätta den <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.0.2/ds_Hive/optimize-joins.html#JoinOptimization-OptimizeAutoJoinConversion" target="_blank">optimera automatiskt ansluta konvertering</a> i Hortonworks-dokumentationen. </p></td>
<td>Hive</td>
<td>Hadoop, Hbase</td>
<td>Saknas</td>
</tr>
</table>

## <a name="notes-for-12232014-release-of-hdinsight"></a>Anteckningar för version 2014-12-23 för HDInsight
Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen är:

* HDInsight 2.1.10.420.1246783 (HDP version oförändrade)
* HDInsight 3.0.6.420.1246783 (HDP version oförändrade)
* HDInsight 3.1.1.420.1246783 (HDP version oförändrade)

Den här versionen innehåller följande uppdatering:

<table border="1">
<tr>
<th>Rubrik</th>
<th>Beskrivning</th>
<th>Komponent</th>
<th>Typ av kluster</th>
<th>JIRA (om tillämpligt)</th>
</tr>
<tr>
<td>Fel vid skapande återkommande klustret på grund av hög belastning</td>
<td><p>Förbättrad algoritm för att ladda ned HDP paket när klustret skapas gör mer robusta hantering av fel på grund av hög belastning.</p></td>
<td>Tjänst</td>
<td>Hadoop, Hbase, Storm</td>
<td>Saknas</td>
</tr>
</table>

## <a name="notes-for-12182014-release-of-hdinsight"></a>Information om 2014-12-18-versionen av HDInsight
Den här versionen innehåller följande komponenter:

<table border="1">
<tr>
<th>Rubrik</th>
<th>Beskrivning</th>
<th>Komponent</th>
<th>Typ av kluster</th>
<th>JIRA (om tillämpligt)</th>
</tr>
<tr>
<td><a href = "hdinsight-hadoop-customize-cluster.md" target="_blank">Klustret anpassning allmän tillgänglighet</a></td>
<td><p>Anpassning ger möjlighet att anpassa dina Azure HDInsight-kluster med projekt som är tillgängliga från Apache Hadoop-ekosystemet. Med denna nya funktion kan du experimentera och distribuera Hadoop projekt till Azure HDInsight. Detta aktiveras via den **skriptåtgärd** funktion som kan ändra Hadoop-kluster i valfri sätt med hjälp av anpassade skript. Den här anpassningen är tillgänglig på alla typer av HDInsight-kluster och Hadoop, HBase, Storm. För att demonstrera kraften i den här funktionen har vi beskrivs processen för att installera den populära <a href = "hdinsight-hadoop-spark-install.md" target="_blank">Spark</a>, <a href = "hdinsight-hadoop-r-scripts.md" target="_blank">R</a>, <a href = "hdinsight-hadoop-solr-install.md" target="_blank">Solr</a>, och <a href = "hdinsight-hadoop-giraph-install.md" target="_blank">Giraph</a>moduler. Den här versionen lägger också till möjligheten för kunder att ange sina egna skriptåtgärder via Azure portal innehåller riktlinjer och metodtips om hur du skapar anpassade skriptåtgärder med hjälp av hjälpmetoder och ger riktlinjer om hur du testar skriptet åtgärd. </p></td>
<td>Funktionen för allmän tillgänglighet</td>
<td>Alla</td>
<td>Saknas</td>
</tr>
</table>

## <a name="notes-for-12052014-release-of-hdinsight"></a>Information om 2014-12-05-versionen av HDInsight
Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen är:

* HDInsight 2.1.9.406.1221105 (HDP 1.3.9.0-01351)
* HDInsight 3.0.5.406.1221105 (HDP 2.0.9.0-2097)
* HDInsight 3.1.1.406.1221105 (HDP 2.1.9.0-2196)
* HDInsight SDK saknas

Den här versionen innehåller Komponentuppdateringar för följande:

<table border="1">
<tr>
<th>Rubrik</th>
<th>Beskrivning</th>
<th>Komponent</th>
<th>Typ av kluster</th>
<th>JIRA (om tillämpligt)</th>
</tr>
<tr>
<td>Buggfix: återkommande fel när du lägger till stort antal partitioner i en tabell i ett Hive DDL </td>
<td><p>Om det finns en tillfällig anslutningsfel med Hive metastore databasen när du lägger till en mängd partitioner en Hive-tabell, kan Hive-DDL misslyckas. Följande instruktion isseen i felloggen Hive om det här felet inträffar: </p><p>”FEL [main]: ql. Drivrutinen (SessionState.java:printError(547)) - misslyckades: körningsfel returkoden 1 från org.apache.hadoop.hive.ql.exec.DDLTask. MetaException (message:java.lang.RuntimeException: commitTransaction anropades men openTransactionCalls = 0. Detta indikerar antagligen att det finns Obalanserat anrop till openTransaction/commitTransaction) ”</p></td>
<td>Hive</td>
<td>Hadoop, Hbase</td>
<td>HIVE-482 (detta är ett internt JIRA så inte kan innehålla citattecken externt. Anges här som referens.)</td>
</tr>
<tr>
<td>Buggfix: tillfälliga låser sig i konsolen för HDInsight-fråga</td>
<td>När detta inträffar kan du se följande instruktion i WebHCat loggen för WebHCat starta jobbet: <p>”org.apache.hive.hcatalog.templeton.CatchallExceptionMapper | org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.yarn.exceptions.YarnRuntimeException): kunde inte läsa in historikfil {wasb url på historikfilen} ”</p></td>
<td>WebHCat</td>
<td>Hadoop</td>
<td>HIVE-482 (detta är ett internt JIRA så inte kan innehålla citattecken externt. Anges här som referens.)</td>
</tr>
<tr>
<td>Buggfix: tillfällig topp i antal svarstiden för Hbase frågor</td>
<td>Om detta händer ser användarna en tillfällig topp på 3 sekunder i svarstiden för Hbase frågor. </td>
<td>HDInsight-kluster-Gateway</td>
<td>HBase</td>
<td>Saknas</td>
</tr>
<tr>
<td>HDP JAR-filen namnändringar</td>
<td>HDI kluster, version 3.0, det finns några ändringar i de interna JAR-filer som installerats med HDP. jetty-6.1.26.jar har ersatts med jetty 6.1.26.hwx.jar. jetty-util-6.1.26.jar har ersatts med jetty-util-6.1.26.hwx.jar. Ändringarna tillämpas på Hadoop, Mahout, WebHCat och Oozie projekt.</td>
<td>Hadoop, Mahout, WebHCat, Oozie</td>
<td>Hadoop, HBase</td>
<td>Saknas</td>
</tr>
</table>

## <a name="notes-for-11212014-release-of-hdinsight"></a>Information om 2014-11-21 versionen av HDInsight
Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen är:

* HDInsight 2.1.9.382.1169709 (ingen ändring från 2014-11-14)
* HDInsight 3.0.5.382.1169709 (ingen ändring från 2014-11-14)
* HDInsight 3.1.1.382.1169709 (ingen ändring från 2014-11-14)
* HDInsight SDK 1.4.0

Den här versionen innehåller Komponentuppdateringar för följande:

<table border="1">
<tr><th>Rubrik</th><th>Beskrivning</th><th>Komponent</th><th>Typ av kluster</th><th>JIRA (om tillämpligt)</th></tr>
<tr>
<td>Åtkomst till programloggar</td>
<td>Möjlighet att programmässigt räkna upp program som har körts på ditt kluster och hämta relevanta programspecifika eller behållaren-specifika loggar för att felsöka problematiska program.</td>
<td>SDK</td>
<td>Hadoop</td>
<td>Saknas</td>
</tr>
<tr>
<td>Möjligheten att ange regionnamn i IHdInsightClient.DeleteCluster </td>
<td>Azure HDInsight SDK ger dig möjlighet att ange ett regionnamn när du använder **DeleteCluster**. Detta hjälper att avblockera kunder som har två resurser med samma namn i olika regioner och det går inte att ta bort någon av dem har.</td>
<td>SDK</td>
<td>Alla</td>
<td>Saknas</td>
</tr>
<tr>
<td>ClusterDetails.DeploymentId</td>
<td>Den **ClusterDetails** objekt returnerar en **DeploymentID** fält som representerar en unik identifierare för klustret. Det är säkert att vara unikt inom klustret skapas försök med samma namn.</td>
<td>SDK</td>
<td>Alla</td>
<td>Saknas</td>
</tr>
</table>

## <a name="notes-for-11142014-release-of-hdinsight"></a>Information om 2014-11-14-versionen av HDInsight

Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen är:

* HDInsight 2.1.9.382.1169709
* HDInsight 3.0.5.382.1169709
* HDInsight 3.1.1.382.1169709

Den här versionen innehåller följande nya funktioner, Komponentuppdateringar och felkorrigeringar.

<table border="1">
<tr><th>Rubrik</th><th>Beskrivning</th><th>Komponent</th><th>Typ av kluster</th><th>JIRA (om tillämpligt)</th></tr>
<tr>
<td>Skriptåtgärder (förhandsgranskning)</td>
<td>Förhandsgranskning av funktionen kluster anpassning som gör det möjligt för ändring av Hadoop-kluster i valfri sätt med hjälp av anpassade skript. Med den här funktionen kan användare experimentera med och distribuera projekt som är tillgängliga från Apache Hadoop-ekosystemet för Azure HDInsight-kluster. Anpassning av funktionen är tillgänglig på alla typer av HDInsight-kluster, inklusive Hadoop, HBase och Storm.</td>
<td>Ny funktion</td>
<td>Alla</td>
<td>Saknas</td>
</tr>
<tr>
<td>Fördefinierade jobb för Azure websites och lagring logga analys</td>
<td>Konsolen HDInsight frågan har en komma igång-galleriet som har stöd för lösningar som fungerar på dina data eller på exempeldata.
<p>**Lösningar som kan användas i dina data**:<br>
Vi har skapat jobben för några av de vanligaste data analysis scenarierna utgångspunkt för att skapa egna lösningar. Du kan använda dina data med jobbet för att se hur det fungerar. När du är klar kan du sedan använda det du har lärt dig att skapa en lösning som modelleras efter fördefinierade jobbet.</p>
<p>**Lösningar som fungerar på exempeldata**:<br>
Lär dig hur du arbetar med HDInsight genom att gå igenom några grundläggande scenarier (till exempel analysera webbloggar och sensordata). Du lär dig hur du använder HDInsight för att analysera dessa data och hur du kan ansluta andra program och tjänster till den här informationen. Visualisera data genom att ansluta till Microsoft Excel ger ett exempel av fördelar med den här metoden.</p></td>
<td>Frågan konsolen</td>
<td>Hadoop</td>
<td>Saknas</td>
</tr>
<tr>
<td>Minne-läcka korrigeringen i Templeton</td>
<td>En minnesläcka i Templeton som påverkar kunder som hade tidskrävande kluster eller skickar 100-tal för jobbet begär en andra åtgärdats. Problemet manifesterad som Templeton 5xx-fel och lösningen var att starta om tjänsten. Lösningen behövs inte längre.</td>
<td>Templeton</td>
<td>Alla</td>
<td>https://issues.apache.org/jira/browse/HADOOP-11248</td>
</tr>
</table>

> [!NOTE]
> Om du vill visa de nya funktionerna som gjorts tillgängliga av klustret anpassning har procedurerna använder skriptåtgärd för att installera Spark och R-moduler i ett kluster dokumenterats. Mer information finns i:

* [Installera och använda 1.0 Spark på HDInsight-kluster](hdinsight-hadoop-spark-install.md)
* [Installera och använda R på HDInsight Hadoop-kluster](hdinsight-hadoop-r-scripts.md)

## <a name="notes-for-11072014-release-of-hdinsight"></a>Information om 2014-11-07-versionen av HDInsight

Fullständig versionsnummer för HDInsight-kluster som distribueras med den här versionen är:

* HDInsight 2.1 2.1.9.374.1153876
* HDInsight 3.0 3.0.5.374.1153876
* HDInsight 3.1 3.1.1.374.1153876

Den här versionen innehåller Komponentuppdateringar för följande:

<table border="1">
<tr><th>Rubrik</th><th>Beskrivning</th><th>Komponent</th><th>Typ av kluster</th><th>JIRA (om tillämpligt)</th></tr>
<tr>
<td>HDP 2.1.7</td>
<td>Den här versionen är baserad på Hortonworks Data Platform (HDP) 2.1.7. Mer information finns i <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html" target="_blank">viktig information för HDP 2.1.7</a>.</td>
<td>HDP</td>
<td>Alla</td>
<td>Saknas</td>
</tr>
<tr>
<td>YARN tidslinjen Server</td>
<td>YARN tidslinjen Server (kallas även allmän historik programservern) har aktiverats som standard. Tidslinjen Server ger allmän information om slutförda program (t.ex program-ID, programnamn, programstatus, sändningstid för programmet och tid för slutförande av programmet).

Information om det här programmet kan hämtas från huvudnod genom att öppna URI http://headnodehost:8188 eller genom att köra kommandot YARN: yarn program – lista - appStates alla.

Den här informationen kan också hämtas från en fjärrdator om en REST-API på https://{ClusterDnsName}. azurehdinsight.NET/ws/v1/applicationhistory/.

Mer information finns i <a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">YARN tidslinjen Server</a>.</td>
<td>Tjänsten, YARN</td>
<td>Hadoop, HBase</td>
<td>Saknas</td>
</tr>
<tr>
<td>ID för kluster-distribution</td>
<td>Från och med SDK version 1.3.3.1.5426.29232 kan komma användare åt ett unikt ID för varje kluster som är utfärdat HDInsight. Detta gör det möjligt för kunder att ta reda på unika instanser av kluster när ett DNS-namn återanvänds tvärs över skapa eller ta bort scenarier.</td>
<td>SDK</td>
<td>Alla</td>
<td>Saknas</td>
</tr>
</table>

> [!NOTE]
> Fel som förhindrade fullständig versionsnumret från visas i portalen eller som returneras av SDK eller med Windows PowerShell har korrigerats i den här versionen.

## <a name="notes-for-10152014-release"></a>Anteckningar för version 2014-10-15

Den här versionen av snabbkorrigeringen fast en minnesläcka i Templeton som påverkade tunga användare av Templeton. I vissa fall skulle användare som utnyttjas Templeton kraftigt se felen som är manifesterad som 500 felkoder eftersom begäran inte skulle ha tillräckligt med minne för att köra. Lösning på problemet är att starta om tjänsten Templeton. Det här problemet har korrigerats.

## <a name="notes-for-1072014-release"></a>Anteckningar för version 2014-10-7

* När du använder Ambari slutpunkt, ”https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}” den *värddatornamn* fältet returnerar fullständigt kvalificerade namnet (FQDN) för noden i stället för bara värdnamnet. Till exempel i stället för att returnera ”**headnode0**”, får du det fullständiga Domännamnet ”**headnode0. { ClusterDNS}. azurehdinsight.NET .net**”. Den här ändringen krävdes för att underlätta scenarier där flera klustertyper (till exempel HBase och Hadoop) kan distribueras i ett virtuellt nätverk. Detta inträffar exempelvis när du använder HBase som en backend-plattform för Hadoop.

* Vi har samlat nya minnesinställningarna för standarddistribution av HDInsight-klustret. Tidigare minne standardinställningarna inte rätt hänsyn till vägledning för antalet processorkärnor som distribueras. De nya minnesinställningarna bör ge bättre standardinställningar (enligt Hortonworks rekommendationer). Om du vill ändra dessa finns i SDK referensdokumentationen om hur du ändrar klusterkonfigurationen. De nya minnesinställningarna för som används av HDInsight-standardkluster 4 CPU core (8 behållaren) specificerade i följande tabell. (De värden som används innan den här versionen finns också parenthetically.)

<table border="1">
<tr><th>Komponent</th><th>Minnesallokering</th></tr>
<tr><td> allokering av yarn.Scheduler.minimum</td><td>768 MB (tidigare 512 MB)</td></tr>
<tr><td> allokering av yarn.Scheduler.maximum</td><td>6144 MB (unchanged)</td></tr>
<tr><td>yarn.nodemanager.Resource.Memory</td><td>6144 MB (unchanged)</td></tr>
<tr><td>Mapreduce.Map.Memory</td><td>768 MB (tidigare 512 MB)</td></tr>
<tr><td>mapreduce.Map.Java.opts</td><td>väljer =-Xmx512m (tidigare - Xmx410m)</td></tr>
<tr><td>Mapreduce.Reduce.Memory</td><td>1536 MB (tidigare 1 024 MB)</td></tr>
<tr><td>mapreduce.Reduce.Java.opts</td><td>väljer =-Xmx1024m (tidigare - Xmx819m)</td></tr>
<tr><td>yarn.App.mapreduce.am.Resource</td><td>768 MB (tidigare 1 024 MB)</td></tr>
<tr><td>yarn.App.mapreduce.am.Command</td><td>väljer =-Xmx512m (tidigare - Xmx819m)</td></tr>
<tr><td>mapreduce.Task.IO.sort</td><td>256 MB (tidigare 200 MB)</td></tr>
<tr><td>tez.am.Resource.Memory</td><td>1536 MB (unchanged)</td></tr>
</table>

Mer information om konfigurationsinställningarna för minne som används av YARN och MapReduce på plattformen Hortonworks Data som används av HDInsight finns [fastställa konfigurationsinställningarna för HDP minne](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1-latest/bk_installing_manually_book/content/rpm-chap1-11.html). Hortonworks tillhandahålls också ett verktyg för att beräkna rätt minnesinställningarna.

Om Azure PowerShell och HDInsight SDK-felmeddelande ”:*klustret har inte konfigurerats för HTTP-tjänster åtkomst*”:

* Det här felet är en känd [kompatibilitetsproblem](https://social.msdn.microsoft.com/Forums/azure/a7de016d-8de1-4385-b89e-d2e7a1a9d927/hdinsight-powershellsdk-error-cluster-is-not-configured-for-http-services-access?forum=hdinsight) som kan uppstå på grund av skillnader i HDInsight SDK eller Azure PowerShell-versionen och versionen av klustret. Kluster som skapas på 15/8 eller senare har stöd för etablering till ny funktion i virtuella nätverk. Men den här funktionen tolkas inte korrekt av äldre versioner av Azure PowerShell eller HDInsight SDK. Resultatet är ett fel i vissa åtgärder för överföring av jobbet. Om du använder API: er för HDInsight SDK eller Azure PowerShell-cmdlets (**Använd AzureRmHDInsightCluster** eller **Invoke-AzureRmHDInsightHiveJob**) för att skicka jobb, dessa åtgärder kan misslyckas med felmeddelandet ” *Klustret <clustername> har inte konfigurerats för HTTP-tjänster åtkomst*”. Eller (beroende på operation), kan du hämta andra felmeddelanden som ”*kan inte ansluta till klustret*”.
* Dessa kompatibilitetsproblem matchas i de senaste versionerna av HDInsight SDK och Azure PowerShell. Vi rekommenderar att uppdatera HDInsight SDK till version 1.3.1.6 eller senare och Azure PowerShell-verktyg för version 0.8.8 eller senare. Du kan få åtkomst till den senaste HDInsight SDK från [NugGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) och Azure PowerShell verktygen på [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

## <a name="notes-for-9122014-release-of-hdinsight-31"></a>Anteckningar för version 2014-12-9 HDInsight 3.1
* Den här versionen är baserad på Hortonworks Data Platform (HDP) 2.1.5. En lista över programfel som har korrigerats i den här versionen finns i [fast i den här versionen](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.5/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.5-fixed.html) Hortonworks-webbplats.
* I mappen Pig bibliotek har filen ”avro-mapred-1.7.4.jar” ändrats till ”avro-mapred-1.7.4-hadoop2.jar”. Innehållet i den här filen innehåller en mindre felkorrigering är hårt. Du rekommenderas att kunder inte göra ett direkt beroende på namnet på JAR-filen för att undvika avbrott när filer ändras.

## <a name="notes-for-8212014-release"></a>Anteckningar för version 2014-8-21
* Vi lägger till följande WebHCat konfiguration (HIVE-7155) som anger standardminnesgräns för Templeton domänkontrollant jobb till 1 GB. (Tidigare var standardvärdet 512 MB.)

     templeton.Mapper.Memory.MB (= 1024)

  * Den här ändringen löser följande fel med vissa Hive-frågor på grund av lägre minnesgränserna: ”behållare körs utanför gränserna för fysiskt minne”.
  * Om du vill återgå till de gamla standardinställningarna kan du ange Konfigurationsvärdet till 512 via Azure PowerShell vid tidpunkten för skapandet av klustret med hjälp av följande kommando:

      Lägg till AzureRmHDInsightConfigValues-Core @{”templeton.mapper.memory.mb"="512”;}
* Namnet på rollen zookeeper ändrades till *zookeeper*. Detta påverkar namnmatchning i klustret, men det påverkar inte externa REST API: er. Om du har komponenter som använder den *zookeepernode* värdnamn, måste du uppdatera dem för att använda nya namn. De nya namnen på de tre zookeeper-noderna är:

  * zookeeper0
  * zookeeper1
  * zookeeper2
* HBase version stöd matrix uppdateras. Endast HDInsight version 3.1 (HBase version 0,98) stöds för produktion för HBase-arbetsbelastningar. Version 3.0 (som var tillgängliga för förhandsgranskning) stöds inte flytta framåt.

## <a name="notes-about-clusters-created-prior-to-8152014"></a>Information om kluster som skapades före 2014-8-15
Felmeddelandet Azure PowerShell eller HDInsight SDK ”klustret <clustername> har inte konfigurerats för HTTP-tjänster åtkomst” (eller beroende på operation, andra felmeddelanden som: ”Det går inte att ansluta till klustret”) kan uppstå på grund av en versionsskillnader mellan Azure PowerShell eller HDInsight SDK och ett kluster. Kluster som skapas på 15/8 eller senare har stöd för etablering till ny funktion i virtuella nätverk. Den här funktionen är inte korrekt tolkas av äldre versioner av Azure PowerShell eller HDInsight SDK, vilket resulterar i misslyckade jobb skicka åtgärder. Om du använder API: er för HDInsight SDK eller Azure PowerShell-cmdlets (till exempel använda AzureRmHDInsightCluster eller Invoke-AzureRmHDInsightHiveJob) att skicka jobb misslyckas dessa åtgärder med något av felmeddelandena som beskrivs.

Dessa kompatibilitetsproblem matchas i de senaste versionerna av HDInsight SDK och Azure PowerShell. Vi rekommenderar att uppdatera HDInsight SDK till version 1.3.1.6 eller senare och Azure PowerShell-verktyg för version 0.8.8 eller senare. Du kan få åtkomst till den senaste HDInsight SDK från [NuGet][nuget-link]. Du kan komma åt verktygen Azure PowerShell med hjälp av [Microsoft Web Platform Installer][webpi-link].

## <a name="notes-for-7282014-release"></a>Anteckningar för version 2014-7-28
* **HDInsight som är tillgängliga i nya regioner**: vi utökat HDInsight geografiska förekomst till tre regioner. HDInsight-kunder kan skapa kluster i dessa områden:
  * Östasien
  * Norra centrala USA
  * Södra centrala USA
* HDInsight version 1.6 (HDP 1.1 och Hadoop 1.0.3) och HDInsight version 2.1 (HDP1.3 och Hadoop 1.2) tas bort från Azure-portalen. Du kan fortsätta att skapa Hadoop-kluster för dessa versioner med hjälp av Azure PowerShell-cmdleten [ny AzureRmHDInsightCluster](http://msdn.microsoft.com/library/dn593744.aspx) eller genom att använda den [HDInsight SDK](http://msdn.microsoft.com/library/azure/dn469975.aspx). Mer information finns i [HDInsight component-versioning](hdinsight-component-versioning.md).
* Hortonworks Data Platform (HDP) ändringar i den här versionen:

<table border="1">
<tr><th>HDP</th><th>Ändringar</th></tr>
<tr><td>HDP 1.3 / HDI 2.1</td><td>Inga ändringar</td></tr>
<tr><td>HDP 2.0 / HDI 3.0</td><td>Inga ändringar</td></tr>
<tr><td>HDP 2.1 / HDI 3.1</td><td>zookeeper: [3.4.5.2.1.3.0-1948] -> [3.4.5.2.1.3.2-0002]</td></tr>
</table>

## <a name="notes-for-6242014-release"></a>Anteckningar för version 2014-6-24
Den här versionen innehåller förbättringar av HDInsight-tjänsten:

* **HDP 2.1 tillgänglighet**: HDInsight 3.1 (som innehåller HDP 2.1) är allmänt tillgänglig och är standardversionen som för nya kluster.
* **HBase - Azure portalen förbättringar**: vi gör HBase-kluster tillgängliga i förhandsversionen. Du kan skapa HBase-kluster från portalen med bara några klickningar.

Med HBase, kan du skapa en mängd olika realtid arbetsbelastningar på HDInsight från interaktiva webbplatser som fungerar med stora datauppsättningar till tjänster som lagrar sensor- och telemetridata data från miljontals slutpunkter. Nästa steg är att analysera data i dessa arbetsbelastningar med Hadoop-jobb och det är möjligt i HDInsight via Azure PowerShell och instrumentpanelen för Hive-klustret.

### <a name="apache-mahout-preinstalled-on-hdinsight-31"></a>Apache Mahout förinstallerat på HDInsight 3.1
 [Mahout](http://hortonworks.com/hadoop/mahout/) har förinstallerats HDInsight 3.1 Hadoop-kluster, så att du kan köra Mahout jobb utan att behöva ytterligare klusterkonfigurationen. Till exempel kan du fjärråtkomst i ett Hadoop-kluster med hjälp av Remote Desktop Protocol (RDP) och utföra ytterligare steg du kan köra kommandot Hello World Mahout:

        mahout org.apache.mahout.classifier.df.tools.Describe -p /user/hdp/glass.data -f /user/hdp/glass.info -d I 9 N L

        mahout org.apache.mahout.classifier.df.BreimanExample -d /user/hdp/glass.data -ds /user/hdp/glass.info -i 10 -t 100

En fullständig förklaring av den här proceduren finns i dokumentationen för den [Breiman exempel](https://mahout.apache.org/users/classification/breiman-example.html) på webbplatsen Apache Mahout.

### <a name="hive-queries-can-use-tez-in-hdinsight-31"></a>Hive-frågor kan använda Tez i HDInsight 3.1
Hive 0,13 är tillgänglig i HDInsight 3.1, och du kan köra frågor med Tez som kan anpassas för betydande prestandaförbättringar.
Tez är inte aktiverad som standard för Hive-frågor. Du måste välja i för att använda den. Du kan aktivera Tez genom att köra följande kodavsnitt:

        set hive.execution.engine=tez;
        select sc_status, count(*), histogram_numeric(sc_bytes,5) from website_logs_orc_local group by sc_status;

Hortonworks har publicerat en detaljerad analys av Hive-fråga prestandaförbättringarna Tez som levereras i prestandatester. Mer information finns i [prestandamätningar Apache Hive 13 för Enterprise Hadoop](http://hortonworks.com/blog/benchmarking-apache-hive-13-enterprise-hadoop/).

Mer information finns i [Hive i Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez).

### <a name="global-availability"></a>Globala tillgänglighet
Med lanseringen av HDInsight på Hadoop 2.2 har Microsoft gjort HDInsight tillgänglig i alla större geografiska områden där Azure är tillgängligt. Mer specifikt förts västra Europa och Sydostasien Datacenter in online. Detta gör det möjligt för kunder att hitta kluster i ett datacenter som är nära och eventuellt en zon med liknande krav på efterlevnad.

### <a name="dos--donts-between-cluster-versions"></a>DOS & tänka på mellan kluster-versioner
**Oozie metastores används med ett kluster i HDInsight 3.1 inte är bakåtkompatibla med 2.1 HDInsight-kluster och de kan inte användas med den här tidigare version**.

En anpassad Oozie metastore databas som distribueras med ett kluster i HDInsight 3.1 kan inte återanvändas med 2.1 HDInsight-kluster. Detta gäller även om metastore ursprung med 2.1 HDInsight-kluster. Det här scenariot stöds inte eftersom schemat metastore hämtar uppgraderas när den används med ett kluster i HDInsight 3.1, så att den inte längre är kompatibel med metastore som krävs av 2.1 HDInsight-kluster. Alla försök att återanvända en Oozie metastore som har använts med ett kluster i HDInsight 3.1 återge 2.1 HDInsight-klustret oanvändbar.

**Oozie metastores kan inte delas mellan kluster.**

Oozie metastores är kopplade till specifika kluster och de kan inte delas mellan kluster.

### <a name="breaking-changes"></a>Gör ändringar
**Prefixet syntax**: endast den ”wasb: / /” syntax stöds i HDInsight 3.1 och 3.0-kluster. Den äldre ”asv: / /” syntax stöds i HDInsight 2.1 och 1.6 kluster, men stöds inte i HDInsight 3.1 eller 3.0-kluster. Det innebär att alla jobb som skickats till ett HDInsight-3.1 eller 3.0 som uttryckligen klustret använder den ”asv: / /” syntaxen misslyckas. Den ”wasb: / /” syntax som ska användas i stället. Även jobb som har skickats till alla HDInsight-3.1 eller 3.0 kluster som skapas med en befintlig metastore som innehåller explicita referenser till resurser med hjälp av den ”asv: / /” syntaxen misslyckas. Dessa metastores behöver skapas på nytt med hjälp av den ”wasb: / /” syntax för att adressera resurser.

**Portar**: portar som används av HDInsight-tjänst har ändrats. De portnummer som användes var inom det tillfälliga portintervallet för Windows-operativsystemet. Automatiskt tilldelas portar från ett fördefinierat tillfälliga intervall för tillfällig Internet protocol-kommunikation. Den nya uppsättningen tillåtna Hortonworks Data Platform (HDP) tjänsten portnummer är utanför intervallet för att undvika konflikter som kan uppstå med portar som används av tjänster som körs på huvudnoden. De nya portnumren bör inte göra några viktiga förändringar för. De tal som används är följande:

 **HDInsight 1.6 (HDP 1.1)**

<table border="1">
<tr><th>Namn</th><th>Värde</th></tr>
<tr><td>DFS.http.address</td><td>namenodehost:30070</td></tr>
<tr><td>DFS.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>DFS.datanode.http.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>DFS.datanode.IPC.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>DFS.Secondary.http.address</td><td>0.0.0.0:30090</td></tr>
<tr><td>mapred.Job.Tracker.http.address</td><td>jobtrackerhost:30030</td></tr>
<tr><td>mapred.Task.Tracker.http.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>mapreduce.history.Server.http.address</td><td>0.0.0.0:31111</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table>

 **HDInsight 3.1 och 3.0 (HDP 2.1 och 2.0)**

<table border="1">
<tr><th>Namn</th><th>Värde</th></tr>
<tr><td>DFS.namenode.HTTP-adress</td><td>namenodehost:30070</td></tr>
<tr><td>DFS.namenode.https-adress</td><td>headnodehost:30470</td></tr>
<tr><td>DFS.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>DFS.datanode.http.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>DFS.datanode.IPC.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>DFS.namenode.Secondary.HTTP-adress</td><td>0.0.0.0:30090</td></tr>
<tr><td>yarn.nodemanager.WebApp.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table>

### <a name="dependencies"></a>Beroenden
Följande beroenden har lagts till i HDInsight 3.x (HDP2.x):

* guice servlet
* optiq kärnor
* javax.inject
* Aktivering
* jsr305
* geronimo-jaspic_1.0_spec
* jul till slf4j
* Java-xmlbuilder
* Ant
* Commons-kompilatorn
* jdo-api
* Commons math3
* paranamer
* jaxb impl
* stringtemplate
* eigenbase xom
* Jersey servlet
* Commons exec
* jaxb-api
* jetty-alla-server
* janino
* xercesImpl
* optiq avatica
* jta
* eigenbase-egenskaper
* groovy alla
* hamcrest kärnor
* E-post
* linq4j
* jpam
* Jersey-klient
* aopalliance
* geronimo-annotation_1.0_spec
* Starta Ant
* Jersey guice
* XML-API: er
* stax-api
* Asm-commons
* Asm-trädet
* wadl
* geronimo-jta_1.1_spec
* guice
* leveldbjni alla
* hastighet
* att
* snygga java
* jetty-all
* Commons dbcp

Följande beroenden som inte längre finns i HDInsight 3.x (HDP2.x):

* jdeb
* kfs
* sqlline
* murgröna
* aspectjrt
* JSON
* kärna
* jdo2-api
* avro-mapred
* datanucleus enhancer
* JSP
* Commons-loggning-api
* Commons math
* JavaEWAH
* aspectjtools
* javolution
* hdfsproxy
* hbase
* snygga

### <a name="version-changes"></a>Version ändringar
Följande version ändringar har gjorts mellan HDInsight 2.x (HDP1.x) och HDInsight 3.x (HDP2.x):

* mått kärnor: [2.1.2] -> [3.0.0]
* derbynet: [10.4.2.0] -> [10.10.1.1]
* datanucleus: ['rdbms-3.0.8'] -> ['rdbms-3.2.9']
* jasper-kompilatorn: [5.5.12] -> [5.5.23]
* log4j: ['1.2.15', '1.2.16'] -> ['1.2.16', '1.2.17']
* derbyclient: [10.4.2.0] -> [10.10.1.1]
* httpcore: [4.2.4] -> [4.2.5]
* hsqldb: [1.8.0.10] -> [2.0.0]
* jets3t: [0.6.1] -> [0.9.0]
* protobuf java: [2.4.1] -> [2.5.0]
* ett exempel: [10.4.2.0] -> [10.10.1.1]
* jasper: ['runtime-5.5.12'] -> ['runtime-5.5.23']
* daemon för Commons: [1.0.1] -> [1.0.13]
* datanucleus kärnor: [3.0.9] -> [3.2.10]
* datanucleus-api-jdo: [3.0.7] -> [3.2.6]
* zookeeper: [3.4.5.1.3.9.0-01320] -> [3.4.5.2.1.3.0-1948]
* bonecp: [0.7.1.RELEASE] -> ['
* 0.8.0.RELEASE']

### <a name="drivers"></a>Drivrutiner
Java JDBC (Database Connectivity) drivrutin för SQL Server används internt av HDInsight och används inte för externa operationer. Om du vill ansluta till HDInsight med hjälp av öppna ODBC (Database Connectivity) använder du Microsoft ODBC-drivrutinen. Mer information finns i [ansluta Excel till HDInsight med ODBC-drivrutinen för Microsoft Hive](hdinsight-connect-excel-hive-odbc-driver.md).

### <a name="bug-fixes"></a>Felkorrigeringar
Med den här versionen har vi uppdateras följande HDInsight-versioner med flera felkorrigeringar:

* HDInsight 2.1 (HDP 1.3)
* HDInsight 3.0 (HDP 2.0)
* HDInsight 3.1 (HDP 2.1)

## <a name="hortonworks-release-notes"></a>Hortonworks viktig information
Viktig information för Hortonworks Data plattformar (HDPs) som används av HDInsight operativsystemsversioner är tillgängliga på följande platser:

* HDInsight version 3.1 använder ett Hadoop-distributionen som baseras på den [Hortonworks Data Platform 2.1.7][hdp-2-1-7]. Detta är det standard Hadoop-kluster som skapas när du använder Azure portal efter 2014-11-7. 3.1 HDInsight-kluster som skapas innan 2014-11-7 baserades på den [Hortonworks Data Platform 2.1.1][hdp-2-1-1]
* HDInsight version 3.0 använder ett Hadoop-distributionen som baseras på den [Hortonworks Data Platform 2.0][hdp-2-0-8].
* HDInsight version 2.1 använder ett Hadoop-distributionen som baseras på den [Hortonworks Data Platform 1.3][hdp-1-3-0].
* HDInsight version 1.6 använder ett Hadoop-distributionen som baseras på den [Hortonworks Data Platform 1.1][hdp-1-1-0].

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[nuget-link]: https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.HDInsight/

[webpi-link]: http://go.microsoft.com/?linkid=9811175&clcid=0x409

[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-r-scripts]: ../hdinsight-hadoop-r-scripts/
