---
title: Python UDF med Apache Hive och Apache Pig - Azure HDInsight
description: Lär dig hur du använder Användardefinierade funktioner för Python (UDF) från Apache Hive och Apache Pig i HDInsight, Apache Hadoop-teknikstacken på Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/15/2019
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 201bb40e5024442587f5508886da7e844f35be40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74148405"
---
# <a name="use-python-user-defined-functions-udf-with-apache-hive-and-apache-pig-in-hdinsight"></a>Använd Python User Defined Functions (UDF) med Apache Hive och Apache Pig i HDInsight

Lär dig hur du använder Användardefinierade Funktioner i Python (UDF) med Apache Hive och Apache Pig i Apache Hadoop på Azure HDInsight.

## <a name="python-on-hdinsight"></a><a name="python"></a>Python på HDInsight

Python2.7 installeras som standard på HDInsight 3.0 och senare. Apache Hive kan användas med den här versionen av Python för dataflödesbearbetning. Strömbearbetning använder STDOUT och STDIN för att skicka data mellan Hive och UDF.

HDInsight innehåller även Jython, som är en Python-implementering skriven i Java. Jython körs direkt på Java Virtual Machine och använder inte streaming. Jython är den rekommenderade Python-tolken när du använder Python med Pig.

## <a name="prerequisites"></a>Krav

