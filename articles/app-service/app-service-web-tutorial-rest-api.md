---
title: RESTful-API med CORS i Azure App Service | Microsoft Docs
description: Lär dig hur Azure App Service hjälper dig att vara värd för dina RESTful-API:er med CORS-stöd.
services: app-service\api
documentationcenter: dotnet
author: cephalin
manager: cfowler
editor: ''
ms.assetid: a820e400-06af-4852-8627-12b3db4a8e70
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 02/28/2018
ms.author: cephalin
ms.custom: mvc, devcenter
ms.openlocfilehash: 4b5e432a9c553f5221f138b19a180f92420e6058
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
ms.locfileid: "30902317"
---
# <a name="tutorial-host-a-restful-api-with-cors-in-azure-app-service"></a>Självstudie: Vara värd för en RESTful-API med CORS i Azure App Service

Med [Azure App Service](app-service-web-overview.md) får du en automatiskt uppdaterad webbvärdtjänst med hög skalbarhet. Dessutom har App Service ett inbyggt stöd för [CORS (Cross-Origin Resource Sharing)](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing) för RESTful-API:er. Den här självstudien visar hur du distribuerar en ASP.NET Core API-app till App Service med CORS-stöd. Du konfigurerar appen med hjälp av kommandoradsverktyg och distribuerar appen med Git. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa App Service-resurser med Azure CLI
> * Distribuera en RESTful-API till Azure med Git
> * Aktivera stöd för CORS i App Service

Du kan följa stegen i den här självstudien i macOS, Linux och Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här självstudien behöver du:

* [Installera Git](https://git-scm.com/).
* [Installera .NET Core](https://www.microsoft.com/net/core/).

## <a name="create-local-aspnet-core-app"></a>Skapa en lokal ASP.NET Core-app

I det här steget konfigurerar du det lokala ASP.NET Core-projektet. App Service stöder samma arbetsflöde för API:er som skrivits på andra datorspråk.

### <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Använd kommandot `cd` för att komma till en arbetskatalog i terminalfönstret.  

Klona exempellagringsplatsen med följande kommando. 

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
```

Den här lagringsplatsen innehåller ett program som baseras på följande självstudie: [Hjälpsidor för ASP.NET Cores webb-API med Swagger](/aspnet/core/tutorials/web-api-help-pages-using-swagger?tabs=visual-studio). En Swagger-generator används för att hantera [Swagger-användargränssnittet](https://swagger.io/swagger-ui/) och Swagger JSON-slutpunkten.

### <a name="run-the-application"></a>Köra programmet

Kör följande kommandon för att installera de nödvändiga paketen, köra databasmigreringar och starta programmet.

```bash
cd dotnet-core-api
dotnet restore
dotnet run
```

Gå till `http://localhost:5000/swagger` i en webbläsare för att testa användargränssnittet för Swagger.

![ASP.NET Core-API som körs lokalt](./media/app-service-web-tutorial-rest-api/local-run.png)

Gå till `http://localhost:5000/api/todo` och se en lista med ToDo JSON-objekt.

Gå till `http://localhost:5000` och testa med webbläsarappen. Senare kommer du att peka med webbläsarappen på en fjärr-API i App Service för att testa CORS-funktionerna. Koden för webbläsarappen finns i lagringsplatsens katalog _wwwroot_.

Du kan när som helst stoppa ASP.NET Core genom att trycka på `Ctrl+C` i terminalen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Distribuera appen till Azure

I det här steget distribuerar du din SQL Database-anslutna .NET Core-app till App Service.

### <a name="configure-local-git-deployment"></a>Konfigurera lokal git-distribution

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>Skapa en webbapp

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)] 

### <a name="push-to-azure-from-git"></a>Skicka till Azure från Git

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 98, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (92/92), done.
Writing objects: 100% (98/98), 524.98 KiB | 5.58 MiB/s, done.
Total 98 (delta 8), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: .
remote: Updating submodules.
remote: Preparing deployment for commit id '0c497633b8'.
remote: Generating deployment script.
remote: Project file path: ./DotNetCoreSqlDb.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-azure-web-app"></a>Bläddra till Azure-webbappen

Gå till `http://<app_name>.azurewebsites.net/swagger` i en webbläsare och testa användargränssnittet för Swagger.

![ASP.NET Core-API som körs i Azure App Service](./media/app-service-web-tutorial-rest-api/azure-run.png)

