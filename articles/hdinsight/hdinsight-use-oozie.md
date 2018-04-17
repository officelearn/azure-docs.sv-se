---
title: Använd Hadoop Oozie i HDInsight | Microsoft Docs
description: Använd Hadoop Oozie i HDInsight, en stordatatjänst. Lär dig hur du definierar ett arbetsflöde för Oozie och skicka ett Oozie-jobb.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 870098f0-f416-4491-9719-78994bf4a369
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: a3c7252be926c6e6b2f9116efb652b888154e46a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="use-oozie-with-hadoop-to-define-and-run-a-workflow-in-hdinsight"></a>Använda Oozie med Hadoop för att definiera och köra ett arbetsflöde i HDInsight
[!INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

Lär dig använda Apache Oozie att definiera ett arbetsflöde och köra arbetsflödet på HDInsight. Läs om Oozie-koordinator i [använda tidsbaserade Hadoop Oozie-koordinator med HDInsight][hdinsight-oozie-coordinator-time]. Information om Azure Data Factory finns [Use Pig och Hive med Data Factory][azure-data-factory-pig-hive].

Apache Oozie är ett arbetsflöde/samordning system som hanterar Hadoop-jobb. Det är integrerat med Hadoop-stacken och stöder Hadoop-jobb för Apache MapReduce, Apache Pig, Apache Hive och Apache Sqoop. Det kan också användas för att schemalägga jobb som är specifika för ett system, t.ex. Java-program eller kommandoskript.

Arbetsflödet som du implementerar genom att följa anvisningarna i den här självstudiekursen innehåller två åtgärder:

![Arbetsflödesdiagram][img-workflow-diagram]

1. En Hive-åtgärden körs ett HiveQL-skript för att räkna antalet förekomster av varje loggningsnivån typ i en log4j-fil. Varje log4j-fil består av en rad med fält som innehåller ett [LOGGNINGSNIVÅ] fält som visar vilken typ och allvarlighetsgrad, till exempel:
   
        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...
   
    Utdata för Hive-skriptet är ungefär:
   
        [DEBUG] 434
        [ERROR] 3
        [FATAL] 1
        [INFO]  96
        [TRACE] 816
        [WARN]  4
   
    Mer information om Hive finns i [Använda Hive med HDInsight][hdinsight-use-hive].
2. En åtgärd för Sqoop exporterar HiveQL utdata till en tabell i Azure SQL-databas. Läs mer om Sqoop [Använd Hadoop Sqoop med HDInsight][hdinsight-use-sqoop].

> [!NOTE]
> Versioner som stöds Oozie i HDInsight-kluster, se [vad är nytt i Hadoop-klusterversioner som tillhandahålls av HDInsight?] [hdinsight-versions].
> 
> 

### <a name="prerequisites"></a>Förutsättningar
Innan du påbörjar den här självstudien måste du ha följande objekt:

* **En arbetsstation med Azure PowerShell**. 
  

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]
  

## <a name="define-oozie-workflow-and-the-related-hiveql-script"></a>Definiera Oozie arbetsflödet och relaterade HiveQL-skript
Oozie arbetsflöden definitioner skrivs i hPDL (en XML-processen Definition Language). Standardfilnamnet för arbetsflödet är *workflow.xml*. Följande är arbetsflödesfilen som du använder i den här kursen.

    <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
        <start to = "RunHiveScript"/>

        <action name="RunHiveScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                    <property>
                        <name>mapred.job.queue.name</name>
                        <value>${queueName}</value>
                    </property>
                </configuration>
                <script>${hiveScript}</script>
                <param>hiveTableName=${hiveTableName}</param>
                <param>hiveDataFolder=${hiveDataFolder}</param>
                <param>hiveOutputFolder=${hiveOutputFolder}</param>
            </hive>
            <ok to="RunSqoopExport"/>
            <error to="fail"/>
        </action>

        <action name="RunSqoopExport">
            <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                    <property>
                        <name>mapred.compress.map.output</name>
                        <value>true</value>
                    </property>
                </configuration>
            <arg>export</arg>
            <arg>--connect</arg>
            <arg>${sqlDatabaseConnectionString}</arg>
            <arg>--table</arg>
            <arg>${sqlDatabaseTableName}</arg>
            <arg>--export-dir</arg>
            <arg>${hiveOutputFolder}</arg>
            <arg>-m</arg>
            <arg>1</arg>
            <arg>--input-fields-terminated-by</arg>
            <arg>"\001"</arg>
            </sqoop>
            <ok to="end"/>
            <error to="fail"/>
        </action>

        <kill name="fail">
            <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>

        <end name="end"/>
    </workflow-app>

