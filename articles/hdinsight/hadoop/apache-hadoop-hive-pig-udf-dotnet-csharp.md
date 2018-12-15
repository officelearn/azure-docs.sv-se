---
title: Använd C# med Apache Hive och Apache Pig på Apache Hadoop i HDInsight - Azure
description: Lär dig hur du använder C# användardefinierade funktioner (UDF) med Apache Hive och Apache Pig strömning i Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: e45c5a37c4ba12d93ff7f78bb465cb650a7faffb
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2018
ms.locfileid: "53435097"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-streaming-on-apache-hadoop-in-hdinsight"></a>Använd C# användardefinierade funktioner med Apache Hive och Apache Pig strömning på Apache Hadoop i HDInsight

Lär dig hur du använder C# användardefinierade funktioner (UDF) med Apache Hive och Apache Pig i HDInsight.

> [!IMPORTANT]
> Stegen i det här dokumentet fungerar med både Linux- och Windows-baserade HDInsight-kluster. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [versionshantering för HDInsight](../hdinsight-component-versioning.md).

Både Hive och Pig kan överföra data till externa program för bearbetning. Den här processen kallas _streaming_. När du använder ett .NET-program, data skickas till programmet på STDIN och programmet returnerar resultat i STDOUT. Du kan använda för att läsa och skriva från STDIN och STDOUT `Console.ReadLine()` och `Console.WriteLine()` från ett konsolprogram.

## <a name="prerequisites"></a>Förutsättningar

