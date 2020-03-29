---
title: Skicka MapReduce-jobb med HDInsight .NET SDK - Azure
description: Lär dig hur du skickar MapReduce-jobb till Azure HDInsight Apache Hadoop med HDInsight .NET SDK.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/15/2020
ms.openlocfilehash: e50510f2420d69be37af584a2648a794e1561ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157061"
---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>Köra MapReduce-jobb med HDInsight .NET SDK

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Lär dig hur du skickar MapReduce-jobb med HDInsight .NET SDK. HDInsight kluster levereras med en burk fil med några MapReduce prover. Jar-filen `/example/jars/hadoop-mapreduce-examples.jar`är .  Ett av exemplen är **wordcount**. Du utvecklar en C#-konsolansökan för att skicka in ett wordcount-jobb.  Jobbet läser `/example/data/gutenberg/davinci.txt` filen och matar ut `/example/data/davinciwordcount`resultaten till .  Om du vill köra programmet igen måste du rensa utdatamappen.

> [!NOTE]  
> Stegen i den här artikeln måste utföras från en Windows-klient. Om du vill ha information om hur du använder en Linux-, OS X- eller Unix-klient för att arbeta med Hive använder du flikväljaren som visas högst upp i artikeln.

## <a name="prerequisites"></a>Krav

* En Apache Hadoop kluster på HDInsight. Se [Skapa Apache Hadoop-kluster med Azure-portalen](../hdinsight-hadoop-create-linux-clusters-portal.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/).

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>Skicka MapReduce-jobb med HDInsight .NET SDK

HDInsight .NET SDK tillhandahåller .NET-klientbibliotek, vilket gör det enklare att arbeta med HDInsight-kluster från .NET.

1. Starta Visual Studio och skapa ett C#-konsolprogram.

1. Navigera till **Tools** > **NuGet Package Manager** > **Package Manager Console** och ange följande kommando:

    ```   
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

1. Kopiera koden nedan till **Program.cs**. Redigera sedan koden genom att `existingClusterName`ange `existingClusterPassword` `defaultStorageAccountName`värden `defaultStorageAccountKey`för: , , , och `defaultStorageContainerName`.

    ```csharp
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading;
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    
    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;
    
            private const string existingClusterName = "<Your HDInsight Cluster Name>";
            private const string existingClusterPassword = "<Cluster User Password>";
            private const string defaultStorageAccountName = "<Default Storage Account Name>"; 
            private const string defaultStorageAccountKey = "<Default Storage Account Key>";
            private const string defaultStorageContainerName = "<Default Blob Container Name>";
    
            private const string existingClusterUsername = "admin";
            private const string existingClusterUri = existingClusterName + ".azurehdinsight.net";
            private const string sourceFile = "/example/data/gutenberg/davinci.txt";
            private const string outputFolder = "/example/data/davinciwordcount";
    
            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");
    
                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = existingClusterUsername, Password = existingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(existingClusterUri, clusterCredentials);
    
                SubmitMRJob();
    
                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }
    
            private static void SubmitMRJob()
            {
                List<string> args = new List<string> { { "/example/data/gutenberg/davinci.txt" }, { "/example/data/davinciwordcount" } };
    
                var paras = new MapReduceJobSubmissionParameters
                {
                    JarFile = @"/example/jars/hadoop-mapreduce-examples.jar",
                    JarClass = "wordcount",
                    Arguments = args
                };
    
                System.Console.WriteLine("Submitting the MR job to the cluster...");
                var jobResponse = _hdiJobManagementClient.JobManagement.SubmitMapReduceJob(paras);
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
                System.Console.WriteLine("Job output is: ");
                var storageAccess = new AzureStorageAccess(defaultStorageAccountName, defaultStorageAccountKey,
                    defaultStorageContainerName);
    
                if (jobDetail.ExitValue == 0)
                {
                    // Create the storage account object
                    CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=" +
                        defaultStorageAccountName +
                        ";AccountKey=" + defaultStorageAccountKey);
    
                    // Create the blob client.
                    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
                    // Retrieve reference to a previously created container.
                    CloudBlobContainer container = blobClient.GetContainerReference(defaultStorageContainerName);
    
                    CloudBlockBlob blockBlob = container.GetBlockBlobReference(outputFolder.Substring(1) + "/part-r-00000");
    
                    using (var stream = blockBlob.OpenRead())
                    {
                        using (StreamReader reader = new StreamReader(stream))
                        {
                            while (!reader.EndOfStream)
                            {
                                System.Console.WriteLine(reader.ReadLine());
                            }
                        }
                    }
                }
                else
                {
                    // fetch stderr output in case of failure
                    var output = _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess);
    
                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
    
                }
            }
        }
    }

    ```

1. Tryck på **F5** för att köra programmet.

Om du vill köra jobbet igen måste du ändra jobbutdatamappens `/example/data/davinciwordcount`namn i exemplet .

När jobbet har slutförts skrivs innehållet i utdatafilen `part-r-00000`ut.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig flera sätt att skapa ett HDInsight-kluster. Mer information finns i följande artiklar:

* För att skicka ett Hive-jobb finns i [Köra Apache Hive-frågor med HDInsight .NET SDK](apache-hadoop-use-hive-dotnet-sdk.md).
* Information om hur du skapar HDInsight-kluster finns i [Skapa Linux-baserade Apache Hadoop-kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* För hantering av HDInsight-kluster finns i [Hantera Apache Hadoop-kluster i HDInsight](../hdinsight-administer-use-portal-linux.md).
* För att lära dig HDInsight .NET SDK finns i [HDInsight .NET SDK-referens](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight).
* Icke-interaktiv autentisera till Azure finns i [Skapa icke-interaktiva autentisering .NET HDInsight-program](../hdinsight-create-non-interactive-authentication-dotnet-applications.md).