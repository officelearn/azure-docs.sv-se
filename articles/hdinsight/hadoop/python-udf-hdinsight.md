---
title: Python UDF med Apache Hive och svin - Azure HDInsight | Microsoft Docs
description: Lär dig hur du använder Python användaren definierat funktioner (UDF) från Hive och Pig i HDInsight Hadoop-teknikstacken på Azure.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c44d6606-28cd-429b-b535-235e8f34a664
ms.service: hdinsight
ms.devlang: python
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: c508ffcdd5cccaad5c7b0717d33e2ea1449ab522
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31405501"
---
# <a name="use-python-user-defined-functions-udf-with-hive-and-pig-in-hdinsight"></a>Använd användardefinierade Python funktioner (UDF) med Hive och Pig i HDInsight

Lär dig hur du använder Python användardefinierade funktioner (UDF) med Apache Hive och Pig i Hadoop på Azure HDInsight.

## <a name="python"></a>Python på HDInsight

Python2.7 installeras som standard på HDInsight 3.0 och senare. Apache Hive kan användas med den här versionen av Python för bearbetning av dataströmmen. Dataströmmen bearbetning använder STDOUT- och STDIN för att överföra data mellan Hive och UDF-filen.

HDInsight innehåller också Jython, vilket är en Python-implementering skriven i Java. Jython körs direkt på Java Virtual Machine och använder inte strömning. Jython är den rekommenderade Python-tolkning när du använder Python med Pig.

> [!WARNING]
> Stegen i det här dokumentet gör följande antaganden: 
>
> * Du kan skapa Python-skript på din lokala utvecklingsmiljö.
> * Du överför skript till HDInsight med antingen den `scp` från en lokal Bash-session eller PowerShell-skriptet.
>
> Om du vill använda den [Azure Cloud Shell (bash)](https://docs.microsoft.com/azure/cloud-shell/overview) Förhandsgranska för att arbeta med HDInsight, måste du:
>
> * Skapa skript i molnet shell-miljö.
> * Använd `scp` att överföra filer från molnet shell till HDInsight.
> * Använd `ssh` från molnet shell att ansluta till HDInsight och köra exemplen.

## <a name="hivepython"></a>Hive UDF

Python kan användas som en UDF från Hive via HiveQL `TRANSFORM` instruktionen. Till exempel följande HiveQL anropar den `hiveudf.py` filen lagras i Azure Storage standardkontot för klustret.

**Linux-baserat HDInsight**

```hiveql
add file wasb:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

**Windows-baserat HDInsight**

```hiveql
add file wasb:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'D:\Python27\python.exe hiveudf.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

> [!NOTE]
> På Windows-baserade HDInsight-kluster i `USING` sats måste ange den fullständiga sökvägen till python.exe.

Här är det här exemplet har:

1. Den `add file` i början av filen läggs den `hiveudf.py` filen till det distribuerade cacheminnet, så är den tillgänglig för alla noder i klustret.
2. Den `SELECT TRANSFORM ... USING` uttrycket hämtar data från den `hivesampletable`. Skickar också värden för clientid, devicemake och devicemodel till den `hiveudf.py` skript.
3. Den `AS` satsen beskriver de fält som returneras från `hiveudf.py`.

<a name="streamingpy"></a>

### <a name="create-the-hiveudfpy-file"></a>Skapa filen hiveudf.py


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

Det här skriptet utförs följande åtgärder:

1. Läsa en rad med data från STDIN.
2. Avslutande radmatningstecken tas bort med `string.strip(line, "\n ")`.
3. När du utför bearbetning innehåller alla värdena med tabbtecken mellan varje värde i en enda rad. Så `string.split(line, "\t")` kan användas för att dela indata på varje flik, returnerar bara fält.
4. När bearbetningen är klar måste utdata skrivs STDOUT som en enda rad med en flik mellan varje fält. Till exempel `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. Den `while` loop upprepas tills inga `line` läses.

Utdata från skriptet är en sammansättning av indatavärden för `devicemake` och `devicemodel`, och en hash av sammanfogad värdet.

Se [köra exemplen](#running) för hur du kör det här exemplet på ditt HDInsight-kluster.

## <a name="pigpython"></a>Pig UDF

Python-skriptet kan användas som en UDF från Pig via den `GENERATE` instruktionen. Du kan köra skriptet med Jython eller C Python.

* Jython körs på JVM och internt kan anropas från Pig.
* C Python är en extern process så att data från Pig på JVM skickas till det skript som körs i en Python-process. Utdata från skriptet Python skickas tillbaka till Pig.

Om du vill ange Python-tolkning `register` referera till Python-skriptet. I följande exempel registrera skript med Pig som `myfuncs`:

* **Att använda Jython**: `register '/path/to/pigudf.py' using jython as myfuncs;`
* **Att använda C Python**: `register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]
> När du använder Jython, sökvägen till filen pig_jython kan vara en lokal sökväg eller en WASB: / / sökväg. När du använder C Python, måste du referera en fil på det lokala filsystemet på den nod som du använder för att skicka Pig-jobbet.

