---
title: 'Always Encrypted: Azure SQL Database-Windows certifikat Arkiv | Microsoft Docs'
description: Den här artikeln visar hur du skyddar känsliga data i en SQL-databas med databas kryptering med hjälp av guiden Always Encrypted i SQL Server Management Studio (SSMS). Det visar också hur du lagrar dina krypterings nycklar i Windows certifikat arkiv.
keywords: kryptera data, SQL-kryptering, databas kryptering, känsliga data Always Encrypted
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
ms.openlocfilehash: e9aaa7cb022d4096ec8a175611d0b4c118007b40
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569552"
---
# <a name="always-encrypted-protect-sensitive-data-and-store-encryption-keys-in-the-windows-certificate-store"></a>Always Encrypted: Skydda känsliga data och lagra krypterings nycklar i Windows certifikat Arkiv

Den här artikeln visar hur du skyddar känsliga data i en SQL-databas med databas kryptering med hjälp av [guiden Always Encrypted](https://msdn.microsoft.com/library/mt459280.aspx) i [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx). Det visar också hur du lagrar dina krypterings nycklar i Windows certifikat arkiv.

Always Encrypted är en ny data krypterings teknik i Azure SQL Database och SQL Server som skyddar känsliga data i vila på servern, under förflyttning mellan klient och Server, medan data används, vilket säkerställer att känsliga data aldrig visas som klartext inuti databas systemet. När du har krypterat data kan endast klient program eller App-servrar som har åtkomst till nycklarna komma åt oformaterade data. Detaljerad information finns i [Always Encrypted (databas motor)](https://msdn.microsoft.com/library/mt163865.aspx).

När du har konfigurerat databasen att använda Always Encrypted skapar du ett klient program i C# med Visual Studio för att arbeta med krypterade data.

Följ stegen i den här artikeln för att lära dig hur du konfigurerar Always Encrypted för en Azure SQL-databas. I den här artikeln får du lära dig hur du utför följande uppgifter:

* Skapa [Always Encrypted-nycklar](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3)med hjälp av guiden Always Encrypted i SSMS.
  * Skapa en [kolumn huvud nyckel (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
  * Skapa en [kolumn krypterings nyckel (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
* Skapa en databas tabell och kryptera kolumner.
* Skapa ett program som infogar, väljer och visar data från de krypterade kolumnerna.

## <a name="prerequisites"></a>Förutsättningar

I den här självstudien behöver du:

* Ett Azure-konto och prenumeration. Om du inte har någon kan du registrera dig för en [kostnads fri utvärderings version](https://azure.microsoft.com/pricing/free-trial/).
* [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) version 13.0.700.242 eller senare.
* [.NET Framework 4,6](https://msdn.microsoft.com/library/w0x726c2.aspx) eller senare (på klient datorn).
* [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).

## <a name="create-a-blank-sql-database"></a>Skapa en tom SQL-databas

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Klicka på **skapa en resurs** > **data + lagrings** > **SQL Database**.
3. Skapa en **Tom** databas med namnet **klinik** på en ny eller befintlig server. Detaljerade anvisningar om hur du skapar en databas i Azure Portal finns i [din första Azure SQL-databas](sql-database-single-database-get-started.md).

    ![Skapa en tom databas](./media/sql-database-always-encrypted/create-database.png)

Du behöver anslutnings strängen senare i självstudien. När databasen har skapats går du till den nya klinik-databasen och kopierar anslutnings strängen. Du kan hämta anslutnings strängen när som helst, men det är enkelt att kopiera den när du befinner dig i Azure Portal.

1. Klicka **på SQL-databaser** > **klinik** > **Visa databas anslutnings strängar**.
2. Kopiera anslutnings strängen för **ADO.net**.

    ![Kopiera anslutningssträngen](./media/sql-database-always-encrypted/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>Ansluta till databasen med SSMS

Öppna SSMS och Anslut till servern med klinik-databasen.

1. Öppna SSMS. (Klicka på **Anslut** > **databas motor** för att öppna fönstret **Anslut till Server** om det inte är öppet).
2. Ange server namn och autentiseringsuppgifter. Du hittar Server namnet på bladet SQL Database och i anslutnings strängen som du kopierade tidigare. Ange det fullständiga Server namnet, inklusive *Database.Windows.net*.

    ![Kopiera anslutningssträngen](./media/sql-database-always-encrypted/ssms-connect.png)

Om fönstret **ny brand Väggs regel** öppnas loggar du in på Azure och låter SSMS skapa en ny brand Väggs regel åt dig.

## <a name="create-a-table"></a>Skapa en tabell

I det här avsnittet ska du skapa en tabell för att lagra patient data. Detta är en normal tabell – du kommer att konfigurera kryptering i nästa avsnitt.

1. Expandera **databaser**.
2. Högerklicka på **klinik** -databasen och klicka på **ny fråga**.
3. Klistra in följande Transact-SQL (T-SQL) i fönstret ny fråga och **Kör** det.

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

SSMS innehåller en guide för att enkelt konfigurera Always Encrypted genom att konfigurera CMK, CEK och krypterade kolumner åt dig.

1. Expandera > klinik- > **tabeller**för databaser.
2. Högerklicka på tabellen **patienter** och välj **kryptera kolumner** för att öppna guiden Always Encrypted:

    ![Kryptera kolumner](./media/sql-database-always-encrypted/encrypt-columns.png)

Guiden Always Encrypted innehåller följande avsnitt: **Val av kolumn**, **huvud nyckel konfiguration** (CMK), **verifiering**och **Sammanfattning**.

### <a name="column-selection"></a>Kolumn val

Klicka på **Nästa** på sidan **Introduktion** för att öppna sidan **kolumn val** . På den här sidan kan du välja vilka kolumner du vill kryptera, [vilken typ av kryptering och vilken kolumn krypterings nyckel (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) som ska användas.

Kryptera uppgifter om **SSN** och **födelse datum** för varje patient. Kolumnen **SSN** använder deterministisk kryptering, som stöder likhets uppslag, kopplingar och gruppering av. Kolumnen **födelse datum** kommer att använda slumpmässig kryptering, som inte stöder åtgärder.

Ange **krypterings typ** för kolumnen **SSN** till **deterministisk** och kolumnen **födelse datum** till **slumpmässig**. Klicka på **Nästa**.

![Kryptera kolumner](./media/sql-database-always-encrypted/column-selection.png)

### <a name="master-key-configuration"></a>Huvud nyckel konfiguration

På sidan **konfiguration av huvud nyckel** kan du konfigurera din CMK och välja den nyckel lagrings leverantör där CMK ska lagras. För närvarande kan du lagra en CMK i certifikat arkivet i Windows, Azure Key Vault eller en HSM (Hardware Security Module). I den här självstudien visas hur du lagrar dina nycklar i Windows certifikat arkiv.

Kontrol lera att **Windows certifikat Arkiv** är markerat och klicka på **Nästa**.

![Huvud nyckel konfiguration](./media/sql-database-always-encrypted/master-key-configuration.png)

### <a name="validation"></a>Validering

Du kan kryptera kolumnerna nu eller spara ett PowerShell-skript för att köra senare. I den här självstudien väljer **du Fortsätt för att slutföra nu** och klickar på **Nästa**.

### <a name="summary"></a>Sammanfattning

Kontrol lera att inställningarna är korrekta och klicka på **Slutför** för att slutföra installationen av Always Encrypted.

![Sammanfattning](./media/sql-database-always-encrypted/summary.png)

### <a name="verify-the-wizards-actions"></a>Verifiera guidens åtgärder

När guiden har slutförts konfigureras databasen för Always Encrypted. Följande åtgärder utfördes av guiden:

* Skapade en CMK.
* Skapade en CEK.
* Konfigurerade de markerade kolumnerna för kryptering. Din **patienter** -tabell har för närvarande inga data, men alla befintliga data i de markerade kolumnerna är nu krypterade.

Du kan kontrol lera att nycklarna skapas i SSMS genom att gå till**Always Encrypted nycklar**för **klinik** > -**säkerhet** > . Nu kan du se de nya nycklar som skapas av guiden.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Skapa ett klient program som fungerar med krypterade data

Nu när Always Encrypted har kon figurer ATS kan du skapa ett program som utför *infogningar* och *väljer* i de krypterade kolumnerna. För att kunna köra exempel programmet måste du köra det på samma dator där du körde guiden Always Encrypted. Om du vill köra programmet på en annan dator måste du distribuera dina Always Encrypted certifikat till den dator som kör-klient programmet.  

> [!IMPORTANT]
> Ditt program måste använda [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) -objekt när du skickar oformaterade data till servern med Always Encrypted kolumner. Att skicka litterala värden utan att använda SqlParameter-objekt resulterar i ett undantag.

1. Öppna Visual Studio och skapa ett nytt C# konsol program. Kontrol lera att ditt projekt är inställt på **.NET Framework 4,6** eller senare.
2. Ge projektet namnet **AlwaysEncryptedConsoleApp** och klicka på **OK**.

![Nytt konsol program](./media/sql-database-always-encrypted/console-app.png)

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Ändra anslutnings strängen för att aktivera Always Encrypted

I det här avsnittet beskrivs hur du aktiverar Always Encrypted i databas anslutnings strängen. Du ändrar den-konsol app som du nyss skapade i nästa avsnitt, "Always Encrypted exempel konsol program".

Om du vill aktivera Always Encrypted måste du lägga till nyckelordet för **kolumn krypterings inställningen** i anslutnings strängen och ange det som **aktive rad**.

Du kan ställa in detta direkt i anslutnings strängen, eller så kan du ange den med hjälp av en [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). Exempel programmet i nästa avsnitt visar hur du använder **SqlConnectionStringBuilder**.

> [!NOTE]
> Detta är den enda ändringen som krävs i ett klient program som är specifika för Always Encrypted. Om du har ett befintligt program som lagrar anslutnings strängen externt (det vill säga i en konfigurations fil) kanske du kan aktivera Always Encrypted utan att ändra någon kod.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Aktivera Always Encrypted i anslutnings strängen

Lägg till följande nyckelord i anslutnings strängen:

    Column Encryption Setting=Enabled

### <a name="enable-always-encrypted-with-a-sqlconnectionstringbuilder"></a>Aktivera Always Encrypted med en SqlConnectionStringBuilder

Följande kod visar hur du aktiverar Always Encrypted genom att ställa in funktionen [SqlConnectionStringBuilder. ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) på [aktive rad](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;

## <a name="always-encrypted-sample-console-application"></a>Always Encrypted exempel konsol program

Det här exemplet visar hur du:

* Ändra anslutnings strängen för att aktivera Always Encrypted.
* Infoga data i de krypterade kolumnerna.
* Välj en post genom att filtrera efter ett särskilt värde i en krypterad kolumn.

Ersätt innehållet i **Program.cs** med följande kod. Ersätt anslutnings strängen för den globala connectionString-variabeln på raden direkt ovanför huvud metoden med din giltiga anslutnings sträng från Azure Portal. Detta är den enda ändringen du behöver göra i den här koden.

Kör appen för att se Always Encrypted i praktiken.

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

## <a name="verify-that-the-data-is-encrypted"></a>Verifiera att data är krypterade

Du kan snabbt kontrol lera att faktiska data på servern är krypterade genom att fråga patienternas data med SSMS. (Använd din aktuella anslutning där kolumn krypterings inställningen inte har Aktiver ATS än.)

Kör följande fråga i klinik-databasen.

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Du kan se att de krypterade kolumnerna inte innehåller några oformaterade data.

   ![Nytt konsol program](./media/sql-database-always-encrypted/ssms-encrypted.png)

Om du vill använda SSMS för att komma åt data i klartext kan du lägga till **kolumn krypterings inställningen = aktive rad** parameter till anslutningen.

1. I SSMS högerklickar du på servern i **Object Explorer**och klickar sedan på **Koppla från**.
2. Klicka på **Anslut** > **databas motor** för att öppna fönstret **Anslut till Server** och klicka sedan på **alternativ**.
3. Klicka på **ytterligare anslutnings parametrar** och ange **kolumn krypterings inställning = aktive rad**.

    ![Nytt konsol program](./media/sql-database-always-encrypted/ssms-connection-parameter.png)
4. Kör följande fråga i **klinik** -databasen.

        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

     Nu kan du se data i klartext i de krypterade kolumnerna.

    ![Nytt konsol program](./media/sql-database-always-encrypted/ssms-plaintext.png)

> [!NOTE]
> Om du ansluter till SSMS (eller någon klient) från en annan dator, kommer den inte att ha åtkomst till krypterings nycklarna och kommer inte att kunna dekryptera data.

## <a name="next-steps"></a>Nästa steg

När du har skapat en databas som använder Always Encrypted kanske du vill göra följande:

* Kör det här exemplet från en annan dator. Den har inte åtkomst till krypterings nycklarna, så den kommer inte att ha åtkomst till data i klartext och kommer inte att kunna köras.
* [Rotera och rensa dina nycklar](https://msdn.microsoft.com/library/mt607048.aspx).
* [Migrera data som redan är krypterade med Always Encrypted](https://msdn.microsoft.com/library/mt621539.aspx).
* [Distribuera Always Encrypted certifikat till andra klient datorer](https://msdn.microsoft.com/library/mt723359.aspx#Anchor_1) (mer information finns i avsnittet "göra certifikat tillgängliga för program och användare").

## <a name="related-information"></a>Relaterad information

* [Always Encrypted (klient utveckling)](https://msdn.microsoft.com/library/mt147923.aspx)
* [Transparent datakryptering](https://msdn.microsoft.com/library/bb934049.aspx)
* [SQL Server kryptering](https://msdn.microsoft.com/library/bb510663.aspx)
* [Always Encrypted guiden](https://msdn.microsoft.com/library/mt459280.aspx)
* [Always Encrypted blogg](https://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)