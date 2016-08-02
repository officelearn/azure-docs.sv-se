<properties
    pageTitle="Skapa en elastisk databaspool med C# | Microsoft Azure"
    description="Använd utvecklingstekniker för C#-databaser, för att skapa en skalbar elastisk databaspool i Azure SQL Database så att du kan dela resurser över flera databaser."
    services="sql-database"
    documentationCenter=""
    authors="srinia"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="csharp"
    ms.workload="data-management"
    ms.date="05/27/2016"
    ms.author="srinia"/>

# Skapa en ny elastisk databaspool med C&#x23;

> [AZURE.SELECTOR]
- [Azure-portalen](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


Lär dig hur du skapar en [elastisk databaspool](sql-database-elastic-pool.md) med C&#x23;. 

Vanliga felkoder finns i [SQL-felkoder för SQL Database-klientprogram: anslutningsfel för databasen och andra problem](sql-database-develop-error-messages.md).

Elastiska databaspooler är för närvarande i förhandsvisning och finns bara för SQL Database V12-servrar. Om du har en SQL Database V11-server, kan du [använda PowerShell för att uppgradera till V12 och skapa en pool](sql-database-upgrade-server-portal.md) i ett enda steg.

Exemplen använder sig av [SQL Database-biblioteket för .NET](https://msdn.microsoft.com/library/azure/mt349017.aspx) så du behöver installera biblioteket. Du kan installera det genom att köra följande kommando i [pakethanterarkonsolen](http://docs.nuget.org/Consume/Package-Manager-Console) i Visual Studio (**Verktyg** > **NuGet-pakethanteraren** > **Pakethanterarkonsolen**):

    PM> Install-Package Microsoft.Azure.Management.Sql –Pre

## Skapa en ny pool

Skapa en [SqlManagementClient](https://msdn.microsoft.com/library/microsoft.azure.management.sql.sqlmanagementclient)-instans med värden från [Azure Active Directory](sql-database-client-id-keys.md). Skapa en [ElasticPoolCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.elasticpoolcreateorupdateparameters)-instans och anropa [CreateOrUpdate](https://msdn.microsoft.com/library/microsoft.azure.management.sql.databaseoperationsextensions.createorupdate)-metoden. Värdena för eDTU per pool, min och max Dtu:er, är begränsade av tjänstnivåvärdet (basic, standard eller premium). Se [eDTU och lagringsgränser för elastiska pooler och elastiska databaser](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases).


    ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = "South Central US",
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = "Standard",
            Dtu = 400,
            DatabaseDtuMin = 0,
            DatabaseDtuMax = 100
         }
    };

    // Create the pool
    var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);

## Skapa en ny databas i en pool

Skapa en [DataBaseCreateorUpdateProperties](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.databasecreateorupdateproperties)-instans och ange egenskaper för den nya databasen. Sedan anropar du metoden CreateOrUpdate med resursgrupp, servernamn och namn på ny databas.

    // Create a database: configure create or update parameters and properties explicitly
    DatabaseCreateOrUpdateParameters newPooledDatabaseParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentServer.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = 268435456000, // 250 GB,
            Collation = "SQL_Latin1_General_CP1_CI_AS"
        }
    };

    var poolDbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database2", newPooledDatabaseParameters);

