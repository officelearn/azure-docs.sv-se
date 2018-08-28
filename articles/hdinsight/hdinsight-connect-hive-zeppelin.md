---
title: Använda Zeppelin för att köra Hive-frågor i Azure HDInsight
description: Lär dig mer om att använda Zeppelin för att köra Hive-frågor.
keywords: hdinsight, hadoop, hive, LLAP interaktiv fråga
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jasonh
ms.openlocfilehash: 3064c9cd141458307891f666bd5af9aa738cc021
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43093176"
---
# <a name="use-zeppelin-to-run-hive-queries-in-azure-hdinsight"></a>Använda Zeppelin för att köra Hive-frågor i Azure HDInsight 

Interaktiv HDInsight-fråga innehåller Zeppelin-anteckningsböcker som du kan använda för att köra interaktiva Hive-frågor. I den här artikeln får du lära dig hur du använder Zeppelin för att köra Hive-frågor i Azure HDInsight. 

## <a name="prerequisites"></a>Förutsättningar
Innan du går igenom den här artikeln, måste du ha följande objekt:

* **Klustret HDInsight interaktiv fråga**. Se [Skapa kluster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) att skapa ett HDInsight-kluster.  Se till att välja typen interaktiv fråga. 

## <a name="create-a-zeppelin-note"></a>Skapa en Zeppelin-anteckning

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
I den här artikeln lärde du dig att visualisera data från HDInsight med hjälp av Power BI.  Mer information finns i följande artiklar:

* [Visualisera Hive-data med Microsoft Power BI i Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md).
* [Visualisera Interactive Query Hive-data med Power BI i Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Ansluta Excel till HDInsight med Microsoft Hive ODBC-drivrutin](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Ansluta Excel till Hadoop med Power Query](hadoop/apache-hadoop-connect-excel-power-query.md).
* [Ansluta till Azure HDInsight och köra Hive-frågor med Data Lake Tools för Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Använda Azure HDInsight-verktyg för Visual Studio Code](hdinsight-for-vscode.md).
* [Ladda upp Data till HDInsight](./hdinsight-upload-data.md).
