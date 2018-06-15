---
title: Ansluta Excel till Hadoop med Hive ODBC-drivrutin - Azure HDInsight | Microsoft Docs
description: Lär dig hur du ställer in och använda Microsoft Hive ODBC-drivrutin för Excel för att fråga efter data i HDInsight-kluster från Microsoft Excel.
keywords: hadoop excel, hive excel, hive odbc
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
tags: azure-portal
editor: cgronlun
ms.assetid: a7665a14-0211-4521-b3e7-3b26e8029cc0
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jgao
ms.openlocfilehash: 26234ca17d833fef01ad5a6465824c99d84cc556
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34200511"
---
# <a name="connect-excel-to-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Ansluta Excel till Hadoop i Azure HDInsight med Microsoft Hive ODBC-drivrutin

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Microsofts Stordata lösningen integreras Microsoft Business Intelligence (BI) komponenter med Apache Hadoop-kluster som har distribuerats i Azure HDInsight. Ett exempel på den här integreringen är möjligheten att ansluta Excel till datalagret Hive i ett Hadoop-kluster i HDInsight med hjälp av drivrutinen för Microsoft Hive ODBC Open Database Connectivity ().

Det är också möjligt att ansluta data som är associerade med ett HDInsight-kluster och andra datakällor, inklusive andra (ej HDInsight) Hadoop-kluster, från Excel med hjälp av Microsoft Power Query-tillägget för Excel. Information om att installera och använda Power Query finns [ansluta Excel till HDInsight med Power Query][hdinsight-power-query].



**Krav för**:

Innan du börjar den här artikeln, måste du ha följande:

