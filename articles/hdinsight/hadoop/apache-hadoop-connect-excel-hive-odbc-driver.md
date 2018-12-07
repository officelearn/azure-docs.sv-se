---
title: Ansluta Excel till Apache Hadoop med Hive ODBC-drivrutin - Azure HDInsight
description: Lär dig hur du konfigurerar och använder Microsoft Hive ODBC driver för Excel för att fråga efter data i HDInsight-kluster från Microsoft Excel.
keywords: hadoop excel, hive excel, hive odbc
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 622ebbd902b0a87d891432e14398d40eb4004e92
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2018
ms.locfileid: "53010985"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Ansluta Excel till Apache Hadoop i Azure HDInsight med Microsoft Hive ODBC-drivrutin

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Microsofts Big Data-lösning integrerar Microsoft Business Intelligence (BI)-komponenter med Apache Hadoop-kluster som har distribuerats med Azure HDInsight. Ett exempel på den här integreringen är möjligheten att ansluta Excel till Hive-datalagret i ett Hadoop-kluster i HDInsight med Microsoft Hive ODBC Open Database Connectivity ()-drivrutinen.

Det är också möjligt att ansluta de data som hör till ett HDInsight-kluster och andra datakällor, inklusive andra Hadoop-kluster med (icke-HDInsight), från Excel med hjälp av Microsoft Power Query-tillägget för Excel. Information om att installera och använda Power Query finns i [Anslut Excel till HDInsight med Power Query][hdinsight-power-query].



**Krav för**:

Innan du påbörjar den här artikeln måste du ha följande objekt:

