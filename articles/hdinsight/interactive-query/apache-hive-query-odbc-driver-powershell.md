---
title: Fråga Apache Hive ODBC-drivrutinen och PowerShell - Azure HDInsight
description: Använda Microsoft Hive ODBC-drivrutinen och PowerShell för att fråga Apache Hive-kluster på Azure HDInsight.
keywords: hive hive odbc powershell
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 06/27/2019
ms.author: hrasheed
ms.openlocfilehash: b02c865e953861b5ac396538fdd0f0623b0e5428
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486104"
---
# <a name="tutorial-query-apache-hive-with-odbc-and-powershell"></a>Självstudier: Fråga Apache Hive ODBC-och PowerShell

Microsoft ODBC-drivrutiner ger ett flexibelt sätt att interagera med olika typer av datakällor, inklusive Apache Hive. Du kan skriva kod i skriptspråk som PowerShell som använder ODBC-drivrutiner för att öppna en anslutning till ditt Hive-kluster, skicka en fråga välja och visa resultatet.

I den här självstudien gör du följande uppgifter:

> [!div class="checklist"]
> * Ladda ned och installera Microsoft Hive ODBC-drivrutin
> * Skapa ett Apache Hive ODBC-datakälla kopplad till ditt kluster
> * Läsa exemplet information från ditt kluster med hjälp av PowerShell

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här självstudiekursen behöver du följande:

* Ett interaktivt frågekluster på HDInsight. Om du vill skapa en [Kom igång med Azure HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Välj **interaktiv fråga** som klustertyp av.

## <a name="install-microsoft-hive-odbc-driver"></a>Installera Microsoft Hive ODBC-drivrutin

Ladda ned och installera den [Microsoft Hive ODBC-drivrutin](https://go.microsoft.com/fwlink/?LinkID=286698).

## <a name="create-apache-hive-odbc-data-source"></a>Skapa Apache Hive ODBC-datakälla

Följande steg visar hur du skapar ett Apache Hive ODBC-datakälla.

1. Från Windows, navigerar du till **starta** > **Windows Administrationsverktyg** > **ODBC-datakällor (32-bit)/(64-bit)** .  En **ODBC Data Source Administrator** öppnas.

    ![Datakälla för OBDC](./media/apache-hive-query-odbc-driver-powershell/hive-odbc-driver-dsn-setup.png "konfigurera en DNS med hjälp av ODBC-datakälla")

1. Från den **användar-DSN** fliken **Lägg till** att öppna den **Skapa ny datakälla** fönster.

1. Välj **Microsoft Hive ODBC-drivrutin**, och välj sedan **Slutför** att öppna den **Microsoft Hive ODBC-drivrutinen DSN för** fönster.

1. Ange eller välj följande värden:

   | Egenskap | Beskrivning |
   | --- | --- |
   |  Namn på datakälla |Namnge din datakälla |
   |  Värdar |Ange `CLUSTERNAME.azurehdinsight.net`. Till exempel, `myHDICluster.azurehdinsight.net` |
   |  Port |Använd **443**.|
   |  Databas |Använd **standard**. |
   |  Mekanism |Välj **Windows Azure HDInsight-tjänsten** |
   |  Användarnamn |Ange HDInsight-kluster HTTP användarens användarnamn. Standardanvändarnamnet är **admin**. |
   |  Lösenord |Ange användarlösenord för HDInsight-kluster. Markera kryssrutan **spara lösenord (krypterade)** .|

1. Valfritt: Välj **avancerade alternativ**.  

   | Parameter | Beskrivning |
   | --- | --- |
   |  Använda Internfråga |När det är valt försöker ODBC-drivrutinen inte att konvertera TSQL till HiveQL. Använd det här alternativet endast om du är 100% se till att du skickar ren HiveQL-instruktioner. När du ansluter till SQL Server eller Azure SQL Database, bör du lämna det avmarkerat. |
   |  Rader hämtade per block |När du hämtar ett stort antal poster, kan justera den här parametern krävas att säkerställa optimala prestanda. |
   |  Standardlängden för sträng-kolumnen, binär Kolumnlängd, Decimal kolumnskalan |Datatypen längder och Precision-datorerna kan påverka hur data returneras. De kan orsaka felaktig information som ska returneras på grund av förlust av precision och trunkering. |

    ![Avancerade alternativ](./media/apache-hive-query-odbc-driver-powershell/odbc-data-source-advanced-options.png "avancerade DSN konfigurationsalternativ")

1. Välj **testa** att testa datakällan. När datakällan är korrekt konfigurerad, visar testresultatet **lyckades**.  

1. Välj **OK** att stänga testfönstret.  

1. Välj **OK** att Stäng den **Microsoft Hive ODBC-drivrutinen DSN för** fönster.  

1. Välj **OK** att Stäng den **ODBC Data Source Administrator** fönster.  

## <a name="query-data-with-powershell"></a>Fråga efter data med PowerShell

Följande PowerShell-skript är en funktion som ODBC att fråga en Hive-kluster.

```powershell
function Get-ODBC-Data {

   param(
   [string]$query=$(throw 'query is required.'),
   [string]$dsn,  
   [PSCredential] $cred = (Get-Credential)  
   )

   $conn = New-Object System.Data.Odbc.OdbcConnection
   $uname = $cred.UserName

   $pswd = (New-Object System.Net.NetworkCredential -ArgumentList "", $cred.Password).Password
   $conn.ConnectionString = "DSN=$dsn;Uid=$uname;Pwd=$pswd;"
   $conn.open()
   $cmd = New-object System.Data.Odbc.OdbcCommand($query,$conn)

   $ds = New-Object system.Data.DataSet

   (New-Object system.Data.odbc.odbcDataAdapter($cmd)).fill($ds) #| out-null
   $conn.close()
   $ds.Tables
}
```

Följande kodavsnitt använder funktionen ovan för att köra en fråga på klustret för interaktiv fråga som du skapade i början av självstudien. Ersätt `DATASOURCENAME` med den **datakällnamn** som du angav på det **Microsoft Hive ODBC-drivrutinen DSN för** skärmen. När du tillfrågas om autentiseringsuppgifter, anger du användarnamnet och lösenordet som du angav under **användarnamnet för klusterinloggning** och **inloggningslösenordet för klustret** när du skapade klustret.

```powershell

$dsn = "DATASOURCENAME"

$query = "select count(distinct clientid) AS total_clients from hivesampletable"

Get-ODBC-Data -query $query -dsn $dsn
```

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, HDInsight-klustret och storage-konto när de inte längre behövs. Välj den resursgrupp där klustret har skapats för att göra det, och klicka på **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien beskrivs hur du använder Microsoft Hive ODBC-drivrutinen och PowerShell för att hämta data från ditt interaktiv fråga i Azure HDInsight-kluster.

> [!div class="nextstepaction"]
> [Ansluta Excel till Apache Hive med hjälp av ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
