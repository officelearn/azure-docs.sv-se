---
title: 'Snabbstart: Skapa en C# ASP.NET Core-app'
description: Lär dig hur du kör webbappar i Azure App Service genom att distribuera standardmallen C# ASP.NET Core-webbapp från Visual Studio.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 03/17/2020
ms.custom: mvc, devcenter, vs-azure, seodec18
ms.openlocfilehash: be7c4a2fb65f913bc97617af1a0f21ee8fcca714
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313288"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Snabbstart: Skapa en ASP.NET Core-webbapp i Azure

I den här snabbstarten får du lära dig hur du skapar och distribuerar din första ASP.NET Core-webbapp till [Azure App Service](overview.md). 

När du är klar har du en Azure-resursgrupp som består av en App Service-värdplan och en App-tjänst med ett distribuerat webbprogram.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/dotnet/).
- Den här snabbstarten distribuerar en app till App Service i Windows. Om du vill distribuera en app till App Service i _Linux_ kan du läsa [Skapa en .NET Core-webbapp i App Service på Linux](./containers/quickstart-dotnetcore.md).
- Installera <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> med **arbetsbelastningen ASP.NET och webbutveckling.**

  Om du redan har installerat Visual Studio 2019:

  - Installera de senaste uppdateringarna i Visual Studio genom att välja **Hjälp** > **sök efter uppdateringar**.
  - Lägg till arbetsbelastningen genom att välja **Verktyg** > **Hämta verktyg och funktioner**.


## <a name="create-an-aspnet-core-web-app"></a>Skapa en ASP.NET Core-webbapp

Skapa en ASP.NET Core-webbapp i Visual Studio genom att följa följande:

1. Öppna Visual Studio och välj **Skapa ett nytt projekt**.

1. I **Skapa ett nytt projekt**väljer du ASP.NET Core Web **Application** och bekräftar att **C#** visas på språken för det valet och väljer sedan **Nästa**.

1. I **Konfigurera ditt nya projekt**namnger du webbprogrammets projekt *myFirstAzureWebApp*och väljer **Skapa**.

   ![Konfigurera webbappprojektet](./media/app-service-web-get-started-dotnet/configure-web-app-project.png)

1. Du kan distribuera alla typer av ASP.NET Core-webbapp till Azure, men för den här snabbstarten väljer du **webbprogrammallen.** Kontrollera att **autentisering** är inställt på **Ingen autentisering**och att inget annat alternativ är markerat. Välj sedan **Create** (Skapa).

   ![Skapa en ny ASP.NET Core-webbapp](./media/app-service-web-get-started-dotnet/create-aspnet-core-web-app.png) 
   
1. På Visual Studio-menyn väljer du **Felsökning** > **start utan felsökning** för att köra webbappen lokalt.

   ![Webbapp som körs lokalt](./media/app-service-web-get-started-dotnet/web-app-running-locally.png)

## <a name="publish-your-web-app"></a>Publicera din webbapp

Om du vill publicera webbappen måste du först skapa och konfigurera en ny Apptjänst som du kan publicera appen på. 

Som en del av inrättandet av App-tjänsten skapar du:

