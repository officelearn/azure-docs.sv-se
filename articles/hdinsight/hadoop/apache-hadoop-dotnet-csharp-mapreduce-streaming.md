---
title: "Använda C# med MapReduce med Hadoop i HDInsight - Azure | Microsoft Docs"
description: "Lär dig använda C# för att skapa lösningar för MapReduce med Hadoop i Azure HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d83def76-12ad-4538-bb8e-3ba3542b7211
ms.custom: hdinsightactive
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: 0db3df1666a56ca96af208a4e43f814105de40d5
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="use-c-with-mapreduce-streaming-on-hadoop-in-hdinsight"></a>Använda C# med MapReduce strömning på Hadoop i HDInsight

Lär dig använda C# för att skapa en lösning för MapReduce på HDInsight.

> [!IMPORTANT]
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight component-versioning](../hdinsight-component-versioning.md).

Hadoop streaming är ett verktyg som låter dig köra MapReduce-jobb med hjälp av ett skript eller en körbar fil. I det här exemplet används .NET för att implementera mapper och reducer för en word antal lösning.

## <a name="net-on-hdinsight"></a>.NET på HDInsight

__Linux-baserat HDInsight__ kluster Använd [Mono (https://mono-project.com)](https://mono-project.com) att köra .NET-program. Monoljud version 4.2.1 ingår i HDInsight version 3,6. Mer information om versionen av Mono som ingår i HDInsight finns [HDInsight komponenten versioner](../hdinsight-component-versioning.md). Om du vill använda en viss version av Mono, finns det [installera eller uppdatera Mono](../hdinsight-hadoop-install-mono.md) dokumentet.

Mer information om monoljud kompatibilitet med .NET Framework-versioner finns [monoljud kompatibilitet](http://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="how-hadoop-streaming-works"></a>Så här fungerar Hadoop-strömning

Den grundläggande processen som används för strömning i det här dokumentet är följande:

1. Hadoop skickar data till mapper (mapper.exe i det här exemplet) på STDIN.
2. Mapparen bearbetar data och skickar tabbavgränsad nyckel/värde-par STDOUT.
3. Utdata läses av Hadoop och skickas sedan till reducer (reducer.exe i det här exemplet) STDIN.
4. Reducer läser tabbavgränsad nyckel/värde-par, bearbetar data och skickar sedan resultatet som tabbavgränsad nyckel/värde-par i STDOUT.
5. Utdata läses av Hadoop och skrivs till den angivna katalogen.

Mer information om strömning finns [Hadoop Streaming (https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html)](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Förutsättningar

* Tidigare erfarenhet av skrivning och bygga C#-kod som riktar sig till .NET Framework 4.5. Stegen i det här dokumentet använder Visual Studio 2017.

* Ett sätt att överföra .exe-filer till klustret. Stegen i det här dokumentet använder Data Lake-verktyg för Visual Studio för att överföra filer till primära lagring för klustret.

* Azure PowerShell eller en SSH-klient.

* En Hadoop på HDInsight-kluster. Mer information om hur du skapar ett kluster finns [skapar ett HDInsight-kluster](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="create-the-mapper"></a>Skapa mapparen

I Visual Studio skapar en ny __Konsolapp__ med namnet __mapper__. Använd följande kod för programmet:

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

I Visual Studio skapar en ny __Konsolapp__ med namnet __reducer__. Använd följande kod för programmet:

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

## <a name="upload-to-storage"></a>Överföra till lagring

1. Öppna i Visual Studio **Server Explorer**.

2. Expandera **Azure** och expandera därefter **HDInsight**.

3. Om du uppmanas ange dina autentiseringsuppgifter för Azure-prenumeration och klicka sedan på **logga In**.

4. Expandera HDInsight-klustret som du vill distribuera programmet till. En post med texten __(standard Storage-konto)__ visas.

    ![Server Explorer visar lagringskontot för klustret](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/storage.png)

    * Om den här posten kan utökas måste du använder en __Azure Storage-konto__ som standardlagring för klustret. Om du vill visa filerna på lagringsutrymmet som standard för klustret, expandera posten och dubbelklicka sedan på den __(standardbehållaren)__.

    * Om den här posten inte kan expanderas, använder du __Azure Data Lake Store__ som standardlagring för klustret. Om du vill visa filerna på lagringsutrymmet som standard för klustret, dubbelklickar du på den __(standardkontot för lagring)__ post.

5. Använd någon av följande metoder för att ladda upp .exe-filer:

    * Om du använder en __Azure Storage-konto__, klicka på ikonen överföringen och bläddra sedan till den **bin\debug** mapp för den **mapper** projekt. Välj slutligen den **mapper.exe** fil och klicka på **Ok**.

        ![Överför ikon](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/upload.png)
    
    * Om du använder __Azure Data Lake Store__, högerklicka på ett tomt område i listan över filer och välj sedan __överför__. Välj slutligen den **mapper.exe** fil och klicka på **öppna**.

    En gång i __mapper.exe__ överföringen har slutförts, Upprepa processen för att ladda upp den __reducer.exe__ fil.

## <a name="run-a-job-using-an-ssh-session"></a>Kör ett jobb: använder en SSH-session

1. Använda SSH för att ansluta till HDInsight-klustret. Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. Använd något av följande kommandon för att starta MapReduce-jobb:

    * Om du använder __Datasjölager__ som standardlagring:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files adl:///mapper.exe,adl:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
        ```
    
    * Om du använder __Azure Storage__ som standardlagring:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files wasb:///mapper.exe,wasb:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
        ```

    I följande lista beskrivs vad varje parameter är:

    * `hadoop-streaming.jar`: Jar-fil som innehåller funktioner för direktuppspelning MapReduce.
    * `-files`: Lägger till den `mapper.exe` och `reducer.exe` filer till det här jobbet. Den `adl:///` eller `wasb:///` innan varje fil är sökvägen till roten i standardlagring för klustret.
    * `-mapper`: Anger vilken fil som implementerar mapparen.
    * `-reducer`: Anger vilken fil som implementerar reducer.
    * `-input`: Indata.
    * `-output`: Den angivna katalogen.

3. När MapReduce-jobbet har slutförts kan du använda följande för att visa resultatet:

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    Följande är ett exempel på de data som returneras av kommandot:

        you     1128
        young   38
        younger 1
        youngest        1
        your    338
        yours   4
        yourself        34
        yourselves      3
        youth   17

## <a name="run-a-job-using-powershell"></a>Kör ett jobb: med hjälp av PowerShell

Använd följande PowerShell-skript för att köra ett MapReduce-jobb och hämta resultaten.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Det här skriptet uppmanas du att klustret konto inloggningsnamn och lösenord, tillsammans med HDInsight-klustrets namn. När jobbet har slutförts hämtas utdata till en fil med namnet `output.txt`. Följande är ett exempel på data i den `output.txt` filen:

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

Mer information om hur du använder MapReduce med HDInsight finns [använda MapReduce med HDInsight](hdinsight-use-mapreduce.md).

Information om hur du använder C# med Hive och Pig finns [använder en C# användardefinierad funktion med Hive och Pig](apache-hadoop-hive-pig-udf-dotnet-csharp.md).

Information om hur du använder C# med Storm på HDInsight finns i [utveckla C#-topologier för Storm på HDInsight](../storm/apache-storm-develop-csharp-visual-studio-topology.md).