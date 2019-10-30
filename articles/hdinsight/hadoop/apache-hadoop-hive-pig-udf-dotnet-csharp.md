---
title: C#, Apache Hive & Apache gris på Apache Hadoop-Azure HDInsight
description: Lär dig hur du C# använder användardefinierade funktioner (UDF) med Apache Hive och Apache gris streaming i Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: hrasheed
ms.openlocfilehash: 222b91b2efefa81186d32fee7229aa0cc4f13a63
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044596"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-on-apache-hadoop-in-hdinsight"></a>Använd C# användardefinierade funktioner med Apache Hive och Apache gris på Apache Hadoop i HDInsight

Lär dig hur du C# använder användardefinierade funktioner (UDF) med Apache Hive och Apache gris på HDInsight.

> [!IMPORTANT]
> Stegen i det här dokumentet fungerar med både Linux-baserade och Windows-baserade HDInsight-kluster. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [versions hantering av HDInsight-komponenter](../hdinsight-component-versioning.md).

Både Hive och gris kan skicka data till externa program för bearbetning. Den här processen kallas för _strömning_. När du använder ett .NET-program skickas data till programmet på STDIN och programmet returnerar resultatet i STDOUT. Om du vill läsa och skriva från STDIN och STDOUT kan du använda `Console.ReadLine()` och `Console.WriteLine()` från ett konsol program.

## <a name="prerequisites"></a>Krav

