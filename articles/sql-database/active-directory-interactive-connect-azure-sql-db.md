---
title: ActiveDirectoryInteractive ansluter till SQL | Microsoft Docs
description: C#-kod exempel med förklaringar för att ansluta till Azure SQL Database med hjälp av SqlAuthenticationMethod.ActiveDirectoryInteractive läget.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: GeneMi
ms.date: 03/12/2019
manager: craigg
ms.openlocfilehash: bc7274308b8a349d16866f107eac4a57e115be9e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58850168"
---
# <a name="connect-to-azure-sql-database-with-azure-multi-factor-authentication"></a>Ansluta till Azure SQL Database med Azure Multi-Factor Authentication

Den här artikeln innehåller en C# program som ansluter till Azure SQL Database. Interaktivt läge-autentisering, vilket stöder används [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).

Läs mer om Multi-Factor Authentication-stöd för SQL-verktyg, [Azure Active Directory-stöd i SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory).

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Multi-Factor Authentication för Azure SQL-databas

Från och med .NET Framework version 4.7.2, vilken uppräkningen [ `SqlAuthenticationMethod` ](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) har ett nytt värde: `ActiveDirectoryInteractive`. I en klient C# programmet, uppräkningsvärdet gör att systemet använder Azure Active Directory (Azure AD) interaktiva läget som stöder Multi-Factor Authentication för att ansluta till en Azure SQL database. Den användare som kör programmet får dialogrutorna följande:

* En dialogruta som visar ett Azure AD-användarnamn och frågar efter användarens lösenord.

   Om användarens domän är federerad med Azure AD, visas den här dialogrutan inte, eftersom inga lösenord krävs.

   Om Azure AD-princip ålägger användaren Multi-Factor Authentication, visas två dialogrutorna.

* Första gången en användare genomgår Multifaktorautentisering, visas en dialogruta som frågar efter ett mobiltelefonnummer att skicka textmeddelanden till. Varje meddelande som innehåller den *Verifieringskod* som användaren måste ange i nästa dialogruta.

* En dialogruta som frågar efter en Multi-Factor Authentication-Verifieringskod som systemet har skickats till en mobiltelefon.

Information om hur du konfigurerar Azure AD för att kräva Multifaktorautentisering finns i [komma igång med Azure Multi-Factor Authentication i molnet](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud).

