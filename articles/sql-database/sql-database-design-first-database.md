---
title: "Utforma din första Azure SQL-databas | Microsoft Docs"
description: "Lär dig att utforma din första Azure SQL-databas på Azure-portalen och med SQL Server Management Studio."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,develop databases
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 08/25/2017
ms.author: carlrab
ms.openlocfilehash: e4848eb366faea134a484c8a494fed6a83203116
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="design-your-first-azure-sql-database"></a>Utforma din första Azure SQL-databas

Azure SQL Database är en relationell databas-som-en tjänst (DBaaS) i Microsoft Cloud (”Azure”). I kursen får du lära dig hur du använder Azure-portalen och [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) till: 

> [!div class="checklist"]
> * Skapa en databas på Azure-portalen
> * Konfigurera en brandväggsregel på servernivå i Azure-portalen
> * Ansluta till databasen med SSMS
> * Skapa tabeller med SSMS
> * Massinläsning av data med BCP
> * Fråga data med SSMS
> * Återställa databasen till ett tidigare [återställning vid tidpunkt](sql-database-recovery-using-backups.md#point-in-time-restore) i Azure-portalen

Om du inte har en Azure-prenumeration [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Kontrollera att du har installerat den här kursen:
- Den senaste versionen av [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS).
- Den senaste versionen av [BCP och SQLCMD](https://www.microsoft.com/download/details.aspx?id=36433).

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-blank-sql-database"></a>Skapa en tom SQL-databas

Azure SQL-databasen skapas med en definierad uppsättning [beräknings-och lagringsresurser](sql-database-service-tiers.md). Databasen skapas i en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) och i en [logisk Azure SQL Database-server](sql-database-features.md). 

Följ dessa steg om du vill skapa en tom SQL-databas. 

1. Klicka på knappen **New** (Nytt) i det övre vänstra hörnet i Azure Portal.

2. Välj **Databaser** på sidan **Nytt** och välj **Skapa** under **SQL Database** på sidan **Nytt**.

   ![Skapa en tom databas](./media/sql-database-design-first-database/create-empty-database.png)

3. Fyll i följande information i SQL Database-formuläret (se föregående bild):   

   | Inställning       | Föreslaget värde | Beskrivning | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Databasnamn** | mySampleDatabase | För giltiga databasnamn, se [databasidentifierare](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers). | 
   | **Prenumeration** | Din prenumeration  | Mer information om dina prenumerationer finns i [Prenumerationer](https://account.windowsazure.com/Subscriptions). |
   | **Resursgrupp** | myResourceGroup | Giltiga resursgruppnamn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   | **Välj källa** | Tom databas | Anger att en tom databas ska skapas. |

4. Klicka på **Server** för att skapa och konfigurera en ny server för den nya databasen. Fyll i den **nytt serverformulär** med följande information: 

   | Inställning       | Föreslaget värde | Beskrivning | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Servernamn** | Valfritt globalt unikt namn | Giltiga servernamn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). | 
   | **Inloggning för serveradministratör** | Valfritt giltigt namn | För giltiga inloggningsnamn, se [Databasidentifierare](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers).|
   | **Lösenord** | Valfritt giltigt lösenord | Lösenordet måste innehålla minst åtta tecken och måste innehålla tecken från tre av följande kategorier: versaler, gemener, siffror och specialtecken. |
   | **Plats** | Valfri giltig plats | För information om regioner, se [Azure-regioner](https://azure.microsoft.com/regions/). |

   ![skapa databas-server](./media/sql-database-design-first-database/create-database-server.png)

5. Klicka på **Välj**.

6. Klicka på **Prisnivå** för att ange tjänstnivå, antalet DTU:er och mängden lagring. Utforska alternativ för antalet dtu: er och lagring som är tillgängliga för varje tjänstnivå. 

7. Den här självstudiekursen, Välj den **Standard** servicenivå och Använd sedan skjutreglaget för att välja **100 dtu: er (S3)** och **400** GB lagringsutrymme.

   ![skapa databas-s1](./media/sql-database-design-first-database/create-empty-database-pricing-tier.png)

8. Godkänn förhandsgranskningsvillkoren för att använda alternativet **Lägg till mer lagringsutrymme**. 

   > [!IMPORTANT]
   > \*Lagringsstorlekar som är större än mängden lagringsutrymme som ingår finns i förhandsversionen, och extra kostnader tillkommer. Mer information finns i [Priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). 
   >
   >\*I Premium-nivån finns för närvarande mer än 1 TB lagringsutrymme i följande regioner: Östra USA 2, USA, västra, Virginia (USA-förvaltad region), Europa, västra, Centrala Tyskland, Sydostasien, Östra Japan, Australien, östra, Centrala Kanada och Östra Kanada. Se [sidan 11-15 i Aktuella begränsningar](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
   > 

9. När du har valt tjänstenivå, antalet DTU:er och mängden lagring klickar du på **Apply** (Använd).  

10. Välj en **sorteringen** för tom databas (använda standardvärdet för den här självstudiekursen). Mer information om sorteringar finns [sorteringar](https://docs.microsoft.com/sql/t-sql/statements/collations)

11. Nu när du har fyllt i SQL Database-formuläret klickar du på **Skapa** så att databasen etableras. Etableringen tar några minuter. 

12. Klicka på **Aviseringar** i verktygsfältet för att övervaka distributionsprocessen.
    
     ![avisering](./media/sql-database-get-started-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Skapa en brandväggsregel på servernivå

SQL Database-tjänsten skapar en brandvägg på servernivå som hindrar externa program och verktyg från att ansluta till servern eller databaser på servern, såvida inte en brandväggsregel konfigureras som öppnar brandväggen för specifika IP-adresser. Följ de här stegen om du vill skapa en [brandväggsregel på SQL Database-servernivå](sql-database-firewall-configure.md) för din klients IP-adress och aktivera extern anslutning genom SQL Database-brandväggen endast för din IP-adress. 

> [!NOTE]
> SQL Database kommunicerar via port 1433. Om du försöker ansluta inifrån ett företagsnätverk, kan utgående trafik via port 1433 nekas av nätverkets brandvägg. I så fall kommer du inte att kunna ansluta till din Azure SQL Database-server om inte din IT-avdelning öppnar port 1433.
>

1. När distributionen är klar klickar du på **SQL-databaser** på menyn till vänster och klickar sedan på **mySampleDatabase** på sidan **SQL-databaser**. Översiktssidan för databasen öppnas, där du kan se det fullständigt kvalificerade servernamnet (som **mynewserver-20170824.database.windows.net**) och alternativ för ytterligare konfiguration. 

2. Kopiera det här fullständiga servernamnet för anslutning till servern och databaserna i efterföljande snabbstarter. 

   ![servernamn](./media/sql-database-get-started-portal/server-name.png) 

3. Klicka på **ange serverbrandvägg** i verktygsfältet. Sidan **Brandväggsinställningar** för SQL Database-servern öppnas. 

   ![brandväggsregler för server](./media/sql-database-get-started-portal/server-firewall-rule.png) 

4. Klicka på **Lägg till klient-IP** i verktygsfältet och lägg till din aktuella IP-adress i en ny brandväggsregel. Med en brandväggsregel kan du öppna port 1433 för en enskild IP-adress eller för IP-adressintervall.

5. Klicka på **Spara**. En brandväggsregel på servernivå för att öppna port 1433 på den logiska servern skapas för din aktuella IP-adress.

6. Klicka på **OK** och stäng sedan sidan **Brandväggsinställningar**.

Nu kan du ansluta till SQL Server Database-servern och dess databaser med SQL Server Management Studio eller något annat verktyg från den här IP-adressen med det Server-administratörskonto som skapades tidigare.

> [!IMPORTANT]
> Som standard är åtkomst genom SQL Database-brandväggen aktiverad för alla Azure-tjänster. Klicka på **AV** på den här sidan om du vill inaktivera åtkomsten för alla Azure-tjänster.

## <a name="sql-server-connection-information"></a>Anslutningsinformation för en SQL-server

Hämta det fullständigt kvalificerade servernamnet för Azure SQL Database-servern i Azure Portal. Du kan använda det fullständigt kvalificerade servernamnet för att ansluta till servern med hjälp av SQL Server Management Studio.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **SQL-databaser** på den vänstra menyn och klicka på databasen på sidan **SQL-databaser**. 
3. I rutan **Essentials** på sidan för Azure Portal för databasen letar du reda på och kopierar **servernamnet**.

   ![anslutningsinformation](./media/sql-database-get-started-portal/server-name.png)

## <a name="connect-to-the-database-with-ssms"></a>Ansluta till databasen med SSMS

Använd [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) att upprätta en anslutning till din Azure SQL Database-server.

1. Öppna SQL Server Management Studio.

2. I dialogrutan **Anslut till server** anger du följande information:

   | Inställning       | Föreslaget värde | Beskrivning | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | Servertyp | Databasmotor | Det här värdet krävs |
   | servernamn | Fullständigt kvalificerat servernamn | Namnet ska vara ungefär så här: **mynewserver20170824.database.windows.net**. |
   | Autentisering | SQL Server-autentisering | SQL-autentisering är den enda autentiseringstypen som vi har konfigurerat i den här kursen. |
   | Inloggning | Serveradministratörskontot | Detta är det konto som du angav när du skapade servern. |
   | Lösenord | Lösenordet för serveradministratörskontot | Detta är det lösenord som du angav när du skapade servern. |

   ![Anslut till server](./media/sql-database-connect-query-ssms/connect.png)

3. Klicka på **Alternativ** i dialogrutan **Anslut till server**. I avsnittet **Anslut till databas** anger du **mySampleDatabase** så att du ansluter till den här databasen.

   ![ansluta till databas på server](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Klicka på **Anslut**. Fönstret Object Explorer öppnas i SSMS. 

5. I Object Explorer expanderar du **Databaser** och sedan **mySampleDatabase** för att visa objekten i exempeldatabasen.

   ![objekt i databasen](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="create-tables-in-the-database"></a>Skapa tabeller i databasen 

Skapa ett databasschema med fyra tabeller som modellen är ett system för hantering av student för universitet med [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference):

- Person
- Kursen
- För studenter
- Kredit att modellen ett student hanteringssystem för universitet

Följande diagram visar hur dessa tabeller är relaterade till varandra. Vissa av dessa tabeller referera till kolumner i andra tabeller. Till exempel studenttabell refererar till den **PersonId** kolumnen i den **Person** tabell. Studera diagram för att förstå hur tabellerna i den här kursen är relaterade till varandra. En titt på hur du skapar effektiva databastabeller finns [skapa effektiva databastabeller](https://msdn.microsoft.com/library/cc505842.aspx). Information om hur du väljer datatyper finns i [datatyper](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql).

> [!NOTE]
> Du kan också använda den [tabelldesignern i SQL Server Management Studio](https://msdn.microsoft.com/library/hh272695.aspx) att skapa och utforma tabeller. 

![Relationerna mellan tabellerna](./media/sql-database-design-first-database/tutorial-database-tables.png)

1. I Object Explorer högerklickar du på **mySampleDatabase** och klickar sedan på **Ny fråga**. Ett tomt frågefönster öppnas som är anslutet till databasen.

2. I frågefönstret och kör följande fråga om du vill skapa fyra tabeller i databasen: 

   ```sql 
   -- Create Person table

   CREATE TABLE Person
   (
   PersonId   INT IDENTITY PRIMARY KEY,
   FirstName   NVARCHAR(128) NOT NULL,
   MiddelInitial NVARCHAR(10),
   LastName   NVARCHAR(128) NOT NULL,
   DateOfBirth   DATE NOT NULL
   )
   
   -- Create Student table
 
   CREATE TABLE Student
   (
   StudentId INT IDENTITY PRIMARY KEY,
   PersonId  INT REFERENCES Person (PersonId),
   Email   NVARCHAR(256)
   )
   
   -- Create Course table
 
   CREATE TABLE Course
   (
   CourseId  INT IDENTITY PRIMARY KEY,
   Name   NVARCHAR(50) NOT NULL,
   Teacher   NVARCHAR(256) NOT NULL
   ) 

   -- Create Credit table
 
   CREATE TABLE Credit
   (
   StudentId   INT REFERENCES Student (StudentId),
   CourseId   INT REFERENCES Course (CourseId),
   Grade   DECIMAL(5,2) CHECK (Grade <= 100.00),
   Attempt   TINYINT,
   CONSTRAINT  [UQ_studentgrades] UNIQUE CLUSTERED
   (
   StudentId, CourseId, Grade, Attempt
   )
   )
   ```

   ![Skapa tabeller](./media/sql-database-design-first-database/create-tables.png)

3. Expandera noden 'tabeller' i objektet för SQL Server Management Studio explorer att se de tabeller som du skapade.

   ![Skapa tabeller ssms](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>Läser in data i tabeller

1. Skapa en mapp med namnet **SampleTableData** i din mapp för nedladdningar att lagra exempeldata för din databas. 

2. Högerklicka på länken nedan och spara dem i den **SampleTableData** mapp. 

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. Öppna en kommandotolk och navigera till mappen SampleTableData.

4. Kör följande kommandon för att infoga exempeldata i tabellerna där du ersätter värdena för **ServerName**, **DatabaseName**, **användarnamn**, och **lösenord** med värden för din miljö.
  
   ```bcp
   bcp Course in SampleCourseData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   ```

Du har nu lästs in exempeldata i tabeller som du skapade tidigare.

## <a name="query-data"></a>Frågedata

Kör följande frågor för att hämta information från databastabeller. Se [skriva SQL-frågor](https://technet.microsoft.com/library/bb264565.aspx) vill veta mer om hur du skriver SQL-frågor. Den första frågan kopplar ihop alla fyra tabeller för att hitta alla eleverna vilka undervisning förekommer av ' Dominick påven ' som har en klass som är högre än 75% i sin klass. Den andra frågan kopplar ihop alla fyra tabeller och söker efter alla kurser där 'Noe Coleman' någonsin har registrerats.

1. Kör följande fråga i frågefönstret en SQL Server Management Studio:

   ```sql 
   -- Find the students taught by Dominick Pope who have a grade higher than 75%

   SELECT  person.FirstName,
   person.LastName,
   course.Name,
   credit.Grade
   FROM  Person AS person
   INNER JOIN Student AS student ON person.PersonId = student.PersonId
   INNER JOIN Credit AS credit ON student.StudentId = credit.StudentId
   INNER JOIN Course AS course ON credit.CourseId = course.courseId
   WHERE course.Teacher = 'Dominick Pope' 
   AND Grade > 75
   ```

2. Kör följande fråga i frågefönstret en SQL Server Management Studio:

   ```sql
   -- Find all the courses in which Noe Coleman has ever enrolled

   SELECT  course.Name,
   course.Teacher,
   credit.Grade
   FROM  Course AS course
   INNER JOIN Credit AS credit ON credit.CourseId = course.CourseId
   INNER JOIN Student AS student ON student.StudentId = credit.StudentId
   INNER JOIN Person AS person ON person.PersonId = student.PersonId
   WHERE person.FirstName = 'Noe'
   AND person.LastName = 'Coleman'
   ```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Återställa en databas till en tidigare tidpunkt

Anta att du av misstag har tagit bort en tabell. Detta är något du lätt kan återställa från. Azure SQL-databas kan du gå tillbaka till valfri punkt i tiden i den senaste upp till 35 dagar och en återställningspunkt i tid till en ny databas. Du kan den här databasen om du vill återställa dina data. Följande steg återställa exempeldatabasen till en innan tabellerna lades till.

1. På sidan SQL-databasen för din databas **återställa** i verktygsfältet. Den **återställa** öppnas.

   ![Återställa](./media/sql-database-design-first-database/restore.png)

2. Fyll i den **återställa** formulär med informationen som krävs:
    * Databasnamn: Ange ett databasnamn 
    * I tidpunkt: Välj den **i tidpunkt** i formuläret återställning 
    * Återställningspunkt: Välj en tidpunkt som inträffar innan databasen har ändrats
    * Målservern: du kan inte ändra det här värdet när du återställer en databas 
    * Elastisk databaspool: Välj **ingen**  
    * Prisnivån: Välj **20 dtu: er** och **40 GB** lagring.

   ![återställningspunkt](./media/sql-database-design-first-database/restore-point.png)

3. Klicka på **OK** att återställa databasen till [återställa till en tidpunkt](sql-database-recovery-using-backups.md#point-in-time-restore) innan tabellerna lades till. Återställa en databas till en annan tidpunkt skapar en dubblett databas på samma server som den ursprungliga databasen från och med punkten tidpunkt du anger som befinner sig inom kvarhållningsperioden för din [tjänstnivån](sql-database-service-tiers.md).

## <a name="next-steps"></a>Nästa steg 
I kursen får du lärt dig grundläggande uppgifter som skapar en databas och tabeller, läsa in fråga efter data och återställa databasen till en tidigare tidpunkt. Du har lärt dig att:
> [!div class="checklist"]
> * Skapa en databas
> * Konfigurera en brandväggsregel
> * Ansluta till databasen med [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS)
> * Skapa tabeller
> * Massinläsning av data
> * Fråga data
> * Återställa databasen till en tidigare tidpunkt med hjälp av SQL-databas [återställning vid tidpunkt](sql-database-recovery-using-backups.md#point-in-time-restore) funktioner

Gå vidare till nästa kurs mer information om hur du utformar en databas med Visual Studio och C#.

> [!div class="nextstepaction"]
>[Utforma en Azure SQL database och ansluta med C# och ADO.NET](sql-database-design-first-database-csharp.md)