* Tidigare erfarenhet av att skriva och att skapa C#-kod som riktas mot .NET Framework 4.5.

    * Använd den IDE som du vill. Vi rekommenderar att [Visual Studio](https://www.visualstudio.com/vs) 2015, 2017, eller [Visual Studio Code](https://code.visualstudio.com/). Stegen i det här dokumentet använder Visual Studio 2017.

* Ett sätt att överföra .exe-filer till klustret och köra Pig och Hive-jobb. Vi rekommenderar Data Lake Tools för Visual Studio, Azure PowerShell och Azure klassiskt CLI. Stegen i det här dokumentet använder Data Lake Tools för Visual Studio för att ladda upp filerna och kör exemplet Hive-fråga.

    Information om andra sätt att köra Hive-frågor och Pig-jobb, finns i följande dokument:

    * [Använda Apache Hive med HDInsight](hdinsight-use-hive.md)

    * [Använda Apache Pig med HDInsight](hdinsight-use-pig.md)

* Ett Hadoop på HDInsight-kluster. Mer information om hur du skapar ett kluster finns i [skapar ett HDInsight-kluster](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="net-on-hdinsight"></a>.NET på HDInsight

* __Linux-baserade HDInsight__ kluster med hjälp av [Mono (https://mono-project.com) ](https://mono-project.com) att köra .NET-program. Mono version 4.2.1 medföljer HDInsight version 3.6.

    Läs mer om Mono kompatibilitet med .NET Framework-versioner, [Mono-kompatibilitet](https://www.mono-project.com/docs/about-mono/compatibility/).

    Om du vill använda en specifik version av Mono se den [installera eller uppdatera Mono](../hdinsight-hadoop-install-mono.md) dokumentet.

* __Windows-baserade HDInsight__ kluster använder Microsoft .NET CLR för att köra .NET-program.

Läs mer på vilken version av .NET framework och Mono som medföljer HDInsight-versioner, [HDInsight komponenten versioner](../hdinsight-component-versioning.md).

## <a name="create-the-c-projects"></a>Skapa C\# projekt

### <a name="apache-hive-udf"></a>Apache Hive UDF

1. Öppna Visual Studio och skapa en lösning. Projekttyp, Välj **Konsolapp (.NET Framework)**, och namnge det nya projektet **HiveCSharp**.

    > [!IMPORTANT]
    > Välj __.NET Framework 4.5__ om du använder en Linux-baserade HDInsight-kluster. Läs mer om Mono kompatibilitet med .NET Framework-versioner, [Mono-kompatibilitet](https://www.mono-project.com/docs/about-mono/compatibility/).

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

### <a name="apache-pig-udf"></a>Apache Pig UDF

1. Öppna Visual Studio och skapa en lösning. Projekttyp, Välj **konsolprogram**, och namnge det nya projektet **PigUDF**.

2. Ersätt innehållet i den **Program.cs** filen med följande kod:

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

    Den här koden Parsar raderna som skickas från Pig och förbereder rader som börjar med `java.lang.Exception`.

3. Spara **Program.cs**, och skapa projektet.

## <a name="upload-to-storage"></a>Ladda upp till storage

1. Öppna i Visual Studio **Server Explorer**.

2. Expandera **Azure** och expandera därefter **HDInsight**.

3. Om du uppmanas ange dina autentiseringsuppgifter för Azure-prenumeration och klicka sedan på **logga In**.

4. Expandera det HDInsight-kluster som du vill distribuera programmet till. En post med texten __(standard Storage-konto)__ visas.

    ![Server Explorer visar storage-konto för klustret](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/storage.png)

    * Om den här posten kan utökas, som du använder en __Azure Storage-konto__ som standardlagringen för klustret. Om du vill visa filerna på standardlagringen för klustret, expanderar du posten och dubbelklicka sedan på den __(standardbehållaren)__.

    * Om den här posten inte kan expanderas, använder du __Azure Data Lake Storage__ som standardlagringen för klustret. Om du vill visa filerna på standardlagringen för klustret, dubbelklickar du på den __(standard Storage-konto)__ posten.

6. Använd någon av följande metoder för att ladda upp .exe-filer:

    * Om du använder en __Azure Storage-konto__, klickar du på ikonen ladda upp och bläddra sedan till den **bin\debug** mapp för den **HiveCSharp** projekt. Välj slutligen den **HiveCSharp.exe** fil och klicka på **Ok**.

        ![ladda upp ikon](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/upload.png)
    
    * Om du använder __Azure Data Lake Storage__, högerklicka på ett tomt område i listan över filer och välj sedan __överför__. Välj slutligen den **HiveCSharp.exe** fil och klicka på **öppna**.

    När den __HiveCSharp.exe__ överföringen har slutförts, upprepa uppladdningsprocessen för att ladda den __PigUDF.exe__ fil.

## <a name="run-an-apache-hive-query"></a>Kör ett Apache Hive-fråga

1. Öppna i Visual Studio **Server Explorer**.

2. Expandera **Azure** och expandera därefter **HDInsight**.

3. Högerklicka på det kluster som du har distribuerat den **HiveCSharp** program till och välj sedan **Skriv en Hive-fråga**.

4. Använd följande text för Hive-frågan:

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasb:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage
    -- add file adl:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    > [!IMPORTANT]
    > Ta bort kommentarerna i `add file` -instruktion som matchar typ av standardlagring som används för klustret.

    Den här frågan väljer den `clientid`, `devicemake`, och `devicemodel` fält från `hivesampletable`, och skickar fälten till HiveCSharp.exe-programmet. Frågan förväntar sig programmet att returnera tre fält, som lagras som `clientid`, `phoneLabel`, och `phoneHash`. Frågan förväntar sig även att hitta HiveCSharp.exe i roten av standardbehållare för lagring.

5. Klicka på **skicka** att skicka jobb till HDInsight-kluster. Den **Hive-jobbsammanfattning** öppnas.

6. Klicka på **uppdatera** att uppdatera sammanfattning tills **jobbstatus** ändras till **slutförd**. Om du vill visa jobbutdata, klickar du på **Jobbutdata**.

## <a name="run-an-apache-pig-job"></a>Kör ett Apache Pig-jobb

1. Använd någon av följande metoder för att ansluta till ditt HDInsight-kluster:

    * Om du använder en __Linux-baserade__ HDInsight-kluster, använda SSH. Till exempel `ssh sshuser@mycluster-ssh.azurehdinsight.net`. Mer information finns i [använda SSH withHDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)
    
    * Om du använder en __Windows-baserade__ HDInsight-kluster, [Anslut till klustret med hjälp av fjärrskrivbord](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)

2. Använd en följande kommando för att starta Pig från kommandoraden:

        pig

    > [!IMPORTANT]
    > Om du använder ett Windows-baserat kluster, använder du följande kommandon:
    > ```
    > cd %PIG_HOME%
    > bin\pig
    > ```

    En `grunt>` uppmaning visas.

3. Ange följande om du vill köra ett Pig-jobb som använder .NET Framework-program:

        DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
        LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
        DUMP DETAILS;

    Den `DEFINE` -uttrycket skapar ett alias för `streamer` för pigudf.exe-program och `CACHE` läses den in från standardlagringen för klustret. Senare, `streamer` används med den `STREAM` operator för att bearbeta de enda rader i LOGGEN och returnera data som en serie kolumner.

    > [!NOTE]
    > Namnet på programmet som används för direktuppspelning måste omges av den \` (backtick) tecken när ett alias, och ”(enkelt citattecken) när det används med `SHIP`.

4. När du har angett den sista raden ska jobbet starta. Den returnerar utdata som liknar följande text:

        (2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
        (2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
        (2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du använder .NET Framework-program från Hive och Pig i HDInsight. Om du vill lära dig hur du använder Python med Hive och Pig kan du läsa [använda Python med Apache Hive och Apache Pig i HDInsight](python-udf-hdinsight.md).

Andra sätt att använda Pig och Hive och Läs om hur du använder MapReduce, finns i följande dokument:

* [Använda Apache Hive med HDInsight](hdinsight-use-hive.md)
* [Använda Apache Pig med HDInsight](hdinsight-use-pig.md)
* [Använda MapReduce med HDInsight](hdinsight-use-mapreduce.md)
