
<a name="cs_0_csharpprogramexample_h2"/>

## <a name="c-program-example"></a>C#-program-exempel

I nästa avsnitt i den här artikeln finns ett C#-program som använder ADO.NET för att skicka Transact-SQL-uttryck till SQL-databasen. C# utförs följande åtgärder:

1. [Ansluter till vår SQL-databas med hjälp av ADO.NET](#cs_1_connect).
2. [Skapar tabeller](#cs_2_createtables).
3. [Fyller tabeller med data, genom att utfärda T-SQL INSERT-instruktioner](#cs_3_insert).
4. [Uppdaterar data med hjälp av en koppling](#cs_4_updatejoin).
5. [Tar bort data med hjälp av en koppling](#cs_5_deletejoin).
6. [Väljer datarader med hjälp av en koppling](#cs_6_selectrows).
7. Stänger anslutningen (som utelämnar eventuella temporära tabeller från tempdb).

C#-programmet innehåller:

- C#-kod för att ansluta till databasen.
- Metoder som returnerar T-SQL-källkoden.
- Två metoder som skickar T-SQL i databasen.

#### <a name="to-compile-and-run"></a>Att kompilera och köra

Den här C#-program är logiskt en .cs fil. Men det här programmet fysiskt är uppdelad i flera kodblock att göra det lättare att förstå och varje block. För att kompilera och köra det här programmet måste göra följande:

1. Skapa ett C#-projekt i Visual Studio.
    - Projekttypen ska vara en *konsolen* program från något som liknar följande hierarki: **mallar** > **Visual C#** >  **Klassiska Windows-skrivbordet** > **konsolen App (.NET Framework)**.
3. I filen **Program.cs**, radera små starter rader med kod.
3. I Program.cs, kopiera och klistra in var och en av de följande blocken i samma ordning som de visas här.
4. I Program.cs redigera följande värden i den **Main** metoden:

   - **CB. DataSource**
   - **CD: n. Användar-ID**
   - **CB. Lösenord**
   - **InitialCatalog**

5. Kontrollera att sammansättningen **System.Data.dll** refererar till. Om du vill kontrollera, expandera den **referenser** nod i den **Solution Explorer** fönstret.
6. Om du vill bygga program i Visual Studio klickar du på den **skapa** menyn.
7. Om du vill köra programmet från Visual Studio klickar du på den **starta** knappen. Rapportutdata visas i ett fönster för cmd.exe.

> [!NOTE]
> Du har möjlighet att redigera T-SQL för att lägga till en inledande  **#**  tabellnamnen, vilket skapar dem som temporära tabeller i **tempdb**. Detta kan vara användbart i demonstrationssyfte, när ingen testdatabasen är tillgänglig. Temporära tabeller tas bort automatiskt när anslutningen stängs. Alla referenser till sekundärnycklar tillämpas inte för temporära tabeller.
>

<a name="cs_1_connect"/>
### <a name="c-block-1-connect-by-using-adonet"></a>C#-blocket 1: ansluta med hjälp av ADO.NET

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
### <a name="c-block-2-t-sql-to-create-tables"></a>C# block 2: T-SQL för att skapa tabeller

- [Tidigare](#cs_1_connect) &nbsp;  /  &nbsp; [nästa](#cs_3_insert)

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

#### <a name="entity-relationship-diagram-erd"></a>Diagram för entiteten relation (ERD)

Föregående CREATE TABLE-instruktioner omfattar den **referenser** nyckelord för att skapa en *sekundärnyckeln* (FK) relation mellan två tabeller.  Om du använder tempdb kommentera ut den `--REFERENCES` nyckelordet med ett par med inledande streck.

Nästa är en Reparationsdiskett som visar relationen mellan två tabeller. Värdena i #tabEmployee.DepartmentCode *underordnade* kolumnen är begränsade till värdena som finns i #tabDepartment.Department *överordnade* kolumn.

![ERD visar sekundärnyckeln](./media/sql-database-csharp-adonet-create-query-2/erd-dept-empl-fky-2.png)


<a name="cs_3_insert"/>
### <a name="c-block-3-t-sql-to-insert-data"></a>C# block 3: T-SQL infoga data

- [Tidigare](#cs_2_createtables) &nbsp;  /  &nbsp; [nästa](#cs_4_updatejoin)


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
### <a name="c-block-4-t-sql-to-update-join"></a>C# block 4: T-SQL för update-kopplingen

- [Tidigare](#cs_3_insert) &nbsp;  /  &nbsp; [nästa](#cs_5_deletejoin)


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
### <a name="c-block-5-t-sql-to-delete-join"></a>C# block 5: T-SQL för att ta bort koppling

- [Tidigare](#cs_4_updatejoin) &nbsp;  /  &nbsp; [nästa](#cs_6_selectrows)


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
### <a name="c-block-6-t-sql-to-select-rows"></a>C# block 6: T-SQL för att markera rader

- [Tidigare](#cs_5_deletejoin) &nbsp;  /  &nbsp; [nästa](#cs_6b_datareader)


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
### <a name="c-block-6b-executereader"></a>C# block 6b: ExecuteReader

- [Tidigare](#cs_6_selectrows) &nbsp;  /  &nbsp; [nästa](#cs_7_executenonquery)

Den här metoden är utformat för att köra T-SQL SELECT-uttrycket som skapats av den **Build_6_Tsql_SelectEmployees** metod.


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
### <a name="c-block-7-executenonquery"></a>C# block 7: ExecuteNonQuery

- [Tidigare](#cs_6b_datareader) &nbsp;  /  &nbsp; [nästa](#cs_8_output)

Den här metoden anropas för åtgärder som ändrar datainnehållet i tabeller utan att returnera alla rader med data.


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
### <a name="c-block-8-actual-test-output-to-the-console"></a>C# block 8: faktiska testet av utdata till konsolen

- [Tidigare](#cs_7_executenonquery)

Det här avsnittet innehåller de utdata som skickades till konsolen. Endast guid-värdena variera mellan testkörningar.


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
