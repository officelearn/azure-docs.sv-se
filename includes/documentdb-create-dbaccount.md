1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. I Jumpbar klickar du på **Ny**, **Data + lagring** och sedan på **DocumentDB (NoSQL)**.
   
   ![Skärmbild av Azure Portal, med fokus på Fler tjänster och DocumentDB (NoSQL)](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)  
3. På **Nytt konto**-bladet anger du önskad konfiguration för DocumentDB-kontot.
   
    ![Skärmbild av Nytt DocumentDB-bladet](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-2.png)

    - I **ID**-rutan, anger du ett namn för att identifiera DocumentDB-kontot.  När **ID**:n har verifierats, visas en grön kryssmarkering i **ID**-rutan. **ID**-värdet blir värdnamnet inom URI:n. **ID**:n får bara innehålla gemener, siffror och bindestreck och måste vara mellan 3 och 50 tecken. Observera att *documents.azure.com* läggs till till slutpunktsnamnet du väljer, och resultatet blir slutpunkten för ditt DocumentDB-konto.

    - För **Prenumeration** väljer du den Azure-prenumeration som du vill ange för DocumentDB-kontot. Om ditt konto bara har en prenumeration så väljs det kontot som standard.

    - I **Resursgrupp** väljer eller skapar du en resursgrupp för ditt DocumentDB-konto.  Som standard skapas en ny resursgrupp. Mer information finns i [Using the Azure portal to manage your Azure resources](../articles/azure-portal/resource-group-portal.md) (Hantera Azure-resurser med hjälp av Azure Portal).

    - Använd **Plats** för att ange den geografiska platsen där du vill att DocumentDB-kontots värd finns. 

    - För att ge bekväm åtkomst till ditt konto och de resurser som du skapar i framtiden, kan du markera **Fäst på instrumentpanelen**.  

1. När de nya DocumentDB-kontoalternativen har konfigurerats, klickar du på **Skapa**. Du kan kontrollera distributionens status genom att övervaka förloppet på Startsidan.  
   ![Skärmbild som visar Skapa-panelen på Startsidan – online-databasskaparen](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-3.png)  
   
   Du kan också övervaka förloppet från Meddelandehubben.  
   
   ![Skapa databaser snabbt – Skärmbild av Meddelandehubben som visar att DocumentDb-kontot håller på att skapas](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-4.png)  
   
   ![Skärmbild av Meddelandehubben som visar att DocumentDB-kontot har skapats och distribuerats till en resursgrupp – Meddelande från online-databasskaparen](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-5.png)
2. När DocumentDB-kontot har skapats är det redo att användas med standardinställningarna. Observera att standardkonsekvensen för DocumentDB-kontot har ställts in på **Session**.  Du kan justera standardkonsekvensen genom att klicka på **Standardkonsekvens** på resursmenyn. Mer information om de konsekvensnivåer som finns för DocumentDB finns i [Konsekvensnivåer i DocumentDB](../articles/azure-portal/resource-group-portal.md).
   
   ![Skärmbild av Resursgrupp-bladet – starta apputveckling](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-6.png)  
   
   ![Skärmbild av Konsekvensnivå-bladet – sessionskonsekvens](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-7.png)  

[Så här skapar du ett DocumentDB-konto]: #Howto
[Nästa steg]: #NextSteps
[documentdb-manage]:../articles/documentdb/documentdb-manage.md



<!--HONumber=sep16_HO1-->