* **Ett Hadoop-kluster på HDInsight**. Se [Komma igång med HDInsight på Linux](apache-hadoop-linux-tutorial-get-started.md).
* **En SSH-klient**. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
* [URI-schemat](../hdinsight-hadoop-linux-information.md#URI-and-scheme) för klustrets primära lagring. Detta skulle `wasb://` vara för `abfs://` Azure Storage, för Azure Data Lake Storage Gen2 eller adl:// för Azure Data Lake Storage Gen1. Om säker överföring är aktiverad för Azure Storage, skulle URI vara wasbs://.  Se även [säker överföring](../../storage/common/storage-require-secure-transfer.md).
* **Möjlig ändring av lagringskonfiguration.**  Se [Lagringskonfiguration](#storage-configuration) om `BlobStorage`du använder lagringskontosort .
* Valfri.  Om du planerar att använda PowerShell måste [AZ-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) vara installerad.

> [!NOTE]  
> Lagringskontot som användes i den här artikeln `wasbs` var Azure Storage med säker [överföring](../../storage/common/storage-require-secure-transfer.md) aktiverad och används därför i hela artikeln.

## <a name="storage-configuration"></a>Storage-konfiguration

Ingen åtgärd krävs om lagringskontot `Storage (general purpose v1)` `StorageV2 (general purpose v2)`som används är av slag eller .  Processen i den här artikeln kommer `/tezstaging`att producera utdata till minst .  En standardkonfiguration för hadoop `fs.azure.page.blob.dir` kommer `core-site.xml` att `HDFS`finnas `/tezstaging` i konfigurationsvariabeln i för tjänsten .  Den här konfigurationen gör att utdata till katalogen blir sidblobar, som inte stöds för lagringskontosdort `BlobStorage`.  Om `BlobStorage` du vill använda `/tezstaging` den `fs.azure.page.blob.dir` här artikeln tar du bort från konfigurationsvariabeln.  Konfigurationen kan nås från [Ambari UI](../hdinsight-hadoop-manage-ambari.md).  Annars visas felmeddelandet:`Page blob is not supported for this account type.`

> [!WARNING]  
> Stegen i det här dokumentet gör följande antaganden:  
>
> * Du skapar Python-skripten i din lokala utvecklingsmiljö.
> * Du laddar upp skripten till HDInsight med kommandot `scp` eller det medföljande PowerShell-skriptet.
>
> Om du vill använda [Azure Cloud Shell (bash)](https://docs.microsoft.com/azure/cloud-shell/overview) för att arbeta med HDInsight måste du:
>
> * Skapa skripten i molnskalmiljön.
> * Används `scp` för att ladda upp filerna från molnskalet till HDInsight.
> * Använd `ssh` från molnskalet för att ansluta till HDInsight och köra exemplen.

## <a name="apache-hive-udf"></a><a name="hivepython"></a>Apache Hive UDF

Python kan användas som en UDF från Hive `TRANSFORM` via HiveQL-satsen. Följande HiveQL anropar till `hiveudf.py` exempel filen som lagras i standardkontot för Azure Storage för klustret.

```hiveql
add file wasbs:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLabel string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

Så här gör det här exemplet:

1. Uttrycket `add file` i början av filen `hiveudf.py` lägger till filen i den distribuerade cachen, så den är tillgänglig för alla noder i klustret.
2. Utdraget `SELECT TRANSFORM ... USING` väljer data `hivesampletable`från . Den skickar också värdena clientid, devicemake och `hiveudf.py` devicemodel till skriptet.
3. Satsen `AS` beskriver de fält `hiveudf.py`som returneras från .

<a name="streamingpy"></a>

### <a name="create-file"></a>Skapa fil

Skapa en textfil med namnet `hiveudf.py`. Använd följande kod som innehållet i filen:

```python
#!/usr/bin/env python
import sys
import string
import hashlib

while True:
    line = sys.stdin.readline()
    if not line:
        break

    line = string.strip(line, "\n ")
    clientid, devicemake, devicemodel = string.split(line, "\t")
    phone_label = devicemake + ' ' + devicemodel
    print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])
```

Det här skriptet utför följande åtgärder:

1. Läser en rad data från STDIN.
2. Det efterföljande nyradstecknet `string.strip(line, "\n ")`tas bort med .
3. När du bearbetar dataström innehåller en enda rad alla värden med ett fliktecken mellan varje värde. Så `string.split(line, "\t")` kan användas för att dela indata på varje flik, returnerar bara fälten.
4. När bearbetningen är klar måste utdata skrivas till STDOUT som en enda rad, med en flik mellan varje fält. Till exempel `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. Slingan `while` upprepas `line` tills ingen läss.

Skriptutdata är en sammanfogning av `devicemake` `devicemodel`indatavärdena för och , och en hash av det sammanfogade värdet.

### <a name="upload-file-shell"></a>Ladda upp fil (skal)

Ersätt `sshuser` med det faktiska användarnamnet om det är annorlunda i kommandona nedan.  Ersätt `mycluster` med det faktiska klusternamnet.  Kontrollera att arbetskatalogen är där filen finns.

1. Används `scp` för att kopiera filerna till ditt HDInsight-kluster. Redigera och ange kommandot nedan:

    ```cmd
    scp hiveudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Använd SSH för att ansluta till klustret.  Redigera och ange kommandot nedan:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Från SSH-sessionen lägger du till pythonfiler som tidigare överförts till lagringen för klustret.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    ```

### <a name="use-hive-udf-shell"></a>Använd Hive UDF (skal)

1. Om du vill ansluta till Hive använder du följande kommando från den öppna SSH-sessionen:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    Det här kommandot startar Beeline-klienten.

2. Ange följande fråga `0: jdbc:hive2://headnodehost:10001/>` vid prompten:

   ```hive
   add file wasbs:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. När du har angett den sista raden ska jobbet starta. När jobbet är klart returneras utdata som liknar följande exempel:

        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

4. Om du vill avsluta Beeline anger du följande kommando:

    ```hive
    !q
    ```

### <a name="upload-file-powershell"></a>Ladda upp fil (PowerShell)

PowerShell kan också användas för att fjärrköra Hive-frågor. Kontrollera att arbetskatalogen finns där. `hiveudf.py`  Använd följande PowerShell-skript för att köra `hiveudf.py` en Hive-fråga som använder skriptet:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Revise file path as needed
$pathToStreamingFile = ".\hiveudf.py"

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzStorageAccountKey `
   -ResourceGroupName $resourceGroup `
   -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# Upload local files to an Azure Storage blob
Set-AzStorageBlobContent `
    -File $pathToStreamingFile `
    -Blob "hiveudf.py" `
    -Container $container `
    -Context $context
```

> [!NOTE]  
> Mer information om hur du laddar upp filer finns i [Ladda upp data för Apache Hadoop-jobb i HDInsight-dokumentet.](../hdinsight-upload-data.md)

#### <a name="use-hive-udf"></a>Använd Hive UDF

```PowerShell
# Script should stop on failures
$ErrorActionPreference = "Stop"

# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -UserName "admin" -Message "Enter the login for the cluster"

$HiveQuery = "add file wasbs:///hiveudf.py;" +
                "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
                "USING 'python hiveudf.py' AS " +
                "(clientid string, phoneLabel string, phoneHash string) " +
                "FROM hivesampletable " +
                "ORDER BY clientid LIMIT 50;"

# Create Hive job object
$jobDefinition = New-AzHDInsightHiveJobDefinition `
    -Query $HiveQuery

# For status bar updates
$activity="Hive query"

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Starting query..."

# Start defined Azure HDInsight job on specified cluster.
$job = Start-AzHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Waiting on query to complete..."

# Wait for completion or failure of specified job
Wait-AzHDInsightJob `
    -JobId $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Uncomment the following to see stderr output
<#
Get-AzHDInsightJobOutput `
   -Clustername $clusterName `
   -JobId $job.JobId `
   -HttpCredential $creds `
   -DisplayOutputType StandardError
#>

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Retrieving output..."

# Gets the log output
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

Utdata för **Hive-jobbet** ska se ut ungefär som i följande exempel:

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

## <a name="apache-pig-udf"></a><a name="pigpython"></a>Apache Gris UDF

Ett Python-skript kan användas som en `GENERATE` UDF från Pig via uttrycket. Du kan köra skriptet med antingen Jython eller C Python.

* Jython körs på JVM, och kan inbyggt kallas från Pig.
* C Python är en extern process, så data från Pig på JVM skickas ut till skriptet som körs i en Python-process. Utdata från Python-skriptet skickas tillbaka till Pig.

Om du vill ange `register` Python-tolken använder du när du refererar till Python-skriptet. Följande exempel registrerar skript med `myfuncs`Gris som :

* **Så här använder du Jython:**`register '/path/to/pigudf.py' using jython as myfuncs;`
* **Så här använder du C Python:**`register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]  
> När du använder Jython kan sökvägen till pig_jython filen vara antingen en lokal sökväg eller en WASBS:// sökväg. När du använder C Python måste du dock referera till en fil i det lokala filsystemet för den nod som du använder för att skicka pig-jobbet.

