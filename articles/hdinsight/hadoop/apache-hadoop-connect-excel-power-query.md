---
title: Ansluta Excel till Apache Hadoop med Power Query - Azure HDInsight
description: Lär dig att dra nytta av business intelligence-komponenterna och använda Power Query för Excel för att komma åt data som lagras i Hadoop på HDInsight.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.openlocfilehash: 687a9884c861b4cf72e51f9179e6c0b43968801d
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202919"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Ansluta Excel till Apache Hadoop med Power Query
En nyckelegenskap för Microsofts lösning för stordata är integreringen av Microsoft business intelligence (BI)-komponenterna med Apache Hadoop-kluster i Azure HDInsight. En primär exempel är möjligheten att ansluta Excel till Azure Storage-kontot som innehåller data som är associerade med ditt Hadoop-kluster med hjälp av Microsoft Power Query för Excel-tillägg. Den här artikeln vägleder dig igenom hur du konfigurerar och använder Power Query för att köra frågor mot data som är associerade med ett Hadoop-kluster som hanteras med HDInsight.

### <a name="prerequisites"></a>Förutsättningar
Innan du påbörjar den här artikeln måste du ha följande objekt:

* **Ett HDInsight-kluster**. Om du vill konfigurera en finns i [komma igång med Azure HDInsight] [hdinsight-get-started].
* **En arbetsstation** som kör Windows 7, Windows Server 2008 R2 eller senare operativsystem.
* **Office 2016, Office 2013 Professional Plus, Office 365 ProPlus, fristående Excel 2013 eller Office Professional Plus 2010**.

## <a name="install-power-query"></a>Installera Power Query
Power Query kan importera data som har varit utdata eller som har genererats av ett Hadoop-jobb som körs på ett HDInsight-kluster.

Power Query har integrerats i menyfliksområdet Data under avsnittet Hämta & transformera i Excel 2016. För äldre versioner av Excel kan du ladda ned Microsoft Power Query för Excel från den [Microsoft Download Center] [ powerquery-download] och installera den.

## <a name="import-hdinsight-data-into-excel"></a>Importera HDInsight-data till Excel
Power Query-tillägget för Excel gör det enkelt att importera data från ditt HDInsight-kluster i Excel, där BI-verktyg som PowerPivot och Power Map kan användas för att inspektera, analysera och presentera data.

**Att importera data från ett HDInsight-kluster**

1. Öppna Excel.
2. Skapa en ny tom arbetsbok.
3. Utför följande steg beroende på vilken version av Excel:

   - Excel 2016

     - Klicka på den **Data** -menyn klickar du på **hämta Data** från den **hämta och transformera Data** menyfliksområdet, klickar du på **från Azure**, och klicka sedan på **Från Azure HDInsight(HDFS)**.

       ![HDI.PowerQuery.SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.excel2016.png)

   - Excel 2013/2010

     - Klicka på den **Power Query** -menyn klickar du på **från Azure**, och klicka sedan på **från Microsoft Azure HDInsight**.
   
       ![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]
       
       **Obs!** Om du inte ser den **Power Query** gå till menyn **filen** > **alternativ** > **tillägg**, och välj  **COM-tillägg** från listrutan **hantera** rutan längst ned på sidan. Välj den **gå...**  knappen och kontrollera att kryssrutan för den Power Query för Excel-tillägget har kontrollerats.
       
       **Obs!** Power Query kan du importera data från HDFS genom att klicka på **från andra källor**.
4. För **kontonamn**, anger du namnet på Azure Blob storage-konto som är associerade med klustret och klicka sedan på **OK**. Det här kontot kan vara standardkontot för lagring eller ett länkat lagringskonto.  Formatet är *https://&lt;StorageAccountName >.blob.core.windows.net/*.
5. För **Kontonyckel**, ange nyckeln för Blob storage-kontot och klicka sedan på **spara**. (Du måste ange konto information endast första gång som du har åtkomst till det här arkivet.)
6. I den **Navigator** till vänster i frågeredigeraren, dubbelklicka på namnet på Blob storage-behållare. Behållarens namn är som standard samma namn som klusternamnet.
7. Leta upp **HiveSampleData.txt** i den **namn** kolumnen (mappsökvägen är **... / hive/datalager/hivesampletable/**), och klicka sedan på **binära** till vänster om HiveSampleData.txt. HiveSampleData.txt ingår i klustret. Du kan även använda en egen fil.
   
    ![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]
8. Om du vill kan du byta namn på kolumnnamnen. När du är klar klickar du på **Stäng och Läs in**.  Data har lästs in till din arbetsbok:
   
    ![HDI.PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a name="next-steps"></a>Nästa steg
I den här artikeln beskrivs hur du använder Power Query för att hämta data från HDInsight till Excel. På samma sätt kan du hämta data från HDInsight till Azure SQL Database. Det är också möjligt att överföra data till HDInsight. Mer information finns i följande artiklar:

* [Visualisera Apache Hive-data med Microsoft Power BI i Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualisera Interactive Query Hive-data med Power BI i Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Använda Apache Zeppelin för att köra Apache Hive-frågor i Azure HDInsight](./../hdinsight-connect-hive-zeppelin.md).
* [Ansluta Excel till HDInsight med Microsoft Hive ODBC-drivrutin](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Ansluta till Azure HDInsight och kör Apache Hive-frågor med Data Lake Tools för Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Använda Azure HDInsight-verktyg för Visual Studio Code](../hdinsight-for-vscode.md).
* [Ladda upp data till HDInsight](./../hdinsight-upload-data.md).

[image-hdi-powerquery-hdi-source]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.png
[image-hdi-powerquery-importdata]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importdata.png
[image-hdi-powerquery-imported-table]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importedtable.PNG

[powerquery-download]: https://go.microsoft.com/fwlink/?LinkID=286689
