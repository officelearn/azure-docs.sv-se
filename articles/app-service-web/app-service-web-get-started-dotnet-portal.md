---
title: "Distribuera en Umbraco-webbapp i Azure Portal på fem minuter | Microsoft Docs"
description: "Distribuera en ASP.NET-exempelapp och se hur enkelt det är att köra webbappar i App Service. Resultatet visas omedelbart."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/10/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 6b1dede903083d1771733330a069b6ab533d9f00
ms.lasthandoff: 04/06/2017


---
# <a name="deploy-an-umbraco-web-app-in-the-azure-portal-in-five-minutes"></a>Distribuera en Umbraco-webbapp i Azure Portal på fem minuter

I de här självstudierna lär du dig hur du distribuerar en [Umbraco](https://our.umbraco.org/)-webbapp till [Azure App Service](../app-service/app-service-value-prop-what-is.md) på bara några minuter.

![Umbraco-app](./media/app-service-web-get-started-dotnet-portal/defaultpage.png)

## <a name="prerequisites"></a>Krav
Du behöver ett Microsoft Azure-konto. Om du inte har ett konto kan du [registrera dig för en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) eller [aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> Du kan [Prova App Service](https://azure.microsoft.com/try/app-service/) utan ett Azure-konto. Skapa en startapp och testa den i upp till en timme – inget kreditkort behövs, inga åtaganden.
> 
> 

## <a name="deploy-the-aspnet-app"></a>Distribuera appen ASP.NET
1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Öppna [https://portal.azure.com/#create/umbracoorg.UmbracoCMS](https://portal.azure.com/#create/umbracoorg.UmbracoCMS).

    Den här länken är en genväg du kan använda när du vill konfigurera en ny Umbraco-app direkt i Azure Portal.

3. I **Appnamn** anger du webbappens namn. Om namnet är unikt för domänen `azurewebsites.net` så visas en grön bockmarkering i rutan.
   
5. Klicka på **Skapa ny** i **Resursgrupp** om du vill skapa en ny [resursgrupp](../azure-resource-manager/resource-group-overview.md) och ge den sedan ett namn.

7. Klicka på **App Service plan/plats** > **Skapa ny**. Så här konfigurerar du din [App Service plan](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md):

    - Ange önskat namn i **App Service plan**.
    - Välj en plats för planens värd i **Plats**.
    - Klicka på **Prisnivå**, välj **F1 Kostnadsfri** eller någon annan nivå som passar dig och klicka på **Välj**.
    - Klicka på **OK**.

    Din Umbraco CMS-konfiguration ska nu se ut som i följande skärmbild:

    ![Konfigurationen pågår – första Umbraco i Azure App Service](./media/app-service-web-get-started-dotnet-portal/configure-in-progress.png)

12. Klicka på **SQL Database** > **Skapa en ny databas**. Konfigurera SQL-databasen så här:

    - Ange ett namn i fältet **Namn**, till exempel **minDB**.
    - Klicka på **Prisnivå**, välj **1 Kostnadsfri** eller någon annan nivå som passar dig och klicka på **Välj**.
    - Klicka på **Målserver** > **Skapa en ny server**. Konfigurera databasservern så här:

        - Ange ett servernamn i fältet **Servernamn**. Om namnet är unikt för domänen `.database.windows.net` så visas en grön bockmarkering i rutan.
        - Ange användarnamnet för önskad administratör i fältet **Inloggning för serveradministratör**.
        - Ange önskat lösenord i fälten I **Lösenord** och **Bekräfta lösenord**.
        - Välj samma plats som du använder för webbappen i fältet Plats.
        - Kontrollera att **Ge Azure-tjänster åtkomst till servern**.
        - Klicka på **Välj**.
    
    - Klicka på **Välj**.

13. Klicka på **Webbappinställningar**, ange användarnamn och lösenord för databasen och klicka på **OK**.

    Din Umbraco CMS-konfiguration ska nu se ut som i följande skärmbild:

    ![Konfigurationen är slutförd – första Umbraco i Azure App Service](./media/app-service-web-get-started-dotnet-portal/configure-complete.png)

14. Klicka på **Skapa**.
    
    Azure skapar nu Umbraco-appen utifrån din konfiguration. Du bör se meddelandet **Distributionen har påbörjats**.

    ![Distributionen har utförts – första Umbraco i Azure App Service](./media/app-service-web-get-started-dotnet-portal/deployment-started.png)
   
## <a name="launch-and-manage-your-umrbaco-web-app"></a>Starta och hantera Umbraco-webbappen

När appen har distribuerats i Azure visas ett nytt meddelande.

![Distributionen har utförts – första Umbraco i Azure App Service](./media/app-service-web-get-started-dotnet-portal/deployment-succeeded.png)

1. Klicka på meddelandet. Om du missade meddelandet kan du alltid visa det genom att klicka på meddelandeikonen (![Meddelandeikon – första Umbraco i Azure App Service](./media/app-service-web-get-started-dotnet-portal/notification.png)).

    Du bör nu se webbappens [administrationsblad](../azure-resource-manager/resource-group-portal.md#manage-resources) (*blad*: en portalsida som öppnas horisontellt).

3. Klicka på **Bläddra** längst upp på sidan Översikt.
   
    ![Bläddra – första Umbraco i Azure App Service](./media/app-service-web-get-started-dotnet-portal/browse.png)

    Nu visas **välkomstsidan** för Umbraco. Konfigurera Umbraco-installationen och prova några funktioner!

    ![Umbraco-konfiguration – första Umbraco i Azure App Service](./media/app-service-web-get-started-dotnet-portal/umbraco-config.png)
    
## <a name="next-steps"></a>Nästa steg
* [Distribuera en ASP.NET-webbapp till Azure App Service med Visual Studio](app-service-web-get-started-dotnet.md) – lär dig hur du använder de medföljande appmallarna till att skapa en ny Azure-webbapp i Visual Studio.
* [Distribuera din kod till Azure App Service](web-sites-deploy.md) – lär dig hur du distribuerar via FTP eller källkodsdatabaser.
* [Lägg in funktioner i din första webbapp](app-service-web-get-started-2.md) – ta din Azure-app till nästa nivå. Autentisera användarna. Skala den på begäran. Konfigurera prestandavarningar. Allt med några få klickningar.