* **Ett HDInsight-kluster**. Om du vill skapa en finns [komma igång med Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* **En arbetsstation** med Office 2013 Professional Plus Office 365 Pro Plus, fristående för Excel 2013 eller Office Professional Plus 2010.

## <a name="install-microsoft-hive-odbc-driver"></a>Installera Microsoft Hive ODBC-drivrutin
Hämta och installera Microsoft Hive ODBC-drivrutinen från den [Download Center][hive-odbc-driver-download].

Den här drivrutinen kan installeras på 32-bitars eller 64-bitars versioner av Windows 7, Windows 8, Windows 10, Windows Server 2008 R2 och Windows Server 2012. Drivrutinen tillåter anslutning till Azure HDInsight. Du kan installera den version som matchar versionen av programmet där du använder ODBC-drivrutinen. Den här självstudien används drivrutinen från Office Excel.

## <a name="create-hive-odbc-data-source"></a>Skapa Hive ODBC-datakälla
Följande steg visar hur du skapar en Hive ODBC-datakällan.

1. Från Windows 8 eller Windows 10, tryck på Windows-tangenten för att öppna startskärmen och skriv **datakällor**.
2. Klicka på **installera ODBC-datakällor (32-bitars)** eller **ställa in ODBC-datakällor (64-bitars)** beroende på dina Office-version. Om du använder Windows 7, väljer du **ODBC-datakällor (32 bitar)** eller **ODBC-datakällor (64-bitars)** från **Administrationsverktyg**. Du bör se den **ODBC Data Source Administrator** dialogrutan.
   
    ![Datakälla för OBDC](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png "konfigurera DSN med ODBC Data Source Administrator")

3. Användaren DNS, klicka på **Lägg till** att öppna den **Skapa ny datakälla** guiden.
4. Välj **Microsoft Hive ODBC-drivrutinen**, och klicka sedan på **Slutför**. Du bör se den **Microsoft Hive ODBC-drivrutinen DNS-inställningar för** dialogrutan.
5. Ange eller välj följande värden:
   
   | Egenskap  | Beskrivning |
   | --- | --- |
   |  Namn på datakälla |Namnge din datakälla |
   |  Värd |Ange &lt;HDInsightClusterName>.azurehdinsight.net. Till exempel myHDICluster.azurehdinsight.net |
   |  Port |Använd <strong>443</strong>. (Den här porten har ändrats från 563 till 443.) |
   |  Databas |Använd <strong>Standard</strong>. |
   |  Mekanism |Välj <strong>Azure HDInsight-tjänst</strong> |
   |  Användarnamn |Ange ett HDInsight-kluster HTTP användaren användarnamn. Standardanvändarnamnet är <strong>admin</strong>. |
   |  Lösenord |Ange användarlösenord för HDInsight-kluster. |
   
    </table>
   
    Det finns vissa viktiga parametrar att vara medveten om när du klickar på **avancerade alternativ**:
   
   | Parameter | Beskrivning |
   | --- | --- |
   |  Använda en intern fråga |När den är markerad försöker ODBC-drivrutinen inte konvertera TSQL till HiveQL. Du kan använda den om du är 100% säker på att du skickar ren HiveQL-instruktioner. När du ansluter till SQL Server eller Azure SQL Database, bör den vara avmarkerad. |
   |  Rader som hämtas per block |När du hämtar ett stort antal poster kan inställning av den här parametern krävas att säkerställa optimala prestanda. |
   |  Standard kolumnen stränglängd, binära kolumnlängden, Decimal kolumnskalan |Datatypen längder och Precision-datorerna kan påverka hur data returneras. De orsakar felaktig information som ska returneras på grund av förlust av precision och/eller trunkering. |

    ![Avancerade alternativ](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png "DSN avancerade konfigurationsalternativ")

1. Klicka på **testa** att testa datakällan. När datakällan är korrekt konfigurerad, visas *TESTERNA har SLUTFÖRTS!*.
2. Klicka på **OK** att stänga dialogrutan Test. Den nya datakällan skall anges i den **ODBC Data Source Administrator**.
3. Klicka på **OK** vill avsluta guiden.

## <a name="import-data-into-excel-from-hdinsight"></a>Importera data till Excel från HDInsight
Följande steg beskriver hur du importerar data från en Hive-tabell till en Excel-arbetsbok med hjälp av ODBC-datakällan som du skapade i föregående avsnitt.

1. Öppna en ny eller befintlig arbetsbok i Excel.
2. Från den **Data** klickar du på **hämta Data**, klickar du på **från andra källor**, och klicka sedan på **från ODBC** att starta den **Dataanslutningsguiden**.
   
    ![Öppna guiden](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png "öppna guiden")
4. Välj namnet på datakällan som du skapade i det sista avsnittet och klicka sedan på **OK**.
5. Ange Hadoop-användarnamn (standardnamnet är admin) och lösenordet och klicka sedan på **Anslut**.
6. Expandera på Navigator **HIVE**, expandera **standard**, klickar du på **hivesampletable**, och klicka sedan på **belastningen**. Det tar några sekunder innan data har importerats till Excel.

    ![HDInsight Hive ODBC navigator](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight.hive.odbc.navigator.png "öppna guiden")


## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du använder Microsoft Hive ODBC-drivrutinen för att hämta data från HDInsight Service till Excel. På liknande sätt kan du hämta data från HDInsight Service i SQL-databasen. Det är också möjligt att överföra data till ett HDInsight Service. Du kan läsa mer här:

* [Visualisera Hive-data med Microsoft Power BI i Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualisera interaktiva frågan Hive-data med Power BI i Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Använda Zeppelin för att köra Hive-frågor i Azure HDInsight ](./../hdinsight-connect-hive-zeppelin.md).
* [Ansluta Excel till Hadoop med Power Query](apache-hadoop-connect-excel-power-query.md).
* [Ansluta till Azure HDInsight och köra Hive-frågor med Data Lake-verktyg för Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Använda Azure HDInsight-verktyg för Visual Studio Code](../hdinsight-for-vscode.md).
* [Överföra data till HDInsight](./../hdinsight-upload-data.md).

[hdinsight-use-sqoop]:hdinsight-use-sqoop.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-hive]:hdinsight-use-hive.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query.md
[hive-odbc-driver-download]: http://go.microsoft.com/fwlink/?LinkID=286698


