---
title: ActiveDirectoryInteractive ansluter till SQL
description: C# Kodexempel, med förklaringar, för anslutning till Azure SQL Database med hjälp av SqlAuthenticationMethod.ActiveDirectoryInteractive mode.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: GeneMi, vanto
ms.date: 10/11/2019
ms.openlocfilehash: 5e7d58e5e0fc79e407e77ae9d73314a1d5d22666
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73692298"
---
# <a name="connect-to-azure-sql-database-with-azure-multi-factor-authentication"></a>Ansluta till Azure SQL-databas med Azure Multi Factor-autentisering

Den här artikeln innehåller ett C#-program som ansluter till Azure SQL Database. Programmet använder autentisering i interaktivt läge, som stöder [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).

Mer information om stöd för multifaktorautentisering för SQL-verktyg finns [i Azure Active Directory-stöd i SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory).

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Multifaktorautentisering för Azure SQL Database

Från och med .NET Framework version 4.7.2 har uppräkningen [`SqlAuthenticationMethod`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) ett nytt värde: `ActiveDirectoryInteractive`. I ett klient-C#-program leder uppräkningsvärdet systemet till att använda det interaktiva azure active directory-läget (Azure AD) som stöder multifaktorautentisering för att ansluta till en Azure SQL-databas. Användaren som kör programmet ser följande dialogrutor:

* En dialogruta som visar ett Azure AD-användarnamn och frågar efter användarens lösenord.

   Om användarens domän är federerad med Azure AD visas inte den här dialogrutan eftersom inget lösenord behövs.

   Om Azure AD-principen ålägger multifaktorautentisering för användaren visas följande två dialogrutor.

* Första gången en användare går igenom Multifaktorautentisering visar systemet en dialogruta som ber om ett mobiltelefonnummer att skicka textmeddelanden till. Varje meddelande innehåller den *verifieringskod* som användaren måste ange i nästa dialogruta.

* En dialogruta som frågar efter en multifaktorautentiseringsverifieringskod som systemet har skickat till en mobiltelefon.

Information om hur du konfigurerar Azure AD för att kräva multifaktorautentisering finns [i Komma igång med Azure Multi-Factor Authentication i molnet](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud).