* **Ett HDInsight-kluster**. Om du vill skapa en [Kom igång med Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* **En arbetsstation** med Office 2013 Professional Plus, Office 365 Pro Plus, fristående Excel 2013 eller Office Professional Plus 2010.

## <a name="install-microsoft-hive-odbc-driver"></a>Installera Microsoft Hive ODBC-drivrutin
Ladda ned och installera Microsoft Hive ODBC-drivrutin från den [Download Center][hive-odbc-driver-download].

Den här drivrutinen kan installeras på 32-bitars eller 64-bitars versioner av Windows 7, Windows 8, Windows 10, Windows Server 2008 R2 och Windows Server 2012. Drivrutinen tillåter anslutning till Azure HDInsight. Du bör installera den version som matchar versionen av programmet där du använder ODBC-drivrutinen. Den här självstudien används drivrutinen från Office Excel.

## <a name="create-apache-hive-odbc-data-source"></a>Skapa Apache Hive ODBC-datakälla
Följande steg visar hur du skapar en Hive ODBC-datakälla.

1. Från Windows 8 eller Windows 10, trycker du på Windows-tangenten för att öppna startskärmen och skriv sedan **datakällor**.
2. Klicka på **konfigurera ODBC-datakällor (32-bitars)** eller **konfigurera ODBC-datakällor (64-bitars)** beroende på dina Office-version. Om du använder Windows 7, väljer **ODBC-datakällor (32-bitars)** eller **ODBC-datakällor (64-bitars)** från **Administrationsverktyg**. Du bör se den **ODBC Data Source Administrator** dialogrutan.
   
    ![Datakälla för OBDC](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png "konfigurera en DNS med hjälp av ODBC-datakälla")

3. Användaren DNS, klickar du på **Lägg till** att öppna den **Skapa ny datakälla** guiden.
4. Välj **Microsoft Hive ODBC-drivrutin**, och klicka sedan på **Slutför**. Du bör se den **Microsoft Hive ODBC-drivrutinen DNS-inställningar** dialogrutan.
5. Ange eller välj följande värden:
   
   | Egenskap  | Beskrivning |
   | --- | --- |
   |  Namn på datakälla |Namnge din datakälla |
   |  Värd |Ange &lt;HDInsightClusterName>.azurehdinsight.net. Till exempel myHDICluster.azurehdinsight.net |
   |  Port |Använd <strong>443</strong>. (Den här porten har ändrats från 563 till 443.) |
   |  Databas |Använd <strong>Standard</strong>. |
   |  Mekanism |Välj <strong>Azure HDInsight-tjänst</strong> |
   |  Användarnamn |Ange HDInsight-kluster HTTP användarens användarnamn. Standardanvändarnamnet är <strong>admin</strong>. |
   |  Lösenord |Ange användarlösenord för HDInsight-kluster. |
   
    </table>
   
    Det finns några viktiga parametrar känna till när du klickar på **avancerade alternativ**:
   
   | Parameter | Beskrivning |
   | --- | --- |
   |  Använda Internfråga |När det är valt försöker ODBC-drivrutinen inte att konvertera TSQL till HiveQL. Du bör använda den endast om du är 100% säker på att du skickar ren HiveQL-instruktioner. När du ansluter till SQL Server eller Azure SQL Database, bör du lämna det avmarkerat. |
   |  Rader hämtade per block |När du hämtar ett stort antal poster, kan justera den här parametern krävas att säkerställa optimala prestanda. |
   |  Standardlängden för sträng-kolumnen, binär Kolumnlängd, Decimal kolumnskalan |Datatypen längder och Precision-datorerna kan påverka hur data returneras. De kan orsaka felaktig information som ska returneras på grund av förlust av precision och/eller trunkering. |

    ![Avancerade alternativ](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png "avancerade DSN konfigurationsalternativ")

1. Klicka på **testa** att testa datakällan. När datakällan är korrekt konfigurerad, visas *TESTERNA har SLUTFÖRTS!*.
2. Klicka på **OK** att stänga dialogrutan Test. Den nya datakällan skall anges i den **ODBC Data Source Administrator**.
3. Klicka på **OK** vill avsluta guiden.

## <a name="import-data-into-excel-from-hdinsight"></a>Importera data till Excel från HDInsight
Följande steg beskriver hur du importerar data från en Hive-tabell till en Excel-arbetsbok med hjälp av ODBC-datakälla som du skapade i föregående avsnitt.

1. Öppna en ny eller befintlig arbetsbok i Excel.
2. Från den **Data** fliken **hämta Data**, klickar du på **från andra källor**, och klicka sedan på **från ODBC** att starta den **Data Guiden Dataanslutning**.
   
    ![Öppna dataanslutningsguiden](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png "öppna dataanslutningsguiden")
4. Välj namnet på datakällan som du skapade i det sista avsnittet och klicka sedan på **OK**.
5. Ange Hadoop-användarnamn (standardnamnet är admin) och lösenordet och klicka sedan på **Connect**.
6. I Navigatören expanderar **HIVE**, expandera **standard**, klickar du på **hivesampletable**, och klicka sedan på **belastningen**. Det tar några sekunder innan data har importerats till Excel.

    ![HDInsight Hive ODBC navigator](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight.hive.odbc.navigator.png "öppna dataanslutningsguiden")


## <a name="next-steps"></a>Nästa steg
I den här artikeln beskrivs hur du använder Microsoft Hive ODBC-drivrutinen för att hämta data från HDInsight Service till Excel. På samma sätt kan du hämta data från HDInsight Service till SQL Database. Det är också möjligt att överföra data till ett HDInsight Service. Du kan läsa mer här:

* [Visualisera Hive-data med Microsoft Power BI i Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualisera Interactive Query Hive-data med Power BI i Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Använda Zeppelin för att köra Hive-frågor i Azure HDInsight ](./../hdinsight-connect-hive-zeppelin.md).
* [Ansluta Excel till Hadoop med Power Query](apache-hadoop-connect-excel-power-query.md).
* [Ansluta till Azure HDInsight och köra Hive-frågor med Data Lake Tools för Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Använda Azure HDInsight-verktyg för Visual Studio Code](../hdinsight-for-vscode.md).
* [Ladda upp data till HDInsight](./../hdinsight-upload-data.md).

[hdinsight-use-sqoop]:hdinsight-use-sqoop.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-hive]:hdinsight-use-hive.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query.md
[hive-odbc-driver-download]: https://go.microsoft.com/fwlink/?LinkID=286698


