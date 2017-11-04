---
title: 'Always Encrypted: SQL Database - Azure Key Vault | Microsoft Docs'
description: "Den här artikeln visar hur du skyddar känsliga data i en SQL-databas med datakryptering guiden alltid krypterade i SQL Server Management Studio."
keywords: datakryptering krypteringsnyckeln molnet kryptering
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: cgronlun
ms.assetid: 6ca16644-5969-497b-a413-d28c3b835c9b
ms.service: sql-database
ms.custom: security
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: sstein
ms.openlocfilehash: 4fb189abfaddcf27c8af223773ab0e5fc9dfca14
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="always-encrypted-protect-sensitive-data-in-sql-database-and-store-your-encryption-keys-in-azure-key-vault"></a>Always Encrypted: Skydda känsliga data i SQL-databasen och lagra krypteringsnycklar i Azure Key Vault

Den här artikeln visar hur du skyddar känsliga data i en SQL-databas med data kryptering med hjälp av den [krypteras alltid guiden](https://msdn.microsoft.com/library/mt459280.aspx) i [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx). Den innehåller också anvisningar som visar hur du lagrar varje krypteringsnyckel i Azure Key Vault.

Krypterad är alltid en teknik för kryptering av nya data i Azure SQL Database och SQL Server som skyddar känsliga data i vila på servern, under flytt mellan klienten och servern, och när data används. Krypterad garanterar alltid att känsliga data aldrig visas i klartext i databassystemet. När du har konfigurerat datakryptering endast klientprogram eller appservrar som har tillgång till nycklarna kan komma åt data i klartext. Detaljerad information finns i [Always Encrypted (Database Engine)](https://msdn.microsoft.com/library/mt163865.aspx).

När du har konfigurerat databasen om du vill använda Always Encrypted skapar du ett klientprogram i C# med Visual Studio för att arbeta med krypterade data.

Följ stegen i den här artikeln och lär dig hur du ställer in Always Encrypted för en Azure SQL database. I den här artikeln får du lära dig hur du utför följande uppgifter:

* Använd guiden Always Encrypted i SSMS för att skapa [Always Encrypted nycklar](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
  * Skapa en [kolumnhuvudnyckeln (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
  * Skapa en [kolumnkrypteringsnyckeln (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
* Skapa en databastabell och kryptera kolumner.
* Skapa ett program som infogar, väljer och visar data från de krypterade kolumnerna.

## <a name="prerequisites"></a>Krav
Den här kursen behöver du:

* Ett Azure-konto och prenumeration. Om du inte har någon registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).
* [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) version 13.0.700.242 eller senare.
* [.NET framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) eller senare (på klientdatorn).
* [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* [Azure PowerShell](/powershell/azure/overview), version 1.0 eller senare. Typen **(Get-Module azure - ListAvailable). Version** att se vilken version av PowerShell som du kör.

## <a name="enable-your-client-application-to-access-the-sql-database-service"></a>Aktivera ditt klientprogram att komma åt SQL Database-tjänsten
Du måste aktivera ditt klientprogram att komma åt SQL Database-tjänsten genom att ställa in nödvändig autentisering och hämtning av den *ClientId* och *hemlighet* som du behöver att autentisera din program i följande kod.

1. Öppna den [klassiska Azure-portalen](http://manage.windowsazure.com).
2. Välj **Active Directory** och klicka på Active Directory-instans som programmet ska använda.
3. Klicka på **program**, och klicka sedan på **lägga till**.
4. Skriv ett namn för ditt program (till exempel: *myClientApp*), Välj **WEBBPROGRAM**, och klicka på pilen för att fortsätta.
5. För den **SIGN-ON-URL** och **APP-ID URI** du kan ange en giltig URL (t.ex, *http://myClientApp*) och fortsätta.
6. Klicka på **konfigurera**.
7. Kopiera ditt **klient-ID**. (Du behöver det här värdet i koden senare.)
8. I den **nycklar** väljer **1 års** från den **Markera varaktighet** listrutan. (Du kommer att kopiera nyckeln när du har sparat i steg 13.)
9. Bläddra nedåt och klicka **lägga till program**.
10. Lämna **visa** inställd på **Microsoft Apps** och välj **Microsoft Azure Service Management API**. Klicka på bockmarkeringen för att fortsätta.
11. Välj **åtkomsthantering för Azure-tjänst...**  från den **delegerade behörigheter** listrutan.
12. Klicka på **SPARA**.
13. När det är klar att spara, kopiera nyckelvärdet i den **nycklar** avsnitt. (Du behöver det här värdet i koden senare.)

## <a name="create-a-key-vault-to-store-your-keys"></a>Skapa ett nyckelvalv för att lagra dina nycklar
Nu när du har klient-ID och din klientapp konfigureras är det dags att skapa en nyckelvalvet och konfigurera dess åtkomstprincip så att du och ditt program kan komma åt på valvet hemligheter (Always Encrypted nycklarna). Den *skapa*, *hämta*, *lista*, *logga*, *Kontrollera*, *wrapKey*, och *unwrapKey* behörigheter som krävs för att skapa en ny kolumnhuvudnyckel och konfigurera kryptering med SQL Server Management Studio.

Du kan snabbt skapa en nyckelvalv genom att köra följande skript. En detaljerad förklaring av dessa cmdletar och mer information om hur du skapar och konfigurerar ett nyckelvalv finns [Kom igång med Azure Key Vault](../key-vault/key-vault-get-started.md).

    $subscriptionName = '<your Azure subscription name>'
    $userPrincipalName = '<username@domain.com>'
    $clientId = '<client ID that you copied in step 7 above>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $vaultName = 'AeKeyVault'


    Login-AzureRmAccount
    $subscriptionId = (Get-AzureRmSubscription -SubscriptionName $subscriptionName).Id
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $resourceGroupName -Location $location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName -PermissionsToKeys create,get,wrapKey,unwrapKey,sign,verify,list -UserPrincipalName $userPrincipalName
    Set-AzureRmKeyVaultAccessPolicy  -VaultName $vaultName  -ResourceGroupName $resourceGroupName -ServicePrincipalName $clientId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list




## <a name="create-a-blank-sql-database"></a>Skapa en tom SQL-databas
1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Gå till **nya** > **Data + lagring** > **SQL-databas**.
3. Skapa en **tom** databas med namnet **kurs** på en ny eller befintlig server. Detaljerade anvisningar om hur du skapar en databas i Azure portal finns [första Azure SQL database](sql-database-get-started-portal.md).
   
    ![Skapa en tom databas](./media/sql-database-always-encrypted-azure-key-vault/create-database.png)

Du behöver anslutningen sträng senare under kursen, så när du har skapat databasen, bläddra till den nya e-kurs databasen och kopiera anslutningssträngen. Du kan hämta anslutningssträngen när som helst, men det är lätt att kopiera den i Azure-portalen.

1. Gå till **SQL-databaser** > **kurs** > **visa databasanslutningssträngar**.
2. Kopiera anslutningssträngen för **ADO.NET**.
   
    ![Kopiera anslutningssträngen](./media/sql-database-always-encrypted-azure-key-vault/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>Ansluta till databasen med SSMS
Öppna SSMS och ansluta till servern med kurs-databasen.

1. Öppna SSMS. (Gå till **Anslut** > **databasmotorn** att öppna den **Anslut till Server** om den inte är öppen.)
2. Ange servernamn och autentiseringsuppgifter. Namnet på servern kan hittas på bladet SQL-databasen och i anslutningssträngen du kopierade tidigare. Fullständiga servernamnet, inklusive *database.windows.net*.
   
    ![Kopiera anslutningssträngen](./media/sql-database-always-encrypted-azure-key-vault/ssms-connect.png)

Om den **ny brandväggsregel** öppnas, logga in på Azure och låter SSMS skapa en ny brandväggsregel för dig.

## <a name="create-a-table"></a>Skapa en tabell
I det här avsnittet skapar du en tabell för att lagra Patientdata. Det är inte ursprungligen krypterade--du vill konfigurera kryptering i nästa avsnitt.

1. Expandera **databaser**.
2. Högerklicka på den **kurs** databasen och klicka på **ny fråga**.
3. Klistra in följande Transact-SQL (T-SQL) i nytt frågefönster och **Execute** den.

        CREATE TABLE [dbo].[Patients](
         [PatientId] [int] IDENTITY(1,1),
         [SSN] [char](11) NOT NULL,
         [FirstName] [nvarchar](50) NULL,
         [LastName] [nvarchar](50) NULL,
         [MiddleName] [nvarchar](50) NULL,
         [StreetAddress] [nvarchar](50) NULL,
         [City] [nvarchar](50) NULL,
         [ZipCode] [char](5) NULL,
         [State] [char](2) NULL,
         [BirthDate] [date] NOT NULL
         PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
         GO


## <a name="encrypt-columns-configure-always-encrypted"></a>Kryptera kolumner (Konfigurera Always Encrypted)
SSMS innehåller en guide som hjälper dig att enkelt konfigurera Always Encrypted genom att ställa in kolumnhuvudnyckeln, kolumnkrypteringsnyckeln och krypterade kolumner för dig.

1. Expandera **databaser** > **kurs** > **tabeller**.
2. Högerklicka på den **patienter** tabell och välj **kryptera kolumner** att öppna guiden Always Encrypted:
   
    ![Kryptera kolumner](./media/sql-database-always-encrypted-azure-key-vault/encrypt-columns.png)

Guiden Always Encrypted innehåller följande avsnitt: **kolumnen**, **huvudnyckeln Configuration**, **validering**, och **sammanfattning**.

### <a name="column-selection"></a>Kolumnen
Klicka på **nästa** på den **introduktion** kan du öppna den **kolumnen** sidan. På den här sidan väljer du vilka kolumner som du vill kryptera, [typen av kryptering, och vilka kolumnkrypteringsnyckeln (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) ska användas.

Kryptera **SSN** och **födelsedatum** information för varje tålamod. Kolumnen SSN använder deterministiska kryptering, som stöder likheten sökningar, kopplingar och gruppera efter. Kolumnen födelsedatum använder slumpmässig kryptering, som inte stöder åtgärder.

Ange den **krypteringstyp** för SSN kolumnen som **Deterministic** och födelsedatum kolumnen till **Randomized**. Klicka på **Nästa**.

![Kryptera kolumner](./media/sql-database-always-encrypted-azure-key-vault/column-selection.png)

### <a name="master-key-configuration"></a>Huvudnyckeln konfiguration
Den **huvudnyckeln Configuration** är där du ställer in din CMK och välj KeyStore-providern där CMK ska lagras. För närvarande kan du lagra en CMK i Windows certifikatarkiv, Azure Key Vault eller en maskinvarusäkerhetsmodul (HSM).

Den här kursen visar hur du lagrar dina nycklar i Azure Key Vault.

1. Välj **Azure Key Vault**.
2. Välj önskad nyckelvalvet från den nedrullningsbara listan.
3. Klicka på **Nästa**.

![Huvudnyckeln konfiguration](./media/sql-database-always-encrypted-azure-key-vault/master-key-configuration.png)

### <a name="validation"></a>Validering
Du kan kryptera kolumnerna nu eller sparar ett PowerShell-skript och köra den senare. Den här kursen väljer **fortsätta att avsluta nu** och på **nästa**.

### <a name="summary"></a>Sammanfattning
Kontrollera att inställningarna är korrekta och klicka på **Slutför** att slutföra installationen för Always Encrypted.

![Sammanfattning](./media/sql-database-always-encrypted-azure-key-vault/summary.png)

### <a name="verify-the-wizards-actions"></a>Kontrollera i guiden åtgärder
När guiden har slutförts kan har databasen konfigurerats för Always Encrypted. Guiden utförs följande åtgärder:

* Skapa en huvudnyckel i kolumnen och lagras i Azure Key Vault.
* Skapa en kolumnkrypteringsnyckel och lagras i Azure Key Vault.
* Konfigurera de markerade kolumnerna för kryptering. Tabellen patienter har för närvarande inga data, men alla befintliga data i de markerade kolumnerna är krypterad.

Du kan verifiera att skapa nycklar i SSMS genom att expandera **kurs** > **säkerhet** > **alltid krypterade nycklar**.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Skapa ett klientprogram som fungerar med krypterade data
Nu när Always Encrypted har konfigurerats, kan du skapa ett program som utför *infogar* och *väljer* för krypterade kolumner.  

> [!IMPORTANT]
> Programmet måste använda [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) objekt när skickas i klartext data till servern med Always Encrypted kolumner. Skicka litterala värden utan att använda SqlParameter objekt resulterar i ett undantag.
> 
> 

1. Öppna Visual Studio och skapa en ny C# **konsolprogram** (Visual Studio 2015 och tidigare) eller **Konsolapp (.NET Framework)** (Visual Studio 2017 och senare). Kontrollera att projektet har angetts till **.NET Framework 4.6** eller senare.
2. Namnge projektet **AlwaysEncryptedConsoleAKVApp** och på **OK**.
3. Installera följande NuGet-paket genom att gå till **verktyg** > **NuGet Package Manager** > **Pakethanterarkonsolen**.

Kör dessa två rader med kod i Package Manager-konsolen.

    Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory



## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Ändra anslutningssträngen för att aktivera Always Encrypted
Det här avsnittet beskrivs hur du aktiverar Always Encrypted i anslutningssträngen för databasen.

Om du vill aktivera Always Encrypted, måste du lägga till den **Kolumnkrypteringsinställningen** nyckelord i anslutningen sträng och värdet **aktiverad**.

Du kan ange detta direkt i anslutningssträngen eller kan du ändra det med hjälp av [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). Exempelprogrammet i nästa avsnitt visar hur du använder **SqlConnectionStringBuilder**.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Aktivera Always Encrypted i anslutningssträngen
Lägg till följande nyckelord i anslutningssträngen.

    Column Encryption Setting=Enabled


### <a name="enable-always-encrypted-with-sqlconnectionstringbuilder"></a>Aktivera Always Encrypted med SqlConnectionStringBuilder
Följande kod visar hur du aktiverar Always Encrypted genom att ange [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) till [aktiverad](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;

## <a name="register-the-azure-key-vault-provider"></a>Registrera Azure Key Vault-providern
Följande kod visar hur du registrerar Azure Key Vault-providern med ADO.NET-drivrutinen.

    private static ClientCredential _clientCredential;

    static void InitializeAzureKeyVaultProvider()
    {
       _clientCredential = new ClientCredential(clientId, clientSecret);

       SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
          new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

       Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
          new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

       providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
       SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
    }



## <a name="always-encrypted-sample-console-application"></a>Alltid krypterat exempelkonsol-program
Det här exemplet visar hur du:

* Ändra anslutningssträngen för att aktivera Always Encrypted.
* Registrera Azure Key Vault som programmets KeyStore-providern.  
* Infoga data i de krypterade kolumnerna.
* Markera en post genom att filtrera efter ett visst värde i en krypterad kolumn.

Ersätt innehållet i **Program.cs** med följande kod. Ersätt anslutningssträngen för globala connectionString variabeln i rad som föregår direkt Main-metod med en giltig anslutningssträng från Azure-portalen. Det här är den enda förändringen som du behöver göra i den här koden.

Kör appen att se Always Encrypted fungerar.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Data;
    using System.Data.SqlClient;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider;

    namespace AlwaysEncryptedConsoleAKVApp
    {
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"<connection string from the portal>";
        static string clientId = @"<client id from step 7 above>";
        static string clientSecret = "<key from step 13 above>";


        static void Main(string[] args)
        {
            InitializeAzureKeyVaultProvider();

            Console.WriteLine("Signed in as: " + _clientCredential.ClientId);

            Console.WriteLine("Original connection string copied from the Azure portal:");
            Console.WriteLine(connectionString);

            // Create a SqlConnectionStringBuilder.
            SqlConnectionStringBuilder connStringBuilder =
                new SqlConnectionStringBuilder(connectionString);

            // Enable Always Encrypted for the connection.
            // This is the only change specific to Always Encrypted
            connStringBuilder.ColumnEncryptionSetting =
                SqlConnectionColumnEncryptionSetting.Enabled;

            Console.WriteLine(Environment.NewLine + "Updated connection string with Always Encrypted enabled:");
            Console.WriteLine(connStringBuilder.ConnectionString);

            // Update the connection string with a password supplied at runtime.
            Console.WriteLine(Environment.NewLine + "Enter server password:");
            connStringBuilder.Password = Console.ReadLine();


            // Assign the updated connection string to our global variable.
            connectionString = connStringBuilder.ConnectionString;


            // Delete all records to restart this demo app.
            ResetPatientsTable();

            // Add sample data to the Patients table.
            Console.Write(Environment.NewLine + "Adding sample patient data to the database...");

            InsertPatient(new Patient()
            {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993")
            });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now lets locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 999-99-0003):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null)
            {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else
            {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }


        private static ClientCredential _clientCredential;

        static void InitializeAzureKeyVaultProvider()
        {

            _clientCredential = new ClientCredential(clientId, clientSecret);

            SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
              new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

            Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
              new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

            providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
            SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
        }

        public async static Task<string> GetToken(string authority, string resource, string scope)
        {
            var authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(resource, _clientCredential);

            if (result == null)
                throw new InvalidOperationException("Failed to obtain the access token");
            return result.AccessToken;
        }

        static int InsertPatient(Patient newPatient)
        {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate])
     VALUES (@SSN, @FirstName, @LastName, @BirthDate);";

            SqlCommand sqlCmd = new SqlCommand(sqlCmdText);


            SqlParameter paramSSN = new SqlParameter(@"@SSN", newPatient.SSN);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            SqlParameter paramFirstName = new SqlParameter(@"@FirstName", newPatient.FirstName);
            paramFirstName.DbType = DbType.String;
            paramFirstName.Direction = ParameterDirection.Input;

            SqlParameter paramLastName = new SqlParameter(@"@LastName", newPatient.LastName);
            paramLastName.DbType = DbType.String;
            paramLastName.Direction = ParameterDirection.Input;

            SqlParameter paramBirthDate = new SqlParameter(@"@BirthDate", newPatient.BirthDate);
            paramBirthDate.SqlDbType = SqlDbType.Date;
            paramBirthDate.Direction = ParameterDirection.Input;

            sqlCmd.Parameters.Add(paramSSN);
            sqlCmd.Parameters.Add(paramFirstName);
            sqlCmd.Parameters.Add(paramLastName);
            sqlCmd.Parameters.Add(paramBirthDate);

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex)
                {
                    returnValue = 1;
                    Console.WriteLine("The following error was encountered: ");
                    Console.WriteLine(ex.Message);
                    Console.WriteLine(Environment.NewLine + "Press Enter key to exit");
                    Console.ReadLine();
                    Environment.Exit(0);
                }
            }
            return returnValue;
        }


        static List<Patient> SelectAllPatients()
        {
            List<Patient> patients = new List<Patient>();


            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));


            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patients.Add(new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }

            return patients;
        }


        static Patient SelectPatientBySSN(string ssn)
        {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);


            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patient = new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else
                    {
                        patient = null;
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }
            return patient;
        }


        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable()
        {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex)
                {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient
    {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
    }



## <a name="verify-that-the-data-is-encrypted"></a>Kontrollera att informationen är krypterad.
Du kan snabbt kontrollera att den faktiska data på servern krypteras med datafrågor patienter med SSMS (med hjälp av den aktuella anslutningen där **Kolumnkrypteringsinställningen** har inte aktiverats).

Kör följande fråga på kurs-databasen.

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Du kan se att krypterade kolumner inte innehåller några data i klartext.

   ![Nytt konsolprogram](./media/sql-database-always-encrypted-azure-key-vault/ssms-encrypted.png)

Om du vill använda SSMS för att komma åt data i klartext, kan du lägga till den *Kolumnkrypteringsinställningen = aktiverat* parameter för anslutningen.

1. Högerklicka på din server i i SSMS, **Object Explorer** och välj **frånkoppling**.
2. Klicka på **Anslut** > **databasmotorn** att öppna den **Anslut till Server** och klicka på **alternativ**.
3. Klicka på **ytterligare anslutningsparametrar** och skriv **Kolumnkrypteringsinställningen = aktiverat**.
   
    ![Nytt konsolprogram](./media/sql-database-always-encrypted-azure-key-vault/ssms-connection-parameter.png)
4. Kör följande fråga på kurs-databasen.
   
        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
   
     Du kan nu se data i klartext i krypterade kolumner.

    ![Nytt konsolprogram](./media/sql-database-always-encrypted-azure-key-vault/ssms-plaintext.png)


## <a name="next-steps"></a>Nästa steg
När du har skapat en databas som använder Always Encrypted kanske du vill göra följande:

* [Rotera och rensa dina nycklar](https://msdn.microsoft.com/library/mt607048.aspx).
* [Migrera data som redan har krypterats med Always Encrypted](https://msdn.microsoft.com/library/mt621539.aspx).

## <a name="related-information"></a>Relaterad information
* [Always Encrypted (client-utveckling)](https://msdn.microsoft.com/library/mt147923.aspx)
* [Transparent datakryptering](https://msdn.microsoft.com/library/bb934049.aspx)
* [SQL Server-kryptering](https://msdn.microsoft.com/library/bb510663.aspx)
* [Alltid krypterat guiden](https://msdn.microsoft.com/library/mt459280.aspx)
* [Alltid krypterade blogg](http://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)

