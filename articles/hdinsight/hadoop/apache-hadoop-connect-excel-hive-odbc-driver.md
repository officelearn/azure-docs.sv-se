---
title: Excel & Apache Hadoop med ODBC-drivrutin – Azure HDInsight
description: Lär dig hur du konfigurerar och använder Microsoft Hive ODBC-drivrutin för Excel för att fråga efter data i HDInsight-kluster från Microsoft Excel.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/02/2020
ms.openlocfilehash: f356009197c0446efa2ea2d7f0e90040229df47b
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251074"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Ansluta Excel till Apache Hadoop i Azure HDInsight med Microsoft Hive ODBC-drivrutin

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Microsofts Big data-lösning integrerar Microsoft Business Intelligence-komponenter (BI) med Apache Hadoop kluster som har distribuerats i Azure HDInsight. Ett exempel på denna integrering är möjligheten att ansluta Excel till Hive-datalagret i ett Hadoop-kluster i HDInsight med hjälp av Microsoft Hive Open Database Connectivity-drivrutinen (ODBC).

Det är också möjligt att ansluta data som är associerade med ett HDInsight-kluster och andra data källor, inklusive andra (icke-HDInsight) Hadoop-kluster från Excel med hjälp av Microsoft Power Query-tillägget för Excel. Information om hur du installerar och använder Power Query finns i [ansluta Excel till HDInsight med Power Query](../hdinsight-connect-excel-power-query.md).

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här artikeln måste du ha följande objekt:

* An-HDInsight Hadoop-kluster. Information om hur du skapar ett finns i [Kom igång med Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* En arbets station med Office 2010 Professional Plus eller senare, eller Excel 2010 eller senare.

## <a name="install-microsoft-hive-odbc-driver"></a>Installera Microsoft Hive ODBC-drivrutin

Hämta och installera [Microsoft HIVE ODBC driv rutins](https://www.microsoft.com/download/details.aspx?id=40886) version som matchar versionen av programmet där du använder ODBC-drivrutinen.  I den här artikeln används driv rutinen för Office Excel.

## <a name="create-apache-hive-odbc-data-source"></a>Skapa Apache Hive ODBC-datakälla

Följande steg visar hur du skapar en ODBC-datakälla för Hive.

1. Från Windows navigerar du till Start > administrations verktyg för Windows > ODBC-datakällor (32-bitars)/(64-bitars).  Då öppnas fönstret **ODBC-Administrera data källa** .

    ![OBDC data källans administratör](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-datasourceadmin1.png "Konfigurera en data källa med hjälp av administratör för ODBC-datakälla")

1. På fliken **användar-DSN** väljer du **Lägg till** för att öppna fönstret **Skapa ny data källa** .

1. Välj **Microsoft HIVE ODBC-drivrutin**och välj sedan **Slutför** för att öppna **installations fönstret för Microsoft Hive ODBC-drivrutin** .

1. Ange eller välj följande värden:

   | Egenskap | Beskrivning |
   | --- | --- |
   |  Namn på datakälla |Namnge din datakälla |
   |  Värd (er) |Ange `HDInsightClusterName.azurehdinsight.net`. Till exempel `myHDICluster.azurehdinsight.net`. Obs! `HDInsightClusterName-int.azurehdinsight.net` stöds så länge den virtuella datorn är peer-kopplad till samma virtuella nätverk. |
   |  Port |Använd **443**. (Den här porten har ändrats från 563 till 443.) |
   |  Databas |Använd **standard**. |
   |  Mekanism |Välj **Windows Azure HDInsight-tjänst** |
   |  Användarnamn |Ange HDInsight-kluster HTTP användar namn användar namn. Standardanvändarnamnet är **admin**. |
   |  lösenord |Ange användar lösen ord för HDInsight-kluster. Markera kryss rutan **Spara lösen ord (krypterad)** .|

1. Valfritt: Välj **Avancerade alternativ...**  

   | Parameter | Beskrivning |
   | --- | --- |
   |  Använd intern fråga |När den är markerad försöker inte ODBC-drivrutinen konvertera TSQL till HiveQL. Du får bara använda det om du är 100% säker på att du skickar in ren HiveQL-instruktioner. När du ansluter till SQL Server eller Azure SQL Database bör du lämna det omarkerat. |
   |  Hämtade rader per block |När du hämtar ett stort antal poster kan du behöva justera den här parametern för att säkerställa optimala prestanda. |
   |  Standard sträng kolumn längd, binär kolumn längd, decimal kolumn skala |Data typens längd och precision kan påverka hur data returneras. De gör att felaktig information returneras på grund av förlust av precision och/eller trunkering. |

    ![Avancerade konfigurations alternativ för DSN](./media/apache-hadoop-connect-excel-hive-odbc-driver/hiveodbc-datasource-advancedoptions1.png "Avancerade konfigurations alternativ för DSN")

1. Testa data källan genom att välja **test** . När data källan har kon figurer ATS korrekt visar test resultatet **lyckades!** .  

1. Stäng test fönstret genom att klicka på **OK** .  

1. Välj **OK** för att stänga **installations fönstret för Microsoft Hive ODBC-drivrutin** .  

1. Välj **OK** för att stänga fönstret **ODBC-Administrera data källa** .  

## <a name="import-data-into-excel-from-hdinsight"></a>Importera data till Excel från HDInsight

Följande steg beskriver hur du importerar data från en Hive-tabell till en Excel-arbetsbok med ODBC-datakällan som du skapade i föregående avsnitt.

1. Öppna en ny eller befintlig arbetsbok i Excel.

2. Från fliken **data** navigerar du till **Hämta data** > **från andra källor** > **från ODBC** för att starta **från ODBC-** fönstret.

    ![Öppna guiden data anslutning för Excel](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-excel-dataconnection1.png "Öppna guiden data anslutning för Excel")

3. I list rutan väljer du namnet på den data källa som du skapade i det sista avsnittet och väljer sedan **OK**.

4. För den första användningen öppnas en **ODBC-drivrutins** dialog ruta. Välj **fönster** på den vänstra menyn. Välj **Anslut** för att öppna fönstret **navigatör** .

5. Från **Navigator**navigerar du till **HIVE** > **standard** > **hivesampletable**och väljer sedan **load**. Det tar en stund innan data importeras till Excel.

    ![Excel Hive ODBC-navigatör för HDInsight](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight-hive-odbc-navigator.png "Excel Hive ODBC-navigatör för HDInsight")

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du använder Microsoft Hive ODBC-drivrutin för att hämta data från HDInsight-tjänsten till Excel. På samma sätt kan du hämta data från HDInsight-tjänsten till SQL Database. Det går också att ladda upp data till en HDInsight-tjänst. Du kan läsa mer här:

* [Visualisera Apache Hive data med Microsoft Power BI i Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualisera interaktiva Hive-frågedata med Power BI i Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Använd Apache Zeppelin för att köra apache Hive frågor i Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Anslut Excel till Apache Hadoop med hjälp av Power Query](apache-hadoop-connect-excel-power-query.md).
* [Anslut till Azure HDInsight och kör Apache Hive frågor med hjälp av data Lake verktyg för Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Använd Azure HDInsight-verktyget för Visual Studio Code](../hdinsight-for-vscode.md).
* [Ladda upp data till HDInsight](./../hdinsight-upload-data.md).
