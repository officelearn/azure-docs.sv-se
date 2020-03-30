---
title: Alltid krypterat – Azure Key Vault
description: I den här artikeln visas hur du skyddar känsliga data i en SQL-databas med datakryptering med guiden Alltid krypterad i SQL Server Management Studio.
keywords: datakryptering, krypteringsnyckel, molnkryptering
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 006c780aeb3db813c8fdfb5da0b5c13fc4dcfebc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067421"
---
# <a name="always-encrypted-protect-sensitive-data-and-store-encryption-keys-in-azure-key-vault"></a>Alltid krypterad: Skydda känsliga data och lagra krypteringsnycklar i Azure Key Vault

I den här artikeln visas hur du skyddar känsliga data i en SQL-databas med datakryptering med hjälp av [guiden Alltid krypterad](https://msdn.microsoft.com/library/mt459280.aspx) i [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx). Den innehåller också instruktioner som visar hur du lagrar varje krypteringsnyckel i Azure Key Vault.

Always Encrypted är en ny datakrypteringsteknik i Azure SQL Database och SQL Server som hjälper till att skydda känsliga data i vila på servern, under förflyttning mellan klient och server och medan data används. Alltid krypterad säkerställer att känsliga data aldrig visas som klartext i databassystemet. När du har konfigurerat datakryptering kan endast klientprogram eller appservrar som har åtkomst till nycklarna komma åt oformaterade data. Detaljerad information finns [i Alltid krypterad (databasmotor).](https://msdn.microsoft.com/library/mt163865.aspx)

När du har konfigurerat databasen så att den alltid är krypterad skapar du ett klientprogram i C# med Visual Studio för att arbeta med krypterade data.

Följ stegen i den här artikeln och lär dig hur du konfigurerar Alltid krypterad för en Azure SQL-databas. I den här artikeln får du lära dig hur du utför följande uppgifter:

- Använd guiden Alltid krypterad i SSMS för att skapa [alltid krypterade nycklar](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
  - Skapa en [kolumnhuvudnyckel (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
  - Skapa en [kolumnkrypteringsnyckel (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
- Skapa en databastabell och kryptera kolumner.
- Skapa ett program som infogar, markerar och visar data från de krypterade kolumnerna.

## <a name="prerequisites"></a>Krav

För den här självstudien behöver du:

- Ett Azure-konto och prenumeration. Om du inte har en, registrera dig för en [gratis testperiod](https://azure.microsoft.com/pricing/free-trial/).
- [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) version 13.0.700.242 eller senare.
- [.NET Framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) eller senare (på klientdatorn).
- [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
- [Azure PowerShell](/powershell/azure/overview) eller [Azure CLI](/cli/azure/install-azure-cli)

## <a name="enable-your-client-application-to-access-the-sql-database-service"></a>Aktivera klientprogrammet för åtkomst till SQL Database-tjänsten

Du måste aktivera klientprogrammet för åtkomst till SQL Database-tjänsten genom att konfigurera ett AAD-program (Azure Active Directory) och kopiera *program-ID* och *nyckel* som du måste autentisera ditt program.

Följ stegen för att *key* [skapa ett Azure Active Directory-program och tjänsthuvudnamn som kan komma åt resurser](../active-directory/develop/howto-create-service-principal-portal.md). *Application ID*

## <a name="create-a-key-vault-to-store-your-keys"></a>Skapa ett nyckelvalv för att lagra dina nycklar

Nu när klientappen är konfigurerad och du har ditt program-ID är det dags att skapa ett nyckelvalv och konfigurera dess åtkomstprincip så att du och ditt program kan komma åt valvets hemligheter (alltid krypterade nycklar). Behörigheterna *skapa*, *hämta*, *lista*, *verifiering*, *wrapKey*och *verify* *unwrapKey* krävs för att skapa en ny kolumnhuvudnyckel och för att konfigurera kryptering med SQL Server Management Studio.

Du kan snabbt skapa ett nyckelvalv genom att köra följande skript. En detaljerad förklaring av dessa kommandon och mer information om hur du skapar och konfigurerar ett nyckelvalv finns i [Vad är Azure Key Vault?](../key-vault/key-vault-overview.md).

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

> [!IMPORTANT]
> PowerShell Azure Resource Manager (RM) stöds fortfarande av Azure SQL Database, men all framtida utveckling är för Az.Sql-modulen. AzureRM-modulen fortsätter att ta emot buggfixar fram till åtminstone december 2020.  Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är i stort sett identiska. Mer information om deras kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](/powershell/azure/new-azureps-module-az).

```powershell
$subscriptionName = '<subscriptionName>'
$userPrincipalName = '<username@domain.com>'
$applicationId = '<applicationId from AAD application>'
$resourceGroupName = '<resourceGroupName>' # use the same resource group name when creating your SQL Database below
$location = '<datacenterLocation>'
$vaultName = '<vaultName>'

Connect-AzAccount
$subscriptionId = (Get-AzSubscription -SubscriptionName $subscriptionName).Id
Set-AzContext -SubscriptionId $subscriptionId

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $resourceGroupName -Location $location

Set-AzKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName -PermissionsToKeys create,get,wrapKey,unwrapKey,sign,verify,list -UserPrincipalName $userPrincipalName
Set-AzKeyVaultAccessPolicy  -VaultName $vaultName  -ResourceGroupName $resourceGroupName -ServicePrincipalName $applicationId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$subscriptionName = '<subscriptionName>'
$userPrincipalName = '<username@domain.com>'
$applicationId = '<applicationId from AAD application>'
$resourceGroupName = '<resourceGroupName>' # use the same resource group name when creating your SQL Database below
$location = '<datacenterLocation>'
$vaultName = '<vaultName>'

az login
az account set --subscription $subscriptionName

az group create --location $location --name $resourceGroupName

az keyvault create --name $vaultName --resource-group $resourceGroupName --location $location

az keyvault set-policy --name $vaultName --key-permissions create, get, list, sign, unwrapKey, verify, wrapKey --resource-group $resourceGroupName --upn $userPrincipalName
az keyvault set-policy --name $vaultName --key-permissions get, list, sign, unwrapKey, verify, wrapKey --resource-group $resourceGroupName --spn $applicationId
```

* * *

## <a name="create-a-blank-sql-database"></a>Skapa en tom SQL-databas

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Gå till **Skapa en resurs** > **Databaser** > **SQL Database**.
3. Skapa en **tom** databas med namnet **Clinic** på en ny eller befintlig server. Detaljerade anvisningar om hur du skapar en databas i Azure-portalen finns i [din första Azure SQL-databas](sql-database-single-database-get-started.md).

    ![Skapa en tom databas](./media/sql-database-always-encrypted-azure-key-vault/create-database.png)

Du behöver anslutningssträngen senare i självstudien, så när du har skapat databasen bläddrar du till den nya klinikens databas och kopierar anslutningssträngen. Du kan hämta anslutningssträngen när som helst, men det är enkelt att kopiera den i Azure-portalen.

1. Gå till **SQL-databaser** > **Clinic** > **Visa databasanslutningssträngar**.
2. Kopiera anslutningssträngen för **ADO.NET**.

    ![Kopiera anslutningssträngen](./media/sql-database-always-encrypted-azure-key-vault/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>Ansluta till databasen med SSMS

Öppna SSMS och anslut till servern med klinikens databas.

1. Öppna SSMS. (Gå till **Anslut** > **databasmotorn** för att öppna fönstret **Anslut till server** om det inte är öppet.)

2. Ange servernamn och autentiseringsuppgifter. Servernamnet finns på SQL-databasbladet och i anslutningssträngen som du kopierade tidigare. Skriv det fullständiga servernamnet, inklusive *database.windows.net*.

    ![Kopiera anslutningssträngen](./media/sql-database-always-encrypted-azure-key-vault/ssms-connect.png)

Om fönstret **Ny brandväggsregel** öppnas loggar du in på Azure och låter SSMS skapa en ny brandväggsregel åt dig.

## <a name="create-a-table"></a>Skapa en tabell

I det här avsnittet ska du skapa en tabell för att lagra patientdata. Det är inte initialt krypterat - du kommer att konfigurera kryptering i nästa avsnitt.

1. Expandera **databaser**.
2. Högerklicka på **klinikens** databas och klicka på **Ny fråga**.
3. Klistra in följande Transact-SQL (T-SQL) i det nya frågefönstret och **kör** det.

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

## <a name="encrypt-columns-configure-always-encrypted"></a>Kryptera kolumner (konfigurera Alltid krypterad)

SSMS innehåller en guide som hjälper dig att enkelt konfigurera Alltid krypterad genom att ställa in kolumnhuvudnyckeln, kolumnkrypteringsnyckeln och krypterade kolumner åt dig.

1. Expandera **databaser** > **Clinic** > **Tabeller**.
2. Högerklicka på tabellen **Patienter** och välj **Kryptera kolumner** för att öppna guiden Alltid krypterad:

    ![Kryptera kolumner](./media/sql-database-always-encrypted-azure-key-vault/encrypt-columns.png)

Guiden Alltid krypterad innehåller följande avsnitt: **Kolumnval,** **Huvudnyckelkonfiguration,** **Validering**och **Sammanfattning**.

### <a name="column-selection"></a>Kolumnmarkering

Klicka på **Nästa** på sidan **Introduktion** för att öppna sidan **Kolumnval.** På den här sidan väljer du vilka kolumner du vill kryptera, [vilken typ av kryptering och vilken kolumnkrypteringsnyckel (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) som ska användas.

Kryptera **SSN-** och **BirthDate-information** för varje patient. SSN-kolumnen använder deterministisk kryptering, som stöder likhetssökningar, kopplingar och gruppera efter. Kolumnen BirthDate använder randomiserad kryptering, som inte stöder åtgärder.

Ange **krypteringstypen** för SSN-kolumnen till **Deterministisk** och kolumnen Födelsedatum till **Randomiserad**. Klicka på **Nästa**.

![Kryptera kolumner](./media/sql-database-always-encrypted-azure-key-vault/column-selection.png)

### <a name="master-key-configuration"></a>Konfiguration av huvudnyckel

Sidan **Huvudnyckelkonfiguration** är där du ställer in cmk-enheten och väljer den nyckelbutiksleverantör där CMK ska lagras. För närvarande kan du lagra en CMK i Windows-certifikatarkivet, Azure Key Vault eller en maskinvarusäkerhetsmodul (HSM).

Den här självstudien visar hur du lagrar dina nycklar i Azure Key Vault.

1. Välj **Azure Key Vault**.
2. Välj önskat nyckelvalv i listrutan.
3. Klicka på **Nästa**.

![Konfiguration av huvudnyckel](./media/sql-database-always-encrypted-azure-key-vault/master-key-configuration.png)

### <a name="validation"></a>Validering

Du kan kryptera kolumnerna nu eller spara ett PowerShell-skript för att köras senare. För den här självstudien väljer du **Fortsätt för att avsluta nu** och klickar på **Nästa**.

### <a name="summary"></a>Sammanfattning

Kontrollera att alla inställningar är korrekta och klicka på **Slutför** för att slutföra installationen för Alltid krypterad.

![Sammanfattning](./media/sql-database-always-encrypted-azure-key-vault/summary.png)

### <a name="verify-the-wizards-actions"></a>Verifiera guidens åtgärder

När guiden är klar konfigureras databasen för Alltid krypterad. Guiden utförde följande åtgärder:

- Skapade en kolumnnyckel och lagrade den i Azure Key Vault.
- Skapade en kolumnkrypteringsnyckel och lagrade den i Azure Key Vault.
- Konfigurerade de markerade kolumnerna för kryptering. Tabellen Patienter har för närvarande inga data, men alla befintliga data i de valda kolumnerna är nu krypterade.

Du kan verifiera att nycklarna skapas i SSMS genom att expandera **Kliniksäkerhet** > **Security** > **alltid krypterade nycklar**.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Skapa ett klientprogram som fungerar med krypterade data

Nu när Alltid krypterad är konfigurerad kan du skapa ett program som utför *infogningar* och *väljer* på de krypterade kolumnerna.  

> [!IMPORTANT]
> Ditt program måste använda [SqlParameter-objekt](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) när du skickar klartextdata till servern med alltid krypterade kolumner. Om du skickar litterala värden utan att använda SqlParameter-objekt blir det ett undantag.

1. Öppna Visual Studio och skapa ett nytt C# **Console Application** (Visual Studio 2015 och tidigare) eller Console **App (.NET Framework)** (Visual Studio 2017 och senare). Kontrollera att projektet är inställt på **.NET Framework 4.6** eller senare.
2. Namnge projektet **AlwaysEncryptedConsoleAKVApp** och klicka på **OK**.
3. Installera följande NuGet-paket genom att gå till **Tools** > **NuGet Package Manager** > **Package Manager Console**.

Kör dessa två kodrader i Package Manager-konsolen:

   ```powershell
   Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Ändra anslutningssträngen så att den alltid är krypterad

I det här avsnittet beskrivs hur du aktiverar Alltid krypterad i databasanslutningssträngen.

Om du vill aktivera Alltid krypterad måste du lägga till nyckelordet **Kolumnkrypteringsinställning** i **anslutningssträngen**och ställa in det på Aktiverat .

Du kan ställa in detta direkt i anslutningssträngen eller ställa in det med [sqlconnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). Exempelprogrammet i nästa avsnitt visar hur du använder **SqlConnectionStringBuilder**.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Aktivera alltid krypterad i anslutningssträngen

Lägg till följande nyckelord i anslutningssträngen.

   `Column Encryption Setting=Enabled`

### <a name="enable-always-encrypted-with-sqlconnectionstringbuilder"></a>Aktivera alltid krypterad med SqlConnectionStringBuilder

Följande kod visar hur du aktiverar Alltid krypterad genom att ställa in [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) till [Aktiverad](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

```csharp
// Instantiate a SqlConnectionStringBuilder.
SqlConnectionStringBuilder connStringBuilder = new SqlConnectionStringBuilder("replace with your connection string");

// Enable Always Encrypted.
connStringBuilder.ColumnEncryptionSetting = SqlConnectionColumnEncryptionSetting.Enabled;
```

## <a name="register-the-azure-key-vault-provider"></a>Registrera Azure Key Vault-providern
Följande kod visar hur du registrerar Azure Key Vault-providern med ADO.NET-drivrutinen.

```csharp
private static ClientCredential _clientCredential;

static void InitializeAzureKeyVaultProvider() {
    _clientCredential = new ClientCredential(applicationId, clientKey);

    SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider = new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

    Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers = new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

    providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
    SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
}
```

## <a name="always-encrypted-sample-console-application"></a>Alltid krypterat exempelkonsolprogram

Det här exemplet visar hur du:

- Ändra anslutningssträngen så att den alltid är krypterad.
- Registrera Azure Key Vault som programmets nyckelbutiksleverantör.  
- Infoga data i de krypterade kolumnerna.
- Välj en post genom att filtrera efter ett visst värde i en krypterad kolumn.

Ersätt innehållet i *Program.cs* med följande kod. Ersätt anslutningssträngen för den globala connectionString-variabeln på raden som direkt föregår huvudmetoden med din giltiga anslutningssträng från Azure-portalen. Detta är den enda ändring du behöver göra i den här koden.

Kör appen för att se Alltid krypterad i aktion.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data;
using System.Data.SqlClient;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider;

namespace AlwaysEncryptedConsoleAKVApp {
    class Program {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"<connection string from the portal>";
        static string applicationId = @"<application ID from your AAD application>";
        static string clientKey = "<key from your AAD application>";

        static void Main(string[] args) {
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

            InsertPatient(new Patient() {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993")
            });

            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients()) {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now lets locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do {
                Console.WriteLine("Please enter a valid SSN (ex. 999-99-0003):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null) {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }

        private static ClientCredential _clientCredential;

        static void InitializeAzureKeyVaultProvider() {
            _clientCredential = new ClientCredential(applicationId, clientKey);

            SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
              new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

            Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
              new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

            providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
            SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
        }

        public async static Task<string> GetToken(string authority, string resource, string scope) {
            var authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(resource, _clientCredential);

            if (result == null)
                throw new InvalidOperationException("Failed to obtain the access token");
            return result.AccessToken;
        }

        static int InsertPatient(Patient newPatient) {
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

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex) {
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


        static List<Patient> SelectAllPatients() {
            List<Patient> patients = new List<Patient>();

            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));

            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows) {
                        while (reader.Read()) {
                            patients.Add(new Patient() {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex) {
                    throw;
                }
            }

            return patients;
        }

        static Patient SelectPatientBySSN(string ssn) {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows) {
                        while (reader.Read()) {
                            patient = new Patient() {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else {
                        patient = null;
                    }
                }
                catch (Exception ex) {
                    throw;
                }
            }
            return patient;
        }

        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable() {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex) {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
}
```

## <a name="verify-that-the-data-is-encrypted"></a>Kontrollera att data är krypterade

Du kan snabbt kontrollera att de faktiska data på servern är krypterade genom att fråga patientdata med SSMS (med hjälp av din aktuella anslutning där **kolumnkrypteringsinställningen** ännu inte är aktiverad).

Kör följande fråga i klinikens databas.

```sql
SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
```

Du kan se att de krypterade kolumnerna inte innehåller några oformaterade data.

   ![Nytt konsolprogram](./media/sql-database-always-encrypted-azure-key-vault/ssms-encrypted.png)

Om du vill använda SSMS för att komma åt oformaterade data måste du först se till att användaren har rätt behörighet till Azure Key Vault: *hämta*, *ta bort Nyckel*och *verifiera*. Detaljerad information finns i [Skapa och lagra kolumnhuvudnycklar (alltid krypterade).](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted)

Lägg sedan till parametern *Kolumnkryptering=aktiverad* under anslutningen.

1. Högerklicka på servern i **Objektutforskaren** i SSMS och välj **Koppla från**.
2. Klicka på **Anslut** > **databasmotor** för att öppna fönstret **Anslut till server** och klicka på **Alternativ**.
3. Klicka på **Ytterligare anslutningsparametrar** och skriv **kolumnkrypteringsinställning=aktiverad**.

    ![Nytt konsolprogram](./media/sql-database-always-encrypted-azure-key-vault/ssms-connection-parameter.png)

4. Kör följande fråga i klinikens databas.

   ```sql
   SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
   ```

     Du kan nu se klartextdata i de krypterade kolumnerna.
     ![Nytt konsolprogram](./media/sql-database-always-encrypted-azure-key-vault/ssms-plaintext.png)

## <a name="next-steps"></a>Nästa steg

När du har skapat en databas som använder Alltid krypterad kanske du vill göra följande:

- [Rotera och rensa upp dina nycklar](https://msdn.microsoft.com/library/mt607048.aspx).
- [Migrera data som redan är krypterade med Alltid krypterad](https://msdn.microsoft.com/library/mt621539.aspx).

## <a name="related-information"></a>Relaterad information

- [Alltid krypterad (klientutveckling)](https://msdn.microsoft.com/library/mt147923.aspx)
- [Transparent datakryptering](https://msdn.microsoft.com/library/bb934049.aspx)
- [SQL Server-kryptering](https://msdn.microsoft.com/library/bb510663.aspx)
- [Guiden Alltid krypterad](https://msdn.microsoft.com/library/mt459280.aspx)
- [Alltid krypterad blogg](https://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)
