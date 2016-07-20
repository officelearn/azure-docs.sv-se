1. Logga in på [Azure Portal].

2. Klicka på **+NY** > **Webb + Mobil** > **Mobilapp** och ange sedan ett namn för din serverdel för mobilappen.

3. För **Resursgrupp** väljer du en befintlig resursgrupp, eller skapar en ny (med samma namn som din app). 
 

 Du kan antingen välja en annan App Service-plan eller skapa en ny. Mer information om App Service-planer och hur du skapar en ny plan i en annan prisnivå på din önskade plats finns i [Djupgående översikt över Azure App Service-planer](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

4. För **App Service-plan** är standardplanen (i [standardnivån](https://azure.microsoft.com/pricing/details/app-service/)) vald. Du kan också välja en annan plan eller [skapa en ny](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan). App Service-planens inställningar avgör [plats, funktioner, kostnad och beräkningsresurser](https://azure.microsoft.com/pricing/details/app-service/) som associeras med din app. 

    Efter att du har valt planen klickar du på **Skapa**. Serverdelen för mobilappen skapas. 
    
6. På **Inställningar**-bladet för den nya mobilappen klickar du på **Snabbstart** > din klientapplattform > **Anslut en databas**. 

    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)

7. På **Lägg till dataanslutning**-bladet klickar du på **SQL Database** > **Skapa en ny databas**, anger **Namn** på databasen, väljer en prisnivå och klickar sedan på **Server**.  Du kan återanvända den nya databasen. Om du redan har en databas på samma plats kan du istället välja **Använd en befintlig databas**. Det rekommenderas inte att använda en databas på en annan plats, på grund av bandbreddskostnader och högre latens.
 
    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)

8. På **Ny server**-bladet anger du ett unikt servernamn i fältet **Servernamn**, anger ett inloggningsnamn och lösenord, markerar **Ge Azure-tjänster åtkomst till servern** och klickar på **OK**. Den nya databasen skapas.

9. Tillbaka på **Lägg till dataanslutning**-bladet klickar du på **Anslutningssträng**, anger inloggningsnamn och lösenord för din databas och klickar på **OK**. Vänta några minuter på att databasen distribueras innan du fortsätter.

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/



<!--HONumber=Jun16_HO2-->


