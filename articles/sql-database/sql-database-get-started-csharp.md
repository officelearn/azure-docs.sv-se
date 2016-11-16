---
title: "Testa SQL Database: Använd C# för att skapa en SQL-databas | Microsoft Docs"
description: "Testa SQL Database för att utveckla SQL- och C#-appar och skapa en Azure SQL Database med C# med hjälp av SQL Database-biblioteket för .NET."
keywords: "försök sql, sql-c#"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: cgronlun
ms.assetid: cfff2299-a474-4054-8d99-759af1ae5188
ms.service: sql-database
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: csharp
ms.workload: data-management
ms.date: 10/04/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 0ffe433d9899610d5ff83c66b6cdaaecd16e9c57


---
# <a name="try-sql-database-use-c-to-create-a-sql-database-with-the-sql-database-library-for-net"></a>Testa SQL Database: Använd C# för att skapa en SQL-databas med SQL Database-biblioteket för .NET
> [!div class="op_single_selector"]
> * [Azure-portal](sql-database-get-started.md)
> * [C#](sql-database-get-started-csharp.md)
> * [PowerShell](sql-database-get-started-powershell.md)
> 
> 

Läs om hur man använder C# för att skapa en Azure SQL-databas med [Microsoft Azure SQL Management-biblioteket för .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql). Den här artikeln beskriver hur du skapar en enkel databas med SQL och C#. För att skapa elastiska databaspooler, se [Skapa en elastisk databaspool](sql-database-elastic-pool-create-csharp.md).

Azure SQL Database Management-biblioteket för .NET, tillhandahåller en [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)-baserad API som omsluter det [Resource Manager-baserade SQL Database-REST API:et](https://msdn.microsoft.com/library/azure/mt163571.aspx).

> [!NOTE]
> Flera nya funktioner i SQL Database stöds bara när du använder [distributionsmodellen Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) så du bör alltid använda det senaste **Azure SQL Database Management-biblioteket för .NET ([docs](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [NuGet-paket](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))**. De äldre [klassiska distributionsmodellbaserade biblioteken](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql) stöds enbart för bakåtkompatibilitet så det rekommenderas att du använder de nyare Resource Manager-baserade biblioteken.
> 
> 

Du behöver följande för att slutföra stegen i den här artikeln:

* En Azure-prenumeration. Om du behöver en Azure-prenumeration klickar du bara på **KOSTNADSFRITT KONTO** överst på sidan och går tillbaka till den här artikeln efteråt.
* Visual Studio. För en kostnadsfri version av Visual Studio, kan du gå till sidan [Visual Studio-hämtningar](https://www.visualstudio.com/downloads/download-visual-studio-vs).

> [!NOTE]
> Den här artikeln skapar en ny, tom SQL-databas. Ändra metoden *CreateOrUpdateDatabase(...)* i följande exempel för att kopiera databaser, skala databaser, skapa en databas i en pool och så vidare. Mer information finns i kurserna [DatabaseCreateMode](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.databasecreatemode.aspx) och [DatabaseProperties](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.databaseproperties.aspx).
> 
> 

## <a name="create-a-console-app-and-install-the-required-libraries"></a>Skapa en konsolapp och installera nödvändiga bibliotek
1. Starta Visual Studio.
2. Klicka på **Arkiv** > **Nytt** > **Projekt**.
3. Skapa ett C#-baserat **konsolprogram** och ge det namnet *SqlDbConsoleApp*

Skapa en SQL-databas med C# genom att läsa in nödvändiga hanteringsbibliotek (med hjälp av [Package Manager-konsolen](http://docs.nuget.org/Consume/Package-Manager-Console)):

1. Klicka på **Verktyg** > **NuGet Package Manager** > **Package Manager Console**.
2. Skriv in `Install-Package Microsoft.Azure.Management.Sql –Pre` för att installera det senaste [Microsoft Azure SQL Management-biblioteket](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql).
3. Installera [Microsoft Azure Resource Manager Library](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager) genom att skriva `Install-Package Microsoft.Azure.Management.ResourceManager –Pre`.
4. Installera [Microsoft Azure Common Authentication Library](https://www.nuget.org/packages/Microsoft.Azure.Common.Authentication) genom att skriva `Install-Package Microsoft.Azure.Common.Authentication –Pre`. 

> [!NOTE]
> Exemplen i den här artikeln använder en synkron form av varje API-begäran och blockerar tills REST-anropet på den underliggande tjänsten har slutförts. Det finns asynkrona metoder tillgängliga.
> 
> 

## <a name="create-a-sql-database-server-firewall-rule-and-sql-database-c-example"></a>Skapa en SQL Database-server, brandväggsregel och SQL-databas – C#-baserat exempel
Följande exempel skapar en resursgrupp, server, brandväggsregel och en SQL-databas. Information om hur du hämtar `_subscriptionId, _tenantId, _applicationId, and _applicationSecret`-variablerna finns i [Skapa ett tjänstobjekt för att komma åt resurser](#create-a-service-principal-to-access-resources).

Ersätt innehållet i **Program.cs** med följande och uppdatera `{variables}` med dina appvärden (utelämna `{}`).

    using Microsoft.Azure;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;

    namespace SqlDbConsoleApp
    {
    class Program
       {
        // For details about these four (4) values, see
        // https://azure.microsoft.com/documentation/articles/resource-group-authenticate-service-principal/
        static string _subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _tenantId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _applicationId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _applicationSecret = "{your-password}";

        // Create management clients for the Azure resources your app needs to work with.
        // This app works with Resource Groups, and Azure SQL Database.
        static ResourceManagementClient _resourceMgmtClient;
        static SqlManagementClient _sqlMgmtClient;

        // Authentication token
        static AuthenticationResult _token;

        // Azure resource variables
        static string _resourceGroupName = "{resource-group-name}";
        static string _resourceGrouplocation = "{Azure-region}";

        static string _serverlocation = _resourceGrouplocation;
        static string _serverName = "{server-name}";
        static string _serverAdmin = "{server-admin-login}";
        static string _serverAdminPassword = "{server-admin-password}";

        static string _firewallRuleName = "{firewall-rule-name}";
        static string _startIpAddress = "{0.0.0.0}";
        static string _endIpAddress = "{255.255.255.255}";

        static string _databaseName = "{dbfromcsarticle}";
        static string _databaseEdition = DatabaseEditions.Basic;
        static string _databasePerfLevel = ""; // "S0", "S1", and so on here for other tiers


        static void Main(string[] args)
        {
            // Authenticate:
            _token = GetToken(_tenantId, _applicationId, _applicationSecret);
            Console.WriteLine("Token acquired. Expires on:" + _token.ExpiresOn);

            // Instantiate management clients:
            _resourceMgmtClient = new ResourceManagementClient(new Microsoft.Rest.TokenCredentials(_token.AccessToken));
            _sqlMgmtClient = new SqlManagementClient(new TokenCloudCredentials(_subscriptionId, _token.AccessToken));


            Console.WriteLine("Resource group...");
            ResourceGroup rg = CreateOrUpdateResourceGroup(_resourceMgmtClient, _subscriptionId, _resourceGroupName, _resourceGrouplocation);
            Console.WriteLine("Resource group: " + rg.Id);


            Console.WriteLine("Server...");
            ServerGetResponse sgr = CreateOrUpdateServer(_sqlMgmtClient, _resourceGroupName, _serverlocation, _serverName, _serverAdmin, _serverAdminPassword);
            Console.WriteLine("Server: " + sgr.Server.Id);

            Console.WriteLine("Server firewall...");
            FirewallRuleGetResponse fwr = CreateOrUpdateFirewallRule(_sqlMgmtClient, _resourceGroupName, _serverName, _firewallRuleName, _startIpAddress, _endIpAddress);
            Console.WriteLine("Server firewall: " + fwr.FirewallRule.Id);

            Console.WriteLine("Database...");
            DatabaseCreateOrUpdateResponse dbr = CreateOrUpdateDatabase(_sqlMgmtClient, _resourceGroupName, _serverName, _databaseName, _databaseEdition, _databasePerfLevel);
            Console.WriteLine("Database: " + dbr.Database.Id);


            Console.WriteLine("Press any key to continue...");
            Console.ReadKey();
        }

        static ResourceGroup CreateOrUpdateResourceGroup(ResourceManagementClient resourceMgmtClient, string subscriptionId, string resourceGroupName, string resourceGroupLocation)
        {
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = resourceGroupLocation,
            };
            resourceMgmtClient.SubscriptionId = subscriptionId;
            ResourceGroup resourceGroupResult = resourceMgmtClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
            return resourceGroupResult;
        }

        static ServerGetResponse CreateOrUpdateServer(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverLocation, string serverName, string serverAdmin, string serverAdminPassword)
        {
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = serverLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = serverAdmin,
                    AdministratorLoginPassword = serverAdminPassword,
                    Version = "12.0"
                }
            };
            ServerGetResponse serverResult = sqlMgmtClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
            return serverResult;
        }


        static FirewallRuleGetResponse CreateOrUpdateFirewallRule(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string firewallRuleName, string startIpAddress, string endIpAddress)
        {
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = startIpAddress,
                    EndIpAddress = endIpAddress
                }
            };
            FirewallRuleGetResponse firewallResult = sqlMgmtClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
            return firewallResult;
        }



        static DatabaseCreateOrUpdateResponse CreateOrUpdateDatabase(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string databaseName, string databaseEdition, string databasePerfLevel)
        {
            // Retrieve the server that will host this database
            Server currentServer = sqlMgmtClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    CreateMode = DatabaseCreateMode.Default,
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerfLevel
                }
            };
            DatabaseCreateOrUpdateResponse dbResponse = sqlMgmtClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
            return dbResponse;
        }



        private static AuthenticationResult GetToken(string tenantId, string applicationId, string applicationSecret)
        {
            AuthenticationContext authContext = new AuthenticationContext("https://login.windows.net/" + tenantId);
            _token = authContext.AcquireToken("https://management.core.windows.net/", new ClientCredential(applicationId, applicationSecret));
            return _token;
        }
      }
    }





