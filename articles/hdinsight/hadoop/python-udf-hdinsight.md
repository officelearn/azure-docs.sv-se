---
title: Python UDF med Apache Hive och Apache Pig - Azure HDInsight
description: Lär dig hur du använder Python användaren användardefinierade funktioner (UDF) från Apache Hive och Apache Pig i HDInsight, Apache Hadoop-teknikstacken på Azure.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 02/27/2018
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 92221e5aaebbaebb2af17ea211e38a3665a2b04f
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2018
ms.locfileid: "53652481"
---
# <a name="use-python-user-defined-functions-udf-with-apache-hive-and-apache-pig-in-hdinsight"></a>Använd användardefinierade Python funktioner (UDF) med Apache Hive och Apache Pig i HDInsight

Lär dig hur du använder Python användardefinierade funktioner (UDF) med Apache Hive och Apache Pig i Apache Hadoop på Azure HDInsight.

## <a name="python"></a>Python på HDInsight

Python2.7 installeras som standard på HDInsight 3.0 och senare. Apache Hive kan användas med den här versionen av Python för bearbetning av dataströmmen. Stream bearbetning använder STDOUT- och STDIN för att överföra data mellan Hive och en användardefinierad funktion.

HDInsight omfattar även Jython, vilket är en Python-implementering skriven i Java. Jython körs direkt på Java Virtual Machine och används inte för direktuppspelning. Jython är den rekommenderade Python-tolken när du använder Python med Pig.

