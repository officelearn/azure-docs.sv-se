---
title: Autentisera och auktorisera användare från slutpunkt till slutpunkt i Azure App Service | Microsoft Docs
description: Lär dig hur du använder App Service-autentisering och auktorisering och skyddar App Service-appar, inklusive åtkomst till fjärr-API:er.
keywords: app service, azure app service, authN, authZ, säker, säkerhet, flera nivåer, azure active directory, azure ad
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/03/2018
ms.author: cephalin
ms.openlocfilehash: 4bdb182d93b842bf94e75672b1d7b4cf4f6da253
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="tutorial-authenticate-and-authorize-users-end-to-end-in-azure-app-service"></a>Självstudie: Autentisera och auktorisera användare från slutpunkt till slutpunkt i Azure App Service

Med [Azure App Service](app-service-web-overview.md) får du en automatiskt uppdaterad webbvärdtjänst med hög skalbarhet. I App Service finns dessutom funktioner för [användarautentisering och auktorisering](app-service-authentication-overview.md). I den här kursen visar vi hur du skyddar appar med App Service-autentisering och auktorisering. Vi använder en ASP.NET Core-app med Angular.js-klientdel, men det är bara ett exempel. App Service-autentisering och autentisering stöder alla språkkörningar. Du kan lära dig hur du använder det med det språk du föredrar genom att följa självstudiekursen.

