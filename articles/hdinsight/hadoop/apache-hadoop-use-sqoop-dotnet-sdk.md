---
title: Kör jobb för Sqoop med hjälp av .NET- och HDInsight - Azure | Microsoft Docs
description: Lär dig hur du använder HDInsight .NET SDK för att köra Sqoop importera och exportera mellan ett Hadoop-kluster och en Azure SQL database.
keywords: sqoop jobb
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
ms.assetid: 87bacd13-7775-4b71-91da-161cb6224a96
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jgao
ms.openlocfilehash: 818e4aca63249c7a1543abe146e0691e993e9e80
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34200307"
---
# <a name="run-sqoop-jobs-by-using-net-sdk-for-hadoop-in-hdinsight"></a>Kör jobb för Sqoop med hjälp av .NET SDK för Hadoop i HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Lär dig hur du använder Azure HDInsight .NET SDK för att köra Sqoop jobb i HDInsight för att importera och exportera mellan ett HDInsight-kluster och en Azure SQL database eller SQL Server-databas.

> [!NOTE]
> Men du kan använda procedurerna i den här artikeln med antingen en Windows- eller Linux-baserade HDInsight-kluster, fungerar de endast från en Windows-klient. Använd flikväljaren överst i den här artikeln om du vill välja andra metoder.
> 

## <a name="prerequisites"></a>Förutsättningar
Innan du påbörjar den här självstudien måste du ha följande objekt:

* Ett Hadoop-kluster i HDInsight. Mer information finns i [skapa ett kluster och en SQL-databas](hdinsight-use-sqoop.md#create-cluster-and-sql-database).

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>Använda Sqoop på HDInsight-kluster med .NET SDK
HDInsight .NET SDK innehåller klientbibliotek för .NET, så att det är lättare att arbeta med HDInsight-kluster från .NET. I det här avsnittet skapar du en C#-konsolprogram för att exportera hivesampletable till Azure SQL Database-tabellen som du skapade tidigare i den här kursen.

## <a name="submit-a-sqoop-job"></a>Skicka ett Sqoop-jobb

1. Skapa ett C#-konsolprogram i Visual Studio.

2. Importera paketet genom att köra följande kommando i NuGet från Visual Studio Package Manager-konsolen:
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job

3. Använd följande kod i filen Program.cs:
   
        using System.Collections.Generic;
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
   
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
   
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
   
                    SubmitSqoopJob();
   
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
   
                private static void SubmitSqoopJob()
                {
                    var sqlDatabaseServerName = "<SQLDatabaseServerName>";
                    var sqlDatabaseLogin = "<SQLDatabaseLogin>";
                    var sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
                    var sqlDatabaseDatabaseName = "<DatabaseName>";
   
                    var tableName = "<TableName>";
                    var exportDir = "/tutorials/usesqoop/data";
   
                    // Connection string for using Azure SQL Database.
                    // Comment if using SQL Server
                    var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
                    // Connection string for using SQL Server.
                    // Uncomment if using SQL Server
                    //var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
   
                    var parameters = new SqoopJobSubmissionParameters
                    {
                        Files = new List<string> { "/user/oozie/share/lib/sqoop/sqljdbc41.jar" }, // This line is required for Linux-based cluster.
                        Command = "export --connect " + connectionString + " --table " + tableName + "_mobile --export-dir " + exportDir + "_mobile --fields-terminated-by \\t -m 1"
                    };
   
                    System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }
            }
        }

4. Om du vill köra programmet på **F5** nyckel. 

## <a name="limitations"></a>Begränsningar
Linux-baserat HDInsight innehåller följande begränsningar:

* Massredigera export: det Sqoop koppling som används för att exportera data till Microsoft SQL Server eller Azure SQL Database stöder för närvarande inte bulkinfogningar.

* Batchbearbetning: med den `-batch` växla när den utför infogningar, Sqoop utför flera infogningar i stället för batchbearbetning insert-åtgärder.

## <a name="next-steps"></a>Nästa steg
Nu har du lärt dig hur du använder Sqoop. Du kan läsa mer här:

* [Använda Oozie med HDInsight](../hdinsight-use-oozie.md): Använd Sqoop åtgärd i ett arbetsflöde för Oozie.
* [Analysera svarta fördröjning data med HDInsight](../hdinsight-analyze-flight-delay-data.md): använda Hive att analysera svarta fördröjning data och sedan använda Sqoop för att exportera data till en Azure SQL database.
* [Överföra data till HDInsight](../hdinsight-upload-data.md): hitta andra metoder för att ladda upp data till HDInsight eller Azure Blob storage.

