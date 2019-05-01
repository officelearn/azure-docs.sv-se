---
title: Python UDF med Apache Hive och Apache Pig - Azure HDInsight
description: Lär dig hur du använder Python användaren användardefinierade funktioner (UDF) från Apache Hive och Apache Pig i HDInsight, Apache Hadoop-teknikstacken på Azure.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 03/15/2019
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 6f3140f412f9d36ca36cef440bd4e60f1a9197d4
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702228"
---
# <a name="use-python-user-defined-functions-udf-with-apache-hive-and-apache-pig-in-hdinsight"></a>Använd användardefinierade Python funktioner (UDF) med Apache Hive och Apache Pig i HDInsight

Lär dig hur du använder Python användardefinierade funktioner (UDF) med Apache Hive och Apache Pig i Apache Hadoop på Azure HDInsight.

## <a name="python"></a>Python på HDInsight

Python2.7 installeras som standard på HDInsight 3.0 och senare. Apache Hive kan användas med den här versionen av Python för bearbetning av dataströmmen. Stream bearbetning använder STDOUT- och STDIN för att överföra data mellan Hive och en användardefinierad funktion.

HDInsight omfattar även Jython, vilket är en Python-implementering skriven i Java. Jython körs direkt på Java Virtual Machine och används inte för direktuppspelning. Jython är den rekommenderade Python-tolken när du använder Python med Pig.

## <a name="prerequisites"></a>Nödvändiga komponenter