I självstudiekursen använder vi exempelappen och visar hur du skyddar en självständig app (i [Aktivera autentisering och auktorisering för serverdelsapp](#enable-authentication-and-authorization-for-back-end-app)).

![Enkel autentisering och auktorisering](./media/app-service-web-tutorial-auth-aad/simple-auth.png)

Här ser du också hur du skyddar en app med flera nivåer genom att öppna ett skyddat server-API åt den autentiserade användaren, från både [serverkoden](#call-api-securely-from-server-code) och [webbläsarkoden](#call-api-securely-from-browser-code).

![Avancerad autentisering och auktorisering](./media/app-service-web-tutorial-auth-aad/advanced-auth.png)

Det här är endast några av de möjliga scenarierna för autentisering och auktorisering i App Service. 

Här är en mer utförlig lista över saker du lär dig under självstudiekursen:

> [!div class="checklist"]
> * Aktivera inbyggd autentisering och auktorisering
> * Skydda appar mot oautentiserade begäranden
> * Använda Azure Active Directory som identitetsleverantör
> * Få åtkomst till en fjärrapp å den inloggade användarens vägnar
> * Säkra tjänst-till-tjänst-anrop med tokenautentisering
> * Använda åtkomsttoken från serverkod
> * Använd åtkomsttoken från klientkod (webbläsare)

Du kan följa stegen i den här självstudien i macOS, Linux och Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här självstudien behöver du:

* [Installera Git](https://git-scm.com/).
* [Installera .NET Core](https://www.microsoft.com/net/core/).

## <a name="create-local-net-core-app"></a>Skapa en lokal .NET Core-app

I det här steget konfigurerar du det lokala .NET Core-projektet. Du kan använda samma projekt för att distribuera en API-app på serversidan och en webbapp på klientsidan.

### <a name="clone-and-run-the-sample-application"></a>Klona och kör exempelprogrammet

Kör följande kommandon för att klona exempellagringsplatsen och köra den.

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
cd dotnet-core-api
dotnet run
```

Öppna `http://localhost:5000` och prova att lägga till, redigera och ta bort att göra-objekt. 

![ASP.NET Core-API som körs lokalt](./media/app-service-web-tutorial-auth-aad/local-run.png)

Du kan när som helst stoppa ASP.NET Core genom att trycka på `Ctrl+C` i terminalen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-apps-to-azure"></a>Distribuera appar till Azure

I det här steget distribuerar du projektet till två App Service-appar. Den ena är en klientdelsapp och den andra är en serverdelsapp.

### <a name="create-azure-resources"></a>Skapa Azure-resurser

Kör följande kommandon i Cloud Shell för att skapa två webbappar. Ersätt _&lt;front\_end\_app\_name>_ (namnet på klientdelsappen) och _&lt;back\_end\_app\_name>_ (namnet på serverdelsappen) med två globalt unika appnamn (giltiga tecken är `a-z`, `0-9` och `-`). Mer information om varje kommando finns i [RESTful-API med CORS i Azure App Service](app-service-web-tutorial-rest-api.md).

```azurecli-interactive
az group create --name myAuthResourceGroup --location "West Europe"
az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku FREE
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <front_end_app_name> --deployment-local-git --query deploymentLocalGitUrl
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <back_end_app_name> --deployment-local-git --query deploymentLocalGitUrl
```

> [!NOTE]
> Spara webbadresserna till Git-fjärrobjekten för klientdels- och serverdelsapparna. De visas i utdata från `az webapp create`.
>

### <a name="push-to-azure-from-git"></a>Skicka till Azure från Git

Åter i det _lokala terminalfönstret_ kör du följande Git-kommandon för att distribuera till serverdelsappen. Ersätt _&lt;deploymentLocalGitUrl-of-back-end-app>_ med webbadressen för den fjärranslutna Git som du sparade från [Skapa Azure-resurser](#create-azure-resources). När du blir ombedd av Git Credential Manager att ange autentiseringsuppgifter ska du se till att du anger [dina distributionsuppgifter](app-service-deployment-credentials.md) och inte de uppgifter som du använder till att logga in på Azure-portalen.

```bash
git remote add backend <deploymentLocalGitUrl-of-back-end-app>
git push backend master
```

Kör följande Git-kommandon i det lokala terminalfönstret för att distribuera samma kod till klientdelsappen. Ersätt _&lt;deploymentLocalGitUrl-of-front-end-app>_ med webbadressen för den fjärranslutna Git som du sparade från [Skapa Azure-resurser](#create-azure-resources).

```bash
git remote add frontend <deploymentLocalGitUrl-of-front-end-app>
git push frontend master
```

### <a name="browse-to-the-azure-web-apps"></a>Bläddra till Azure-webbapparna

Navigera till följande URL:er i en webbläsare och se hur de två apparna fungerar.

```
http://<back_end_app_name>.azurewebsites.net
http://<front_end_app_name>.azurewebsites.net
```

![ASP.NET Core-API som körs i Azure App Service](./media/app-service-web-tutorial-auth-aad/azure-run.png)

> [!NOTE]
> Om appen startar om noterade du kanske att nya data har tagits bort. Det här är avsiktligt eftersom ASP.NET Core-exempelappen använder en minnesintern databas.
>
>

## <a name="call-back-end-api-from-front-end"></a>Anropa serverdels-API från klientsidan

I det här steget använder du serverkoden för klientdelsappen för att komma åt serverdels-API:t. Du aktiverar senare autentiserad åtkomst från klientdelen till serverdelen.

### <a name="modify-front-end-code"></a>Ändra klientdelskod

Öppna _Controllers/TodoController.cs_ på den lokala lagringsplatsen. I början av klassen `TodoController` lägger du till följande rader och ersätter _&lt;back\_end\_app\_name>_ med namnet på din serverdelsapp:

```cs
private static readonly HttpClient _client = new HttpClient();
private static readonly string _remoteUrl = "https://<back_end_app_name>.azurewebsites.net";
```

Hitta metoden `GetAll()` och byt ut koden inom klamrarna mot:

```cs
var data = _client.GetStringAsync($"{_remoteUrl}/api/Todo").Result;
return JsonConvert.DeserializeObject<List<TodoItem>>(data);
```

Den första raden gör ett `GET /api/Todo`-anrop till serverdels-API-appen.

Leta sedan reda på metoden `GetById(long id)` och byt ut koden i klamrarna mot:

```cs
var data = _client.GetStringAsync($"{_remoteUrl}/api/Todo/{id}").Result;
return Content(data, "application/json");
```

Den första raden gör ett `GET /api/Todo/{id}`-anrop till serverdels-API-appen.

Leta sedan reda på metoden `Create([FromBody] TodoItem item)` och byt ut koden i klamrarna mot:

```cs
var response = _client.PostAsJsonAsync($"{_remoteUrl}/api/Todo", item).Result;
var data = response.Content.ReadAsStringAsync().Result;
return Content(data, "application/json");
```

Den första raden gör ett `POST /api/Todo`-anrop till serverdels-API-appen.

Leta sedan reda på metoden `Update(long id, [FromBody] TodoItem item)` och byt ut koden i klamrarna mot:

```cs
var res = _client.PutAsJsonAsync($"{_remoteUrl}/api/Todo/{id}", item).Result;
return new NoContentResult();
```

Den första raden gör ett `PUT /api/Todo/{id}`-anrop till serverdels-API-appen.

Leta sedan reda på metoden `Delete(long id)` och byt ut koden i klamrarna mot:

```cs
var res = _client.DeleteAsync($"{_remoteUrl}/api/Todo/{id}").Result;
return new NoContentResult();
```

Den första raden gör ett `DELETE /api/Todo/{id}`-anrop till serverdels-API-appen.

Spara alla ändringar. I det lokala terminalfönstret distribuerar du ändringarna till klientdelsappen med följande Git-kommandon:

```bash
git add .
git commit -m "call back-end API"
git push frontend master
```

### <a name="check-your-changes"></a>Kontrollera ändringarna

Öppna `http://<front_end_app_name>.azurewebsites.net` och lägg till några objekt, exempelvis `from front end 1` och `from front end 2`.

Navigera till `http://<back_end_app_name>.azurewebsites.net` för att visa de objekt som lades till från klientdelsappen. Lägg även till några objekt, som `from back end 1` och `from back end 2`, och uppdatera sedan klientdelsappen för att se om ändringarna visas.

![ASP.NET Core-API som körs i Azure App Service](./media/app-service-web-tutorial-auth-aad/remote-api-call-run.png)

## <a name="configure-auth"></a>Konfigurera autentisering

I det här steget aktiverar du autentisering och auktorisering för två appar. Du kan också konfigurera klientdelsappen och generera en åtkomsttoken som du kan använda till att göra autentiserade anrop till serverdelsappen.

Du använder Azure Active Directory som identitetsleverantör. Mer information finns i [Konfigurera Azure Active Directory-autentisering för App Services-appen](app-service-mobile-how-to-configure-active-directory-authentication.md).

### <a name="enable-authentication-and-authorization-for-back-end-app"></a>Aktivera autentisering och auktorisering för serverdelsapp

I [Azure Portal](https://portal.azure.com) öppnar du hanteringssidan för serverdelsappen genom att klicka via vänster meny: **Resource groups** > **myAuthResourceGroup** > _\<back\_end\_app\_name>_ (resursgrupper, min autentiseringsresursgrupp, namn på serverdelsapp).

![ASP.NET Core-API som körs i Azure App Service](./media/app-service-web-tutorial-auth-aad/portal-navigate-back-end.png)

I vänstermenyn för serverdelsappen klickar du på **Authentication / Authorization** (Autentisering/Auktorisering) och aktiverar App Service-autentisering genom att klicka på **On** (På).

För **Åtgärd att vidta när en begäran inte har autentiserats** väljer du **Logga in med Azure Active Directory**.

Under **Autentiseringsprovidrar** och klicka på **Azure Active Directory** 

![ASP.NET Core-API som körs i Azure App Service](./media/app-service-web-tutorial-auth-aad/configure-auth-back-end.png)

Klicka på **Express** och acceptera standardinställningarna för att skapa en ny AD-app och klicka på **OK**.

På sidan **Autentisering/auktorisering** klickar du på **Spara**. 

När du ser aviseringen med meddelandet `Successfully saved the Auth Settings for <back_end_app_name> App` (Autentiseringsinställningarna för serverdelsappen sparades) uppdaterar du sidan.

Klicka på **Azure Active Directory** igen och klicka sedan på **Hantera program**.

På hanteringssidan för AD-programmet kopierar du **program-ID** till Anteckningar. Du behöver det här värdet senare.

![ASP.NET Core-API som körs i Azure App Service](./media/app-service-web-tutorial-auth-aad/get-application-id-back-end.png)

### <a name="enable-authentication-and-authorization-for-front-end-app"></a>Aktivera autentisering och auktorisering för klientappen

Följ samma steg för klientdelsappen, men hoppa över det sista steget. Du behöver inte **program-ID:t** för klientdelsappen. Håll sidan **Azure Active Directory-inställningar** öppen.

Om du vill kan du navigera till `http://<front_end_app_name>.azurewebsites.net`. Du bör nu dirigeras till en inloggningssida. När du har loggat in kan du fortfarande inte komma åt data från serverdelsappen. Du behöver fortfarande göra tre saker:

- Ge klientsidan åtkomst till serversidan
- Konfigurera App Service för att returnera en användbar token
- Använda token i koden

> [!TIP]
> Om du stöter på fel och konfigurerar om inställningarna för autentisering/auktorisering för appen kan det hända att tokenenheterna i tokenarkivet inte skapas om från de nya inställningarna. För att se till att dina token skapas måste du logga ut och sedan logga in i appen igen. Ett enkelt sätt att göra det är att använda webbläsaren i privat läge. Stäng webbläsaren och öppna den sedan i privat läge igen när du har ändrat inställningarna i dina appar.

### <a name="grant-front-end-app-access-to-back-end"></a>Ge klientdelsappen åtkomst till serversidan

Nu när du har aktiverat autentisering och auktorisering för båda dina appar backas var och en av dem upp av ett AD-program. I det här steget kan ge du klientdelsappen åtkomstbehörighet till serverdelsappen å användarens vägnar. (Tekniskt sett ger du _AD-programmet_ för klientdelsappen behörighet att komma åt _AD-programmet_ för serverdelen å användarens vägnar.)

Nu bör du vara på sidan med **inställningar för Azure Active Directory** för klientdelsappen. Om inte navigerar du tillbaka till den sidan. 

Klicka på **Hantera behörigheter** > **Lägg till** > **Välj ett API**.

![ASP.NET Core-API som körs i Azure App Service](./media/app-service-web-tutorial-auth-aad/add-api-access-front-end.png)

På sidan **Välj ett API** anger du AD-programnamnet på serverdelsappen. Standardinställningen är att det är detsamma som namnet på serverdelsappen. Välj det i listan och klicka på **Välj**.

Markera kryssrutan intill **Åtkomst _&lt;AD\_application\_name>_** (AD-programnamn). Klicka på **Välj** > **Klar**.

![ASP.NET Core-API som körs i Azure App Service](./media/app-service-web-tutorial-auth-aad/select-permission-front-end.png)

### <a name="configure-app-service-to-return-a-usable-access-token"></a>Konfigurera App Service för att returnera en användbar åtkomsttoken

Klientdelsappen har nu de behörigheter som krävs. I det här steget konfigurerar du autentisering och auktorisering för App Service, så att du får en användbar åtkomsttoken för att komma åt serverdelen. För det här steget behöver du program-ID:t för serverdelsappen, som du kopierade från [Aktivera autentisering och auktorisering för serverdelsapp](#enable-authentication-and-authorization-for-back-end-app).

Logga in på [Azure Resource Explorer](https://resources.azure.com). Överst på sidan klickar du på **Läs/skriv** för att aktivera redigeringen av Azure-resurserna.

![ASP.NET Core-API som körs i Azure App Service](./media/app-service-web-tutorial-auth-aad/resources-enable-write.png)

Till vänster i webbläsaren klickar du på **subscriptions** > **_&lt;your\_subscription>_** > **resourceGroups** > **myAuthResourceGroup** > **providers** > **Microsoft.Web** > **sites** > **_\<front\_end\_app\_name>_** > **config** > **authsettings** (prenumerationer, <din_prenumeration>, resursgrupper > min autentiseringsresursgrupp > leverantörer > microsoft web > webbplatser >, <namnet på klientdelsappen>, konfiguration, autentiseringsinställningar).

I vyn **authsettings** (autentiseringsinställningar) klickar du på **Edit** (Redigera). Ställ in `additionalLoginParams` till följande JSON-sträng med det program-ID som du kopierade. 

```json
"additionalLoginParams": ["response_type=code id_token","resource=<back_end_application_id>"],
```

![ASP.NET Core-API som körs i Azure App Service](./media/app-service-web-tutorial-auth-aad/additional-login-params-front-end.png)

Spara inställningarna genom att klicka på **PUT** (Placera).

Nu har apparna konfigurerats. Klientdelen är nu redo att komma åt serverdelen med en åtkomsttoken.

Information om hur du konfigurerar det här för andra leverantörer finns i informationen om att [uppdatera åtkomsttoken](app-service-authentication-how-to.md#refresh-access-tokens).

## <a name="call-api-securely-from-server-code"></a>Anropa API på säkert sätt från serverkod

I det här steget aktiverar du din tidigare ändrade serverkod så att du kan göra autentiserade anrop till serverdels-API:et.

Klientdelsappen har nu den behörighet som krävs och lägger också till serverdelens program-ID till inloggningsparametrarna. Den kan därför få en åtkomsttoken för autentisering utan serverdelsappen. App Service tillhandahåller denna token i serverkoden genom att införa en `X-MS-TOKEN-AAD-ACCESS-TOKEN`-rubrik för varje autentiserad begäran (se [Hämta token i appkod](app-service-authentication-how-to.md#retrieve-tokens-in-app-code)).

> [!NOTE]
> Dessa rubriker införs för alla språk som stöds. Du har åtkomst till dem med hjälp av standardmönstret för respektive språk.

Öppna _Controllers/TodoController.cs_ igen i den lokala databasen. Under konstruktorn `TodoController(TodoContext context)` lägger du till följande kod:

```cs
public override void OnActionExecuting(ActionExecutingContext context)
{
    base.OnActionExecuting(context);

    _client.DefaultRequestHeaders.Accept.Clear();
    _client.DefaultRequestHeaders.Authorization =
        new AuthenticationHeaderValue("Bearer", Request.Headers["X-MS-TOKEN-AAD-ACCESS-TOKEN"]);
}
```

Den här koden lägger till den vanliga HTTP-rubriken`Authorization: Bearer <access_token>` till alla fjärranslutna API-anrop. I körningspipelinen för begäran för ASP.NET Core MVC körs `OnActionExecuting` precis före respektive åtgärdsmetod (exempelvis `GetAll()`) (hämta allt) så att varje utgående API-anrop nu presenterar åtkomsttoken.

Spara alla ändringar. I det lokala terminalfönstret distribuerar du ändringarna till klientdelsappen med följande Git-kommandon:

```bash
git add .
git commit -m "add authorization header for server code"
git push frontend master
```

Logga in på `http://<front_end_app_name>.azurewebsites.net` igen. På sidan med dataanvändningsavtalet klickar du på **acceptera**.

Du bör nu kunna skapa, läsa, uppdatera och ta bort data från serverdelsappen, precis som innan. Den enda skillnaden är att båda apparna är nu skyddade med App Service-autentisering och -auktorisering, inklusive tjänst-till-tjänst-anrop.

Grattis! Serverkoden har nu åtkomst till serverdelsdata å den autentiserade användarens vägnar.

## <a name="call-api-securely-from-browser-code"></a>Anropa API på ett säkert sätt från webbläsarkoden

I det här steget pekar du Angular.js-appen för klientdelen till API:et för serverdelen. På så sätt får du veta hur du kan hämta åtkomsttoken och göra API-anrop till serverdelsappen med den.

Medan serverkoden har åtkomst till begäranderubriker kan klientkoden komma åt `GET /.auth/me` för att få samma åtkomsttoken (se [Hämta token i appkod](app-service-authentication-how-to.md#retrieve-tokens-in-app-code)).

> [!TIP]
> Det här avsnittet använder vi standard-HTTP-metoder för att demonstrera det säkra HTTP-anropet. Du kan dock använda [Active Directory Authentication Library (ADAL) för JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js) för att underlätta Angular.js-tillämpningsmönstret.
>

### <a name="configure-cors"></a>Konfigurera CORS

I Cloud Shell aktiverar du CORS till din klient-URL med kommandot [`az resource update`](/cli/azure/resource#az_resource_update). Ersätt platshållarna _\<back\_end\_app\_name>_ och _\<front\_end\_app\_name>_ (namn på serverdelsapp och namn på klientdelsapp).

```azurecli-interactive
az resource update --name web --resource-group myAuthResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<back_end_app_name> --set properties.cors.allowedOrigins="['https://<front_end_app_name>.azurewebsites.net']" --api-version 2015-06-01
```

Det här steget är inte kopplat till autentisering och auktorisering. Du behöver det dock för att webbläsaren ska tillåta API-anrop från flera domäner från Angular.js-appen. Mer information finns i [Lägga till CORS-funktioner](app-service-web-tutorial-rest-api.md#add-cors-functionality).

### <a name="point-angularjs-app-to-back-end-api"></a>Peka Angular.js-app till serverdels-API

Öppna _wwwroot/index.html_ på den lokala lagringsplatsen.

På rad 51 ställer du in `apiEndpoint`-variabeln till URL:en för serverdelsappen (`http://<back_end_app_name>.azurewebsites.net`). Ersätt _\<back\_end\_app\_name> (namnet på serverdelsappen)_ med ditt appnamn i App Service.

På den lokala lagringsplatsen öppnar du _wwwroot/app/scripts/todoListSvc.js_ och ser till att `apiEndpoint` är tillagt för alla API-anrop. Nu anropar Angular.js-appen serverdels-API:erna. 

### <a name="add-access-token-to-api-calls"></a>Lägga till åtkomsttoken till API-anrop

Öppna _wwwroot/app/scripts/todoListSvc.js_. Ovanför listan med API-anrop (ovanför raden `getItems : function(){`), lägger du till följande funktion i listan:

```javascript
setAuth: function (token) {
    $http.defaults.headers.common['Authorization'] = 'Bearer ' + token;
},
```

Den här funktionen anropas för att ange standardrubriken `Authorization` med åtkomsttoken. Du anropar den i nästa steg.

På den lokala lagringsplatsen öppnar du _wwwroot/app/scripts/app.js_ och hittar följande kod:

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
}).otherwise({ redirectTo: "/Home" });
```

Ersätt hela kodblocket med följande kod:

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
    resolve: {
        token: ['$http', 'todoListSvc', function ($http, todoListSvc) {
            return $http.get('/.auth/me').then(function (response) {
                todoListSvc.setAuth(response.data[0].access_token);
                return response.data[0].access_token;
            });
        }]
    },
}).otherwise({ redirectTo: "/Home" });
```

Den nya ändringen lägger till `revolve` mappningen som anropar`/.auth/me` och anger åtkomsttoken. Det innebär att du med säkerhet har åtkomsttoken innan kontrollanten `todoListCtrl` initieras. På så sätt omfattar alla API-anrop av kontrollanten token.

### <a name="deploy-updates-and-test"></a>Distribuera uppdateringar och testa

Spara alla ändringar. I det lokala terminalfönstret distribuerar du ändringarna till klientdelsappen med följande Git-kommandon:

```bash
git add .
git commit -m "add authorization header for Angular"
git push frontend master
```

Navigera till `http://<front_end_app_name>.azurewebsites.net` igen. Du bör nu kunna skapa, läsa, uppdatera och ta bort data från serverdelsappen, direkt i Angular.js-appen.

Grattis! Klientkoden har nu åtkomst till serverdelsdata å den autentiserade användarens vägnar.

## <a name="clean-up-resources"></a>Rensa resurser

I de föregående stegen skapade du Azure-resurser i en resursgrupp. Om du inte tror att du behöver dessa resurser i framtiden tar du bort resursgruppen genom att köra följande kommando i Cloud Shell:

```azurecli-interactive
az group delete --name myAuthResourceGroup
```

Det kan några minuter att köra kommandot.

<a name="next"></a>
## <a name="next-steps"></a>Nästa steg

Vad du lärt dig:

> [!div class="checklist"]
> * Aktivera inbyggd autentisering och auktorisering
> * Skydda appar mot oautentiserade begäranden
> * Använda Azure Active Directory som identitetsleverantör
> * Få åtkomst till en fjärrapp å den inloggade användarens vägnar
> * Säkra tjänst-till-tjänst-anrop med tokenautentisering
> * Använda åtkomsttoken från serverkod
> * Använd åtkomsttoken från klientkod (webbläsare)

Gå vidare till nästa självstudie där du får lära dig att mappa ett anpassat DNS-namn till webbappen.

> [!div class="nextstepaction"]
> [Mappa ett befintligt anpassat DNS-namn till Azure Web Apps](app-service-web-tutorial-custom-domain.md)
