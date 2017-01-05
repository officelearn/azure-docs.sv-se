---
title: "Skapa en webbapp från Azure Marketplace | Microsoft Docs"
description: "Läs om hur du skapar en ny WordPress-webbapp från Azure Marketplace med hjälp av Azure Portal."
services: app-service\web
documentationcenter: 
author: rmcmurray
manager: wpickett
editor: 
ms.assetid: 972a296d-f927-470b-8534-0f2cb9eac223
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 00c4336bd5cef4ddc0b92127d0945d39291b9c7f


---
# <a name="create-a-web-app-from-the-azure-marketplace"></a>Skapa en webbapp i Azure Marketplace
<!-- Note: This article replaces web-sites-php-web-site-gallery.md -->

[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Via Azure Marketplace får du tillgång till en mängd olika populära webbappar som har utvecklats av Microsoft och tredjepartsföretag samt programvara med öppen källkod, till exempel WordPress, Umbraco CMS, Drupal med flera. Webbapparna är baserade på en mängd olika populära ramverk, bland annat [PHP], som i det här WordPress-exemplet, [.NET], [Node.js], [Java] och [Python]. När du skapar en webbapp från Azure Marketplace är den enda programvara du behöver den webbläsare du använder för [Azure Portal].

I den här kursen får du lära du dig att:

* Hitta och skapa en webbapp i Azure App Service som baseras på en Azure Marketplace-mall.
* Konfigurera Azure App Service-inställningarna för den nya webbappen.
* Starta och hantera webbappen.

I den här kursen distribuerar du en WordPress-bloggwebbplats från Azure Marketplace. När du har gått igenom alla stegen i kursen har du en egen WordPress-webbplats igång i molnet.

![Exempel på instrumentpanelen i WordPress-webbappen][WordPressDashboard1]

För WordPress-webbplatsen du distribuerar under den här kursen används MySQL som databas. Om du istället vill använda SQL Database som databas kan du använda [Project Nami], som också är tillgänglig via Azure Marketplace.

> [!NOTE]
> För den här kursen behöver du ett Microsoft Azure-konto. Om du inte har ett konto kan du [aktivera Visual Studio-prenumerantförmåner][activate] eller [registrera dig för en kostnadsfri utvärderingsversion][free trial].
> 
> Om du vill komma igång med Azure Apptjänst innan du registrerar dig för ett Azure-konto kan du gå till [Prova Apptjänst]. Där kan du direkt skapa en tillfällig startwebbapp i Apptjänst. Inget kreditkort behövs och du gör inga åtaganden.
> 
> 

## <a name="find-and-create-a-web-app-in-azure-app-service"></a>Hitta och skapa en webbapp i Azure App Service
1. Logga in på [Azure Portal].
2. Klicka på **Ny**.
   
    ![Skapa en ny resurs i Azure][MarketplaceStart]
3. Sök efter **WordPress** och klicka sedan på **WordPress**. (Om du vill använda SQL Database i stället för MySQL söker du efter **Project Nami**.)
   
    ![Söka efter WordPress i Marketplace][MarketplaceSearch]
4. När du har läst igenom beskrivningen av WordPress-appen klickar du på **Skapa**.
   
    ![Skapa WordPress-webbapp][MarketplaceCreate]

## <a name="configure-azure-app-service-settings-for-your-new-web-app"></a>Konfigurera Azure App Service-inställningarna för din nya webbapp.
1. När du har skapat en ny webbapp, visas bladet med inställningar för WordPress som du använder för att genomföra följande steg:
   
    ![Konfigurera inställningar för WordPress-webbappen][ConfigStart]
2. Ange ett namn på webbappen i rutan **Webbapp**.
   
    Det här namnet måste vara unikt i domänen azurewebsites.net eftersom webbappens webbadress kommer att vara *{namn}*.azurewebsites.net. Om namnet inte är unikt visas ett rött utropstecken i textrutan.
   
    ![Ställ in namnet på WordPress-webbappen][ConfigAppName]
3. Om du har mer än en prenumeration väljer du den du vill använda.
   
    ![Konfigurera prenumerationen för webbappen][ConfigSubscription]
4. Välj en **Resursgrupp** eller skapa en ny.
   
    Mer information om resursgrupper finns i [Översikt över Azure Resource Manager][ResourceGroups].
   
    ![Konfigurera resursgruppen för webbappen][ConfigResourceGroup]
5. Välj en **App Service-plan/plats** eller skapa en ny.
   
    Mer information om App Service-planer finns i [Översikt över Azure App Service-planer][AzureAppServicePlans].
   
    ![Konfigurera Apptjänstplanen för webbappen][ConfigServicePlan]
6. Klicka på **Databas**. I bladet **Ny MySQL-databas** anger du de värden som behövs för att konfigurera MySQL-databasen.
   
    a. Ange ett nytt namn eller låt standardnamnet stå kvar.
   
    b. Lämna **Databastyp** inställt på **Delad**.
   
    c. Välj samma plats som du valde för webbappen.
   
    d. Välj prisnivå. **Mercury** – För den här kursen räcker det bra med Mercury (kostnadsfri med det lägsta antalet tillåtna anslutningar och lägst mängd diskutrymme).
   
    e. I bladet **Ny MySQL-databas** godkänner du de juridiska villkoren och klickar på **OK**.
   
    ![Konfigurera databasinställningarna för webbappen][ConfigDatabase]
7. I bladet **WordPress** godkänner du de juridiska villkoren och klickar på **Skapa**.
   
    ![Slutföra webbappsinställningarna och klicka på OK][ConfigFinished]
   
    Den nya webbappen skapas i Azure Apptjänst. Det går vanligen på mindre än en minut. Du kan se förloppet genom att klicka på klockikonen överst på portalsidan.
   
    ![Förloppsindikator][ConfigProgress]

## <a name="launch-and-manage-your-wordpress-web-app"></a>Starta och hantera WordPress-webbappen
1. När webbappen har skapats kan du visa webbappen och databasen genom att gå till den resursgrupp i Azure Portal där du skapade programmet.
   
    Extraresursen med lampikonen är [Application Insights][ApplicationInsights] som tillhandahåller övervakningstjänster för webbappen.
2. I bladet **Resursgrupp** klickar du på raden webbapp.
   
    ![Välja din WordPress-webbapp][WordPressSelect]
3. I bladet Webbapp klickar du på **Bläddra**.
   
    ![Leta fram din WordPress-webbapp][WordPressBrowse]
4. Om du uppmanas att välja språk för din WordPress-blogg väljer du önskat språk och klickar på **Fortsätt**.
   
    ![Ställa in språket för WordPress-webbappen][WordPressLanguage]
5. På WordPress sida **Välkommen** anger du de konfigurationsuppgifter som efterfrågas av WordPress och klickar på **Installera WordPress**.
   
    ![Konfigurera inställningarna för WordPress-webbappen][WordPressConfigure]
6. Logga in med autentiseringsuppgifterna du skapade på sidan **Välkommen**.  
7. Instrumentpanelen för din webbplats öppnas och de uppgifter du angav tidigare visas.    
   
    ![Öppna instrumentpanelen i WordPress][WordPressDashboard2]

## <a name="next-steps"></a>Nästa steg
I den här kursen har du fått lära dig hur du skapar och distribuerar en exempelwebbapp från Azure Marketplace.

Du hittar mer information om hur du arbetar med App Service Web Apps via länkarna till vänster på sidan (breda webbläsarfönster) eller längst upp på sidan (smala webbläsarfönster).

Mer information om hur du utvecklar WordPress-webbappar i Azure finns i [Developing WordPress on Azure App Service][WordPressOnAzure] (Utveckla WordPress i Azure App Service).

<!-- URL List -->

[PHP]: https://azure.microsoft.com/develop/php/
[.NET]: https://azure.microsoft.com/develop/net/
[Node.js]: https://azure.microsoft.com/develop/nodejs/
[Java]: https://azure.microsoft.com/develop/java/
[Python]: https://azure.microsoft.com/develop/python/
[activate]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[free trial]: https://azure.microsoft.com/pricing/free-trial/
[Prova Apptjänst]: http://go.microsoft.com/fwlink/?LinkId=523751
[ResourceGroups]: ../azure-resource-manager/resource-group-overview.md
[AzureAppServicePlans]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[ApplicationInsights]: https://azure.microsoft.com/services/application-insights/
[Azure Portal]: https://portal.azure.com/
[Project Nami]: http://projectnami.org/
[WordPressOnAzure]: ./develop-wordpress-on-app-service-web-apps.md

<!-- IMG List -->

[MarketplaceStart]: ./media/app-service-web-create-web-app-from-marketplace/marketplacestart.png
[MarketplaceSearch]: ./media/app-service-web-create-web-app-from-marketplace/marketplacesearch.png
[MarketplaceCreate]: ./media/app-service-web-create-web-app-from-marketplace/marketplacecreate.png
[ConfigStart]: ./media/app-service-web-create-web-app-from-marketplace/configstart.png
[ConfigAppName]: ./media/app-service-web-create-web-app-from-marketplace/configappname.png
[ConfigSubscription]: ./media/app-service-web-create-web-app-from-marketplace/configsubscription.png
[ConfigResourceGroup]: ./media/app-service-web-create-web-app-from-marketplace/configresourcegroup.png
[ConfigServicePlan]: ./media/app-service-web-create-web-app-from-marketplace/configserviceplan.png
[ConfigDatabase]: ./media/app-service-web-create-web-app-from-marketplace/configdatabase.png
[ConfigFinished]: ./media/app-service-web-create-web-app-from-marketplace/configfinished.png
[ConfigProgress]: ./media/app-service-web-create-web-app-from-marketplace/configprogress.png
[WordPressSelect]: ./media/app-service-web-create-web-app-from-marketplace/wpselect.png
[WordPressBrowse]: ./media/app-service-web-create-web-app-from-marketplace/wpbrowse.png
[WordPressLanguage]: ./media/app-service-web-create-web-app-from-marketplace/wplanguage.png
[WordPressDashboard1]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard1.png
[WordPressDashboard2]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard2.png
[WordPressConfigure]: ./media/app-service-web-create-web-app-from-marketplace/wpconfigure.png



<!--HONumber=Dec16_HO1-->


