---
title: Skapa en C# ASP.NET Core-webbapp – Azure App Service | Microsoft Docs
description: Lär dig hur du kör webbappar i Azure App Service genom att distribuera standard-C# ASP.NET Core-webbappen.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/30/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 82a1dc293a019e4a48760ccbce830d067f2d620d
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240929"
---
# <a name="create-an-aspnet-core-web-app-in-azure"></a>Skapa en ASP.NET Core-webbapp i Azure

> [!NOTE]
> I den här artikeln distribueras en app till App Service i Windows. Om du vill distribuera en app till App Service i _Linux_ kan du läsa [Skapa en .NET Core-webbapp i App Service på Linux](./containers/quickstart-dotnetcore.md).
>

Med [Azure App Service](overview.md) får du en automatiskt uppdaterad webbvärdtjänst med hög skalbarhet.

Den här snabbstarten visar hur du distribuerar din första ASP.NET Core-webbapp till Azure App Service. När du är klar har du en resursgrupp som består av en App Service-plan och en App Service-app med en distribuerad webbapp.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien installerar du <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> med arbets belastningen **ASP.net och webb utveckling** .

Om du redan har installerat Visual Studio 2019:

- Installera de senaste uppdateringarna i Visual Studio genom att välja **Hjälp** > **sökning efter uppdateringar**.
- Lägg till arbets belastningen genom att välja **verktyg** > **Hämta verktyg och funktioner**.

## <a name="create-an-aspnet-core-web-app"></a>Skapa en ASP.NET Core-webbapp

Skapa en ASP.NET Core-webbapp genom att följa dessa steg:

1. Öppna Visual Studio och välj sedan **skapa ett nytt projekt**.

1. I **skapa ett nytt projekt**, leta upp och välj **ASP.net Core webb program** för C#och välj sedan **Nästa**.

1. I **Konfigurera ditt nya projekt**namnger du programmet _myFirstAzureWebApp_och väljer sedan **skapa**.

   ![Konfigurera ditt webbapp](./media/app-service-web-get-started-dotnet/configure-web-app-project.png)

1. I den här snabb starten väljer du mallen för **webb program** . Se till att autentisering har angetts till **Ingen autentisering** och inget annat alternativ har valts. Välj **Skapa**.

   ![Välj ASP.NET Core Razor Pages för den här självstudien](./media/app-service-web-get-started-dotnet/aspnet-razor-pages-app.png)

    Du kan distribuera alla typer av ASP.NET Core-webbappar till Azure.

1. Från Visual Studio-menyn väljer du **Felsök** > **Start utan fel sökning** för att köra webbappen lokalt.

   ![Kör appen lokalt](./media/app-service-web-get-started-dotnet/razor-web-app-running-locally.png)

## <a name="publish-your-web-app"></a>Publicera din webbapp

1. I **Solution Explorer**högerklickar du på projektet **MyFirstAzureWebApp** och väljer **publicera**.

1. Välj **App Service** och välj sedan **publicera**.

   ![Publicera från projektöversiktssidan](./media/app-service-web-get-started-dotnet/publish-app-vs2019.png)

1. I **app service skapa nya**beror alternativen på om du redan är inloggad på Azure och om du har ett Visual Studio-konto som är länkat till ett Azure-konto. Välj antingen **Lägg till ett konto** eller **Logga** in för att logga in på din Azure-prenumeration. Om du redan är inloggad väljer du det konto som du vill använda.

   > [!NOTE]
   > Välj inte **Skapa** ännu om du redan är inloggad.
   >

   ![Logga in på Azure](./media/app-service-web-get-started-dotnet/sign-in-azure-vs2019.png)

   [!INCLUDE [resource group intro text](../../includes/resource-group.md)]

1. För **resurs grupp**väljer du **nytt**.

1. I **nytt resurs grupp namn**anger du *MyResourceGroup* och väljer **OK**.

   [!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. Välj **ny**för **värd planen**.

1. I dialog rutan **Konfigurera värd plan** anger du värdena i följande tabell och väljer sedan **OK**.

   | Inställning | Föreslaget värde | Beskrivning |
   |-|-|-|
   |App Service-plan| myAppServicePlan | Namnet på App Service-planen. |
   | Location | Västra Europa | Datacenter som är värd för webbappen. |
   | Size | Kostnadsfri | [Prisnivån](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avgör tillgängliga värdfunktioner. |

   ![Skapa apptjänstplan](./media/app-service-web-get-started-dotnet/app-service-plan-vs2019.png)

1. I **namn**anger du ett unikt namn för appen som bara innehåller giltiga tecken är `a-z`, `A-Z` `0-9`, och `-`. Du kan acceptera det automatiskt genererade unika namnet. Webbadressen till webbappen är `http://<app_name>.azurewebsites.net`, där `<app_name>` är appens namn.

   ![Konfigurera appnamn](./media/app-service-web-get-started-dotnet/web-app-name-vs2019.png)

1. Välj **Skapa** för att börja skapa Azure-resurser.

När guiden slutförs publiceras ASP.NET Core-webbappen till Azure och sedan öppnas appen i standardwebbläsaren.

![Publicerad ASP.NET-webbapp i Azure](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

Namnet på appen som anges i **App Service Skapa ny** sida används som URL-prefix i formatet `http://<app_name>.azurewebsites.net`.

**Grattis!** Din ASP.NET Core-webbapp körs Live i Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Uppdatera och distribuera om appen

1. Öppna **Pages** > **index. cshtml**under ditt projekt i **Solution Explorer**.

1. Ersätt de två `<div>`-taggarna med följande kod:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Högerklicka på projektet **myFirstAzureWebApp** i **Solution Explorer** och välj **Publicera** för att distribuera om appen till Azure.

1. På sidan **publicera** Sammanfattning väljer du **publicera**.

   ![Visual Studio publicerings sammanfattnings sida](./media/app-service-web-get-started-dotnet/publish-summary-page-vs2019.png)

När publiceringen är klar startar Visual Studio en webbläsare till webbappens URL.

![Uppdaterad ASP.NET-webbapp i Azure](./media/app-service-web-get-started-dotnet/web-app-running-live-updated.png)

## <a name="manage-the-azure-app"></a>Hantera Azure-appen

1. Gå till <a href="https://portal.azure.com" target="_blank">Azure Portal</a> för att hantera webbappen.

1. Klicka på **App Services** i menyn till vänster och välj sedan namnet på din Azure-app.

   ![Portalnavigering till Azure-app](./media/app-service-web-get-started-dotnet/access-portal-vs2019.png)

   Nu visas sidan Översikt för din webbapp. Här kan du utföra grundläggande hantering som att bläddra, stoppa, starta, starta om och ta bort.

   ![App Service i Azure Portal](./media/app-service-web-get-started-dotnet/web-app-general-vs2019.png)

   Menyn till vänster innehåller olika sidor för att konfigurera appen.

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [ASP.NET Core med SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md)
