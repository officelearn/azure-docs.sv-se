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
ms.date: 01/25/2019
manager: craigg
ms.openlocfilehash: def50aecbcf9186af9d0b9c781c3141ad2dcee59
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55753681"
---
# <a name="connect-to-azure-sql-database-with-active-directory-mfa"></a>Ansluta till Azure SQL Database med Active Directory MFA

Den här artikeln innehåller en C# program som ansluter till din Microsoft Azure SQL Database. Interaktivt läge-autentisering, vilket stöder används [Azure Active Directory (AD) multifaktorautentisering (MFA)](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).

Mer information om MFA-stöd för SQL-verktyg som finns i [Azure Active Directory-stöd i SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory).

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Multi-Factor authentication för Azure SQL Database

Från och med .NET Framework version 4.7.2, vilken uppräkningen [ `SqlAuthenticationMethod` ](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) har ett nytt värde - `ActiveDirectoryInteractive`. I en klient C# programmet, uppräkningsvärdet gör att systemet använder det interaktiva läget för Azure AD stöder MFA för att ansluta till en Azure SQL Database. Den användare som kör programmet får dialogrutorna följande:

1. En dialogruta som visar ett Azure AD-användarnamn och frågar efter användarens lösenord.

   Om användarens domän är federerad med Azure AD, visas inte den här dialogrutan när inga lösenord krävs.

   Om Azure AD-princip ålägger användaren MFA, visas två dialogrutorna.

2. Första gången en användare går igenom MFA, visas en dialogruta att skicka textmeddelanden till ditt mobilnummer. Varje meddelande som innehåller den *Verifieringskod* som användaren måste ange i nästa dialogruta.

3. En dialogruta som ber om en MFA-Verifieringskod som systemet har skickats till en mobiltelefon.

Information om hur du konfigurerar Azure AD för att kräva MFA finns i [komma igång med Azure Multi-Factor Authentication i molnet](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud).

