---
title: Excel & Apache Hadoop med ODBC Driver - Azure HDInsight
description: Lär dig hur du konfigurerar och använder Microsoft Hive ODBC-drivrutinen för Excel för att fråga data i HDInsight-kluster från Microsoft Excel.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/02/2020
ms.openlocfilehash: f356009197c0446efa2ea2d7f0e90040229df47b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251074"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Ansluta Excel till Apache Hadoop i Azure HDInsight med Microsoft Hives ODBC-drivrutin

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Microsofts Big Data-lösning integrerar Microsoft Business Intelligence (BI) komponenter med Apache Hadoop-kluster som har distribuerats i Azure HDInsight. Ett exempel på den här integreringen är möjligheten att ansluta Excel till Hive-informationslagret för ett Hadoop-kluster i HDInsight med hjälp av ODBC-drivrutinen (Microsoft Hive Open Database Connectivity).

Det är också möjligt att ansluta data som är associerade med ett HDInsight-kluster och andra datakällor, inklusive andra (icke-HDInsight) Hadoop-kluster, från Excel med hjälp av Microsoft Power Query-tillägget för Excel. Information om hur du installerar och använder Power Query finns i [Anslut Excel till HDInsight med Power Query](../hdinsight-connect-excel-power-query.md).

## <a name="prerequisites"></a>Krav

Innan du börjar den här artikeln måste du ha följande:

* Ett HDInsight Hadoop-kluster. Information om hur du skapar en information finns [i Komma igång med Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* En arbetsstation med Office 2010 Professional Plus eller senare, eller Excel 2010 eller senare.

## <a name="install-microsoft-hive-odbc-driver"></a>Installera Microsoft Hive ODBC-drivrutin

Hämta och installera [Microsoft Hive ODBC Driver-versionen](https://www.microsoft.com/download/details.aspx?id=40886) som matchar den version av programmet där du ska använda ODBC-drivrutinen.  I den här artikeln används drivrutinen för Office Excel.

## <a name="create-apache-hive-odbc-data-source"></a>Skapa Apache Hive ODBC-datakälla

Följande steg visar hur du skapar en Hive ODBC-datakälla.

1. Från Windows navigerar du till Start > Windows Administrationsverktyg > ODBC-datakällor (32-bitars)/(64-bitars).  Då öppnas fönstret **ADMINISTRATÖR FÖR ODBC-datakälla.**

    ![OBDC-datakälla administratör](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-datasourceadmin1.png "Konfigurera ett DSN med ODBC-datakälladministratör")

1. **Öppna** fönstret **Skapa ny datakälla** på fliken **Användar-DSN.**

1. Välj **Microsoft Hive ODBC Driver**och välj sedan **Slutför** för att öppna installationsfönstret för **Microsoft Hive ODBC Driver DSN.**

1. Ange eller välj följande värden:

   | Egenskap | Beskrivning |
   | --- | --- |
   |  Namn på datakälla |Namnge din datakälla |
   |  Värd/program (er) |Ange `HDInsightClusterName.azurehdinsight.net`. Till exempel `myHDICluster.azurehdinsight.net`. Obs: `HDInsightClusterName-int.azurehdinsight.net` stöds så länge klienten VM är peered till samma virtuella nätverk. |
   |  Port |Använd **443**. (Den här porten har ändrats från 563 till 443.) |
   |  Databas |Använd **standard**. |
   |  Mekanism |Välj **Windows Azure HDInsight-tjänst** |
   |  Användarnamn |Ange HTTP-användarens användarnamn för HDInsight-kluster. Standardanvändarnamnet är **admin**. |
   |  lösenord |Ange användarlösenord för HDInsight-kluster. Markera kryssrutan **Spara lösenord (krypterat)**.|

1. Valfritt: Välj **avancerade alternativ...**  

   | Parameter | Beskrivning |
   | --- | --- |
   |  Använda inbyggd fråga |När den är markerad försöker ODBC-drivrutinen INTE konvertera TSQL till HiveQL. Du ska bara använda den om du är 100% säker på att du skickar in rena HiveQL-uttalanden. När du ansluter till SQL Server eller Azure SQL Database bör du lämna den omarkerad. |
   |  Rader som hämtats per block |När du hämtar ett stort antal poster kan det krävas att den här parametern justeras för att säkerställa optimala prestanda. |
   |  Standardsträngkolumnlängd, binär kolumnlängd, Decimalkolumnskala |Datatypens längder och precisioner kan påverka hur data returneras. De orsakar felaktig information som ska returneras på grund av förlust av precision och / eller trunkering. |

    ![Avancerade DSN-konfigurationsalternativ](./media/apache-hadoop-connect-excel-hive-odbc-driver/hiveodbc-datasource-advancedoptions1.png "Avancerade DSN-konfigurationsalternativ")

1. Välj **Testa** om du vill testa datakällan. När datakällan är korrekt konfigurerad visar testresultatet **FRAMGÅNG!**.  

1. Välj **OK** för att stänga testfönstret.  

1. Välj **OK** för att stänga installationsfönstret för **Microsoft Hive ODBC Driver DSN.**  

1. Välj **OK** för att stänga fönstret ADMINISTRATÖR FÖR **ODBC-datakälla.**  

## <a name="import-data-into-excel-from-hdinsight"></a>Importera data till Excel från HDInsight

I följande steg beskrivs hur data från en Hive-tabell importeras till en Excel-arbetsbok med hjälp av den ODBC-datakälla som du skapade i föregående avsnitt.

1. Öppna en ny eller befintlig arbetsbok i Excel.

2. Från fliken **Data** navigerar du till **Hämta data** > **från andra källor** > **från ODBC** för att starta **fönstret Från ODBC.**

    ![Öppna guiden För dataanslutning till Excel](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-excel-dataconnection1.png "Öppna guiden För dataanslutning till Excel")

3. Välj det datakällnamn som du skapade i det sista avsnittet i listrutan och välj sedan **OK**.

4. För första användningen öppnas en **ODBC-drivrutinsdialogruta.** Välj **Windows** på den vänstra menyn. Välj sedan **Anslut** för att öppna **navigatorfönstret.**

5. Från **Navigator**navigerar du till HIVE-standarduppkvalstabell **HIVE** > **default** > **hivesampletable**och väljer sedan **Läs in**. Det tar en stund innan data importeras till Excel.

    ![HDInsight Excel Hive ODBC-navigator](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight-hive-odbc-navigator.png "HDInsight Excel Hive ODBC-navigator")

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig hur du använder Microsoft Hive ODBC-drivrutinen för att hämta data från HDInsight-tjänsten till Excel. På samma sätt kan du hämta data från HDInsight-tjänsten till SQL Database. Det är också möjligt att ladda upp data till en HDInsight-tjänst. Du kan läsa mer här:

* [Visualisera Apache Hive-data med Microsoft Power BI i Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualisera interaktiva frågeinsdata med Power BI i Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Använd Apache Zeppelin för att köra Apache Hive-frågor i Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Anslut Excel till Apache Hadoop med Power Query](apache-hadoop-connect-excel-power-query.md).
* [Anslut till Azure HDInsight och kör Apache Hive-frågor med DataSjöverktyg för Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Använd Azure HDInsight Tool för Visual Studio-kod](../hdinsight-for-vscode.md).
* [Ladda upp data till HDInsight](./../hdinsight-upload-data.md).
