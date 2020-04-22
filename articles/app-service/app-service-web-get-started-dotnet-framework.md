---
title: 'Snabbstart: Skapa en C# ASP.NET-app'
description: Lär dig hur du kör webbappar i Azure App Service genom att distribuera standardmallen C# ASP.NET webbapp från Visual Studio.
ms.assetid: 04a1becf-7756-4d4e-92d8-d9471c263d23
ms.topic: quickstart
ms.date: 04/21/2020
ms.custom: mvc, devcenter, seodec18
ms.openlocfilehash: 47a5cf2586b8146a68772962c60dc0dbdbb7e2fe
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768861"
---
# <a name="create-an-aspnet-framework-web-app-in-azure"></a>Skapa en ASP.NET Framework-webbapp i Azure

[Azure App Service](overview.md) tillhandahåller en mycket skalbar, självkorrigering webbhotell.

Den här snabbstarten visar hur du distribuerar din första ASP.NET-webbapp till Azure App Service. När du är klar har du en appserviceplan. Du har också en App Service-app med ett distribuerat webbprogram.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

Slutför den här självstudien genom att installera <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> med **arbetsbelastningen för ASP.NET och webbutveckling.**

Om du redan har installerat Visual Studio 2019:

- Installera de senaste uppdateringarna i Visual Studio genom att välja **Hjälp** > **sök efter uppdateringar**.
- Lägg till arbetsbelastningen genom att välja **Verktyg** > **Hämta verktyg och funktioner**.

## <a name="create-an-aspnet-web-app"></a>Skapa en ASP.NET webbapp<a name="create-and-publish-the-web-app"></a>

Skapa en ASP.NET webbapp genom att följa dessa steg:

1. Öppna Visual Studio och välj sedan **Skapa ett nytt projekt**.

2. Leta reda på och välja **ASP.NET webbprogram (.NET Framework)** i **Skapa ett nytt projekt**och välj sedan **Nästa**.

3. I **Konfigurera ditt nya projekt**namnger du programmet _myFirstAzureWebApp_och väljer sedan **Skapa**.

   ![Konfigurera webbappprojektet](./media/app-service-web-get-started-dotnet-framework/configure-web-app-project-framework.png)

4. Du kan distribuera alla typer av ASP.NET-webbappar till Azure. För den här snabbstarten väljer du **MVC-mallen.**

5. Kontrollera att autentiseringen är inställd på **Ingen autentisering**. Välj **Skapa**.

   ![Skapa ASP.NET webbprogram](./media/app-service-web-get-started-dotnet-framework/select-mvc-template-vs2019.png)

6. På Visual Studio-menyn väljer du **Felsökning** > **start utan felsökning** för att köra webbappen lokalt.

   ![Kör appen lokalt](./media/app-service-web-get-started-dotnet-framework/local-web-app.png)

## <a name="publish-your-web-app"></a>Publicera din webbapp<a name="launch-the-publish-wizard"></a>

1. Högerklicka på **projektet myFirstAzureWebApp** i **Solution Explorer**och välj **Publicera**.

1. Välj **Apptjänst** och välj **Skapa profil**.

   ![Publicera från projektöversiktssidan](./media/app-service-web-get-started-dotnet-framework/publish-app-framework-vs2019.png)

1. I **App Service Skapa nya**beror dina alternativ på om du redan är inloggad på Azure och om du har ett Visual Studio-konto kopplat till ett Azure-konto. Välj antingen **Lägg till ett konto** eller Logga **in** för att logga in på din Azure-prenumeration. Om du redan är inloggad väljer du det konto du vill använda.

   > [!NOTE]
   > Välj inte **Skapa** ännu om du redan är inloggad.
   >
   >

   ![Logga in på Azure](./media/app-service-web-get-started-dotnet-framework/sign-in-azure-framework-vs2019.png)

   [!INCLUDE [resource group intro text](../../includes/resource-group.md)]

1. För **resursgrupp**väljer du **Nytt**.

1. Ange *myResourceGroup* i **Nytt resursgruppsnamn**och välj **OK**.

   [!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. Välj **Nytt**för att planera för **hostingplan**.

1. I dialogrutan **Konfigurera värdplan** anger du värdena i följande tabell och väljer sedan **OK**.

   | Inställning | Föreslaget värde | Beskrivning |
   |-|-|-|
   | Värdplan| myAppServicePlan | Namnet på App Service-planen. |
   | Location | Europa, västra | Datacenter som är värd för webbappen. |
   | Storlek | Kostnadsfri | [Prisnivån](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avgör tillgängliga värdfunktioner. |

   ![Skapa apptjänstplan](./media/app-service-web-get-started-dotnet-framework/app-service-plan-framework-vs2019.png)

1. I **Namn**anger du ett unikt appnamn `a-z`som `A-Z`bara innehåller giltiga tecken som är , , `0-9`och `-`. Du kan acceptera det automatiskt genererade unika namnet. Webbadressen till webbappen är `http://<app-name>.azurewebsites.net`, där `<app-name>` är appens namn.

2. Välj **Skapa** för att börja skapa Azure-resurser.

   ![Konfigurera appnamn](./media/app-service-web-get-started-dotnet-framework/web-app-name-framework-vs2019.png)

    När guiden är klar skapas Azure-resurserna åt dig och du är redo att publicera.

3. Klicka på **Publicera**på sidan **Publicera.** Visual Studio bygger, paketar och publicerar appen till Azure och startar sedan appen i standardwebbläsaren.

    ![Publicerad ASP.NET-webbapp i Azure](./media/app-service-web-get-started-dotnet-framework/published-azure-web-app.png)

Det appnamn som anges på sidan **Skapa ny apptjänst** används `http://<app-name>.azurewebsites.net`som URL-prefix i formatet .

**Grattis!** Din ASP.NET webbapp körs live i Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Uppdatera och distribuera om appen

1. Öppna **Views** > **Home** > **Index.cshtml**i Solution **Explorer**i Lösningsutforskaren .

1. Leta reda på HTML-taggen `<div class="jumbotron">` längst upp på sidan och ersätt hela elementet med följande kod:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Högerklicka på projektet **myFirstAzureWebApp** i **Solution Explorer** och välj **Publicera** för att distribuera om appen till Azure. Välj sedan **Publicera**.

    När publiceringen är klar startar Visual Studio en webbläsare till webbappens URL.

    ![Uppdaterad ASP.NET-webbapp i Azure](./media/app-service-web-get-started-dotnet-framework/updated-azure-web-app.png)

## <a name="manage-the-azure-app"></a>Hantera Azure-appen

1. Om du vill hantera webbappen går du till [Azure-portalen](https://portal.azure.com)och söker efter och väljer **App Services**.

   ![Välj App-tjänster](./media/app-service-web-get-started-dotnet-framework/app-services.png)

2. På sidan **AppTjänster** väljer du namnet på webbappen.

   ![Portalnavigering till Azure-app](./media/app-service-web-get-started-dotnet-framework/access-portal-framework-vs2019.png)

   Nu visas sidan Översikt för din webbapp. Här kan du göra grundläggande hantering som bläddra, stoppa, starta, starta om och ta bort.

   ![Översikt över App-tjänsten i Azure-portalen](./media/app-service-web-get-started-dotnet-framework/web-app-general-framework-vs2019.png)

   Menyn till vänster innehåller olika sidor för att konfigurera appen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [ASP.NET med SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