När tidigare registrering, pig latin för detta exempel är densamma för både:

```pig
LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Så här gör det här exemplet:

1. Den första raden läser in `sample.log` `LOGS`exempeldatafilen i . Den definierar också varje `chararray`post som en .
2. Nästa rad filtrerar bort alla null-värden och `LOG`lagrar resultatet av operationen i .
3. Därefter itererar det över `LOG` posterna i och använder `GENERATE` för att anropa `create_structure` metoden som `myfuncs`finns i Python/Jython-skriptet som läses in som . `LINE`används för att skicka den aktuella posten till funktionen.
4. Slutligen dumpas utdata till STDOUT `DUMP` med kommandot. Det här kommandot visar resultaten när åtgärden är klar.

### <a name="create-file"></a>Skapa fil

Skapa en textfil med namnet `pigudf.py`. Använd följande kod som innehållet i filen:

<a name="streamingpy"></a>

```python
# Uncomment the following if using C Python
#from pig_util import outputSchema


@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
def create_structure(input):
    if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
    date, time, classname, level, detail = input.split(' ', 4)
    return date, time, classname, level, detail
```

I exemplet Pig Latin `LINE` definieras indata som en chararray eftersom det inte finns något konsekvent schema för indata. Python-skriptet omvandlar data till ett konsekvent schema för utdata.

1. Satsen `@outputSchema` definierar formatet på de data som returneras till Pig. I det här fallet är det en **datapåse**, som är en Pig-datatyp. Påsen innehåller följande fält, som alla är chararray (strängar):

   * datum - det datum då loggposten skapades
   * tid - den tidpunkt då loggposten skapades
   * classname - klassnamnet som transaktionen skapades för
   * nivå - loggnivån
   * detalj - utförlig information för loggposten

2. Därefter `def create_structure(input)` definierar funktionen som Pig skickar radobjekt till.

3. `sample.log`Exempeldata, , överensstämmer oftast med schemat datum, tid, klassnamn, nivå och detalj. Den innehåller dock några rader som `*java.lang.Exception*`börjar med . Dessa rader måste ändras för att matcha schemat. Uttalandet `if` kontrollerar för dem, sedan masserar `*java.lang.Exception*` indata för att flytta strängen till slutet, vilket data i linje med den förväntade utdata schema.

4. Därefter används `split` kommandot för att dela upp data vid de fyra första blankstegs tecknen. Utdata tilldelas till `date` `time`, `classname` `level`, `detail`, och .

5. Slutligen returneras värdena till Pig.

När data returneras till Gris har de ett konsekvent `@outputSchema` schema enligt beskrivningen i uttrycket.

### <a name="upload-file-shell"></a>Ladda upp fil (skal)

Ersätt `sshuser` med det faktiska användarnamnet om det är annorlunda i kommandona nedan.  Ersätt `mycluster` med det faktiska klusternamnet.  Kontrollera att arbetskatalogen är där filen finns.

1. Används `scp` för att kopiera filerna till ditt HDInsight-kluster. Redigera och ange kommandot nedan:

    ```cmd
    scp pigudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Använd SSH för att ansluta till klustret.  Redigera och ange kommandot nedan:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Från SSH-sessionen lägger du till pythonfiler som tidigare överförts till lagringen för klustret.

    ```bash
    hdfs dfs -put pigudf.py /pigudf.py
    ```

### <a name="use-pig-udf-shell"></a>Använd Pig UDF (skal)

1. Om du vill ansluta till gris använder du följande kommando från den öppna SSH-sessionen:

    ```bash
    pig
    ```

