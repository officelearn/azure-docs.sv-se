---
title: Använd tidsbaserade Hadoop Oozie-koordinator i HDInsight
description: Använd tidsbaserade Hadoop Oozie-koordinator i HDInsight, en big data-tjänst. Lär dig hur du definierar Oozie arbetsflöden och koordinatorer och skicka jobb.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.author: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/04/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 7f10990d2e4531be42f0553875bf3a01f0a23f58
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45575668"
---
# <a name="use-time-based-oozie-coordinator-with-hadoop-in-hdinsight-to-define-workflows-and-coordinate-jobs"></a>Använda tidsbaserad Oozie-koordinator med Hadoop i HDInsight för att definiera arbetsflöden och samordna jobb
I den här artikeln får lära du dig hur du definierar arbetsflöden och koordinatorer och hur du utlöser coordinator jobben, baserat på tiden. Är det bra att gå igenom [Använd Oozie med HDInsight] [ hdinsight-use-oozie] innan du läser den här artikeln. Förutom Oozie, kan du också schemalägga jobb med hjälp av Azure Data Factory. Läs Azure Data Factory i [Använd Pig och Hive med Data Factory](../data-factory/transform-data.md).

> [!NOTE]
> Den här artikeln kräver ett Windows-baserade HDInsight-kluster. Information om hur du använder Oozie, inklusive tidsbaserade jobb på en Linux-baserat kluster finns i [Använd Oozie med Hadoop för att definiera och köra ett arbetsflöde på Linux-baserat HDInsight](hdinsight-use-oozie-linux-mac.md)

## <a name="what-is-oozie"></a>Vad är Oozie
Apache Oozie är ett arbetsflödeskoordination/system som hanterar Hadoop-jobb. Det är integrerat med Hadoop-stacken och stöder Hadoop-jobb för Apache MapReduce, Apache Pig, Apache Hive och Apache Sqoop. Det kan också användas för att schemalägga jobb som är specifika för ett system, till exempel Java-program eller kommandoskript.

Följande bild visar arbetsflödet som du kommer att implementera:

![Arbetsflödesdiagram][img-workflow-diagram]

Arbetsflödet innehåller två åtgärder:

1. En Hive-åtgärd körs ett HiveQL-skript för att räkna förekomsterna av varje Loggnivå typ i en log4j-loggfil. Varje log4j logg består av en rad med fält som innehåller ett [LOGGNINGSNIVÅ]-fält för att visa meddelandetyp och allvarlighetsgrad, till exempel:

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
2. En åtgärd som Sqoop exporterar åtgärdsutdata HiveQL till en tabell i en Azure SQL database. Läs mer om Sqoop [Använd Sqoop med HDInsight][hdinsight-use-sqoop].

> [!NOTE]
> Versioner som stöds Oozie på HDInsight-kluster, se [vad är nytt i klusterversionerna från HDInsight?] [hdinsight-versions].
>
>

## <a name="prerequisites"></a>Förutsättningar
Innan du påbörjar de här självstudierna måste du ha:

* **En arbetsstation med Azure PowerShell**.

    > [!IMPORTANT]
    > Stödet för Azure PowerShell för hantering av HDInsight-resurser med hjälp av Azure Service Manager är **föråldrat** och dras tillbaka den 1 januari 2017. I stegen i det här dokumentet används de nya HDInsight-cmdletarna som fungerar med Azure Resource Manager.
    >
    > Följ stegen i [Installera och konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs) för att installera den senaste versionen av Azure PowerShell. Om du har skript som behöver ändras för att använda de nya cmdletarna som fungerar med Azure Resource Manager, hittar du mer information i [Migrera till Azure Resource Manager-baserade utvecklingsverktyg för HDInsight-kluster](hdinsight-hadoop-development-using-azure-resource-manager.md).