En gång tidigare registrering, Pig Latin för det här exemplet är samma för både:

```pig
LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Här är det här exemplet har:

1. Den första raden läser in data exempelfilen `sample.log` till `LOGS`. Varje post som definierar också en `chararray`.
2. Nästa rad filtrerar ut eventuella null-värden som lagrar resultatet av åtgärden i `LOG`.
3. Därefter den itererar över poster i `LOG` och använder `GENERATE` att anropa den `create_structure` metod i Python/Jython-skript som läses in som `myfuncs`. `LINE` används för att skicka den aktuella posten till funktionen.
4. Slutligen utdata dumpas STDOUT med hjälp av den `DUMP` kommando. Detta kommando visar resultaten när åtgärden har slutförts.

### <a name="create-the-pigudfpy-file"></a>Skapa filen pigudf.py

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

I exemplet Pig Latin i `LINE` indata har definierats som en chararray eftersom det finns inget konsekvent schema för indata. Python-skriptet omvandlar data till ett konsekvent schema för utdata.

1. Den `@outputSchema` uttrycket definierar formatet för data som returneras till Pig. I det här fallet har en **data egenskapsuppsättning**, vilket är en Pig-datatyp. Uppsättningen innehåller följande fält som är chararray (strängar):

   * datum – datumet loggposten skapades
   * tid - tiden loggposten skapades
   * Klassnamn - klassnamnet posten skapades för
   * nivå - loggningsnivån
   * detaljer - utförlig information för loggposten

2. Sedan den `def create_structure(input)` definierar vilken funktion Pig skickar objekt till.

3. Exempeldata `sample.log`, främst överensstämmer med datum, tid, classname, nivå, och innehåller information om schemat. Men den innehåller några rader som börjar med `*java.lang.Exception*`. Dessa rader måste ändras för att matcha schemat. Den `if` instruktionen kontrollerar för dem och sedan massages indata för att flytta den `*java.lang.Exception*` strängen i syfte att samla data i-raden med schemat för utdata som förväntas.

4. Sedan den `split` kommandot används för att dela data på de första fyra blanksteg. Utdata har tilldelats till `date`, `time`, `classname`, `level`, och `detail`.

5. Slutligen returneras värdena till Pig.

När data returneras till Pig, har ett konsekvent schema som definierats i den `@outputSchema` instruktionen.

## <a name="running"></a>Ladda upp och köra exemplen

> [!IMPORTANT]
> Den **SSH** stegen fungerar endast med en Linux-baserade HDInsight-kluster. Den **PowerShell** stegen fungerar med en Linux- eller Windows-baserade HDInsight-kluster, men kräver en Windows-klient.

### <a name="ssh"></a>SSH

Mer information om hur du använder SSH finns [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Använd `scp` att kopiera filer till ditt HDInsight-kluster. Till exempel följande kommando kopierar filerna till ett kluster med namnet **mycluster**.

    ```bash
    scp hiveudf.py pigudf.py myuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Använda SSH för att ansluta till klustret.

    ```bash
    ssh myuser@mycluster-ssh.azurehdinsight.net
    ```

    Mer information finns i dokumentet [Använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. Lägga till python-filer som tidigare har överförts till WASB-lagring för klustret från SSH-session.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    hdfs dfs -put pigudf.py /pigudf.py
    ```

Använd följande steg när du överför filerna för att köra Hive och Pig-jobb.

#### <a name="use-the-hive-udf"></a>Använda Hive UDF

1. Om du vill ansluta till registreringsdatafilen, använder du följande kommando:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    Detta kommando startar Beeline-klienten.

2. Ange följande fråga i den `0: jdbc:hive2://headnodehost:10001/>` prompten:

   ```hive
   add file wasb:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. När du har angett den sista raden ska jobbet starta. När jobbet har slutförts, returnerar utdata liknar följande exempel:

        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

4. Om du vill avsluta Beeline, använder du följande kommando:

    ```hive
    !q
    ```

#### <a name="use-the-pig-udf"></a>Använda Pig UDF

1. För att ansluta till pig, använder du följande kommando:

    ```bash
    pig
    ```

2. Ange följande uttryck på den `grunt>` prompten:

   ```pig
   Register wasb:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. När du har angett följande rad ska jobbet starta. När jobbet har slutförts, returnerar utdata liknar följande uppgifter:

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. Använd `quit` att lämna gränssnittet Grunt och Använd sedan följande för att redigera filen pigudf.py på det lokala filsystemet:

    ```bash
    nano pigudf.py
    ```

5. En gång i redigeraren, ta bort kommentarerna följande rad genom att ta bort den `#` tecken från början av raden:

    ```bash
    #from pig_util import outputSchema
    ```

    Den här raden ändrar Python-skript för att arbeta med C Python i stället för Jython. När ändringen har gjorts, använder **Ctrl + X** att avsluta redigeraren. Välj **Y**, och sedan **RETUR** spara ändringarna.

6. Använd den `pig` kommando för att starta gränssnittet igen. När du är på den `grunt>` uppmanar, Använd följande för att köra skriptet Python med Python C-tolken.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    När jobbet har slutförts, bör du se samma utdata som när du redan har kört skriptet med Jython.

### <a name="powershell-upload-the-files"></a>PowerShell: Överför filerna

Du kan använda PowerShell för att överföra filer till HDInsight-servern. Använd följande skript för att ladda upp Python-filer:

> [!IMPORTANT] 
> Stegen i det här avsnittet använder Azure PowerShell. Mer information om hur du använder Azure PowerShell finns [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=5-41)]

