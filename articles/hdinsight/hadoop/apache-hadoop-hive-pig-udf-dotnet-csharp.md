---
title: "Använda C# med Hive och Pig med Hadoop i HDInsight - Azure | Microsoft Docs"
description: "Lär dig använda C# användardefinierade funktioner (UDF) med Hive och Pig strömning i Azure HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d83def76-12ad-4538-bb8e-3ba3542b7211
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/04/2017
ms.author: larryfr
ms.openlocfilehash: 38b6c153905d556bc9407a6cb5531a46ca17f5eb
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="use-c-user-defined-functions-with-hive-and-pig-streaming-on-hadoop-in-hdinsight"></a>Använda C# användardefinierade funktioner med Hive och Pig strömning på Hadoop i HDInsight

Lär dig använda C# användardefinierade funktioner (UDF) med Apache Hive och Pig på HDInsight.

> [!IMPORTANT]
> Stegen i det här dokumentet fungerar med både Linux- och Windows-baserade HDInsight-kluster. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight component-versioning](../hdinsight-component-versioning.md).

Både Hive och Pig kan överföra data till externa program för bearbetning. Den här processen kallas _strömning_. När du använder ett .NET-program, data skickas till programmet på STDIN och programmet returnerar resultat i STDOUT. Du kan använda för att läsa och skriva från STDIN och STDOUT `Console.ReadLine()` och `Console.WriteLine()` från ett konsolprogram.

## <a name="prerequisites"></a>Krav

