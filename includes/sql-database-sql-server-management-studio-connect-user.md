## <a name="connect-to-azure-sql-database-as-a-user"></a>Anslut till SQL Database som en användare
Använd följande steg för att ansluta till Azure SQL Database med SSMS som en användare.

1. Skriv ”Microsoft SQL Server Management Studio” i Windows-sökrutan och klicka sedan på skrivbordsappen för att starta SSMS.
2. I Anslut till server-fönstret anger du följande information:

* **Servertyp**: standardvärdet är databasmotor, ändra inte det värdet.
  
  * **Servernamn**: Ange namnet på servern som är värd för din SQL-databas i följande format: *&lt;servernamn>*.**database.windows.net**
  * **Autentiseringstyp**: om du precis har kommit igång, välj SQL-autentisering. Om du har aktiverat Active Directory för din logiska SQL Database-server, kan du antingen välja Active Directory-lösenordsautentisering eller Active Directory-integrerad autentisering.
  * **Användarnamn**: om du antingen valt SQL-autentisering eller Active Directory-lösenordsautentisering, anger du namnet på en användare med åtkomst till en databas på servern.
  * **Lösenord**: om du valt antingen SQL-autentisering eller Active Directory-lösenordsautentisering, anger du lösenordet för den angivna användaren.
    
       ![SQL Server Management Studio: Anslut till en SQL Database-server](./media/sql-database-sql-server-management-studio-connect-user/connect-user-1.png)

1. Klicka på **Alternativ** för att ange vilken databas du vill ansluta till.
   
      ![SQL Server Management Studio: Anslut till en SQL Database-server](./media/sql-database-sql-server-management-studio-connect-user/connect-user-2.png)
2. I **Anslut till databas**-fönstret väljer du den databas du vill ansluta till.
   
     ![SQL Server Management Studio: Anslut till en SQL Database-server](./media/sql-database-sql-server-management-studio-connect-user/connect-user-3.png)
3. Klicka på **Anslut**.
4. Om din klients IP-adress inte har åtkomst till den logiska SQL Database-servern, ombes du logga in på ett Azure-konto och skapa en brandväggsregel på servernivå. Om du är administratör för en Azure-prenumeration, klickar du på **Logga in** för att skapa en brandväggsregel på servernivå. Om så inte är fallet, be en administratör att antingen skapa en brandväggsregel på servernivå eller en brandväggsregel på databasnivå i den databas som du försöker ansluta till.
   
      ![SQL Server Management Studio: Anslut till en SQL Database-server](./media/sql-database-sql-server-management-studio-connect-user/connect-user-4.png)
5. Om dina autentiseringsuppgifter ger dig åtkomst till den angivna databasen, öppnas Object Explorer och du kan utföra administrativa åtgärder eller fråga efter data beroende på användarbehörigheter.
   
      ![SQL Server Management Studio: Anslut till en SQL Database-server](./media/sql-database-sql-server-management-studio-connect-user/connect-user-5.png)

## <a name="troubleshoot-connection-failures"></a>Felsök anslutningsfel
Den vanligaste orsaken för anslutningsfel är fel i servernamnet (kom ihåg att <*servernamn*> är namnet på den logiska servern, inte databasen), användarnamnet eller lösenordet, eller att servern inte tillåter anslutningar av säkerhetsskäl. 



<!--HONumber=Nov16_HO2-->


