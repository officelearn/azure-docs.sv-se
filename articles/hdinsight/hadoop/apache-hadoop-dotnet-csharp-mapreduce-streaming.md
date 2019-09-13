---
title: Använda C# med MapReduce på Hadoop i HDInsight – Azure
description: Lär dig hur du C# använder för att skapa MapReduce-lösningar med Apache Hadoop i Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: hrasheed
ms.openlocfilehash: 7f82ad65ecc805d5a45c78e8b190dd0eee4c340c
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70882322"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>Använd C# med MapReduce streaming på Apache Hadoop i HDInsight

Lär dig hur du C# använder för att skapa en MapReduce-lösning i HDInsight.

> [!IMPORTANT]
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [versions hantering av HDInsight-komponenter](../hdinsight-component-versioning.md).

Apache Hadoop strömning är ett verktyg som gör att du kan köra MapReduce-jobb med ett skript eller en körbar fil. I det här exemplet används .NET för att implementera mapper och minskaren för en ord räknings lösning.

## <a name="net-on-hdinsight"></a>.NET på HDInsight

__Linux-baserade HDInsight-__ kluster använder [mono (https://mono-project.com) ](https://mono-project.com) för att köra .NET-program. Mono version 4.2.1 ingår i HDInsight version 3,6. Mer information om den version av mono som ingår i HDInsight finns i [versioner av HDInsight-komponenter](../hdinsight-component-versioning.md). 

Mer information om mono-kompatibilitet med .NET Framework-versioner finns i [mono-kompatibilitet](https://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="how-hadoop-streaming-works"></a>Så här fungerar Hadoop streaming

Den grundläggande processen som används för strömning i det här dokumentet är följande:

1. Hadoop skickar data till Mapper (mapper. exe i det här exemplet) på STDIN.
2. Mapper bearbetar data och avgränsar TABB-avgränsade nyckel/värde-par till STDOUT.
3. Utdata läses av Hadoop och skickas sedan till minsknings tjänsten (reducerar. exe i det här exemplet) på STDIN.
4. Minsknings tjänsten läser in TABB-avgränsade nyckel/värde-par, bearbetar data och genererar sedan resultatet som TABB-avgränsade nyckel/värde-par i STDOUT.
5. Utdata läses av Hadoop och skrivs till utdata-katalogen.

Mer information om strömning finns i [Hadoop-direktuppspelning](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Förutsättningar

* En välbekanthet med att skriva C# och skapa kod som är riktad mot .NET Framework 4,5. Stegen i det här dokumentet använder Visual Studio 2017.

* Ett sätt att ladda upp exe-filer till klustret. Stegen i det här dokumentet använder Data Lake verktyg för Visual Studio för att överföra filerna till primär lagring för klustret.

* Azure PowerShell eller en SSH-klient.

* Ett Hadoop på HDInsight-kluster. Mer information om hur du skapar ett kluster finns i [skapa ett HDInsight-kluster](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="create-the-mapper"></a>Skapa mapper

Skapa ett nytt __konsol program__ med namnet __Mapper__i Visual Studio. Använd följande kod för programmet:

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

När du har skapat programmet skapar du det för att `/bin/Debug/mapper.exe` skapa filen i projekt katalogen.

## <a name="create-the-reducer"></a>Skapa reduceraren

Skapa ett nytt __konsol program__ med namnet __reduce__i Visual Studio. Använd följande kod för programmet:

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

När du har skapat programmet skapar du det för att `/bin/Debug/reducer.exe` skapa filen i projekt katalogen.

## <a name="upload-to-storage"></a>Ladda upp till lagring

1. Öppna **Server Explorer**i Visual Studio.

2. Expandera **Azure** och expandera därefter **HDInsight**.

3. Om du uppmanas till det anger du dina autentiseringsuppgifter för Azure-prenumerationen och klickar sedan på **Logga**in.

4. Expandera det HDInsight-kluster som du vill distribuera programmet till. En post med texten __(standard lagrings kontot)__ visas.

    ![Server Explorer som visar lagrings kontot för klustret](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png)

    * Om du kan expandera den här posten använder du ett __Azure Storage konto__ som standard lagring för klustret. Om du vill visa filerna på standard lagrings utrymmet för klustret expanderar du posten och dubbelklickar sedan på __(standard container)__ .

    * Om den här posten inte kan utökas använder du __Azure Data Lake Storage__ som standard lagring för klustret. Om du vill visa filerna på standard lagrings utrymmet för klustret dubbelklickar du på posten __(standard lagrings konto)__ .

5. Använd någon av följande metoder för att ladda upp exe-filerna:

   * Om du använder ett __Azure Storage konto__klickar du på ikonen Ladda upp och bläddrar sedan till mappen **bin\debug** för **Mapper** -projektet. Välj slutligen filen **mapper. exe** och klicka på **OK**.

        ![Ladda upp ikon](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png)
    
   * Om du använder __Azure Data Lake Storage__högerklickar du på ett tomt utrymme i fil listan och väljer sedan __Ladda upp__. Välj slutligen filen **mapper. exe** och klicka på **Öppna**.

     När filen __mapper. exe__ har laddats upp upprepar du överförings processen för filen __Reducer. exe__ .

## <a name="run-a-job-using-an-ssh-session"></a>Kör ett jobb: Använda en SSH-session

1. Använd SSH för att ansluta till HDInsight-klustret. Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. Använd något av följande kommandon för att starta MapReduce-jobbet:

   * Om du använder __data Lake Storage Gen2__ som standard lagring:

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files abfs:///mapper.exe,abfs:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```

   * Om du använder __data Lake Storage gen1__ som standard lagring:

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files adl:///mapper.exe,adl:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```
    
   * Om du använder __Azure Storage__ som standard lagring:

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files wasb:///mapper.exe,wasb:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```

     I följande lista beskrivs vad varje parameter gör:

   * `hadoop-streaming.jar`: Jar-filen som innehåller de strömmande MapReduce-funktionerna.
   * `-files`: Lägger till `reducer.exe` och-filer i det här jobbet. `mapper.exe` `abfs:///` Ellerförevarjefilärsökvägentillrotenförstandardlagrings`wasb:///` utrymmet för klustret.`adl:///`
   * `-mapper`: Anger vilken fil som implementerar mapper.
   * `-reducer`: Anger vilken fil som implementerar minskningen.
   * `-input`: Indata.
   * `-output`: Utdata-katalogen.

3. När MapReduce-jobbet har slutförts använder du följande för att visa resultaten:

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    Följande text är ett exempel på de data som returneras av det här kommandot:

        you     1128
        young   38
        younger 1
        youngest        1
        your    338
        yours   4
        yourself        34
        yourselves      3
        youth   17

## <a name="run-a-job-using-powershell"></a>Kör ett jobb: Använda PowerShell

Använd följande PowerShell-skript för att köra ett MapReduce-jobb och hämta resultaten.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

I det här skriptet uppmanas du att ange konto namnet och lösen ordet för klustrets inloggning, tillsammans med HDInsight-klustrets namn. När jobbet har slutförts hämtas utdata till en fil med namnet `output.txt`. Följande text är ett exempel på data i `output.txt` filen:

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

Information om hur du C# använder med Hive och gris finns i [använda C# en användardefinierad funktion med Apache Hive och Apache gris](apache-hadoop-hive-pig-udf-dotnet-csharp.md).

Information om hur du C# använder med storm på HDInsight finns [i C# utveckla topologier för Apache storm i HDInsight](../storm/apache-storm-develop-csharp-visual-studio-topology.md).
