---
title: Skapa en WordPress-webbapp i Azure App Service | Microsoft Docs
description: "Lär dig hur du skapar en ny Azure-webbapp för en WordPress-blogg i Azure Portal."
services: app-service\web
documentationcenter: php
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 193ae094-0d7c-4749-a09b-ff4b1240149e
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: hero-article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 83ef925a8268873d09c4a1611a1f2341acd92ba7


---
# <a name="create-a-wordpress-web-app-in-azure-app-service"></a>Skapa en WordPress-webbapp i Azure App Service
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

I den här kursen får du veta hur du distribuerar en WordPress-bloggwebbplats från Azure Marketplace.

När du har gått igenom kursen har du en egen WordPress-bloggwebbplats som körs i molnet.

![WordPress-webbplats](./media/web-sites-php-web-site-gallery/wpdashboard.png)

Du får lära dig att:

* hitta en programmall i Azure Marketplace.
* skapa en webbapp i Azure App Service som baseras på mallen.
* konfigurera inställningarna för Azure App Service för nya webbappar och databaser.

Via Azure Marketplace får du tillgång till en mängd olika populära webbappar som har utvecklats av Microsoft och tredje part samt programvara med öppen källkod. Webbapparna baseras på en mängd olika populära ramverk, bland annat [PHP](/develop/nodejs/), som i det här WordPress-exemplet, [.NET](/develop/net/), [Node.js](/develop/nodejs/), [Java](/develop/java/) och [Python](/develop/python/). När du skapar en webbapp från Azure Marketplace är den enda programvara du behöver den webbläsare du använder för [Azure Portal](https://portal.azure.com/). 

För WordPress-webbplatsen du distribuerar under den här kursen används MySQL som databas. Se [Project Nami](http://projectnami.org/) om du hellre vill använda SQL Database som databas. **Project Nami** också är tillgängligt via Marketplace.

> [!NOTE]
> För den här kursen behöver du ett Microsoft Azure-konto. Om du inte har ett konto kan du [aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) eller [registrera dig för en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
> 
> Om du vill komma igång med Azure App Service innan du registrerar dig för ett Azure-konto kan du gå till [Prova App Service](https://azure.microsoft.com/try/app-service/). Där kan du direkt skapa en tillfällig startwebbapp i App Service. Inget kreditkort behövs och du gör inga åtaganden.
> 
> 

## <a name="select-wordpress-and-configure-for-azure-app-service"></a>Välj WordPress och konfigurera för Azure App Service
1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Klicka på **Ny**.
   
    ![Skapa Ny][5]
3. Sök efter **WordPress** och klicka sedan på **WordPress**. Om du vill använda SQL Database i stället för MySQL söker du efter **Project Nami**.
   
    ![WordPress från lista][7]
4. När du har läst igenom beskrivningen av WordPress-appen klickar du på **Skapa**.
   
    ![Skapa](./media/web-sites-php-web-site-gallery/create.png)
5. Ange ett namn på webbappen i rutan **Webbapp**.
   
    Det här namnet måste vara unikt i domänen azurewebsites.net eftersom webbappens webbadress är {namn}.azurewebsites.net. Om namnet inte är unikt visas ett rött utropstecken i textrutan.
6. Om du har fler än en prenumeration väljer du den du vill använda. 
7. Välj en **Resursgrupp** eller skapa en ny.
   
    Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
8. Välj en **App Service-plan/plats** eller skapa en ny.
   
    Mer information om App Service-planer finns i [Översikt över Azure App Service-planer](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)    
9. Klicka på **Databas** och ange sedan i bladet **Ny MySQL-databas** de värden som krävs för att konfigurera MySQL-databasen.
   
    a. Ange ett nytt namn eller låt standardnamnet stå kvar.
   
    b. Lämna **Databastyp** inställt på **Delad**.
   
    c. Välj samma plats som du valde för webbappen.
   
    d. Välj en prisnivå. För kursen räcker det bra med Mercury (kostnadsfri med det lägsta antalet tillåtna anslutningar och lägst mängd diskutrymme).
10. I bladet **Ny MySQL-databas** klickar du på **OK**. 
11. I bladet **WordPress** godkänner du de juridiska villkoren och klickar på **Skapa**. 
    
     ![Konfigurera webbappen](./media/web-sites-php-web-site-gallery/configure.png)
    
     Azure App Service skapar den nya webbappen. Det går vanligen på mindre än en minut. Du kan se förloppet genom att klicka på klockikonen överst på portalsidan.
    
     ![Förloppsindikator](./media/web-sites-php-web-site-gallery/progress.png)

## <a name="launch-and-manage-your-wordpress-web-app"></a>Starta och hantera WordPress-webbappen
1. När webbappen har skapats kan du visa webbappen och databasen genom att gå till den resursgrupp i Azure Portal där du skapade programmet.
   
    Extraresursen med lampikonen är [Application Insights](/services/application-insights/) som tillhandahåller övervakningstjänster för webbappen.
2. I bladet **Resursgrupp** klickar du på raden webbapp.
   
    ![Konfigurera webbappen](./media/web-sites-php-web-site-gallery/resourcegroup.png)
3. I bladet Webbapp klickar du på **Bläddra**.
   
    ![webbadress][browse]
4. På WordPress sida **Välkommen** anger du de konfigurationsuppgifter som krävs av WordPress och klickar på **Installera WordPress**.
   
    ![Konfigurera WordPress](./media/web-sites-php-web-site-gallery/wpconfigure.png)
5. Logga in med autentiseringsuppgifterna du skapade på sidan **Välkommen**.  
6. Webbplatsens instrumentpanelssida öppnas.    
   
    ![WordPress-webbplats](./media/web-sites-php-web-site-gallery/wpdashboard.png)

## <a name="next-steps"></a>Nästa steg
Du har fått veta hur du skapar och distribuerar en PHP-webbapp från galleriet. Mer information om hur du använder PHP i Azure finns i [PHP Developer Center](/develop/php/).

Du hittar mer information om hur du arbetar med App Service Web Apps via länkarna till vänster på sidan (breda webbläsarfönster) eller längst upp på sidan (smala webbläsarfönster). 

## <a name="whats-changed"></a>Nyheter
* En guide till övergången från Webbplatser till App Service finns i: [Azure App Service och dess påverkan på befintliga Azure-tjänster](http://go.microsoft.com/fwlink/?LinkId=529714)

[5]: ./media/web-sites-php-web-site-gallery/startmarketplace.png
[7]: ./media/web-sites-php-web-site-gallery/search-web-app.png
[browse]: ./media/web-sites-php-web-site-gallery/browse-web.png



<!--HONumber=Feb17_HO3-->


