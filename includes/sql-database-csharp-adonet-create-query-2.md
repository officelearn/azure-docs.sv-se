---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 11/09/2018
ms.author: genemi
ms.openlocfilehash: c4329b9efef3cdb2911466e64ac6c9f07a1e9b31
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52272370"
---
<a name="cs_0_csharpprogramexample_h2"/>

## <a name="c-program-example"></a>C#-programexempel

I följande avsnitt i den här artikeln finns ett C#-program som använder ADO.NET för att skicka Transact-SQL-instruktioner till SQL-databasen. C#-programmet utför följande åtgärder:

1. [Ansluter till vår SQL-databas med ADO.NET](#cs_1_connect).
2. [Skapar tabeller](#cs_2_createtables).
3. [Fyller tabeller med data genom att utfärda INSERT T-SQL-instruktioner](#cs_3_insert).
4. [Uppdaterar data med hjälp av en koppling](#cs_4_updatejoin).
5. [Tar bort data med hjälp av en koppling](#cs_5_deletejoin).
6. [Markerar datarader med hjälp av en koppling](#cs_6_selectrows).
7. Stänger anslutningen (som släpper alla temporära tabeller från TempDB-databasen).

C#-programmet innehåller:

- C#-kod för att ansluta till databasen.
- Metoder som returnerar T-SQL-källkoden.
- Två metoder som skickar T-SQL till databasen.

#### <a name="to-compile-and-run"></a>Kompilera och köra

Det här C#-programmet är logiskt en .cs-fil. Men programmet är fysiskt uppdelat i flera kodblock, som gör det lättare att se och förstå varje block. Gör följande för att kompilera och köra det här programmet:

1. Skapa C#-projektet i Visual Studio.
    - Projekttypen ska vara ett *konsolprogram* från något som liknar följande hierarki: **Mallar** > **Visual C#**  > **Windows Classic Desktop** > **Konsolprogram (.NET Framework)**.
3. I filen **Program.cs** raderar du de små startraderna med kod.
3. I Program.cs kopierar och klistrar du in var och en av de följande blocken i samma ordning som de visas här.
4. I Program.cs redigerar du följande värden i **Main**-metoden:

   - **cb.DataSource**
   - **cd.UserID**
   - **cb.Password**
   - **InitialCatalog**

5. Kontrollera att sammansättningen **System.Data.dll** är refererad. Du kan kontrollera det genom att expandera noden **Referenser** i fönstret **Solution Explorer**.
6. För att skapa programmet i Visual Studio klickar du på menyn **Skapa**.
7. Om du vill köra programmet från Visual Studio klickar du på knappen **Start**. Rapportutdata visas i ett cmd.exe-fönster.

> [!NOTE]
> Du har möjlighet att redigera T-SQL för att lägga till en ledande **#** till tabellnamnen, vilket skapar dem som temporära tabeller i **tempdb**. Det kan vara användbart i visningssyfte när ingen testdatabas är tillgänglig. Temporära tabeller tas bort automatiskt när anslutningen avbryts. Referenser till sekundärnycklar tillämpas inte för temporära tabeller.
>

<a name="cs_1_connect"/>
### <a name="c-block-1-connect-by-using-adonet"></a>C#-block 1: Ansluta med hjälp av ADO.NET

- [Nästa](#cs_2_createtables)


```csharp
using System;
using System.Data.SqlClient;   // System.Data.dll 
//using System.Data;           // For:  SqlDbType , ParameterDirection

namespace csharp_db_test
{
   class Program
   {
      static void Main(string[] args)
      {
         try
         {
            var cb = new SqlConnectionStringBuilder();
            cb.DataSource = "your_server.database.windows.net";
            cb.UserID = "your_user";
            cb.Password = "your_password";
            cb.InitialCatalog = "your_database";

            using (var connection = new SqlConnection(cb.ConnectionString))
            {
               connection.Open();

               Submit_Tsql_NonQuery(connection, "2 - Create-Tables",
                  Build_2_Tsql_CreateTables());

               Submit_Tsql_NonQuery(connection, "3 - Inserts",
                  Build_3_Tsql_Inserts());

               Submit_Tsql_NonQuery(connection, "4 - Update-Join",
                  Build_4_Tsql_UpdateJoin(),
                  "@csharpParmDepartmentName", "Accounting");

               Submit_Tsql_NonQuery(connection, "5 - Delete-Join",
                  Build_5_Tsql_DeleteJoin(),
                  "@csharpParmDepartmentName", "Legal");

               Submit_6_Tsql_SelectEmployees(connection);
            }
         }
         catch (SqlException e)
         {
            Console.WriteLine(e.ToString());
         }
         Console.WriteLine("View the report output here, then press any key to end the program...");
         Console.ReadKey();
      }
```


<a name="cs_2_createtables"/>
### <a name="c-block-2-t-sql-to-create-tables"></a>C#-block 2: T-SQL för att skapa tabeller

- [Föregående](#cs_1_connect) &nbsp; / &nbsp; [Nästa](#cs_3_insert)

```csharp
      static string Build_2_Tsql_CreateTables()
      {
         return @"
DROP TABLE IF EXISTS tabEmployee;
DROP TABLE IF EXISTS tabDepartment;  -- Drop parent table last.


CREATE TABLE tabDepartment
(
   DepartmentCode  nchar(4)          not null
      PRIMARY KEY,
   DepartmentName  nvarchar(128)     not null
);

CREATE TABLE tabEmployee
(
   EmployeeGuid    uniqueIdentifier  not null  default NewId()
      PRIMARY KEY,
   EmployeeName    nvarchar(128)     not null,
   EmployeeLevel   int               not null,
   DepartmentCode  nchar(4)              null
      REFERENCES tabDepartment (DepartmentCode)  -- (REFERENCES would be disallowed on temporary tables.)
);
";
      }
```

#### <a name="entity-relationship-diagram-erd"></a>Entitetsambandsdiagram (ERD)

Föregående CREATE TABLE-instruktioner som innehåller nyckelordet **REFERENCES** för att skapa en *sekundärnyckelrelation* mellan två tabeller.  Om du använder tempdb kommenterar du ut `--REFERENCES`-nyckelordet med hjälp av ett par inledande tankstreck.

Här följer en ERD som visar relationen mellan de två tabellerna. Värdena i den *underordnade* kolumnen #tabEmployee.DepartmentCode är begränsade till värdena som finns i den *överordnade* kolumnen #tabDepartment.Department.

![ERD som visar sekundärnyckel](./media/sql-database-csharp-adonet-create-query-2/erd-dept-empl-fky-2.png)


<a name="cs_3_insert"/>
### <a name="c-block-3-t-sql-to-insert-data"></a>C#-block 3: T-SQL för att infoga data

- [Föregående](#cs_2_createtables) &nbsp; / &nbsp; [Nästa](#cs_4_updatejoin)


```csharp
      static string Build_3_Tsql_Inserts()
      {
         return @"
-- The company has these departments.
INSERT INTO tabDepartment
   (DepartmentCode, DepartmentName)
      VALUES
   ('acct', 'Accounting'),
   ('hres', 'Human Resources'),
   ('legl', 'Legal');

-- The company has these employees, each in one department.
INSERT INTO tabEmployee
   (EmployeeName, EmployeeLevel, DepartmentCode)
      VALUES
   ('Alison'  , 19, 'acct'),
   ('Barbara' , 17, 'hres'),
   ('Carol'   , 21, 'acct'),
   ('Deborah' , 24, 'legl'),
   ('Elle'    , 15, null);
";
      }
```


<a name="cs_4_updatejoin"/>
### <a name="c-block-4-t-sql-to-update-join"></a>C#-block 4: T-SQL för update-join

- [Föregående](#cs_3_insert) &nbsp; / &nbsp; [Nästa](#cs_5_deletejoin)


```csharp
      static string Build_4_Tsql_UpdateJoin()
      {
         return @"
DECLARE @DName1  nvarchar(128) = @csharpParmDepartmentName;  --'Accounting';


-- Promote everyone in one department (see @parm...).
UPDATE empl
   SET
      empl.EmployeeLevel += 1
   FROM
      tabEmployee   as empl
      INNER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   WHERE
      dept.DepartmentName = @DName1;
";
      }
```


<a name="cs_5_deletejoin"/>
### <a name="c-block-5-t-sql-to-delete-join"></a>C#-block 5: T-SQL för delete-join

- [Föregående](#cs_4_updatejoin) &nbsp; / &nbsp; [Nästa](#cs_6_selectrows)


```csharp
      static string Build_5_Tsql_DeleteJoin()
      {
         return @"
DECLARE @DName2  nvarchar(128);
SET @DName2 = @csharpParmDepartmentName;  --'Legal';


-- Right size the Legal department.
DELETE empl
   FROM
      tabEmployee   as empl
      INNER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   WHERE
      dept.DepartmentName = @DName2

-- Disband the Legal department.
DELETE tabDepartment
   WHERE DepartmentName = @DName2;
";
      }
```



<a name="cs_6_selectrows"/>
### <a name="c-block-6-t-sql-to-select-rows"></a>C#-block 6: T-SQL för att välja rader

- [Föregående](#cs_5_deletejoin) &nbsp; / &nbsp; [Nästa](#cs_6b_datareader)


```csharp
      static string Build_6_Tsql_SelectEmployees()
      {
         return @"
-- Look at all the final Employees.
SELECT
      empl.EmployeeGuid,
      empl.EmployeeName,
      empl.EmployeeLevel,
      empl.DepartmentCode,
      dept.DepartmentName
   FROM
      tabEmployee   as empl
      LEFT OUTER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   ORDER BY
      EmployeeName;
";
      }
```


<a name="cs_6b_datareader"/>
### <a name="c-block-6b-executereader"></a>C#-block 6b: ExecuteReader

- [Föregående](#cs_6_selectrows) &nbsp; / &nbsp; [Nästa](#cs_7_executenonquery)

Den här metoden är utformad för att köra T-SQL SELECT-instruktionen som skapas genom metoden **Build_6_Tsql_SelectEmployees**.


```csharp
      static void Submit_6_Tsql_SelectEmployees(SqlConnection connection)
      {
         Console.WriteLine();
         Console.WriteLine("=================================");
         Console.WriteLine("Now, SelectEmployees (6)...");

         string tsql = Build_6_Tsql_SelectEmployees();

         using (var command = new SqlCommand(tsql, connection))
         {
            using (SqlDataReader reader = command.ExecuteReader())
            {
               while (reader.Read())
               {
                  Console.WriteLine("{0} , {1} , {2} , {3} , {4}",
                     reader.GetGuid(0),
                     reader.GetString(1),
                     reader.GetInt32(2),
                     (reader.IsDBNull(3)) ? "NULL" : reader.GetString(3),
                     (reader.IsDBNull(4)) ? "NULL" : reader.GetString(4));
               }
            }
         }
      }
```


<a name="cs_7_executenonquery"/>
### <a name="c-block-7-executenonquery"></a>C#-block 7: ExecuteNonQuery

- [Föregående](#cs_6b_datareader) &nbsp; / &nbsp; [Nästa](#cs_8_output)

Den här metoden anropas för åtgärder som ändrar datainnehållet i tabeller utan att returnera några rader med data.


```csharp
      static void Submit_Tsql_NonQuery(
         SqlConnection connection,
         string tsqlPurpose,
         string tsqlSourceCode,
         string parameterName = null,
         string parameterValue = null
         )
      {
         Console.WriteLine();
         Console.WriteLine("=================================");
         Console.WriteLine("T-SQL to {0}...", tsqlPurpose);

         using (var command = new SqlCommand(tsqlSourceCode, connection))
         {
            if (parameterName != null)
            {
               command.Parameters.AddWithValue(  // Or, use SqlParameter class.
                  parameterName,
                  parameterValue);
            }
            int rowsAffected = command.ExecuteNonQuery();
            Console.WriteLine(rowsAffected + " = rows affected.");
         }
      }
   } // EndOfClass
}
```


<a name="cs_8_output"/>
### <a name="c-block-8-actual-test-output-to-the-console"></a>C#-block 8: Faktiska testutdata till konsolen

- [Föregående](#cs_7_executenonquery)

Det här avsnittet innehåller de utdata som skickades till konsolen. Endast guid-värdena varierar mellan testkörningar.


```text
[C:\csharp_db_test\csharp_db_test\bin\Debug\]
>> csharp_db_test.exe

=================================
Now, CreateTables (10)...

=================================
Now, Inserts (20)...

=================================
Now, UpdateJoin (30)...
2 rows affected, by UpdateJoin.

=================================
Now, DeleteJoin (40)...

=================================
Now, SelectEmployees (50)...
0199be49-a2ed-4e35-94b7-e936acf1cd75 , Alison , 20 , acct , Accounting
f0d3d147-64cf-4420-b9f9-76e6e0a32567 , Barbara , 17 , hres , Human Resources
cf4caede-e237-42d2-b61d-72114c7e3afa , Carol , 22 , acct , Accounting
cdde7727-bcfd-4f72-a665-87199c415f8b , Elle , 15 , NULL , NULL

[C:\csharp_db_test\csharp_db_test\bin\Debug\]
>>
```
