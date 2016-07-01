<properties
    pageTitle="Distribuera en ASP.NET-app till Azure App Service med Visual Studio | Microsoft Azure"
    description="Ta reda på hur du distribuerar ett ASP.NET-webbprojekt till en ny webbapp i Azure App Service med hjälp av Visual Studio."
    services="app-service\web"
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="04/22/2016"
    ms.author="tdykstra"/>

# Distribuera en ASP.NET-webbapp till Azure App Service med Visual Studio

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

## Översikt

I den här kursen får du veta hur du distribuerar en ASP.NET-webbapp till en [webbapp i Azure App Service](app-service-web-overview.md) med hjälp av Visual Studio 2015.

Kursen riktar sig till dig som är ASP.NET-utvecklare och som inte har tidigare erfarenhet av att använda Azure. När du har gått igenom kursen har du en enkel webbapp som körs i molnet.

Du får lära dig att:

* skapa en ny webbapp för App Service när du skapar ett nytt webbprojekt i Visual Studio.
* distribuera ett webbprojekt till en webbapp i App Service med hjälp av Visual Studio.

I diagrammet kan du se vad du får göra under kursen.

![Diagram över att skapa och distribuera med Visual Studio](./media/web-sites-dotnet-get-started/Create_App.png)

I slutet av kursen finns avsnittet [Felsökning](#troubleshooting), där du hittar förslag på vad du kan göra om något inte fungerar, och avsnittet [Nästa steg](#next-steps) där du hittar länkar till andra självstudiekurser som går in djupare på hur du använder Azure App Service.

Eftersom det här är en introduktionskurs är det webbprojekt som används som exempel på distribuering ett enkelt projekt som inte använder en databas och inte utför autentiseringar och auktorisationer. Länkar till mer avancerade avsnitt om distribution finns i [Distribuera en Azure-webbapp](web-sites-deploy.md).

Förutom den tid det tar att installera Azure SDK för .NET tar det cirka 10–15 minuter att slutföra kursen.

## Krav

* Kursen riktar sig till dig som har arbetat med ASP.NET MVC och Visual Studio. En introduktion finns i [Komma igång med ASP.NET MVC 5](http://www.asp.net/mvc/overview/getting-started/introduction/getting-started).

* Du behöver ett Azure-konto. Du kan [öppna ett kostnadsfritt Azure-konto](/pricing/free-trial/?WT.mc_id=A261C142F) eller [aktivera Visual Studio-prenumerantförmåner](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). 

    Om du vill komma igång med Azure App Service innan du registrerar dig för ett Azure-konto kan du gå till [Prova App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Där kan du skapa en tillfällig startapp i App Service – inget kreditkort behövs och du gör inga åtaganden.

## <a name="setupdevenv"></a>Konfigurera utvecklingsmiljön

Kursen gäller för Visual Studio 2015 med [Azure SDK för .NET](../dotnet-sdk.md) 2.9 eller senare. 

* [Ladda ned den senaste Azure SDK:n för Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003). SDK:n installerar Visual Studio 2015 om du inte redan har det.

    >[AZURE.NOTE] Beroende på hur många av SDK-beroendena du redan har på datorn, kan det några minuter till en halvtimme och mer att installera SDK:n.

Om du har Visual Studio 2013 och vill använda det kan du [ladda ned den senaste Azure SDK:n för Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkID=324322). Vissa av skärmarna kan se annorlunda ut än på bilderna.

## Konfigurera ett nytt webbprojekt

Nästa steg är att skapa ett webbprojekt i Visual Studio och en webbapp i Azure App Service. I den här delen av kursen får du konfigurera ett nytt webbprojekt. 

1. Öppna Visual Studio 2015.

2. Klicka på **Arkiv > Nytt > Projekt**.

3. I dialogrutan **Nytt projekt** klickar du på **Visual C# > Webb > ASP.NET-webbtillämpningsprogram**.

3. Se till att **.NET Framework 4.5.2** är valt som målramverk.

4.  [Azure Application Insights](../application-insights/app-insights-overview.md) övervakar webbappens tillgänglighet, prestanda och användning. Kryssrutan **Lägg till Application Insights i projekt** är markerad som standard när du skapar ett webbprojekt efter du har installerat Visual Studio. Avmarkera kryssrutan om den är markerad och du inte vill prova Application Insights.

4. Ge programmet namnet **MittExempel** och klicka på **OK**.

    ![Dialogrutan Nytt projekt](./media/web-sites-dotnet-get-started/GS13newprojdb.png)

5. I dialogrutan **Nytt ASP.NET-projekt** markerar du mallen **MVC** och klickar på **Ändra autentisering**.

    Under den här kursen får du distribuera ett ASP.NET MVC-webbprojekt. Om du vill lära dig att distribuera ett ASP.NET Web API-projekt går du till avsnittet [Nästa steg](#next-steps). 

    ![Dialogrutan Nytt ASP.NET-projekt](./media/web-sites-dotnet-get-started/GS13changeauth.png)

6. I dialogrutan **Ändra autentisering** klickar du på **Ingen autentisering** och sedan på **OK**.

    ![Ingen autentisering](./media/web-sites-dotnet-get-started/GS13noauth.png)

    Under den här introduktionskursen får du distribuera en enkel app utan användarinloggning.

5. Gå till området **Microsoft Azure** i dialogrutan **Nytt ASP.NET-projekt** och kontrollera att **Hantera i molnet** har valts och att **App Service** har valts i listrutan.

    ![Dialogrutan Nytt ASP.NET-projekt](./media/web-sites-dotnet-get-started/GS13newaspnetprojdb.png)

    De här inställningarna anger att Visual Studio ska skapa en Azure-webbapp för webbprojektet.

6. Klicka på **OK**

## Konfigurera Azure-resurser för en ny webbapp

Nu anger du de Azure-resurser du vill skapa i Visual Studio.

5. I dialogrutan **Skapa App Service** klickar du på **Lägg till ett konto**. Sedan loggar du in på Azure med ID och lösenord för det konto som används för att hantera Azure-prenumerationen.

    ![Logga in på Azure](./media/web-sites-dotnet-get-started/configuresitesettings.png)

    Om du redan har loggat in tidigare på samma dator kan det hända att knappen **Lägg till ett konto** inte visas. I så fall kan du hoppa över det här steget eller så kanske du måste ange dina autentiseringsuppgifter igen.
 
3. Ange ett **namn på webbappen** som är unikt för domänen *azurewebsites.net*. Du kan till exempel kalla den MittExempel med siffror till höger som gör namnet unikt, till exempel MittExempel810. Om ett standardnamn skapas automatiskt för webbappen är det unikt och du kan använda det.

    Du ser ett rött utropstecken till höger i stället för en grön bock om någon annan redan har använt det namn du anger. I så fall måste du ange ett nytt namn.

    Webbadressen för programmet är det här namnet följt av *.azurewebsites.net*. Om namnet till exempel är `MyExample810` är webbadressen `myexample810.azurewebsites.net`.

    Du kan också använda en anpassad domän för en Azure-webbapp. Mer information finns i [Konfigurera ett anpassat domännamn i Azure App Service](web-sites-custom-domain-name.md).

6. Klicka på knappen **Ny** bredvid rutan **Resursgrupp** och ange sedan "MittExempel" eller något annat namn. 

    ![Dialogrutan Skapa App Service](./media/web-sites-dotnet-get-started/rgcreate.png)

    En resursgrupp är en samling Azure-resurser som webbappar, databaser och virtuella datorer. Under en kurs är det oftast bäst att skapa en ny resursgrupp eftersom det gör att du enkelt kan ta bort alla Azure-resurser som du skapar under kursen i ett steg. Mer information finns i [Översikt över Azure Resource Manager](../resource-group-overview.md).

4. Klicka på knappen **Nytt** bredvid listrutan **App Service-plan**.

    ![Dialogrutan Skapa App Service](./media/web-sites-dotnet-get-started/createasplan.png)

    Dialogrutan **Konfigurera App Service-plan** visas.

    ![Dialogrutan Konfigurera App Service](./media/web-sites-dotnet-get-started/configasp.png)

    I följande steg får du konfigurera en App Service-plan för den nya resursgruppen. I en App Service-plan anges beräkningsresurserna som webbappen körs på. Om du till exempel väljer den kostnadsfria nivån körs API-appen på delade virtuella datorer medan den körs på dedikerade virtuella datorer för vissa betalnivåer. Mer information finns i [Översikt över App Service-planer](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

5. I dialogrutan **Konfigurera App Service-plan** anger du "MinExempelplan" eller ett annat namn.

5. I listrutan **Plats** väljer du den plats som är närmast dig.

    Den här inställningen anger vilket Azure-datacenter appen ska köras i. I den här kursen kan du välja en region utan att det gör någon märkbar skillnad. Men för en produktionsapp är det bra om servern finns så nära klienterna som använder den som möjligt för att minimera [fördröjningen](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. I listrutan **Nivå** klickar du på **Kostnadsfri**.

    För den här kursens syfte räcker det bra att välja den kostnadsfria prisnivån.

6. I dialogrutan **Konfigurera App Service-plan** klickar du på **OK**.

7. I dialogrutan **Skapa App Service** klickar du på **Skapa**.

## Projektet och webbappen skapas i Visual Studio

På kort tid, vanligtvis mindre än en minut, skapar Visual Studio webbprojektet och webbappen.  

I fönstret **Solution Explorer** visas det nya projektets filer och mappar.

![Solution Explorer](./media/web-sites-dotnet-get-started/solutionexplorer.png)

Fönstret **Aktivitet för Azure App Service** visar att webbappen har skapats.

![Webbapp som har skapats i Aktivitetsfönstret för Azure App Service](./media/web-sites-dotnet-get-started/GS13sitecreated1.png)

I fönstret **Cloud Explorer** kan du visa och hantera Azure-resurser, inklusive den nya webbapp du precis skapade.

![Webbapp som skapats i Cloud Explorer](./media/web-sites-dotnet-get-started/siteinse.png)
    
## Distribuera webbprojektet till Azure-webbappen

I det här avsnittet distribuerar du webbprojektet till webbappen.

1. I **Solution Explorer** högerklickar du på projektet och väljer **Publicera**.

    ![Välj publicera på Visual Studio-menyn](./media/web-sites-dotnet-get-started/choosepublish.png)

    Inom några sekunder visas guiden **Publicera webbplats**. Guiden öppnar en *publiceringsprofil* med inställningar för att distribuera webbprojektet till den nya webbappen.

    Publiceringsprofilen innehåller ett användarnamn och lösenord för distribution.  Dessa autentiseringsuppgifter genereras automatiskt och du behöver inte ange dem. Lösenordet är krypterat och finns i en dold användarspecifik fil i mappen `Properties\PublishProfiles`.
 
8. På fliken **Anslutning** i guiden **Publicera webbplats** klickar du på **Nästa**.

    ![Klicka på Nästa på fliken Anslutning i guiden Publicera webbplats](./media/web-sites-dotnet-get-started/GS13ValidateConnection.png)

    Härnäst följer fliken **Inställningar**. Här kan du ändra versionskonfiguration och distribuera en felsökningsversion för [fjärrfelsökning](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug). På fliken finns också flera [alternativ för filpublicering](https://msdn.microsoft.com/library/dd465337.aspx#Anchor_2).

10. På fliken **inställningar** klickar du på **Nästa**.

    ![Fliken Inställningar i guiden Publicera webbplats](./media/web-sites-dotnet-get-started/GS13SettingsTab.png)

    Härnäst följer fliken **Förhandsgranska**. Här kan du se vilka filer som ska kopieras från projektet till API-appen. När du distribuerar ett projekt till en API-app som du redan har distribuerat till tidigare, kopieras bara ändrade filer. Du kan visa en lista med det som ska kopieras genom att klicka på knappen **Starta förhandsgranskning**.

11. På fliken **Förhandsgranska ** klickar du på **Publicera**.

    ![Fliken Förhandsgranska i guiden Publicera webbplats](./media/web-sites-dotnet-get-started/GS13previewoutput.png)

    När du klickar på **Publicera** börjar filerna kopieras till Azure-servern. Det kan ta någon minut.

    I fönstren **Utdata** och **Aktivitet för Azure App Service** visas vilka distributionsåtgärder som utförts. Här rapporteras också slutförd distribution.

    ![Fönstret Utdata i Visual Studio med meddelande om slutförd distribution](./media/web-sites-dotnet-get-started/PublishOutput.png)

    Vid distributionen öppnas den distribuerade webbappens webbadress automatiskt i standardwebbläsaren, och programmet du skapade körs nu i molnet. Webbadressen i webbläsarens adressfält visar att webbappen har lästs in från internet.

    ![Webbapp som körs i Azure](./media/web-sites-dotnet-get-started/GS13deployedsite.png)

    > [AZURE.TIP] Du kan aktivera verktygsfältet **Webbpublicering med ett klick** för snabbdistribution. Klicka på **Visa > Verktygsfält** och välj **Webbpublicering med ett klick**. Via verktygsfältet kan du välja en profil, klicka på knappar för att publicera och för att öppna guiden **Publicera webbplats**.
    > ![Verktygsfältet Webbpublicering med ett klick](./media/web-sites-dotnet-get-started/weboneclickpublish.png)

## Felsökning

Om du får problem när du går igenom den här kursen kontrollerar du att du använder den senaste versionen av Azure SDK för .NET. Det enklaste sättet att göra det på är att [ladda ner Azure SDK för Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003). Om du har den senaste versionen får du ett meddelande från installationsprogrammet för webbplattform om att det inte krävs någon installation.

Om du befinner dig i ett företagsnätverk och försöker att distribuera till Azure App Service via en brandvägg ska du kontrollera att portarna 443 och 8172 är öppna för webbdistribution. Om du inte kan öppna portarna hittar du andra distributionsalternativ i avsnittet Nästa steg nedan.

När din ASP.NET-webbapp körs i Azure App Service kanske du vill veta mer om Visual Studio-funktioner som förenklar felsökningen. Information om bland annat loggning och fjärrfelsökning finns i [Felsöka Azure-webbappar i Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

## Nästa steg

I den här kursen har du fått veta hur du skapar en enkel webbapp och distribuerar den till en Azure-webbapp. Här följer några närliggande ämnen och resurser i vilka du kan lära dig mer om Azure App Service:

* Övervaka och hantera webbappen i [Azure Portal](https://portal.azure.com/). 

    Mer information finns i [en översikt över Azure Portal](/services/management-portal/) och [Konfigurera webbappar i Azure App Service](web-sites-configure.md).

* Distribuera ett befintligt webbprojekt till en ny webbapp med hjälp av Visual Studio

    I **Solution Explorer** högerklickar du på projektet och sedan på **Publicera**. Välj **Microsoft Azure App Service** som publiceringsmål och klicka på **Ny**. Sedan visas de dialogrutor du har sett i den här kursen.

* Distribuera ett webbprojekt från källkontroll

    Information om att [automatisera distributionen](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) från ett [källkontrollsystem](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control) finns i [Komma igång med webbappar i Azure App Service](app-service-web-get-started.md) och [Distribuera en Azure-webbapp](web-sites-deploy.md).

* Distribuera ett ASP.NET webb-API till en API-app i Azure App Service

    Du har fått veta hur du skapar en instans av Azure App Service som framförallt är avsedd att vara värd för en webbplats. I App Service finns också funktioner för webb-API: er, t.ex. stöd för CORS och API-metadatastöd för generering av klientkod. Du kan använda API-funktioner i en webbapp, men om du främst vill ha en API i en instans av App Service är en **API-app** ett bättre alternativ. Mer information finns i [Komma igång med API Apps och ASP.NET i Azure App Service](../app-service-api/app-service-api-dotnet-get-started.md). 

* Lägga till ett eget domännamn och SSL

    Mer information om hur du använder SSL och en egen domän (till exempel: www.contoso.com i stället för contoso.azurewebsites.net) finns i följande resurser:

    * [Konfigurera ett anpassat domännamn i Azure App Service](web-sites-custom-domain-name.md)
    * [Aktivera HTTPS för en Azure-webbplats](web-sites-configure-ssl-certificate.md)

* Ta bort resursgruppen som innehåller webbappen och eventuella relaterade Azure-resurser när du är klar med dem.

    Mer information om hur du arbetar med resursgrupper i Azure Portal finns i [Använda Azure Portal till att distribuera och hantera Azure-resurser](../resource-group-portal.md).   


<!--HONumber=Jun16_HO2-->


