---
title: Skapa en ASP.NET-webbapp i Azure | Microsoft Docs
description: "Distribuera standard-ASP.NET-webbappen och lär dig att köra webbappar i Azure App Service."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 06/14/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 2b447bcc5930550af3996cb40925ab59d203dc7c
ms.contentlocale: sv-se
ms.lasthandoff: 06/20/2017

---
# Skapa en ASP.NET-webbapp i Azure
<a id="create-an-aspnet-web-app-in-azure" class="xliff"></a>

Med [Azure Web Apps](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview) får du en mycket skalbar och automatiskt uppdaterad webbvärdtjänst.  Den här snabbstarten visar hur du distribuerar din första ASP.NET-webbapp till Azure Web Apps. När du är klar har du en resursgrupp som består av en App Service-plan och en Azure-webbapp med en distribuerad webbapp.

![ASP.NET-webbapp i Azure App Service](./media/app-service-web-get-started-dotnet/updated-azure-web-app.png)

## Krav
<a id="prerequisites" class="xliff"></a>

För att slutföra den här självstudien behöver du:

* Installera [Visual Studio 2017](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx) med följande arbetsbelastningar:
    - **ASP.NET och webbutveckling**
    - **Azure Development**

    ![ASP.NET och webbutveckling och Azure Development (under webb och moln)](media/app-service-web-tutorial-dotnet-sqldatabase/workloads.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## Skapa en ASP.NET-webbapp
<a id="create-an-aspnet-web-app" class="xliff"></a>

Skapa ett nytt projekt i Visual Studio genom att välja **Arkiv > Nytt > Projekt**. 

I dialogrutan **Nytt projekt** väljer du **Visual C# > Webb > ASP.NET-webbtillämpningsprogram (.NET Framework)**.

Ge programmet namnet _myFirstAzureWebApp_ och välj **OK**.
   
![Dialogrutan Nytt projekt](./media/app-service-web-get-started-dotnet/new-project.png)

Du kan distribuera alla typer av ASP.NET-webbappar till Azure. I den här snabbstarten väljer du **MVC**-mallen och ser till att autentiseringen är inställd på **Ingen autentisering**.
      
Välj **OK**.

![Dialogrutan Nytt ASP.NET-projekt](./media/app-service-web-get-started-dotnet/select-mvc-template.png)

På menyn väljer du **Felsöka > Starta utan felsökning** för att köra webbappen lokalt.

![Kör appen lokalt](./media/app-service-web-get-started-dotnet/local-web-app.png)

## Publicera till Azure
<a id="publish-to-azure" class="xliff"></a>

Högerklicka på projektet **myFirstAzureWebApp** i **Solution Explorer** och välj **Publicera**.

![Publicera från Solution Explorer](./media/app-service-web-get-started-dotnet/solution-explorer-publish.png)

Se till att **Microsoft Azure App Service** är markerat och välj **Publicera**.

![Publicera från projektöversiktssidan](./media/app-service-web-get-started-dotnet/publish-to-app-service.png)

Då öppnas dialogrutan **Skapa App Service** där du får hjälp med att skapa alla Azure-resurser du behöver för att köra ASP.NET-webbappen i Azure.

## Logga in på Azure
<a id="sign-in-to-azure" class="xliff"></a>

I dialogrutan **Skapa App Service** väljer du **Lägg till ett konto** och loggar sedan in med din Azure-prenumeration. Om du redan är inloggad kontrollerar du att kontot har en Azure-prenumeration. Du kan välja det inloggade kontot för att lägga till rätt konto.

> [!NOTE]
> Välj inte **Skapa** ännu om du redan är inloggad.
>
>
   
![Logga in på Azure](./media/app-service-web-get-started-dotnet/sign-in-azure.png)

När du har loggat in kan du skapa alla resurser du behöver för din Azure-webbapp i den här dialogrutan.

## Skapa en resursgrupp
<a id="create-a-resource-group" class="xliff"></a>

[!INCLUDE [resource group intro text](../../includes/resource-group.md)]

Välj **Ny** bredvid **Resursgrupp**.

Ge resursgruppen namnet **myResourceGroup** och välj **OK**.

## Skapa en App Service-plan
<a id="create-an-app-service-plan" class="xliff"></a>

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

Välj **Ny** bredvid **App Service-plan**. 

I dialogrutan **Configure App Service Plan** (Konfigurera App Service-plan) använder du inställningarna i tabellen som följer skärmbilden.

![Skapa apptjänstplan](./media/app-service-web-get-started-dotnet/configure-app-service-plan.png)

| Inställning | Föreslaget värde | Beskrivning |
|-|-|-|
|App Service-plan| myAppServicePlan | Namnet på App Service-planen. |
| Plats | Västra Europa | Datacenter som är värd för webbappen. |
| Storlek | Kostnadsfri | [Prisnivån](https://azure.microsoft.com/pricing/details/app-service/) avgör tillgängliga värdfunktioner. |

Välj **OK**.

## Skapa och publicera webbappen
<a id="create-and-publish-the-web-app" class="xliff"></a>

Ange ett unikt appnamn i **Web App Name** (Namn på webbapp) (giltiga tecken är `a-z`, `0-9` och `-`). Webbadressen till webbappen är `http://<app_name>.azurewebsites.net`, där `<app_name>` är webbappens namn. 

Du kan godkänna namnet som genereras automatiskt och som är unikt.

Välj **Skapa** för att börja skapa Azure-resurser.

![Ange webbappnamn](./media/app-service-web-get-started-dotnet/web-app-name.png)

När guiden slutförs publiceras ASP.NET-webbappen till Azure och sedan öppnas appen i standardwebbläsaren.

![Publicerad ASP.NET-webbapp i Azure](./media/app-service-web-get-started-dotnet/published-azure-web-app.png)

I URL:en används det webbappnamn som du angav tidigare, med formatet `http://<app_name>.azurewebsites.net`. 

Grattis, din ASP.NET-webbapp körs live i Azure App Service.

## Uppdatera och distribuera om appen
<a id="update-the-app-and-redeploy" class="xliff"></a>

Öppna _Views\Home\Index.cshtml_ från **Solution Explorer**.

Leta reda på HTML-taggen `<div class="jumbotron">` längst upp på sidan och ersätt hela elementet med följande kod:

```HTML
<div class="jumbotron">
    <h1>ASP.NET in Azure!</h1>
    <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
</div>
```

Högerklicka på projektet **myFirstAzureWebApp** i **Solution Explorer** och välj **Publicera** för att distribuera om appen till Azure.

Välj **Publicera** på publiceringssidan.

När publiceringen är klar startar Visual Studio en webbläsare till webbappens URL.

![Uppdaterad ASP.NET-webbapp i Azure](./media/app-service-web-get-started-dotnet/updated-azure-web-app.png)

## Hantera Azure-webbappen
<a id="manage-the-azure-web-app" class="xliff"></a>

Gå till <a href="https://portal.azure.com" target="_blank">Azure Portal</a> för att hantera webbappen.

Klicka på **Apptjänster** på menyn till vänster och välj sedan namnet på din Azure-webbapp.

![Navigera till webbappen på Azure Portal](./media/app-service-web-get-started-dotnet/access-portal.png)

Nu visas sidan Översikt för din webbapp. Här kan du utföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort. 

![App Service-blad på Azure Portal](./media/app-service-web-get-started-dotnet/web-app-blade.png)

Menyn till vänster innehåller olika sidor för att konfigurera appen. 

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## Nästa steg
<a id="next-steps" class="xliff"></a>

> [!div class="nextstepaction"]
> [ASP.NET med SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)

