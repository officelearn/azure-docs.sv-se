1. Logga in på [Azure Portal](https://portal.azure.com/) i ett nytt fönster.
2. I snabbåtkomstfältet klickar du på **Ny**, **Databaser** och sedan på **NoSQL (DocumentDB)**.
   
   ![Skärmbild av Azure Portal, med fokus på Fler tjänster och DocumentDB (NoSQL)](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)  
3. På **Nytt konto**-bladet anger du önskad konfiguration för DocumentDB-kontot.
   
    ![Skärmbild av Nytt DocumentDB-bladet](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-2.png)
   
   * I **ID**-rutan, anger du ett namn för att identifiera DocumentDB-kontot.  När **ID**:n har verifierats, visas en grön kryssmarkering i **ID**-rutan. **ID**-värdet blir värdnamnet inom URI:n. **ID**:n får bara innehålla gemener, siffror och bindestreck och måste vara mellan 3 och 50 tecken. Observera att *documents.azure.com* läggs till i slutpunktsnamnet du väljer, och resultatet blir slutpunkten för ditt DocumentDB-konto.
   * I rutan **NoSQL API** väljer du **DocumentDB**.  
   * För **Prenumeration** väljer du den Azure-prenumeration som du vill ange för DocumentDB-kontot. Om ditt konto bara har en prenumeration så väljs det kontot som standard.
   * I **Resursgrupp** väljer eller skapar du en resursgrupp för ditt DocumentDB-konto.  Som standard skapas en ny resursgrupp. Mer information finns i [Using the Azure portal to manage your Azure resources](../articles/azure-portal/resource-group-portal.md) (Hantera Azure-resurser med hjälp av Azure Portal).
   * Använd **Plats** för att ange den geografiska platsen där du vill att DocumentDB-kontots värd ska finnas. 
4. När de nya DocumentDB-kontoalternativen har konfigurerats, klickar du på **Skapa**. Kontrollera meddelandehubben för att se statusen för distributionen.  
   
   ![Skapa databaser snabbt – Skärmbild av Meddelandehubben som visar att DocumentDB-kontot håller på att skapas](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-4.png)  
   
   ![Skärmbild av Meddelandehubben som visar att DocumentDB-kontot har skapats och distribuerats till en resursgrupp – Meddelande från online-databasskaparen](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-5.png)
5. När DocumentDB-kontot har skapats är det redo att användas med standardinställningarna. Om du vill granska standardinställningarna klickar du på ikonen för **NoSQL (DocumentDB)** i snabbåtkomstfältet, klickar på det nya kontot och sedan på **Standardkonsekvens** på Resurs-menyn.

   ![Skärmbild som visar hur du öppnar ditt Azure DocumentDB-databaskonto på Azure Portal](./media/documentdb-create-dbaccount/azure-documentdb-database-open-account-portal.png)  

   Standardkonsekvensen för DocumentDB-kontot har angetts till **Session**.  Du kan justera standardkonsekvensen genom att välja ett av de andra alternativen för konsekvenskontroll som finns. Mer information om de konsekvensnivåer som finns för DocumentDB finns i [Konsekvensnivåer i DocumentDB](../articles/documentdb/documentdb-consistency-levels.md).

[How to: Create a DocumentDB account]: #Howto
[Next steps]: #NextSteps
[documentdb-manage]:../articles/documentdb/documentdb-manage.md


<!--HONumber=Jan17_HO1-->