Skärmdumpar av dessa dialogrutor, se [konfigurera Multi-Factor authentication för SQL Server Management Studio och Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!TIP]
> Du kan söka API: er i .NET Framework med vår **.NET API-webbläsare** verktyget sidan:
>
> [https://docs.microsoft.com/dotnet/api/](https://docs.microsoft.com/dotnet/api/)
>
> Du kan också söka direkt med det valfria **? termen =&lt;Sök värdet&gt;**  parameter:
>
> [https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod)

## <a name="configure-your-c-application-in-the-azure-portal"></a>Konfigurera din C# program i Azure portal

Innan du börjar måste du ha en [Azure SQL Database-server](sql-database-get-started-portal.md) skapats och är tillgänglig.

### <a name="register-your-app-and-set-permissions"></a>Registrera din app och ange behörigheter


Du använder Azure AD-autentisering, din C# programmet har registrera som en AD-program. Om du vill registrera en app, måste du vara antingen en AD-administratör eller en användare som tilldelats AD *programutvecklare* roll. Mer information om hur du tilldelar roller finns i [tilldela användare med Azure Active Directory rollerna Administratör och icke-administratör.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)

 Du har slutfört en appregistrering genereras och visas i en **program-ID**. Programmet måste ange följande ID för att ansluta.


Att registrera och ange nödvändiga behörigheter för ditt program:

1. Azure-portalen > **Azure Active Directory** > **appregistreringar** > **ny programregistrering**

    ![Appregistrering](media/active-directory-interactive-connect-azure-sql-db/image1.png)

    När appregistreringen har skapats kan den **program-ID** värde genereras och visas.

    ![App-ID som visas](media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. **Registrerad app** > **inställningar** > **nödvändiga behörigheter** > **Lägg till**

    ![Behörighetsinställningar för registrerad app](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. **Nödvändiga behörigheter** > **Lägg till** > **Välj en API** > **Azure SQL Database**

    ![Lägg till åtkomst till API: et för Azure SQL Database](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. **API-åtkomst** > **Välj behörigheter** > **delegerade behörigheter**

    ![Delegeringsbehörigheter till API: et för Azure SQL Database](media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-sql-database-server"></a>Ange en Azure AD-administratör för din SQL Database-server


För din C# program som ska köras, en Azure SQL server-administratören måste tilldela en Azure AD-administratör för Azure SQL-servern. 

 * **SQL Server** > **Active Directory-administratör** > **konfigurera administratör**

Mer information om Azure AD-administratörer och användare för Azure SQL Database finns i skärmbilderna i [konfigurera och hantera Azure Active Directory-autentisering med SQL Database](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server), i ett avsnitt **etablera en Azure Active Directory-administratör för din Azure SQL Database-server**.

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>Lägg till en icke-administratörsanvändare till en viss databas (valfritt)

En Azure AD-administratör för en SQL Database-server kan köra den C# exempelprogrammet. Azure AD-användare kan köra programmet om de finns i databasen. En Azure AD-SQL-administratör eller Azure AD-användare som redan finns i databasen och har den `ALTER ANY USER` behörighet på databasen kan lägga till en användare.

Du kan lägga till en användare till databasen med SQL [ `Create User` ](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=sql-server-2017) kommando. Till exempel `CREATE USER [<username>] FROM EXTERNAL PROVIDER`.

Mer information finns i [Använd Azure Active Directory-autentisering för autentisering med SQL Database Managed Instance eller SQL Data Warehouse](sql-database-aad-authentication.md).

## <a name="new-authentication-enum-value"></a>Ny autentisering enum-värde

Den C# exempel förlitar sig på den [ `System.Data.SqlClient` ](https://docs.microsoft.com/dotnet/api/system.data.sqlclient) namnområde. Är särskilt intressanta för multi-Factor authentication i uppräkningen `SqlAuthenticationMethod`, som har följande värden:

- `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   Använd det här värdet med ett användarnamn för Azure AD för att implementera MFA. Det här värdet är fokus i den här artikeln. Den genererar en interaktiv upplevelse genom att visa dialogrutor för användarens lösenord och sedan för MFA-verifieringen om MFA har införts på den här användaren. Det här värdet är tillgängliga från och med .NET Framework version 4.7.2.

- `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  Använd det här värdet för en *federerad* konto. Användarnamnet är ett federerat konto kända till Windows-domän. Den här autentiseringsmetoden stöder inte MFA.

- `SqlAuthenticationMethod.ActiveDirectoryPassword`

  Använd det här värdet för autentisering som kräver en Azure AD-användarnamn och lösenord. Azure SQL Database gör autentiseringen. Den här metoden stöder inte MFA.

## <a name="set-c-parameter-values-from-the-azure-portal"></a>Ange C# parametervärden från Azure portal

För den C# program för att köra, måste du tilldela statiska fält rätt värden. Här visas fälten med exempelvärden. Filterläget visas är de Azure-portalen platser där du kan hämta de nödvändiga värdena:

| Statisk fältnamn | Exempelvärde | Var i Azure-portalen |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | **SQL-servrar** > **filtrera efter namn** |
| AzureAD_UserID | "auser@abc.onmicrosoft.com" | **Azure Active Directory** > **användaren** > **ny gästanvändare** |
| Initial_DatabaseName | "myDatabase" | **SQL-servrar** > **SQL-databaser** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Azure Active Directory** > **appregistreringar** > **Sök efter namn** > **program-ID** |
| RedirectUri | ny Uri (”https://mywebserver.com/”) | **Azure Active Directory** > **appregistreringar** > **Sök efter namn** > *[Your-App-regis]*  >  **Inställningar** > **RedirectURIs**<br /><br />I den här artikeln är ett giltigt värde bra för RedirectUri, eftersom den inte användas här. |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio-ssms"></a>Verifiera med SQL Server Management Studio (SSMS)

Innan du kör den C# programmet, är det en bra idé att kontrollera att din installation och konfiguration är korrekt i SSMS. Alla C# programmet fel kan sedan begränsas till källkoden.

### <a name="verify-sql-database-firewall-ip-addresses"></a>Verifiera IP-adresser för SQL Database-brandvägg

Kör SSMS från samma dator, i samma byggnad, där du planerar att köra den C# program. För det här testet alla **autentisering** läge är okej. Om det inte finns något indikerar att serverbrandväggen databas inte tar emot din IP-adress, se [Azure SQL Database-servernivå och databasnivå brandväggsregler](sql-database-firewall-configure.md) om du behöver hjälp.

### <a name="verify-azure-active-directory-mfa"></a>Verifiera Azure Active Directory MFA

Kör SSMS igen den här gången med **autentisering** inställd **Active Directory - Universal med stöd för MFA**. Det här alternativet kräver SSMS 17,5 eller senare.

Mer information finns i [konfigurera Multi-Factor authentication för SSMS och Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!NOTE]
> Om du är en gästanvändare i databasen kan du också behöva tillhandahålla AD-domännamn för databasen – **alternativ** > **AD-domän eller klient-ID**. För att hitta domännamnet i Azure portal, Välj **Azure Active Directory** > **anpassade domännamn**. I den C# exempelprogrammet, vilket ger ett domännamn inte är nödvändigt.

## <a name="c-code-example"></a>C#-kodexempel

I exempel C# program som förlitar sig på den [ *Microsoft.IdentityModel.Clients.ActiveDirectory* ](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory) DLL-sammansättningen.

Om du vill installera det här paketet i Visual Studio, Välj **projekt** > **hantera NuGet-paket**. Sök efter och installera **Microsoft.IdentityModel.Clients.ActiveDirectory**.

### <a name="c-source-code"></a>C#källkod

```csharp

using System;

// Reference to AD authentication assembly
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
    } // EOClass Program .

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2 .
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
    } // EOClass ActiveDirectoryAuthProvider .
} // EONamespace.  End of entire program source code.

```

&nbsp;

#### <a name="actual-test-output-from-c"></a>Faktiska testet av utdata från C#

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

- [Få information om en Azure AD-administratör för SQL Server](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlserveractivedirectoryadministrator)

