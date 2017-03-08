---
title: "Distribuera en WordPress-app i Azure Portal på fem minuter | Microsoft Docs"
description: "Distribuera en WordPress-app och se hur enkelt det är att köra webbappar i App Service. Resultatet visas omedelbart."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/13/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: 7ef5802866bf96859d3f44cdb58cbb412b08a700
ms.lasthandoff: 02/28/2017


---
# <a name="deploy-a-wordpress-app-in-the-azure-portal-in-five-minutes"></a>Distribuera en WordPress-app i Azure Portal på fem minuter

I den här kursen får du lära dig hur du distribuerar din första [WordPress](https://wordpress.org/)-webbapp till [Azure App Service](../app-service/app-service-value-prop-what-is.md) på några minuter.

![WordPress-webbplats](./media/app-service-web-get-started-php-portal/wpdashboard.png)

## <a name="prerequisites"></a>Krav
Du behöver ett Microsoft Azure-konto. Om du inte har ett konto kan du [registrera dig för en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) eller [aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> Du kan [Prova App Service](https://azure.microsoft.com/try/app-service/) utan ett Azure-konto. Skapa en startapp och testa den i upp till en timme – inget kreditkort behövs, inga åtaganden.
> 
> 

## <a name="deploy-the-wordpress-app"></a>Distribuera WordPress-appen
1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Öppna [https://portal.azure.com/#create/WordPress.WordPress](https://portal.azure.com/#create/WordPress.WordPress).

    Den här länken är en genväg du kan använda när du vill konfigurera en ny WordPress-app direkt i Azure Portal.

3. I **Appnamn** anger du webbappens namn. Om namnet är unikt för domänen `azurewebsites.net` så visas en grön bockmarkering i rutan.
   
5. Klicka på **Skapa ny** i **Resursgrupp** om du vill skapa en ny [resursgrupp](../azure-resource-manager/resource-group-overview.md) och ge den sedan ett namn.

6. I **Databasprovider** väljer du **CleaDB**.

7. Klicka på **App Service plan/plats** > **Skapa ny**. Så här konfigurerar du din [App Service plan](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md):

    - Ange önskat namn i **App Service plan**.
    - Välj en plats för planens värd i **Plats**.
    - Klicka på **Prisnivå**, välj **F1 Kostnadsfri** eller någon annan nivå som passar dig och klicka på **Välj**.
    - Klicka på **OK**.

8. Klicka på **Databas** > **Skapa ny**. Konfigurera SQL-databasen så här:

    - I **Databasnamn** skriver du ett databasnamn. 
    - I **Plats** väljer du samma plats som App Service- planen.
    - Klicka på **Prisnivå**, välj **Mercury** eller någon annan nivå som passar dig och klicka på **Välj**.
    - Välj **Juridiska villkor** och klicka på **Köp**.
    - Klicka på **OK**.

9. Klicka på **Skapa**.

    Azure skapar nu WordPress-appen utifrån din konfiguration. Du bör se meddelandet **Distributionen har påbörjats**.

    ![Distributionen har startat – första WordPress i Azure App Service](./media/app-service-web-get-started-php-portal/deployment-started.png)
   
## <a name="launch-and-manage-your-wordpress-web-app"></a>Starta och hantera WordPress-webbappen

När appen har distribuerats i Azure visas ett nytt meddelande.

![Distributionen har utförts – första WordPress i Azure App Service](./media/app-service-web-get-started-php-portal/deployment-succeeded.png)

1. Klicka på meddelandet. Om du missade meddelandet kan du alltid visa det genom att klicka på meddelandeikonen (![Meddelandeikon – första WordPress i Azure App Service](./media/app-service-web-get-started-dotnet-portal/notification.png)).

    Du bör nu se webbappens [administrationsblad](../azure-resource-manager/resource-group-portal.md#manage-resources) (*blad*: en portalsida som öppnas horisontellt).

3. Klicka på **Bläddra** längst upp på sidan Översikt.
   
    ![Bläddra – första WordPress i Azure App Service](./media/app-service-web-get-started-php-portal/browse.png)

    Nu visas **välkomstsidan** för WordPress. Konfigurera WordPress-installationen och prova några funktioner!

    ![WordPress-konfiguration – första WordPress i Azure App Service](./media/app-service-web-get-started-php-portal/wordpress-config.png)
    
## <a name="next-steps"></a>Nästa steg
* [Skapa, konfigurera och distribuera en Laravel-webbapp till Azure](app-service-web-php-get-started.md) – Lär dig grunderna du behöver för att köra valfri PHP-webbapp i Azure, som:

    * Skapa och konfigurera appar i Azure från PowerShell/Bash.
    * Ange PHP-version.
    * Använda en startfil som inte finns i rotkatalogen för programmet.
    * Aktivera Composer-automatisering.
    * Få åtkomst till miljöspecifika variabler.
    * Felsöka vanliga fel.

* [Distribuera din kod till Azure App Service](web-sites-deploy.md) – lär dig hur du distribuerar via FTP eller källkodsdatabaser.
* [Lägg in funktioner i din första webbapp](app-service-web-get-started-2.md) – ta din Azure-app till nästa nivå. Autentisera användarna. Skala den på begäran. Konfigurera prestandavarningar. Allt med några få klickningar.