* **Ett Hadoop-kluster på HDInsight**. Se [Kom igång med HDInsight på Linux](apache-hadoop-linux-tutorial-get-started.md).
* **En SSH-klient**. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
* Den [URI-schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme) för ditt kluster primär lagring. Detta skulle vara wasb: / / för Azure Storage, abfs: / / för Azure Data Lake Storage Gen2 eller adl: / / för Azure Data Lake Storage Gen1. Om säker överföring har aktiverats för Azure Storage eller Data Lake Storage Gen2, URI: N blir wasbs: / / eller abfss: / /, respektive Se även [säker överföring](../../storage/common/storage-require-secure-transfer.md).
* **Möjliga ändring lagringskonfiguration.**  Se [lagringskonfiguration](#storage-configuration) om du använder storage-kontotyp `BlobStorage`.
* Valfri.  Om du tänker använda PowerShell, måste den [AZ modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) installerad.

> [!NOTE]  
> Lagringskontot som används i den här artikeln har Azure Storage med [säker överföring](../../storage/common/storage-require-secure-transfer.md) aktiverad och därmed `wasbs` används i hela artikeln.

## <a name="storage-configuration"></a>Storage-konfiguration
Ingen åtgärd krävs om lagringskontot som används är av typen `Storage (general purpose v1)` eller `StorageV2 (general purpose v2)`.  Processen i den här artikeln resultat till minst `/tezstaging`.  Innehåller en standardkonfiguration för hadoop `/tezstaging` i den `fs.azure.page.blob.dir` configuration variabel i `core-site.xml` för tjänsten `HDFS`.  Den här konfigurationen leder till utdata till katalogen är sidblobbar, som inte stöds för storage-kontotyp `BlobStorage`.  Att använda `BlobStorage` i den här artikeln tar du bort `/tezstaging` från den `fs.azure.page.blob.dir` configuration variabeln.  Konfigurationen kan nås från den [Ambari UI](../hdinsight-hadoop-manage-ambari.md).  I annat fall visas ett felmeddelande: `Page blob is not supported for this account type.`

> [!WARNING]  
> Stegen i det här dokumentet gör följande antaganden:  
>
> * Du kan skapa Python-skript på din lokala utvecklingsmiljö.
> * Du överför skript till HDInsight med antingen den `scp` kommandot eller skriptet i PowerShell.
>
> Om du vill använda den [Azure Cloud Shell (bash)](https://docs.microsoft.com/azure/cloud-shell/overview) för att fungera med HDInsight, måste du:
>
> * Skapa skript i cloud shell-miljön.
> * Använd `scp` att ladda upp filer från cloudshell till HDInsight.
> * Använd `ssh` från cloudshell för att ansluta till HDInsight och köra exemplen.

## <a name="hivepython"></a>Apache Hive UDF

Python kan användas som en UDF från Hive via HiveQL `TRANSFORM` instruktionen. Till exempel följande HiveQL anropar den `hiveudf.py` -fil som lagras i Azure Storage-kontot för klustret.

```hiveql
add file wasbs:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLabel string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

Här är vad som gör det här exemplet:

1. Den `add file` instruktion i början av filen lägger till den `hiveudf.py` filen till distribuerad cache, så är den tillgänglig för alla noder i klustret.
2. Den `SELECT TRANSFORM ... USING` instruktionen väljer data från den `hivesampletable`. Den skickar även värden för clientid, devicemake och devicemodel till den `hiveudf.py` skript.
3. Den `AS` satsen beskriver de fält som returneras från `hiveudf.py`.

<a name="streamingpy"></a>

### <a name="create-file"></a>Skapa fil

Skapa en textfil med namnet på din utvecklingsmiljö `hiveudf.py`. Använd följande kod som innehållet i filen:

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

1. Läser en rad med data från STDIN.
2. Avslutande radmatningstecken tas bort med hjälp av `string.strip(line, "\n ")`.
3. När du gör för strömbearbetning innehåller alla värden med ett tabbtecken mellan varje värde i en enda rad. Så `string.split(line, "\t")` kan användas för att dela upp inmatningen vid varje flik, returnerar enbart fält.
4. När bearbetningen är klar, måste utdata skrivas till STDOUT som en enda rad med en flik mellan varje fält. Till exempel `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. Den `while` loopen upprepas tills Nej `line` läses.

Utdata från skriptet är en sammansättning av indatavärden för `devicemake` och `devicemodel`, och en hash av sammansatt värde.

### <a name="upload-file-shell"></a>Ladda upp fil (shell)
I nedanstående kommandon ersätter `sshuser` med faktiska användarnamn om det är olika.  Ersätt `mycluster` med faktiska klustrets namn.  Kontrollera att arbetskatalogen är där filen finns.

1. Använd `scp` att kopiera filer till ditt HDInsight-kluster. Redigera och ange kommandot nedan:

    ```cmd
    scp hiveudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Använda SSH för att ansluta till klustret.  Redigera och ange kommandot nedan:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Lägg till python-filer som laddats upp tidigare till lagring för klustret från SSH-sessionen.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    ```

### <a name="use-hive-udf-shell"></a>Använda Hive UDF (shell)

1. Om du vill ansluta till Hive, använder du följande kommando från öppna SSH-sessionen:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    Detta kommando startar Beeline-klienten.

2. Ange följande fråga i den `0: jdbc:hive2://headnodehost:10001/>` prompten:

   ```hive
   add file wasbs:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. När du har angett den sista raden ska jobbet starta. När jobbet har slutförts, returnerar den utdata som liknar följande exempel:

        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

4. Om du vill avsluta Beeline, anger du följande kommando:

    ```hive
    !q
    ```

### <a name="upload-file-powershell"></a>Ladda upp fil (PowerShell)

> [!IMPORTANT]  
> Dessa PowerShell-skript fungerar inte om [säker överföring](../../storage/common/storage-require-secure-transfer.md) är aktiverad.  Använd shell-kommandon antingen eller inaktivera säker överföring.

PowerShell kan också användas för att köra Hive-frågor via en fjärranslutning. Se till att arbetskatalogen är där `hiveudf.py` finns.  Använd följande PowerShell-skript för att köra en Hive-fråga som använder den `hiveudf.py` skript:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

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
> Mer information om överföringen av filer finns i den [överföra data för Apache Hadoop-jobb i HDInsight](../hdinsight-upload-data.md) dokumentet.


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

Utdata för den **Hive** jobb bör se ut ungefär så här:

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9


## <a name="pigpython"></a>Apache Pig UDF

Ett Python-skript som kan användas som en UDF från Pig via den `GENERATE` instruktionen. Du kan köra skriptet med Jython eller C Python.

* Jython körs på JVM och internt kan anropas från Pig.
* C Python är en extern process så att data från Pig på JVM skickas ut till det skript som körs i en Python-process. Utdata från Python-skriptet skickas tillbaka till Pig.

Om du vill ange Python-tolk använda `register` när du refererar till Python-skriptet. I följande exempel registrera skript med Pig som `myfuncs`:

* **Att använda Jython**: `register '/path/to/pigudf.py' using jython as myfuncs;`
* **Du använder C Python**: `register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]  
> När du använder Jython, sökvägen till filen pig_jython kan vara antingen en lokal sökväg eller en WASBS: / / sökväg. När du använder C Python, måste du referera en fil på det lokala filsystemet för noden som du använder för att skicka Pig-jobbet.

En gång tidigare registreringen Pig Latin för det här exemplet är samma för både:

```pig
LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Här är vad som gör det här exemplet:

1. Den första raden läser in Exempeldatafilen, `sample.log` till `LOGS`. Den definierar även varje post som en `chararray`.
2. Nästa rad filtrerar ut några null-värden som lagrar resultatet av åtgärden i `LOG`.
3. Därefter den itererar över poster i `LOG` och använder `GENERATE` att anropa den `create_structure` metod i Python/Jython-skript som läses in som `myfuncs`. `LINE` används för att skicka den aktuella posten till funktionen.
4. Slutligen utdata dumpas STDOUT med hjälp av den `DUMP` kommando. Det här kommandot visar resultat när åtgärden har slutförts.

### <a name="create-file"></a>Skapa fil

Skapa en textfil med namnet på din utvecklingsmiljö `pigudf.py`. Använd följande kod som innehållet i filen:

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

I exemplet Pig Latin i `LINE` indata har definierats som en chararray eftersom det finns inget konsekvent schema för indata. Python-skriptet som omvandlar data till ett konsekvent schema för utdata.

1. Den `@outputSchema` instruktionen definierar formatet på de data som returneras till Pig. I det här fallet den har en **data egenskapsuppsättning**, vilket är en typ av Pig. Uppsättningen innehåller följande fält som alla är chararray (strängar):

   * datum – datumet loggposten skapades
   * tid - format som logg har skapats
   * ClassName - klassnamnet posten skapades för
   * nivå - loggningsnivån
   * detaljer - utförlig information för logg

2. Därefter den `def create_structure(input)` definierar vilken funktion som Pig skickar radobjekt till.

3. Exempeldata, `sample.log`, främst överensstämmer med datum, tid, classname, nivå, och detaljerat schema. Men den innehåller några rader som börjar med `*java.lang.Exception*`. Dessa rader måste ändras så att den matchar schemat. Den `if` instruktion söker efter dem och sedan massages indata att flytta den `*java.lang.Exception*` sträng till slutet, föra på data i-raden med det förväntade utdataschemat.

4. Därefter den `split` används för att dela data på de första tecknen i fyra utrymme. Utdata har tilldelats till `date`, `time`, `classname`, `level`, och `detail`.

5. Slutligen returneras värdena till Pig.

När data returneras till Pig, har ett konsekvent schema som definierats i den `@outputSchema` instruktionen.



### <a name="upload-file-shell"></a>Ladda upp fil (shell)

I nedanstående kommandon ersätter `sshuser` med faktiska användarnamn om det är olika.  Ersätt `mycluster` med faktiska klustrets namn.  Kontrollera att arbetskatalogen är där filen finns.

1. Använd `scp` att kopiera filer till ditt HDInsight-kluster. Redigera och ange kommandot nedan:

    ```cmd
    scp pigudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Använda SSH för att ansluta till klustret.  Redigera och ange kommandot nedan:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Lägg till python-filer som laddats upp tidigare till lagring för klustret från SSH-sessionen.

    ```bash
    hdfs dfs -put pigudf.py /pigudf.py
    ```


### <a name="use-pig-udf-shell"></a>Använda Pig UDF (shell)

1. Om du vill ansluta till pig, använder du följande kommando från öppna SSH-sessionen:

    ```bash
    pig
    ```

2. Ange följande instruktioner i den `grunt>` prompten:

   ```pig
   Register wasbs:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. När du har angett följande rad ska jobbet starta. När jobbet har slutförts, returnerar den utdata som liknar följande data:

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. Använd `quit` att lämna Grunt-gränssnittet och Använd sedan följande för att redigera filen pigudf.py på det lokala filsystemet:

    ```bash
    nano pigudf.py
    ```

5. En gång i redigeraren, ta bort följande raden genom att ta bort den `#` tecknet från början av raden:

    ```bash
    #from pig_util import outputSchema
    ```

    Den här raden ändrar Python-skriptet för att arbeta med C Python i stället för Jython. När ändringen har gjorts, använder **Ctrl + X** att avsluta redigeraren. Välj **Y**, och sedan **RETUR** att spara ändringarna.

6. Använd den `pig` kommando för att starta gränssnittet igen. När du är på den `grunt>` uppmanar, Använd följande för att köra Python-skriptet med C Python-tolk.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    När det här jobbet har slutförts bör du se samma utdata som tidigare när du körde skriptet med Jython.


### <a name="upload-file-powershell"></a>Ladda upp fil (PowerShell)

> [!IMPORTANT]  
> Dessa PowerShell-skript fungerar inte om [säker överföring](../../storage/common/storage-require-secure-transfer.md) är aktiverad.  Använd shell-kommandon antingen eller inaktivera säker överföring.

PowerShell kan också användas för att köra Hive-frågor via en fjärranslutning. Se till att arbetskatalogen är där `pigudf.py` finns.  Använd följande PowerShell-skript för att köra en Hive-fråga som använder den `pigudf.py` skript:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

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

### <a name="use-pig-udf-powershell"></a>Använda Pig UDF (PowerShell)

> [!NOTE]  
> När du skickar in ett jobb med hjälp av PowerShell via fjärranslutning, går det inte att använda C Python som vilken tolk.

PowerShell kan också användas för att köra Pig Latin-jobb. Att köra ett Pig Latin-jobb som använder den `pigudf.py` skript, Använd följande PowerShell-skript:

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

Utdata för den **Pig** jobb bör likna följande data:

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a>Felsökning

### <a name="errors-when-running-jobs"></a>Fel när du kör jobb

När du kör hive-jobb, kan det uppstå ett fel som liknar följande text:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Det här problemet kan ha orsakats av radbrytningar i Python-filen. Många Windows-redigerare som standard använder CRLF som raden slutar, men Linux-program vanligtvis förväntar sig LF.

Du kan använda följande PowerShell-uttryck för att ta bort CR tecken innan du laddar upp filen till HDInsight:

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>PowerShell-skript

Båda exempel PowerShell-skript som används för att köra exemplen innehåller en kommenterade rad som visar Felutdata för jobbet. Om du inte ser utdata som förväntas för jobbet, avkommenterar följande rad och se om information om felet tyder på problem.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

Information om felet (STDERR) och resultatet av jobbet (STDOUT) loggas också din HDInsight-lagring.

| För det här jobbet... | Titta på de här filerna i blob-behållaren |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next"></a>Nästa steg

Om du vill läsa in Python-moduler som inte tillhandahålls som standard kan se [hur du distribuerar en modul till Azure HDInsight](https://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx).

Andra sätt att använda Apache Pig, Hive, och Läs om hur du använder MapReduce i följande dokument:

* [Använda Apache Hive med HDInsight](hdinsight-use-hive.md)
* [Använda Apache Pig med HDInsight](hdinsight-use-pig.md)
* [Använda MapReduce med HDInsight](hdinsight-use-mapreduce.md)
