---
title: Använda Apache Zeppelin för att köra Apache Hive-frågor i Azure HDInsight
description: Lär dig hur du använder Apache Zeppelin du kör Apache Hive-frågor.
keywords: hdinsight, hadoop, hive, LLAP interaktiv fråga
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: hrasheed
ms.openlocfilehash: 035e70eef88d5d5dae08c329017430db25c20464
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52494832"
---
# <a name="use-apache-zeppelin-to-run-apache-hive-queries-in-azure-hdinsight"></a>Använda Apache Zeppelin för att köra Apache Hive-frågor i Azure HDInsight 

Interaktiv HDInsight-fråga innehåller [Apache Zeppelin](https://zeppelin.apache.org/) anteckningsböcker som du kan använda för att köra interaktiva Hive-frågor. I den här artikeln får du lära dig hur du använder Apache Zeppelin för att köra [Apache Hive](https://hive.apache.org/) frågor i Azure HDInsight. 

## <a name="prerequisites"></a>Förutsättningar
Innan du går igenom den här artikeln, måste du ha följande objekt:

* **Klustret HDInsight interaktiv fråga**. Se [Skapa kluster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) att skapa ett HDInsight-kluster.  Se till att välja typen interaktiv fråga. 

## <a name="create-an-apache-zeppelin-note"></a>Skapa ett Apache Zeppelin-anteckning

1. Bläddra till följande URL:

        https://CLUSTERNAME.azurehdinsight.net/zeppelin
    Ersätt **CLUSTERNAME** med namnet på klustret.

2. Ange ditt Hadoop-användarnamn och lösenord. Zeppelin-sidan kan du skapa en ny anteckning eller öppna befintliga anteckningar. HiveSample innehåller vissa Hive-exempelfrågor.  

    ![Interaktiv HDInsight-fråga zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)
3. Klicka på **Skapa ny anteckning**.
4. Ange eller välj följande värden:

    - Obs namn: Ange ett namn för kommentaren.
    - Standard-tolk: Välj **JDBC**.

5. Klicka på **skapa anteckning**.
6. Kör följande Hive-fråga:

        %jdbc(hive)
        show tables

    ![Interaktiv HDInsight-fråga zeppelin Kör fråga](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    Den **%jdbc(hive)** instruktion i den första raden anger anteckningsboken för att använda Hive JDBC-tolk.

    Frågan bör returnera en Hive-tabell som kallas *hivesampletable*.

    Följande är två mer Hive-frågor som du kan köra mot hivesampletable. 

        %jdbc(hive)
        select * from hivesampletable limit 10

        %jdbc(hive)
        select ${group_name}, count(*) as total_count
        from hivesampletable
        group by ${group_name=market,market|deviceplatform|devicemake}
        limit ${total_count=10}

    Jämföra med traditionella Hive, kommer resultatet av frågan tillbaka måste snabbare.


## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig att visualisera data från HDInsight med hjälp av [Microsoft Power BI](https://powerbi.microsoft.com/).  Mer information finns i följande artiklar:

* [Visualisera Apache Hive-data med Microsoft Power BI i Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md).
* [Visualisera Interactive Query Apache Hive-data med Power BI i Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Ansluta Excel till HDInsight med Microsoft Hive ODBC-drivrutin](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Ansluta Excel till Apache Hadoop med Power Query](hadoop/apache-hadoop-connect-excel-power-query.md).
* [Ansluta till Azure HDInsight och kör Apache Hive-frågor med Data Lake Tools för Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Använda Azure HDInsight-verktyg för Visual Studio Code](hdinsight-for-vscode.md).
* [Ladda upp Data till HDInsight](./hdinsight-upload-data.md).
