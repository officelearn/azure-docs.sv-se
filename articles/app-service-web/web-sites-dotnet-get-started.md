---
title: Distribuera en ASP.NET-app till Azure med Visual Studio | Microsoft Docs
description: "Ta reda på hur du distribuerar ett ASP.NET-webbprojekt till en ny webbapp i Azure App Service med hjälp av Visual Studio."
services: app-service\web
documentationcenter: .net
author: cephalin
manager: erikre
editor: 
ms.assetid: 69759e3c-384c-4afb-9278-db6724f6cb74
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 12/16/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: 93107d19e1814b30d69fc12c92b544d9760bda6a
ms.lasthandoff: 04/04/2017


---
# <a name="deploy-an-aspnet-web-app-to-azure-app-service-using-visual-studio"></a>Distribuera en ASP.NET-webbapp till Azure App Service med Visual Studio
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

## <a name="overview"></a>Översikt
I den här kursen får du veta hur du distribuerar en ASP.NET-webbapp till en [webbapp i Azure App Service](app-service-web-overview.md) med hjälp av Visual Studio 2015.

Kursen riktar sig till dig som är ASP.NET-utvecklare och som inte har tidigare erfarenhet av att använda Azure. När du har gått igenom kursen har du en enkel webbapp som körs i molnet.

Du får lära dig:

* skapa en ny webbapp för App Service när du skapar ett nytt webbprojekt i Visual Studio.
* distribuera ett webbprojekt till en webbapp i App Service med hjälp av Visual Studio.

I diagrammet kan du se vad du får göra under kursen.

![Diagram över att skapa och distribuera med Visual Studio](./media/web-sites-dotnet-get-started/Create_App.png)

