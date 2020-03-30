---
title: Alltid krypterat – Windows-certifikatarkiv
description: I den här artikeln visas hur du skyddar känsliga data i en SQL-databas med databaskryptering med hjälp av guiden Alltid krypterad i SQL Server Management Studio (SSMS). Den visar också hur du lagrar krypteringsnycklarna i Windows-certifikatarkivet.
keywords: kryptera data, sql-kryptering, databaskryptering, känsliga data, Alltid krypterad
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviwer: ''
ms.date: 03/08/2019
ms.openlocfilehash: 82c3c3274a8a9d66019ce906ee7be47cedac7470
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822048"
---
# <a name="always-encrypted-protect-sensitive-data-and-store-encryption-keys-in-the-windows-certificate-store"></a>Alltid krypterad: Skydda känsliga data och lagra krypteringsnycklar i Windows-certifikatarkivet

I den här artikeln visas hur du skyddar känsliga data i en SQL-databas med databaskryptering med hjälp av [guiden Alltid krypterad](https://msdn.microsoft.com/library/mt459280.aspx) i [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx). Den visar också hur du lagrar krypteringsnycklarna i Windows-certifikatarkivet.

Always Encrypted är en ny datakrypteringsteknik i Azure SQL Database och SQL Server som hjälper till att skydda känsliga data i vila på servern, under förflyttning mellan klient och server, och medan data används, se till att känsliga data aldrig visas som klartext i databassystemet. När du har krypterat data kan endast klientprogram eller appservrar som har åtkomst till nycklarna komma åt oformaterade data. Detaljerad information finns [i Alltid krypterad (databasmotor).](https://msdn.microsoft.com/library/mt163865.aspx)

När du har konfigurerat databasen så att den alltid är krypterad skapar du ett klientprogram i C# med Visual Studio för att arbeta med krypterade data.

Följ stegen i den här artikeln för att lära dig hur du konfigurerar Alltid krypterad för en Azure SQL-databas. I den här artikeln får du lära dig hur du utför följande uppgifter:

* Använd guiden Alltid krypterad i SSMS för att skapa [alltid krypterade nycklar](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
  * Skapa en [kolumnnyckel (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
  * Skapa en [kolumnkrypteringsnyckel (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
* Skapa en databastabell och kryptera kolumner.
* Skapa ett program som infogar, markerar och visar data från de krypterade kolumnerna.

## <a name="prerequisites"></a>Krav

För den här självstudien behöver du:

* Ett Azure-konto och prenumeration. Om du inte har en, registrera dig för en [gratis testperiod](https://azure.microsoft.com/pricing/free-trial/).
* [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) version 13.0.700.242 eller senare.
* [.NET Framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) eller senare (på klientdatorn).
* [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).

## <a name="create-a-blank-sql-database"></a>Skapa en tom SQL-databas

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på **Skapa en resursdata** > +**lagrings-SQL-databas****Data + Storage** > .
3. Skapa en **tom** databas med namnet **Clinic** på en ny eller befintlig server. Detaljerade instruktioner om hur du skapar en databas i Azure-portalen finns i [din första Azure SQL-databas](sql-database-single-database-get-started.md).

    ![Skapa en tom databas](./media/sql-database-always-encrypted/create-database.png)

Du behöver anslutningssträngen senare i självstudien. När databasen har skapats går du till den nya klinikens databas och kopierar anslutningssträngen. Du kan hämta anslutningssträngen när som helst, men det är enkelt att kopiera den när du är i Azure-portalen.

1. Klicka på **SQL-databaser** > **Clinic** > **Visa databasanslutningssträngar**.
2. Kopiera anslutningssträngen för **ADO.NET**.

    ![Kopiera anslutningssträngen](./media/sql-database-always-encrypted/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>Ansluta till databasen med SSMS

Öppna SSMS och anslut till servern med klinikens databas.

1. Öppna SSMS. (Klicka på **Anslut** > **databasmotor** för att öppna fönstret **Anslut till server** om det inte är öppet).
2. Ange servernamn och autentiseringsuppgifter. Servernamnet finns på SQL-databasbladet och i anslutningssträngen som du kopierade tidigare. Skriv in hela servernamnet inklusive *database.windows.net*.

    ![Kopiera anslutningssträngen](./media/sql-database-always-encrypted/ssms-connect.png)

Om fönstret **Ny brandväggsregel** öppnas loggar du in på Azure och låter SSMS skapa en ny brandväggsregel åt dig.

## <a name="create-a-table"></a>Skapa en tabell

I det här avsnittet ska du skapa en tabell för att lagra patientdata. Detta kommer att vara en normal tabell inledningsvis - du kommer att konfigurera kryptering i nästa avsnitt.

1. Expandera **databaser**.
2. Högerklicka på **klinikens** databas och klicka på **Ny fråga**.
3. Klistra in följande Transact-SQL (T-SQL) i det nya frågefönstret och **kör** det.

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

## <a name="encrypt-columns-configure-always-encrypted"></a>Kryptera kolumner (konfigurera Alltid krypterad)

SSMS innehåller en guide för att enkelt konfigurera Alltid krypterad genom att ställa in CMK, CEK och krypterade kolumner åt dig.

1. Expandera **databaser** > **Clinic** > **Tabeller**.
2. Högerklicka på tabellen **Patienter** och välj **Kryptera kolumner** för att öppna guiden Alltid krypterad:

    ![Kryptera kolumner](./media/sql-database-always-encrypted/encrypt-columns.png)

Guiden Alltid krypterad innehåller följande avsnitt: **Kolumnval,** CMK **(Huvudnyckelkonfiguration),** **Validering**och **Sammanfattning**.

### <a name="column-selection"></a>Kolumnmarkering

Klicka på **Nästa** på sidan **Introduktion** för att öppna sidan **Kolumnval.** På den här sidan väljer du vilka kolumner du vill kryptera, [vilken typ av kryptering och vilken kolumnkrypteringsnyckel (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) som ska användas.

Kryptera **SSN-** och **BirthDate-information** för varje patient. **SSN-kolumnen** använder deterministisk kryptering, som stöder likhetssökningar, kopplingar och gruppera efter. Kolumnen **BirthDate** använder randomiserad kryptering, som inte stöder åtgärder.

Ange **krypteringstypen** för **SSN-kolumnen** till **Deterministisk** och kolumnen **Födelsedatum** till **Randomiserad**. Klicka på **Nästa**.

![Kryptera kolumner](./media/sql-database-always-encrypted/column-selection.png)

### <a name="master-key-configuration"></a>Konfiguration av huvudnyckel

Sidan **Huvudnyckelkonfiguration** är där du ställer in cmk-enheten och väljer den nyckelbutiksleverantör där CMK ska lagras. För närvarande kan du lagra en CMK i Windows-certifikatarkivet, Azure Key Vault eller en maskinvarusäkerhetsmodul (HSM). Den här självstudien visar hur du lagrar nycklarna i Windows-certifikatarkivet.

Kontrollera att **Windows-certifikatarkivet** är markerat och klicka på **Nästa**.

![Konfiguration av huvudnyckel](./media/sql-database-always-encrypted/master-key-configuration.png)

### <a name="validation"></a>Validering

Du kan kryptera kolumnerna nu eller spara ett PowerShell-skript för att köras senare. För den här självstudien väljer du **Fortsätt för att avsluta nu** och klickar på **Nästa**.

### <a name="summary"></a>Sammanfattning

Kontrollera att alla inställningar är korrekta och klicka på **Slutför** för att slutföra installationen för Alltid krypterad.

![Sammanfattning](./media/sql-database-always-encrypted/summary.png)

### <a name="verify-the-wizards-actions"></a>Verifiera guidens åtgärder

När guiden är klar konfigureras databasen för Alltid krypterad. Guiden utförde följande åtgärder:

* Skapade en CMK.
* Skapade en CEK.
* Konfigurerade de markerade kolumnerna för kryptering. Tabellen **Patienter** har för närvarande inga data, men alla befintliga data i de valda kolumnerna är nu krypterade.

Du kan verifiera att nycklarna skapas i SSMS genom att gå till **Clinic** > **Security** > **Always Encrypted Keys**. Nu kan du se de nya nycklarna som guiden har genererat.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Skapa ett klientprogram som fungerar med krypterade data

Nu när Alltid krypterad är konfigurerad kan du skapa ett program som utför *infogningar* och *väljer* på de krypterade kolumnerna. Om du vill köra exempelprogrammet måste du köra det på samma dator där guiden Alltid krypterad kördes. Om du vill köra programmet på en annan dator måste du distribuera alltid krypterade certifikat till den dator som kör klientappen.  

> [!IMPORTANT]
> Ditt program måste använda [SqlParameter-objekt](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) när du skickar klartextdata till servern med alltid krypterade kolumner. Om du skickar litterala värden utan att använda SqlParameter-objekt blir det ett undantag.

1. Öppna Visual Studio och skapa ett nytt C#-konsolprogram. Kontrollera att projektet är inställt på **.NET Framework 4.6** eller senare.
2. Namnge projektet **AlwaysEncryptedConsoleApp** och klicka på **OK**.

![Nytt konsolprogram](./media/sql-database-always-encrypted/console-app.png)

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Ändra anslutningssträngen så att den alltid är krypterad

I det här avsnittet beskrivs hur du aktiverar Alltid krypterad i databasanslutningssträngen. Du kommer att ändra konsolappen som du just skapade i nästa avsnitt, "Alltid krypterat exempelkonsolprogram".

Om du vill aktivera Alltid krypterad måste du lägga till nyckelordet **Kolumnkrypteringsinställning** i **anslutningssträngen**och ställa in det på Aktiverat .

Du kan ställa in detta direkt i anslutningssträngen eller ställa in det med hjälp av en [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). Exempelprogrammet i nästa avsnitt visar hur du använder **SqlConnectionStringBuilder**.

> [!NOTE]
> Detta är den enda ändring som krävs i ett klientprogram som är specifikt för Alltid krypterat. Om du har ett befintligt program som lagrar anslutningssträngen externt (det vill säga i en config-fil) kanske du kan aktivera Alltid krypterad utan att ändra någon kod.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Aktivera alltid krypterad i anslutningssträngen

Lägg till följande nyckelord i anslutningssträngen:

    Column Encryption Setting=Enabled

### <a name="enable-always-encrypted-with-a-sqlconnectionstringbuilder"></a>Aktivera alltid krypterad med en SqlConnectionStringBuilder

Följande kod visar hur du aktiverar Alltid krypterad genom att ställa in [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) till [Aktiverad](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;

## <a name="always-encrypted-sample-console-application"></a>Alltid krypterat exempelkonsolprogram

Det här exemplet visar hur du:

* Ändra anslutningssträngen så att den alltid är krypterad.
* Infoga data i de krypterade kolumnerna.
* Välj en post genom att filtrera efter ett visst värde i en krypterad kolumn.

Ersätt innehållet i **Program.cs** med följande kod. Ersätt anslutningssträngen för den globala connectionString-variabeln på raden direkt ovanför huvudmetoden med din giltiga anslutningssträng från Azure-portalen. Detta är den enda ändring du behöver göra i den här koden.

Kör appen för att se Alltid krypterad i aktion.

```cs
using System;
using System.Collections.Generic;
using System.Data;
using System.Data.SqlClient;
using System.Globalization;

namespace AlwaysEncryptedConsoleApp
{
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"Data Source = SPE-T640-01.sys-sqlsvr.local; Initial Catalog = Clinic; Integrated Security = true";

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

            CultureInfo culture = CultureInfo.CreateSpecificCulture("en-US");
            InsertPatient(new Patient()
            {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993", culture)
            });


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
```

## <a name="verify-that-the-data-is-encrypted"></a>Kontrollera att data är krypterade

Du kan snabbt kontrollera att de faktiska data på servern krypteras genom att fråga **patientdata** med SSMS. (Använd den aktuella anslutningen där kolumnkrypteringsinställningen ännu inte är aktiverad.)

Kör följande fråga i klinikens databas.

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Du kan se att de krypterade kolumnerna inte innehåller några oformaterade data.

   ![Nytt konsolprogram](./media/sql-database-always-encrypted/ssms-encrypted.png)

Om du vill använda SSMS för att komma åt oformaterade data kan du lägga till parametern **Kolumnkryptering=aktiverad** i anslutningen.

1. Högerklicka på servern i **Objektutforskaren**i SSMS och klicka sedan på **Koppla från**.
2. Klicka på **Anslut** > **databasmotor** för att öppna fönstret **Anslut till server** och klicka sedan på **Alternativ**.
3. Klicka på **Ytterligare anslutningsparametrar** och skriv **kolumnkrypteringsinställning=aktiverad**.

    ![Nytt konsolprogram](./media/sql-database-always-encrypted/ssms-connection-parameter.png)
4. Kör följande fråga i **klinikens** databas.

        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

     Du kan nu se klartextdata i de krypterade kolumnerna.

    ![Nytt konsolprogram](./media/sql-database-always-encrypted/ssms-plaintext.png)

> [!NOTE]
> Om du ansluter till SSMS (eller någon klient) från en annan dator, kommer den inte att ha tillgång till krypteringsnycklarna och kommer inte att kunna dekryptera data.

## <a name="next-steps"></a>Nästa steg

När du har skapat en databas som använder Alltid krypterad kanske du vill göra följande:

* Kör det här exemplet från en annan dator. Den kommer inte att ha tillgång till krypteringsnycklarna, så den kommer inte att ha tillgång till klartextdata och kommer inte att köras.
* [Rotera och rensa upp dina nycklar](https://msdn.microsoft.com/library/mt607048.aspx).
* [Migrera data som redan är krypterade med Alltid krypterad](https://msdn.microsoft.com/library/mt621539.aspx).
* [Distribuera alltid krypterade certifikat till andra klientdatorer](https://msdn.microsoft.com/library/mt723359.aspx#Anchor_1) (se avsnittet "Göra certifikat tillgängliga för program och användare".

## <a name="related-information"></a>Relaterad information

* [Alltid krypterad (klientutveckling)](https://msdn.microsoft.com/library/mt147923.aspx)
* [Transparent datakryptering](https://msdn.microsoft.com/library/bb934049.aspx)
* [SQL Server-kryptering](https://msdn.microsoft.com/library/bb510663.aspx)
* [Alltid krypterad guide](https://msdn.microsoft.com/library/mt459280.aspx)
* [Alltid krypterad blogg](https://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)