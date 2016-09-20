

## Ge den nya databasanvändaren db_owner-behörigheter

Använd följande steg för att ge en befintlig databasanvändare db_owner-behörigheter

De här stegen förutsätter att du är ansluten till SQL Database i Object Explorer i SSMS och är ansluten till din logiska SQL Database-server som en huvudsaklig administratör på servernivå eller med ett användarkonto med behörigheter att ge användarbehörigheter. 

1. Expandera noden Databaser i Object Explorer och markera den databas som innehåller användaren som du vill ge dbo-behörigheter till.

     ![SQL Server Management Studio: Anslut till en SQL Database-server](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-1.png)

2. Högerklicka på den valda databasen och klicka sedan på **Fråga**.

     ![SQL Server Management Studio: Anslut till en SQL Database-server](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-2.png)

3. I frågefönstret redigerar du och använder följande Transact-SQL-instruktion för att ge dbo-behörigheter till en specifik användare. 

    ```ALTER ROLE db_owner ADD MEMBER user1;
    ```

     ![SQL Server Management Studio: Connect to SQL Database server](./media/sql-database-grant-database-user-dbo-permissions/sql-database-grant-database-user-dbo-permissions-1.png)





<!--HONumber=sep16_HO1-->