> [!IMPORTANT]
> Ändra den `C:\path\to` värde till sökvägen till filerna på din utvecklingsmiljö.

Detta skript hämtar information om ditt HDInsight-kluster och sedan extraherar konto och nyckel för standardkontot för lagring och överför filer till roten i behållaren.

> [!NOTE]
> Mer information om överföringen av filer finns i [överföra data för Hadoop-jobb i HDInsight](../hdinsight-upload-data.md) dokumentet.

#### <a name="powershell-use-the-hive-udf"></a>PowerShell: Använda Hive UDF

PowerShell kan också användas för att köra Hive-frågor via fjärranslutning. Använd följande PowerShell-skript för att köra en Hive-fråga som använder **hiveudf.py** skript:

> [!IMPORTANT]
> Innan du kör uppmanas du att HTTPs/Admin kontoinformationen för ditt HDInsight-kluster i skriptet.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=45-94)]

Utdata för den **Hive** jobbet ska se ut ungefär så här:

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

#### <a name="pig-jython"></a>Pig (Jython)

PowerShell kan också användas för att köra Pig Latin-jobb. Att köra ett Pig Latin-jobb som använder den **pigudf.py** skript, Använd följande PowerShell-skript:

> [!NOTE]
> När du skickar in ett jobb som använder PowerShell via fjärranslutning, går det inte att använda C Python som tolkens.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=98-144)]

Utdata för den **Pig** jobb bör likna följande uppgifter:

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a>Felsökning

### <a name="errors-when-running-jobs"></a>Fel när du kör jobb

När du kör hive-jobb kan som uppstå ett fel som liknar följande:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Det här problemet kan orsakas av radbrytningar i Python-fil. Många Windows redigerare som standard använder CRLF som rad avslutas, men Linux-program vanligtvis förväntas LF.

Du kan använda följande PowerShell-instruktioner för att ta bort CR-tecken före överföra filen till HDInsight:

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>PowerShell-skript

Både exempel PowerShell-skript som används för att köra exemplen innehåller en kommenterad rad som visar Felutdata för jobbet. Om du inte ser utdata som förväntas för jobbet, ta bort kommentarerna följande rad och se om information om felet uppstått problem.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

Information om felet (STDERR) och resultatet av jobbet (STDOUT) loggas också i HDInsight-lagringen.

| För det här jobbet... | Titta på de här filerna i blob-behållaren |
| --- | --- |
| Hive |/ HivePython/stderr<p>/ HivePython/stdout |
| Pig |/PigPython/stderr<p>/ PigPython/stdout |

## <a name="next"></a>Nästa steg

Om du behöver läsa in Python-moduler som inte är som standard finns [hur du distribuerar en modul till Azure HDInsight](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx).

Andra sätt att använda svin, Hive, och Läs om hur du använder MapReduce i följande dokument:

* [Använda Hive med HDInsight](hdinsight-use-hive.md)
* [Använda Pig med HDInsight](hdinsight-use-pig.md)
* [Använda MapReduce med HDInsight](hdinsight-use-mapreduce.md)
