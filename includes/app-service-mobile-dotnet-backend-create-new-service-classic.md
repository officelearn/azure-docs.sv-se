1. Logga in på den [Azure Portal].
2. Välj **+ ny** > **webb + mobilt** > **Mobilapp**, och sedan ange ett namn för Mobile Apps-serverdel.
3. För **resursgrupp**, Välj en befintlig resursgrupp eller skapa en ny (med samma namn som din app). 
4. För **apptjänstplan**, standardplanen (i den [standardnivån](https://azure.microsoft.com/pricing/details/app-service/)) är markerad. Du kan också välja en annan plan eller [skapa en ny](../articles/app-service/app-service-plan-manage.md#create-an-app-service-plan). 

   App Service-planens inställningar avgör den [plats, funktioner, kostnad och beräkningsresurser](https://azure.microsoft.com/pricing/details/app-service/) som är associerade med din app. Mer information om App Service-planer och hur du skapar en ny plan i en annan prisnivå nivå och på din önskade plats finns i [Azure App Service-planer djupgående översikt över](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
   
5. Välj **Skapa**. Det här steget skapar Mobile Apps-serverdel. 
6. I den **inställningar** för nya Mobile Apps serverdel, väljer **Snabbstart** > din klientapplattform > **Anslut en databas**. 
   
   ![Val för att ansluta en databas](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)
7. I den **Lägg till dataanslutning** väljer **SQL Database** > **skapa en ny databas**. Ange namnet på databasen, Välj en prisnivå och välj sedan **Server**. Du kan återanvända den nya databasen. Om du redan har en databas på samma plats kan du istället välja **Använd en befintlig databas**. Vi rekommenderar inte användning av en databas på en annan plats, på grund av bandbreddskostnader och högre latens.
   
   ![Att välja en databas](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)
8. I den **ny server** rutan Ange ett unikt namn i den **servernamn** anger ett inloggningsnamn och lösenord, Välj **Tillåt Azure-tjänster åtkomst till servern**, och välj  **OK**. Det här steget skapar den nya databasen.
9. I den **Lägg till dataanslutning** väljer **anslutningssträngen**, anger du användarnamn och lösenord för värden för din databas och välj **OK**. 

   Vänta några minuter för databasen distribueras innan du fortsätter.

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/