* Tidigare erfarenhet av skrivning och bygga C#-kod som riktar sig till .NET Framework 4.5.

    * Använd oavsett IDE som du vill använda. Vi rekommenderar [Visual Studio](https://www.visualstudio.com/vs) 2015, 2017, eller [Visual Studio Code](https://code.visualstudio.com/). Stegen i det här dokumentet använder Visual Studio 2017.

* Ett sätt att överföra .exe-filer till klustret och köra Pig och Hive-jobb. Vi rekommenderar Data Lake-verktyg för Visual Studio, Azure PowerShell och Azure CLI. Stegen i det här dokumentet används Data Lake-verktyg för Visual Studio för att överföra filer och köra exemplet Hive frågan.

    Information om andra sätt att köra Hive-frågor och Pig-jobb, finns i följande dokument:

    * [Använda Apache Hive med HDInsight](hdinsight-use-hive.md)

    * [Använda Apache Pig med HDInsight](hdinsight-use-pig.md)

* En Hadoop på HDInsight-kluster. Mer information om hur du skapar ett kluster finns [skapar ett HDInsight-kluster](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="net-on-hdinsight"></a>.NET på HDInsight

* __Linux-baserat HDInsight__ kluster med [Mono (https://mono-project.com)](https://mono-project.com) att köra .NET-program. Monoljud version 4.2.1 ingår i HDInsight version 3.5.

    Mer information om monoljud kompatibilitet med .NET Framework-versioner finns [monoljud kompatibilitet](http://www.mono-project.com/docs/about-mono/compatibility/).

    Om du vill använda en viss version av Mono, finns det [installera eller uppdatera Mono](../hdinsight-hadoop-install-mono.md) dokumentet.

* __Windows-baserade HDInsight__ kluster använder Microsoft .NET CLR för att köra .NET-program.

Mer information om versionen av .NET framework och Mono som ingår i HDInsight-versioner finns [HDInsight komponenten versioner](../hdinsight-component-versioning.md).

## <a name="create-the-c-projects"></a>Skapa C\# projekt

### <a name="hive-udf"></a>Hive UDF

1. Öppna Visual Studio och skapa en lösning. Projekttypen, Välj **Konsolapp (.NET Framework)**, och namnet på det nya projektet **HiveCSharp**.

    > [!IMPORTANT]
    > Välj __.NET Framework 4.5__ om du använder en Linux-baserade HDInsight-kluster. Mer information om monoljud kompatibilitet med .NET Framework-versioner finns [monoljud kompatibilitet](http://www.mono-project.com/docs/about-mono/compatibility/).

2. Ersätt innehållet i **Program.cs** med följande kod:

    ```csharp
    using System;
    using System.Security.Cryptography;
    using System.Text;
    using System.Threading.Tasks;

    namespace HiveCSharp
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Parse the string, trimming line feeds
                    // and splitting fields at tabs
                    line = line.TrimEnd('\n');
                    string[] field = line.Split('\t');
                    string phoneLabel = field[1] + ' ' + field[2];
                    // Emit new data to stdout, delimited by tabs
                    Console.WriteLine("{0}\t{1}\t{2}", field[0], phoneLabel, GetMD5Hash(phoneLabel));
                }
            }
            /// <summary>
            /// Returns an MD5 hash for the given string
            /// </summary>
            /// <param name="input">string value</param>
            /// <returns>an MD5 hash</returns>
            static string GetMD5Hash(string input)
            {
                // Step 1, calculate MD5 hash from input
                MD5 md5 = System.Security.Cryptography.MD5.Create();
                byte[] inputBytes = System.Text.Encoding.ASCII.GetBytes(input);
                byte[] hash = md5.ComputeHash(inputBytes);

                // Step 2, convert byte array to hex string
                StringBuilder sb = new StringBuilder();
                for (int i = 0; i < hash.Length; i++)
                {
                    sb.Append(hash[i].ToString("x2"));
                }
                return sb.ToString();
            }
        }
    }
    ```

3. Bygga projektet.

### <a name="pig-udf"></a>Pig UDF

1. Öppna Visual Studio och skapa en lösning. Projekttypen, Välj **konsolprogram**, och namnet på det nya projektet **PigUDF**.

2. Ersätt innehållet i den **Program.cs** med följande kod:

    ```csharp
    using System;

    namespace PigUDF
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Fix formatting on lines that begin with an exception
                    if(line.StartsWith("java.lang.Exception"))
                    {
                        // Trim the error info off the beginning and add a note to the end of the line
                        line = line.Remove(0, 21) + " - java.lang.Exception";
                    }
                    // Split the fields apart at tab characters
                    string[] field = line.Split('\t');
                    // Put fields back together for writing
                    Console.WriteLine(String.Join("\t",field));
                }
            }
        }
    }
    ```

    Den här koden Parsar rader skickas från Pig och formaterar om rader som börjar med `java.lang.Exception`.

3. Spara **Program.cs**, och sedan skapa projektet.

## <a name="upload-to-storage"></a>Överföra till lagring

1. Öppna i Visual Studio **Server Explorer**.

2. Expandera **Azure** och expandera därefter **HDInsight**.

3. Om du uppmanas ange dina autentiseringsuppgifter för Azure-prenumeration och klicka sedan på **logga In**.

4. Expandera HDInsight-klustret som du vill distribuera programmet till. En post med texten __(standard Storage-konto)__ visas.

    ![Server Explorer visar lagringskontot för klustret](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/storage.png)

    * Om den här posten kan utökas måste du använder en __Azure Storage-konto__ som standardlagring för klustret. Om du vill visa filerna på lagringsutrymmet som standard för klustret, expandera posten och dubbelklicka sedan på den __(standardbehållaren)__.

    * Om den här posten inte kan expanderas, använder du __Azure Data Lake Store__ som standardlagring för klustret. Om du vill visa filerna på lagringsutrymmet som standard för klustret, dubbelklickar du på den __(standardkontot för lagring)__ post.

6. Använd någon av följande metoder för att ladda upp .exe-filer:

    * Om du använder en __Azure Storage-konto__, klicka på ikonen överföringen och bläddra sedan till den **bin\debug** mapp för den **HiveCSharp** projekt. Välj slutligen den **HiveCSharp.exe** fil och klicka på **Ok**.

        ![Överför ikon](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/upload.png)
    
    * Om du använder __Azure Data Lake Store__, högerklicka på ett tomt område i listan över filer och välj sedan __överför__. Välj slutligen den **HiveCSharp.exe** fil och klicka på **öppna**.

    En gång i __HiveCSharp.exe__ överföringen har slutförts, Upprepa processen för att ladda upp den __PigUDF.exe__ fil.

## <a name="run-a-hive-query"></a>Köra en Hive-fråga

1. Öppna i Visual Studio **Server Explorer**.

2. Expandera **Azure** och expandera därefter **HDInsight**.

3. Högerklicka på klustret som du har distribuerat den **HiveCSharp** program till och markera sedan **skriva en Hive-fråga**.

4. Använd följande text för Hive-frågan:

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasb:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Store
    -- add file adl:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    > [!IMPORTANT]
    > Ta bort kommentarerna i `add file` -uttryck som matchar typ av standard lagringsutrymme som används för klustret.

    Den här frågan markeras den `clientid`, `devicemake`, och `devicemodel` fält från `hivesampletable`, och skickar fälten till HiveCSharp.exe-programmet. Frågan förväntar sig programmet att returnera tre fält, som lagras som `clientid`, `phoneLabel`, och `phoneHash`. Frågan också förväntas HiveCSharp.exe i roten av standardbehållare för lagring.

5. Klicka på **skicka** att skicka jobb till HDInsight-klustret. Den **Hive-jobbsammanfattning** öppnas.

6. Klicka på **uppdatera** att uppdatera sammanfattning tills **jobbstatus** ändras till **slutförd**. Om du vill visa jobbutdata klickar du på **Jobbutdata**.

## <a name="run-a-pig-job"></a>Kör ett Pig-jobb

1. Använd någon av följande metoder för att ansluta till ditt HDInsight-kluster:

    * Om du använder en __Linux-baserade__ HDInsight-kluster använder SSH. Till exempel `ssh sshuser@mycluster-ssh.azurehdinsight.net`. Mer information finns i [använda SSH withHDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)
    
    * Om du använder en __Windows-baserade__ HDInsight-kluster [Anslut till klustret med hjälp av fjärrskrivbord](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)

2. Använd en följande kommando för att starta Pig-kommandoraden:

        pig

    > [!IMPORTANT]
    > Om du använder ett Windows-baserade kluster använder du följande kommandon:
    > ```
    > cd %PIG_HOME%
    > bin\pig
    > ```

    En `grunt>` visas frågan.

3. Ange följande om du vill köra ett Pig-jobb som använder .NET Framework-program:

        DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
        LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
        DUMP DETAILS;

    Den `DEFINE` uttrycket skapar ett alias för `streamer` för pigudf.exe-program och `CACHE` läser från standardlagring för klustret. Senare, `streamer` används tillsammans med den `STREAM` operatorn för att bearbeta de rader som finns i LOGGEN och returnera data som en serie kolumner.

    > [!NOTE]
    > Namnet på programmet som används för strömning måste omges av den \` (backtick) tecken när ett alias, och ' (enkelt citattecken) när det används med `SHIP`.

4. När du har angett den sista raden ska jobbet starta. Den returnerar utdata liknar följande:

        (2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
        (2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
        (2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du använder .NET Framework-program från Hive och Pig på HDInsight. Om du vill lära dig hur du använder Python med Hive och Pig [använder Python med Hive och Pig i HDInsight](python-udf-hdinsight.md).

Andra sätt att använda Pig och Hive och lära dig mer om MapReduce finns i följande dokument:

* [Använda Hive med HDInsight](hdinsight-use-hive.md)
* [Använda Pig med HDInsight](hdinsight-use-pig.md)
* [Använda MapReduce med HDInsight](hdinsight-use-mapreduce.md)