* **Ett HDInsight-kluster**. Information om hur du skapar ett HDInsight-kluster finns i [skapa HDInsight-kluster][hdinsight-provision], eller [Kom igång med HDInsight][hdinsight-get-started]. Du måste gå igenom självstudien följande data:

    <table border = "1">
    <tr><th>Kluster-egenskapen</th><th>Variabelnamn för Windows PowerShell</th><th>Värde</th><th>Beskrivning</th></tr>
    <tr><td>HDInsight-klusternamnet</td><td>$clusterName</td><td></td><td>HDInsight-kluster som du vill köra den här självstudien.</td></tr>
    <tr><td>Användarnamn för HDInsight-kluster</td><td>$clusterUsername</td><td></td><td>Användarnamn för HDInsight-kluster. </td></tr>
    <tr><td>Användarlösenord för HDInsight-kluster </td><td>$clusterPassword</td><td></td><td>Användarlösenord för HDInsight-kluster.</td></tr>
    <tr><td>Azure storage-kontonamn</td><td>$storageAccountName</td><td></td><td>Ett Azure Storage-konto är tillgänglig för HDInsight-klustret. Använd standardkontot för lagring som du angav när klustret etablera den här självstudien.</td></tr>
    <tr><td>Azure Blob-behållarnamn</td><td>$containerName</td><td></td><td>I det här exemplet använder du Azure Blob storage-behållaren som används för standardfilsystemet för HDInsight-kluster. Som standard har samma namn som HDInsight-klustret.</td></tr>
    </table>

* **En Azure SQL database**. Du måste konfigurera en brandväggsregel för SQL-databasserver och tillåta åtkomst från din arbetsstation. Mer information om att skapa en Azure SQL-databas och konfigurerar brandväggen finns i [komma igång med Azure SQL-databas][sqldatabase-get-started]. Den här artikeln innehåller ett Windows PowerShell-skript för att skapa Azure SQL database-tabell som ska användas för den här självstudien.

    <table border = "1">
    <tr><th>Egenskapen för SQL-databas</th><th>Variabelnamn för Windows PowerShell</th><th>Värde</th><th>Beskrivning</th></tr>
    <tr><td>SQL server-databasnamn</td><td>$sqlDatabaseServer</td><td></td><td>SQL-databasservern som Sqoop exporterar data. </td></tr>
    <tr><td>SQL database-inloggningsnamn</td><td>$sqlDatabaseLogin</td><td></td><td>Inloggningsnamn för SQL-databas.</td></tr>
    <tr><td>Inloggningslösenordet för SQL-databas</td><td>$sqlDatabaseLoginPassword</td><td></td><td>Inloggningslösenordet för SQL-databas.</td></tr>
    <tr><td>SQL-databasnamn</td><td>$sqlDatabaseName</td><td></td><td>Azure SQL-databasen som Sqoop exporterar data. </td></tr>
    </table>

  > [!NOTE]
  > Som standard tillåter anslutningar från Azure-tjänster, till exempel Azure HDInsight i en Azure SQL database. Om brandväggsinställningen är inaktiverad, måste du aktivera det från Azure Portal. Anvisningar om att skapa en SQL-databas och konfigurera brandväggsregler finns i [skapa och konfigurera SQL Database][sqldatabase-get-started].

> [!NOTE]
> Egna värden i tabeller. Det kan vara bra för att gå igenom den här kursen.

## <a name="define-oozie-workflow-and-the-related-hiveql-script"></a>Definiera Oozie-arbetsflöde och relaterade HiveQL-skript
Oozie arbetsflöden definitioner är skrivna i hPDL (en XML-processen för definition language). Standardfilnamnet för arbetsflödet är *workflow.xml*.  Du spara arbetsflödet lokalt och sedan distribuera den till HDInsight-kluster med hjälp av Azure PowerShell senare i den här självstudien.

Hive-åtgärden i arbetsflödet anropar en skriptfil för HiveQL. Den här skriptfilen innehåller tre HiveQL-instruktioner:

