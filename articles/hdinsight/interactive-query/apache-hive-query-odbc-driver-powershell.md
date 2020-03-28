---
title: Fråga Apache Hive med ODBC-drivrutin & PowerShell - Azure HDInsight
description: Använd Microsoft Hive ODBC-drivrutinen och PowerShell för att fråga Apache Hive-kluster på Azure HDInsight.
keywords: hive, hive odbc, powershell
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 06/27/2019
ms.openlocfilehash: f6139bb98fa0272e43c8e180d4ec029f7a7538bb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73494331"
---
# <a name="tutorial-query-apache-hive-with-odbc-and-powershell"></a>Självstudiekurs: Fråga Apache Hive med ODBC och PowerShell

Microsoft ODBC-drivrutiner är ett flexibelt sätt att interagera med olika typer av datakällor, inklusive Apache Hive. Du kan skriva kod på skriptspråk som PowerShell som använder ODBC-drivrutinerna för att öppna en anslutning till Hive-klustret, skicka en fråga som du väljer och visa resultaten.

I den här självstudien ska du göra följande:

> [!div class="checklist"]
> * Ladda ned och installera Microsoft Hive ODBC-drivrutinen
> * Skapa en Apache Hive ODBC-datakälla som är länkad till klustret
> * Frågeexempelinformation från klustret med PowerShell

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

Innan du börjar den här självstudiekursen behöver du följande:

* Ett interaktivt frågekluster på HDInsight. Information om hur du skapar en information finns [i Komma igång med Azure HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Välj **Interaktiv fråga** som klustertyp.

## <a name="install-microsoft-hive-odbc-driver"></a>Installera Microsoft Hive ODBC-drivrutin

Ladda ned och installera [Microsoft Hive ODBC Driver](https://www.microsoft.com/download/details.aspx?id=40886).

## <a name="create-apache-hive-odbc-data-source"></a>Skapa Apache Hive ODBC-datakälla

Följande steg visar hur du skapar en Apache Hive ODBC-datakälla.

1. Från Windows navigerar du till **Starta** > **Windows Administrativa verktyg** > **ODBC-datakällor (32-bitars)/(64-bitars)**.  Ett **ODBC-datakälladministratörsfönster** öppnas.

    ![OBDC-datakälla administratör](./media/apache-hive-query-odbc-driver-powershell/hive-odbc-driver-dsn-setup.png "Konfigurera ett DSN med ODBC-datakälladministratör")

1. **Öppna** fönstret **Skapa ny datakälla** på fliken **Användar-DSN.**

1. Välj **Microsoft Hive ODBC Driver**och välj sedan **Slutför** för att öppna installationsfönstret för **Microsoft Hive ODBC Driver DSN.**

1. Ange eller välj följande värden:

   | Egenskap | Beskrivning |
   | --- | --- |
   |  Namn på datakälla |Namnge din datakälla |
   |  Värd/program (er) |Ange `CLUSTERNAME.azurehdinsight.net`. Till exempel, `myHDICluster.azurehdinsight.net` |
   |  Port |Använd **443**.|
   |  Databas |Använd **standard**. |
   |  Mekanism |Välj **Windows Azure HDInsight-tjänst** |
   |  Användarnamn |Ange HTTP-användarens användarnamn för HDInsight-kluster. Standardanvändarnamnet är **admin**. |
   |  lösenord |Ange användarlösenord för HDInsight-kluster. Markera kryssrutan **Spara lösenord (krypterat)**.|

1. Valfritt: Välj **Avancerade alternativ**.  

   | Parameter | Beskrivning |
   | --- | --- |
   |  Använda inbyggd fråga |När den är markerad försöker ODBC-drivrutinen INTE konvertera TSQL till HiveQL. Använd det här alternativet endast om du är 100 % säker på att du skickar in rena HiveQL-satser. När du ansluter till SQL Server eller Azure SQL Database bör du lämna den omarkerad. |
   |  Rader som hämtats per block |När du hämtar ett stort antal poster kan det krävas att den här parametern justeras för att säkerställa optimala prestanda. |
   |  Standardsträngkolumnlängd, binär kolumnlängd, Decimalkolumnskala |Datatypens längder och precisioner kan påverka hur data returneras. De orsakar felaktig information som ska returneras på grund av förlust av precision och trunkering. |

    ![Avancerade DSN-konfigurationsalternativ](./media/apache-hive-query-odbc-driver-powershell/odbc-data-source-advanced-options.png "Avancerade DSN-konfigurationsalternativ")

1. Välj **Testa** om du vill testa datakällan. När datakällan är korrekt konfigurerad visar testresultatet **FRAMGÅNG**.  

1. Välj **OK** för att stänga testfönstret.  

1. Välj **OK** för att stänga installationsfönstret för **Microsoft Hive ODBC Driver DSN.**  

1. Välj **OK** för att stänga fönstret ADMINISTRATÖR FÖR **ODBC-datakälla.**  

## <a name="query-data-with-powershell"></a>Fråga data med PowerShell

Följande PowerShell-skript är en funktion som ODBC för att fråga ett Hive-kluster.

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

Följande kodavsnitt använder funktionen ovan för att köra en fråga i det interaktiva frågeklustret som du skapade i början av självstudien. Ersätt `DATASOURCENAME` med det **datakällnamn** som du angav på installationsskärmen för **Microsoft Hive ODBC Driver DSN.** När du uppmanas att ange användarnamn och lösenord som du angav under användarnamn och lösenord för **klusterinloggning** när du skapade klustret. **Cluster login password**

```powershell

$dsn = "DATASOURCENAME"

$query = "select count(distinct clientid) AS total_clients from hivesampletable"

Get-ODBC-Data -query $query -dsn $dsn
```

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs tar du bort resursgruppen, HDInsight-klustret och lagringskontot. Om du vill göra det markerar du den resursgrupp där klustret skapades och klickar på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du använder Microsoft Hive ODBC-drivrutinen och PowerShell för att hämta data från ditt Azure HDInsight Interactive Query-kluster.

> [!div class="nextstepaction"]
> [Ansluta Excel till Apache Hive med ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
