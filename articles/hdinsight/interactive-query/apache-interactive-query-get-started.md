---
title: Vad är interaktiv fråga i Azure HDInsight?
description: En introduktion till interaktiv fråga, även kallad Apache Hive LLAP, i Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive
ms.date: 03/03/2020
ms.openlocfilehash: e133e08e333cb478269a93cce963566e195d6949
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78271959"
---
# <a name="what-is-interactive-query-in-azure-hdinsight"></a>Vad är interaktiv fråga i Azure HDInsight

Interaktiv fråga (kallas även Apache Hive LLAP eller [Analytisk bearbetning med låg latens)](https://cwiki.apache.org/confluence/display/Hive/LLAP)är en Azure HDInsight-klustertyp . [cluster type](../hdinsight-hadoop-provision-linux-clusters.md#cluster-type) Interactive Query stöder cachelagring i minnet, vilket gör Apache Hive-frågor snabbare och mycket mer interaktiva. Kunder använder interaktiv fråga för att fråga data som lagras i Azure-lagring & Azure Data Lake Storage på ett supersnabbt sätt. Interaktiv fråga gör det enkelt för utvecklare och datavetare att arbeta med stordata med bi-verktyg som de älskar mest. HDInsight Interactive Query stöder flera verktyg för att komma åt stordata på ett enkelt sätt.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Ett interaktivt frågekluster skiljer sig från ett Apache Hadoop-kluster. Den innehåller bara Hive-tjänsten.

Du kan bara komma åt Hive-tjänsten i det interaktiva frågeklustret via Apache Ambari Hive View, Beeline och Microsoft Hive Open Database Connectivity driver (Hive ODBC). Du kan inte komma åt den via Hive-konsolen, Templeton, Azure Classic CLI eller Azure PowerShell.

## <a name="create-an-interactive-query-cluster"></a>Skapa ett interaktivt frågekluster

Information om hur du skapar ett HDInsight-kluster finns i [Skapa Apache Hadoop-kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Välj klustertypen Interaktiv fråga.

> [!IMPORTANT]
> Minsta huvudnodsstorlek för interaktiva frågekluster är Standard_D13_v2. Mer information finns i [storlekstabellen](../../cloud-services/cloud-services-sizes-specs.md#dv2-series)för Azure VM.

## <a name="execute-apache-hive-queries-from-interactive-query"></a>Köra Apache Hive-frågor från interaktiv fråga

Om du vill köra Hive-frågor har du följande alternativ:

|Metod |Beskrivning |
|---|---|
|Microsoft Power BI|Se [Visualisera interaktiva Apache Hive-data med Power BI i Azure HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md)och [Visualisera stordata med Power BI i Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).|
|Visual Studio|Se [Anslut till Azure HDInsight och kör Apache Hive-frågor med DataSjöverktyg för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries).|
|Visual Studio-koden|Se [Använda Visual Studio-kod för Apache Hive, LLAP eller pySpark](../hdinsight-for-vscode.md).|
|Apache Ambari Hive Visa|Se [Använda Apache Hive View med Apache Hadoop i Azure HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md). Hive View är inte tillgängligt för HDInsight 4.0.|
|Apache Beeline|Se [Använd Apache Hive med Apache Hadoop i HDInsight med Beeline](../hadoop/apache-hadoop-use-hive-beeline.md). Du kan använda Beeline från antingen huvudnoden eller från en tom kantnod. Vi rekommenderar att du använder Beeline från en tom kantnod. Information om hur du skapar ett HDInsight-kluster med hjälp av en nod för tom kant finns [i Använda tomma kantnoder i HDInsight](../hdinsight-apps-use-edge-node.md).|
|Hive ODBC|Se [Anslut Excel till Apache Hadoop med Microsoft Hive ODBC-drivrutinen](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).|

Så här hittar du anslutningssträngen för Java-databasanslutning (JDBC):

1. Från en webbläsare navigerar du till `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary`, var `CLUSTERNAME` är namnet på klustret.
1. Om du vill kopiera WEBBADRESSen markerar du ikonen urklipp:

   ![HDInsight Hadoop interaktiv fråga LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar interaktiva frågekluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Lär dig hur du [visualiserar stordata med Power BI i Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Lär dig hur du [använder Apache Zeppelin för att köra Apache Hive-frågor i Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