Gå till `http://<app_name>.azurewebsites.net/swagger/v1/swagger.json` för att se _swagger.json_ för din distribuerade API.

Gå till `http://<app_name>.azurewebsites.net/api/todo` för att se om din distribuerade API fungerar.

## <a name="add-cors-functionality"></a>Lägga till CORS-funktioner

Därefter aktiverar du det inbyggda CORS-stödet i App Service för din API.

### <a name="test-cors-in-sample-app"></a>Testa CORS i exempelappen

Öppna _wwwroot/index.html_ på din lokala lagringsplats.

På rad 51 anger du `apiEndpoint`-variabeln till URL:en för din distribuerade API (`http://<app_name>.azurewebsites.net`). Ersätt _\<appname >_ med ditt appnamn i App Service.

Kör exempelappen igen i ditt lokala terminalfönster.

```bash
dotnet run
```

Gå till webbläsarappen på `http://localhost:5000`. Öppna fönstret med utvecklingsverktyg i webbläsaren (`Ctrl`+`Shift`+`i` i Chrome för Windows) och kontrollera fliken **Konsol**. Du bör nu se felmeddelandet `No 'Access-Control-Allow-Origin' header is present on the requested resource`.

![CORS-fel i webbläsarklienten](./media/app-service-web-tutorial-rest-api/cors-error.png)

På grund av domänmatchningsfel mellan webbläsarappen (`http://localhost:5000`) och fjärresursen (`http://<app_name>.azurewebsites.net`), samt det faktum att din API i App Service inte skickar `Access-Control-Allow-Origin`-huvudet, har din webbläsare förhindrat att innehåll från flera domäner blir inlästa i din webbläsarapp.

Webbläsarappen bör ha en offentlig URL i stället för en localhost-URL i produktionsmiljö, men sättet att aktivera CORS till en localhost-URL är detsamma som en offentlig URL.

### <a name="enable-cors"></a>Aktivera CORS 

I Cloud Shell aktiverar du CORS till din klient-URL med kommandot [`az resource update`](/cli/azure/resource#az_resource_update). Ersätt platshållaren _&lt;appname>_.

```azurecli-interactive
az resource update --name web --resource-group myResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<app_name> --set properties.cors.allowedOrigins="['http://localhost:5000']" --api-version 2015-06-01
```

Du kan ange fler än en klient-URL i `properties.cors.allowedOrigins` (`"['URL1','URL2',...]"`). Du kan också aktivera alla klient-URL:er med `"['*']"`.

### <a name="test-cors-again"></a>Testa CORS igen

Uppdatera webbläsarappen i `http://localhost:5000`. Felmeddelandet i fönstret **Konsol** är nu borta och du kan se data från den distribuerade API:n samt interagera med den. Fjärr-API:n har nu stöd för CORS i webbläsarappen som körs lokalt. 

![CORS finns nu i webbläsarklienten](./media/app-service-web-tutorial-rest-api/cors-success.png)

Grattis! Du kör en API i Azure App Service med CORS-stöd.

## <a name="app-service-cors-vs-your-cors"></a>App Services CORS jämfört med din CORS

Du kan använda dina egna CORS-verktyg i stället för App Services CORS om du vill ha mer flexibilitet. Du kanske vill ange olika tillåtna ursprung för olika vägar eller metoder. Eftersom du med App Services CORS kan ange en uppsättning godkända ursprung för alla API-vägar och metoder, kan du använda din egna CORS-kod. Se hur ASP.NET Core gör detta i [Aktivera CORS (Cross-Origin Requests)](/aspnet/core/security/cors).

> [!NOTE]
> Försök inte att använda App Services CORS och din egen CORS-kod tillsammans. När de används tillsammans har App Services CORS företräde och din egen CORS-kod fyller ingen funktion.
>
>

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Nästa steg

Vad du lärt dig:

> [!div class="checklist"]
> * Skapa App Service-resurser med Azure CLI
> * Distribuera en RESTful-API till Azure med Git
> * Aktivera stöd för CORS i App Service

Gå vidare till nästa självstudie för att lära dig att autentisera och auktorisera användare.

> [!div class="nextstepaction"]
> [Självstudie: Autentisera och auktorisera användare från slutpunkt till slutpunkt](app-service-web-tutorial-auth-aad.md)