I slutet av kursen finns avsnittet [Felsökning](#troubleshooting), där du hittar förslag på vad du kan göra om något inte fungerar, och avsnittet [Nästa steg](#next-steps) där du hittar länkar till andra självstudiekurser som går in djupare på hur du använder Azure App Service.

Eftersom det här är en introduktionskurs är det webbprojekt som används som exempel på distribuering ett enkelt projekt som inte använder en databas och inte utför autentiseringar och auktorisationer. Länkar till mer avancerade avsnitt om distribution finns i [Distribuera en Azure-webbapp](web-sites-deploy.md).

Förutom den tid det tar att installera Azure SDK för .NET tar det cirka 10–15 minuter att slutföra kursen.

## <a name="prerequisites"></a>Krav
* Kursen riktar sig till dig som har arbetat med ASP.NET MVC och Visual Studio. En introduktion finns i [Komma igång med ASP.NET MVC 5](http://www.asp.net/mvc/overview/getting-started/introduction/getting-started).
* Du behöver ett Azure-konto. Du kan [öppna ett kostnadsfritt Azure-konto](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) eller [aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). 
  
    Om du vill komma igång med Azure App Service innan du registrerar dig för ett Azure-konto kan du gå till [Prova App Service](https://azure.microsoft.com/try/app-service/). Där kan du skapa en tillfällig startapp i App Service – inget kreditkort behövs och du gör inga åtaganden.

## <a name="setupdevenv"></a>Konfigurera utvecklingsmiljön
Kursen gäller för Visual Studio 2015 med [Azure SDK för .NET](../dotnet-sdk.md) 2.9 eller senare. 

* [Ladda ned den senaste Azure SDK:n för Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003). SDK:n installerar Visual Studio 2015 om du inte redan har det.
  
  > [!NOTE]
  > Beroende på hur många av SDK-beroendena du redan har på datorn kan det ta lång tid att installera SDK:n, från några minuter till drygt en halvtimma.
  > 
  > 

Om du har Visual Studio 2013 och vill använda det kan du [ladda ned den senaste Azure SDK:n för Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkID=324322). Vissa av skärmarna kan se annorlunda ut än på bilderna.

## <a name="create-a-web-application"></a>Skapa ett webbprogram
Nästa steg är att skapa ett webbprogramsprojekt i Visual Studio och en webbapp i Azure App Service. I den här delen av kursen får du konfigurera ett nytt webbprojekt. 

1. Öppna Visual Studio 2015.
2. Klicka på **Arkiv > Nytt > Projekt**.
3. I dialogrutan **Nytt projekt** klickar du på **Visual C# > Webb > ASP.NET-webbtillämpningsprogram**.
4. Se till att **.NET Framework 4.5.2** är valt som målramverk.
5. [Azure Application Insights](../application-insights/app-insights-overview.md) övervakar webbappens tillgänglighet, prestanda och användning. Kryssrutan **Lägg till Application Insights i projekt** är markerad som standard när du skapar ett webbprojekt efter du har installerat Visual Studio. Avmarkera kryssrutan om den är markerad och du inte vill prova Application Insights.
6. Ge programmet namnet **MittExempel** och klicka på **OK**.
   
    ![Dialogrutan Nytt projekt](./media/web-sites-dotnet-get-started/GS13newprojdb.png)
7. I dialogrutan **Nytt ASP.NET-projekt** markerar du mallen **MVC** och klickar på **Ändra autentisering**.
   
    Under den här kursen får du distribuera ett ASP.NET MVC-webbprojekt. Om du vill lära dig att distribuera ett ASP.NET Web API-projekt går du till avsnittet [Nästa steg](#next-steps). 
   
    ![Dialogrutan Nytt ASP.NET-projekt](./media/web-sites-dotnet-get-started/GS13changeauth.png)
8. I dialogrutan **Ändra autentisering** klickar du på **Ingen autentisering** och sedan på **OK**.
   
    ![Ingen autentisering](./media/web-sites-dotnet-get-started/GS13noauth.png)
   
    Under den här introduktionskursen får du distribuera en enkel app utan användarinloggning.
9. Gå till området **Microsoft Azure** i dialogrutan **Nytt ASP.NET-projekt** och kontrollera att **Hantera i molnet** har valts och att **App Service** har valts i listrutan.
   
    ![Dialogrutan Nytt ASP.NET-projekt](./media/web-sites-dotnet-get-started/GS13newaspnetprojdb.png)
   
    De här inställningarna anger att Visual Studio ska skapa en Azure-webbapp för webbprojektet.
10. Klicka på **OK**

## <a name="create-the-azure-resources"></a>Skapa Azure-resurserna
Nu anger du de Azure-resurser du vill skapa i Visual Studio.

1. I dialogrutan **Skapa App Service** klickar du på **Lägg till ett konto**. Sedan loggar du in på Azure med ID och lösenord för det konto som används för att hantera Azure-prenumerationen.
   
    ![Logga in på Azure](./media/web-sites-dotnet-get-started/configuresitesettings.png)
   
    Om du redan har loggat in tidigare på samma dator kan det hända att knappen **Lägg till ett konto** inte visas. I så fall kan du hoppa över det här steget eller så kanske du måste ange dina autentiseringsuppgifter igen.
2. Ange ett **namn på webbappen** som är unikt för domänen *azurewebsites.net*. Du kan till exempel kalla den MittExempel med siffror till höger som gör namnet unikt, till exempel MittExempel810. Om ett standardnamn skapas automatiskt för webbappen är det unikt och du kan använda det.
   
    Du ser ett rött utropstecken till höger i stället för en grön bock om någon annan redan har använt det namn du anger. I så fall måste du ange ett nytt namn.
   
    Webbadressen för programmet är det här namnet följt av *.azurewebsites.net*. Om namnet till exempel är `MyExample810` är webbadressen `myexample810.azurewebsites.net`.
   
    Du kan också använda en anpassad domän för en Azure-webbapp. Mer information finns i [Konfigurera ett anpassat domännamn i Azure App Service](web-sites-custom-domain-name.md).
3. Klicka på knappen **Ny** bredvid rutan **Resursgrupp** och ange sedan "MittExempel" eller något annat namn. 
   
    ![Dialogrutan Skapa App Service](./media/web-sites-dotnet-get-started/rgcreate.png)
   
    En resursgrupp är en samling Azure-resurser som webbappar, databaser och virtuella datorer. Under en kurs är det oftast bäst att skapa en ny resursgrupp eftersom det gör att du enkelt kan ta bort alla Azure-resurser som du skapar under kursen i ett steg. Mer information finns i [Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
4. Klicka på knappen **Nytt** bredvid listrutan **App Service plan**.
   
    ![Dialogrutan Skapa App Service](./media/web-sites-dotnet-get-started/createasplan.png)
   
    Dialogrutan **Konfigurera App Service plan** visas.
   
    ![Dialogrutan Konfigurera App Service](./media/web-sites-dotnet-get-started/configasp.png)
   
    I följande steg får du konfigurera en App Service plan för den nya resursgruppen. I en App Service plan anges beräkningsresurserna som webbappen körs på. Om du till exempel väljer den kostnadsfria nivån körs API-appen på delade virtuella datorer medan den körs på dedikerade virtuella datorer för vissa betalnivåer. Mer information finns i [Översikt över App Service planer](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
5. I dialogrutan **Konfigurera App Service plan** anger du "MinExempelplan" eller ett annat namn.
6. I listrutan **Plats** väljer du den plats som är närmast dig.
   
    Den här inställningen anger vilket Azure-datacenter appen ska köras i. I den här kursen kan du välja en region utan att det gör någon märkbar skillnad. Men för en produktionsapp är det bra om servern finns så nära klienterna som använder den som möjligt för att minimera [fördröjningen](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).
7. I listrutan **Nivå** klickar du på **Kostnadsfri**.
   
    För den här kursens syfte räcker det bra att välja den kostnadsfria prisnivån.
8. I dialogrutan **Konfigurera App Service plan** klickar du på **OK**.
9. I dialogrutan **Skapa App Service** klickar du på **Skapa**.

## <a name="inspect-the-azure-resources-in-visual-studio"></a>Inspektera Azure-resurserna i Visual Studio
På kort tid, vanligtvis mindre än en minut, skapar Visual Studio webbprojektet och webbappen.  

I fönstret **Solution Explorer** visas det nya projektets filer och mappar.

![Solution Explorer](./media/web-sites-dotnet-get-started/solutionexplorer.png)

Fönstret **Aktivitet för Azure App Service** visar att App Service-resurserna har skapats i Azure. Du kan klicka på länken här för att omedelbart starta publiceringen av ditt nya projekt. Senare kommer självstudierna att visa hur du publicerar dina filer när som helst.

![Webbapp som har skapats i Aktivitetsfönstret för Azure App Service](./media/web-sites-dotnet-get-started/GS13sitecreated1.png)

I fönstret **Cloud Explorer** kan du visa och hantera Azure-resurser, inklusive den nya webbapp du precis skapade.

![Webbapp som skapats i Cloud Explorer](./media/web-sites-dotnet-get-started/siteinse.png)

## <a name="deploy-the-web-project-to-azure"></a>Distribuera webbprojektet till Azure
I det här avsnittet distribuerar du webbprojektet till webbappresursen som du skapade i Azure App Service.

1. I **Solution Explorer** högerklickar du på projektet och väljer **Publicera**.
   
    ![Välj publicera på Visual Studio-menyn](./media/web-sites-dotnet-get-started/choosepublish.png)
   
    Inom några sekunder visas guiden **Publicera webbplats**. Guiden öppnar en *publiceringsprofil* med inställningar för att distribuera webbprojektet till den nya webbappen.
   
    > [!TIP] 
    > Publiceringsprofilen innehåller ett användarnamn och lösenord för distribution.  Dessa autentiseringsuppgifter genereras automatiskt och du behöver inte ange dem. Lösenordet är krypterat och finns i en dold användarspecifik fil i mappen `Properties\PublishProfiles`.
    >
    >
2. På fliken **Anslutning** i guiden **Publicera webbplats** klickar du på **Nästa**.
   
    ![Klicka på Nästa på fliken Anslutning i guiden Publicera webbplats](./media/web-sites-dotnet-get-started/GS13ValidateConnection.png)
   
    Härnäst följer fliken **Inställningar**. Här kan du ändra versionskonfiguration och distribuera en felsökningsversion för [fjärrfelsökning](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug). På fliken finns också flera [alternativ för filpublicering](https://msdn.microsoft.com/library/dd465337.aspx#Anchor_2).
3. På fliken **inställningar** klickar du på **Nästa**.
   
   ![Fliken Inställningar i guiden Publicera webbplats](./media/web-sites-dotnet-get-started/GS13SettingsTab.png)
   
   Härnäst följer fliken **Förhandsgranska**. Här kan du se vilka filer som ska kopieras från projektet till API-appen. När du distribuerar ett projekt till en API-app som du redan har distribuerat till tidigare, kopieras bara ändrade filer. Du kan visa en lista med det som ska kopieras genom att klicka på knappen **Starta förhandsgranskning**.
4. På fliken **Förhandsgranska** klickar du på **Publicera**.
   
   ![Fliken Förhandsgranska i guiden Publicera webbplats](./media/web-sites-dotnet-get-started/GS13previewoutput.png)
   
   När du klickar på **Publicera** börjar filerna kopieras till Azure-servern. Det kan ta någon minut.
   
   I fönstren **Utdata** och **Aktivitet för Azure App Service** visas vilka distributionsåtgärder som utförts. Här rapporteras också slutförd distribution.
   
   ![Fönstret Utdata i Visual Studio med meddelande om slutförd distribution](./media/web-sites-dotnet-get-started/PublishOutput.png)
   
   Vid distributionen öppnas den distribuerade webbappens webbadress automatiskt i standardwebbläsaren, och programmet du skapade körs nu i molnet. Webbadressen i webbläsarens adressfält visar att webbappen har lästs in från internet.
   
   ![Webbapp som körs i Azure](./media/web-sites-dotnet-get-started/GS13deployedsite.png)
   
   > [!TIP]
   > Du kan aktivera verktygsfältet **Webbpublicering med ett klick** för snabbdistribution. Klicka på **Visa > Verktygsfält** och välj **Webbpublicering med ett klick**. Via verktygsfältet kan du välja en profil, klicka på knappar för att publicera och för att öppna guiden **Publicera webbplats**.
   > ![Verktygsfältet Webbpublicering med ett klick](./media/web-sites-dotnet-get-started/weboneclickpublish.png)
   > 
   > 

## <a name="troubleshooting"></a>Felsökning
Om du får problem när du går igenom den här kursen kontrollerar du att du använder den senaste versionen av Azure SDK för .NET. Det enklaste sättet att göra det på är att [ladda ner Azure SDK för Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003). Om du har den senaste versionen får du ett meddelande från installationsprogrammet för webbplattform om att det inte krävs någon installation.

När din ASP.NET-webbapp körs i Azure App Service kanske du vill veta mer om Visual Studio-funktioner som förenklar felsökningen. Information om bland annat loggning och fjärrfelsökning finns i [Felsöka Azure Web Apps i Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

## <a name="next-steps"></a>Nästa steg
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
  
    Mer information om hur du arbetar med resursgrupper i Azure Portal finns i [Distribuera resurser med Resource Manager-mallar och Azure Portal](../azure-resource-manager/resource-group-template-deploy-portal.md).   
* Fler exempel på hur du kan skapa en ASP.NET-webbapp i App Service finns i [Skapa och distribuera en ASP.NET-webbapp i Azure App Service](https://github.com/Microsoft/HealthClinic.biz/wiki/Create-and-deploy-an-ASP.NET-web-app-in-Azure-App-Service) och [Skapa och distribuera en mobilapp i Azure App Service](https://github.com/Microsoft/HealthClinic.biz/wiki/Create-and-deploy-a-mobile-app-in-Azure-App-Service) i 2015 års [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz)-[anslutningsdemo](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Fler snabbstartsguider från HealthClinic.biz-demonstrationen finns i [Snabbstartsguider för Azure Developer Tools](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).


