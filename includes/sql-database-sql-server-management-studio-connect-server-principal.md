

## <a name="connect-to-azure-sql-database-using-sql-server-authentication"></a>Anslut till Azure SQL-databasen med SQL Server-autentisering
Följande steg visar hur du ansluter till en Azure SQL-server och -databas med SSMS. Om du inte har en server och databas, se [Skapa en SQL-databas på bara några minuter](../articles/sql-database/sql-database-get-started.md) för att skapa en.

1. Starta SSMS genom att skriva **Microsoft SQL Server Management Studio** i Windows-sökrutan och klicka sedan på skrivbordsappen.
2. I fönstret **Anslut till server** anger du följande information (om SSMS redan körs, klickar du på **Anslut > Databasmotor** för att öppna fönstret **Anslut till server**):
   
   * **Servertyp**: standardvärdet är databasmotor, ändra inte det värdet.
   * **Servernamn**: Ange det fullständigt kvalificerade namnet på din Azure SQL Database-server i följande format: *&lt;servernamn >*.**database.windows.net**
   * **Autentiseringstyp**: Den här artikeln visar hur du ansluter via **SQL Server-autentisering**. Mer information om hur du ansluter med Azure Active Directory finns i [Anslut med Active Directory-integrerad autentisering](../articles/sql-database/sql-database-aad-authentication.md#connect-using-active-directory-integrated-authentication), [Anslut med Active Directory-lösenordsautentisering](../articles/sql-database/sql-database-aad-authentication.md#connect-using-active-directory-password-authentication) och [Anslut med Active Directory Universal-autentisering](../articles/sql-database/sql-database-ssms-mfa-authentication.md).
   * **Användarnamn**: Ange namnet på en användare med åtkomst till en databas på servern (till exempel den *serveradministratör* du ställer in när du skapar servern). 
   * **Lösenord**: Ange lösenordet för den angivna användaren (till exempel det *lösenord* du ställer in när du skapar servern).
     
       ![SQL Server Management Studio: Anslut till en SQL Database-server](./media/sql-database-sql-server-management-studio-connect-server-principal/connect.png)
3. Klicka på **Anslut**.
4. Som standard har nya servrar inga definierade [brandväggsregler](../articles/sql-database/sql-database-firewall-configure.md) så att klienter blockeras ursprungligen från att ansluta. Om servern ännu inte har en brandväggsregel som tillåter din specifika IP-adress att ansluta uppmanas SSMS att skapa en brandväggsregel på servernivå för dig.
   
    Klicka på **Logga in** och skapa en brandväggsregel på servernivå. Du måste vara en Azure-administratör för att skapa en brandväggsregel på servernivå.
   
       ![SQL Server Management Studio: Connect to SQL Database server](./media/sql-database-sql-server-management-studio-connect-server-principal/newfirewallrule.png)
5. När du har lyckats ansluta till din Azure SQL-databas öppnas **Object Explorer** och du kan nu komma åt databasen för att [utföra administrativa uppgifter eller fråga data](../articles/sql-database/sql-database-manage-azure-ssms.md).
   
     ![ny brandvägg på servernivå](./media/sql-database-sql-server-management-studio-connect-server-principal/connect-server-principal-5.png)

## <a name="troubleshoot-connection-failures"></a>Felsök anslutningsfel
De vanligaste orsakerna till anslutningsfel är fel i servernamnet och problem med nätverksanslutningen. Kom ihåg att <*servernamn*> är namnet på servern, inte på databasen, och du måste ange det fullständigt kvalificerade servernamnet: `<servername>.database.windows.net`

Kontrollera att användarnamn och lösenord inte innehåller några skrivfel eller extra blanksteg (användarnamn är inte skiftlägeskänsliga, men lösenord). 

Du kan också uttryckligen ange protokoll och portnummer med servernamnet enligt följande: `tcp:servername.database.windows.net,1433`

Problem med nätverksanslutningen kan även orsaka anslutningsfel och timeout. Att försöka ansluta igen (när du vet att servernamnet, autentiseringsuppgifterna och brandväggsregler är korrekta) kan leda till att det lyckas.

Mer information om anslutningsproblem finns i [Felsök, diagnostisera och förebygg SQL-anslutningsfel och tillfälliga fel i SQL-databasen](../articles/sql-database/sql-database-connectivity-issues.md).



<!--HONumber=Nov16_HO2-->


