<properties
    pageTitle="Stöd för CORS i Apptjänst | Microsoft Azure"
    description="Lär dig hur du använder CORS-stöd i Azure Apptjänst."
    services="app-service\api"
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-api"
    ms.workload="na"
    ms.tgt_pltfrm="dotnet"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="03/31/2016"
    ms.author="tdykstra"/>

# Använda en API-app från JavaScript med CORS

Apptjänst har inbyggt stöd för [Cross Origin Resource Sharing (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing), vilket gör att JavaScript-klienterna mellan domäner kan anropa API:er som finns i API Apps. Med Apptjänst kan du konfigurera CORS-åtkomst till ditt API utan att skriva någon kod i ditt API.

Den här artikeln innehåller två avsnitt:

* Avsnittet [Hur du konfigurerar CORS](#corsconfig) förklarar i allmänhet hur du konfigurerar CORS för API Apps, webbappar eller mobilappar. Det gäller även för alla ramverk som stöds av Apptjänsten, inklusive .NET, Node.js och Java. 

* Från och med avsnittet [Fortsättning på självstudierna för att komma igång med .NET](#tutorialstart) är artikeln en genomgång som visar CORS-stöd genom att bygga vidare på vad du gjorde i [de första självstudierna för att komma igång med API Apps](app-service-api-dotnet-get-started.md). 

## <a id="corsconfig"></a> Hur du konfigurerar CORS i Azure Apptjänst

Du kan konfigurera CORS i Azure-portalen eller genom att använda verktygen i  [Azure Resource Manager](../resource-group-overview.md).

#### Konfigurera CORS i Azure-portalen

8. I en webbläsare går du till [Azure-portalen](https://portal.azure.com/).

2. Klicka på **Apptjänster** och klicka sedan på namnet på din API-app.

    ![Välj API-app i portalen](./media/app-service-api-cors-consume-javascript/browseapiapps.png)

10. I bladet **Inställningar** som öppnas till höger om bladet **API-app** letar du rätt på avsnittet **API** och klickar sedan på **CORS**.

    ![Välj CORS i bladet Inställningar](./media/app-service-api-cors-consume-javascript/clicksettings.png)

11. I textrutan anger du den URL eller de URL:er som du vill tillåta JavaScript-anrop från.


    Om du till exempel har distribuerat JavaScript-programmet till en webbapp med namnet todolistangular anger du "https://todolistangular.azurewebsites.net". Alternativt kan ange du en asterisk (*) om du vill ange att alla ursprungsdomäner accepteras.


13. Klicka på **Spara**.

    ![Klicka på Spara](./media/app-service-api-cors-consume-javascript/corsinportal.png)

    När du klickar på **Spara** kommer API-appen att acceptera JavaScript-anrop från angivna URL:er.

#### Konfigurera CORS med hjälp av Azure Resource Manager-verktyg

Du kan även konfigurera CORS för en API-app genom att använda [mallarna i Azure Resource Manager](../resource-group-authoring-templates.md) i kommandoradsverktyg som [Azure PowerShell](../powershell-install-configure.md) och [Azure CLI](../xplat-cli-install.md). 

Se ett exempel på en mall i Azure Resource Manager som anger CORS-egenskapen genom att öppna filen [azuredeploy.json i lagringsplatsen för den här kursens exempelprogram](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/azuredeploy.json). Leta reda på det avsnitt i mallen som ser ut som följande exempel:

        "cors": {
            "allowedOrigins": [
                "todolistangular.azurewebsites.net"
            ]
        }

## <a id="tutorialstart"></a> Fortsättning på självstudierna för att komma igång med .NET

Om du följer serien för att komma igång med Node.js eller Java för API Apps har du slutfört komma igång-serien. Gå vidare till avsnittet [Nästa steg](#next-steps) för att få förslag på ytterligare utbildning om API Apps.

Resten av den här artikeln är en förlängning av serien för att komma igång med .NET och förutsätter att du har slutfört de [första självstudierna](app-service-api-dotnet-get-started.md).

## Distribuera ToDoListAngular-projektet till en ny webbapp

I de [första självstudierna](app-service-api-dotnet-get-started.md) skapade du en API-app på mellannivå och en på datanivå. I de här självstudierna skapar du en SPA-webbapp (Single-page Application) som anropar API-appen på mellannivån. För att SPA:n ska fungera måste du aktivera CORS i API-appen på mellannivå. 

I [exempelappen ToDoList](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list) är ToDoListAngular-projektet en enkel AngularJS-klient som anropar API:et för mellannivåprojektet ToDoListAPI Web API. JavaScript-koden i filen *app/scripts/todoListSvc.js* anropar API:et med AngularJS HTTP-leverantören. 

        angular.module('todoApp')
        .factory('todoListSvc', ['$http', function ($http) {
            var apiEndpoint = "http://localhost:46439";
        
            $http.defaults.useXDomain = true;
            delete $http.defaults.headers.common['X-Requested-With']; 
        
            return {
                getItems : function(){
                    return $http.get(apiEndpoint + '/api/TodoList');
                },

                /* Get by ID, Put, and Delete methods not shown */

                postItem : function(item){
                    return $http.post(apiEndpoint + '/api/TodoList', item);
                }
            };
        }]);

### Skapa en ny webbapp för ToDoListAngular-projektet

Proceduren för att skapa en ny Apptjänst-webbapp och distribuera ett projekt liknar den du såg för [att skapa och distribuera en API-app i den första kursen i den här serien](app-service-api-dotnet-get-started.md#createapiapp). Den enda skillnaden är att apptypen är **webbapp** i stället för **API-app**.  Skärmdumpar av dialogrutorna finns i 

1. I **Solution Explorer** högerklickar du på ToDoListAngular-projektet och sedan på **Publicera**.

3.  På fliken **Profil** i guiden **Publicera webbplats** klickar du på **Microsoft Azure Apptjänst**.

5. I dialogrutan **Apptjänst** klickar du på **Ny**.

3. På fliken **Värdskap** i dialogrutan **Skapa Apptjänst** anger du **Webbprogrammets namn** som är unikt i domänen *azurewebsites.net*. 

5. Välj den Azure-**prenumeration** du vill arbeta med.

6. I listrutan **Resursgrupp** väljer du samma resursgrupp som du skapade tidigare.

4. I listrutan **Apptjänstplan** väljer du samma plan som du skapade tidigare. 

7. Klicka på **Skapa**.

    Visual Studio skapar webbappen, en publiceringsprofil för den och visar steget **Anslutning** i guiden **Publicera webbplats**.

    Klicka inte på **Publicera** ännu. I följande avsnitt konfigurerar du den nya webbappen för att anropa API-appen på mellannivå som körs i Apptjänst. 

### Ange URL:en på mellannivå i inställningarna för webbappen

1. Gå till [Azure-portalen](https://portal.azure.com/) och navigera sedan till bladet **Webbapp** för den webbapp som du skapade för att vara värd för ToDoListAngular-projektet (klientdel).

2. Klicka på **Inställningar > Programinställningar**.

3. I avsnittet **Appinställningar** lägger du till följande nyckel och värde:

  	|Nyckel|Värde|Exempel
  	|---|---|---|
  	|toDoListAPIURL|https://{namnet på ditt API på mellannivå}.azurewebsites.net|https://todolistapi0121.azurewebsites.net|

4. Klicka på **Spara**.

    När koden körs i Azure åsidosätter det här värdet URL:en för den lokala värden som finns i filen *Web.config*. 

    Koden som hämtar inställningsvärdet finns i *index.cshtml*:

        <script type="text/javascript">
            var apiEndpoint = "@System.Configuration.ConfigurationManager.AppSettings["toDoListAPIURL"]";
        </script>
        <script src="app/scripts/todoListSvc.js"></script>

    Koden i *todoListSvc.js* använder inställningen:

        return {
            getItems : function(){
                return $http.get(apiEndpoint + '/api/TodoList');
            },
            getItem : function(id){
                return $http.get(apiEndpoint + '/api/TodoList/' + id);
            },
            postItem : function(item){
                return $http.post(apiEndpoint + '/api/TodoList', item);
            },
            putItem : function(item){
                return $http.put(apiEndpoint + '/api/TodoList/', item);
            },
            deleteItem : function(id){
                return $http({
                    method: 'DELETE',
                    url: apiEndpoint + '/api/TodoList/' + id
                });
            }
        };

### Distribuera ToDoListAngular-projektet till den nya webbappen

*  I Visual Studio, i steget **Anslutning** i guiden **Publicera webbplats** klickar du på **Publicera**.

    Visual Studio distribuerar ToDoListAngular-projektet till den nya webbappen och öppnar en webbläsare till webbappens URL. 

### Testa appen utan att CORS är aktiverat 

2. Öppna konsolfönstret i webbläsarens utvecklingsverktyg.

3. I webbläsarfönstret som visar AngularJS-gränssnittet klickar du på länken **Att göra-lista**.

    JavaScript-koden försöker anropa API-appen på mellannivå men anropet misslyckas eftersom klientdelen körs i en annan domän än serversidan. Webbläsarens konsolfönster för utvecklingsverktyg visar ett felmeddelande för cross-origin.

    ![Felmeddelande för cross-origin](./media/app-service-api-cors-consume-javascript/consoleaccessdenied.png)

## Konfigurera CORS för API-appen på mellannivå

I det här avsnittet kan du konfigurera CORS-inställningen i Azure för API-appen ToDoListAPI på mellannivå. Den här inställningen tillåter att API-appen på mellannivå tar emot JavaScript-anrop från den webbapp som du skapade för ToDoListAngular-projektet.

8. I en webbläsare går du till [Azure-portalen](https://portal.azure.com/).

2. Klicka på **Apptjänster** och klicka sedan på API-appen Att göra-lista (mellannivå).

    ![Välj API-app i portalen](./media/app-service-api-cors-consume-javascript/browseapiapps.png)

10. I bladet **Inställningar** som öppnas till höger om bladet **API-app** letar du rätt på avsnittet **API** och klickar sedan på **CORS**.

    ![Välj CORS i portalen](./media/app-service-api-cors-consume-javascript/clicksettings.png)

12. I textrutan anger du URL:en för webbappen ToDoListAngular (klientdelen). Om du har distribuerat ToDoListAngular-projektet till en webbapp med namnet todolistangular0121 kan du till exempel tillåta anrop från URL:en `https://todolistangular0121.azurewebsites.net`.

    Alternativt kan ange du en asterisk (*) om du vill ange att alla ursprungsdomäner accepteras.

13. Klicka på **Spara**.

    ![Klicka på Spara](./media/app-service-api-cors-consume-javascript/corsinportal.png)

    När du klickar på **Spara** kommer API-appen att acceptera JavaScript-anrop från angiven URL. I den här skärmdumpen kommer API-appen ToDoListAPI0223 att acceptera JavaScript-klientanrop från webbappen ToDoListAngular.

### Testa appen med CORS aktiverat

* Öppna en webbläsare till HTTPS-URL:en för webbappen. 

    Den här gången låter programmet dig visa, lägga till, redigera och ta bort arbetsuppgifter. 

    ![Sida med Att göra-lista för exempelapp](./media/app-service-api-cors-consume-javascript/corssuccess.png)

## Apptjänst-CORS kontra Web API CORS

I ett Web API-projekt kan du installera paketet [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) NuGet för att ange i koden från vilken domän din API ska ta emot JavaScript-anrop.
 
Stöd för Web API CORS är mer flexibelt än stöd för Apptjänst-CORS. Till exempel kan du i koden ange olika accepterade ursprung för olika åtgärdsmetoder, medan du anger en uppsättning accepterade ursprung för alla metoder i en API-app när det gäller Apptjänst-CORS.

> [AZURE.NOTE] Försök inte att använda både Web API CORS och Apptjänst CORS i en API-app. Apptjänst-CORS har företräde och Web API CORS har ingen effekt. Om du till exempel aktiverar en ursprungsdomän i Apptjänst och aktiverar alla ursprungsdomäner i Web API-koden tar din Azure API-app bara emot samtal från den domän du angav i Azure.

### Så här aktiverar du CORS i Web API-koden

Följande steg sammanfattar processen för att aktivera stöd för Web API CORS. Mer information finns i [Aktivera cross-origin-begäranden i ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api).

1. Installera paketet [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) NuGet i ett Web API-projekt.

1. Inkludera en `config.EnableCors()` kodrad i metoden **Registrera** för **WebApiConfig**-klassen, som i följande exempel. 

        public static class WebApiConfig
        {
            public static void Register(HttpConfiguration config)
            {
                // Web API configuration and services
                
                // The following line enables you to control CORS by using Web API code
                config.EnableCors();
    
                // Web API routes
                config.MapHttpAttributeRoutes();
    
                config.Routes.MapHttpRoute(
                    name: "DefaultApi",
                    routeTemplate: "api/{controller}/{id}",
                    defaults: new { id = RouteParameter.Optional }
                );
            }
        }

1. I Web API-kontrollanten lägger du till ett `using`-uttryck för `System.Web.Http.Cors`Namnområde och lägg till `EnableCors`-attributet i kontrollantklassen eller för enskilda åtgärdsmetoder. I följande exempel gäller CORS-stödet för hela kontrollanten.

        namespace ToDoListAPI.Controllers 
        {
            [HttpOperationExceptionFilterAttribute]
            [EnableCors(origins:"https://todolistangular0121.azurewebsites.net", headers:"accept,content-type,origin,x-my-header", methods: "get,post")]
            public class ToDoListController : ApiController
 
## Använda Azure API Management med API Apps

Om du använder Azure API Management med en API-app konfigurerar du CORS i API Management i stället för i API-appen. Mer information finns i följande resurser:

* [Översikt över Azure API Management (video: CORS börjar vid 12:10)](https://azure.microsoft.com/documentation/videos/azure-api-management-overview/)
* [Korsdomänprinciper för API Management](https://msdn.microsoft.com/library/azure/dn894084.aspx#CORS)
 
## Felsökning

Om du stöter på problem när du går igenom den här kursen får du några felsökningsidéer här.

* Kontrollera att du använder den senaste versionen av [Azure SDK för .NET för Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003).

* Kontrollera att du angett `https` i CORS-inställningen och se till att du använder `https` för att köra webbappen i klientdelen.

* Kontrollera att du angett CORS-inställningen i API-appen på mellannivå, inte i webbappen i klientdelen.

* Om du konfigurerar CORS i både programkoden och i Azure Apptjänst bör du tänka på att Apptjänstens CORS-inställning åsidosätter vad du gör i programkoden. 

Mer information om Visual Studio-funktioner som gör det lättare att felsöka finns i [Felsöka Azure Apptjänst-appar i Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md).

## Nästa steg 

I den här artikeln har du sett hur du aktiverar stöd för Apptjänst-CORS så att klientens JavaScript-kod kan anropa ett API i en annan domän. Läs mer information om API Apps i [Introduktion till autentisering i Apptjänst](../app-service/app-service-authentication-overview.md),. Gå sedan till självstudierna [Användarautentisering för API Apps](app-service-api-dotnet-user-principal-auth.md).



<!--HONumber=Jun16_HO2-->