## <a name="create-a-service-principal-to-access-resources"></a>Skapa ett tjänstobjekt för att komma åt resurser
Följande PowerShell-skript skapar Active Directory-programmet (AD) och tjänstobjektet som vi behöver för att autentisera vår C#-app. Skriptet matar ut värden som vi behöver för det föregående C#-exemplet. Detaljerad information finns i [Skapa ett tjänstobjekt med Azure PowerShell för att komma åt resurser](../resource-group-authenticate-service-principal.md).

    # Sign in to Azure.
    Add-AzureRmAccount

    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzureRmContext -SubscriptionId $subscriptionId

    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.

    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"

    # Create a AAD app
    $azureAdApplication = New-AzureRmADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret

    # Create a Service Principal for the app
    $svcprincipal = New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15

    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid


    # Output the values we need for our C# application to successfully authenticate

    Write-Output "Copy these values into the C# sample app"

    Write-Output "_subscriptionId:" (Get-AzureRmContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzureRmContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret



## <a name="next-steps"></a>Nästa steg
När du nu har testat SQL Database och ställt in en databas med C#, är du redo för följande artiklar:

* [Anslut till SQL Database med SQL Server Management Studio och kör en exempelfråga i T-SQL](sql-database-connect-query-ssms.md)

## <a name="additional-resources"></a>Ytterligare resurser
* [SQL Database](https://azure.microsoft.com/documentation/services/sql-database/)
* [Databasklass](https://msdn.microsoft.com/library/azure/microsoft.azure.management.sql.models.database.aspx)

<!--Image references-->
[1]: ./media/sql-database-get-started-csharp/aad.png
[2]: ./media/sql-database-get-started-csharp/permissions.png
[3]: ./media/sql-database-get-started-csharp/getdomain.png
[4]: ./media/sql-database-get-started-csharp/aad2.png
[5]: ./media/sql-database-get-started-csharp/aad-applications.png
[6]: ./media/sql-database-get-started-csharp/add.png
[7]: ./media/sql-database-get-started-csharp/add-application.png
[8]: ./media/sql-database-get-started-csharp/add-application2.png
[9]: ./media/sql-database-get-started-csharp/clientid.png



<!--HONumber=Nov16_HO2-->


