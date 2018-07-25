---
title: ActiveDirectoryInteractive ansluter till SQL | Microsoft Docs
description: C#-kod exempel med förklaringar för att ansluta till Azure SQL Database med hjälp av SqlAuthenticationMethod.ActiveDirectoryInteractive läget.
services: sql-database
author: GithubMirek
manager: craigg
ms.service: sql-database
ms.custom: active directory
ms.topic: conceptual
ms.date: 04/06/2018
ms.author: MirekS
ms.reviewer: GeneMi
ms.openlocfilehash: 3d6eb70b3ce9072dc2c51220af89549022b5dacf
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2018
ms.locfileid: "39238276"
---
# <a name="use-activedirectoryinteractive-mode-to-connect-to-azure-sql-database"></a>Använda ActiveDirectoryInteractive läge för att ansluta till Azure SQL Database

Den här artikeln innehåller ett körbara C# kodexempel som ansluter till din Microsoft Azure SQL Database. C#-programmet använder interaktiva läget för autentisering, som stöder Azure AD multifaktorautentisering (MFA). Ett anslutningsförsök kan exempelvis innehålla en Verifieringskod som skickas till din mobiltelefon.

Mer information om MFA-stöd för SQL-verktyg som finns i [Azure Active Directory-stöd i SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory).




## <a name="sqlauthenticationmethod-activedirectoryinteractive-enum-value"></a>SqlAuthenticationMethod. ActiveDirectoryInteractive enum-värde

Från och med .NET Framework version 4.7.2, vilken uppräkningen [ **SqlAuthenticationMethod** ](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) har ett nytt värde **. ActiveDirectoryInteractive**. När den används av ett klientprogram C#, gör att systemet använder det interaktiva läget för Azure AD stöder MFA för att autentisera till Azure SQL Database enum-värde. Den användare som kör programmet sedan ser dialogrutorna för följande:

1. En dialogruta som visar ett Azure AD-användarnamn och som ber om lösenordet för Azure AD-användare.
    - Den här dialogrutan visas inte om inget lösenord krävs. Inget lösenord krävs om användarens domän är federerad med Azure AD.

    Om MFA införts på användaren av den princip som angetts i Azure AD, visas följande dialogrutorna bredvid.

2. Endast visas första gången användaren upplever MFA-scenariot, en ytterligare dialogruta. Dialogrutan begär ett mobiltelefonnummer textmeddelanden skickas. Varje meddelande som innehåller den *Verifieringskod* som användaren måste ange i nästa dialogruta.

3. En annan dialogruta som frågar efter Verifieringskod MFA som systemet har skickats till en mobiltelefon.

Information om hur du konfigurerar Azure AD för att kräva MFA finns i [komma igång med Azure Multi-Factor Authentication i molnet](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud).

