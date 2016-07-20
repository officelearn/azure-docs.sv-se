

## Skapa en ny databasanvändare med hjälp av SSMS

Använd följande steg för att skapa en ny databasanvändare i en befintlig databas med hjälp av SSMS. 

De här stegen förutsätter att du är ansluten till SQL Database i Object Explorer med hjälp av SSMS och är ansluten till din logiska SQL Database-server som en huvudsaklig administratör på servernivå eller med ett användarkonto med behörighet att skapa en ny användare. 

1. Expandera noden Databaser i Object Explorer och markera den databas som du vill skapa ett nytt användarkonto i.

     ![SQL Server Management Studio: Anslut till en SQL Database-server](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-1.png)

2. Högerklicka på den valda databasen och klicka sedan på **Fråga**.

     ![SQL Server Management Studio: Anslut till en SQL Database-server](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-2.png)

3. I frågefönstret redigerar du och använder följande Transact-SQL-instruktion för att skapa en innesluten användare i din användardatabas. 

    ```CREATE USER user1 WITH PASSWORD ='p@ssw0rd1';

     ![SQL Server Management Studio: Anslut till en SQL Database-server](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-3.png)






<!--HONumber=Jun16_HO2-->