* En välbekanthet med att skriva C# och skapa kod som är riktad mot .NET Framework 4,5.

    * Använd vilken IDE du vill. Vi rekommenderar [Visual studio](https://www.visualstudio.com/vs) 2015, 2017 eller [Visual Studio Code](https://code.visualstudio.com/). Stegen i det här dokumentet använder Visual Studio 2017.

* Ett sätt att ladda upp exe-filer till klustret och köra gris-och Hive-jobb. Vi rekommenderar Data Lake verktyg för Visual Studio, Azure PowerShell och klassisk Azure-CLI. Stegen i det här dokumentet använder Data Lake verktyg för Visual Studio för att ladda upp filerna och köra exempel filen Hive-fråga.

    Information om andra sätt att köra Hive-frågor och gris-jobb finns i följande dokument:

    * [Använda Apache Hive med HDInsight](hdinsight-use-hive.md)

    * [Använda Apache gris med HDInsight](hdinsight-use-pig.md)

* Ett Hadoop på HDInsight-kluster. Mer information om hur du skapar ett kluster finns i [skapa ett HDInsight-kluster](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="net-on-hdinsight"></a>.NET på HDInsight

* __Linux-baserade HDInsight-__ kluster med [mono (https://mono-project.com)](https://mono-project.com) att köra .NET-program. Mono version 4.2.1 ingår i HDInsight version 3,6.

    Mer information om mono-kompatibilitet med .NET Framework-versioner finns i [mono-kompatibilitet](https://www.mono-project.com/docs/about-mono/compatibility/).

* __Windows-baserade HDInsight-__ kluster använder Microsoft .NET CLR för att köra .NET-program.

Mer information om den version av .NET Framework och mono som ingår i HDInsight-versioner finns i versions [versioner för HDInsight](../hdinsight-component-versioning.md).

## <a name="create-the-c-projects"></a>Skapa C\#-projekt

### <a name="apache-hive-udf"></a>Apache Hive UDF

1. Öppna Visual Studio och skapa en lösning. För projekt typen väljer du **konsol program (.NET Framework)** och namnger den nya Project- **HiveCSharp**.

    > [!IMPORTANT]
    > Välj __.NET Framework 4,5__ om du använder ett Linux-baserat HDInsight-kluster. Mer information om mono-kompatibilitet med .NET Framework-versioner finns i [mono-kompatibilitet](https://www.mono-project.com/docs/about-mono/compatibility/).

2. Ersätt innehållet i **program.cs** med följande kod:

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

### <a name="apache-pig-udf"></a>Apache gris UDF

1. Öppna Visual Studio och skapa en lösning. För projekt typen väljer du **konsol program**och namnger den nya Project- **PigUDF**.

2. Ersätt innehållet i **program.cs** -filen med följande kod:

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

    Den här koden analyserar de rader som skickas från gris och formaterar om rader som börjar med `java.lang.Exception`.

3. Spara **program.cs**och bygg sedan projektet.

## <a name="upload-to-storage"></a>Ladda upp till lagring

1. Öppna **Server Explorer**i Visual Studio.

2. Expandera **Azure** och expandera därefter **HDInsight**.

3. Om du uppmanas till det anger du dina autentiseringsuppgifter för Azure-prenumerationen och klickar sedan på **Logga**in.

4. Expandera det HDInsight-kluster som du vill distribuera programmet till. En post med texten __(standard lagrings kontot)__ visas.

    ![Server Explorer som visar lagrings kontot för klustret](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-storage-account.png)

    * Om du kan expandera den här posten använder du ett __Azure Storage konto__ som standard lagring för klustret. Om du vill visa filerna på standard lagrings utrymmet för klustret expanderar du posten och dubbelklickar sedan på __(standard container)__ .

    * Om den här posten inte kan utökas använder du __Azure Data Lake Storage__ som standard lagring för klustret. Om du vill visa filerna på standard lagrings utrymmet för klustret dubbelklickar du på posten __(standard lagrings konto)__ .

6. Använd någon av följande metoder för att ladda upp exe-filerna:

   * Om du använder ett __Azure Storage konto__klickar du på ikonen Ladda upp och bläddrar sedan till mappen **bin\debug** för **HiveCSharp** -projektet. Välj slutligen filen **HiveCSharp. exe** och klicka på **OK**.

       ![HDInsight upload-ikon för nytt projekt](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-upload-icon.png)
    
   * Om du använder __Azure Data Lake Storage__högerklickar du på ett tomt utrymme i fil listan och väljer sedan __Ladda upp__. Välj slutligen filen **HiveCSharp. exe** och klicka på **Öppna**.

     När __HiveCSharp. exe__ -överföringen är färdig upprepar du överförings processen för filen __PigUDF. exe__ .

## <a name="run-an-apache-hive-query"></a>Köra en Apache Hive fråga

1. Öppna **Server Explorer**i Visual Studio.

2. Expandera **Azure** och expandera därefter **HDInsight**.

3. Högerklicka på det kluster som du har distribuerat **HiveCSharp** -programmet till och välj sedan **Skriv en Hive-fråga**.

4. Använd följande text för Hive-frågan:

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasb:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen1
    -- add file adl:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen2
    -- add file abfs:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    > [!IMPORTANT]
    > Ta bort kommentaren `add file`-instruktionen som matchar den typ av standard lagring som används för klustret.

    Den här frågan väljer fälten `clientid`, `devicemake`och `devicemodel` från `hivesampletable`och skickar fälten till programmet HiveCSharp. exe. Frågan förväntar sig att programmet ska returnera tre fält, som lagras som `clientid`, `phoneLabel`och `phoneHash`. Frågan förväntar sig också att hitta HiveCSharp. exe i roten för standard lagrings behållaren.

5. Klicka på **Skicka** för att skicka jobbet till HDInsight-klustret. Fönstret **Sammanfattning av Hive-jobb** öppnas.

6. Klicka på **Uppdatera** för att uppdatera sammanfattningen tills **jobbets status** ändras till **slutförd**. Klicka på **jobbets utdata**om du vill visa jobbets utdata.

## <a name="run-an-apache-pig-job"></a>Köra ett Apache gris-jobb

1. Använd SSH för att ansluta till ditt HDInsight-kluster. Till exempel `ssh sshuser@mycluster-ssh.azurehdinsight.net`. Mer information finns i [använda SSH-withHDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)

2. Använd något av följande kommando för att starta kommando raden för gris:

        pig

    > [!IMPORTANT]
    > Om du använder ett Windows-baserat kluster använder du följande kommandon i stället:
    > ```
    > cd %PIG_HOME%
    > bin\pig
    > ```

    En `grunt>`-prompt visas.

3. Ange följande för att köra ett gris-jobb som använder .NET Framework-programmet:

        DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
        LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
        DUMP DETAILS;

    `DEFINE`-instruktionen skapar ett alias för `streamer` för pigudf. exe-program och `CACHE` läser in det från standard lagrings utrymmet för klustret. Senare används `streamer` med operatorn `STREAM` för att bearbeta de enskilda raderna i LOGGen och returnera data som en serie kolumner.

    > [!NOTE]
    > Det program namn som används för strömning måste omges av \` (baktick)-tecknet vid alias och "(enkelt citat tecken) när det används med `SHIP`.

4. När du har angett den sista raden ska jobbet starta. Den returnerar utdata som liknar följande text:

        (2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
        (2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
        (2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du använder ett .NET Framework program från Hive och gris i HDInsight. Om du vill lära dig hur du använder python med Hive och gris, se [Använd python med Apache Hive och Apache gris i HDInsight](python-udf-hdinsight.md).

Andra sätt att använda gris och Hive, och lär dig mer om hur du använder MapReduce finns i följande dokument:

* [Använda Apache Hive med HDInsight](hdinsight-use-hive.md)
* [Använda Apache gris med HDInsight](hdinsight-use-pig.md)
* [Använda MapReduce med HDInsight](hdinsight-use-mapreduce.md)