Skärmdumpar av dialogrutorna finns [konfigurera Multi-Factor authentication för SQL Server Management Studio och Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!TIP]
> Du kan söka API: er i .NET Framework med den [.NET API-webbläsare verktyget sidan](https://docs.microsoft.com/dotnet/api/).
>
> Du kan också söka direkt med den [valfritt? termen =&lt;Sök värdet&gt; parametern](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod).

## <a name="configure-your-c-application-in-the-azure-portal"></a>Konfigurera din C# program i Azure portal

Innan du börjar måste du ha en [Azure SQL Database-server](sql-database-get-started-portal.md) skapats och är tillgänglig.

### <a name="register-your-app-and-set-permissions"></a>Registrera din app och ange behörigheter

Du använder Azure AD-autentisering, din C# programmet har registrera som en Azure AD-program. Om du vill registrera en app, måste du vara antingen en Azure AD-administratör eller en användare som tilldelats Azure AD *programutvecklare* roll. Mer information om hur du tilldelar roller finns i [tilldela rollerna Administratör och icke-administratör för användare med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

Du har slutfört en appregistrering genereras och visas i en **program-ID**. Programmet måste ange följande ID för att ansluta.

Att registrera och ange nödvändiga behörigheter för ditt program:

1. I Azure-portalen väljer du **Azure Active Directory** > **appregistreringar** > **ny programregistrering**.

    ![Appregistrering](media/active-directory-interactive-connect-azure-sql-db/image1.png)

    När appregistreringen har skapats kan den **program-ID** värde genereras och visas.

    ![App-ID som visas](media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. Välj **registrerad app** > **inställningar** > **nödvändiga behörigheter** > **Lägg till**.

    ![Behörighetsinställningar för registrerad app](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. Välj **nödvändiga behörigheter** > **Lägg till** > **Välj en API** > **Azure SQL Database**.

    ![Lägg till åtkomst till API: et för Azure SQL Database](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. Välj **API-åtkomst** > **Välj behörigheter** > **delegerade behörigheter**.

    ![Delegeringsbehörigheter till API: et för Azure SQL Database](media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-sql-database-server"></a>Ange en Azure AD-administratör för din SQL Database-server

För din C# program som ska köras, en Azure SQL server-administratören måste tilldela en Azure AD-administratör för din SQL Database-server. 

På den **SQL Server** väljer **Active Directory-administratör** > **konfigurera administratör**.

Mer information om Azure AD-administratörer och användare för Azure SQL Database finns i skärmbilderna i [konfigurera och hantera Azure Active Directory-autentisering med SQL Database](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server).

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>Lägg till en icke-administratörsanvändare till en viss databas (valfritt)

En Azure AD-administratör för en SQL Database-server kan köra den C# exempelprogrammet. Azure AD-användare kan köra programmet om de finns i databasen. En Azure AD-SQL-administratör eller Azure AD-användare som redan finns i databasen och har den `ALTER ANY USER` behörighet på databasen kan lägga till en användare.

Du kan lägga till en användare till databasen med SQL [ `Create User` ](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) kommando. Ett exempel är `CREATE USER [<username>] FROM EXTERNAL PROVIDER`.

Mer information finns i [Använd Azure Active Directory-autentisering för autentisering med SQL Database Managed Instance eller SQL Data Warehouse](sql-database-aad-authentication.md).

## <a name="new-authentication-enum-value"></a>Ny autentisering enum-värde

Den C# exempel förlitar sig på den [ `System.Data.SqlClient` ](https://docs.microsoft.com/dotnet/api/system.data.sqlclient) namnområde. Är särskilt intressanta för multi-Factor Authentication i uppräkningen `SqlAuthenticationMethod`, som har följande värden:

- `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   Använd det här värdet med ett användarnamn för Azure AD för att implementera Multifaktorautentisering. Det här värdet är fokus i den här artikeln. Den genererar en interaktiv upplevelse genom att visa dialogrutorna för användarens lösenord och sedan för Mfa-valideringen om Multifaktorautentisering har införts på den här användaren. Det här värdet är tillgängliga från och med .NET Framework version 4.7.2.

- `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  Använd det här värdet för en *federerad* konto. Användarnamnet är ett federerat konto kända till Windows-domän. Den här autentiseringsmetoden stöd inte för Multifaktorautentisering.

- `SqlAuthenticationMethod.ActiveDirectoryPassword`

  Använd det här värdet för autentisering som kräver en Azure AD-användarnamn och lösenord. Azure SQL Database gör autentiseringen. Den här metoden stöder inte Multifaktorautentisering.

## <a name="set-c-parameter-values-from-the-azure-portal"></a>Ange C# parametervärden från Azure portal

För den C# program för att köra, måste du tilldela statiska fält rätt värden. Här visas fälten med exempelvärden. Filterläget visas är Azure portal där du kan hämta de nödvändiga värdena.

| Statisk fältnamn | Exempelvärde | Var i Azure-portalen |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | **SQL-servrar** > **filtrera efter namn** |
| AzureAD_UserID | "auser\@abc.onmicrosoft.com" | **Azure Active Directory** > **användaren** > **ny gästanvändare** |
| Initial_DatabaseName | "myDatabase" | **SQL-servrar** > **SQL-databaser** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Azure Active Directory** > **appregistreringar** > **Sök efter namn** > **program-ID** |
| RedirectUri | ny Uri (”https://mywebserver.com/”) | **Azure Active Directory** > **appregistreringar** > **Sök efter namn** > *[Your App-registrering]*  >  **Inställningar** > **RedirectURIs**<br /><br />I den här artikeln är ett giltigt värde bra för RedirectUri, eftersom den inte användas här. |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio"></a>Verifiera med SQL Server Management Studio

Innan du kör den C# programmet, är det en bra idé att kontrollera att din installation och konfiguration är korrekta i SQL Server Management Studio (SSMS). Alla C# programmet fel kan sedan begränsas till källkoden.

### <a name="verify-sql-database-firewall-ip-addresses"></a>Verifiera IP-adresser för SQL Database-brandvägg

Kör SSMS från samma dator, i samma byggnad, där du planerar att köra den C# program. För det här testet alla **autentisering** läge är OK. Om det inte finns något indikerar att serverbrandväggen databas inte tar emot din IP-adress, se [Azure SQL Database-servernivå och databasnivå brandväggsregler](sql-database-firewall-configure.md) om du behöver hjälp.

### <a name="verify-azure-active-directory-multi-factor-authentication"></a>Verifiera Azure Active Directory-Multifaktorautentisering

Kör SSMS igen den här gången med **autentisering** inställd **Active Directory - Universal med stöd för MFA**. Det här alternativet kräver SSMS 17,5 eller senare.

Mer information finns i [konfigurera Multi-Factor Authentication för SSMS och Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!NOTE]
> Om du är en gästanvändare i databasen kan behöva du också ange domännamnet för Azure AD för databasen: Välj **alternativ** > **AD-domän eller klient-ID**. För att hitta domännamnet i Azure portal, Välj **Azure Active Directory** > **anpassade domännamn**. I den C# exempelprogrammet, vilket ger ett domännamn inte är nödvändigt.

## <a name="c-code-example"></a>C#-kodexempel

I exempel C# program som förlitar sig på den [ *Microsoft.IdentityModel.Clients.ActiveDirectory* ](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory) DLL-sammansättningen.

Om du vill installera det här paketet i Visual Studio, Välj **projekt** > **hantera NuGet-paket**. Sök efter och installera **Microsoft.IdentityModel.Clients.ActiveDirectory**.

Det här är ett exempel på C# källkod.

```csharp

using System;

// Reference to Azure AD authentication assembly
using Microsoft.IdentityModel.Clients.ActiveDirectory;

using DA = System.Data;
using SC = System.Data.SqlClient;
using AD = Microsoft.IdentityModel.Clients.ActiveDirectory;
using TX = System.Text;
using TT = System.Threading.Tasks;

namespace ADInteractive5
{
    class Program
    {
        // ASSIGN YOUR VALUES TO THESE STATIC FIELDS !!
        static public string Az_SQLDB_svrName = "<Your SQL DB server>";
        static public string AzureAD_UserID = "<Your User ID>";
        static public string Initial_DatabaseName = "<Your Database>";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<Your App ID>";
        static public readonly Uri RedirectUri = new Uri("<Your URI>");

        public static void Main(string[] args)
        {
            var provider = new ActiveDirectoryAuthProvider();

            SC.SqlAuthenticationProvider.SetProvider(
                SC.SqlAuthenticationMethod.ActiveDirectoryInteractive,
                //SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated,  // Alternatives.
                //SC.SqlAuthenticationMethod.ActiveDirectoryPassword,
                provider);

            Program.Connection();
        }

        public static void Connection()
        {
            SC.SqlConnectionStringBuilder builder = new SC.SqlConnectionStringBuilder();

            // Program._  static values that you set earlier.
            builder["Data Source"] = Program.Az_SQLDB_svrName;
            builder.UserID = Program.AzureAD_UserID;
            builder["Initial Catalog"] = Program.Initial_DatabaseName;

            // This "Password" is not used with .ActiveDirectoryInteractive.
            //builder["Password"] = "<YOUR PASSWORD HERE>";

            builder["Connect Timeout"] = 15;
            builder["TrustServerCertificate"] = true;
            builder.Pooling = false;

            // Assigned enum value must match the enum given to .SetProvider().
            builder.Authentication = SC.SqlAuthenticationMethod.ActiveDirectoryInteractive;
            SC.SqlConnection sqlConnection = new SC.SqlConnection(builder.ConnectionString);

            SC.SqlCommand cmd = new SC.SqlCommand(
                "SELECT '******** MY QUERY RAN SUCCESSFULLY!! ********';",
                sqlConnection);

            try
            {
                sqlConnection.Open();
                if (sqlConnection.State == DA.ConnectionState.Open)
                {
                    var rdr = cmd.ExecuteReader();
                    var msg = new TX.StringBuilder();
                    while (rdr.Read())
                    {
                        msg.AppendLine(rdr.GetString(0));
                    }
                    Console.WriteLine(msg.ToString());
                    Console.WriteLine(":Success");
                }
                else
                {
                    Console.WriteLine(":Failed");
                }
                sqlConnection.Close();
            }
            catch (Exception ex)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("Connection failed with the following exception...");
                Console.WriteLine(ex.ToString());
                Console.ResetColor();
            }
        }
    } // EOClass Program.

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2.
    ///  . 
    /// 1. Interactive,  2. Integrated,  3. Password
    ///  . 
    /// All 3 authentication methods are based on the Azure
    /// Active Directory Authentication Library (ADAL) managed library.
    /// </summary>
    public class ActiveDirectoryAuthProvider : SC.SqlAuthenticationProvider
    {
        // Program._ more static values that you set!
        private readonly string _clientId = Program.ClientApplicationID;
        private readonly Uri _redirectUri = Program.RedirectUri;

        public override async TT.Task<SC.SqlAuthenticationToken>
            AcquireTokenAsync(SC.SqlAuthenticationParameters parameters)
        {
            AD.AuthenticationContext authContext =
                new AD.AuthenticationContext(parameters.Authority);
            authContext.CorrelationId = parameters.ConnectionId;
            AD.AuthenticationResult result;

            switch (parameters.AuthenticationMethod)
            {
                case SC.SqlAuthenticationMethod.ActiveDirectoryInteractive:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,  // "https://database.windows.net/"
                        _clientId,
                        _redirectUri,
                        new AD.PlatformParameters(AD.PromptBehavior.Auto),
                        new AD.UserIdentifier(
                            parameters.UserId,
                            AD.UserIdentifierType.RequiredDisplayableId));
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_1 == '.ActiveDirectoryIntegrated'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserCredential());
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryPassword:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_2 == '.ActiveDirectoryPassword'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserPasswordCredential(
                            parameters.UserId,
                            parameters.Password));
                    break;

                default: throw new InvalidOperationException();
            }
            return new SC.SqlAuthenticationToken(result.AccessToken, result.ExpiresOn);
        }

        public override bool IsSupported(SC.SqlAuthenticationMethod authenticationMethod)
        {
            return authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryInteractive
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryPassword;
        }
    } // EOClass ActiveDirectoryAuthProvider.
} // EONamespace.  End of entire program source code.

```

&nbsp;

Det här är ett exempel på den C# testa utdata.

```
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modulen PowerShell Azure Resource Manager är fortfarande stöds av Azure SQL Database, men alla framtida utveckling är för modulen Az.Sql. Dessa cmdlets finns i [i AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandon i modulen Az och AzureRm-moduler är avsevärt identiska.

- [Get-AzSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)