Det finns två åtgärder som definierats i arbetsflödet. Start-till-åtgärden är *RunHiveScript*. Om instruktionen körs utan problem, är nästa åtgärd *RunSqoopExport*.

RunHiveScript har flera variabler. Du kan skicka värdena när du har skickat jobbet Oozie från din arbetsstation med hjälp av Azure PowerShell.

<table border = "1">
<tr><th>Arbetsflödesvariabler</th><th>Beskrivning</th></tr>
<tr><td>${jobTracker}</td><td>Anger Webbadressen för Spårare för Hadoop-jobb. Använd <strong>jobtrackerhost:9010</strong> i HDInsight version 3.0 och 2.1.</td></tr>
<tr><td>${nameNode}</td><td>Anger URL för noden Hadoop namn. Använda filen system standardadressen, till exempel <i>wasb: / /&lt;containerName&gt;@&lt;storageAccountName&gt;. blob.core.windows.net</i>.</td></tr>
<tr><td>${Könamn}</td><td>Anger namnet på kön som jobbet skickas till. Använd den <strong>standard</strong>.</td></tr>
</table>

<table border = "1">
<tr><th>Hive variabel</th><th>Beskrivning</th></tr>
<tr><td>${hiveDataFolder}</td><td>Anger källkatalogen för Hive Create Table-kommando.</td></tr>
<tr><td>${hiveOutputFolder}</td><td>Anger den utgående mappen för instruktionen INSERT skriva över.</td></tr>
<tr><td>${hiveTableName}</td><td>Anger namnet på den Hive-tabell som refererar till log4j-datafiler.</td></tr>
</table>

<table border = "1">
<tr><th>Sqoop variabel</th><th>Beskrivning</th></tr>
<tr><td>${sqlDatabaseConnectionString}</td><td>Anger anslutningssträngen för Azure SQL-databasen.</td></tr>
<tr><td>${sqlDatabaseTableName}</td><td>Anger Azure SQL database-tabellen där data exporteras till.</td></tr>
<tr><td>${hiveOutputFolder}</td><td>Anger den utgående mappen för instruktionen Hive Infoga skriva över. Det här är samma mapp för Sqoop exporten (export-dir).</td></tr>
</table>

Läs mer om Oozie arbetsflödet och använder arbetsflödesåtgärder [Apache Oozie 4.0 dokumentationen] [ apache-oozie-400] (för HDInsight version 3.0) eller [Apache Oozie 3.3.2 dokumentationen] [ apache-oozie-332] (för HDInsight version 2.1).

Åtgärden Hive i arbetsflödet anropar en skriptfil för HiveQL. Den här skriptfilen innehåller tre HiveQL-instruktioner:

    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

1. **DROP TABLE-instruktionen** tar bort log4j Hive-tabell om den finns.
2. **Instruktionen CREATE TABLE** skapar en log4j Hive extern tabell som pekar på platsen för filen log4j. Fältavgränsaren är ””,. Standard rad avgränsaren är ”\n”. En extern tabell Hive används för att undvika att filen tas bort från den ursprungliga platsen om du vill köra arbetsflödet Oozie flera gånger.
3. **Instruktionen INSERT över** räknar förekomster av varje loggningsnivån typ från log4j Hive-tabell och sparar utdata till en blobb i Azure Storage.

Det finns tre variabler som används i skriptet:

* ${hiveTableName}
* ${hiveDataFolder}
* ${hiveOutputFolder}

Definitionsfilen för arbetsflöde (workflow.xml i den här självstudiekursen) skickar dessa värden till skriptet HiveQL vid körning.

Både arbetsflödesfilen och HiveQL filen lagras i en blob-behållare.  PowerShell-skriptet som du använder senare i den här kursen kopierar filer till standardkontot för lagring. 

## <a name="submit-oozie-jobs-using-powershell"></a>Skicka Oozie jobb med hjälp av PowerShell
Azure PowerShell tillhandahåller inte för närvarande alla cmdlets för att definiera Oozie jobb. Du kan använda den **Invoke-RestMethod** för att anropa Oozie-webbtjänster. Oozie web services API är en HTTP-REST-API för JSON. Mer information om webbtjänster Oozie API finns [Apache Oozie 4.0 dokumentationen] [ apache-oozie-400] (för HDInsight version 3.0) eller [Apache Oozie 3.3.2 dokumentationen] [ apache-oozie-332] (för HDInsight version 2.1).