- En ny [resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/management/overview#terminology) som innehåller alla Azure-resurser för tjänsten.
- En ny [värdplan](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) som anger platsen, storleken och funktionerna i webbservergruppen som är värd för din app.

Så här skapar du apptjänsten och publicerar webbappen:

1. Högerklicka på **projektet myFirstAzureWebApp** i **Solution Explorer**och välj **Publicera**. Om du inte redan har loggat in på ditt Azure-konto från Visual Studio väljer du antingen **Lägg till ett konto** eller Logga **in**. Du kan också skapa ett kostnadsfritt Azure-konto.

1. I dialogrutan **Välj ett publiceringsmål** väljer du **Apptjänst,** väljer **Skapa ny**och väljer sedan **Skapa profil**.

   ![Välj ett publiceringsmål](./media/app-service-web-get-started-dotnet/pick-publish-target-vs2019.png)

1. I **dialogrutan Apptjänst: Skapa nytt,** ange ett globalt unikt **namn** för din app genom att antingen acceptera standardnamnet eller ange ett nytt namn. Giltiga tecken `a-z`är: `0-9`, `-` `A-Z`, och . Det här **namnet** används som URL-prefix för `http://<app_name>.azurewebsites.net`webbappen i formatet .

1. För **Prenumeration**godkänner du prenumerationen som visas eller väljer en ny i listrutan.

1. Välj **Nytt**i **resursgrupp**. Ange *myResourceGroup* i **Nytt resursgruppsnamn**och välj **OK**. 

1. För **hostingplan**väljer du **Nytt**. 

1. I **dialogrutan Värdplan: Skapa nya** anger du de värden som anges i följande tabell:

   | Inställning  | Föreslaget värde | Beskrivning |
   | -------- | --------------- | ----------- |
   | **Värdplan**  | *myFirstAzureWebAppPlan* | Namnet på App Service-planen. |
   | **Location**      | *Europa, västra* | Datacenter som är värd för webbappen. |
   | **Storlek**          | *Gratis* | [Prisnivån](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avgör tillgängliga värdfunktioner. |
   
   ![Skapa ny värdplan](./media/app-service-web-get-started-dotnet/create-new-hosting-plan-vs2019.png)

1. Lämna **Application Insights** inställt på *Ingen*.

1. I dialogrutan **Apptjänst: Skapa ny** dialogruta väljer du **Skapa** för att börja skapa Azure-resurser.

   ![Skapa ny apptjänst](./media/app-service-web-get-started-dotnet/create-new-app-service-vs2019.png)

1. När guiden är klar väljer du **Publicera**.

   ![Publicera webbapp till Azure](./media/app-service-web-get-started-dotnet/publish-web-app-vs2019.png)

   Visual Studio publicerar din ASP.NET Core-webbapp till Azure och startar appen i standardwebbläsaren. 

   ![Publicerad ASP.NET webbapp som körs i Azure](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

**Grattis!** Din ASP.NET Core-webbapp körs live i Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Uppdatera och distribuera om appen

Så här uppdaterar och distribuerar du webbappen:

1. Öppna **Sidor** > **Index.cshtml**i **Solution Explorer**.

1. Ersätt de två `<div>`-taggarna med följande kod:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Högerklicka på projektet **myFirstAzureWebApp** i **Solution Explorer** och välj **Publicera** för att distribuera om appen till Azure.

1. På sidan **Publicera** sammanfattning väljer du **Publicera**.

   ![Publicera uppdatering till webbapp](./media/app-service-web-get-started-dotnet/publish-update-to-web-app-vs2019.png)

När publiceringen är klar startar Visual Studio en webbläsare till webbappens URL.

![Uppdaterad ASP.NET webbapp som körs i Azure](./media/app-service-web-get-started-dotnet/updated-web-app-running-live.png)

## <a name="manage-the-azure-app"></a>Hantera Azure-appen

Om du vill hantera din webbapp går du till [Azure-portalen](https://portal.azure.com)och söker efter och väljer **App Services**.

![Välj Apptjänster](./media/app-service-web-get-started-dotnet/app-services.png)

På sidan **AppTjänster** väljer du namnet på webbappen.

![Portalnavigering till Azure-app](./media/app-service-web-get-started-dotnet/select-app-service.png)

Sidan **Översikt** för din webbapp innehåller alternativ för grundläggande hantering som att bläddra, stoppa, starta, starta om och ta bort. Den vänstra menyn innehåller ytterligare sidor för att konfigurera appen.

![Apptjänst i Azure-portalen](./media/app-service-web-get-started-dotnet/web-app-overview-page.png)

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten använde du Visual Studio för att skapa och distribuera en ASP.NET Core-webbapp till Azure App Service.

Gå vidare till nästa artikel om du vill lära dig hur du skapar en .NET Core-app och ansluter den till en SQL-databas:

> [!div class="nextstepaction"]
> [ASP.NET Core med SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md)
