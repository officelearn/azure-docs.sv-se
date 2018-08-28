---
title: Kör Apache Pig-jobb med .NET SDK för Hadoop - Azure HDInsight
description: Lär dig hur du använder .NET SDK för Hadoop för att skicka Pig-jobb till Hadoop på HDInsight.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: jasonh
ms.openlocfilehash: 06d2633556e149f48c9750b5df0408b601bf3da3
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43046768"
---
# <a name="run-pig-jobs-using-the-net-sdk-for-hadoop-in-hdinsight"></a>Köra Pig-jobb med .NET SDK för Hadoop i HDInsight

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Lär dig hur du använder .NET SDK för Hadoop för att skicka Apache Pig-jobb till Hadoop på Azure HDInsight.

HDInsight .NET SDK tillhandahåller .NET-klientbibliotek som gör det enklare att arbeta med HDInsight-kluster från .NET. Pig kan du skapa MapReduce åtgärder genom att modellera en serie Datatransformationer. I detta dokument kan du lära dig hur du använder ett grundläggande C#-program för att skicka ett Pig-jobb i ett HDInsight-kluster.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att slutföra stegen i den här artikeln.

* Ett Azure HDInsight (Hadoop på HDInsight)-kluster (antingen Windows eller Linux-baserade).

  > [!IMPORTANT]
  > Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Visual Studio 2012, 2013, 2015 eller 2017.

## <a name="create-the-application"></a>Skapa programmet

HDInsight .NET SDK tillhandahåller klientbibliotek för .NET, vilket gör det enklare att arbeta med HDInsight-kluster från .NET.

1. Från den **filen** menyn i Visual Studio väljer **New** och välj sedan **projekt**.

2. Ange eller Välj följande värden för det nya projektet:

   | Egenskap  | Värde |
   | ------ | ------ |
   | Kategori | Mallar/Visual C#/Windows |
   | Mall | Konsolprogram |
   | Namn | SubmitPigJob |

3. Klicka på **OK** för att skapa projektet.

4. Från den **verktyg** menyn och välj **Library Package Manager** eller **NuGet-Pakethanteraren**, och välj sedan **Pakethanterarkonsolen**.

5. Om du vill installera .NET SDK-paket, använder du följande kommando:

        Install-Package Microsoft.Azure.Management.HDInsight.Job

6. Från Solution Explorer dubbelklickar du på **Program.cs** att öppna den. Ersätt den befintliga koden med följande.

    ```csharp
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;

    namespace SubmitPigJob
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;

            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
            private const string ExistingClusterUsername = "<Cluster Username>";
            private const string ExistingClusterPassword = "<Cluster User Password>";

            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");

                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

                SubmitPigJob();

                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }

            private static void SubmitPigJob()
            {
                var parameters = new PigJobSubmissionParameters
                {
                    Query = @"LOGS = LOAD '/example/data/sample.log';
                                LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
                                FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
                                GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
                                FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
                                RESULT = order FREQUENCIES by COUNT desc;
                                DUMP RESULT;"
                };

                System.Console.WriteLine("Submitting the Pig job to the cluster...");
                var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
                System.Console.WriteLine("Validating that the response is as expected...");
                System.Console.WriteLine("Response status code is " + response.StatusCode);
                System.Console.WriteLine("Validating the response object...");
                System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
            }
        }
    }
    ```

7. Du kan börja programmet **F5**.

8. Om du vill avsluta programmet, trycker du på **RETUR**.

## <a name="next-steps"></a>Nästa steg

Information om Pig i HDInsight finns i [använda Pig med Hadoop på HDInsight](hdinsight-use-pig.md).

Mer information om hur du använder Hadoop på HDInsight finns i följande dokument:

* [Använda Hive med Hadoop i HDInsight](hdinsight-use-hive.md)
* [Använda MapReduce med Hadoop i HDInsight](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/
