---
title: Kör Apache Hive-frågor med hjälp av HDInsight .NET SDK - Azure
description: Lär dig mer om att skicka Apache Hadoop-jobb till Azure HDInsight Apache Hadoop med HDInsight .NET SDK.
ms.reviewer: jasonh
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 790cc11e3d4cf1d0260b1099efd82bdddec85785
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634574"
---
# <a name="run-apache-hive-queries-using-hdinsight-net-sdk"></a>Kör Apache Hive-frågor med hjälp av HDInsight .NET SDK
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Lär dig hur du skickar in Apache Hive-frågor med hjälp av HDInsight .NET SDK. Du skriva ett C#-program att skicka en Hive-fråga för att lista Hive-tabeller och visa resultatet.

> [!NOTE]
> Stegen i den här artikeln måste utföras från en Windows-klient. Använd flikväljaren visas överst i artikeln för information om hur du använder en Linux-, OS X- eller Unix-klient för att arbeta med Hive.

## <a name="prerequisites"></a>Förutsättningar
Innan du påbörjar den här artikeln måste du ha följande objekt:

* **Ett Apache Hadoop-kluster i HDInsight**. Se [komma igång med Linux-baserat Hadoop i HDInsight](apache-hadoop-linux-tutorial-get-started.md).

    > [!WARNING]
    > HDInsight .NET SDK stöder endast returnerar Hive-frågeresultat från Azure Storage-konton från och med den 15 September 2017. Om du använder det här exemplet med ett HDInsight-kluster som använder Azure Data Lake Store som primär lagring, kan du inte kan hämta sökresultat med hjälp av .NET SDK.

* **Visual Studio 2013/2015/2017**.

## <a name="run-a-hive-query"></a>Kör en Hive-fråga
HDInsight .NET SDK tillhandahåller klientbibliotek för .NET, vilket gör det enklare att arbeta med HDInsight-kluster från .NET. 

**Skicka jobb**

1. Skapa ett C#-konsolprogram i Visual Studio.
2. Kör följande kommando från Nuget Package Manager-konsolen:
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job
3. Använd följande kod:

    ```csharp
        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;
   
        namespace SubmitHDInsightJobDotNet
        {
            class Program
            {
                private static HDInsightJobManagementClient _hdiJobManagementClient;
   
                private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
                private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                private const string ExistingClusterUsername = "<Cluster Username>";
                private const string ExistingClusterPassword = "<Cluster User Password>";
                
                // Only Azure Storage accounts are supported by the SDK
                private const string DefaultStorageAccountName = "<Default Storage Account Name>";
                private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
                private const string DefaultStorageContainerName = "<Default Blob Container Name>";
   
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
   
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
   
                    SubmitHiveJob();
   
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
   
                private static void SubmitHiveJob()
                {
                    Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "tez" }, { "hive.exec.reducers.max", "1" } };
                    List<string> args = new List<string> { { "argA" }, { "argB" } };
                    var parameters = new HiveJobSubmissionParameters
                    {
                        Query = "SHOW TABLES",
                        Defines = defines,
                        Arguments = args
                    };
   
                    System.Console.WriteLine("Submitting the Hive job to the cluster...");
                    var jobResponse = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
                    var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                    System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                    System.Console.WriteLine("JobId is " + jobId);
   
                    System.Console.WriteLine("Waiting for the job completion ...");
   
                    // Wait for job completion
                    var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    while (!jobDetail.Status.JobComplete)
                    {
                        Thread.Sleep(1000);
                        jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    }
   
                    // Get job output
                    var storageAccess = new AzureStorageAccess(DefaultStorageAccountName, DefaultStorageAccountKey,
                        DefaultStorageContainerName);
                    var output = (jobDetail.ExitValue == 0)
                        ? _hdiJobManagementClient.JobManagement.GetJobOutput(jobId, storageAccess) // fetch stdout output in case of success
                        : _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); // fetch stderr output in case of failure
   
                    System.Console.WriteLine("Job output is: ");
   
                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
                }
            }
        }
    ```
4. Tryck på **F5** för att köra programmet.

Utdata från programmet bör se ut:

![HDInsight Hadoop Hive jobbutdata](./media/apache-hadoop-use-hive-dotnet-sdk/hdinsight-hadoop-use-hive-net-sdk-output.png)

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig att skapa ett HDInsight-kluster på flera olika sätt. Mer information finns i följande artiklar:

* [Kom igång med Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Skapa Hadoop-kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Hantera Hadoop-kluster i HDInsight med hjälp av Azure-portalen](../hdinsight-administer-use-management-portal.md)
* [HDInsight .NET SDK-referens](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [Använda Pig med HDInsight](hdinsight-use-pig.md)
* [Använda Sqoop med HDInsight](apache-hadoop-use-sqoop-mac-linux.md)
* [Skapa .NET HDInsight-program med icke-interaktiv autentisering](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)
 


