---
title: ActiveDirectoryInteractive ansluter till SQL | Microsoft Docs
description: C#Kod exempel, med förklaringar, för att ansluta till Azure SQL Database med hjälp av SqlAuthenticationMethod. ActiveDirectoryInteractive-läge.
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
ms.openlocfilehash: 4f36dcc9953134ac5dd24d3d762ac0dde9949ab7
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561364"
---
# <a name="connect-to-azure-sql-database-with-azure-multi-factor-authentication"></a>Ansluta till Azure SQL Database med Azure Multi-Factor Authentication

Den här artikeln innehåller C# ett program som ansluter till Azure SQL Database. Programmet använder autentisering i interaktivt läge, som stöder [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).

Mer information om Multi-Factor Authentication-stöd för SQL-verktyg finns [i Azure Active Directory support i SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory).

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Multi-Factor Authentication för Azure SQL Database

Från och med .NET Framework version 4.7.2 har uppräkningen [`SqlAuthenticationMethod`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) ett nytt värde:. `ActiveDirectoryInteractive` I ett klient C# program dirigerar uppräkning svärdet systemet till att använda det interaktiva läget Azure Active Directory (Azure AD) som stöder Multi-Factor Authentication för att ansluta till en Azure SQL-databas. Den användare som kör programmet ser följande dialog rutor:

* En dialog ruta som visar ett användar namn för Azure AD och som frågar efter användarens lösen ord.

   Om användarens domän är federerad med Azure AD visas inte den här dialog rutan eftersom inget lösen ord krävs.

   Om Azure AD-principen tillämpar Multi-Factor Authentication för användaren visas följande två dialog rutor.

* Första gången en användare går igenom Multi-Factor Authentication visar systemet en dialog ruta som frågar efter ett mobiltelefon nummer för att skicka textmeddelanden till. Varje meddelande innehåller den *verifierings kod* som användaren måste ange i nästa dialog ruta.

* En dialog ruta som frågar efter en verifierings kod för Multi-Factor Authentication som systemet har skickat till en mobil telefon.

Information om hur du konfigurerar Azure AD för att kräva Multi-Factor Authentication finns i [komma igång med Azure Multi-Factor Authentication i molnet](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud).

