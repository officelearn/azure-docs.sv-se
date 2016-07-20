1.  Logga in på [Microsoft Azure Portal](https://portal.azure.com/) online.
2.  I Jumpbar klickar du på **Ny**, **Data + lagring** och sedan på **Azure DocumentDB**.

    ![Skärmbild av Azure-portalen för att skapa en databas, med Ny-knappen, Data + lagring på Skapa-bladet och Azure DocumentDB i Data + lagring-bladet markerade](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)  

3. På **Nytt DocumentDB-konto**-bladet anger du önskad konfiguration för DocumentDB-kontot.

    ![Skärmbild av Nytt DocumentDB-bladet](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-2.png)


    - I **ID**-rutan, anger du ett namn för att identifiera DocumentDB-kontot.  När **ID**:n har verifierats, visas en grön kryssmarkering i **ID**-rutan. **ID**-värdet blir värdnamnet inom URI:n. **ID**:n får bara innehålla gemener, siffror och bindestreck och måste vara mellan 3 och 50 tecken. Observera att *documents.azure.com* läggs till till slutpunktsnamnet du väljer, och resultatet blir slutpunkten för ditt DocumentDB-konto.

    - För **Prenumeration** väljer du den Azure-prenumeration som du vill ange för DocumentDB-kontot. Om ditt konto bara har en prenumeration så väljs det kontot som standard.

    - I **Resursgrupp** väljer eller skapar du en resursgrupp för ditt DocumentDB-konto.  Som standard väljs en befintlig resursgrupp under Azure-prenumerationen.  Du kan dock välja att skapa en ny resursgrupp som du vill lägga till till DocumentDB-kontot. Mer information finns i [Hantera Azure-resurser med hjälp av Azure-portalen](resource-group-portal.md).

    - Använd **Plats** för att ange den geografiska platsen där du vill att DocumentDB-kontots värd finns.   

4.  När de nya DocumentDB-kontoalternativen har konfigurerats, klickar du på **Skapa**.  Det kan ta några minuter att skapa DocumentDB-kontot.  Du kan kontrollera statusen genom att övervaka förloppet på Startsidan.  
    ![Skärmbild som visar Skapa-panelen på Startsidan – online-databasskaparen](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-3.png)  

    Du kan också övervaka förloppet från Meddelandehubben.  

    ![Skapa databaser snabbt – Skärmbild av Meddelandehubben som visar att DocumentDb-kontot håller på att skapas](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-4.png)  

    ![Skärmbild av Meddelandehubben som visar att DocumentDB-kontot har skapats och distribuerats till en resursgrupp – Meddelande från online-databasskaparen](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-5.png)

5.  När du har skapat DocumentDB-kontot, är det redo för användning med standardinställningarna i onlineportalen. Observera att standardkonsekvensen för DocumentDB-kontot har ställts in på **Session**.  Du kan justera standardinställningen för konsekvens genom att klicka på ikonen **Inställningar** överst i kommandofältet och sedan klicka på **Standardkonsekvens**-posten under **Funktion** på bladet **Alla inställningar**.

    ![Skärmbild av Resursgrupp-bladet – starta apputveckling](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-6.png)  

    ![Skärmbild av Konsekvensnivå-bladet – sessionskonsekvens](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-7.png)  

[Så här skapar du ett DocumentDB-konto]: #Howto
[Nästa steg]: #NextSteps
[documentdb-manage]:../articles/documentdb/documentdb-manage.md



<!--HONumber=Jun16_HO2-->