1. **DROP TABLE-instruktionen** tar bort log4j Hive-tabell om den finns.
2. **CREATE TABLE-instruktionen** skapar en log4j Hive extern tabell, som pekar mot platsen för log4j-loggfil;
3. **Platsen för log4j-loggfil**. Fältavgränsaren är ””,. Rad saknas används ”\n”. En extern tabell Hive används för att undvika att filen tas bort från den ursprungliga platsen, om du vill köra Oozie-arbetsflöde flera gånger.
4. **Instruktionen INSERT skriva över** antal förekomster av varje Loggnivå typen från log4j Hive-tabell och sparar resultatet till en Azure Blob storage-plats.

> [!NOTE]
> Det finns ett känt problem i Hive-sökväg. Du ska köra det här problemet när du skickar ett Oozie-jobb. Anvisningar för att åtgärda problemet finns på TechNet Wiki: [HDInsight Hive-fel: Det gick inte att byta namn på][technetwiki-hive-error].

**Definiera filen HiveQL-skript som ska anropas av arbetsflödet**

1. Skapa en textfil med följande innehåll:

        DROP TABLE ${hiveTableName};
        CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
        INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

    Det finns tre variabler som används i skriptet:

   * ${hiveTableName}
   * ${hiveDataFolder}
   * ${hiveOutputFolder}

     Arbetsflöde-definitionsfil (workflow.xml i den här självstudien) skickar dessa värden till den här HiveQL-skript vid körning.
2. Spara filen som **C:\Tutorials\UseOozie\useooziewf.hql** med hjälp av ANSI (ASCII) kodning. (Använd anteckningar om din textredigerare inte tillhandahåller det här alternativet.) Den här skriptfilen kommer att distribueras till HDInsight-klustret senare under kursen.

**Definiera ett arbetsflöde**

1. Skapa en textfil med följande innehåll:

    ```xml
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
    ```

    Det finns två åtgärder som definierats i arbetsflödet. Start-till-åtgärden är *RunHiveScript*. Om åtgärden körs *OK*, nästa åtgärd är *RunSqoopExport*.

    RunHiveScript har flera variabler. Du kan skicka värdena när du har skickat jobbet Oozie från din arbetsstation med hjälp av Azure PowerShell.

    Arbetsflödesvariabler

    <table border = "1">
    <tr><th>Arbetsflödesvariabler</th><th>Beskrivning</th></tr>
    <tr><td>${jobTracker}</td><td>Ange Webbadressen till spårningsverktyget för Hadoop-jobb. Använd <strong>jobtrackerhost:9010</strong> på HDInsight-kluster version 3.0 och 2.0.</td></tr>
    <tr><td>${nameNode}</td><td>Ange URL: en för noden Hadoop namn. Använd standard file system wasb: / / adress, till exempel <i>wasb: / /&lt;containerName&gt;@&lt;storageAccountName&gt;. blob.core.windows.net</i>.</td></tr>
    <tr><td>${queueName}</td><td>Anger namnet på kön som jobbet ska skickas till. Använd <strong>standard</strong>.</td></tr>
    </table>

    Åtgärdsvariabler för hive

    <table border = "1">
    <tr><th>Variabeln för hive-åtgärd</th><th>Beskrivning</th></tr>
    <tr><td>${hiveDataFolder}</td><td>Källkatalog för Hive Create Table-kommando.</td></tr>
    <tr><td>${hiveOutputFolder}</td><td>Utdatamappen för instruktionen INSERT skrivs över.</td></tr>
    <tr><td>${hiveTableName}</td><td>Namnet på Hive-tabell som refererar till log4j-datafiler.</td></tr>
    </table>

    Sqoop åtgärdsvariabler

    <table border = "1">
    <tr><th>Sqoop åtgärd variabel</th><th>Beskrivning</th></tr>
    <tr><td>${sqlDatabaseConnectionString}</td><td>Anslutningssträngen för SQL-databas.</td></tr>
    <tr><td>${sqlDatabaseTableName}</td><td>Azure SQL-databastabell där data ska exporteras.</td></tr>
    <tr><td>${hiveOutputFolder}</td><td>Utdatamappen för instruktionen Hive Infoga skrivs över. Det här är samma mapp för Sqoop exportera (export-dir).</td></tr>
    </table>

    Läs mer om hur Oozie-arbetsflöde och använder arbetsflödesåtgärderna [Apache Oozie 4.0 dokumentation] [ apache-oozie-400] (för HDInsight-kluster av version 3.0) eller [Apache Oozie 3.3.2 dokumentation ] [ apache-oozie-332] (för HDInsight-kluster av version 2.1).

