---
title: Skapa en ASP.NET Framework-webbapp i C# – Azure App Service | Microsoft Docs
description: Distribuera den C#-baserade standard-ASP.NET-webbappen och lär dig att köra webbappar i Azure App Service.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 04a1becf-7756-4d4e-92d8-d9471c263d23
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/30/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 4f479ad60d74f1c7381b5fb776c5508aaa0785f1
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242168"
---
# <a name="create-an-aspnet-framework-web-app-in-azure"></a>Skapa en ASP.NET Framework-webbapp i Azure

Med [Azure App Service](overview.md) får du en automatiskt uppdaterad webbvärdtjänst med hög skalbarhet.

Den här snabbstarten visar hur du distribuerar din första ASP.NET-webbapp till Azure App Service. När du är klar har du en App Service plan. Du har också en App Service-app med ett distribuerat webb program.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien installerar du <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> med arbets belastningen **ASP.net och webb utveckling** .

Om du redan har installerat Visual Studio 2019:

- Installera de senaste uppdateringarna i Visual Studio genom att välja **Hjälp** > **sökning efter uppdateringar**.
- Lägg till arbets belastningen genom att välja **verktyg** > **Hämta verktyg och funktioner**.

## Skapa en ASP.NET-webbapp<a name="create-and-publish-the-web-app"></a>

Skapa en ASP.NET-webbapp genom att följa dessa steg:

1. Öppna Visual Studio och välj sedan **skapa ett nytt projekt**.

1. I **skapa ett nytt projekt**, leta upp och välj **ASP.NET-webbprogram (.NET Framework)** för C#och välj sedan **Nästa**.

1. I **Konfigurera ditt nya projekt**namnger du programmet _myFirstAzureWebApp_och väljer sedan **skapa**.

   ![Konfigurera ditt webbapp](./media/app-service-web-get-started-dotnet-framework/configure-web-app-project-framework.png)

1. Du kan distribuera alla typer av ASP.NET-webbappar till Azure. I den här snabb starten väljer du **MVC** -mallen. 

1. Kontrol lera att autentiseringen är inställd på **Ingen autentisering**. Välj **Skapa**.

   ![Skapa ASP.NET-webbprogram](./media/app-service-web-get-started-dotnet-framework/select-mvc-template-vs2019.png)

1. Från Visual Studio-menyn väljer du **Felsök** > **Start utan fel sökning** för att köra webbappen lokalt.

   ![Kör appen lokalt](./media/app-service-web-get-started-dotnet-framework/local-web-app.png)

## Publicera din webbapp<a name="launch-the-publish-wizard"></a>

1. I **Solution Explorer**högerklickar du på projektet **MyFirstAzureWebApp** och väljer **publicera**.

1. Välj **App Service** och välj sedan **publicera**.

   ![Publicera från projektöversiktssidan](./media/app-service-web-get-started-dotnet-framework/publish-app-framework-vs2019.png)

1. I **app service skapa nya**beror alternativen på om du redan är inloggad på Azure och om du har ett Visual Studio-konto som är länkat till ett Azure-konto. Välj antingen **Lägg till ett konto** eller **Logga** in för att logga in på din Azure-prenumeration. Om du redan är inloggad väljer du det konto som du vill använda.

   > [!NOTE]
   > Välj inte **Skapa** ännu om du redan är inloggad.
   >
   >

   ![Logga in på Azure](./media/app-service-web-get-started-dotnet-framework/sign-in-azure-framework-vs2019.png)

   [!INCLUDE [resource group intro text](../../includes/resource-group.md)]

1. För **resurs grupp**väljer du **nytt**.

1. I **nytt resurs grupp namn**anger du *MyResourceGroup* och väljer **OK**.

   [!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. Välj **ny**för att vara **värd för planen**.

1. I dialog rutan **Konfigurera värd plan** anger du värdena i följande tabell och väljer sedan **OK**.

   | Inställning | Föreslaget värde | Beskrivning |
   |-|-|-|
   |App Service-plan| myAppServicePlan | Namnet på App Service-planen. |
   | Location | Västra Europa | Datacenter som är värd för webbappen. |
   | Size | Kostnadsfri | [Prisnivån](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avgör tillgängliga värdfunktioner. |

   ![Skapa apptjänstplan](./media/app-service-web-get-started-dotnet-framework/app-service-plan-framework-vs2019.png)

1. I **namn**anger du ett unikt namn för appen som bara innehåller giltiga tecken är `a-z`, `A-Z` `0-9`, och `-`. Du kan acceptera det automatiskt genererade unika namnet. Webbadressen till webbappen är `http://<app_name>.azurewebsites.net`, där `<app_name>` är appens namn.

   ![Konfigurera appnamn](./media/app-service-web-get-started-dotnet-framework/web-app-name-framework-vs2019.png)

1. Välj **Skapa** för att börja skapa Azure-resurser.

När guiden slutförs publiceras ASP.NET-webbappen till Azure och sedan öppnas appen i standardwebbläsaren.

![Publicerad ASP.NET-webbapp i Azure](./media/app-service-web-get-started-dotnet-framework/published-azure-web-app.png)

Namnet på appen som anges i **App Service Skapa ny** sida används som URL-prefix i formatet `http://<app_name>.azurewebsites.net`.

**Grattis!** Din ASP.NET-webbapp körs Live i Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Uppdatera och distribuera om appen

1. I **Solution Explorer**, under projektet, öppnar du **vyer** > **Start** > **index. cshtml**.

1. Leta reda på HTML-taggen `<div class="jumbotron">` längst upp på sidan och ersätt hela elementet med följande kod:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Högerklicka på projektet **myFirstAzureWebApp** i **Solution Explorer** och välj **Publicera** för att distribuera om appen till Azure.

1. På sidan **publicera** Sammanfattning väljer du **publicera**.

   ![Sidan Visual Studio Summary för publicering](./media/app-service-web-get-started-dotnet-framework/publish-summary-page-framework-vs2019.png)

När publiceringen är klar startar Visual Studio en webbläsare till webbappens URL.

![Uppdaterad ASP.NET-webbapp i Azure](./media/app-service-web-get-started-dotnet-framework/updated-azure-web-app.png)

## <a name="manage-the-azure-app"></a>Hantera Azure-appen

1. Gå till <a href="https://portal.azure.com" target="_blank">Azure Portal</a> för att hantera webbappen.

1. Klicka på **App Services** i menyn till vänster och välj sedan namnet på din Azure-app.

   ![Portalnavigering till Azure-app](./media/app-service-web-get-started-dotnet-framework/access-portal-framework-vs2019.png)

   Nu visas sidan Översikt för din webbapp. Här kan du utföra grundläggande hantering som att bläddra, stoppa, starta, starta om och ta bort.

   ![App Service översikt i Azure Portal](./media/app-service-web-get-started-dotnet-framework/web-app-general-framework-vs2019.png)

   Menyn till vänster innehåller olika sidor för att konfigurera appen.

## <a name="video"></a>Video

Titta på videon som beskriver den här snabbstarten och följ sedan stegen för att publicera din första .NET-app i Azure.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-for-NET-Developers/Create-a-NET-app-in-Azure-Quickstart/player]

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [ASP.NET med SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
