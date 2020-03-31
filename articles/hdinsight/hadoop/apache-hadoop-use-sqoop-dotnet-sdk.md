---
title: Kör Apache Sqoop-jobb med hjälp av .NET och HDInsight - Azure
description: Lär dig hur du använder HDInsight .NET SDK för att köra Apache Sqoop-import och export mellan ett Apache Hadoop-kluster och en Azure SQL-databas.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/14/2020
ms.openlocfilehash: f0f767273a40bc91b1d49477c896b0b157623106
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157074"
---
# <a name="run-apache-sqoop-jobs-by-using-net-sdk-for-apache-hadoop-in-hdinsight"></a>Kör Apache Sqoop-jobb med hjälp av .NET SDK för Apache Hadoop i HDInsight

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Lär dig hur du använder Azure HDInsight .NET SDK för att köra Apache Sqoop-jobb i HDInsight för att importera och exportera mellan ett HDInsight-kluster och en Azure SQL Database- eller SQL Server-databas.

## <a name="prerequisites"></a>Krav

* Slutförande av [Inrätta testmiljö](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) från [Använd Apache Sqoop med Hadoop i HDInsight](./hdinsight-use-sqoop.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/).

* Förtrogenhet med Sqoop. Mer information finns i [Användarhandboken för Sqoop](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>Använd Sqoop på HDInsight-kluster med .NET SDK

HDInsight .NET SDK tillhandahåller .NET-klientbibliotek, så att det blir enklare att arbeta med HDInsight-kluster från .NET. I det här avsnittet skapar du ett `hivesampletable` C#-konsolprogram för att exportera tabellen till Azure SQL Database som du skapade från förutsättningarna.

## <a name="set-up"></a>Konfigurera

1. Starta Visual Studio och skapa ett C#-konsolprogram.

1. Navigera till **Tools** > **NuGet Package Manager** > **Package Manager Console** och kör följande kommando:

    ```
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

## <a name="sqoop-export"></a>Sqoop export

Från Hive till SQL Server.  I det här exemplet `hivesampletable` exporteras `mobiledata` data från tabellen Hive till tabellen i SQL Database.

1. Använd följande kod i Program.cs-filen. Redigera koden för att `ExistingClusterName`ange `ExistingClusterPassword`värden för och .

    ```csharp
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    
    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;
    
            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterPassword = "<Cluster User Password>";
            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
            private const string ExistingClusterUsername = "admin";
    
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
                var sqlDatabaseServerName = ExistingClusterName + "dbserver";
                var sqlDatabaseLogin = "sqluser";
                var sqlDatabaseLoginPassword = ExistingClusterPassword;
                var sqlDatabaseDatabaseName = ExistingClusterName + "db";
    
                // Connection string for using Azure SQL Database; Comment if using SQL Server
                var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                // Connection string for using SQL Server; Uncomment if using SQL Server
                // var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                //sqoop start
                var tableName = "mobiledata";
    
                var parameters = new SqoopJobSubmissionParameters
                {
                     Command = "export --connect " + connectionString + " --table " + tableName + " --hcatalog-table hivesampletable"
                };
                //sqoop end
    
                System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                System.Console.WriteLine("Validating that the response is as expected...");
                System.Console.WriteLine("Response status code is " + response.StatusCode);
                System.Console.WriteLine("Validating the response object...");
                System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
            }
        }
    }
    ```

1. Om du vill köra **F5** programmet väljer du F5-tangenten.

## <a name="sqoop-import"></a>Sqoop import

Från SQL Server till Azure Storage. Det här exemplet är beroende av att ovanstående export har utförts.  I det här `mobiledata` exemplet importeras data `wasb:///tutorials/usesqoop/importeddata` från tabellen i SQL Database till katalogen i klustrets standardlagringskonto.

1. Ersätt koden ovan `//sqoop start //sqoop end` i blocket med följande kod:

    ```csharp
    var tableName = "mobiledata";
    var exportDir = "/tutorials/usesqoop/importeddata";
    
    var parameters = new SqoopJobSubmissionParameters
    {
        Command = "import --connect " + connectionString + " --table " + tableName + " --target-dir " +  exportDir + " --fields-terminated-by \\t --lines-terminated-by \\n -m 1"
    };
    ```

1. Om du vill köra **F5** programmet väljer du F5-tangenten.

## <a name="limitations"></a>Begränsningar

Linux-baserade HDInsight presenterar följande begränsningar:

* Massexport: Sqoop-anslutningen som används för att exportera data till Microsoft SQL Server eller Azure SQL Database stöder för närvarande inte massinfogningar.

* Batchbearbetning: Genom `-batch` att använda växeln utför Sqoop flera skär i stället för batchning av skäråtgärderna.

## <a name="next-steps"></a>Nästa steg

Nu har du lärt dig att använda Sqoop. Du kan läsa mer här:

* [Använd Apache Oozie med HDInsight:](../hdinsight-use-oozie-linux-mac.md)Använd Sqoop-åtgärd i ett Oozie-arbetsflöde.
* [Ladda upp data till HDInsight](../hdinsight-upload-data.md): Hitta andra metoder för att överföra data till HDInsight- eller Azure Blob-lagring.
