

## Anslut till Azure SQL Database med en huvudsaklig inloggning på servernivå

Använd följande steg för att ansluta till Azure SQL Database med SSMS med en huvudsaklig inloggning på servernivå.

1. Skriv ”Microsoft SQL Server Management Studio” i Windows-sökrutan och klicka sedan på skrivbordsappen för att starta SSMS.

2. I Anslut till server-fönstret anger du följande information:

 - **Servertyp**: standardvärdet är databasmotor, ändra inte det värdet.
 - **Servernamn**: Ange namnet på servern som är värd för din SQL-databas i följande format: *&lt;servernamn>*.**database.windows.net**
 - **Autentiseringstyp**: om du precis har kommit igång, välj SQL-autentisering. Om du har aktiverat Active Directory för din logiska SQL Database-server, kan du antingen välja Active Directory-lösenordsautentisering eller Active Directory-integrerad autentisering.
 - **Användarnamn**: om du antingen valt SQL-autentisering eller Active Directory-lösenordsautentisering, anger du namnet på en användare med åtkomst till en databas på servern.
 - **Lösenord**: om du valt antingen SQL-autentisering eller Active Directory-lösenordsautentisering, anger du lösenordet för den angivna användaren.
   
       ![SQL Server Management Studio: Connect to SQL Database server](./media/sql-database-sql-server-management-studio-connect-server-principal/connect-server-principal-1.png)

3. Klicka på **Anslut**.
 
4. Om din klients IP-adress inte har åtkomst till den logiska SQL Database-servern, ombes du logga in på ett Azure-konto och skapa en brandväggsregel på servernivå. Om du är administratör för en Azure-prenumeration, klickar du på **Logga in** för att skapa en brandväggsregel på servernivå. I annat fall måste du be en Azure-administratör att skapa en brandväggsregel på servernivå.
 
      ![SQL Server Management Studio: Anslut till en SQL Database-server](./media/sql-database-sql-server-management-studio-connect-server-principal/connect-server-principal-2.png)
 
1. Om du är administratör för en Azure-prenumeration och behöver logga in, anger du autentiseringsuppgifterna för din prenumeration och loggar in när inloggningssidan visas.

      ![logga in](./media/sql-database-sql-server-management-studio-connect-server-principal/connect-server-principal-3.png)
 
1. Efter att du loggat in på Azure, kan du granska den föreslagna brandväggsregeln på servernivå (du kan ändra den så den tillåter ett IP-adressintervall). Klicka sedan på **OK** för att skapa brandväggsregeln och slutföra anslutningen till SQL Database.
 
      ![ny brandvägg på servernivå](./media/sql-database-sql-server-management-studio-connect-server-principal/connect-server-principal-4.png)
 
5. Om dina autentiseringsuppgifter ger dig åtkomst, öppnas Object Explorer och du kan utföra administrativa åtgärder eller fråga efter data. 
 
     ![ny brandvägg på servernivå](./media/sql-database-sql-server-management-studio-connect-server-principal/connect-server-principal-5.png)
 
     
## Felsök anslutningsfel

Den vanligaste orsaken för anslutningsfel är fel i servernamnet (kom ihåg att <*servernamn*> är namnet på den logiska servern, inte databasen), användarnamnet eller lösenordet, eller att servern inte tillåter anslutningar av säkerhetsskäl. 





<!--HONumber=Jun16_HO2-->