1. Spara filen som **C:\Tutorials\UseOozie\workflow.xml** med hjälp av ANSI (ASCII) kodning. (Använd anteckningar om din textredigerare inte tillhandahåller det här alternativet.)

**Definiera coordinator**

1. Skapa en textfil med följande innehåll:

    ```xml
    <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
        <action>
            <workflow>
                <app-path>${wfPath}</app-path>
            </workflow>
        </action>
    </coordinator-app>
    ```

    Det finns fem variabler som används i definitionsfilen för:

   | Variabel | Beskrivning |
   | --- | --- |
   | ${coordFrequency} |Pausa jobbtiden. Frekvensen är alltid uttrycks i minuter. |
   | ${coordStart} |Jobbets starttid. |
   | ${coordEnd} |Jobbets sluttid. |
   | ${coordTimezone} |Oozie bearbetar coordinator jobb i en fast tidszon med inga sommartid (visas vanligtvis genom att använda UTC). Den här tidszonen kallas ”Oozie bearbetning tidszonen”. |
   | ${wfPath} |Sökvägen för workflow.xml.  Om arbetsflödet filnamnet inte är standardfilnamnet (workflow.xml), måste du ange den. |
2. Spara filen som **C:\Tutorials\UseOozie\coordinator.xml** med hjälp av ANSI (ASCII) kodning. (Använd anteckningar om din textredigerare inte tillhandahåller det här alternativet.)

## <a name="deploy-the-oozie-project-and-prepare-the-tutorial"></a>Distribuera Oozie-projektet och förbereda självstudierna
Du kan köra Azure PowerShell-skript för att utföra följande:

* Kopiera HiveQL-skript (useoozie.hql) till Azure Blob storage wasb:///tutorials/useoozie/useoozie.hql.
* Kopiera workflow.xml till wasb:///tutorials/useoozie/workflow.xml.
* Kopiera coordinator.xml till wasb:///tutorials/useoozie/coordinator.xml.
* Kopiera datafilen (/ example/data/sample.log) till wasb:///tutorials/useoozie/data/sample.log.
* Skapa en Azure SQL database-tabell för att lagra Sqoop export data. Tabellnamnet är *log4jLogCount*.

**Förstå HDInsight storage**

HDInsight använder Azure Blob storage för lagring av data. wasb: / / är Microsofts implementering av Hadoop distributed file system (HDFS) i Azure Blob storage. Mer information finns i [använda Azure Blob storage med HDInsight][hdinsight-storage].