Skärm dum par av dessa dialog rutor finns i [Konfigurera Multi-Factor Authentication för SQL Server Management Studio och Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!TIP]
> Du kan söka .NET Framework-API: er på [sidan med .NET API](https://docs.microsoft.com/dotnet/api/)-webbläsar verktyget.
>
> Du kan också söka direkt med den [valfria? term =&lt;Sök värde&gt; parameter](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod).

## <a name="configure-your-c-application-in-the-azure-portal"></a>Konfigurera ditt C# program i Azure Portal

Innan du börjar bör du ha en [Azure SQL Database-Server](sql-database-get-started-portal.md) som skapats och är tillgänglig.

### <a name="register-your-app-and-set-permissions"></a>Registrera din app och ange behörigheter

Om du vill använda Azure AD- C# autentisering måste programmet registreras som ett Azure AD-program. För att registrera en app måste du antingen vara en Azure AD-administratör eller en användare som tilldelats rollen Azure AD *Application Developer* . Mer information om hur du tilldelar roller finns i [tilldela roller administratörer och icke-administratörer till användare med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

När en app-registrering slutförs skapas och visas ett **program-ID**. Ditt program måste innehålla detta ID för att ansluta.

Så här registrerar och anger du nödvändiga behörigheter för ditt program:

1. I Azure Portal väljer du **Azure Active Directory** > **Appregistreringar** > **ny program registrering**.

    ![Appregistrering](media/active-directory-interactive-connect-azure-sql-db/image1.png)

    När appens registrering har skapats genereras och visas värdet för **program-ID** .

    ![App-ID visas](media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. Välj **registrerade app** > -**Inställningar** > **nödvändiga behörigheter** > **Lägg till**.

    ![Behörighets inställningar för registrerad app](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. Välj **nödvändiga behörigheter** > **Lägg till** > **Välj en API** > -**Azure SQL Database**.

    ![Lägg till åtkomst till API: et för Azure SQL Database](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. Välj **API-åtkomst** > **Välj behörigheter** > **delegerade behörigheter**.

    ![Delegera behörigheter till API för Azure SQL Database](media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-sql-database-server"></a>Ange en Azure AD-administratör för din SQL Database-Server

För att C# ditt program ska kunna köras måste en Azure SQL Server-administratör tilldela en Azure AD-administratör för din SQL Database-Server. 

På sidan **SQL Server** väljer du **Active Directory admin** > **set admin**.

Mer information om Azure AD-administratörer och användare för Azure SQL Database finns i skärm bilderna i [Konfigurera och hantera Azure Active Directory autentisering med SQL Database](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server).

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>Lägga till en icke-administratör till en speciell databas (valfritt)

En Azure AD-administratör för en SQL Database-Server kan C# köra exempel programmet. En Azure AD-användare kan köra programmet om de finns i databasen. En Azure AD SQL-administratör eller en Azure AD-användare som redan finns i databasen och som `ALTER ANY USER` har behörighet för databasen kan lägga till en användare.

Du kan lägga till en användare till databasen med SQL [`Create User`](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) -kommandot. Ett exempel är `CREATE USER [<username>] FROM EXTERNAL PROVIDER`.

Mer information finns i [använda Azure Active Directory autentisering för autentisering med SQL Database, hanterad instans eller SQL Data Warehouse](sql-database-aad-authentication.md).

## <a name="new-authentication-enum-value"></a>Uppräknings värde för ny autentisering

C# Exemplet förlitar sig på [`System.Data.SqlClient`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient) namn området. Av särskilt intresse för Multi-Factor Authentication är uppräkningen `SqlAuthenticationMethod`, som har följande värden:

- `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   Använd det här värdet med ett användar namn för Azure AD för att implementera Multi-Factor Authentication. Det här värdet är fokus för den här artikeln. Den ger en interaktiv upplevelse genom att Visa dialog rutor för användar lösen ordet och sedan för autentisering med multifaktorautentisering om Multi-Factor Authentication har angetts för den här användaren. Det här värdet är tillgängligt från och med .NET Framework version 4.7.2.

- `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  Använd det här värdet för ett *federerat* konto. För ett federerat konto är användar namnet känt för Windows-domänen. Den här autentiseringsmetoden stöder inte Multi-Factor Authentication.

- `SqlAuthenticationMethod.ActiveDirectoryPassword`

  Använd det här värdet för autentisering som kräver ett användar namn och lösen ord för Azure AD. Azure SQL Database utför autentiseringen. Den här metoden stöder inte Multi-Factor Authentication.

## <a name="set-c-parameter-values-from-the-azure-portal"></a>Ange C# parameter värden från Azure Portal

För att C# programmet ska kunna köras måste du tilldela rätt värden till statiska fält. Här visas fält med exempel värden. Du kan också se Azure Portal platser där du kan hämta de värden som krävs.

| Statiskt fält namn | Exempelvärde | Var i Azure Portal |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | **SQL-servrar** > **Filtrera efter namn** |
| AzureAD_UserID | "Auser\@ABC.onmicrosoft.com" | **Azure Active Directory** > användareny > **gäst användare** |
| Initial_DatabaseName | "databas" | **SQL Server** > **SQL-databaser** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Azure Active Directory** **Appregistreringar Sök efter**namnprogram- > ID >  >  |
| RedirectUri | ny URI (https://mywebserver.com/"") | **Azure Active Directory** >  > Appregistreringar Sök efternamn > [your-app-Registration] Settings RedirectURIs >  > <br /><br />I den här artikeln är ett giltigt värde bra för RedirectUri, eftersom det inte används här. |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio"></a>Verifiera med SQL Server Management Studio

Innan du kör C# programmet är det en bra idé att kontrol lera att konfigurationen och konfigurationerna är korrekta i SQL Server Management Studio (SSMS). Alla C# program haverier kan sedan begränsas till käll koden.

### <a name="verify-sql-database-firewall-ip-addresses"></a>Verifiera SQL Database brand Väggs-IP-adresser

Kör SSMS från samma dator, i samma byggnad, där du planerar att köra C# programmet. I det här testet är alla **autentiseringsläge** OK. Om det finns någon indikation på att databas serverns brand vägg inte accepterar din IP-adress, se [Azure SQL Database brand Väggs regler på server nivå och databas nivå](sql-database-firewall-configure.md) för hjälp.

### <a name="verify-azure-active-directory-multi-factor-authentication"></a>Verifiera Azure Active Directory Multi-Factor Authentication

Kör SSMS igen, den här gången med autentiseringen inställd på **Active Directory-Universal med MFA-support**. Det här alternativet kräver SSMS version 17,5 eller senare.

Mer information finns i [Konfigurera Multi-Factor Authentication för SSMS och Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!NOTE]
> Om du är gäst användare i databasen måste du också ange Azure AD-domännamnet för databasen: Välj **alternativ** > **AD-domännamn eller klient-ID**. Om du vill hitta domän namnet i Azure Portal väljer du **Azure Active Directory** > **anpassade domän namn**. I C# exempel programmet är det inte nödvändigt att ange ett domän namn.

## <a name="c-code-example"></a>C#kod exempel

Exempel C# programmet använder [Microsoft. IdentityModel. clients. ActiveDirectory](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory) dll-sammansättningen.

Om du vill installera det här paketet väljer du **projekt** > **Hantera NuGet-paket**i Visual Studio. Sök efter och installera **Microsoft. IdentityModel. clients. ActiveDirectory**.

Detta är ett exempel på C# en käll kod.

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

Detta är ett exempel på utdata C# från testet.

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
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska.

- [Get-AzSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)