Skärmdumpar av dessa dialogrutor, se [konfigurera Multi-Factor authentication för SQL Server Management Studio och Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!TIP]
> Vår allmänna söksidan för alla typer av .NET Framework API: er finns på länken nedan för att våra praktiska **.NET API-webbläsare** verktyget:
>
> [https://docs.microsoft.com/dotnet/api/](https://docs.microsoft.com/dotnet/api/)
>
> Genom att lägga till namnet på den valfria sist **? termen =** parametern söksidan kan ha vår resultatet klart och väntar på för oss:
>
> [https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod)


## <a name="preparations-for-c-by-using-the-azure-portal"></a>Förberedelser för C# med hjälp av Azure portal

Vi förutsätter att du redan har en [Azure SQL Database-server som skapade](sql-database-get-started-portal.md) och är tillgängliga.


### <a name="a-create-an-app-registration"></a>A. Skapa en appregistrering

Om du vill använda Azure AD-autentisering, ditt C#-klientprogram måste ange ett GUID som en *clientId* när programmet försöker ansluta. Du har slutfört en appregistrering genereras och visas GUID i Azure-portalen märkta som **program-ID**. Navigeringsstegen är följande:

1. Azure-portalen &gt; **Azure Active Directory** &gt; **appregistrering**

    ![Appregistrering](media\active-directory-interactive-connect-azure-sql-db\sshot-create-app-registration-b20.png)

2. Den **program-ID** värde genereras och visas.

    ![App-ID som visas](media\active-directory-interactive-connect-azure-sql-db\sshot-application-id-app-regis-mk49.png)

3. **Registrerad app** &gt; **inställningar** &gt; **nödvändiga behörigheter** &gt; **Lägg till**

    ![Behörighetsinställningar för registrerad app](media\active-directory-interactive-connect-azure-sql-db\sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

4. **Nödvändiga behörigheter** &gt; **Lägg till API-åtkomst** &gt; **Välj en API** &gt; **Azure SQL Database**

    ![Lägg till åtkomst till API: et för Azure SQL Database](media\active-directory-interactive-connect-azure-sql-db\sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

5. **API-åtkomst** &gt; **Välj behörigheter** &gt; **delegerade behörigheter**

    ![Delegeringsbehörigheter till API: et för Azure SQL Database](media\active-directory-interactive-connect-azure-sql-db\sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)


### <a name="b-set-azure-ad-admin-on-your-sql-database-server"></a>B. Ange Azure AD-administratören för din SQL Database-server

Varje Azure SQL Database-server har en egen logisk SQL-server i Azure AD. Vårt C# scenario, måste du ange en Azure AD-administratör för Azure SQL-servern.

1. **SQL Server** &gt; **Active Directory-administratör** &gt; **konfigurera administratör**

    - Mer information om Azure AD-administratörer och användare för Azure SQL Database finns i skärmbilderna i [konfigurera och hantera Azure Active Directory-autentisering med SQL Database](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server), i ett avsnitt **etablera en Azure Active Directory-administratör för din Azure SQL Database-server**.


### <a name="c-prepare-an-azure-ad-user-to-connect-to-a-specific-database"></a>C. Förbereda Azure AD-användare att ansluta till en specifik databas

Du kan lägga till en användare som har åtkomst till en viss databas i Azure AD som är specifik för din Azure SQL Database-server.

Mer information finns i [Använd Azure Active Directory-autentisering för autentisering med SQL Database Managed Instance eller SQL Data Warehouse](sql-database-aad-authentication.md).


### <a name="d-add-a-non-admin-user-to-azure-ad"></a>D. Lägg till en icke-administratörsanvändare till Azure AD

Azure AD-administratör för SQL Database-server kan användas för att ansluta till SQL Database-servern. Mer allmän fall är dock att lägga till en icke-administratörsanvändare med Azure AD. När den icke-administratörsanvändaren används för att ansluta, anropas MFA-sekvensen om MFA har införts på den här användaren av Azure AD.




## <a name="azure-active-directory-authentication-library-adal"></a>Azure Active Directory Authentication Library (ADAL)

C#-program som förlitar sig på namnområdet **Microsoft.IdentityModel.Clients.ActiveDirectory**. Klasserna för det här namnområdet är i sammansättningen med samma namn.

- Använd NuGet för att ladda ned och installera ADAL-sammansättningen.
    - [https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)

- Lägg till en referens till sammansättningen att stödja en kompilering av C#-programmet.




## <a name="sqlauthenticationmethod-enum"></a>SqlAuthenticationMethod enum

En namnområden som C#-exempel som förlitar sig på är **System.Data.SqlClient**. Är särskilt intressanta i uppräkningen **SqlAuthenticationMethod**. Den här uppräkningen har följande värden:

- **SqlAuthenticationMethod.ActiveDirectory*interaktiv ***:&nbsp; Använd det här med ett användarnamn för Azure AD för att uppnå multifaktorautentisering MFA.
    - Det här värdet är fokus i den här artikeln. Den genererar en interaktiv upplevelse genom att visa dialogrutor för användarens lösenord och sedan för MFA-verifieringen om MFA har införts på den här användaren.
    - Det här värdet är tillgängliga från och med .NET Framework version 4.7.2.

- **SqlAuthenticationMethod.ActiveDirectory*integrerad ***:&nbsp; Använd detta för en *federerad* konto. Användarnamnet är ett federerat konto kända till Windows-domän. Den här metoden har inte stöd för MFA.

- **SqlAuthenticationMethod.ActiveDirectory*lösenord ***:&nbsp; använder detta för autentisering som kräver en Azure AD-användare och användarens lösenord. Azure SQL Database utför autentiseringen. Den här metoden har inte stöd för MFA.




## <a name="prepare-c-parameter-values-from-the-azure-portal"></a>Förbereda C#-parametervärden från Azure portal

För en lyckad körning av C#-programmet, måste du tilldela rätt värden för följande statiska fält. Dessa statiska fält fungerar som parametrar i programmet. Fält som visas här med pretend värden. Filterläget visas är platser i Azure-portalen från där du kan hämta rätt värden:


| Statisk fältnamn | Föreställ dig värde | Var i Azure-portalen |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | ”my-favorit-sqldb-svr.database.windows.net” | **SQL-servrar** &gt; **filtrera efter namn** |
| AzureAD_UserID | "user9@abc.onmicrosoft.com" | **Azure Active Directory** &gt; **användaren** &gt; **ny gästanvändare** |
| Initial_DatabaseName | ”huvud” | **SQL-servrar** &gt; **SQL-databaser** |
| ClientApplicationID | ”a94f9c62-97fe-4d19-b06d-111111111111” | **Azure Active Directory** &gt; **appregistreringar**<br /> &nbsp; &nbsp; &gt; **Sök efter namn** &gt; **program-ID** |
| RedirectUri | ny Uri (”https://bing.com/”) | **Azure Active Directory** &gt; **appregistreringar**<br /> &nbsp; &nbsp; &gt; **Sök efter namn** &gt; *[Your-App-regis]* &gt;<br /> &nbsp; &nbsp; **Inställningar för** &gt; **RedirectURIs**<br /><br />För den här artikeln är ett giltigt värde bra för RedirectUri. Värdet används inte i vårt fall verkligen. |
| &nbsp; | &nbsp; | &nbsp; |


Beroende på ditt specifika scenario kanske du inte behöver värden alla parametrar i tabellen ovan.




## <a name="run-ssms-to-verify"></a>Kör SSMS för att verifiera

Det är bra att köra SQL Server Management Studio (SSMS) innan du kör C#-programmet. SSMS kör verifierar att olika konfigurationer är korrekta. Eventuella fel i C#-programmet kan sedan vara valvinformation till bara källkoden.


#### <a name="verify-sql-database-firewall-ip-addresses"></a>Verifiera IP-adresser för SQL Database-brandvägg

Kör SSMS från samma dator, i samma byggnad, att du senare kör C#-programmet. Du kan använda beroende på vilket som **autentisering** läge som du anser är den enklaste. Om det inte finns något indikerar att serverbrandväggen databas inte tar emot din IP-adress, du kan åtgärda detta enligt [Azure SQL Database-servernivå och databasnivå brandväggsregler](sql-database-firewall-configure.md).


#### <a name="verify-multi-factor-authentication-mfa-for-azure-ad"></a>Verifiera multifaktorautentisering (MFA) för Azure AD

Kör SSMS igen den här gången med **autentisering** inställd **Active Directory - Universal med stöd för MFA**. För det här alternativet måste du ha SSMS 17,5 eller senare.

Mer information finns i [konfigurera Multi-Factor authentication för SSMS och Azure AD](sql-database-ssms-mfa-authentication-configure.md).




## <a name="c-code-example"></a>C#-kodexempel

För att kompilera den här C#-exemplet, måste du lägga till en referens till sammansättningen DLL-fil med namnet **Microsoft.IdentityModel.Clients.ActiveDirectory**.


#### <a name="reference-documentation"></a>Referensdokumentation

- **System.Data.SqlClient** namnområde:
    - Sökning:&nbsp; [https://docs.microsoft.com/dotnet/api/?term=System.Data.SqlClient](https://docs.microsoft.com/dotnet/api/?term=System.Data.SqlClient)
    - Direct:&nbsp; [System.Data.Client](https://docs.microsoft.com/dotnet/api/system.data.sqlclient)

- **Microsoft.IdentityModel.Clients.ActiveDirectory** namnområde:
    - Sökning:&nbsp; [https://docs.microsoft.com/dotnet/api/?term=Microsoft.IdentityModel.Clients.ActiveDirectory](https://docs.microsoft.com/dotnet/api/?term=Microsoft.IdentityModel.Clients.ActiveDirectory)
    - Direct:&nbsp; [Microsoft.IdentityModel.Clients.ActiveDirectory](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory)


#### <a name="c-source-code-in-two-parts"></a>C#-källkod, i två delar

&nbsp;

```csharp

using System;    // C# ,  part 1 of 2.

// Add a reference to assembly:  Microsoft.IdentityModel.Clients.ActiveDirectory.DLL
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
        static public string Az_SQLDB_svrName = "<YOUR VALUE HERE>";
        static public string AzureAD_UserID = "<YOUR VALUE HERE>";
        static public string Initial_DatabaseName = "master";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<YOUR VALUE HERE>";
        static public readonly Uri RedirectUri = new Uri("<YOUR VALUE HERE>");

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

```

&nbsp;

#### <a name="second-half-of-the-c-program"></a>Andra hälften av C#-program

C#-programmet är uppdelat i två kodblock för bättre visning. Klistra in två kodblock tillsammans för att köra programmet.

&nbsp;

```csharp

    // C# ,  part 2 of 2 ,  to concatenate below part 1.

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

&nbsp;


## <a name="next-steps"></a>Nästa steg

- [Get-AzureRmSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlserveractivedirectoryadministrator)

