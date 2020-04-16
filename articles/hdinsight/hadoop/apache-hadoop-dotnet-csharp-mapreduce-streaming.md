---
title: Använd C# med MapReduce på Hadoop i HDInsight - Azure
description: Lär dig hur du använder C# för att skapa MapReduce-lösningar med Apache Hadoop i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: 7a299ce16f6e9c7292cebf198c9c3077f8e05fcb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417615"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>Använd C# med MapReduce streaming på Apache Hadoop i HDInsight

Lär dig hur du använder C# för att skapa en MapReduce-lösning på HDInsight.

Apache Hadoop streaming kan du köra MapReduce jobb med hjälp av ett skript eller körbar. Här används .NET för att implementera mapper och reducer för en ordräkningslösning.

## <a name="net-on-hdinsight"></a>.NET på HDInsight

HDInsight-kluster använder [Mono (https://mono-project.com) ](https://mono-project.com) för att köra .NET-program. Mono version 4.2.1 ingår i HDInsight version 3.6. Mer information om den version av Mono som ingår i HDInsight finns i [Apache Hadoop-komponenter som finns med olika HDInsight-versioner](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions).

Mer information om monokompatibilitet med .NET Framework-versioner finns i [Mono-kompatibilitet](https://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="how-hadoop-streaming-works"></a>Så fungerar Hadoop streaming

Den grundläggande process som används för direktuppspelning i det här dokumentet är följande:

1. Hadoop skickar data till mapper *(mapper.exe* i det här exemplet) på STDIN.
2. Mappern bearbetar data och avger tab-avgränsade nyckel-/värdepar till STDOUT.
3. Utdata läss av Hadoop och skickas sedan till reducer *(reducer.exe* i det här exemplet) på STDIN.
4. Reducer läser av tabbavgränsade nyckel-/värdepar, bearbetar data och avger sedan resultatet som tabbavgränsade nyckel-/värdepar på STDOUT.
5. Utdata läss av Hadoop och skrivs till utdatakatalogen.

Mer information om direktuppspelning finns i [Hadoop Streaming](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Krav

* Visual Studio.

* En förtrogenhet med att skriva och bygga C# kod som riktar sig till .NET Framework 4.5.

* Ett sätt att överföra EXE-filer till klustret. I stegen i det här dokumentet används DataSjöverktyg för Visual Studio för att överföra filerna till primär lagring för klustret.

* Om du använder PowerShell behöver du [Az-modulen](https://docs.microsoft.com/powershell/azure/overview).

* En Apache Hadoop kluster på HDInsight. Se [Komma igång med HDInsight på Linux](../hadoop/apache-hadoop-linux-tutorial-get-started.md).

* [URI-schemat](../hdinsight-hadoop-linux-information.md#URI-and-scheme) för klustrets primära lagring. Det här `wasb://` schemat skulle `abfs://` vara för Azure Storage, `adl://` för Azure Data Lake Storage Gen2 eller för Azure Data Lake Storage Gen1. Om säker överföring är aktiverad för Azure Storage eller Data `wasbs://` Lake `abfss://`Storage Gen2, skulle URI vara eller , respektive Se även [säker överföring](../../storage/common/storage-require-secure-transfer.md).

## <a name="create-the-mapper"></a>Skapa mappern

Skapa ett nytt .NET Framework-konsolprogram med namnet *mapper*i Visual Studio . Använd följande kod för programmet:

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

När du har skapat programmet skapar du det för att skapa filen */bin/Debug/mapper.exe* i projektkatalogen.

## <a name="create-the-reducer"></a>Skapa reduceraren

Skapa ett nytt .NET Framework-konsolprogram med namnet *reducer*i Visual Studio . Använd följande kod för programmet:

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

När du har skapat programmet skapar du det för att skapa filen */bin/Debug/reducer.exe* i projektkatalogen.

## <a name="upload-to-storage"></a>Ladda upp till lagring

Därefter måste du ladda upp *mapper-* och *reducerprogram* till HDInsight-lagring.

1. Välj **Visa** > **serverutforskaren**i Visual Studio.

1. Högerklicka på **Azure,** välj **Anslut till Microsoft Azure-prenumeration...** och slutför inloggningsprocessen.

1. Expandera HDInsight-klustret som du vill distribuera det här programmet till. En post med texten **(Standardlagringskonto)** visas.

   ![Lagringskonto, HDInsight-kluster, Server Explorer, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png)

   * Om **posten (standardlagringskonto)** kan utökas använder du ett **Azure Storage Account** som standardlagringskonto för klustret. Om du vill visa filerna i standardlagringen för klustret expanderar du posten och dubbelklickar sedan **(standardbehållare).**

   * Om posten **(Standardlagringskonto)** inte kan utökas använder du **Azure Data Lake Storage** som standardlagring för klustret. Om du vill visa filerna i standardlagringen för klustret dubbelklickar du på posten **(Standardlagringskonto).**

1. Om du vill ladda upp EXE-filerna använder du någon av följande metoder:

    * Om du använder ett **Azure Storage-konto**väljer du ikonen **Ladda upp blob.**

        ![HdInsight-uppladdningsikon för mapper, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png)

        Välj **Bläddra**under **Filnamn**i dialogrutan **Ladda upp ny fil** . Gå till mappen *bin\debug* för *mapper-projektet* i dialogrutan **Ladda upp blob** och välj sedan filen *mapper.exe.* Slutligen väljer du **Öppna** och sedan **OK** för att slutföra överföringen.

    * För **Azure Data Lake Storage**högerklickar du på ett tomt område i fillistan och väljer sedan Ladda **upp**. Slutligen markerar du filen *mapper.exe* och väljer sedan **Öppna**.

    När uppladdningen *mapper.exe* har slutförts upprepar du uppladdningsprocessen för filen *reducer.exe.*

## <a name="run-a-job-using-an-ssh-session"></a>Köra ett jobb: Använda en SSH-session

I följande procedur beskrivs hur du kör ett MapReduce-jobb med hjälp av en SSH-session:

1. Använd kommandot ssh för att ansluta till [klustret.](../hdinsight-hadoop-linux-use-ssh-unix.md) Redigera kommandot nedan genom att ersätta CLUSTERNAME med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Använd något av följande kommandon för att starta MapReduce-jobbet:

   * Om standardlagringen är **Azure Storage:**

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files wasbs:///mapper.exe,wasbs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

    * Om standardlagringen är **Data Lake Storage Gen1:**

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files adl:///mapper.exe,adl:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   * Om standardlagringen är **Data Lake Storage Gen2:**

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files abfs:///mapper.exe,abfs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   I följande lista beskrivs vad varje parameter och alternativ representerar:

   |Parameter | Beskrivning |
   |---|---|
   |hadoop-streaming.jar|Anger den jar-fil som innehåller funktionen för direktuppspelning mapreduce.|
   |-filer|Anger filerna *mapper.exe* och *reducer.exe* för det här jobbet. Den `wasbs:///` `adl:///`, `abfs:///` , eller protokolldeklarationen före varje fil är sökvägen till roten för standardlagring för klustret.|
   |-mapper|Anger filen som implementerar mappern.|
   |-reducer|Anger den fil som implementerar reduceraren.|
   |-ingång|Anger indata.|
   |-utgång|Anger utdatakatalogen.|

1. När MapReduce-jobbet är klart använder du följande kommando för att visa resultaten:

   ```bash
   hdfs dfs -text /example/wordcountout/part-00000
   ```

   Följande text är ett exempel på de data som returneras av det här kommandot:

   ```output
   you     1128
   young   38
   younger 1
   youngest        1
   your    338
   yours   4
   yourself        34
   yourselves      3
   youth   17
   ```

## <a name="run-a-job-using-powershell"></a>Köra ett jobb: Använda PowerShell

Använd följande PowerShell-skript för att köra ett MapReduce-jobb och hämta resultaten.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Det här skriptet uppmanar dig att ange namn och lösenord för klustrets inloggningskonto tillsammans med hdinsight-klusternamnet. När jobbet är klart hämtas utdata till en fil med namnet *output.txt*. Följande text är ett exempel på `output.txt` data i filen:

```output
you     1128
young   38
younger 1
youngest        1
your    338
yours   4
yourself        34
yourselves      3
youth   17
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder MapReduce med HDInsight finns [i Använd MapReduce i Apache Hadoop på HDInsight](hdinsight-use-mapreduce.md).

Information om hur du använder C# med Hive och Pig finns i [Använda en C#-användardefinierad funktion med Apache Hive och Apache Pig](apache-hadoop-hive-pig-udf-dotnet-csharp.md).

Information om hur du använder C# with Storm on HDInsight finns i [Utveckla C# topologier för Apache Storm på HDInsight](../storm/apache-storm-develop-csharp-visual-studio-topology.md).