> [!WARNING]  
> Stegen i det här dokumentet gör följande antaganden: 
>
> * Du kan skapa Python-skript på din lokala utvecklingsmiljö.
> * Du överför skript till HDInsight med antingen den `scp` från en lokal Bash-session eller det angivna PowerShell-skriptet.
>
> Om du vill använda den [Azure Cloud Shell (bash)](https://docs.microsoft.com/azure/cloud-shell/overview) förhandsversion att arbeta med HDInsight, måste du:
>
> * Skapa skript i cloud shell-miljön.
> * Använd `scp` att ladda upp filer från cloudshell till HDInsight.
> * Använd `ssh` från cloudshell för att ansluta till HDInsight och köra exemplen.

## <a name="hivepython"></a>Apache Hive UDF

Python kan användas som en UDF från Hive via HiveQL `TRANSFORM` instruktionen. Till exempel följande HiveQL anropar den `hiveudf.py` -fil som lagras i Azure Storage-kontot för klustret.

**Linux-baserat HDInsight**

```hiveql
add file wasb:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLabel string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

**Windows-baserade HDInsight**

```hiveql
add file wasb:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'D:\Python27\python.exe hiveudf.py' AS
    (clientid string, phoneLabel string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

> [!NOTE]  
> På Windows-baserade HDInsight-kluster i `USING` -satsen måste innehålla den fullständiga sökvägen till python.exe.

Här är vad som gör det här exemplet:

1. Den `add file` instruktion i början av filen lägger till den `hiveudf.py` filen till distribuerad cache, så är den tillgänglig för alla noder i klustret.
2. Den `SELECT TRANSFORM ... USING` instruktionen väljer data från den `hivesampletable`. Den skickar även värden för clientid, devicemake och devicemodel till den `hiveudf.py` skript.
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

Det här skriptet utför följande åtgärder:

1. Läsa en rad med data från STDIN.
2. Avslutande radmatningstecken tas bort med hjälp av `string.strip(line, "\n ")`.
3. När du gör för strömbearbetning innehåller alla värden med ett tabbtecken mellan varje värde i en enda rad. Så `string.split(line, "\t")` kan användas för att dela upp inmatningen vid varje flik, returnerar enbart fält.
4. När bearbetningen är klar, måste utdata skrivas till STDOUT som en enda rad med en flik mellan varje fält. Till exempel `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. Den `while` loopen upprepas tills Nej `line` läses.

Utdata från skriptet är en sammansättning av indatavärden för `devicemake` och `devicemodel`, och en hash av sammansatt värde.

Se [köra exemplen](#running) för hur du kör det här exemplet på ditt HDInsight-kluster.

## <a name="pigpython"></a>Apache Pig UDF

Ett Python-skript som kan användas som en UDF från Pig via den `GENERATE` instruktionen. Du kan köra skriptet med Jython eller C Python.

* Jython körs på JVM och internt kan anropas från Pig.
* C Python är en extern process så att data från Pig på JVM skickas ut till det skript som körs i en Python-process. Utdata från Python-skriptet skickas tillbaka till Pig.

Om du vill ange Python-tolk använda `register` när du refererar till Python-skriptet. I följande exempel registrera skript med Pig som `myfuncs`:

* **Att använda Jython**: `register '/path/to/pigudf.py' using jython as myfuncs;`
* **Du använder C Python**: `register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]  
> När du använder Jython, sökvägen till filen pig_jython kan vara antingen en lokal sökväg eller en WASB: / / sökväg. När du använder C Python, måste du referera en fil på det lokala filsystemet för noden som du använder för att skicka Pig-jobbet.

En gång tidigare registreringen Pig Latin för det här exemplet är samma för både:

```pig
LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Här är vad som gör det här exemplet:

1. Den första raden läser in Exempeldatafilen, `sample.log` till `LOGS`. Den definierar även varje post som en `chararray`.
2. Nästa rad filtrerar ut några null-värden som lagrar resultatet av åtgärden i `LOG`.
3. Därefter den itererar över poster i `LOG` och använder `GENERATE` att anropa den `create_structure` metod i Python/Jython-skript som läses in som `myfuncs`. `LINE` används för att skicka den aktuella posten till funktionen.
4. Slutligen utdata dumpas STDOUT med hjälp av den `DUMP` kommando. Det här kommandot visar resultat när åtgärden har slutförts.

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

## <a name="running"></a>Ladda upp och köra exemplen

> [!IMPORTANT]  
> Den **SSH** stegen fungerar endast med ett Linux-baserade HDInsight-kluster. Den **PowerShell** steg fungera med en Linux- eller Windows-baserade HDInsight-kluster, men kräver en Windows-klient.

### <a name="ssh"></a>SSH

Mer information om hur du använder SSH finns i [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Använd `scp` att kopiera filer till ditt HDInsight-kluster. Till exempel följande kommando kopierar filerna till ett kluster med namnet **mycluster**.

    ```bash
    scp hiveudf.py pigudf.py myuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Använda SSH för att ansluta till klustret.

    ```bash
    ssh myuser@mycluster-ssh.azurehdinsight.net
    ```

    Mer information finns i dokumentet [Använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. Lägg till python-filer som tidigare har överförts till WASB-lagring för klustret från SSH-sessionen.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    hdfs dfs -put pigudf.py /pigudf.py
    ```

När du har överfört filerna använder du följande steg för att köra Hive och Pig-jobb.

#### <a name="use-the-hive-udf"></a>Använda Hive UDF

1. Om du vill ansluta till Hive, använder du följande kommando:

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

3. När du har angett den sista raden ska jobbet starta. När jobbet har slutförts, returnerar den utdata som liknar följande exempel:

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

1. Om du vill ansluta till pig, använder du följande kommando:

    ```bash
    pig
    ```

2. Ange följande instruktioner i den `grunt>` prompten:

   ```pig
   Register wasb:///pigudf.py using jython as myfuncs;
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
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    När det här jobbet har slutförts bör du se samma utdata som tidigare när du körde skriptet med Jython.

### <a name="powershell-upload-the-files"></a>PowerShell: Ladda upp filer

Du kan använda PowerShell för att överföra filer till HDInsight-servern. Använd följande skript för att ladda upp Python-filer:

> [!IMPORTANT]   
> Stegen i det här avsnittet använder Azure PowerShell. Mer information om hur du använder Azure PowerShell finns i [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=5-41)]

> [!IMPORTANT]
> Ändra den `C:\path\to` värdet till sökvägen till filerna på din utvecklingsmiljö.

Detta skript hämtar information för ditt HDInsight-kluster och sedan extraherar konto och nyckel för standardkontot för lagring och överför filer till roten för behållaren.

> [!NOTE]  
> Mer information om överföringen av filer finns i den [överföra data för Apache Hadoop-jobb i HDInsight](../hdinsight-upload-data.md) dokumentet.

#### <a name="powershell-use-the-hive-udf"></a>PowerShell: Använda Hive UDF

PowerShell kan också användas för att köra Hive-frågor via en fjärranslutning. Använd följande PowerShell-skript för att köra en Hive-fråga som använder **hiveudf.py** skript:

> [!IMPORTANT]  
> Innan du kör efterfrågas skriptet HTTPs/Admin kontoinformationen för ditt HDInsight-kluster.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=45-94)]

Utdata för den **Hive** jobb bör se ut ungefär så här:

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

#### <a name="pig-jython"></a>Pig (Jython)

PowerShell kan också användas för att köra Pig Latin-jobb. Att köra ett Pig Latin-jobb som använder den **pigudf.py** skript, Använd följande PowerShell-skript:

> [!NOTE]  
> När du skickar in ett jobb med hjälp av PowerShell via fjärranslutning, går det inte att använda C Python som vilken tolk.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=98-144)]

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
| Hive |/ HivePython/stderr<p>/ HivePython/stdout |
| Pig |/PigPython/stderr<p>/ PigPython/stdout |

## <a name="next"></a>Nästa steg

Om du vill läsa in Python-moduler som inte tillhandahålls som standard kan se [hur du distribuerar en modul till Azure HDInsight](https://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx).

Andra sätt att använda Apache Pig, Hive, och Läs om hur du använder MapReduce i följande dokument:

* [Använda Apache Hive med HDInsight](hdinsight-use-hive.md)
* [Använda Apache Pig med HDInsight](hdinsight-use-pig.md)
* [Använda MapReduce med HDInsight](hdinsight-use-mapreduce.md)
