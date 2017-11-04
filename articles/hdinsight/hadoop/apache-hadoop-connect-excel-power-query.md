---
title: Ansluta Excel till Hadoop med Power Query - Azure HDInsight | Microsoft Docs
description: "Lär dig att dra nytta av business intelligence-komponenter och använda Power Query för Excel för att komma åt data som lagras i Hadoop i HDInsight."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 01ad2f90-7520-44d9-8c16-4d936faaff9b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jgao
ms.openlocfilehash: e40ddd50a84dfbd8e31a13cef7e4342658bb4f49
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="connect-excel-to-hadoop-by-using-power-query"></a>Ansluta Excel till Hadoop med Power Query
En nyckelfunktion i Microsofts lösning för stora data är integreringen av Microsoft business intelligence (BI)-komponenter med Hadoop-kluster i Azure HDInsight. En primär exempel är möjligheten att ansluta Excel till Azure Storage-konto som innehåller data som är associerade med Hadoop-kluster med hjälp av Microsoft Power Query för Excel-tillägg. Den här artikeln vägleder dig genom att ställa in och använda Power Query att fråga efter data som är associerade med ett Hadoop-kluster som hanteras med HDInsight.

### <a name="prerequisites"></a>Krav
Innan du börjar den här artikeln, måste du ha följande:

* **Ett HDInsight-kluster**. Om du vill konfigurera en finns [komma igång med Azure HDInsight] [hdinsight-get-started].
* **En arbetsstation** som kör Windows 7, Windows Server 2008 R2 eller senare operativsystem.
* **Office 2016, Office 2013 Professional Plus Office 365 ProPlus, fristående för Excel 2013 eller Office Professional Plus 2010**.

## <a name="install-power-query"></a>Installera Power Query
Power Query kan importera data som har tagits ut eller som har genererats av ett Hadoop-jobb som körs på ett HDInsight-kluster.

Power Query har integrerats i menyfliksområdet under avsnittet Get och transformera Data i Excel 2016. För äldre versioner av Excel, ladda ned Microsoft Power Query för Excel från den [Microsoft Download Center] [ powerquery-download] och installera den.

## <a name="import-hdinsight-data-into-excel"></a>Importera HDInsight data till Excel
Power Query-tillägget för Excel gör det enkelt att importera data från ditt HDInsight-kluster i Excel där BI-verktyg som PowerPivot och Power mappning kan användas för att inspektera, analysera och presentera informationen.

**Importera data från ett HDInsight-kluster**

1. Öppna Excel.
2. Skapa en ny tom arbetsbok.
3. Utför följande steg beroende på vilken version av Excel:

    - Excel 2016

        - Klicka på den **Data** -menyn klickar du på **hämta Data** från den **Hämta & transformera Data** band klickar du på **från Azure**, och klicka sedan på **Från Azure HDInsight(HDFS)**.

        ![HDI. PowerQuery.SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.excel2016.png)

    - Excel 2013/2010

        - Klicka på den **Power Query** -menyn klickar du på **från Azure**, och klicka sedan på **från Microsoft Azure HDInsight**.
   
        ![HDI. PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]
       
        **Obs:** om du inte ser den **Power Query** gå till menyn **filen** > **alternativ** > **tillägg**, och välj **COM-tillägg** från listrutan **hantera** rutan längst ned på sidan. Välj den **gå...**  knappen och kontrollera att kryssrutan för Power Query för Excel-tillägget har kontrollerats.
       
        **Obs:** Power Query kan du importera data från HDFS genom att klicka på **från andra källor**.
4. För **kontonamn**, ange namnet på Azure Blob storage-konto som är associerade med klustret och klicka sedan på **OK**. Det här kontot kan vara den [standard lagringskonto](../hdinsight-administer-use-management-portal.md#find-the-default-storage-account) eller ett länkade storage-konto.  Formatet är *https://&lt;StorageAccountName >.blob.core.windows.net/*.
5. För **Kontonyckel**ange nyckel för Blob storage-konto och klicka sedan på **spara**. (Du måste ange konto information endast första tid som du har åtkomst till den här butiken.)
6. I den **Navigator** fönstret till vänster för frågeredigeraren Dubbelklicka på Blob storage behållarens namn. Behållarens namn är samma namn som klusternamnet som standard.
7. Leta upp **HiveSampleData.txt** i den **namn** kolumn (mappsökvägen är **... / hive/datalager/hivesampletable/**), och klicka sedan på **binära** till vänster om HiveSampleData.txt. HiveSampleData.txt ingår i klustret. Du kan använda en egen fil.
   
    ![HDI. PowerQuery.ImportData][image-hdi-powerquery-importdata]
8. Om du vill kan du byta namn på kolumnnamn. När du är klar klickar du på **Stäng & ladda**.  Data har lästs in till din arbetsbok:
   
    ![HDI. PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du använder Power Query för att hämta data från HDInsight till Excel. På liknande sätt kan du hämta data från HDInsight till Azure SQL Database. Det är också möjligt att överföra data till HDInsight. Mer information finns i följande artiklar:

* [Visualisera Hive-data med Microsoft Power BI i Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Använda Zeppelin för att köra Hive-frågor i Azure HDInsight ](./../hdinsight-connect-hive-zeppelin.md).
* [Anslut Excel till HDInsight med Microsoft Hive ODBC-drivrutinen](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Ansluta till Azure HDInsight och köra Hive-frågor med Data Lake-verktyg för Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Använda Azure HDInsight-verktyg för Visual Studio Code](../hdinsight-for-vscode.md).
* [Överföra data till HDInsight](./../hdinsight-upload-data.md).

[image-hdi-powerquery-hdi-source]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.png
[image-hdi-powerquery-importdata]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importdata.png
[image-hdi-powerquery-imported-table]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importedtable.PNG

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689
