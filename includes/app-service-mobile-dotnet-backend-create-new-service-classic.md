1. Logga in på den [Azure-portalen].
2. Välj **+ ny** > **webb + mobilt** > **Mobilapp**, och ange sedan ett namn för Mobile Apps serverdel.
3. För **resursgruppen**, Välj en befintlig resursgrupp eller skapa en ny (med samma namn som din app). 
4. För **programtjänstplanen**, standardplanen (i det [standardnivån](https://azure.microsoft.com/pricing/details/app-service/)) är markerad. Du kan också välja en annan plan eller [skapa en ny](../articles/app-service/app-service-plan-manage.md#create-an-app-service-plan). 

   App Service-planens inställningar avgör den [plats, funktioner, kostnad och beräkningsresurser](https://azure.microsoft.com/pricing/details/app-service/) som är associerade med din app. Mer information om App Service planer och hur du skapar en ny plan i en annan prisnivå på datanivå och på din önskade plats finns [Azure App Service-planer djupgående översikt över](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
   
5. Välj **Skapa**. Det här steget skapar Mobile Apps-serverdel. 
6. I den **inställningar** för nya Mobile Apps serverdel väljer **Snabbstart** > din klientapplattform > **ansluta en databas**. 
   
   ![Alternativ för att ansluta en databas](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)
7. I den **Lägg till dataanslutning** väljer **SQL-databas** > **skapa en ny databas**. Ange namnet på databasen, väljer en prisnivå och välj sedan **Server**. Du kan återanvända den nya databasen. Om du redan har en databas på samma plats kan du istället välja **Använd en befintlig databas**. Vi rekommenderar inte att använda en databas på en annan plats på grund av bandbreddskostnader och högre latens.
   
   ![Att välja en databas](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)
8. I den **ny server** rutan Ange ett unikt servernamn i den **servernamn** anger ett inloggningsnamn och lösenord, Välj **Tillåt Azure-tjänster åtkomst till servern**, och välj  **OK**. Det här steget skapar den nya databasen.
9. I den **Lägg till dataanslutning** väljer **anslutningssträngen**, ange värden för inloggningsnamn och lösenord för databasen och välj **OK**. 

   Vänta några minuter för databasen distribueras innan du fortsätter.

<!-- URLs. -->
[Azure-portalen]: https://portal.azure.com/
