---
title: Köra Hive-frågor med HDInsight .NET SDK - Azure | Microsoft Docs
description: Lär dig mer om att skicka Hadoop-jobb på Azure HDInsight Hadoop med HDInsight .NET SDK.
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
ms.assetid: 4e291890-f8b4-426c-b5e8-d4fd512ff042
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: jgao
ms.openlocfilehash: 558ea75a74b89776be32095a7230f9c6e97dcea2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="run-hive-queries-using-hdinsight-net-sdk"></a>Köra Hive-frågor med HDInsight .NET SDK
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Lär dig mer om att skicka Hive-frågor med HDInsight .NET SDK. Du skriva ett C#-program att skicka en Hive-fråga för att lista Hive-tabeller och visa resultatet.

> [!NOTE]
> Stegen i den här artikeln måste utföras från en Windows-klient. Använd flikväljaren visas överst i artikeln för information om hur du använder en Linux-, OS X- eller Unix-klient ska fungera med Hive.

## <a name="prerequisites"></a>Förutsättningar
Innan du börjar den här artikeln, måste du ha följande:

* **Ett Hadoop-kluster i HDInsight**. Se [komma igång med Linux-baserade Hadoop i HDInsight](apache-hadoop-linux-tutorial-get-started.md).

    > [!WARNING]
    > Från och med den 15 September 2017 stöder endast HDInsight .NET SDK returnerar Hive frågeresultat från Azure Storage-konton. Om du använder det här exemplet med ett HDInsight-kluster som använder Azure Data Lake Store som primär lagring kan du hämta sökresultat med .NET SDK.

* **Visual Studio 2013/2015/2017**.

## <a name="run-a-hive-query"></a>Köra en Hive-fråga
HDInsight .NET SDK innehåller klientbibliotek för .NET, vilket gör det lättare att arbeta med HDInsight-kluster från .NET. 

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

Utdata från programmet bör likna följande:

![HDInsight Hadoop Hive jobbutdata](./media/apache-hadoop-use-hive-dotnet-sdk/hdinsight-hadoop-use-hive-net-sdk-output.png)

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig att skapa ett HDInsight-kluster på flera olika sätt. Mer information finns i följande artiklar:

* [Komma igång med Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Skapa Hadoop-kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Hantera Hadoop-kluster i HDInsight med hjälp av Azure portal](../hdinsight-administer-use-management-portal.md)
* [HDInsight .NET SDK-referens](https://msdn.microsoft.com/library/mt271028.aspx)
* [Använda Pig med HDInsight](hdinsight-use-pig.md)
* [Använda Sqoop med HDInsight](apache-hadoop-use-sqoop-mac-linux.md)
* [Skapa .NET HDInsight-program med icke-interaktiv autentisering](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)
 


