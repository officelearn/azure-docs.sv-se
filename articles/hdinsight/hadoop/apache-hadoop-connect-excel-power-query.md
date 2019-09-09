---
title: Ansluta Excel till Apache Hadoop med Power Query – Azure HDInsight
description: Lär dig hur du utnyttjar Business Intelligence-komponenter och använder Power Query för Excel för att komma åt data som lagras i Hadoop på HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.openlocfilehash: c1255296854fd28cca4427b6cac04de7dc7919d8
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810913"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Ansluta Excel till Apache Hadoop med hjälp av Power Query
En viktig funktion i Microsofts stor data lösning är integreringen av Microsoft Business Intelligence-komponenter (BI) med Apache Hadoop kluster i Azure HDInsight. Ett primärt exempel är möjligheten att ansluta Excel till det Azure Storage konto som innehåller de data som är kopplade till ditt Hadoop-kluster med hjälp av Microsoft Power Query för Excel-tillägget. Den här artikeln beskriver hur du konfigurerar och använder Power Query för att fråga efter data som är associerade med ett Hadoop-kluster som hanteras med HDInsight.

### <a name="prerequisites"></a>Förutsättningar
Innan du börjar den här artikeln måste du ha följande objekt:

* **An-HDInsight kluster**. Om du vill konfigurera ett läser du [komma igång med Azure HDInsight] [HDInsight-get-started].
* **En arbets Station** som kör Windows 7, windows Server 2008 R2 eller ett senare operativ system.
* **Office 2016, office 2013 Professional Plus, office 365 ProPlus, Excel 2013 fristående eller Office 2010 Professional Plus**.

## <a name="install-power-query"></a>Installera Power Query
Power Query kan importera data som har skrivits ut eller som har genererats av ett Hadoop-jobb som körs på ett HDInsight-kluster.

I Excel 2016 har Power Query integrerats i menyfliksområdet under avsnittet Hämta & Transform. För äldre versioner av Excel laddar du ned Microsoft Power Query för Excel från [Microsoft Download Center][powerquery-download] och installerar det.

## <a name="import-hdinsight-data-into-excel"></a>Importera HDInsight-data till Excel
Med Power Query-tillägget för Excel kan du enkelt importera data från HDInsight-klustret till Excel, där BI-verktyg som PowerPivot och Power Map kan användas för att kontrol lera, analysera och presentera data.

**Importera data från ett HDInsight-kluster**

1. Öppna Excel.
2. Skapa en ny tom arbets bok.
3. Utför följande steg baserat på Excel-versionen:

   - Excel 2016

     - Klicka på **data** -menyn, klicka på **Hämta data** från menyfliken **Hämta & transformera data** , klicka på **från Azure**och klicka sedan på **från Azure HDInsight (HDFS)** .

       ![HDI. PowerQuery. SelectHdiSource. 2016](./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.excel2016.png)

   - Excel 2013/2010

     - Klicka på menyn **Power Query** , klicka på **från Azure**och klicka sedan på **från Microsoft Azure HDInsight**.
   
       ![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]
       
       **Obs:** Om du inte ser **Power Query** menyn går du till **fil** > **alternativ** > **tillägg**och väljer **com-tillägg** i list rutan **Hantera** längst ned på sidan. Välj knappen **gå...** och kontrol lera att rutan för tillägget Power Query för Excel har marker ATS.
       
       **Obs:** Med Power Query kan du också importera data från HDFS genom att klicka på **från andra källor**.
4. För **konto namn**anger du namnet på det Azure Blob Storage-konto som är associerat med klustret och klickar sedan på **OK**. Det här kontot kan vara standard lagrings kontot eller ett länkat lagrings konto.  Formatet är *https://&lt;StorageAccountName >. blob. Core. Windows. net/* .
5. För **konto nyckel**anger du nyckeln för Blob Storage-kontot och klickar sedan på **Spara**. (Du behöver bara ange konto informationen första gången du öppnar det här arkivet.)
6. I fönstret **navigatör** till vänster i Frågeredigeraren dubbelklickar du på namnet på Blob storage-behållaren. Behållarens namn är som standard samma namn som kluster namnet.
7. Leta upp **HiveSampleData. txt** i kolumnen **namn** (mappsökvägen är **... /Hive/Warehouse/hivesampletable/** ) och klicka sedan på **binär** till vänster om HiveSampleData. txt. HiveSampleData. txt levereras med alla kluster. Du kan också använda en egen fil.
   
    ![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]
8. Om du vill kan du byta namn på kolumn namnen. När du är klar klickar du på **stäng & belastning**.  Data har lästs in i din arbets bok:
   
    ![HDI. PowerQuery. ImportedTable][image-hdi-powerquery-imported-table]

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du använder Power Query för att hämta data från HDInsight till Excel. På samma sätt kan du hämta data från HDInsight till Azure SQL Database. Det går också att ladda upp data till HDInsight. Mer information finns i följande artiklar:

* [Visualisera Apache Hive data med Microsoft Power BI i Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualisera interaktiva Hive-frågedata med Power BI i Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Använd Apache Zeppelin för att köra apache Hive frågor i Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Anslut Excel till HDInsight med Microsoft HIVE ODBC-drivrutinen](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Anslut till Azure HDInsight och kör Apache Hive frågor med hjälp av data Lake verktyg för Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Använd Azure HDInsight-verktyget för Visual Studio Code](../hdinsight-for-vscode.md).
* [Ladda upp data till HDInsight](./../hdinsight-upload-data.md).

[image-hdi-powerquery-hdi-source]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.png
[image-hdi-powerquery-importdata]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importdata.png
[image-hdi-powerquery-imported-table]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importedtable.PNG

[powerquery-download]: https://go.microsoft.com/fwlink/?LinkID=286689
