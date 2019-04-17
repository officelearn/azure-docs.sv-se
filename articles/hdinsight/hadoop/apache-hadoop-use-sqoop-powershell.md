---
title: Kör Apache Sqoop jobb med hjälp av PowerShell och Azure HDInsight
description: Lär dig hur du använder Azure PowerShell på en arbetsstation för att köra Apache Sqoop-import och export mellan ett Apache Hadoop-kluster och en Azure SQL database.
ms.reviewer: jasonh
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: hrasheed
ms.openlocfilehash: fb15a6389577e8d132b9c5a895a9995a9f7da208
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59607371"
---
# <a name="run-apache-sqoop-jobs-by-using-azure-powershell-for-apache-hadoop-in-hdinsight"></a>Kör Apache Sqoop jobb med hjälp av Azure PowerShell för Apache Hadoop i HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Lär dig hur du använder Azure PowerShell för att köra Apache Sqoop jobb i Azure HDInsight för att importera och exportera data mellan ett HDInsight-kluster och en Azure SQL database eller SQL Server-databas. Det här exemplet exporterar data från `/tutorials/usesqoop/data/sample.log` från standardkontot för lagring och importerar den till en tabell kallas `log4jlogs` i en SQL Server-databas. Den här artikeln är en förlängning av [Använd Apache Sqoop med Hadoop i HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar den här självstudiekursen behöver du följande:

* En arbetsstation med Azure PowerShell [AZ modulen](https://docs.microsoft.com/powershell/azure/overview) installerad.

* Slutförandet av [skapa testmiljö](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) från [Använd Apache Sqoop med Hadoop i HDInsight](./hdinsight-use-sqoop.md).


## <a name="run-apache-sqoop-by-using-powershell"></a>Kör Apache Sqoop med hjälp av PowerShell
Följande PowerShell-skript före bearbetar källfilen och exporterar den till en Azure SQL database till tabellen `log4jlogs`. Ersätt `CLUSTERNAME`, `CLUSTERPASSWORD`, och `SQLPASSWORD` med de värden som du använde från nödvändiga.

```powershell 
<#------ BEGIN USER INPUT ------#>
$hdinsightClusterName = "CLUSTERNAME"
$httpUserName = "admin"  #default is admin, update as needed
$httpPassword = 'CLUSTERPASSWORD'
$sqlDatabasePassword = 'SQLPASSWORD'
<#------- END USER INPUT -------#>

# Other fixed variable that should be used as is
$sqlServerName = $hdinsightClusterName + "dbserver"
$sqlDatabaseName = $hdinsightClusterName + "db"
$tableName_log4j = "log4jlogs"
$exportDir_log4j = "/tutorials/usesqoop/data"
$sourceBlobName = "example/data/sample.log"
$destBlobName = "tutorials/usesqoop/data/sample.log"
$sqljdbcdriver = "/user/oozie/share/lib/sqoop/mssql-jdbc-7.0.0.jre8.jar"

$cluster = Get-AzHDInsightCluster -ClusterName $hdinsightClusterName
$defaultStorageAccountName = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
$defaultStorageContainer = $cluster.DefaultStorageContainer
$resourceGroup = $cluster.ResourceGroup

$sqlServer = Get-AzSqlServer -ResourceGroupName $resourceGroup -ServerName $sqlServerName
$sqlServerLogin = $sqlServer.SqlAdministratorLogin
$sqlServerFQDN = $sqlServer.FullyQualifiedDomainName

#Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzContext}
catch{Connect-AzAccount}

#pre-process the source file
Write-Host "`nPreprocessing the source file ..." -ForegroundColor Green

# This procedure creates a new file with $destBlobName
# Define the connection string
$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroup `
                                -Name $defaultStorageAccountName)[0].Value

# Create block blob objects referencing the source and destination blob.
$storageAccount = Get-AzStorageAccount `
    -ResourceGroupName $resourceGroup `
    -Name $defaultStorageAccountName

$storageContainer = ($storageAccount |Get-AzStorageContainer -Name $defaultStorageContainer).CloudBlobContainer

$sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)

# Define a MemoryStream and a StreamReader for reading from the source file
$stream = New-Object System.IO.MemoryStream
$stream = $sourceBlob.OpenRead()
$sReader = New-Object System.IO.StreamReader($stream)

# Define a MemoryStream and a StreamWriter for writing into the destination file
$memStream = New-Object System.IO.MemoryStream
$writeStream = New-Object System.IO.StreamWriter $memStream

# Pre-process the source blob
$exString = "java.lang.Exception:"
while(-Not $sReader.EndOfStream){
    $line = $sReader.ReadLine()
    $split = $line.Split(" ")

    # remove the "java.lang.Exception" from the first element of the array
    # for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
    if ($split[0] -eq $exString){
        #create a new ArrayList to remove $split[0]
        $newArray = [System.Collections.ArrayList] $split
        $newArray.Remove($exString)

        # update $split and $line
        $split = $newArray
        $line = $newArray -join(" ")
    }

    # remove the lines that has less than 7 elements
    if ($split.count -ge 7){
        write-host $line
        $writeStream.WriteLine($line)
    }
}

# Write to the destination blob
$writeStream.Flush()
$memStream.Seek(0, "Begin")
$destBlob.UploadFromStream($memStream)

#export the log file from the cluster to the SQL database
Write-Host "Exporting the log file ..." -ForegroundColor Green

$pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

# Connection string
$connectionString = "jdbc:sqlserver://$sqlServerFQDN;user=$sqlServerLogin@$sqlServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"

# Submit a Sqoop job
$sqoopDef = New-AzHDInsightSqoopJobDefinition `
    -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1" `
    -Files $sqljdbcdriver

$sqoopJob = Start-AzHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef

Wait-AzHDInsightJob `
    -ResourceGroupName $resourceGroup `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzHDInsightJobOutput `
    -ResourceGroupName $resourceGroup `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultStorageContainer `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardError

Write-Host "Standard Output" -BackgroundColor Green
Get-AzHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultStorageContainer `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardOutput
```

## <a name="limitations"></a>Begränsningar
Linux-baserade HDInsight visar följande begränsningar:

* Bulk export: Sqoop-koppling som används för att exportera data till Microsoft SQL Server eller Azure SQL Database stöder för närvarande inte bulkinfogningar.

* Batchbearbetning: Med hjälp av den `-batch` växla när den utför infogningar, Sqoop utför flera infogningar i stället för batchbearbetning insert-åtgärder. 

## <a name="next-steps"></a>Nästa steg
Nu har du lärt dig hur du använder Sqoop. Du kan läsa mer här:

* [Använda Apache Oozie med HDInsight](../hdinsight-use-oozie-linux-mac.md): Använd Sqoop åtgärden i ett Oozie-arbetsflöde.
* [Ladda upp data till HDInsight](../hdinsight-upload-data.md): Hitta andra metoder för att överföra data till HDInsight eller Azure Blob storage.

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
