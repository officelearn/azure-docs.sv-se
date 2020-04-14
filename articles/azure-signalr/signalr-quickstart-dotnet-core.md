---
title: Snabbstart för att lära dig hur du använder Azure SignalR Service
description: En snabbstart för att använda Azure SignalR Service för att skapa ett chattrum med ASP.NET Core MVC-appar.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: zhshang
ms.openlocfilehash: f87625fe4f56b369f2bf4aade3ef5424084b6fe8
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81254894"
---
# <a name="quickstart-create-a-chat-room-by-using-signalr-service"></a>Snabbstart: Skapa ett chattrum med hjälp av SignalR-tjänsten


Azure SignalR Service är en Azure-tjänst som hjälper utvecklare att enkelt skapa webbappar med realtidsfunktioner. Denna tjänst är baserad på [SignalR för ASP.NET Core 2.1](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-2.1), men stöder även [SignalR för ASP.NET Core 3.0](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.0).

Den här artikeln visar hur du kommer igång med Azure SignalR Service. I den här snabbstarten skapar du ett chattprogram med hjälp av en ASP.NET Core MVC-webbapp. Den här appen skapar en anslutning till din Azure SignalR Service-resurs för att aktivera uppdateringar av innehåll i realtid. Du är värd för webbprogrammet lokalt och ansluter till flera webbläsarklienter. Varje klient kommer att kunna skicka innehållsuppdateringar till alla andra klienter. 

