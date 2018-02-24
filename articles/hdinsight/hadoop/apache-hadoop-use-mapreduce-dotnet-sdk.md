---
title: Skicka MapReduce-jobb med HDInsight .NET SDK - Azure | Microsoft Docs
description: "Lär dig mer om att skicka MapReduce-jobb på Azure HDInsight Hadoop med HDInsight .NET SDK."
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: c85e44b0-85fd-4185-ad1c-c34a9fe5ef44
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: jgao
ms.openlocfilehash: e967c6df89722492554998dc21e09388b7b7cdf2
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2018
---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>Kör jobb för MapReduce med HDInsight .NET SDK
[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Lär dig mer om att skicka MapReduce-jobb med HDInsight .NET SDK. HDInsight kluster medföljer jar-filen med vissa MapReduce-exempel. Jar-filen är */example/jars/hadoop-mapreduce-examples.jar*.  Ett exempel är *wordcount*. Du kan utveckla en C#-konsolapp att skicka ett wordcount-jobb.  Jobbet läser den */example/data/gutenberg/davinci.txt* filen och skickar resultatet till */example/data/davinciwordcount*.  Om du vill köra programmet måste du rensa den utgående mappen.

> [!NOTE]
> Stegen i den här artikeln måste utföras från en Windows-klient. Använd flikväljaren visas överst i artikeln för information om hur du använder en Linux-, OS X- eller Unix-klient ska fungera med Hive.
> 
> 

## <a name="prerequisites"></a>Förutsättningar
Innan du börjar den här artikeln, måste du ha följande:

* **Ett Hadoop-kluster i HDInsight**. Se [komma igång med Linux-baserade Hadoop i HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* **Visual Studio 2013/2015/2017**.

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>Skicka MapReduce-jobb med HDInsight .NET SDK
HDInsight .NET SDK innehåller klientbibliotek för .NET, vilket gör det lättare att arbeta med HDInsight-kluster från .NET. 

**Skicka jobb**

1. Skapa ett C#-konsolprogram i Visual Studio.
2. Kör följande kommando från NuGet Package Manager-konsolen:

    ```   
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```
3. Använd följande kod:

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
            private const string existingClusterUri = existingClusterName + ".azurehdinsight.net";
            private const string existingClusterUsername = "<Cluster Username>";
            private const string existingClusterPassword = "<Cluster User Password>";

            private const string defaultStorageAccountName = "<Default Storage Account Name>"; //<StorageAccountName>.blob.core.windows.net
            private const string defaultStorageAccountKey = "<Default Storage Account Key>";
            private const string defaultStorageContainerName = "<Default Blob Container Name>";

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

4. Tryck på **F5** för att köra programmet.

Du kan köra jobbet igen måste du ändra mappnamnet utdata för jobbet i det här exemplet är ”/ davinciwordcount-exempel/data”.

När jobbet har slutförts, skrivs innehållet i filen ”del-r-00000” i programmet.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig att skapa ett HDInsight-kluster på flera olika sätt. Mer information finns i följande artiklar:

* För att skicka ett Hive-jobb finns [köra Hive-frågor med HDInsight .NET SDK](apache-hadoop-use-hive-dotnet-sdk.md).
* För att skapa HDInsight-kluster, se [skapa Linux-baserade Hadoop-kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* För att hantera HDInsight-kluster, se [hantera Hadoop-kluster i HDInsight](../hdinsight-administer-use-portal-linux.md).
* För HDInsight .NET SDK, se [HDInsight .NET SDK referens](https://msdn.microsoft.com/library/mt271028.aspx).
* För icke-interaktiv autentisera till Azure, se [skapa icke-interaktiv autentisering .NET HDInsight-program](../hdinsight-create-non-interactive-authentication-dotnet-applications.md).

