---
title: 'Självstudier: Skapa din första enkla databas i Azure SQL Database med hjälp av SSMS | Microsoft Docs'
description: Läs hur du skapar din första Azure SQL-databas med SQL Server Management Studio.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: CarlRabeler
ms.author: carlrab
ms.reviewer: v-masebo
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: e7229a0816cf74fed08397a68dd34e305bf8c0ea
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55459544"
---
# <a name="tutorial-design-your-first-azure-sql-database-using-ssms"></a>Självstudie: Skapa din första Azure SQL-databas med SSMS

Azure SQL Database är en relationsdatabas-som-tjänst (DBaaS) som bygger på Microsoft Cloud (Azure). I de här självstudierna får du lära dig att använda Azure-portalen och [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) för att:

> [!div class="checklist"]
> * Skapa en databas på Azure Portal*
> * Skapade en brandväggsregel på servernivå på Azure-portalen
> * Ansluta till databasen med SSMS
> * Skapa tabeller med SSMS
> * Massinläsa data med BCP
> * Fråga efter data med SSMS

*Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

> [!NOTE]
> I den här självstudiekursen använder vi [den DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md), men du kan också välja [den vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna följa de här självstudierna ska du kontrollera att du har installerat:

- [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (senaste versionen)
- [BCP och SQLCMD](https://www.microsoft.com/download/details.aspx?id=36433) (senaste versionen)

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-blank-database"></a>Skapa en tom databas

Azure SQL-databasen skapas med en definierad uppsättning [beräknings-och lagringsresurser](sql-database-service-tiers-dtu.md). Databasen skapas i en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) och på en [Azure SQL Database-server](sql-database-features.md).

Följ de här stegen om du vill skapa en tom SQL-databas.

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure Portal.

1. Välj **Databaser** i avsnittet Azure Marketplace på sidan **Nytt** och klicka sedan på **SQL Database** i avsnittet **Aktuellt**.

   ![skapa tom databas](./media/sql-database-design-first-database/create-empty-database.png)

   1. Fyll i följande information i **SQL Database**-formuläret (se föregående bild):

      | Inställning       | Föreslaget värde | Beskrivning |
      | ------------ | ------------------ | ------------------------------------------------- |
      | **Databasnamn** | *yourDatabase* | För giltiga databasnamn, se [databasidentifierare](/sql/relational-databases/databases/database-identifiers). |
      | **Prenumeration** | *yourSubscription*  | Mer information om dina prenumerationer finns i [Prenumerationer](https://account.windowsazure.com/Subscriptions). |
      | **Resursgrupp** | *yourResourceGroup* | Giltiga resursgruppnamn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/naming-conventions). |
      | **Välj källa** | Tom databas | Anger att en tom databas ska skapas. |

   1. Klicka på **Server** för att använda en befintlig server eller skapa och konfigurera en ny server för databasen. Antingen väljer du servern eller klicka på **Skapa en ny server** och fyller i följande information i formuläret **Ny server**:

      | Inställning       | Föreslaget värde | Beskrivning |
      | ------------ | ------------------ | ------------------------------------------------- |
      | **Servernamn** | Valfritt globalt unikt namn | Giltiga servernamn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/naming-conventions). |
      | **Inloggning för serveradministratör** | Valfritt giltigt namn | För giltiga inloggningsnamn, se [Databasidentifierare](/sql/relational-databases/databases/database-identifiers). |
      | **Lösenord** | Valfritt giltigt lösenord | Lösenordet måste innehålla minst åtta tecken och måste innehålla tecken från tre av följande kategorier: versaler, gemener, siffror och icke-alfanumeriska tecken. |
      | **Plats** | Valfri giltig plats | För information om regioner, se [Azure-regioner](https://azure.microsoft.com/regions/). |

      ![skapa databas-server](./media/sql-database-design-first-database/create-database-server.png)

      Klicka på **Välj**.

   1. Klicka på **Prisnivå** för att ange tjänstnivå, antalet DTU:er eller V-kärnor och mängden lagring. Du kan undersöka alternativen för antalet DTU:er/V-kärnor och lagringsutrymme som du har tillgång till på varje tjänstnivå. I den här självstudiekursen använder vi [den DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md) **Standard**, men du kan också välja [den vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md).

      > [!IMPORTANT]
      > Mer än 1 TB lagringsutrymme på Premium-nivån är för närvarande tillgängligt i alla regioner förutom följande: Storbritannien, norra; USA, västra centrala; Storbritannien, södra 2; Kina, östra; US DoD, centrala; Tyskland, centrala; US DoD, östra; US Gov, sydvästra; US Gov, södra centrala; Tyskland, nordöstra; Kina, norra; US Gov, östra. Det maximala lagringsutrymmet på Premium-nivån i andra regioner är begränsat till 1 TB. Se [sidan 11-15 i Aktuella begränsningar]( sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).

      När du har valt tjänstenivå, antalet DTU:er och mängden lagring klickar du på **Apply** (Använd).

   1. Ange en **sortering** för den tomma databasen (använd standardvärdet för de här självstudierna). Mer information om sorteringar finns i [Sorteringar](/sql/t-sql/statements/collations).

1. Nu när du har fyllt i**SQL Database**-formuläret klickar du på **Skapa** så att databasen etableras. Det här steget kan ta några minuter.

1. Klicka på **Aviseringar** i verktygsfältet för att övervaka distributionsprocessen.

     ![avisering](./media/sql-database-design-first-database/notification.png)

## <a name="create-a-firewall-rule"></a>Skapa en brandväggsregel

SQL Database-tjänsten skapar en brandvägg på servernivå. Brandväggen förhindrar att externa program och verktyg ansluter till servern eller databaser på servern. Om du vill aktivera extern anslutning till din databas måste du först lägga till en regel för din IP-adress i brandväggen. Följ de här stegen för att skapa en [brandväggsregel på SQL Database-servernivå](sql-database-firewall-configure.md).

> [!NOTE]
> SQL Database kommunicerar via port 1433. Om du försöker ansluta inifrån ett företagsnätverk, kan utgående trafik via port 1433 nekas av nätverkets brandvägg. I så fall kommer du inte att kunna ansluta till din Azure SQL Database-server om inte din administratör öppnar port 1433.

1. När distributionen är klar klickar du på **SQL-databaser** på menyn till vänster och klickar sedan på *yourDatabase* på sidan **SQL-databaser**. Översiktssidan för databasen öppnas och visar det fullständigt kvalificerade **servernamnet** (till exempel *yourserver.database.windows.net*) tillsammans med alternativ för ytterligare konfiguration.

1. Kopiera det här fullständigt kvalificerade servernamnet. Du behöver det när du ansluter till servern och dess databaser från SQL Server Management Studio.

   ![servernamn](./media/sql-database-design-first-database/server-name.png)

1. Klicka på **Konfigurera serverns brandvägg** i verktygsfältet. Sidan **Brandväggsinställningar** för SQL Database-servern öppnas.

   ![brandväggsregler för server](./media/sql-database-design-first-database/server-firewall-rule.png)

   1. Klicka på **Lägg till klient-IP** i verktygsfältet och lägg till din aktuella IP-adress i en ny brandväggsregel. Med en brandväggsregel kan du öppna port 1433 för en enskild IP-adress eller för IP-adressintervall.

   1. Klicka på **Spara**. En brandväggsregel på servernivå för att öppna port 1433 på SQL Database-servern skapas för din aktuella IP-adress.

   1. Klicka på **OK** och stäng sedan sidan **Brandväggsinställningar**.

Din IP-adress kan nu passera genom brandväggen. Nu kan du ansluta till SQL-databasservern och dess databaser med hjälp av SQL Server Management Studio eller något annat verktyg. Se till att använda serveradmin-kontot som du skapade tidigare.

> [!IMPORTANT]
> Som standard är åtkomst genom SQL Database-brandväggen aktiverad för alla Azure-tjänster. Klicka på **AV** på den här sidan om du vill inaktivera åtkomsten för alla Azure-tjänster.

## <a name="connect-to-the-database"></a>Ansluta till databasen

Använd [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) för att upprätta en anslutning till Azure SQL Database-servern.

1. Öppna SQL Server Management Studio.

1. I dialogrutan **Anslut till server** anger du följande information:

   | Inställning       | Föreslaget värde | Beskrivning |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Servertyp** | Databasmotor | Det här värdet är obligatoriskt. |
   | **Servernamn** | Fullständigt kvalificerat servernamn | Till exempel *yourserver.database.windows.net*. |
   | **Autentisering** | SQL Server-autentisering | SQL-autentisering är den enda autentiseringstypen som vi har konfigurerat i den här självstudien. |
   | **Inloggning** | Serveradministratörskontot | Kontot som du angav när du skapade servern. |
   | **Lösenord** | Lösenordet för serveradministratörskontot | Detta är det lösenord som du angav när du skapade servern. |

   ![Anslut till server](./media/sql-database-design-first-database/connect.png)

   1. Klicka på **Alternativ** i dialogrutan **Anslut till server**. I avsnittet **Anslut till databas** anger du *yourDatabase* så att du ansluter till den här databasen.

      ![ansluta till databas på server](./media/sql-database-design-first-database/options-connect-to-db.png)  

   1. Klicka på **Anslut**. Fönstret **Object Explorer** öppnas i SSMS.

1. I **Object Explorer** expanderar du **Databaser** och sedan *yourDatabase* för att visa objekten i exempeldatabasen.

   ![databasobjekt](./media/sql-database-design-first-database/connected.png)  

## <a name="create-tables-in-the-database"></a>Skapa tabeller i databasen

Skapa ett databasschema med fyra tabeller som visar ett studenthanteringssystem för universitet med [Transact-SQL](/sql/t-sql/language-reference):

- Person
- Kurs
- Student
- Kredit

Följande diagram visar hur tabellerna är relaterade till varandra. Vissa av tabellerna hänvisar till kolumner i andra tabeller. Till exempel hänvisar *studenttabellen* till kolumnen *PersonId* i tabellen *Person*. Studera diagrammet för att förstå hur tabellerna i de här självstudierna är relaterade till varandra. Mer information om hur du skapar effektiva databastabeller finns i [Skapa effektiva databastabeller](https://msdn.microsoft.com/library/cc505842.aspx). Information om hur du väljer datatyper finns i [Datatyper](/sql/t-sql/data-types/data-types-transact-sql).

> [!NOTE]
> Du kan också använda [tabelldesignern i SQL Server Management Studio](/sql/ssms/visual-db-tools/design-database-diagrams-visual-database-tools) för att skapa och utforma tabeller.

![Relationer mellan tabellerna](./media/sql-database-design-first-database/tutorial-database-tables.png)

1. I **Object Explorer** högerklickar du på *yourDatabase* och väljer sedan **Ny fråga**. Ett tomt frågefönster öppnas som är anslutet till databasen.

1. I frågefönstret skriver du följande fråga om du vill skapa fyra tabeller i databasen:

   ```sql
   -- Create Person table
   CREATE TABLE Person
   (
       PersonId INT IDENTITY PRIMARY KEY,
       FirstName NVARCHAR(128) NOT NULL,
       MiddelInitial NVARCHAR(10),
       LastName NVARCHAR(128) NOT NULL,
       DateOfBirth DATE NOT NULL
   )

   -- Create Student table
   CREATE TABLE Student
   (
       StudentId INT IDENTITY PRIMARY KEY,
       PersonId INT REFERENCES Person (PersonId),
       Email NVARCHAR(256)
   )

   -- Create Course table
   CREATE TABLE Course
   (
       CourseId INT IDENTITY PRIMARY KEY,
       Name NVARCHAR(50) NOT NULL,
       Teacher NVARCHAR(256) NOT NULL
   )

   -- Create Credit table
   CREATE TABLE Credit
   (
       StudentId INT REFERENCES Student (StudentId),
       CourseId INT REFERENCES Course (CourseId),
       Grade DECIMAL(5,2) CHECK (Grade <= 100.00),
       Attempt TINYINT,
       CONSTRAINT [UQ_studentgrades] UNIQUE CLUSTERED
       (
           StudentId, CourseId, Grade, Attempt
       )
   )
   ```

   ![Skapa tabeller](./media/sql-database-design-first-database/create-tables.png)

1. Expandera noden **Tabeller** under *yourDatabase* i **Object Explorer** för att se de tabeller som du skapade.

   ![skapade ssms-tabeller](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>Läs in data till tabellerna

1. Skapa en mapp med namnet *sampleData* i din mapp för nedladdningar för att lagra exempeldata till din databas.

1. Högerklicka på följande länkar och spara dem i mappen *sampleData*.

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

1. Öppna en kommandotolk och navigera till mappen *sampleData*.

1. Kör följande kommandon för att infoga exempeldata i tabellerna där du ersätter värdena för *server*, *databas*, *användare* och *lösenord* med värdena för din miljö.
  
   ```cmd
   bcp Course in SampleCourseData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   ```

Du har nu läst in exempeldata i de tabeller som du skapade tidigare.

## <a name="query-data"></a>Söka i data

Kör följande frågor för att hämta information från databastabellerna. I [Skriva SQL-frågor](https://technet.microsoft.com/library/bb264565.aspx) kan du läsa mer om hur du skriver SQL-frågor. Den första frågan kopplar ihop alla fyra tabellerna och söker efter alla elever som undervisats av ”Dominick Pope” som har ett betyg som är högre än 75 %. Den andra frågan kopplar ihop alla fyra tabeller och söker efter kurserna som ”Noe Coleman” någonsin har registrerat sig på.

1. Kör följande fråga i frågefönstret i SQL Server Management Studio:

   ```sql
   -- Find the students taught by Dominick Pope who have a grade higher than 75%
   SELECT  person.FirstName, person.LastName, course.Name, credit.Grade
   FROM  Person AS person
       INNER JOIN Student AS student ON person.PersonId = student.PersonId
       INNER JOIN Credit AS credit ON student.StudentId = credit.StudentId
       INNER JOIN Course AS course ON credit.CourseId = course.courseId
   WHERE course.Teacher = 'Dominick Pope'
       AND Grade > 75
   ```

1. I ett frågefönster kör du följande fråga:

   ```sql
   -- Find all the courses in which Noe Coleman has ever enrolled
   SELECT  course.Name, course.Teacher, credit.Grade
   FROM  Course AS course
       INNER JOIN Credit AS credit ON credit.CourseId = course.CourseId
       INNER JOIN Student AS student ON student.StudentId = credit.StudentId
       INNER JOIN Person AS person ON person.PersonId = student.PersonId
   WHERE person.FirstName = 'Noe'
       AND person.LastName = 'Coleman'
   ```

## <a name="next-steps"></a>Nästa steg

I den här självstudien beskrivs många grundläggande databasuppgifter. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en databas
> * Konfigurera en brandväggsregel
> * Ansluta till databasen med [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS)
> * Skapa tabeller
> * Massinläsa data
> * Fråga efter dessa data

Gå vidare till fler självstudier för att lära dig att utforma en databas med Visual Studio och C#.

> [!div class="nextstepaction"]
> [Utforma en Azure SQL databas och ansluta med C# och ADO.NET](sql-database-design-first-database-csharp.md)