PowerShell-skript i det här avsnittet utför följande steg:

1. Ansluta till Azure.
2. Skapa en Azure-resursgrupp. Mer information finns i [Använd Azure PowerShell med Azure Resource Manager](../powershell-azure-resource-manager.md).
3. Skapa en Azure SQL Database-server, en Azure SQL database och två tabeller. Dessa används för åtgärden Sqoop i arbetsflödet.
   
    Tabellnamnet är *log4jLogCount*.
4. Skapa ett HDInsight-kluster som används för att köra Oozie jobb.
   
    Du kan använda Azure-portalen eller Azure PowerShell för att undersöka klustret.
5. Kopiera filen oozie arbetsflödet och HiveQL skriptfilen till filsystemet.
   
    Filer som lagras i en offentlig Blob-behållare.
   
   * Kopiera HiveQL-skript (useoozie.hql) till Azure Storage (wasb:///tutorials/useoozie/useoozie.hql).
   * Kopiera workflow.xml till wasb:///tutorials/useoozie/workflow.xml.
   * Kopiera datafilen (/ example/data/sample.log) till wasb:///tutorials/useoozie/data/sample.log.
6. Skicka ett Oozie-jobb.
   
    Om du vill granska resultaten för OOzie-jobbet använder du Visual Studio eller andra verktyg för att ansluta till Azure SQL-databasen.

Här är skriptet.  Du kan köra skriptet från Windows PowerShell ISE. Du behöver bara konfigurera först 7 variabler.

    #region - provide the following values

    $subscriptionID = "<Enter your Azure subscription ID>"

    # SQL Database server login credentials used for creating and connecting
    $sqlDatabaseLogin = "<Enter SQL Database Login Name>"
    $sqlDatabasePassword = "<Enter SQL Database Login Password>"

    # HDInsight cluster HTTP user credential used for creating and connectin
    $httpUserName = "admin"  # The default name is "admin"
    $httpPassword = "<Enter HDInsight Cluster HTTP User Password>"

    # Used for creating Azure service names
    $nameToken = "<Enter an Alias>"
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
    #endregion

    #region - variables

    # Resource group variables
    $resourceGroupName = $namePrefix + "rg"
    $location = "East US 2" # used by all Azure services defined in this tutorial

    # SQL database varialbes
    $sqlDatabaseServerName = $namePrefix + "sqldbserver"
    $sqlDatabaseName = $namePrefix + "sqldb"
    $sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
    $sqlDatabaseMaxSizeGB = 10

    # Used for retrieving external IP address and creating firewall rules
    $ipAddressRestService = "http://bot.whatismyipaddress.com"
    $fireWallRuleName = "UseSqoop"

    # HDInsight variables
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName
    #endregion

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{
        Login-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionId $subscriptionID
    }
    #endregion

    #region - Create Azure resouce group
    Write-Host "`nCreating an Azure resource group ..." -ForegroundColor Green
    try{
        Get-AzureRmResourceGroup -Name $resourceGroupName
    }
    catch{
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    }
    #endregion

    #region - Create Azure SQL database server
    Write-Host "`nCreating an Azure SQL Database server ..." -ForegroundColor Green
    try{
        Get-AzureRmSqlServer -ServerName $sqlDatabaseServerName -ResourceGroupName $resourceGroupName}
    catch{
        Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green

        $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
        $sqlLoginCredentials = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)

        $sqlDatabaseServerName = (New-AzureRmSqlServer `
                                    -ResourceGroupName $resourceGroupName `
                                    -ServerName $sqlDatabaseServerName `
                                    -SqlAdministratorCredentials $sqlLoginCredentials `
                                    -Location $location).ServerName
        Write-Host "`tThe new SQL database server name is $sqlDatabaseServerName." -ForegroundColor Cyan

        Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
        $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
        New-AzureRmSqlServerFirewallRule `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -FirewallRuleName "$fireWallRuleName-workstation" `
            -StartIpAddress $workstationIPAddress `
            -EndIpAddress $workstationIPAddress

        #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. 
        #Note that this allows Azure traffic from any Azure subscription to access the server.
        New-AzureRmSqlServerFirewallRule `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -FirewallRuleName "$fireWallRuleName-Azureservices" `
            -StartIpAddress "0.0.0.0" `
            -EndIpAddress "0.0.0.0"
    }
    #endregion

    #region - Create and validate Azure SQL database
    Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green

    try {
        Get-AzureRmSqlDatabase `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -DatabaseName $sqlDatabaseName
    }
    catch {
        New-AzureRMSqlDatabase `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -DatabaseName $sqlDatabaseName `
            -Edition "Standard" `
            -RequestedServiceObjectiveName "S1"
    }
    #endregion

    #region - Create SQL database tables
    Write-Host "Creating the log4jlogs table  ..." -ForegroundColor Green

    $sqlDatabaseTableName = "log4jLogsCount"
    $cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
            [Level] [nvarchar](10) NOT NULL,
            [Total] float,
        CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
        (
        [Level] ASC
        )
        )"

    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = $sqlDatabaseConnectionString
    $conn.Open()

    # Create the log4jlogs table and index
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.Connection = $conn
    $cmd.CommandText = $cmdCreateLog4jCountTable
    $cmd.ExecuteNonQuery()

    $conn.close()
    #endregion

    #region - Create HDInsight cluster

    Write-Host "Creating the HDInsight cluster and the dependent services ..." -ForegroundColor Green

    # Create the default storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_LRS

    # Create the default Blob container
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext `
                                        -StorageAccountName $defaultStorageAccountName `
                                        -StorageAccountKey $defaultStorageAccountKey 
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageAccountContext 

    # Create the HDInsight cluster
    $pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $HDInsightClusterName `
        -Location $location `
        -ClusterType Hadoop `
        -OSType Windows `
        -ClusterSizeInNodes 2 `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultBlobContainerName 

    # Validate the cluster
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName
    #endregion

    #region - copy Oozie workflow and HiveQL files

    Write-Host "Copy workflow definition and HiveQL script file ..." -ForegroundColor Green

    # Both files are stored in a public Blob
    $publicBlobContext = New-AzureStorageContext -StorageAccountName "hditutorialdata" -Anonymous

    # WASB folder for storing the Oozie tutorial files.
    $destFolder = "tutorials/useoozie"  # Do NOT use the long path here

    Start-CopyAzureStorageBlob `
        -Context $publicBlobContext `
        -SrcContainer "useoozie" `
        -SrcBlob "useooziewf.hql"  `
        -DestContext $defaultStorageAccountContext `
        -DestContainer $defaultBlobContainerName `
        -DestBlob "$destFolder/useooziewf.hql" `
        -Force

    Start-CopyAzureStorageBlob `
        -Context $publicBlobContext `
        -SrcContainer "useoozie" `
        -SrcBlob "workflow.xml"  `
        -DestContext $defaultStorageAccountContext `
        -DestContainer $defaultBlobContainerName `
        -DestBlob "$destFolder/workflow.xml" `
        -Force

    #validate the copy
    Get-AzureStorageBlob `
        -Context $defaultStorageAccountContext `
        -Container $defaultBlobContainerName `
        -Blob $destFolder/workflow.xml

    Get-AzureStorageBlob `
        -Context $defaultStorageAccountContext `
        -Container $defaultBlobContainerName `
        -Blob $destFolder/useooziewf.hql

    #endregion

    #region - copy the sample.log file

    Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green

    Start-CopyAzureStorageBlob `
        -Context $defaultStorageAccountContext `
        -SrcContainer $defaultBlobContainerName `
        -SrcBlob "example/data/sample.log"  `
        -DestContext $defaultStorageAccountContext `
        -DestContainer $defaultBlobContainerName `
        -destBlob "$destFolder/data/sample.log" 

    #validate the copy
    Get-AzureStorageBlob `
        -Context $defaultStorageAccountContext `
        -Container $defaultBlobContainerName `
        -Blob $destFolder/data/sample.log

    #endregion

    #region - submit Oozie job

    $storageUri="wasb://$defaultBlobContainerName@$defaultStorageAccountName.blob.core.windows.net"

    $oozieJobName = $namePrefix + "OozieJob"

    #Oozie WF variables
    $oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
    $waitTimeBetweenOozieJobStatusCheck=10

    #Hive action variables
    $hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
    $hiveTableName = "log4jlogs"
    $hiveDataFolder = "$storageUri/tutorials/useoozie/data"
    $hiveOutputFolder = "$storageUri/tutorials/useoozie/output"

    #Sqoop action variables
    $sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"

    $OoziePayload =  @"
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

    <property>
        <name>nameNode</name>
        <value>$storageUrI</value>
    </property>

    <property>
        <name>jobTracker</name>
        <value>jobtrackerhost:9010</value>
    </property>

    <property>
        <name>queueName</name>
        <value>default</value>
    </property>

    <property>
        <name>oozie.use.system.libpath</name>
        <value>true</value>
    </property>

    <property>
        <name>hiveScript</name>
        <value>$hiveScript</value>
    </property>

    <property>
        <name>hiveTableName</name>
        <value>$hiveTableName</value>
    </property>

    <property>
        <name>hiveDataFolder</name>
        <value>$hiveDataFolder</value>
    </property>

    <property>
        <name>hiveOutputFolder</name>
        <value>$hiveOutputFolder</value>
    </property>

    <property>
        <name>sqlDatabaseConnectionString</name>
        <value>&quot;$sqlDatabaseConnectionString&quot;</value>
    </property>

    <property>
        <name>sqlDatabaseTableName</name>
        <value>$SQLDatabaseTableName</value>
    </property>

    <property>
        <name>user.name</name>
        <value>$httpUserName</value>
    </property>

    <property>
        <name>oozie.wf.application.path</name>
        <value>$oozieWFPath</value>
    </property>

    </configuration>
    "@

    Write-Host "Checking Oozie server status..." -ForegroundColor Green
    $clusterUriStatus = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/admin/status"
    $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $httpCredential -OutVariable $OozieServerStatus

    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
    $oozieServerSatus = $jsonResponse[0].("systemMode")
    Write-Host "Oozie server status is $oozieServerSatus."

    # create Oozie job
    Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
    Write-Host "`n--------`n$OoziePayload`n--------"
    $clusterUriCreateJob = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/jobs"
    $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $httpCredential -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName #-debug

    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
    $oozieJobId = $jsonResponse[0].("id")
    Write-Host "Oozie job id is $oozieJobId..."

    # start Oozie job
    Write-Host "Starting the Oozie job $oozieJobId..." -ForegroundColor Green
    $clusterUriStartJob = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=start"
    $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $httpCredential | Format-Table -HideTableHeaders #-debug

    # get job status
    Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
    Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck

    Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
    $clusterUriGetJobStatus = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
    $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $httpCredential
    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
    $JobStatus = $jsonResponse[0].("status")

    while($JobStatus -notmatch "SUCCEEDED|KILLED")
    {
        Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
        Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $httpCredential
        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $JobStatus = $jsonResponse[0].("status")
        $jobStatus
    }

    Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!" -ForegroundColor Green

    #endregion


