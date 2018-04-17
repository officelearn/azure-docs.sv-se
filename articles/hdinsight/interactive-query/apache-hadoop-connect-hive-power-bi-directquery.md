---
title: Visualisera interaktiva frågan Hive-data med Power BI i Azure HDInsight | Microsoft Docs
description: Lär dig hur du använder Microsoft Power BI för att visualisera interaktiva Hive-fråga data som bearbetas av Azure HDInsight.
keywords: hdinsight hadoop och hive, interaktiva fråga interaktiva hive, LLAP, directquery
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: jgao
ms.openlocfilehash: b8da1f17b9e477caf9031cf94ee14f3a181e247e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="visualize-interactive-query-hive-data-with-microsoft-power-bi-using-direct-query-in-azure-hdinsight"></a>Visualisera interaktiva Hive-fråga data med Microsoft Power BI med direktfrågan i Azure HDInsight

Lär dig mer om att ansluta Microsoft Power BI till interaktiva frågan i Azure HDInsight-kluster och visualisera Hive-data med hjälp av direkt fråga. I den här självstudiekursen läsa in data från en hivesampletable Hive-tabell till Power BI. Hive-tabellen innehåller några mobiltelefon användningsdata. Sedan kan du rita användningsdata i en värld av:

![HDInsight Power BI rapporten karta](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Du kan utnyttja den [ODBC-drivrutinen](../hadoop/apache-hadoop-connect-hive-power-bi.md) att importera via de allmänna ODBC-kopplingen i Power BI Desktop. Men det rekommenderas inte för BI arbetsbelastningar eftersom icke-interaktiv frågemotorn Hive. [HDInsight interaktiva frågan connector](./apache-hadoop-connect-hive-power-bi-directquery.md) och [HDInsight Spark connector](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) bättre val för deras prestanda.

## <a name="prerequisites"></a>Förutsättningar
Innan du fortsätter med den här artikeln, måste du ha följande:

* **HDInsight-kluster**. Klustret kan vara ett HDInsight-kluster med Hive eller ett nyligen utgivna interaktiva fråga-kluster. För att skapa kluster, se [Skapa kluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Du kan hämta en kopia från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Läs in data från HDInsight

Hivesampletable Hive-tabell kommer med alla HDInsight-kluster.

1. Logga in på Power BI Desktop.
2. Klicka på den **Start** klickar du på **hämta Data** från den **externa data** band och välj sedan **mer**.

    ![Öppna HDInsight Power BI-data](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)
3. Från den **hämta Data** rutan, skriver **hdinsight** i sökrutan. Om du inte ser **HDInsight interaktiva fråga (Beta)**, måste du uppdatera din Power BI Desktop till den senaste versionen.
4. Klicka på **HDInsight interaktiva fråga (Beta)**, och klicka sedan på **Anslut**.
5. Klicka på **Fortsätt** att stänga den **Preview connector** varningsmeddelandet.
6. Från **HDInsight interaktiva frågan**väljer eller anger du följande information:

    - **Server**: Ange interaktiva frågan klustrets namn, till exempel *myiqcluster.azurehdinsight.net*.
    - **Databasen**: den här självstudiekursen, ange **standard**.
    - **Data anslutningsläget**: den här kursen väljer **DirectQuery**.

    ![HDInsight interaktiva frågan power bi directquery ansluter](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)
7. Klicka på **OK**.
8. Ange autentiseringsuppgifter för HTTP-användare och klicka sedan på **OK**.  Standardanvändarnamnet är **admin**
9. I den vänstra rutan, Välj **hivesampletale**, och klicka sedan på **belastningen**.

    ![HDInsight interaktiva frågan power bi hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data"></a>Visualisera data

Fortsätt från föregående procedur.

1. Fönstret visualiseringar Välj **kartan**.  Det är en Globikon.

    ![HDInsight Power BI anpassar rapport](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)
2. Fönstret fält, Välj **land** och **devicemake**. Du kan se de data som visas på kartan.
3. Expandera kartan.

## <a name="next-steps"></a>Nästa steg
I den här artikeln beskrivs hur du visualisera data från HDInsight med hjälp av Power BI.  Mer information finns i följande artiklar:

* [Visualisera data med Hive med Microsoft Power BI med ODBC i Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Använda Zeppelin för att köra Hive-frågor i Azure HDInsight](./../hdinsight-connect-hive-zeppelin.md).
* [Anslut Excel till HDInsight med Microsoft Hive ODBC-drivrutinen](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Ansluta Excel till Hadoop med Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Ansluta till Azure HDInsight och köra Hive-frågor med Data Lake-verktyg för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Använda Azure HDInsight-verktyg för Visual Studio Code](../hdinsight-for-vscode.md).
* [Överföra Data till HDInsight](./../hdinsight-upload-data.md).
