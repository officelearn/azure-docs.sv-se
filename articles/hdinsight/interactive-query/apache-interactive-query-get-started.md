---
title: Vad är en interaktiv fråga i Azure HDInsight?
description: En introduktion till interaktiv fråga, även kallat Apache Hive LLAP, i Azure HDInsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: overview
ms.date: 09/17/2019
ms.openlocfilehash: f03797a8c7df1609a32f934bc090c7adc899aa9a
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198930"
---
# <a name="what-is-interactive-query-in-azure-hdinsight"></a>Vad är interaktiv fråga i Azure HDInsight?

Interaktiv fråga (kallas även Apache Hive LLAP eller [analytisk bearbetning av låg latens](https://cwiki.apache.org/confluence/display/Hive/LLAP)) är en [kluster typ](../hdinsight-hadoop-provision-linux-clusters.md#cluster-type)för Azure HDInsight. Interaktiv fråga stöder cachelagring i minnet, vilket gör Apache Hive frågor snabbare och mycket mer interaktivt. Kunder använder interaktiv fråga för att fråga data som lagras i Azure Storage & Azure Data Lake Storage på ett snabbt sätt. Interaktiv fråga gör det enkelt för utvecklare och data expert att arbeta med stora data med BI-verktyg som de älskar mest. En interaktiv HDInsight-fråga stöder flera verktyg för att få åtkomst till Big data på ett enkelt sätt.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Ett interaktivt Query-kluster skiljer sig från ett Apache Hadoop kluster. Den innehåller bara Hive-tjänsten.

Du kan bara komma åt Hive-tjänsten i det interaktiva fråge klustret via Apache Ambari Hive-vyn, Beeline och Microsoft Hive Open Database Connectivity-drivrutinen (Hive ODBC). Du kan inte komma åt den via Hive-konsolen, Templeton, den klassiska Azure-CLI: en eller Azure PowerShell.

## <a name="create-an-interactive-query-cluster"></a>Skapa ett interaktivt Query-kluster

Information om hur du skapar ett HDInsight-kluster finns i [skapa Apache Hadoop kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Välj kluster typen interaktiv fråga.

> [!IMPORTANT]
> Den minsta huvudnoden storleken för interaktiva fråge kluster är Standard_D13_v2. Mer information finns i [diagrammet storleks schema för virtuell Azure-dator](../../cloud-services/cloud-services-sizes-specs.md#dv2-series).

## <a name="execute-apache-hive-queries-from-interactive-query"></a>Köra Apache Hive frågor från interaktiv fråga

Om du vill köra Hive-frågor kan du välja mellan följande alternativ:

* Använd Microsoft Power BI

    Se [visualisera interaktiva frågor Apache Hive data med Power BI i Azure HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) se [visualisera big data med Power BI i Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).

* Använda Visual Studio

    Se [ansluta till Azure HDInsight och köra apache Hive frågor med hjälp av data Lake verktyg för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries).

* Använda Visual Studio Code

    Se [använda Visual Studio Code för Apache Hive, LLAP eller pySpark](../hdinsight-for-vscode.md).
* Kör Apache Hive med hjälp av vyn Apache Ambari Hive.
  
    Se [använda Apache Hive vy med Apache Hadoop i Azure HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md).

* Kör Apache Hive med hjälp av Beeline.
  
    Se [använda Apache Hive med Apache Hadoop i HDInsight med Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).
  
    Du kan använda Beeline antingen från Head-noden eller från en tom Edge-nod. Vi rekommenderar att du använder Beeline från en tom Edge-nod. Information om hur du skapar ett HDInsight-kluster med hjälp av en tom Edge-nod finns i [använda tomma Edge-noder i HDInsight](../hdinsight-apps-use-edge-node.md).
* Kör Apache Hive med hjälp av Hive ODBC.
  
    Se [ansluta Excel till Apache Hadoop med Microsoft HIVE ODBC-drivrutin](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

Så här hittar du anslutnings strängen för Java Database Connectivity (JDBC):

1. Logga in på Apache Ambari genom att använda följande URL: `https://<cluster name>.AzureHDInsight.net`.
2. På den vänstra menyn väljer du **Hive**.
3. Om du vill kopiera URL: en väljer du urklipps ikonen:

   ![HDInsight Hadoop Interactive Query LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar interaktiva Query-kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Lär dig hur du kan [visualisera Big data med Power BI i Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Lär dig hur du [använder Apache Zeppelin för att köra apache Hive frågor i Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
