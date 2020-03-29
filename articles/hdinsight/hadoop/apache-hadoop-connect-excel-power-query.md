---
title: Anslut Excel till Apache Hadoop med Power Query – Azure HDInsight
description: Lär dig hur du drar nytta av business intelligence-komponenter och använder Power Query för Excel för att komma åt data som lagras i Hadoop på HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: e643c7fe7b18eed30843e7cab3977036435d2112
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435807"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Ansluta Excel till Apache Hadoop med Power Query

En viktig funktion i Microsofts stordatalösning är integreringen av Microsoft Business Intelligence (BI) komponenter med Apache Hadoop-kluster i Azure HDInsight. Ett primärt exempel är möjligheten att ansluta Excel till Azure Storage-kontot som innehåller data som är associerade med Ditt Hadoop-kluster med hjälp av tillägget Microsoft Power Query för Excel. I den här artikeln får du lära dig hur du konfigurerar och använder Power Query för att fråga data som är associerade med ett Hadoop-kluster som hanteras med HDInsight.

## <a name="prerequisites"></a>Krav

* En Apache Hadoop kluster på HDInsight. Se [Komma igång med HDInsight på Linux](./apache-hadoop-linux-tutorial-get-started.md).
* En arbetsstation som kör Windows 10, 7, Windows Server 2008 R2 eller ett senare operativsystem.
* Office 2016, Office 2013 Professional Plus, Office 365 ProPlus, Excel 2013 Fristående eller Office 2010 Professional Plus.

## <a name="install-microsoft-power-query"></a>Installera Microsoft Power Query

Power Query kan importera data som har utdata eller som har genererats av ett Hadoop-jobb som körs på ett HDInsight-kluster.

I Excel 2016 har Power Query integrerats i menyfliksområdet För data under avsnittet Hämta & Transform. För äldre Excel-versioner hämtar du Microsoft Power Query för Excel från [Microsoft Download Center](https://go.microsoft.com/fwlink/?LinkID=286689) och installerar det.

## <a name="import-hdinsight-data-into-excel"></a>Importera HDInsight-data till Excel

Power Query-tillägget för Excel gör det enkelt att importera data från ditt HDInsight-kluster till Excel, där BI-verktyg som PowerPivot och Power Map kan användas för att granska, analysera och presentera data.

1. Starta Excel.

1. Skapa en ny tom arbetsbok.

1. Utför följande steg baserat på Excel-versionen:

   * Excel 2016

     * Välj > **data** > **hämta data** > **från Azure** > **från Azure HDInsight(HDFS).**

       ![Hdi. PowerQuery.SelectHdiSource.2016](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource-excel2016.png)

   * Excel 2013/2010

     * Välj **Power Query** > **från Azure** > **från Microsoft Azure HDInsight**.

       ![Hdi. PowerQuery.SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource.png)

       **Anm.:** Om du inte ser **Power Query-menyn** går du **till** > **Filalternativtillägg** > **Add-ins**och väljer **COM-tillägg i** **listrutan Hantera** längst ned på sidan. Välj knappen **Gå...** och kontrollera att rutan för Power Query för Excel-tillägget har markerats.

       **Anm.:** Med Power Query kan du också importera data från HDFS genom att välja **Från andra källor**.

1. I dialogrutan **Kontonamn eller URL** i dialogrutan **Kontonamn** eller URL anger du namnet på azure blob-lagringskontot som är associerat med klustret. Välj sedan **OK**. Det här kontot kan vara standardlagringskontot eller ett länkat lagringskonto.  Formatet är `https://StorageAccountName.blob.core.windows.net/`.

1. För **Kontonyckel**anger du nyckeln för Blob-lagringskontot och väljer sedan **Anslut**. (Du behöver bara ange kontoinformationen första gången du öppnar den här arkivet.)

1. Dubbelklicka på det Blob-lagringsbehållarenamn som är associerat med klustret i fönstret **Navigator** till vänster i Frågeredigeraren. Som standard är behållarnamnet samma namn som klusternamnet.

1. Leta reda på **HiveSampleData.txt** i kolumnen **Namn** (mappsökvägen är **.. /hive/warehouse/hivesampletable/**) och välj sedan **Binär till** vänster om HiveSampleData.txt. HiveSampleData.txt levereras med alla kluster. Du kan också använda din egen fil.

    ![HDI Excel power query import data](./media/apache-hadoop-connect-excel-power-query/powerquery-importdata.png)

1. Om du vill kan du byta namn på kolumnnamnen. När du är klar väljer du **Stäng & Läs in**.  Data har lästs in i arbetsboken:

    ![Hdi Excel power query importerad tabell](./media/apache-hadoop-connect-excel-power-query/powerquery-importedtable.png)

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig hur du använder Power Query för att hämta data från HDInsight till Excel. På samma sätt kan du hämta data från HDInsight till Azure SQL Database. Det är också möjligt att ladda upp data till HDInsight. Mer information finns i följande artiklar:

* [Visualisera Apache Hive-data med Microsoft Power BI i Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualisera interaktiva frågeinsdata med Power BI i Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Använd Apache Zeppelin för att köra Apache Hive-frågor i Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Anslut Excel till HDInsight med Microsoft Hive ODBC Driver](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Anslut till Azure HDInsight och kör Apache Hive-frågor med DataSjöverktyg för Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Använd Azure HDInsight Tool för Visual Studio-kod](../hdinsight-for-vscode.md).
* [Ladda upp data till HDInsight](./../hdinsight-upload-data.md).