**Att köra guiden igen**

Om du vill köra arbetsflödet igen, måste du ta bort följande objekt:

* Utdatafilen Hive-skript
* Data i tabellen log4jLogsCount

Här är ett PowerShell-skript som du kan använda:

    $resourceGroupName = "<AzureResourceGroupName>"

    $defaultStorageAccountName = "<AzureStorageAccountName>"
    $defaultBlobContainerName = "<ContainerName>"

    #SQL database variables
    $sqlDatabaseServerName = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabasePassword = "<SQLDatabaseLoginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"
    $sqlDatabaseTableName = "log4jLogsCount"

    Write-host "Delete the Hive script output file ..." -ForegroundColor Green
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
    Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $defaultBlobContainerName

    Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
    $conn.open()
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.connection = $conn
    $cmd.commandtext = "delete from $sqlDatabaseTableName"
    $cmd.executenonquery()

    $conn.close()

## <a name="next-steps"></a>Nästa steg
I kursen får du har lärt dig hur du definierar ett arbetsflöde för Oozie och hur du kör ett Oozie-jobb med hjälp av PowerShell. Mer information finns i följande artiklar:

* [Använd tidsbaserad Oozie-koordinator med HDInsight][hdinsight-oozie-coordinator-time]
* [Komma igång med Hadoop med Hive i HDInsight för att analysera mobila luren användning][hdinsight-get-started]
* [Använda Azure Blob storage med HDInsight][hdinsight-storage]
* [Administrera HDInsight med hjälp av PowerShell][hdinsight-admin-powershell]
* [Överföra data för Hadoop-jobb i HDInsight][hdinsight-upload-data]
* [Använda Sqoop med Hadoop i HDInsight][hdinsight-use-sqoop]
* [Använda Hive med Hadoop i HDInsight][hdinsight-use-hive]
* [Använda Pig med Hadoop i HDInsight][hdinsight-use-pig]
* [Utveckla Java-MapReduce-program för HDInsight][hdinsight-develop-mapreduce]

[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563



[azure-data-factory-pig-hive]: ../data-factory/transform-data.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md


[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-develop-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[sqldatabase-create-configue]: ../sql-database-create-configure.md
[sqldatabase-get-started]: ../sql-database-get-started.md

[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/en-us/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png  
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
