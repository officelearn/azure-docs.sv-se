
<!--
includes/sql-database-create-new-server-firewall-portal.md

Latest Freshness check:  2016-11-28 , rickbyh.

As of circa 2016-04-11, the following topics might include this include:
articles/sql-database/sql-database-get-started.md
articles/sql-database/sql-database-configure-firewall-settings
articles/sql-data-warehouse-get-started-provision.md

-->
## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Skapa en brandväggsregel på servernivå på Azure Portal

1. Öppna bladet Brandvägg för SQL-servern genom att klicka på **Brandvägg** under Inställningar på bladet SQL-server.

    ![Brandvägg för SQL-server](../articles/sql-database/media/sql-database-get-started/sql-server-firewall.png)
    
2. Granska klient-IP-adressen som visas och använd valfri webbläsare för att kontrollera att det här är din IP-adress på Internet (fråga ”what is my IP adress”). Ibland överensstämmer de inte av olika anledningar.

    ![Din IP-adress](../articles/sql-database/media/sql-database-get-started/your-ip-address.png)

3. Om IP-adresserna matchar varandra klickar du på **Lägg till klient-IP** i verktygsfältet.

    ![Lägg till klient-IP](../articles/sql-database/media/sql-database-get-started/add-client-ip.png)

    > [!NOTE]
    > Du kan öppna SQL Database-brandväggen på servern för en enskild IP-adress eller ett helt adressintervall. Om du öppnar brandväggen kan SQL-administratörer och användare logga in i valfri databas på servern som de har giltiga autentiseringsuppgifter för.
    >

4. Klicka på **Spara** i verktygsfältet för att spara den här brandväggsregeln på servernivå och klicka sedan på **OK**.

    ![Lägg till klient-IP](../articles/sql-database/media/sql-database-get-started/save-firewall-rule.png)

> [!Tip]
> Om du vill gå en självstudiekurs rekommenderar vi [Självstudiekurs om SQL Database: Skapa en server, en brandväggsregel på servernivå, en exempeldatabas, en brandväggsregel på databasnivå och ansluta till SQL Server](../articles/sql-database/sql-database-get-started.md).    
>


<!--HONumber=Jan17_HO1-->


