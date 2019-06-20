---
title: Vad är interaktiv fråga i Azure HDInsight?
description: En introduktion till interaktiv fråga i Azure HDInsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: overview
ms.date: 06/14/2019
ms.openlocfilehash: ea17ddeab21c371f41cc57115df4dd91277c3c42
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67151203"
---
# <a name="what-is-interactive-query-in-azure-hdinsight"></a>Vad är interaktiv fråga i Azure HDInsight

Interaktiv fråga (kallas även Apache Hive-LLAP eller [med låg latens Analytical Processing](https://cwiki.apache.org/confluence/display/Hive/LLAP)) är ett Azure HDInsight [klustertyp](../hdinsight-hadoop-provision-linux-clusters.md#cluster-types). Interaktiv fråga har stöd för cachelagring i minnet, vilket gör Apache Hive-frågor snabbare och mycket mer interaktivt. Kunder använda interaktiv fråga för att köra frågor mot data som lagras i Azure storage och Azure Data Lake Storage på Supersnabb sätt. Interaktiv fråga gör det enkelt för utvecklare och alla användare arbeta med big data med BI-verktyg som de gillar mest. Interaktiv HDInsight-fråga har stöd för flera verktyg för att komma åt big data på enkelt sätt.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Ett interaktivt frågekluster skiljer sig från ett Apache Hadoop-kluster. Den innehåller endast Hive-tjänsten.

Du kan komma åt tjänsten Hive i klustret för interaktiv fråga endast via Apache Ambari Hive-vy, Beeline och Microsoft Hive Open Database Connectivity-drivrutinen (Hive ODBC). Du kan inte komma åt den via Hive-konsolen, Templeton, klassiska Azure-CLI eller Azure PowerShell.

## <a name="create-an-interactive-query-cluster"></a>Skapa ett interaktivt frågekluster

Information om hur du skapar ett HDInsight-kluster finns i [skapa Apache Hadoop-kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Välj klustertyp interaktiv fråga.

## <a name="execute-apache-hive-queries-from-interactive-query"></a>Kör Apache Hive-frågor från interaktiv fråga

För att köra Hive-frågor, har du följande alternativ:

* Use Microsoft Power BI

    Se [visualisera Interactive Query Apache Hive-data med Power BI i Azure HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) Se [visualisera big data med Power BI i Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).

* Använd Visual Studio

    Se [Anslut till Azure HDInsight och köra Apache Hive-frågor med Data Lake Tools för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries).

* Använd Visual Studio Code

    Se [använda Visual Studio Code för Apache Hive, LLAP eller pySpark](../hdinsight-for-vscode.md).
* Kör Apache Hive med hjälp av Apache Ambari Hive-vy.
  
    Se [använda Apache Hive-vyn med Apache Hadoop i Azure HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md).

* Kör Apache Hive genom att använda Beeline.
  
    Se [använda Apache Hive med Apache Hadoop i HDInsight med Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).
  
    Du kan använda Beeline från antingen huvudnoden eller från en tom edge-nod. Vi rekommenderar att du använder Beeline från en tom edge-nod. Information om hur du skapar ett HDInsight-kluster med hjälp av en tom edge-nod finns i [använda tomma kantnoder i HDInsight](../hdinsight-apps-use-edge-node.md).
* Kör Apache Hive med Hive ODBC.
  
    Se [Anslut Excel till Apache Hadoop med Microsoft Hive ODBC-drivrutinen](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

Hitta anslutningssträngen Java Database Connectivity (JDBC):

1. Logga in på Apache Ambari med hjälp av följande URL: `https://<cluster name>.AzureHDInsight.net`.
2. I den vänstra menyn väljer du **Hive**.
3. Välj ikonen Urklipp för att kopiera URL: en:

   ![HDInsight Hadoop Interactive Query LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapa interaktiva frågekluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Lär dig hur du [visualisera big data med Power BI i Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Lär dig hur du [använder Apache Zeppelin för att köra Apache Hive-frågor i Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
