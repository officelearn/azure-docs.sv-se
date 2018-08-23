---
title: Använda C# med MapReduce med Hadoop i HDInsight - Azure
description: Lär dig hur du använder C# för att skapa lösningar för MapReduce med Hadoop i Azure HDInsight.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: jasonh
ms.openlocfilehash: ef0001d5693d96abc128acf6c052dbedc0983b51
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "42055625"
---
# <a name="use-c-with-mapreduce-streaming-on-hadoop-in-hdinsight"></a>Använda C# med MapReduce med Hadoop i HDInsight för direktuppspelning

Lär dig hur du använder C# för att skapa en lösning för MapReduce på HDInsight.

> [!IMPORTANT]
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [versionshantering för HDInsight](../hdinsight-component-versioning.md).

Hadoop streaming är ett verktyg som låter dig köra MapReduce-jobb med hjälp av ett skript eller en körbar fil. I det här exemplet används .NET för att implementera mapper och reducer för en lösning för word antal.

## <a name="net-on-hdinsight"></a>.NET på HDInsight

__Linux-baserade HDInsight__ kluster Använd [Mono (https://mono-project.com) ](https://mono-project.com) att köra .NET-program. Mono version 4.2.1 medföljer HDInsight version 3.6. Läs mer på vilken version av Mono som medföljer HDInsight [HDInsight komponenten versioner](../hdinsight-component-versioning.md). Om du vill använda en specifik version av Mono se den [installera eller uppdatera Mono](../hdinsight-hadoop-install-mono.md) dokumentet.

Läs mer om Mono kompatibilitet med .NET Framework-versioner, [Mono-kompatibilitet](http://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="how-hadoop-streaming-works"></a>Så här fungerar Hadoop streaming

Den grundläggande processen som används för direktuppspelning i det här dokumentet är följande:

1. Hadoop skickar data till mapper (mapper.exe i det här exemplet) på STDIN.
2. Mappningen bearbetar data och genererar tabbavgränsad nyckel/värde-par STDOUT.
3. Utdata är läses av Hadoop och skickas sedan till reducer (reducer.exe i det här exemplet) på STDIN.
4. Reducer läser tabbavgränsad nyckel/värde-par, bearbeta data och sedan genererar resultatet som tabbavgränsad nyckel/värde-par i STDOUT.
5. Utdata är läses av Hadoop och skrivs till katalogen.

Läs mer på strömmande [Hadoop Streaming](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Förutsättningar

* Tidigare erfarenhet av att skriva och att skapa C#-kod som riktas mot .NET Framework 4.5. Stegen i det här dokumentet använder Visual Studio 2017.

* Ett sätt att överföra .exe-filer till klustret. Stegen i det här dokumentet använder Data Lake Tools för Visual Studio för att ladda upp filer till primär lagring för klustret.

* Azure PowerShell eller en SSH-klient.

* Ett Hadoop på HDInsight-kluster. Mer information om hur du skapar ett kluster finns i [skapar ett HDInsight-kluster](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="create-the-mapper"></a>Skapa mappningen

I Visual Studio skapar du en ny __Konsolapp__ med namnet __mapper__. Använd följande kod för programmet:

```csharp
using System;
using System.Text.RegularExpressions;

namespace mapper
{
    class Program
    {
        static void Main(string[] args)
        {
            string line;
            //Hadoop passes data to the mapper on STDIN
            while((line = Console.ReadLine()) != null)
            {
                // We only want words, so strip out punctuation, numbers, etc.
                var onlyText = Regex.Replace(line, @"\.|;|:|,|[0-9]|'", "");
                // Split at whitespace.
                var words = Regex.Matches(onlyText, @"[\w]+");
                // Loop over the words
                foreach(var word in words)
                {
                    //Emit tab-delimited key/value pairs.
                    //In this case, a word and a count of 1.
                    Console.WriteLine("{0}\t1",word);
                }
            }
        }
    }
}
```

När du har skapat programmet, skapar du det att skapa den `/bin/Debug/mapper.exe` filen i projektkatalogen.

## <a name="create-the-reducer"></a>Skapa reducer

I Visual Studio skapar du en ny __Konsolapp__ med namnet __reducer__. Använd följande kod för programmet:

```csharp
using System;
using System.Collections.Generic;

namespace reducer
{
    class Program
    {
        static void Main(string[] args)
        {
            //Dictionary for holding a count of words
            Dictionary<string, int> words = new Dictionary<string, int>();

            string line;
            //Read from STDIN
            while ((line = Console.ReadLine()) != null)
            {
                // Data from Hadoop is tab-delimited key/value pairs
                var sArr = line.Split('\t');
                // Get the word
                string word = sArr[0];
                // Get the count
                int count = Convert.ToInt32(sArr[1]);

                //Do we already have a count for the word?
                if(words.ContainsKey(word))
                {
                    //If so, increment the count
                    words[word] += count;
                } else
                {
                    //Add the key to the collection
                    words.Add(word, count);
                }
            }
            //Finally, emit each word and count
            foreach (var word in words)
            {
                //Emit tab-delimited key/value pairs.
                //In this case, a word and a count of 1.
                Console.WriteLine("{0}\t{1}", word.Key, word.Value);
            }
        }
    }
}
```

När du har skapat programmet, skapar du det att skapa den `/bin/Debug/reducer.exe` filen i projektkatalogen.

## <a name="upload-to-storage"></a>Ladda upp till storage

1. Öppna i Visual Studio **Server Explorer**.

2. Expandera **Azure** och expandera därefter **HDInsight**.

3. Om du uppmanas ange dina autentiseringsuppgifter för Azure-prenumeration och klicka sedan på **logga In**.

4. Expandera det HDInsight-kluster som du vill distribuera programmet till. En post med texten __(standard Storage-konto)__ visas.

    ![Server Explorer visar storage-konto för klustret](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/storage.png)

    * Om den här posten kan utökas, som du använder en __Azure Storage-konto__ som standardlagringen för klustret. Om du vill visa filerna på standardlagringen för klustret, expanderar du posten och dubbelklicka sedan på den __(standardbehållaren)__.

    * Om den här posten inte kan expanderas, använder du __Azure Data Lake Store__ som standardlagringen för klustret. Om du vill visa filerna på standardlagringen för klustret, dubbelklickar du på den __(standard Storage-konto)__ posten.

5. Använd någon av följande metoder för att ladda upp .exe-filer:

    * Om du använder en __Azure Storage-konto__, klickar du på ikonen ladda upp och bläddra sedan till den **bin\debug** mapp för den **mapper** projekt. Välj slutligen den **mapper.exe** fil och klicka på **Ok**.

        ![ladda upp ikon](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/upload.png)
    
    * Om du använder __Azure Data Lake Store__, högerklicka på ett tomt område i listan över filer och välj sedan __överför__. Välj slutligen den **mapper.exe** fil och klicka på **öppna**.

    När den __mapper.exe__ överföringen har slutförts, upprepa uppladdningsprocessen för att ladda den __reducer.exe__ fil.

## <a name="run-a-job-using-an-ssh-session"></a>Köra ett jobb: med hjälp av en SSH-session

1. Använda SSH för att ansluta till HDInsight-kluster. Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. Använd något av följande kommandon för att starta MapReduce-jobb:

    * Om du använder __Data Lake Store__ som standardlagringsutrymme:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files adl:///mapper.exe,adl:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
        ```
    
    * Om du använder __Azure Storage__ som standardlagringsutrymme:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files wasb:///mapper.exe,wasb:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
        ```

    I följande lista beskrivs vad varje parameter gör:

    * `hadoop-streaming.jar`: Den jar-fil som innehåller funktioner för direktuppspelning MapReduce.
    * `-files`: Lägger till den `mapper.exe` och `reducer.exe` filer till det här jobbet. Den `adl:///` eller `wasb:///` innan varje fil måste sökvägen till roten för standardlagringen för klustret.
    * `-mapper`: Anger vilken fil implementerar mappningen.
    * `-reducer`: Anger vilken fil implementerar reducer.
    * `-input`: Indata.
    * `-output`: Katalogen.

3. När MapReduce-jobbet har slutförts kan du använda följande för att visa resultatet:

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    Följande text är ett exempel på data som returneras av det här kommandot:

        you     1128
        young   38
        younger 1
        youngest        1
        your    338
        yours   4
        yourself        34
        yourselves      3
        youth   17

## <a name="run-a-job-using-powershell"></a>Köra ett jobb: med hjälp av PowerShell

Använd följande PowerShell-skript för att köra ett MapReduce-jobb och hämta resultaten.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Det här skriptet uppmanas du att kontot för klustrets inloggningsnamn och lösenord, tillsammans med HDInsight-klusternamnet. När jobbet har slutförts hämtas utdata till en fil med namnet `output.txt`. Följande text är ett exempel på data i den `output.txt` fil:

    you     1128
    young   38
    younger 1
    youngest        1
    your    338
    yours   4
    yourself        34
    yourselves      3
    youth   17

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder MapReduce med HDInsight finns i [använda MapReduce med HDInsight](hdinsight-use-mapreduce.md).

Information om hur du använder C# med Hive och Pig finns i [använda en C#-användardefinierade funktion med Hive och Pig](apache-hadoop-hive-pig-udf-dotnet-csharp.md).

Information om hur du använder C# med Storm på HDInsight finns i [utveckla C#-topologier för Storm på HDInsight](../storm/apache-storm-develop-csharp-visual-studio-topology.md).
