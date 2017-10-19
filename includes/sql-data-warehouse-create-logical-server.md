### <a name="create-a-new-logical-sql-server-in-the-azure-portal"></a>Skapa en ny logisk SQL-server på Azure Portal

1. Klicka på **Nytt**, sök efter **logisk server** och tryck på **Retur**.

    ![sök efter logisk server](./media/sql-data-warehouse-create-logical-server/search-logical-server.png)
2. Välj **SQL-server (logisk server)** 

    ![välj logisk server](./media/sql-data-warehouse-create-logical-server/select-logical-server.png)
  
3. Öppna bladet för den nya SQL-servern (logisk server) genom att klicka på **Skapa**.

   <kbd> ![öppna bladet logisk server](./media/sql-data-warehouse-create-logical-server/open-logical-server-blade.png) </kbd> <kbd>![bladet logisk server](./media/sql-data-warehouse-create-logical-server/logical-server-blade.png) </kbd>
  
3. Ange ett giltigt namn för den nya logiska servern i rutan för servernamn på bladet SQL Server (logisk server). En grön kryssmarkering visar att du har angett ett giltigt namn.
    
    ![Namn på ny server](./media/sql-data-warehouse-create-logical-server/new-name-logical-server.png)

    > [!IMPORTANT]
    > Det fullständigt kvalificerade namnet för den nya servern är <ditt_servernamn>. database.windows.net.
    >
    
4. I textrutan Inloggning för serveradministratör anger du ett användarnamn för SQL-autentiseringsinloggningen för den här servern. Den här inloggningen kallas den primära server-inloggningen. En grön kryssmarkering visar att du har angett ett giltigt namn.
    
    ![SQL-administratörsinloggning](./media/sql-data-warehouse-create-logical-server/sql-admin-login.png)
5. I textrutorna **Lösenord** och **Bekräfta lösenord** anger du ett lösenord för inloggningskontot för serverhuvudobjektet. En grön kryssmarkering visar att du har angett ett giltigt lösenord.
    
    ![SQL-administratörslösenord](./media/sql-data-warehouse-create-logical-server/sql-admin-password.png)
6. Välj en prenumeration som du har behörighet att skapa objekt i.

    ![prenumeration](./media/sql-data-warehouse-create-logical-server/subscription.png)
7. I textrutan Resursgrupp väljer du **Skapa nytt** och anger sedan ett giltigt namn för den nya resursgruppen i textrutan Resursgrupp. (Du kan också använda en befintlig resursgrupp om du redan har skapat en.) En grön kryssmarkering visar att du har angett ett giltigt namn.

    ![Ny resursgrupp](./media/sql-data-warehouse-create-logical-server/new-resource-group.png)

8. I textrutan **Plats** väljer du ett lämpligt datacenter för din plats, t.ex. ”Australien, östra”.
    
    ![Serverplats](./media/sql-data-warehouse-create-logical-server/server-location.png)
    
    > [!TIP]
    > Du kan inte ändra kryssrutan för **Ge Azure-tjänster åtkomst till servern** på det här bladet. Du kan ändra den här inställningen på bladet Serverbrandvägg. Mer information finns i [Get started with security](../articles/sql-database/sql-database-manage-servers-portal.md) (Komma igång med säkerhet).
    >
    
9. Klicka på **Skapa**.

    ![Knappen Skapa](./media/sql-data-warehouse-create-logical-server/create.png)

