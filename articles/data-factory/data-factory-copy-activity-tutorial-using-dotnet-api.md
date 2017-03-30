---
title: "Självstudiekurs: Skapa en pipeline med en kopieringsaktivitet med hjälp av .NET-API:et | Microsoft Docs"
description: "I den här självstudiekursen ska du skapa en Azure Data Factory-pipeline med en kopieringsaktivitet med hjälp av .NET-API:et."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 58fc4007-b46d-4c8e-a279-cb9e479b3e2b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/17/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 4b29fd1c188c76a7c65c4dcff02dc9efdf3ebaee
ms.openlocfilehash: 733c151012e3d896f720fbc64120432aca594bda
ms.lasthandoff: 02/03/2017


---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-net-api"></a>Självstudiekurs: Skapa en pipeline med en kopieringsaktivitet med hjälp av .NET-API:et
> [!div class="op_single_selector"]
> * [Översikt och förutsättningar](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Guiden Kopiera](data-factory-copy-data-wizard-tutorial.md)
> * [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-mall](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET-API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

Den här självstudiekursen visar hur du skapar och övervakar en Azure-datafabrik med hjälp av .NET-API:et. Pipelinen i datafabriken använder en kopieringsaktivitet för att kopiera data från Azure Blob Storage till Azure SQL Database.

Kopieringsaktiviteten utför dataflyttningen i Azure Data Factory. Aktiviteten drivs av en globalt tillgänglig tjänst som kan kopiera data mellan olika datalager på ett säkert, tillförlitligt och skalbart sätt. Se artikeln [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) för information om kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln beskriver inte hela .NET-API:et för Data Factory. Mer information om Data Factory .NET SDK finns i [referensen för .NET-API:et för Data Factory](https://msdn.microsoft.com/library/mt415893.aspx).
> 
> Datapipelinen i den här självstudien kopierar data från ett källdatalager till ett måldatalager. Det transformerar inte indata för att generera utdata. Om du vill se en självstudie som visar hur du omvandlar data med Azure Data Factory går du till [Tutorial: Build a pipeline to transform data using Hadoop cluster](data-factory-build-your-first-pipeline.md) (Självstudie: Bygg en pipeline för att omvandla data med Hadoop-kluster).


## <a name="prerequisites"></a>Krav
* Gå igenom [Översikt och förutsättningar](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för att få en översikt av självstudierna och slutför de **nödvändiga** stegen.
* Visual Studio 2012, 2013 eller 2015
* Ladda ned och installera [Azure .NET SDK](http://azure.microsoft.com/downloads/)
* Azure PowerShell. Följ instruktionerna i artikeln [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azureps-cmdlets-docs) för att installera Azure PowerShell på datorn. Du kan använda Azure PowerShell för att skapa ett Azure Active Directory-program.

### <a name="create-an-application-in-azure-active-directory"></a>Skapa ett program i Azure Active Directory
Skapa ett Azure Active Directory-program, skapa ett tjänstobjektnamn för programmet och tilldela det rollen som **Data Factory-deltagare**.

1. Starta **PowerShell**.
2. Kör följande kommando och ange användarnamnet och lösenordet som du använder för att logga in på Azure-portalen.

    ```PowerShell
    Login-AzureRmAccount
    ```
3. Kör följande kommando för att visa alla prenumerationer för det här kontot.

    ```PowerShell
    Get-AzureRmSubscription
    ```
4. Kör följande kommando för att välja den prenumeration som du vill arbeta med. Ersätt **&lt;NameOfAzureSubscription**&gt; med namnet på din Azure-prenumeration.

    ```PowerShell
    Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
    ```

   > [!IMPORTANT]
   > Anteckna **ID** och **TenantId** i kommandots utdata.

5. Skapa en Azure-resursgrupp med namnet **ADFTutorialResourceGroup** genom att köra följande kommando i PowerShell.

    ```PowerShell
    New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```

    Om resursgruppen redan finns anger du om du vill uppdatera den (Y) eller lämna den som den är (N).

    Om du använder en annan resursgrupp måste du använda namnet på din resursgrupp i stället för ADFTutorialResourceGroup i den här självstudiekursen.
6. Skapa ett Azure Active Directory-program.

    ```PowerShell
    $azureAdApplication = New-AzureRmADApplication -DisplayName "ADFCopyTutotiralApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfcopytutorialapp.org/example" -Password "Pass@word1"
    ```

    Om följande fel returneras anger du en annan URL och kör kommandot igen.
    
    ```PowerShell
    Another object with the same value for property identifierUris already exists.
    ```
7. Skapa AD-tjänstobjektet.

    ```PowerShell
    New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    ```
8. Lägg till tjänstobjektet till rollen som **Data Factory-deltagare**.

    ```PowerShell
    New-AzureRmRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    ```
9. Hämta program-ID:t.

    ```PowerShell
    $azureAdApplication    
    ```
    Anteckna program-ID:t (**applicationID** från utdata).

Du bör nu ha tillgång till följande fyra värden efter de här stegen:

* Klient-ID:t
* Prenumerations-ID:t
* Program-ID:t
* Lösenord (anges i det första kommandot)

## <a name="walkthrough"></a>Genomgång
1. Skapa ett C# .NET-konsolprogram med hjälp av Visual Studio 2012/2013/2015.
   1. Starta **Visual Studio** 2012/2013/2015.
   2. Klicka på **Arkiv**, peka på **Nytt** och klicka på **Projekt**.
   3. Expandera **Mallar** och välj **Visual C#**. I den här genomgången ska du använda C#, men du kan använda valfritt .NET-språk.
   4. Välj **Konsolprogram** i listan över projekttyper till höger.
   5. Ange **DataFactoryAPITestApp** som namn.
   6. Välj **C:\ADFGetStarted** som plats.
   7. Klicka på **OK** för att skapa projektet.
2. Klicka på **Verktyg**, peka på **Nuget Package Manager** och klicka på **Package Manager Console**.
3. I **Package Manager Console** gör du följande steg:
   1. Kör följande kommando för att installera Data Factory-paketet: `Install-Package Microsoft.Azure.Management.DataFactories`
   2. Kör följande kommando för att installera Azure Active Directory-paketet (du använder Active Directory-API i koden): `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213`
4. Lägg till följande **appSetttings**-avsnitt i filen **App.config**. De här inställningarna används av helper-metoden: **GetAuthorizationHeader**.

    Ersätt värdena för **&lt;Program-ID&gt;**, **&lt;Lösenord&gt;**, **&lt;Prenumerations-ID&gt;** och **&lt;Klient-ID&gt;** med dina egna värden.

    ```xml
    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <appSettings>
            <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
            <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
            <add key="WindowsManagementUri" value="https://management.core.windows.net/" />

            <add key="ApplicationId" value="your application ID" />
            <add key="Password" value="Password you used while creating the AAD application" />
            <add key="SubscriptionId" value= "Subscription ID" />
            <add key="ActiveDirectoryTenantId" value="Tenant ID" />
        </appSettings>
    </configuration>
    ```

5. Lägg till följande **genom att använda** instruktioner till källfilen (Program.cs) i projektet.

    ```csharp
    using System.Configuration;
    using System.Collections.ObjectModel;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure;
    using Microsoft.Azure.Management.DataFactories;
    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Common.Models;

    using Microsoft.IdentityModel.Clients.ActiveDirectory;

    ```

6. Lägg till följande kod som skapar en instans av **DataPipelineManagementClient**-klassen till **Main**-metoden. Du kan använda det här objektet för att skapa en datafabrik, en länkad tjänst, in- och utdatauppsättningar och en pipeline. Du kan också använda det här objektet om du vill övervaka sektorer för en datauppsättning vid körningstillfället.

    ```csharp
    // create data factory management client
    string resourceGroupName = "ADFTutorialResourceGroup";
    string dataFactoryName = "APITutorialFactory";

    TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader().Result);

    Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);
    ```

   > [!IMPORTANT]
   > Ersätt värdet för **resourceGroupName** med namnet på Azure-resursgruppen.
   >
   > Uppdatera namnet på datafabriken (**dataFactoryName**) så att det är unikt. Namnet på datafabriken måste vara unikt globalt. Se artikeln [Data Factory – namnregler](data-factory-naming-rules.md) för namnregler för Data Factory-artefakter.

7. Lägg till följande kod som skapar en **datafabrik** till **Main**-metoden.

    ```csharp
    // create a data factory
    Console.WriteLine("Creating a data factory");
    client.DataFactories.CreateOrUpdate(resourceGroupName,
        new DataFactoryCreateOrUpdateParameters()
        {
            DataFactory = new DataFactory()
            {
                Name = dataFactoryName,
                Location = "westus",
                Properties = new DataFactoryProperties()
            }
        }
    );
    ```

8. Lägg till följande kod som skapar en **länkad Azure Storage-tjänst** till **Main**-metoden.

   > [!IMPORTANT]
   > Ersätt **storageaccountname** och **accountkey** med namnet och nyckeln för ditt Azure Storage-konto.

    ```csharp
    // create a linked service for input data store: Azure Storage
    Console.WriteLine("Creating Azure Storage linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "AzureStorageLinkedService",
                Properties = new LinkedServiceProperties
                (
                    new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accountkey>")
                )
            }
        }
    );
    ```

9. Lägg till följande kod som skapar en **länkad Azure SQL-tjänst** till **Main**-metoden.

   > [!IMPORTANT]
   > Ersätt **servername**, **databasename**, **username** och **password** med namnen för Azure SQL-servern, databasen, användaren och lösenordet.

    ```csharp
    // create a linked service for output data store: Azure SQL Database
    Console.WriteLine("Creating Azure SQL Database linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "AzureSqlLinkedService",
                Properties = new LinkedServiceProperties
                (
                    new AzureSqlDatabaseLinkedService("Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30")
                )
            }
        }
    );
    ```

10. Lägg till följande kod som skapar **in- och utdatauppsättningar** till **Main**-metoden.

    ```csharp
    // create input and output datasets
    Console.WriteLine("Creating input and output datasets");
    string Dataset_Source = "DatasetBlobSource";
    string Dataset_Destination = "DatasetAzureSqlDestination";

    Console.WriteLine("Creating input dataset of type: Azure Blob");
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,

    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Source,
            Properties = new DatasetProperties()
            {
                Structure = new List<DataElement>()
                {
                    new DataElement() { Name = "FirstName", Type = "String" },
                    new DataElement() { Name = "LastName", Type = "String" }
                },
                LinkedServiceName = "AzureStorageLinkedService",
                TypeProperties = new AzureBlobDataset()
                {
                    FolderPath = "adftutorial/",
                    FileName = "emp.txt"
                },
                External = true,
                Availability = new Availability()
                {
                    Frequency = SchedulePeriod.Hour,
                    Interval = 1,
                },

                Policy = new Policy()
                {
                    Validation = new ValidationPolicy()
                    {
                        MinimumRows = 1
                    }
                }
            }
        }
    });

    Console.WriteLine("Creating output dataset of type: Azure SQL");
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new DatasetCreateOrUpdateParameters()
        {
            Dataset = new Dataset()
            {
                Name = Dataset_Destination,
                Properties = new DatasetProperties()
                {
                    Structure = new List<DataElement>()
                    {
                        new DataElement() { Name = "FirstName", Type = "String" },
                        new DataElement() { Name = "LastName", Type = "String" }
                    },
                    LinkedServiceName = "AzureSqlLinkedService",
                    TypeProperties = new AzureSqlTableDataset()
                    {
                        TableName = "emp"
                    },
                    Availability = new Availability()
                    {
                        Frequency = SchedulePeriod.Hour,
                        Interval = 1,
                    },
                }
            }
        });
    ```

11. Lägg till följande kod som **skapar och aktiverar en pipeline** till **Main**-metoden. Den här pipelinen har en **CopyActivity** som använder **BlobSource** som källa och **BlobSink** som mottagare.

    ```csharp
    // create a pipeline
    Console.WriteLine("Creating a pipeline");
    DateTime PipelineActivePeriodStartTime = new DateTime(2016, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
    DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
    string PipelineName = "ADFTutorialPipeline";

    client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new PipelineCreateOrUpdateParameters()
        {
            Pipeline = new Pipeline()
            {
                Name = PipelineName,
                Properties = new PipelineProperties()
                {
                    Description = "Demo Pipeline for data transfer between blobs",

                    // Initial value for pipeline's active period. With this, you won't need to set slice status
                    Start = PipelineActivePeriodStartTime,
                    End = PipelineActivePeriodEndTime,

                    Activities = new List<Activity>()
                    {
                        new Activity()
                        {
                            Name = "BlobToAzureSql",
                            Inputs = new List<ActivityInput>()
                            {
                                new ActivityInput() {
                                    Name = Dataset_Source
                                }
                            },
                            Outputs = new List<ActivityOutput>()
                            {
                                new ActivityOutput()
                                {
                                    Name = Dataset_Destination
                                }
                            },
                            TypeProperties = new CopyActivity()
                            {
                                Source = new BlobSource(),
                                Sink = new BlobSink()
                                {
                                    WriteBatchSize = 10000,
                                    WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                }
                            }
                        }
                    }
                }
            }
        });
    ```

12. Lägg till följande kod till **Main**-metoden för att hämta statusen för en datasektor i utdatauppsättningen. Endast en sektor förväntas i det här exemplet.

    ```csharp
    // Pulling status within a timeout threshold
    DateTime start = DateTime.Now;
    bool done = false;

    while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
    {
        Console.WriteLine("Pulling the slice status");        
        // wait before the next status check
        Thread.Sleep(1000 * 12);

        var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
            new DataSliceListParameters()
            {
                DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
            });

        foreach (DataSlice slice in datalistResponse.DataSlices)
        {
            if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
            {
                Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                done = true;
                break;
            }
            else
            {
                Console.WriteLine("Slice status is: {0}", slice.State);
            }
        }
    }
    ```

13. Lägg till följande kod för att hämta körningsinformation för en datasektor till **Main**-metoden.

    ```csharp
    Console.WriteLine("Getting run details of a data slice");

    // give it a few minutes for the output slice to be ready
    Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
    Console.ReadKey();

    var datasliceRunListResponse = client.DataSliceRuns.List(
            resourceGroupName,
            dataFactoryName,
            Dataset_Destination,
            new DataSliceRunListParameters()
            {
                DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
            }
        );

    foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
    {
        Console.WriteLine("Status: \t\t{0}", run.Status);
        Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
        Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
        Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
        Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
        Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
        Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
    }

    Console.WriteLine("\nPress any key to exit.");
    Console.ReadKey();
    ```

14. Lägg till följande helper-metod som används av **Main**-metoden i **Program**-klassen.

    ```csharp
    public static async Task<string> GetAuthorizationHeader()
    {
        AuthenticationContext context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
        ClientCredential credential = new ClientCredential(
            ConfigurationManager.AppSettings["ApplicationId"],
            ConfigurationManager.AppSettings["Password"]);
        AuthenticationResult result = await context.AcquireTokenAsync(
            resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
            clientCredential: credential);

        if (result != null)
            return result.AccessToken;

        throw new InvalidOperationException("Failed to acquire token");
    }
    ```

15. Expandera projektet i Solution Explorer (**DataFactoryAPITestApp**), högerklicka på **Referenser** och klicka på **Lägg till referens**. Markera kryssrutan för ”**System.Configuration**”-sammansättningen och klicka på **OK**.
16. Skapa konsolprogrammet. Klicka på **Skapa** på menyn och klicka sedan på **Build Solution** (Skapa lösning).
17. Kontrollera att det finns minst en fil i **adftutorial**-behållaren i Azure-blobblagringen. Om inte skapar du **Emp.txt**-filen i Anteckningar med följande innehåll och laddar upp den till adftutorial-behållaren.

    ```
    John, Doe
    Jane, Doe
    ```
18. Kör exemplet genom att klicka på **Felsök** -> **Börja felsöka** på menyn. När du ser **Getting run details of a data slice** (Hämta körningsdata för en datorsektor) väntar du några minuter och trycker sedan på **Retur**.
19. Använd Azure-portalen och kontrollera att datafabriken **APITutorialFactory** har skapats med följande artefakter:
   * Länkad tjänst: **LinkedService_AzureStorage**
   * Datauppsättning: **DatasetBlobSource** och **DatasetBlobDestination**.
   * Pipeline: **PipelineBlobSample**
20. Kontrollera att de två medarbetarposterna skapas i den "**tomma**" tabellen i den angivna Azure SQL-databasen.

## <a name="next-steps"></a>Nästa steg
| Avsnitt | Beskrivning |
|:--- |:--- |
| [Pipelines](data-factory-create-pipelines.md) |Den här artikeln beskriver pipelines och aktiviteter i Azure Data Factory. |
| [Datauppsättningar](data-factory-create-datasets.md) |I den här artikeln förklaras hur datauppsättningar fungerar i Azure Data Factory. |
| [Schemaläggning och körning](data-factory-scheduling-and-execution.md) |I den här artikeln beskrivs aspekter för schemaläggning och körning av Azure Data Factory-programmodellen. |
[Referens för .NET-API:et för Data Factory](/dotnet/api/) | Innehåller information om Data Factory .NET SDK (leta efter Microsoft.Azure.Management.DataFactories.Models i trädvyn).

