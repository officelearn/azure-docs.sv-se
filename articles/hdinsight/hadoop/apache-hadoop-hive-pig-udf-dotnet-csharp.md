---
title: C#, Apache Hive & Apache Pig på Apache Hadoop - Azure HDInsight
description: Lär dig hur du använder C# användardefinierade funktioner (UDF) med Apache Hive och Apache Pig-streaming i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 9ef9eada9b9aec50642a8bf357edab0677868817
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74949397"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-on-apache-hadoop-in-hdinsight"></a>Använd C# användardefinierade funktioner med Apache Hive och Apache Pig på Apache Hadoop i HDInsight

Lär dig hur du använder C# användardefinierade funktioner (UDF) med [Apache Hive](https://hive.apache.org) och [Apache Pig](https://pig.apache.org) på HDInsight.

> [!IMPORTANT]
> Stegen i det här dokumentet fungerar med Linux-baserade HDInsight-kluster. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-komponentversionering](../hdinsight-component-versioning.md).

Både Hive och Pig kan skicka data till externa applikationer för bearbetning. Den här processen kallas _streaming_. När du använder ett .NET-program skickas data till programmet på STDIN och programmet returnerar resultaten på STDOUT. Om du vill läsa och skriva från STDIN och STDOUT kan du använda `Console.ReadLine()` och `Console.WriteLine()` från ett konsolprogram.

## <a name="prerequisites"></a>Krav