Du kan använda valfritt kodredigeringsprogram för att slutföra stegen i den här snabbstarten. Ett alternativ är [Visual Studio Code](https://code.visualstudio.com/), som finns på Windows, macOS och Linux-plattformar.

Koden för de här självstudierna är tillgänglig för nedladdning på [GitHub-lagringsplatsen AzureSignalR-samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom). Du kan också skapa De Azure-resurser som används i den här snabbstarten genom att följa [Skapa ett SignalR-tjänstskript](scripts/signalr-cli-create-service.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Krav

* Installera [.NET Core SDK](https://www.microsoft.com/net/download/windows).
* Ladda ned eller klona GitHub-lagringsplatsen [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples). 

## <a name="create-an-azure-signalr-resource"></a>Skapa en Azure SignalR-resurs

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>Skapa en ASP.NET Core-webbapp

I det här avsnittet använder du [.NET Core command-line interface (CLI)](https://docs.microsoft.com/dotnet/core/tools/) för att skapa ett ASP.NET Core MVC-webbappprojekt. Fördelen med att använda .NET Core CLI över Visual Studio är att den är tillgänglig på Windows-, macOS- och Linux-plattformarna. 

1. Skapa en mapp för projektet. Den här snabbstarten använder mappen *E:\Testing\chattest.*

2. I den nya mappen kör du följande kommando för att skapa projektet:

        dotnet new mvc


## <a name="add-secret-manager-to-the-project"></a>Lägg till Secret Manager i projektet

I det här avsnittet ska du lägga till [secret manager-verktyget](https://docs.microsoft.com/aspnet/core/security/app-secrets) i projektet. Med verktyget Secret Manager lagras känsliga data för utvecklingsarbete utanför projektträdet. Den här metoden hjälper till att förhindra oavsiktlig delning av apphemligheter i källkoden.

1. Öppna *.csproj*-filen. Lägg till ett `DotNetCliToolReference`-element att inkludera *Microsoft.Extensions.SecretManager.Tools*. Lägg också `UserSecretsId` till ett element som visas i följande kod för *chattest.csproj*och spara filen.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
    <PropertyGroup>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <UserSecretsId>SignalRChatRoomEx</UserSecretsId>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.0" />
    </ItemGroup>
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.0" />
        <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.0" />
    </ItemGroup>
    </Project>    
    ```

## <a name="add-azure-signalr-to-the-web-app"></a>Lägg till Azure SignalR till webbappen

1. Lägg till en `Microsoft.Azure.SignalR` referens till NuGet-paketet genom att köra följande kommando:

        dotnet add package Microsoft.Azure.SignalR

2. Kör följande kommando för att återställa paket för projektet:

        dotnet restore

3. Lägg till en hemlighet med namnet *Azure: SignalR:ConnectionString* till Secret Manager. 

    Den här hemligheten innehåller anslutningssträngen för åtkomst till din SignalR Service-resurs. *Azure:SignalR:ConnectionString* är standardkonfigurationsnyckeln som SignalR söker efter för att upprätta en anslutning. Ersätt värdet i följande kommando med anslutningssträngen för din SignalR-tjänstresurs.

    Du måste köra det här kommandot i samma katalog som *CSproj-filen.*

    ```
    dotnet user-secrets set Azure:SignalR:ConnectionString "<Your connection string>"    
    ```

    Secret Manager används endast för att testa webbappen medan den finns lokalt. I en senare självstudiekurs distribuerar du chattwebbappen till Azure. När webbappen har distribuerats till Azure använder du en programinställning i stället för att lagra anslutningssträngen med Secret Manager.

    Den här hemligheten nås med konfigurations-API:et. Ett kolon (:) fungerar i konfigurationsnamnet med konfigurations-API:et på alla plattformar som stöds. Se [Konfiguration efter miljö](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0). 


4. Öppna *Startup.cs* och uppdatera `ConfigureServices`-metoden för att använda Azure SignalR Service genom att anropa metoden `services.AddSignalR().AddAzureSignalR()`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        services.AddSignalR().AddAzureSignalR();
    }
    ```

    Genom att inte `AddAzureSignalR()`skicka en parameter till använder den här koden standardkonfigurationsnyckeln för SignalR-tjänstens resursanslutningssträng. Standardkonfigurationsnyckeln är *Azure:SignalR:ConnectionString*.

5. Även i *Startup.cs*, uppdatera `Configure` metoden genom att `app.UseStaticFiles()` ersätta samtalet till med följande kod och spara filen, för ASP.NET Core 2 bara.

    ```csharp
    app.UseFileServer();
    app.UseAzureSignalR(routes =>
    {
        routes.MapHub<Chat>("/chat");
    });
    ```            
    För ASP.NET Core 3+ ersätter du ovanstående kod med:

    ```csharp
    app.UseFileServer();
    app.UseRouting();
    app.UseAuthorization();

    app.UseEndpoints(routes =>
    {
        routes.MapHub<Chat>("/chat");
    });
    ```

### <a name="add-a-hub-class"></a>Lägg till en hubbklass

I SignalR är ett nav en kärnkomponent som exponerar en uppsättning metoder som kan anropas från klienten. I det här avsnittet får du lära dig att definiera en hubbklass med en av två metoder: 

* `Broadcast`: Den här metoden sänder ett meddelande till alla klienter.
* `Echo`: Den här metoden skickar ett meddelande tillbaka till anroparen.

Båda metoderna `Clients` använder gränssnittet som ASP.NET Core SignalR SDK tillhandahåller. Det här gränssnittet ger dig tillgång till alla anslutna klienter, så att du kan skicka innehåll till dina klienter.

1. Lägg till en ny mapp med namnet *Hubb* i projektkatalogen. Lägg till en ny hubbkodfil med namnet *Chat.cs* i den nya mappen.

2. Lägg till följande kod i *Chat.cs* för att definiera hubbklassen och spara filen. 

    Uppdatera namnområdet för den här klassen om du har använt ett projektnamn som skiljer sig åt från *chattest*.

    ```csharp
    using Microsoft.AspNetCore.SignalR;

    namespace chattest
    {

        public class Chat : Hub
        {
            public void BroadcastMessage(string name, string message)
            {
                Clients.All.SendAsync("broadcastMessage", name, message);
            }

            public void Echo(string name, string message)
            {
                Clients.Client(Context.ConnectionId).SendAsync("echo", name, message + " (echo from server)");
            }
        }
    }
    ```

### <a name="add-the-client-interface-for-the-web-app"></a>Lägga till klientgränssnittet för webbappen

Klientanvändargränssnittet för den här chattrumsappen består av HTML och JavaScript i en fil med namnet *index.html* i *katalogen wwwroot.*

Kopiera *filen index.html,* *css-mappen* och *skriptmappen* från *mappen wwwroot* i [exempeldatabasen](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot). Klistra in dem i projektets *wwwroot-mapp.*

Här är huvudkoden för *index.html:* 

```javascript
var connection = new signalR.HubConnectionBuilder()
                            .withUrl('/chat')
                            .build();
bindConnectionMessage(connection);
connection.start()
    .then(function () {
        onConnected(connection);
    })
    .catch(function (error) {
        console.error(error.message);
    });
```    

Koden i *index.html-anrop* för `HubConnectionBuilder.build()` att göra en HTTP-anslutning till Azure SignalR-resursen.

Om anslutningen lyckas skickas anslutningen till `bindConnectionMessage` som lägger till händelsehanterare för inkommande innehåll som skickas till klienten. 

`HubConnection.start()` startar kommunikationen med hubben. Lägger `onConnected()` sedan till knapphändelsehanterare. Dessa hanterare använder anslutningen för att klientens ska kunna skicka innehållsuppdateringar till alla anslutna klienter.

## <a name="add-a-development-runtime-profile"></a>Lägg till en körningsprofil för utveckling

I det här avsnittet ska du lägga till en utvecklingskörningsmiljö för ASP.NET Core. Mer information finns [i Arbeta med flera miljöer i ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/environments).

1. Skapa en mapp med namnet *Egenskaper* i projektet.

2. Lägg till en ny fil med namnet *launchSettings.json* i mappen, med följande innehåll, och spara filen.

    ```json
    {
        "profiles" : 
        {
            "ChatRoom": 
            {
                "commandName": "Project",
                "launchBrowser": true,
                "environmentVariables": 
                {
                    "ASPNETCORE_ENVIRONMENT": "Development"
                },
                "applicationUrl": "http://localhost:5000/"
            }
        }
    }
    ```


## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Om du vill skapa appen med hjälp av .NET Core CLI kör du följande kommando i kommandoskalet:

        dotnet build

2. När versionen har slutförts kör du följande kommando för att köra webbappen lokalt:

        dotnet run

    Appen kommer att vara värd lokalt på port 5000, som konfigurerats i vår utvecklingskörningsprofil:

        E:\Testing\chattest>dotnet run
        Hosting environment: Development
        Content root path: E:\Testing\chattest
        Now listening on: http://localhost:5000
        Application started. Press Ctrl+C to shut down.    

3. Öppna två webbläsarfönster. Gå till `http://localhost:5000`. Du uppmanas att ange ditt namn. Ange ett klientnamn för båda klienterna och testa att trycka på meddelandeinnehåll mellan båda klienterna med hjälp av knappen **Skicka.**

    ![Exempel på en Azure SignalR-gruppchatt](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)



## <a name="clean-up-resources"></a>Rensa resurser

Om du fortsätter till nästa självstudiekurs kan du behålla de resurser som skapas i den här snabbstarten och återanvända dem.

Om du är klar med snabbstartsexemplprogrammet kan du ta bort Azure-resurser som skapats i den här snabbstarten för att undvika avgifter. 

> [!IMPORTANT]
> Det går inte att ta bort en resursgrupp och alla resurser i den gruppen. Var noga så att du inte tar bort fel resursgrupp eller resurser av misstag. Om du har skapat resurserna för att vara värd för det här exemplet i en befintlig resursgrupp som innehåller resurser som du vill behålla, kan du ta bort varje resurs individuellt från bladet i stället för att ta bort resursgruppen.
> 
> 

Logga in på [Azure Portal](https://portal.azure.com) och välj **Resursgrupper**.

Skriv namnet på resursgruppen i textrutan **Filter efter namn.** Anvisningarna för den här snabbstarten använde en resursgrupp med namnet *SignalRTestResources*. I resursgruppen i resultatlistan väljer du ellipsen (**...**) > **Ta bort resursgruppen**.

   
![Val för att ta bort en resursgrupp](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)


Du blir ombedd att bekräfta borttagningen av resursgruppen. Ange namnet på resursgruppen som du vill bekräfta och välj **Ta bort**.
   
Efter en liten stund tas resursgruppen och de resurser som finns i den bort.



## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en ny Azure SignalR-tjänstresurs. Du använde den sedan med en ASP.NET Core-webbapp för att skicka innehållsuppdateringar i realtid till flera anslutna klienter. Om du vill veta mer om hur du använder Azure SignalR-tjänsten fortsätter du till självstudien som visar autentisering.

> [!div class="nextstepaction"]
> [Azure SignalR Service-autentisering](./signalr-concept-authenticate-oauth.md)