När du etablerar ett HDInsight-kluster anges ett Azure Blob storage-konto och en specifik behållare från detta konto som standardfilsystem, som i HDFS. Förutom det här lagringskontot kan du lägga till ytterligare lagringskonton från samma Azure-prenumeration eller från olika Azure-prenumerationer under etableringen. Mer information om att lägga till ytterligare lagringskonton finns i [etablera HDInsight-kluster][hdinsight-provision]. För att förenkla Azure PowerShell-skript som används i den här självstudien kan alla filer som lagras i filen system standardbehållaren finns på */självstudier/useoozie*. Den här behållaren har samma namn som HDInsight-klustrets namn som standard.
Syntax:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [!NOTE]
> Endast den *wasb: / /* syntaxen stöds i HDInsight-kluster av version 3.0. Den äldre *asv: / /* syntaxen stöds i HDInsight 2.1 och 1.6 kluster, men stöds inte i HDInsight 3.0-kluster.
>
> Wasb: / / sökväg är en virtuell sökväg. Mer information finns i [använda Azure Blob storage med HDInsight][hdinsight-storage].

En fil som lagras i standardbehållaren file system kan nås från HDInsight med hjälp av någon av följande URI: er (jag använder workflow.xml som exempel):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
    wasb:///tutorials/useoozie/workflow.xml
    /tutorials/useoozie/workflow.xml

Om du vill komma åt filen direkt från storage-kontot är blob-namnet för filen:

    tutorials/useoozie/workflow.xml

**Förstå Hive interna och externa tabeller**

Det finns några saker du behöver veta om Hive-interna och externa tabeller:

* CREATE TABLE-kommando skapar en intern tabell, även känd som en hanterad tabell. Datafilen måste befinna sig i standardbehållaren.
* Kommandot CREATE TABLE flyttar filen till/hive/informationslager/<TableName> mapp i standardbehållaren.
* Skapa extern tabell kommandot skapar en extern tabell. Datafilen kan finnas utanför standardbehållaren.
* Kommandot CREATE EXTERNAL TABLE flyttar inte datafilen.
* Kommandot CREATE EXTERNAL TABLE tillåter inte att eventuella undermappar på den mapp som anges i instruktionen plats. Detta är orsaken till varför självstudiekursen skapar en kopia av sample.log-fil.

Mer information finns i [HDInsight: Hive interna och externa tabeller introduktion][cindygross-hive-tables].

**Förbereda självstudierna**

1. Öppna Windows PowerShell ISE (i startskärmen i Windows 8, skriver **PowerShell_ISE**, och klicka sedan på **Windows PowerShell ISE**. Mer information finns i [starta Windows PowerShell på Windows 8 och Windows][powershell-start]).
2. Längst ned i fönstret, kör du följande kommando för att ansluta till din Azure-prenumeration:

    ```powershell
    Add-AzureAccount
    ```

    Du uppmanas att ange dina autentiseringsuppgifter för Azure-konto. Tidsgränsen uppnås för den här metoden för att lägga till en prenumerationsanslutning och du behöver köra cmdleten igen efter 12 timmar.

   > [!NOTE]
   > Om du har flera Azure-prenumerationer och standard-prenumerationen är inte detsamma som du vill använda, Använd den <strong>Select-AzureSubscription</strong> cmdlet för att välja en prenumeration.

3. Kopiera följande skript i skriptfönstret och sedan ange de första sex variablerna:

    ```powershell
    # WASB variables
    $storageAccountName = "<StorageAccountName>"
    $containerName = "<BlobStorageContainerName>"

    # SQL database variables
    $sqlDatabaseServer = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabaseLoginPassword = "SQLDatabaseLoginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"
    $sqlDatabaseTableName = "log4jLogsCount"

    # Oozie files for the tutorial
    $hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"
    $workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
    $coordDefinition =  "C:\Tutorials\UseOozie\coordinator.xml"

    # WASB folder for storing the Oozie tutorial files.
    $destFolder = "tutorials/useoozie"  # Do NOT use the long path here
    ```

    Beskrivningar av variabler finns i den [krav](#prerequisites) avsnitt i den här självstudien.

4. Lägg till följande till skriptet i skriptfönstret:

    ```powershell
    # Create a storage context object
    $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

    function uploadOozieFiles()
    {
        Write-Host "Copy HiveQL script, workflow definition and coordinator definition ..." -ForegroundColor Green
        Set-AzureStorageBlobContent -File $hiveQLScript -Container $containerName -Blob "$destFolder/useooziewf.hql" -Context $destContext
        Set-AzureStorageBlobContent -File $workflowDefinition -Container $containerName -Blob "$destFolder/workflow.xml" -Context $destContext
        Set-AzureStorageBlobContent -File $coordDefinition -Container $containerName -Blob "$destFolder/coordinator.xml" -Context $destContext
    }

    function prepareHiveDataFile()
    {
        Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green
        Start-CopyAzureStorageBlob -SrcContainer $containerName -SrcBlob "example/data/sample.log" -Context $destContext -DestContainer $containerName -destBlob "$destFolder/data/sample.log" -DestContext $destContext
    }

    function prepareSQLDatabase()
    {
        # SQL query string for creating log4jLogsCount table
        $cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                [Level] [nvarchar](10) NOT NULL,
                [Total] float,
            CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
            (
            [Level] ASC
            )
            )"

        #Create the log4jLogsCount table
        Write-Host "Create Log4jLogsCount table ..." -ForegroundColor Green
        $conn = New-Object System.Data.SqlClient.SqlConnection
        $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
        $conn.open()
        $cmd = New-Object System.Data.SqlClient.SqlCommand
        $cmd.connection = $conn
        $cmd.commandtext = $cmdCreateLog4jCountTable
        $cmd.executenonquery()

        $conn.close()
    }

    # upload workflow.xml, coordinator.xml, and ooziewf.hql
    uploadOozieFiles;

    # make a copy of example/data/sample.log to example/data/log4j/sample.log
    prepareHiveDataFile;

    # create log4jlogsCount table on SQL database
    prepareSQLDatabase;
    ```

5. Klicka på **kör skript** eller tryck på **F5** att köra skriptet. Utdata ska vara detsamma som:

    ![Förberedelse av självstudiekursen utdata][img-preparation-output]

## <a name="run-the-oozie-project"></a>Kör projektet för Oozie
Azure PowerShell erbjuder inte för närvarande cmdlets för att definiera Oozie-jobb. Du kan använda den **Invoke-RestMethod** cmdlet för att anropa Oozie-webbtjänster. Oozie web services API är en HTTP-REST-API för JSON. Mer information om API för Oozie-webbtjänsterna finns i [Apache Oozie 4.0 dokumentation] [ apache-oozie-400] (för HDInsight-kluster av version 3.0) eller [Apache Oozie 3.3.2 dokumentation] [ apache-oozie-332] (för HDInsight-kluster av version 2.1).

**Att skicka ett Oozie-jobb**

1. Öppna Windows PowerShell ISE (i Windows 8-startskärm, skriver **PowerShell_ISE**, och klicka sedan på **Windows PowerShell ISE**. Mer information finns i [starta Windows PowerShell på Windows 8 och Windows][powershell-start]).
2. Kopiera följande skript i skriptfönstret och sedan ange de först fjorton variablerna (men hoppa över **$storageUri**).

    ```powershell
    #HDInsight cluster variables
    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterUserPassword>"

    #Azure Blob storage (WASB) variables
    $storageAccountName = "<StorageAccountName>"
    $storageContainerName = "<BlobContainerName>"
    $storageUri="wasb://$storageContainerName@$storageAccountName.blob.core.windows.net"

    #Azure SQL database variables
    $sqlDatabaseServer = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabaseLoginPassword = "<SQLDatabaseloginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"

    #Oozie WF/coordinator variables
    $coordStart = "2014-03-21T13:45Z"
    $coordEnd = "2014-03-21T13:45Z"
    $coordFrequency = "1440"    # in minutes, 24h x 60m = 1440m
    $coordTimezone = "UTC"    #UTC/GMT

    $oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
    $waitTimeBetweenOozieJobStatusCheck=10

    #Hive action variables
    $hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
    $hiveTableName = "log4jlogs"
    $hiveDataFolder = "$storageUri/tutorials/useoozie/data"
    $hiveOutputFolder = "$storageUri/tutorials/useoozie/output"

    #Sqoop action variables
    $sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServer;password=$sqlDatabaseLoginPassword;database=$sqlDatabaseName"  
    $sqlDatabaseTableName = "log4jLogsCount"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)
    ```

    Beskrivningar av variabler finns i den [krav](#prerequisites) avsnitt i den här självstudien.

    $coordstart och $coordend är arbetsflödet start- och sluttid. Om du vill ta reda på UTC-/ GTM kan söka ”utc-tid” på bing.com. $CoordFrequency är hur ofta i minuter som du vill köra arbetsflödet.
3. Lägg till följande till skriptet. Den här delen definierar nyttolasten Oozie:

    ```powershell
    #OoziePayload used for Oozie web service submission
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
            <name>oozie.coord.application.path</name>
            <value>$oozieWFPath</value>
        </property>

        <property>
            <name>wfPath</name>
            <value>$oozieWFPath</value>
        </property>

        <property>
            <name>coordStart</name>
            <value>$coordStart</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>$coordEnd</value>
        </property>

        <property>
            <name>coordFrequency</name>
            <value>$coordFrequency</value>
        </property>

        <property>
            <name>coordTimezone</name>
            <value>$coordTimezone</value>
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
            <value>admin</value>
        </property>

    </configuration>
    "@
    ```

   > [!NOTE]
   > Den stora skillnaden jämfört med arbetsflödesfil bidrag nyttolasten är variabeln **oozie.coord.application.path**. När du skickar in ett arbetsflödesjobb ska du använda **oozie.wf.application.path** i stället.

4. Lägg till följande till skriptet. Den här delen kontrollerar status för Oozie-web-tjänsten:

    ```powershell
    function checkOozieServerStatus()
    {
        Write-Host "Checking Oozie server status..." -ForegroundColor Green
        $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus

        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $oozieServerSatus = $jsonResponse[0].("systemMode")
        Write-Host "Oozie server status is $oozieServerSatus..."

        if($oozieServerSatus -notmatch "NORMAL")
        {
            Write-Host "Oozie server status is $oozieServerSatus...cannot submit Oozie jobs. Check the server status and re-run the job."
        }
    }
    ```

5. Lägg till följande till skriptet. Den här delen skapar ett Oozie-jobb:

    ```powershell
    function createOozieJob()
    {
        # create Oozie job
        Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
        Write-Host "`n--------`n$OoziePayload`n--------"
        $clusterUriCreateJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
        $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $creds -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName -debug -Verbose

        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $oozieJobId = $jsonResponse[0].("id")
        Write-Host "Oozie job id is $oozieJobId..."

        return $oozieJobId
    }
    ```

   > [!NOTE]
   > När du skickar in ett arbetsflödesjobb, gör du en annan-webbtjänstanrop för att starta jobbet när jobbet har skapats. I det här fallet utlöses coordinator jobbet av tid. Jobbet startar automatiskt.

6. Lägg till följande till skriptet. Den här delen kontrollerar Oozie jobbets status:

    ```powershell
    function checkOozieJobStatus($oozieJobId)
    {
        # get job status
        Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
        Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck

        Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
        $clusterUriGetJobStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $JobStatus = $jsonResponse[0].("status")

        while($JobStatus -notmatch "SUCCEEDED|KILLED")
        {
            Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
            Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
            $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
            $JobStatus = $jsonResponse[0].("status")
        }

        Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!"
        if($JobStatus -notmatch "SUCCEEDED")
        {
            Write-Host "Check logs at http://headnode0:9014/cluster for detais."
        }
    }
    ```

7. (Valfritt) Lägg till följande till skriptet.

    ```powershell
    function listOozieJobs()
    {
        Write-Host "Listing Oozie jobs..." -ForegroundColor Green
        $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds

        write-host "Job ID                                   App Name        Status      Started                         Ended"
        write-host "----------------------------------------------------------------------------------------------------------------------------------"
        foreach($job in $response.workflows)
        {
            Write-Host $job.id "`t" $job.appName "`t" $job.status "`t" $job.startTime "`t" $job.endTime
        }
    }

    function ShowOozieJobLog($oozieJobId)
    {
        Write-Host "Showing Oozie job info..." -ForegroundColor Green
        $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/$oozieJobId" + "?show=log"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds
        write-host $response
    }

    function killOozieJob($oozieJobId)
    {
        Write-Host "Killing the Oozie job $oozieJobId..." -ForegroundColor Green
        $clusterUriStartJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=kill" #Valid values for the 'action' parameter are 'start', 'suspend', 'resume', 'kill', 'dryrun', 'rerun', and 'change'.
        $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $creds | Format-Table -HideTableHeaders -debug
    }
    ```

8. Lägg till följande till skriptet:

    ```powershell
    checkOozieServerStatus
    # listOozieJobs
    $oozieJobId = createOozieJob($oozieJobId)
    checkOozieJobStatus($oozieJobId)
    # ShowOozieJobLog($oozieJobId)
    # killOozieJob($oozieJobId)
    ```

Ta bort #-tecken om du vill köra ytterligare funktioner.

9. Om ditt HDinsight-kluster är version 2.1 ersätter du ”https://$clusterName.azurehdinsight.net:443/oozie/v2/” med ”https://$clusterName.azurehdinsight.net:443/oozie/v1/”. HDInsight-kluster av version 2.1 har inte stöd för version 2 av web services.
10. Klicka på **kör skript** eller tryck på **F5** att köra skriptet. Utdata ska vara detsamma som:

     ![Självstudie kör arbetsflödets utdata][img-runworkflow-output]
11. Anslut till SQL Database för att se exporterade data.

**Kontrollera i felloggen för jobbet**

Om du vill felsöka ett arbetsflöde, finns Oozie loggfilen på C:\apps\dist\oozie-3.3.2.1.3.2.0-05\oozie-win-distro\logs\Oozie.log från klustrets huvudnod. Information om RDP finns [administrera HDInsight-kluster med Azure portal][hdinsight-admin-portal].

**Att köra självstudien**

Om du vill köra arbetsflödet, måste du utföra följande uppgifter:

* Ta bort Hive-skriptfilen för utdata.
* Ta bort data i tabellen log4jLogsCount.

Här är en Windows PowerShell-exempelskript som du kan använda:

```powershell
$storageAccountName = "<AzureStorageAccountName>"
$containerName = "<ContainerName>"

#SQL database variables
$sqlDatabaseServer = "<SQLDatabaseServerName>"
$sqlDatabaseLogin = "<SQLDatabaseLoginName>"
$sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>"
$sqlDatabaseName = "<SQLDatabaseName>"
$sqlDatabaseTableName = "log4jLogsCount"

Write-host "Delete the Hive script output file ..." -ForegroundColor Green
$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $containerName

Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
$conn = New-Object System.Data.SqlClient.SqlConnection
$conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
$conn.open()
$cmd = New-Object System.Data.SqlClient.SqlCommand
$cmd.connection = $conn
$cmd.commandtext = "delete from $sqlDatabaseTableName"
$cmd.executenonquery()

$conn.close()
```

## <a name="next-steps"></a>Nästa steg
I den här självstudien beskrivs hur du definierar ett Oozie-arbetsflöde och Oozie-koordinator och hur du kör ett jobb för Oozie-koordinator med hjälp av Azure PowerShell. Mer information finns i följande artiklar:

* [Kom igång med HDInsight][hdinsight-get-started]
* [Använda Azure Blob storage med HDInsight][hdinsight-storage]
* [Administrera HDInsight med hjälp av Azure PowerShell][hdinsight-admin-powershell]
* [Överföra data till HDInsight][hdinsight-upload-data]
* [Använda Sqoop med HDInsight][hdinsight-use-sqoop]
* [Använda Hive med HDInsight][hdinsight-use-hive]
* [Använda Pig med HDInsight][hdinsight-use-pig]
* [Utveckla Java MapReduce-program för HDInsight][hdinsight-develop-java-mapreduce]

[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563

[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md

[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-develop-java-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md

[sqldatabase-get-started]: ../sql-database/sql-database-get-started.md

[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: https://docs.microsoft.com/powershell/scripting/setup/starting-windows-powershell?view=powershell-6
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.RunCoord.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