För att flytta en befintlig databas till en pool, se [Flytta en databas till en elastisk pool](sql-database-elastic-pool-manage-csharp.md#Move-a-database-into-an-elastic-pool).

## Exempel: Skapa en pool med C&#x23

Det här exemplet skapar en ny Azure resursgrupp, en ny Azure SQL Server-instans och en ny elastisk pool. 
 

Följande bibliotek krävs för att köra det här exemplet. Du kan installera genom att köra följande kommandon i [pakethanterarkonsolen](http://docs.nuget.org/Consume/Package-Manager-Console) i Visual Studio (**Verktyg** > **NuGet-pakethanteraren** > **Pakethanterarkonsolen**):

    Install-Package Microsoft.Azure.Management.Sql –Pre
    Install-Package Microsoft.Azure.Management.Resources –Pre
    Install-Package Microsoft.Azure.Common.Authentication –Pre

Skapa en konsolapp och ersätt innehållet i Program.cs med följande. För att få fram den nödvändiga klient-ID:n och andra relaterade värden, se [Registrera din app och hämta nödvändiga klientvärden för att ansluta din app till SQL Database](sql-database-client-id-keys.md). Använd cmdleten [Get-AzureRmSubscription](https://msdn.microsoft.com/library/mt619284.aspx) för att hämta värdet för subscriptionId.

    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    
    namespace SqlDbElasticPoolsSample
    {
    class Program
    {
        // authentication variables
        static string subscriptionId = "<your Azure subscription>";
        static string clientId = "<your client id>";
        static string redirectUri = "<your redirect URI>";
        static string domainName = "<i.e. microsoft.onmicrosoft.com>";
        static AuthenticationResult token;

        // resource group variables
        static string datacenterLocation = "<location>";
        static string resourceGroupName = "<resource group name>";

        // server variables
        static string serverName = "<server name>";
        static string adminLogin = "<server admin>";
        static string adminPassword = "<server password (store it securely!)>";
        static string serverVersion = "12.0";

        // pool variables
        static string elasticPoolName = "<pool name>";
        static string poolEdition = "Standard";
        static int poolDtus = 400;
        static int databaseMinDtus = 0;
        static int databaseMaxDtus = 100;


        static void Main(string[] args)
        {
            // Sign in to Azure
            token = GetAccessToken();
            Console.WriteLine("Logged in as: " + token.UserInfo.DisplayableId);

            // Create a resource group
            ResourceGroup rg = CreateResourceGroup();
            Console.WriteLine(rg.Name + " created.");

            // Create a server
            Console.WriteLine("Creating server... ");
            ServerGetResponse srvr = CreateServer();
            Console.WriteLine("Creation of server " + srvr.Server.Name + ": " + srvr.StatusCode.ToString());

            // Create a pool
            Console.WriteLine("Creating elastic database pool... ");
            ElasticPoolCreateOrUpdateResponse epool = CreateElasticDatabasePool();
            Console.WriteLine("Creation of pool " + epool.ElasticPool.Name + ": " + epool.Status.ToString());


            // keep the console open until Enter is pressed!
            Console.WriteLine("Press Enter to exit.");
            Console.ReadLine();
        }


        static ElasticPoolCreateOrUpdateResponse CreateElasticDatabasePool()
        {
            // Create a SQL Database management client
            SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Create elastic pool: configure create or update parameters and properties explicitly
            ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
            {
                Location = datacenterLocation,
                Properties = new ElasticPoolCreateOrUpdateProperties()
                {
                    Edition = poolEdition,
                    Dtu = poolDtus, 
                    DatabaseDtuMin = databaseMinDtus,
                    DatabaseDtuMax = databaseMaxDtus
                }
            };

            // Create the pool
            var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate(resourceGroupName, serverName, elasticPoolName, newPoolParameters);
            return newPoolResponse;
        }



        static ServerGetResponse CreateServer()
        {

            // Create a SQL Database management client
            SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = datacenterLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = adminLogin,
                    AdministratorLoginPassword = adminPassword,
                    Version = serverVersion
                }
            };

            var serverResult = sqlClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
            return serverResult;

        }


        static ResourceGroup CreateResourceGroup()
        {
           
            Microsoft.Rest.TokenCredentials creds = new Microsoft.Rest.TokenCredentials(token.AccessToken);
            // Create a resource management client 
            ResourceManagementClient resourceClient = new ResourceManagementClient(creds);

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = datacenterLocation,
            };

            //Create a resource group
            resourceClient.SubscriptionId = subscriptionId;
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
            return resourceGroupResult;
        }


        private static AuthenticationResult GetAccessToken()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" + domainName /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken
                ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                    clientId,
            new Uri(redirectUri) /* redirect URI */,
            PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }
    }
    }

  

## Nästa steg

- [Hantera din pool](sql-database-elastic-pool-manage-csharp.md)
- [Skapa elastiska jobb](sql-database-elastic-jobs-overview.md): Elastiska jobb låter dig köra T-SQL-skript mot valfritt antal databaser i poolen.
- [Skala ut med Azure SQL Database](sql-database-elastic-scale-introduction.md): Använd elastisk databasverktyg för att skala ut.

## Ytterligare resurser

- [SQL Database](https://azure.microsoft.com/documentation/services/sql-database/)
- [Azure Resource Manager API:er](https://msdn.microsoft.com/library/azure/dn948464.aspx)


<!--HONumber=Jun16_HO2-->


