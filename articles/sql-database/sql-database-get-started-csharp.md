<properties
    pageTitle="Testa SQL Database: Använd C# för att skapa en SQL-databas | Microsoft Azure"
    description="Testa SQL Database för att utveckla SQL- och C#-appar och skapa en Azure SQL Database med C# med hjälp av SQL Database-biblioteket för .NET."
    keywords="försök sql, sql-c#"   
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="csharp"
   ms.workload="data-management"
   ms.date="05/26/2016"
   ms.author="sstein"/>

# Testa SQL Database: Använd C# för att skapa en SQL-databas med SQL Database-biblioteket för .NET


> [AZURE.SELECTOR]
- [Azure-portalen](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

Lär dig använda C#-kommandon för att skapa en Azure SQL-databas med [Azure SQL Database-biblioteket för .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql). Du kommer att testa SQL Database genom att skapa en databas med SQL och C#. För att skapa elastiska databaspooler, se [Skapa en elastisk databaspool](sql-database-elastic-pool-create-portal.md). Enskilda kodavsnitt är utbrutna för tydlighetens skull och ett exempelkonsol-program för samman alla kommandon i avsnittet längst ned i den här artikeln.

Azure SQL Database-biblioteket för .NET, tillhandahåller en [Azure Resource Manager](../resource-group-overview.md)-baserad API som omsluter det [Resource Manager-baserade SQL Database-REST API:et](https://msdn.microsoft.com/library/azure/mt163571.aspx). Klientbiblioteket följer det vanliga mönstret för Resource Manager-baserade klientbibliotek. Resource Manager kräver resursgrupper och autentisering med [Azure Active Directory](https://msdn.microsoft.com/library/azure/mt168838.aspx) (AAD).

<br>

> [AZURE.NOTE] SQL Database-biblioteket för .NET är i förhandsvisning för tillfället.

<br>

Du behöver följande för att slutföra stegen i den här artikeln:

- En Azure-prenumeration. Om du behöver en Azure-prenumeration, klickar du på **KOSTNADSFRI UTVÄRDERINGSVERSION** överst på sidan och kommer tillbaka till den här artikeln efteråt.
- Visual Studio. För en kostnadsfri version av Visual Studio, kan du gå till sidan [Visual Studio-hämtningar](https://www.visualstudio.com/downloads/download-visual-studio-vs).


## Installera de bibliotek som krävs

För att ställa in en SQL-databas med C#, hämtar du de hanteringsbibliotek som behövs genom att installera följande paket med [pakethanterarkonsolen](http://docs.nuget.org/Consume/Package-Manager-Console) i Visual Studio (**Verktyg** > **NuGet-pakethanteraren** > **pakethanterarkonsolen**):

    Install-Package Microsoft.Azure.Management.Sql –Pre
    Install-Package Microsoft.Azure.Management.ResourceManager –Pre -Version 1.1.1-preview
    Install-Package Microsoft.Azure.Common.Authentication –Pre


## Konfigurera autentisering med Azure Active Directory

Du måste först aktivera ditt klientprogram att komma åt SQL Database-tjänsten genom att ställa in nödvändig autentisering.

För att autentisera ditt klientprogram baserat på den aktuella användaren, behöver du först registrera ditt program i den AAD-domän som är kopplad till prenumerationen som Azure-resurserna har skapats under. Om din Azure-prenumeration har skapats med ett Microsoft-konto istället för ett företags- eller skolkonto, har du redan en standard-AAD-domän.

För att skapa ett nytt program och registrera det i korrekt Active Directory, gör du följande:

1. Gå till den [klassiska Azure-portalen](https://manage.windowsazure.com/).
1. På vänster sida väljer du **Active Directory**-tjänsten och väljer sedan katalogen för att autentisera ditt program och klickar på dess **Namn**.

    ![Testa SQL Database: Ställ in Azure Active Directory (AAD).][1]

2. På katalogsidan, klickar du på **PROGRAM**.

    ![På katalogsidan med program.][5]

4. Klicka på **LÄGG TILL** för att skapa ett nytt program.

5. Välj **Lägg till ett program som min organisation utvecklar**.

5. Ange ett **NAMN** för appen och välj **INTERNT KLIENTPROGRAM**.

    ![Ange information om ditt SQL C#-program.][7]

6. Ange en **OMDIRIGERINGS-URI**. Det behöver inte vara en faktisk slutpunkt, bara en giltig URI.

    ![Lägg till en omdirigerings-URL för ditt SQL C#-program.][8]

7. Slutför skapandet av appen, klicka på **KONFIGURERA** och kopiera **KLIENT-ID** (du kommer att behöva klient-id senare i koden).

    ![Hämta klient-ID för ditt SQL C#-program.][9]


1. Längst ned på sidan, klickar du på **Lägg till program**.
1. Välj **Microsoft-appar**.
1. Välj **Azure Service Management-API** och slutför sedan guiden.
2. Du behöver använda det API du valt för att bevilja de specifika behörigheter som krävs för åtkomst till API:n genom att välja **Åtkomst till Azure Service Management (förhandsversion)**.

    ![Ställ in behörigheter.][2]

2. Klicka på **SPARA**.



### Identifiera domännamnet

Domännamnet krävs för din kod. Ett enkelt sätt att identifiera rätt domännamn är att:

1. Gå till [Azure Portal](http://portal.azure.com).
2. Hovra över ditt namn i det övre högra hörnet och notera domänen som visas i popup-fönstret.

    ![Identifiera domännamnet.][3]





**Ytterligare AAD-resurser**  

Du kan hitta mer information om hur du använder Azure Active Directory för autentisering i [det här användbara blogginlägget](http://www.cloudidentity.com/blog/2013/09/12/active-directory-authentication-library-adal-v1-for-net-general-availability/).


### Hämta åtkomsttoken för den aktuella användaren

Klientprogrammet måste hämta programmets åtkomsttoken för den aktuella användaren. Första gången den här koden körs av en användare, kommer de att uppmanas att ange sina användaruppgifter och den resulterande token cachelagras lokalt. Efterföljande körningar använder sig av token från cachelagringen och kommer bara uppmana användaren att logga in om token har gått ut.

Den här koden returnerar en Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationResult som du behöver i de andra kodavsnitten nedan.

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



> [AZURE.NOTE] Exemplen i den här artikeln använder en synkron form av varje API-begäran och blockerar tills REST-anropet på den underliggande tjänsten har slutförts. Det finns asynkrona metoder tillgängliga.



## Skapa en resursgrupp

I Resource Manager måste alla resurser skapas i en resursgrupp. En resursgrupp är en behållare som innehåller relaterade resurser för ett program. Databasservern i Azure SQL Database, måste skapas inom en befintlig resursgrupp.

        static void CreateResourceGroup()
        {
            creds = new Microsoft.Rest.TokenCredentials(token.AccessToken);

            // Create a resource management client
            ResourceManagementClient resourceClient = new ResourceManagementClient(creds);

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = location,
            };

            //Create a resource group
            resourceClient.SubscriptionId = subscriptionId;
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
        }


## Skapa en server

SQL-databaser finns i servrar. Servernamnet måste vara globalt unikt för alla Azure SQL-servrar så du får ett felmeddelande om servernamnet redan är upptaget. Det är också värt att nämna att det här kommandot kan ta flera minuter att slutföra.

    static void CreateServer()
    {
        // Create a SQL Database management client
        SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

        // Create a server
        ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
        {
            Location = location,
            Properties = new ServerCreateOrUpdateProperties()
            {
                AdministratorLogin = administratorLogin,
                AdministratorLoginPassword = administratorPassword,
                Version = serverVersion
            }
        };
            var serverResult = sqlClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
    }


### Skapa en extern serveradministratör


    // Create a server external admin
    ServerAdministratorCreateOrUpdateParameters aadAdminParameters = new ServerAdministratorCreateOrUpdateParameters()
    {
        Properties = new ServerAdministratorCreateOrUpdateProperties()
        {
            AdministratorType = "ActiveDirectory",
            Login = "<login>",
            Sid = new Guid("<Azure AD admin sid>"),
            TenantId = new Guid("<Azure AD tenant id>")
        }
    };

    var adminResult = sqlClient.ServerAdministrators.CreateOrUpdate(resourceGroupName, serverName, "ActiveDirectory", aadAdminParameters);
    var adminGetResult = sqlClient.ServerAdministrators.Get(resourceGroupName, serverName, "ActiveDirectory");




## Skapa en brandväggsregel för servern som tillåter åtkomst

Som standard kan man inte ansluta till en server från valfri plats. För att kunna ansluta till en server eller en databas på servern, måste man definiera en [brandväggsregel](https://msdn.microsoft.com/library/azure/ee621782.aspx) som ger åtkomst från klientens IP-adress.

Följande exempel skapar en regel som öppnar åtkomst till servern från alla IP-adresser. Det är rekommenderat att du skapar lämpliga SQL-inloggningar och lösenord för att säkra din databas och inte förlitar dig på brandväggsregler som ett primärt intrångsskydd.


        static void CreateFirewallRule()
        {
            // Create a firewall rule on the server
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = "0.0.0.0",
                    EndIpAddress = "255.255.255.255"
                }
            };
            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
        }




För att låta andra Azure-tjänster få åtkomst till en server, lägger du till en brandväggsregel och anger både StartIpAddress och EndIpAddress som 0.0.0.0. Observera att det här gör att Azure-trafik från *alla* Azure-prenumerationer har åtkomst till servern.


## Använd C&#x23; för att skapa en SQL-databas

Följande C#-kommando skapar en ny SQL-databas om det inte redan finns en databas med samma namn på servern. Om det redan finns en databas med samma namn, kommer den att uppdateras.


        static void CreateDatabase()
        {
            // Create a database

            // Retrieve the server on which the database will be created
            Server currentServer = sqlClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerfLevel,
                }
            };
            var dbResponse = sqlClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
        }



## Exempel på C&#x23;-konsolprogram

Följande exempel skapar en resursgrupp, server, brandväggsregel och en SQL-databas. Avsnittet *Konfigurera autentisering med Azure Active Directory* överst i den här artikeln, visar var du får fram värden för clientId-, redirectUri- och domainName-variablerna.


    using Microsoft.Azure;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;

    namespace SqlDbConsoleApp
    {
    class Program
    {
        // Get these values from the Azure portal
        static string subscriptionId = "<Azure subscription id>";
        static string clientId = "<Azure App clientId>";
        static string redirectUri = "<Azure App redirectURI>";
        static string domainName = "<domain>";

        // You create these values
        static string resourceGroupName = "<your resource group name>";
        static string location = "<Azure data center location>";

        static string serverName = "<your server name>";
        static string administratorLogin = "<your server admin>";

        // store your password securely!
        static string administratorPassword = "<your server admin password>";
        static string serverVersion = "12.0";

        static string firewallRuleName = "<your firewall rule name>";

        static string databaseName = "dbfromcsharparticle";
        static string databaseEdition = "Basic"; // Basic, Standard, or Premium
        static string databasePerfLevel = "";

        static AuthenticationResult token;
        static Microsoft.Rest.TokenCredentials creds;

        static SqlManagementClient sqlClient;


        static void Main(string[] args)
        {
            Console.WriteLine("Logging in...");

            token = GetAccessToken();

            Console.WriteLine("Logged in as: " + token.UserInfo.DisplayableId);

            Console.WriteLine("Creating resource group...");
            CreateResourceGroup();

            sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            Console.WriteLine("Creating server...");
            CreateServer();

            Console.WriteLine("Creating firewall rule...");
            CreateFirewallRule();

            Console.WriteLine("Creating database...");

            DatabaseCreateOrUpdateResponse dbResponse = CreateDatabase();
            Console.WriteLine("Status: " + dbResponse.Status.ToString()
                + " Code: " + dbResponse.StatusCode.ToString());

            Console.WriteLine("Press enter to exit...");
            Console.ReadLine();
        }

        static void CreateResourceGroup()
        {
            creds = new Microsoft.Rest.TokenCredentials(token.AccessToken);

            // Create a resource management client
            ResourceManagementClient resourceClient = new ResourceManagementClient(creds);

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = location,
            };

            //Create a resource group
            resourceClient.SubscriptionId = subscriptionId;
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
        }

        static void CreateServer()
        {
            // Create a SQL Database management client
            //SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = location,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = administratorLogin,
                    AdministratorLoginPassword = administratorPassword,
                    Version = serverVersion
                }
            };
            var serverResult = sqlClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
        }

        static void CreateFirewallRule()
        {
            // Create a firewall rule on the server
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    // replace with your client IP address
                    StartIpAddress = "0.0.0.0",
                    EndIpAddress = "255.255.255.255"
                }
            };
            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
        }

        static DatabaseCreateOrUpdateResponse CreateDatabase()
        {
            // Create a database

            // Retrieve the server on which the database will be created
            Server currentServer = sqlClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerfLevel,
                }
            };
            var dbResponse = sqlClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
            return dbResponse;
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
När du nu har testat SQL Database och ställt in en databas med C#, är du redo för följande artiklar:

- [Anslut till SQL Database med SQL Server Management Studio och kör en exempelfråga i T-SQL](sql-database-connect-query-ssms.md)

## Ytterligare resurser

- [SQL Database](https://azure.microsoft.com/documentation/services/sql-database/)





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



<!--HONumber=sep16_HO1-->


