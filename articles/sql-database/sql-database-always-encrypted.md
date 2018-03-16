---
title: 'Always Encrypted: Azure SQL Database - Windows certifikatarkiv | Microsoft Docs'
description: "Den här artikeln visar hur du skyddar känsliga data i en SQL-databas med databaskryptering med hjälp av alltid krypteras guiden i SQL Server Management Studio (SSMS). Den visar också hur du lagrar krypteringsnycklarna i Windows certifikatarkiv."
keywords: "kryptera data, sql-kryptering, databaskryptering, känsliga data krypteras alltid"
services: sql-database
author: stevestein
manager: jhubbard
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 03/02/2017
ms.author: sstein
ms.openlocfilehash: 1bb3f6bd3b4bfc1546e992734a85921248eff8fd
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="always-encrypted-protect-sensitive-data-in-sql-database-and-store-your-encryption-keys-in-the-windows-certificate-store"></a>Always Encrypted: Skydda känsliga data i SQL-databasen och lagra krypteringsnycklar i Windows certifikatarkiv

Den här artikeln visar hur du skyddar känsliga data i en SQL-databas med databaskryptering med hjälp av den [krypteras alltid guiden](https://msdn.microsoft.com/library/mt459280.aspx) i [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx). Den visar också hur du lagrar krypteringsnycklarna i Windows certifikatarkiv.

Krypterat är alltid en teknik för kryptering av nya data i Azure SQL Database och SQL Server som hjälper till att skydda känsliga data i vila på servern, under flytt mellan klienten och servern medan data att säkerställa att känsliga data aldrig visas i klartext i databassystem. När du krypterar data endast klientprogram eller appservrar som har tillgång till nycklarna kan komma åt data i klartext. Detaljerad information finns i [Always Encrypted (Database Engine)](https://msdn.microsoft.com/library/mt163865.aspx).

När du har konfigurerat databasen om du vill använda Always Encrypted, skapar du ett klientprogram i C# med Visual Studio för att arbeta med krypterade data.

Följ stegen i den här artikeln lär du dig hur du ställer in Always Encrypted för en Azure SQL database. I den här artikeln får du lära dig hur du utför följande uppgifter:

* Använd guiden Always Encrypted i SSMS för att skapa [alltid krypterade nycklar](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
  * Skapa en [kolumnen Master Key (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
  * Skapa en [Kolumnkrypteringsnyckeln (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
* Skapa en databastabell och kryptera kolumner.
* Skapa ett program som infogar, väljer och visar data från de krypterade kolumnerna.

## <a name="prerequisites"></a>Förutsättningar
Den här kursen behöver du:

* Ett Azure-konto och prenumeration. Om du inte har någon registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).
* [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) version 13.0.700.242 eller senare.
* [.NET framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) eller senare (på klientdatorn).
* [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).

## <a name="create-a-blank-sql-database"></a>Skapa en tom SQL-databas
1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på **skapar du en resurs** > **Data + lagring** > **SQL-databas**.
3. Skapa en **tom** databas med namnet **kurs** på en ny eller befintlig server. Detaljerade instruktioner om hur du skapar en databas i Azure portal finns [första Azure SQL database](sql-database-get-started-portal.md).
   
    ![Skapa en tom databas](./media/sql-database-always-encrypted/create-database.png)

Anslutningssträngen måste senare under kursen. När databasen har skapats kan gå till den nya e-kurs databasen och kopiera anslutningssträngen. Du kan hämta anslutningssträngen när som helst, men det är lätt att kopiera den när du är i Azure-portalen.

1. Klicka på **SQL-databaser** > **kurs** > **visa databasanslutningssträngar**.
2. Kopiera anslutningssträngen för **ADO.NET**.
   
    ![Kopiera anslutningssträngen](./media/sql-database-always-encrypted/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>Ansluta till databasen med SSMS
Öppna SSMS och ansluta till servern med kurs-databasen.

1. Öppna SSMS. (Klicka på **Anslut** > **databasmotorn** att öppna den **Anslut till Server** om den inte är öppen).
2. Ange servernamn och autentiseringsuppgifter. Namnet på servern kan hittas på bladet SQL-databasen och i anslutningssträngen du kopierade tidigare. Ange fullständig server namn inklusive *database.windows.net*.
   
    ![Kopiera anslutningssträngen](./media/sql-database-always-encrypted/ssms-connect.png)

Om den **ny brandväggsregel** öppnas, logga in på Azure och låter SSMS skapa en ny brandväggsregel för dig.

## <a name="create-a-table"></a>Skapa en tabell
I det här avsnittet skapar du en tabell för att lagra Patientdata. Det här är en vanlig tabell ursprungligen--du vill konfigurera kryptering i nästa avsnitt.

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
SSMS innehåller en guide för att enkelt konfigurera Always Encrypted genom att ställa in CMK, CEK och krypterade kolumner för dig.

1. Expandera **databaser** > **kurs** > **tabeller**.
2. Högerklicka på den **patienter** tabell och välj **kryptera kolumner** att öppna guiden Always Encrypted:
   
    ![Kryptera kolumner](./media/sql-database-always-encrypted/encrypt-columns.png)

Guiden Always Encrypted innehåller följande avsnitt: **kolumnen**, **huvudnyckeln Configuration** (CMK) **validering**, och **sammanfattning**.

### <a name="column-selection"></a>Kolumnen
Klicka på **nästa** på den **introduktion** kan du öppna den **kolumnen** sidan. På den här sidan väljer du vilka kolumner som du vill kryptera, [typen av kryptering, och vilka kolumnkrypteringsnyckeln (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) ska användas.

Kryptera **SSN** och **födelsedatum** information för varje tålamod. Den **SSN** kolumn använder deterministiska kryptering, som stöder likheten sökningar, kopplingar och gruppera efter. Den **födelsedatum** kolumn använder slumpmässig kryptering, som inte stöder åtgärder.

Ange den **krypteringstyp** för den **SSN** kolumnen till **Deterministic** och **födelsedatum** kolumnen till **Randomized**. Klicka på **Nästa**.

![Kryptera kolumner](./media/sql-database-always-encrypted/column-selection.png)

### <a name="master-key-configuration"></a>Huvudnyckeln konfiguration
Den **huvudnyckeln Configuration** är där du ställer in din CMK och välj KeyStore-providern där CMK ska lagras. För närvarande kan du lagra en CMK i Windows certifikatarkiv, Azure Key Vault eller en maskinvarusäkerhetsmodul (HSM). Den här kursen visar hur du lagrar dina nycklar i Windows certifikatarkiv.

Kontrollera att **Windows certifikatarkiv** är markerad och klicka på **nästa**.

![Huvudnyckeln konfiguration](./media/sql-database-always-encrypted/master-key-configuration.png)

### <a name="validation"></a>Validering
Du kan kryptera kolumnerna nu eller sparar ett PowerShell-skript och köra den senare. Den här kursen väljer **fortsätta att avsluta nu** och på **nästa**.

### <a name="summary"></a>Sammanfattning
Kontrollera att inställningarna är korrekta och klicka på **Slutför** att slutföra installationen för Always Encrypted.

![Sammanfattning](./media/sql-database-always-encrypted/summary.png)

### <a name="verify-the-wizards-actions"></a>Kontrollera i guiden åtgärder
När guiden har slutförts kan har databasen konfigurerats för Always Encrypted. Guiden utförs följande åtgärder:

* Skapa en CMK.
* Skapa en CEK.
* Konfigurera de markerade kolumnerna för kryptering. Din **patienter** tabellen har för närvarande inga data, men alla befintliga data i de markerade kolumnerna är krypterad.

Du kan verifiera att skapa nycklar i SSMS genom att gå till **kurs** > **säkerhet** > **alltid krypterade nycklar**. Du kan nu se de nya nycklarna som genereras av guiden.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Skapa ett klientprogram som fungerar med krypterade data
Nu när Always Encrypted har konfigurerats, kan du skapa ett program som utför *infogar* och *väljer* för krypterade kolumner. Om du vill köra exempelprogrammet, måste du köra den på samma dator där du körde guiden Always Encrypted. Om du vill köra programmet på en annan dator, måste du distribuera din Always Encrypted certifikat till datorn som kör klientappen.  

> [!IMPORTANT]
> Programmet måste använda [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) objekt när skickas i klartext data till servern med Always Encrypted kolumner. Skicka litterala värden utan att använda SqlParameter objekt resulterar i ett undantag.
> 
> 

1. Öppna Visual Studio och skapa ett nytt C#-konsolprogram. Kontrollera att projektet har angetts till **.NET Framework 4.6** eller senare.
2. Namnge projektet **AlwaysEncryptedConsoleApp** och på **OK**.

![Nytt konsolprogram](./media/sql-database-always-encrypted/console-app.png)

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Ändra anslutningssträngen för att aktivera Always Encrypted
Det här avsnittet beskrivs hur du aktiverar Always Encrypted i anslutningssträngen för databasen. Du ändrar konsolprogram som du skapade i nästa avsnitt, ”Always Encrypted exempelkonsol-program”.

Om du vill aktivera Always Encrypted, måste du lägga till den **Kolumnkrypteringsinställningen** nyckelord i anslutningen sträng och värdet **aktiverad**.

Du kan ange detta direkt i anslutningssträngen eller kan du ändra det med hjälp av en [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). Exempelprogrammet i nästa avsnitt visar hur du använder **SqlConnectionStringBuilder**.

> [!NOTE]
> Det här är den enda förändringen som krävs i ett klientprogram som är specifika för Always Encrypted. Om du har ett befintligt program som lagrar sin anslutningssträng externt (det vill säga i en config-fil), kanske du kan aktivera Always Encrypted utan att ändra någon kod.
> 
> 

### <a name="enable-always-encrypted-in-the-connection-string"></a>Aktivera Always Encrypted i anslutningssträngen
Lägg till följande nyckelord i anslutningssträngen:

    Column Encryption Setting=Enabled


### <a name="enable-always-encrypted-with-a-sqlconnectionstringbuilder"></a>Aktivera alltid krypteras med en SqlConnectionStringBuilder
Följande kod visar hur du aktiverar Always Encrypted genom att ange den [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) till [aktiverad](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;



## <a name="always-encrypted-sample-console-application"></a>Alltid krypterat exempelkonsol-program
Det här exemplet visar hur du:

* Ändra anslutningssträngen för att aktivera Always Encrypted.
* Infoga data i de krypterade kolumnerna.
* Markera en post genom att filtrera efter ett visst värde i en krypterad kolumn.

Ersätt innehållet i **Program.cs** med följande kod. Ersätt anslutningssträngen för globala connectionString variabeln i rad direkt ovanför Main-metoden med en giltig anslutningssträng från Azure-portalen. Det här är den enda förändringen som du behöver göra i den här koden.

Kör appen att se Always Encrypted fungerar.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Data;
    using System.Data.SqlClient;

    namespace AlwaysEncryptedConsoleApp
    {
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"Replace with your connection string";

        static void Main(string[] args)
        {
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
                SSN = "999-99-0001", FirstName = "Orlando", LastName = "Gee", BirthDate = DateTime.Parse("01/04/1964") });
            InsertPatient(new Patient() {
                SSN = "999-99-0002", FirstName = "Keith", LastName = "Harris", BirthDate = DateTime.Parse("06/20/1977") });
            InsertPatient(new Patient() {
                SSN = "999-99-0003", FirstName = "Donna", LastName = "Carreras", BirthDate = DateTime.Parse("02/09/1973") });
            InsertPatient(new Patient() {
                SSN = "999-99-0004", FirstName = "Janet", LastName = "Gates", BirthDate = DateTime.Parse("08/31/1985") });
            InsertPatient(new Patient() {
                SSN = "999-99-0005", FirstName = "Lucy", LastName = "Harrington", BirthDate = DateTime.Parse("05/06/1993") });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now let's locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 123-45-6789):");
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
Du kan snabbt kontrollera att krypteras faktiska data på servern genom att fråga den **patienter** data med SSMS. (Använda den aktuella anslutningen där kolumnkrypteringsinställningen ännu inte har aktiverats.)

Kör följande fråga på kurs-databasen.

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Du kan se att krypterade kolumner inte innehåller några data i klartext.

   ![Nytt konsolprogram](./media/sql-database-always-encrypted/ssms-encrypted.png)

Om du vill använda SSMS för att komma åt data i klartext, kan du lägga till den **Kolumnkrypteringsinställningen = aktiverat** parameter för anslutningen.

1. Högerklicka på din server i i SSMS, **Object Explorer**, och klicka sedan på **frånkoppling**.
2. Klicka på **Anslut** > **databasmotorn** att öppna den **Anslut till Server** fönstret och klicka sedan på **alternativ**.
3. Klicka på **ytterligare anslutningsparametrar** och skriv **Kolumnkrypteringsinställningen = aktiverat**.
   
    ![Nytt konsolprogram](./media/sql-database-always-encrypted/ssms-connection-parameter.png)
4. Kör följande fråga den **kurs** databas.
   
        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
   
     Du kan nu se data i klartext i krypterade kolumner.

    ![Nytt konsolprogram](./media/sql-database-always-encrypted/ssms-plaintext.png)



> [!NOTE]
> Om du ansluter med SSMS (eller klienter) från en annan dator har inte åtkomst till krypteringsnycklarna och kommer inte att dekryptera data.
> 
> 

## <a name="next-steps"></a>Nästa steg
När du har skapat en databas som använder Always Encrypted kanske du vill göra följande:

* Kör det här exemplet från en annan dator. Det har inte åtkomst till krypteringsnycklarna, så att den inte har åtkomst till data i klartext och kan inte köras.
* [Rotera och rensa dina nycklar](https://msdn.microsoft.com/library/mt607048.aspx).
* [Migrera data som redan har krypterats med Always Encrypted](https://msdn.microsoft.com/library/mt621539.aspx).
* [Distribuera Always Encrypted certifikat till andra klientdatorer](https://msdn.microsoft.com/library/mt723359.aspx#Anchor_1) (se avsnittet ”att göra certifikat tillgängligt till program och-användare”).

## <a name="related-information"></a>Relaterad information
* [Always Encrypted (client-utveckling)](https://msdn.microsoft.com/library/mt147923.aspx)
* [Transparent datakryptering](https://msdn.microsoft.com/library/bb934049.aspx)
* [SQL Server-kryptering](https://msdn.microsoft.com/library/bb510663.aspx)
* [Alltid krypterade guiden](https://msdn.microsoft.com/library/mt459280.aspx)
* [Alltid krypterade blogg](http://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)