* En förtrogenhet med att skriva och bygga C# kod som riktar sig till .NET Framework 4.5.

    Använd vad IDE du vill. Vi rekommenderar [Visual Studio](https://www.visualstudio.com/vs) eller Visual [Studio Code](https://code.visualstudio.com/). I stegen i det här dokumentet används Visual Studio 2019.

* Ett sätt att överföra EXE-filer till klustret och köra Pig- och Hive-jobb. Vi rekommenderar [DataSjöverktyg för Visual Studio,](../../data-lake-analytics/data-lake-analytics-data-lake-tools-install.md) [Azure PowerShell](/powershell/azure)och [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). Stegen i det här dokumentet använder DataSjöverktyg för Visual Studio för att ladda upp filerna och köra exempel på Hive-frågan.

    Information om andra sätt att köra Hive-frågor finns i [Vad är Apache Hive och HiveQL på Azure HDInsight?](hdinsight-use-hive.md).

* En Hadoop på HDInsight-kluster. Mer information om hur du skapar ett kluster finns i [Skapa HDInsight-kluster](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="net-on-hdinsight"></a>.NET på HDInsight

*Linux-baserade HDInsight-kluster* använder [Mono (https://mono-project.com) ](https://mono-project.com) för att köra .NET-program. Mono version 4.2.1 ingår i HDInsight version 3.6.

Mer information om monokompatibilitet med .NET Framework-versioner finns i [Mono-kompatibilitet](https://www.mono-project.com/docs/about-mono/compatibility/).

Mer information om versionen av .NET Framework och Mono som ingår i HDInsight-versioner finns i [HDInsight-komponentversioner](../hdinsight-component-versioning.md).

## <a name="create-the-c-projects"></a>Skapa C-projekten\#

I följande avsnitt beskrivs hur du skapar ett C#-projekt i Visual Studio för en Apache Hive UDF och en Apache Pig UDF.

### <a name="apache-hive-udf"></a>Apache Hive UDF

Så här skapar du ett C#-projekt för en Apache Hive UDF:

1. Starta Visual Studio.

2. Välj **Skapa ett nytt projekt**.

3. I fönstret **Skapa ett nytt projekt** väljer du mallen Console App **(.NET Framework)** (C#-versionen). Välj sedan **Nästa**.

4. I fönstret **Konfigurera det nya projektet** anger du ett **projektnamn för** *HiveCSharp*och navigerar till eller skapar en **plats** att spara det nya projektet i. Välj sedan **Skapa**.

5. I Visual Studio IDE ersätter du innehållet i *Program.cs* med följande kod:

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

6. Välj **Bygg** > **bygglösning på** menyraden för att skapa projektet.

7. Stäng lösningen.

### <a name="apache-pig-udf"></a>Apache Gris UDF

Så här skapar du ett C#-projekt för en Apache Hive UDF:

1. Öppna Visual Studio.

2. Välj Skapa ett **nytt projekt**i **startfönstret** .

3. I fönstret **Skapa ett nytt projekt** väljer du mallen Console App **(.NET Framework)** (C#-versionen). Välj sedan **Nästa**.

4. I fönstret **Konfigurera det nya projektet** anger du ett **projektnamn på** *PigUDF*och går till eller skapar en **plats** att spara det nya projektet i. Välj sedan **Skapa**.

5. I Visual Studio IDE ersätter du innehållet i *Program.cs* med följande kod:

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

    Den här koden tolkar de rader som skickas `java.lang.Exception`från Pig och formaterar om rader som börjar med .

6. Välj **Bygg** > **bygglösning i** menyraden för att skapa projektet.

7. Låt lösningen vara öppen.

## <a name="upload-to-storage"></a>Ladda upp till lagring

Ladda sedan upp Hive- och Pig UDF-programmen till lagring på ett HDInsight-kluster.

1. I Visual Studio navigerar du till **Visa** > **Server Explorer**.

1. I **Server Explorer**högerklickar du på **Azure**, väljer Anslut till **Microsoft Azure-prenumeration**och slutför inloggningsprocessen.

1. Expandera HDInsight-klustret som du vill distribuera det här programmet till. En post med texten **(Standardlagringskonto)** visas.

    ![Standardlagringskonto, HDInsight-kluster, Server Explorer](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-storage-account.png)

    * Om den här posten kan utökas använder du ett **Azure Storage Account** som standardlagring för klustret. Om du vill visa filerna i standardlagringen för klustret expanderar du posten och dubbelklickar sedan på **(Standardbehållaren).**

    * Om den här posten inte kan utökas använder du **Azure Data Lake Storage** som standardlagring för klustret. Om du vill visa filerna i standardlagringen för klustret dubbelklickar du på posten **(Standardlagringskonto).**

1. Om du vill ladda upp EXE-filerna använder du någon av följande metoder:

    * Om du använder ett **Azure Storage-konto**väljer du ikonen **Ladda upp blob.**

        ![HDInsight-uppladdningsikon för nytt projekt](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-upload-icon.png)

        Välj **Bläddra**under **Filnamn**i dialogrutan **Ladda upp ny fil** . Gå till mappen *bin\debug* för *HiveCSharp-projektet* i dialogrutan **Ladda upp blob** och välj sedan filen *HiveCSharp.exe.* Slutligen väljer du **Öppna** och sedan **OK** för att slutföra överföringen.

    * Om du använder **Azure Data Lake Storage**högerklickar du på ett tomt område i fillistan och väljer sedan Ladda **upp**. Slutligen väljer du filen *HiveCSharp.exe* och väljer **Öppna**.

    När *uppladdningen hiveCSharp.exe* har slutförts upprepar du uppladdningsprocessen för filen *PigUDF.exe.*

## <a name="run-an-apache-hive-query"></a>Köra en Apache Hive-fråga

Nu kan du köra en Hive-fråga som använder ditt Hive UDF-program.

1. I Visual Studio navigerar du till **Visa** > **Server Explorer**.

2. Expandera **Azure** och expandera därefter **HDInsight**.

3. Högerklicka på klustret som du distribuerade *HiveCSharp-programmet* till och välj sedan **Skriv en Hive-fråga**.

4. Använd följande text för Hive-frågan:

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasbs:///HiveCSharp.exe;
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
    > Avkommenta `add file` satsen som matchar den typ av standardlagring som används för klustret.

    Den här frågan `clientid` `devicemake`väljer `devicemodel` , `hivesampletable`och fälten från och skickar sedan fälten till *HiveCSharp.exe-programmet.* Frågan förväntar sig att programmet returnerar tre `clientid` `phoneLabel`fält `phoneHash`som lagras som , och . Frågan förväntar sig också att hitta *HiveCSharp.exe* i roten till standardlagringsbehållaren.

5. Växla standard **interactive** till **batch**och välj sedan **Skicka** för att skicka jobbet till HDInsight-klustret. Fönstret **Sammanfattning av Hive-jobb** öppnas.

6. Välj **Uppdatera om** du vill uppdatera sammanfattningen tills **jobbstatus** ändras till **Slutförd**. Om du vill visa projektutdata väljer du **Jobbutdata**.

## <a name="run-an-apache-pig-job"></a>Kör ett Apache Pig jobb

Du kan också köra ett Pig-jobb som använder ditt Pig UDF-program.

1. Använd SSH för att ansluta till ditt HDInsight-kluster. (Kör till exempel `ssh sshuser@<clustername>-ssh.azurehdinsight.net`kommandot .) Mer information finns i [Använda SSH withHDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Använd följande kommando för att starta kommandoraden Pig:

    ```shell
    pig
    ```

    En `grunt>` uppmaning visas.

3. Ange följande om du vill köra ett Pig-jobb som använder .NET Framework-programmet:

    ```pig
    DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
    LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
    DUMP DETAILS;
    ```

    Satsen `DEFINE` skapar ett `streamer` alias för programmet *PigUDF.exe* och `CACHE` läser in det från standardlagring för klustret. `streamer` Senare används med `STREAM` operatorn för att bearbeta `LOG` de enskilda raderna i och returnera data som en serie kolumner.

    > [!NOTE]
    > Programnamnet som används för direktuppspelning måste \` omges av tecknet (backtick) när det aliaseras `SHIP`och av tecknet ' (single quote) när det används med .

4. När du har angett den sista raden ska jobbet starta. Den returnerar utdata som liknar följande text:

    ```output
    (2019-07-15 16:43:25 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
    (2019-07-15 16:43:25 SampleClass5 [DEBUG] detail for id 1976092771)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1317358561)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1737534798)
    (2019-07-15 16:43:25 SampleClass7 [DEBUG] detail for id 1475865947)
    ```

5. Används `exit` för att avsluta grisen.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du använder ett .NET Framework-program från Hive och Pig på HDInsight. Om du vill lära dig hur du använder Python med Hive och Pig, se [Använda Python med Apache Hive och Apache Pig i HDInsight](python-udf-hdinsight.md).

Andra sätt att använda Hive och lära dig mer om hur du använder MapReduce finns i följande artiklar:

* [Använda Apache Hive med HDInsight](hdinsight-use-hive.md)
* [Använd MapReduce med HDInsight](hdinsight-use-mapreduce.md)
* [Pig Latin Grunderna](https://pig.apache.org/docs/latest/basic.html)