2. Ange följande satser `grunt>` vid prompten:

   ```pig
   Register wasbs:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. När du har angett följande rad ska jobbet starta. När jobbet är klart returneras utdata som liknar följande data:

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. Används `quit` för att avsluta grunt-skalet och sedan använda följande för att redigera pigudf.py-filen i det lokala filsystemet:

    ```bash
    nano pigudf.py
    ```

5. En gång i redigeraren, avkommenta `#` följande rad genom att ta bort tecknet från början av raden:

    ```bash
    #from pig_util import outputSchema
    ```

    Den här raden ändrar Python-skriptet så att det fungerar med C Python i stället för Jython. När ändringen har gjorts använder du **Ctrl+X** för att avsluta redigeraren. Välj **Y**och **sedan Ange** för att spara ändringarna.

6. Använd `pig` kommandot för att starta skalet igen. När du är `grunt>` på prompten använder du följande för att köra Python-skriptet med C Python-tolken.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    När det här jobbet är klart bör du se samma utdata som när du körde skriptet tidigare med Jython.

### <a name="upload-file-powershell"></a>Ladda upp fil (PowerShell)

PowerShell kan också användas för att fjärrköra Hive-frågor. Kontrollera att arbetskatalogen finns där. `pigudf.py`  Använd följande PowerShell-skript för att köra `pigudf.py` en Hive-fråga som använder skriptet:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Revise file path as needed
$pathToJythonFile = ".\pigudf.py"


# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzStorageAccountKey `
   -ResourceGroupName $resourceGroup `
   -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# Upload local files to an Azure Storage blob
Set-AzStorageBlobContent `
    -File $pathToJythonFile `
    -Blob "pigudf.py" `
    -Container $container `
    -Context $context
```

### <a name="use-pig-udf-powershell"></a>Använd Pig UDF (PowerShell)

> [!NOTE]  
> När du skickar ett jobb med PowerShell kan det inte använda C Python som tolk.

PowerShell kan också användas för att köra Pig Latin-jobb. Om du vill köra ett `pigudf.py` pig latin-jobb som använder skriptet använder du följande PowerShell-skript:

```PowerShell
# Script should stop on failures
$ErrorActionPreference = "Stop"

# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -UserName "admin" -Message "Enter the login for the cluster"


$PigQuery = "Register wasbs:///pigudf.py using jython as myfuncs;" +
            "LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);" +
            "LOG = FILTER LOGS by LINE is not null;" +
            "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
            "DUMP DETAILS;"

# Create Pig job object
$jobDefinition = New-AzHDInsightPigJobDefinition -Query $PigQuery

# For status bar updates
$activity="Pig job"

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Starting job..."

# Start defined Azure HDInsight job on specified cluster.
$job = Start-AzHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Waiting for the Pig job to complete..."

# Wait for completion or failure of specified job
Wait-AzHDInsightJob `
    -Job $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Uncomment the following to see stderr output
<#
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds `
    -DisplayOutputType StandardError
#>

# Progress bar (optional)
Write-Progress -Activity $activity "Retrieving output..."

# Gets the log output
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

Utdata för **grisjobbet** ska se ut ungefär som följande data:

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a><a name="troubleshooting"></a>Troubleshooting (Felsökning)

### <a name="errors-when-running-jobs"></a>Fel vid jobb som körs

När du kör hive-jobbet kan du stöta på ett fel som liknar följande text:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Det här problemet kan orsakas av radsluten i Python-filen. Många Windows-redigerare som standard använder CRLF som radslut, men Linux-program förväntar sig vanligtvis LF.

Du kan använda följande PowerShell-satser för att ta bort CR-tecknen innan du laddar upp filen till HDInsight:

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>PowerShell-skript

Båda exemplet PowerShell-skript som används för att köra exemplen innehåller en kommenterad rad som visar felutdata för jobbet. Om du inte ser det förväntade utdata för jobbet tar du av följande rad och ser om felinformationen indikerar ett problem.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

Felinformationen (STDERR) och resultatet av jobbet (STDOUT) loggas också till din HDInsight-lagring.

| För det här jobbet... | Titta på dessa filer i blob-behållaren |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next-steps"></a><a name="next"></a>Nästa steg

Om du behöver läsa in Python-moduler som inte tillhandahålls som standard läser du Så här distribuerar du [en modul till Azure HDInsight](https://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx).

Andra sätt att använda Pig, Hive och lära dig mer om hur du använder MapReduce finns i följande dokument:

* [Använda Apache Hive med HDInsight](hdinsight-use-hive.md)
* [Använd MapReduce med HDInsight](hdinsight-use-mapreduce.md)
