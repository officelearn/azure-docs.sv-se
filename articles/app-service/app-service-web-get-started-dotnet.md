---
title: 'Snabb start: skapa en C# ASP.NET Core-app'
description: Lär dig hur du kör Web Apps i Azure App Service genom att distribuera standard mal len C# ASP.NET Core webbapp från Visual Studio.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 04/22/2020
ms.custom: mvc, devcenter, vs-azure, seodec18
ms.openlocfilehash: b7566260786b6fcfa639f244e2eff0cc8304ed9e
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85205803"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Snabb start: skapa en ASP.NET Core webbapp i Azure

I den här snabb starten får du lära dig hur du skapar och distribuerar din första ASP.NET Core-webbapp till [Azure App Service](overview.md). 

När du är klar har du en Azure-resurs grupp som består av en App Service värd plan och en App Service med ett distribuerat webb program.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/dotnet/).
- Den här snabb starten distribuerar en app till App Service i Windows. Om du vill distribuera en app till App Service i _Linux_ kan du läsa [Skapa en .NET Core-webbapp i App Service på Linux](./containers/quickstart-dotnetcore.md).
- Installera <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> med arbets belastningen **ASP.net och webb utveckling** .

  Om du redan har installerat Visual Studio 2019:

  - Installera de senaste uppdateringarna i Visual Studio genom att välja **Hjälp**  >  **sökning efter uppdateringar**.
  - Lägg till arbets belastningen genom att välja **verktyg**  >  **Hämta verktyg och funktioner**.


## <a name="create-an-aspnet-core-web-app"></a>Skapa en ASP.NET Core-webbapp

Skapa en ASP.NET Core webbapp i Visual Studio genom att följa dessa steg:

1. Öppna Visual Studio och välj **skapa ett nytt projekt**.

1. I **skapa ett nytt projekt**väljer du **ASP.net Core webb program** och bekräftar att **C#** visas på de språk som du väljer. Välj sedan **Nästa**.

1. I **Konfigurera ditt nya projekt**namnger du ditt webb program projekt *MyFirstAzureWebApp*och väljer **skapa**.

   ![Konfigurera ditt webbapp](./media/app-service-web-get-started-dotnet/configure-web-app-project.png)

1. Du kan distribuera vilken typ av ASP.NET Core webbapp som helst till Azure, men i den här snabb starten väljer du mallen för **webb program** . Kontrol lera att **autentiseringen** är inställd på **Ingen autentisering**och att inget annat alternativ har valts. Välj sedan **skapa**.

   ![Skapa en ny ASP.NET Core webbapp](./media/app-service-web-get-started-dotnet/create-aspnet-core-web-app.png) 
   
1. Från Visual Studio-menyn väljer du **Felsök**  >  **Start utan fel sökning** för att köra din webbapp lokalt.

   ![Webbapp som körs lokalt](./media/app-service-web-get-started-dotnet/web-app-running-locally.png)

## <a name="publish-your-web-app"></a>Publicera din webbapp

Om du vill publicera din webbapp måste du först skapa och konfigurera en ny App Service som du kan publicera din app till. 

Som en del av att konfigurera App Service skapar du:

- En ny [resurs grupp](https://docs.microsoft.com/azure/azure-resource-manager/management/overview#terminology) som innehåller alla Azure-resurser för tjänsten.
- En ny [värd plan](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) som anger plats, storlek och funktioner för den webb Server grupp som är värd för din app.

Följ de här stegen för att skapa din App Service och publicera din webbapp:

1. I **Solution Explorer**högerklickar du på projektet **MyFirstAzureWebApp** och väljer **publicera**. Om du inte redan har loggat in på ditt Azure-konto från Visual Studio väljer du antingen **Lägg till ett konto** eller **Logga**in. Du kan också skapa ett kostnads fritt Azure-konto.

1. I dialog rutan **Välj ett publicerings mål** väljer du **App Service**, väljer **Skapa ny**och väljer sedan **Skapa profil**.

   ![Välj ett publiceringsmål](./media/app-service-web-get-started-dotnet/pick-publish-target-vs2019.png)

1. I dialog rutan **App Service: skapa ny** anger du ett globalt unikt **namn** för din app genom att antingen acceptera standard namnet eller ange ett nytt namn. Giltiga tecken är: `a-z` , `A-Z` , `0-9` , och `-` . Det här **namnet** används som URL-prefix för din webbapp i formatet `http://<app_name>.azurewebsites.net` .

1. Acceptera prenumerationen som visas i listan eller Välj en ny i list rutan för **prenumeration**.

1. I **resurs grupp**väljer du **ny**. I **nytt resurs grupp namn**anger du *MyResourceGroup* och väljer **OK**. 

1. För **värd plan**väljer du **nytt**. 

1. Ange de värden som anges i följande tabell i dialog rutan **värd plan: skapa ny** .

   | Inställningen  | Föreslaget värde | Beskrivning |
   | -------- | --------------- | ----------- |
   | **Värdplan**  | *myFirstAzureWebAppPlan* | Namnet på App Service-planen. |
   | **Position**      | *Europa, västra* | Datacenter som är värd för webbappen. |
   | **Storlek**          | *Kostnadsfri* | [Prisnivån](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avgör tillgängliga värdfunktioner. |
   
   ![Skapa ny värd plan](./media/app-service-web-get-started-dotnet/create-new-hosting-plan-vs2019.png)

1. Lämna **Application Insights** inställningen *ingen*.

1. I dialog rutan **App Service: skapa nytt** väljer du **skapa** för att börja skapa Azure-resurser.

   ![Skapa ny app service](./media/app-service-web-get-started-dotnet/create-new-app-service-vs2019.png)

1. När guiden har slutförts väljer du **publicera**.

   ![Publicera webbapp till Azure](./media/app-service-web-get-started-dotnet/publish-web-app-vs2019.png)

   Visual Studio publicerar din ASP.NET Core-webbapp till Azure och startar appen i din standard webbläsare. 

   ![Publicerad ASP.NET-webbapp som körs i Azure](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

**Grattis!** Din ASP.NET Core-webbapp körs Live i Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Uppdatera och distribuera om appen

Följ dessa steg för att uppdatera och distribuera om din webbapp:

1. Öppna **Solution Explorer** **Pages**  >  **index. cshtml**under ditt projekt i Solution Explorer.

1. Ersätt hela `<div>` taggen med följande kod:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Högerklicka på projektet **myFirstAzureWebApp** i **Solution Explorer** och välj **Publicera** för att distribuera om appen till Azure.

1. På sidan **publicera** Sammanfattning väljer du **publicera**.

   ![Publicera uppdatering av webbapp](./media/app-service-web-get-started-dotnet/publish-update-to-web-app-vs2019.png)

När publiceringen är klar startar Visual Studio en webbläsare till webbappens URL.

![Uppdaterade ASP.NET-webbappen som körs i Azure](./media/app-service-web-get-started-dotnet/updated-web-app-running-live.png)

## <a name="manage-the-azure-app"></a>Hantera Azure-appen

Om du vill hantera din webbapp går du till [Azure Portal](https://portal.azure.com)och söker efter och väljer **app Services**.

![Välj App Services](./media/app-service-web-get-started-dotnet/app-services.png)

På sidan **app Services** väljer du namnet på din webbapp.

![Portalnavigering till Azure-app](./media/app-service-web-get-started-dotnet/select-app-service.png)

**Översikts** sidan för din webbapp innehåller alternativ för grundläggande hantering som att bläddra, stoppa, starta, starta om och ta bort. Den vänstra menyn innehåller fler sidor för att konfigurera din app.

![App Service i Azure Portal](./media/app-service-web-get-started-dotnet/web-app-overview-page.png)

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten använde du Visual Studio för att skapa och distribuera en ASP.NET Core-webbapp till Azure App Service.

Gå vidare till nästa artikel om du vill lära dig hur du skapar en .NET Core-app och ansluter den till en SQL Database:

> [!div class="nextstepaction"]
> [ASP.NET Core med SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md)

> [!div class="nextstepaction"]
> [Konfigurera ASP.NET Core app](configure-language-dotnetcore.md)
