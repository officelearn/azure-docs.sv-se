---
title: 'Alltid krypterad: SQL-databas – Azure Key Vault | Microsoft Docs'
description: Den här artikeln visar hur du skyddar känsliga data i en SQL-databas med datakryptering guiden alltid krypterad i SQL Server Management Studio.
keywords: kryptering av data, krypteringsnyckel, moln-kryptering
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: ''
manager: craigg
ms.date: 10/05/2018
ms.openlocfilehash: e0971b6b9b8460447d383d238843d4bdddc912f7
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49958989"
---
# <a name="always-encrypted-protect-sensitive-data-and-store-encryption-keys-in-azure-key-vault"></a>Alltid krypterad: Skydda känsliga data och lagra krypteringsnycklar i Azure Key Vault

Den här artikeln visar hur du skyddar känsliga data i en SQL-databas med kryptering med hjälp av den [alltid krypterad guiden](https://msdn.microsoft.com/library/mt459280.aspx) i [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx). Den innehåller också anvisningar som visar hur du lagrar varje krypteringsnyckel i Azure Key Vault.

Always är Encrypted en teknik för kryptering av nya data i Azure SQL Database och SQL Server som hjälper dig att skydda känsliga data i vila på-servern under flytt mellan klienten och servern och medan data används. Alltid säkerställer krypterad att känsliga data aldrig visas som oformaterad text i databassystemet. När du har konfigurerat datakryptering endast klientprogram eller app-servrar som har åtkomst till nycklarna kan komma åt data i klartext. Detaljerad information finns i [Always Encrypted (databasmotor)](https://msdn.microsoft.com/library/mt163865.aspx).

När du konfigurerar databasen om du vill använda Always Encrypted, skapar du ett klientprogram i C# med Visual Studio för att arbeta med krypterade data.

Följ stegen i den här artikeln och lär dig hur du ställer in Always Encrypted för en Azure SQL-databas. I den här artikeln får du lära dig hur du utför följande uppgifter:

* Använd guiden alltid krypterad i SSMS för att skapa [Always Encrypted nycklar](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
  * Skapa en [kolumnhuvudnyckeln (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
  * Skapa en [kolumnkrypteringsnyckel (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
* Skapa en databastabell och kryptera kolumner.
* Skapa ett program som infogar väljer och visar data från de krypterade kolumnerna.

## <a name="prerequisites"></a>Förutsättningar
Den här självstudien behöver du:

* Ett Azure-konto och prenumeration. Om du inte har någon kan registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).
* [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) version 13.0.700.242 eller senare.
* [.NET framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) eller senare (på klientdatorn).
* [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* [Azure PowerShell](/powershell/azure/overview), version 1.0 eller senare. Typ **(Get-Module azure - ListAvailable). Version** att se vilken version av PowerShell som du kör.

## <a name="enable-your-client-application-to-access-the-sql-database-service"></a>Aktivera ditt klientprogram att komma åt tjänsten SQL Database
Du måste aktivera ditt klientprogram att komma åt SQL Database-tjänsten genom att skapa ett Azure Active Directory (AAD)-program och kopiera den *program-ID* och *nyckel* som du behöver autentisera ditt program.

Att hämta den *program-ID* och *nyckel*, följer du stegen i [och skapa en Azure Active Directory program och tjänstens huvudnamn som kan komma åt resurser](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-a-key-vault-to-store-your-keys"></a>Skapa ett nyckelvalv för att lagra dina nycklar
Nu när du har program-ID och din klientapp har konfigurerats är det dags att skapa ett nyckelvalv och konfigurera dess åtkomstprincip så att du och ditt program kan komma åt på valvet hemligheter (Always Encrypted nycklarna). Den *skapa*, *hämta*, *lista*, *logga*, *Kontrollera*, *wrapKey*, och *unwrapKey* behörigheter som krävs för att skapa en ny kolumn huvudnyckel och för att konfigurera kryptering med SQL Server Management Studio.

Du kan snabbt skapa ett nyckelvalv genom att köra följande skript. En detaljerad förklaring av dessa cmdletar och mer information om hur du skapar och konfigurerar ett nyckelvalv, se [Kom igång med Azure Key Vault](../key-vault/key-vault-get-started.md).

```powershell
    $subscriptionName = '<your Azure subscription name>'
    $userPrincipalName = '<username@domain.com>'
    $applicationId = '<application ID from your AAD application>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $vaultName = 'AeKeyVault'


    Connect-AzureRmAccount
    $subscriptionId = (Get-AzureRmSubscription -SubscriptionName $subscriptionName).Id
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $resourceGroupName -Location $location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName -PermissionsToKeys create,get,wrapKey,unwrapKey,sign,verify,list -UserPrincipalName $userPrincipalName
    Set-AzureRmKeyVaultAccessPolicy  -VaultName $vaultName  -ResourceGroupName $resourceGroupName -ServicePrincipalName $applicationId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list
```



## <a name="create-a-blank-sql-database"></a>Skapa en tom SQL-databas
1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Gå till **skapa en resurs** > **databaser** > **SQL Database**.
3. Skapa en **tom** databas med namnet **Clinic** på en ny eller befintlig server. För detaljerade instruktioner om hur du skapar en databas i Azure-portalen finns i [din första Azure SQL database](sql-database-get-started-portal.md).
   
    ![Skapa en tom databas](./media/sql-database-always-encrypted-azure-key-vault/create-database.png)

Du behöver anslutningen sträng senare under kursen, så när du har skapat databasen, bläddra till den nya Clinic-databasen och kopiera anslutningssträngen. Du kan hämta anslutningssträngen när som helst, men det är enkelt att kopiera den i Azure-portalen.

1. Gå till **SQL-databaser** > **Clinic** > **visa databasanslutningssträngar**.
2. Kopiera anslutningssträngen för **ADO.NET**.
   
    ![Kopiera anslutningssträngen](./media/sql-database-always-encrypted-azure-key-vault/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>Ansluta till databasen med SSMS
Öppna SSMS och Anslut till servern med Clinic-databasen.

1. Öppna SSMS. (Gå till **Connect** > **Database Engine** att öppna den **Anslut till Server** fönster om det inte är öppen.)
2. Ange servernamn och autentiseringsuppgifter. Namnet på servern kan hittas på bladet SQL database och i strängen som du kopierade tidigare. Ange det fullständiga servernamnet, inklusive *database.windows.net*.
   
    ![Kopiera anslutningssträngen](./media/sql-database-always-encrypted-azure-key-vault/ssms-connect.png)

Om den **ny brandväggsregel** öppnas, logga in på Azure och gör SSMS att skapa en ny brandväggsregel åt dig.

## <a name="create-a-table"></a>Skapa en tabell
I det här avsnittet skapar du en tabell för att lagra Patientdata. Det är inte inledningsvis krypterad – du konfigurerar kryptering i nästa avsnitt.

1. Expandera **databaser**.
2. Högerklicka på den **Clinic** databasen och klicka på **ny fråga**.
3. Klistra in följande Transact-SQL (T-SQL) i nya frågefönstret och **kör** den.

```sql
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
```

## <a name="encrypt-columns-configure-always-encrypted"></a>Kryptera kolumner (Konfigurera Always Encrypted)
SSMS innehåller en guide som hjälper dig att enkelt konfigurera Always Encrypted genom att ställa in den kolumnhuvudnyckeln och kolumnkrypteringsnyckel krypterade kolumner för dig.

1. Expandera **databaser** > **Clinic** > **tabeller**.
2. Högerklicka på den **patienter** och **kryptera kolumner** att öppna guiden alltid krypterad:
   
    ![Kryptera kolumner](./media/sql-database-always-encrypted-azure-key-vault/encrypt-columns.png)

Guiden alltid krypterad innehåller följande avsnitt: **Kolumnurval**, **huvudnyckeln Configuration**, **verifiering**, och **sammanfattning**.

### <a name="column-selection"></a>Kolumnurval
Klicka på **nästa** på den **introduktion** kan du öppna den **Kolumnurval** sidan. På den här sidan kan du väljer vilka kolumner som du vill kryptera, [typen av kryptering, och vilka kolumnkrypteringsnyckel (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) att använda.

Kryptera **SSN** och **födelsedatum** information varje patients sjukhusvistelse. Kolumnen SSN använder deterministisk kryptering, som stöder likhet sökningar, kopplingar och gruppera efter. Kolumnen födelsedatum använder slumpmässig kryptering, som inte stöder funktioner.

Ange den **krypteringstyp** för kolumnen SSN att **Deterministic** och kolumnen födelsedatum att **Randomized**. Klicka på **Nästa**.

![Kryptera kolumner](./media/sql-database-always-encrypted-azure-key-vault/column-selection.png)

### <a name="master-key-configuration"></a>Huvudnyckel konfiguration
Den **huvudnyckeln Configuration** är där du ställer in din CMK och väljer viktiga meddelandearkivet där CMK kommer att lagras. För närvarande kan lagra du en CMK i certifikatarkivet för Windows, Azure Key Vault eller en maskinvarusäkerhetsmodul (HSM).

Den här självstudiekursen visar hur du lagrar dina nycklar i Azure Key Vault.

1. Välj **Azure Key Vault**.
2. Välj önskad nyckelvalvet från den nedrullningsbara listan.
3. Klicka på **Nästa**.

![Huvudnyckel konfiguration](./media/sql-database-always-encrypted-azure-key-vault/master-key-configuration.png)

### <a name="validation"></a>Validering
Du kan kryptera kolumnerna nu eller spara ett PowerShell.skript för att köra senare. Den här självstudien väljer **fortsätter att avsluta nu** och klicka på **nästa**.

### <a name="summary"></a>Sammanfattning
Kontrollera att inställningarna är korrekta och klicka på **Slutför** att slutföra installationen för Always Encrypted.

![Sammanfattning](./media/sql-database-always-encrypted-azure-key-vault/summary.png)

### <a name="verify-the-wizards-actions"></a>Kontrollera i guiden åtgärder
När guiden har slutförts kan har din databas ställts in för Always Encrypted. Guiden utförs följande åtgärder:

* Skapa en huvudnyckel för kolumnen och sparat den i Azure Key Vault.
* Skapat en kolumnkrypteringsnyckel och lagras i Azure Key Vault.
* Konfigurera de markerade kolumnerna för kryptering. Tabellen patienter har för närvarande inga data, men alla befintliga data i de markerade kolumnerna är krypterad.

Du kan kontrollera skapandet av nycklar i SSMS genom att expandera **Clinic** > **Security** > **alltid krypterad nycklar**.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Skapa ett klientprogram som fungerar med krypterade data
Nu när Always Encrypted har konfigurerats kan du skapa ett program som utför *infogar* och *väljer* för krypterade kolumner.  

> [!IMPORTANT]
> Programmet måste använda [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) objekt vid sändning av data i klartext till servern med Always Encrypted kolumner. Skicka litterala värden utan att använda SqlParameter objekt resulterar i ett undantag.
> 
> 

1. Öppna Visual Studio och skapa ett nytt C# **konsolprogram** (Visual Studio 2015 och tidigare) eller **Konsolapp (.NET Framework)** (Visual Studio 2017 och senare). Kontrollera att ditt projekt är inställd **.NET Framework 4.6** eller senare.
2. Ge projektet namnet **AlwaysEncryptedConsoleAKVApp** och klicka på **OK**.
3. Installera följande NuGet-paket genom att gå till **verktyg** > **NuGet-Pakethanteraren** > **Pakethanterarkonsolen**.

Kör följande två rader med kod i Package Manager-konsolen.

```powershell
    Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```


## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Ändra anslutningssträngen för att aktivera Always Encrypted
Det här avsnittet beskrivs hur du aktiverar Always Encrypted i anslutningssträngen för databasen.

Om du vill aktivera Always Encrypted, du måste lägga till den **kolumnen krypteringsinställning** nyckelord till din anslutning sträng och ange den till **aktiverad**.

Du kan ange detta direkt i anslutningssträngen eller du kan ange det med hjälp av [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). Exempelprogrammet i nästa avsnitt visar hur du använder **SqlConnectionStringBuilder**.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Aktivera Always Encrypted i anslutningssträngen
Lägg till följande nyckelord i anslutningssträngen.

    Column Encryption Setting=Enabled


### <a name="enable-always-encrypted-with-sqlconnectionstringbuilder"></a>Aktivera Always Encrypted med SqlConnectionStringBuilder
Följande kod visar hur du aktiverar Always Encrypted genom att ange [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) till [aktiverad](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

```CS
    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;
```

## <a name="register-the-azure-key-vault-provider"></a>Registrera Azure Key Vault-providern
Följande kod visar hur du registrerar Azure Key Vault-providern med ADO.NET-drivrutinen.

```C#
    private static ClientCredential _clientCredential;

    static void InitializeAzureKeyVaultProvider()
    {
       _clientCredential = new ClientCredential(applicationId, clientKey);

       SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
          new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

       Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
          new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

       providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
       SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
    }
```

## <a name="always-encrypted-sample-console-application"></a>Alltid krypterad exempelkonsol-program
Detta exempel visar hur du:

* Ändra anslutningssträngen för att aktivera Always Encrypted.
* Registrera Azure Key Vault som programmets viktiga store-providern.  
* Infoga data i de krypterade kolumnerna.
* Markera en post genom att filtrera efter ett specifikt värde i en krypterad kolumn.

Ersätt innehållet i **Program.cs** med följande kod. Ersätt anslutningssträngen för global connectionString-variabeln i den rad som föregår direkt Main-metoden med giltig anslutningssträngen från Azure-portalen. Det här är den enda ändringen du behöver göra i den här koden.

Kör appen om du vill se Always Encrypted fungerar i praktiken.
```CS
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
        static string applicationId = @"<application ID from your AAD application>";
        static string clientKey = "<key from your AAD application>";


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

            _clientCredential = new ClientCredential(applicationId, clientKey);

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
```


## <a name="verify-that-the-data-is-encrypted"></a>Kontrollera att informationen är krypterad
Du kan snabbt kontrollera att den faktiska data på servern är krypterad genom att fråga data patienter med SSMS (med hjälp av den aktuella anslutningen där **kolumnen krypteringsinställning** har inte aktiverats).

Kör följande fråga för Clinic-databasen.

```sql
    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
```

Du kan se att de krypterade kolumnerna inte innehåller några data i klartext.

   ![Nytt konsolprogram](./media/sql-database-always-encrypted-azure-key-vault/ssms-encrypted.png)

Om du vill använda SSMS på dataåtkomst klartext, måste du först kontrollera att användaren har rätt behörighet till Azure Key Vault: *hämta*, *unwrapKey*, och *Kontrollera*. Detaljerad information finns i [skapa- och huvudnycklarna för Store kolumnen (Always Encrypted)](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted?view=sql-server-2017).

Lägg sedan till den *kolumnen krypteringsinställning = aktiverat* parametern under anslutningen.

1. I SSMS högerklickar du på din server i **Object Explorer** och välj **Disconnect**.
2. Klicka på **Connect** > **Database Engine** att öppna den **Anslut till Server** och klicka på **alternativ**.
3. Klicka på **ytterligare anslutningsparametrar** och skriv **kolumnen krypteringsinställning = aktiverat**.
   
    ![Nytt konsolprogram](./media/sql-database-always-encrypted-azure-key-vault/ssms-connection-parameter.png)
4. Kör följande fråga för Clinic-databasen.

   ```sql
      SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
   ```

     Du kan nu se klartext data i de krypterade kolumnerna.
     ![Nytt konsolprogram](./media/sql-database-always-encrypted-azure-key-vault/ssms-plaintext.png)


## <a name="next-steps"></a>Nästa steg
När du skapar en databas som använder Always Encrypted kan behöva du göra följande:

* [Rotera och rensa dina nycklar](https://msdn.microsoft.com/library/mt607048.aspx).
* [Migrera data som redan är krypterad med Always Encrypted](https://msdn.microsoft.com/library/mt621539.aspx).

## <a name="related-information"></a>Relaterad information
* [Always Encrypted (Klientutveckling)](https://msdn.microsoft.com/library/mt147923.aspx)
* [Transparent datakryptering](https://msdn.microsoft.com/library/bb934049.aspx)
* [SQL Server-kryptering](https://msdn.microsoft.com/library/bb510663.aspx)
* [Alltid krypterad guiden](https://msdn.microsoft.com/library/mt459280.aspx)
* [Alltid krypterad blogg](http://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)