Skärmbilder av dessa dialogrutor finns i [Konfigurera multifaktorautentisering för SQL Server Management Studio och Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!TIP]
> Du kan söka i .NET Framework API:er med [verktygssidan för .NET API Browser](https://docs.microsoft.com/dotnet/api/).
>
> Du kan också söka direkt med [den&gt; valfria sökvärdesparametern ?term=&lt;](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod).

## <a name="configure-your-c-application-in-the-azure-portal"></a>Konfigurera ditt C#-program i Azure-portalen

Innan du börjar bör du ha en [Azure SQL Database-server](sql-database-get-started-portal.md) skapad och tillgänglig.

### <a name="register-your-app-and-set-permissions"></a>Registrera appen och ange behörigheter

Om du vill använda Azure AD-autentisering måste ditt C#-program registrera dig som ett Azure AD-program. Om du vill registrera en app måste du antingen vara en Azure AD-administratör eller en användare som tilldelats azure AD *Application Developer-rollen.* Mer information om hur du tilldelar roller finns i [Tilldela administratörs- och icke-administratörsroller till användare med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

Om du slutför en appregistrering genereras och visas ett **program-ID**. Programmet måste inkludera detta ID för att ansluta.

Så här registrerar och anger du nödvändiga behörigheter för ditt program:

1. I Azure-portalen väljer du **Azure Active Directory** > **App registreringar** > **Ny registrering**.

    ![Appregistrering](media/active-directory-interactive-connect-azure-sql-db/image1.png)

    När appregistreringen har skapats genereras och visas **program-ID-värdet.**

    ![App-ID visas](media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. Välj **API-behörigheter** > **Lägg till en behörighet**.

    ![Behörighetsinställningar för registrerad app](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. Välj **API:er som min organisation använder** > skriver Azure SQL **Database** i sök-> och väljer Azure **SQL Database**.

    ![Lägga till åtkomst till API för Azure SQL Database](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. Välj **Delegerade behörigheter** > **user_impersonation** > Lägg till**behörigheter**.

    ![Delegera behörigheter till API för Azure SQL Database](media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-sql-database-server"></a>Ange en Azure AD-administratör för SQL Database-servern

För att C#-programmet ska kunna köras måste en Azure SQL-serveradministratör tilldela en Azure AD-administratör för SQL Database-servern. 

På **SQL Server-sidan** väljer du Admin Set admin för **Active Directory.** > **Set admin**

Mer information om Azure AD-administratörer och användare för Azure SQL Database finns i skärmbilderna i [Konfigurera och hantera Azure Active Directory-autentisering med SQL Database](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server).

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>Lägga till en icke-administratörsanvändare i en viss databas (valfritt)

En Azure AD-administratör för en SQL Database-server kan köra C#-exempelprogrammet. En Azure AD-användare kan köra programmet om de finns i databasen. En Azure AD SQL-administratör eller en Azure AD-användare `ALTER ANY USER` som redan finns i databasen och som har behörigheten för databasen kan lägga till en användare.

Du kan lägga till en användare [`Create User`](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) i databasen med SQL-kommandot. Ett exempel är `CREATE USER [<username>] FROM EXTERNAL PROVIDER`.

Mer information finns i [Använda Azure Active Directory-autentisering för autentisering med SQL Database, Hanterad instans eller SQL Data Warehouse](sql-database-aad-authentication.md).

## <a name="new-authentication-enum-value"></a>Nytt uppräkningsvärde för autentisering

C#-exemplet är beroende [`System.Data.SqlClient`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient) av namnområdet. Av särskilt intresse för multifaktorautentisering `SqlAuthenticationMethod`är uppräkningen , som har följande värden:

- `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   Använd det här värdet med ett Azure AD-användarnamn för att implementera multifaktorautentisering. Detta värde är i fokus för den aktuella artikeln. Den ger en interaktiv upplevelse genom att visa dialogrutor för användarlösenordet och sedan för multifaktorautentiseringsvalidering om multifaktorautentisering införs för den här användaren. Det här värdet är tillgängligt från och med .NET Framework version 4.7.2.

- `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  Använd det här värdet för ett *federerat* konto. För ett federerat konto är användarnamnet känt för Windows-domänen. Den här autentiseringsmetoden stöder inte multifaktorautentisering.

- `SqlAuthenticationMethod.ActiveDirectoryPassword`

  Använd det här värdet för autentisering som kräver ett Azure AD-användarnamn och lösenord. Azure SQL Database gör autentiseringen. Den här metoden stöder inte multifaktorautentisering.

## <a name="set-c-parameter-values-from-the-azure-portal"></a>Ange C#-parametervärden från Azure-portalen

För att C#-programmet ska kunna köras måste du tilldela rätt värden till statiska fält. Här visas fält med exempelvärden. Visas också de Azure-portalplatser där du kan hämta de nödvändiga värdena.

| Statiskt fältnamn | Exempelvärde | Var i Azure-portalen |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | **SQL-servrar** > **Filtrera efter namn** |
| AzureAD_UserID | "auser\@abc.onmicrosoft.com" | **Azure Active** > **Directory-användare** > Ny**gästanvändare** |
| Initial_DatabaseName | "myDatabase" | **SQL-servrar** > **SQL-databaser** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-1111111111111111" | **Azure Active Directory** > **App registreringar** > **Sök efter namn** > **Program-ID** |
| OmdirigeraUri | nya Uri("https://mywebserver.com/") | **Azure Active Directory** > **App registreringar** > **Sök efter namn** > *[Din-App-registrering]* > **Inställningar** > **RedirectURIs**<br /><br />För den här artikeln är alla giltiga värden bra för RedirectUri, eftersom det inte används här. |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio"></a>Verifiera med SQL Server Management Studio

Innan du kör C#-programmet är det en bra idé att kontrollera att dina inställningar och konfigurationer är korrekta i SQL Server Management Studio (SSMS). Alla C# programfel kan sedan begränsas till källkod.

### <a name="verify-sql-database-firewall-ip-addresses"></a>Verifiera IP-adresser för SQL Database-brandväggen

Kör SSMS från samma dator, i samma byggnad, där du planerar att köra C#-programmet. För det här testet är alla **autentiseringsläge** OK. Om det finns några tecken på att databasserverbrandväggen inte accepterar din IP-adress läser du [Azure SQL Database-servernivå och brandväggsregler på databasnivå](sql-database-firewall-configure.md) för hjälp.

### <a name="verify-azure-active-directory-multi-factor-authentication"></a>Verifiera Azure Active Directory multifaktorautentisering

Kör SSMS igen, den här gången med **autentisering** inställd på **Active Directory - Universal med MFA-stöd**. Det här alternativet kräver SSMS version 17.5 eller senare.

Mer information finns i [Konfigurera multifaktorautentisering för SSMS och Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!NOTE]
> Om du är gästanvändare i databasen måste du också ange Azure AD-domännamnet för databasen: Välj **alternativ** > **AD-domännamn eller klient-ID**. Om du vill hitta domännamnet i Azure-portalen väljer du **Azure Active Directory** > **Custom-domännamn**. I exempelprogrammet C# är det inte nödvändigt att ange ett domännamn.

## <a name="c-code-example"></a>Exempel på C#-kod

Exemplet C#-programmet är beroende av [*Microsoft.IdentityModel.Clients.ActiveDirectory*](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory) DLL-sammansättningen.

Om du vill installera det här paketet väljer du **Project** > **Manage NuGet-paket**i Visual Studio . Sök efter och installera **Microsoft.IdentityModel.Clients.ActiveDirectory**.

Detta är ett exempel på C# källkod.

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

Detta är ett exempel på C# testutdata.

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
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling är för Az.Sql-modulen. För dessa cmdlets finns i [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är i stort sett identiska.

- [Få-AzSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)
