---
title: Snabbstart för att lära dig hur du använder Azure SignalR Service
description: En snabbstart för att använda Azure SignalR Service för att skapa ett chattrum med ASP.NET Core MVC-appar.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 3dc893ea10e47e867110f674a458498a6bd24a4f
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560694"
---
# <a name="quickstart-create-a-chat-room-by-using-signalr-service"></a>Snabbstart: Skapa en chattrum med SignalR Service


Azure SignalR Service är en Azure-tjänst som hjälper utvecklare att enkelt skapa webbappar med realtidsfunktioner. Den här tjänsten baseras på [SignalR för ASP.NET Core 2.0](https://docs.microsoft.com/aspnet/core/signalr/introduction).

Den här artikeln visar hur du kommer igång med Azure SignalR Service. I den här snabbstarten skapar du ett chattprogram med hjälp av en ASP.NET Core MVC-webbapp. Den här appen skapar en anslutning till din Azure SignalR Service-resurs för att aktivera uppdateringar av innehåll i realtid. Du kommer vara värd för webbappen lokalt och ansluta flera webbläsarklienter. Varje klient kommer att kunna skicka innehållsuppdateringar till alla andra klienter. 

Du kan använda valfritt kodredigeringsprogram för att slutföra stegen i den här snabbstarten. Ett alternativ är [Visual Studio Code](https://code.visualstudio.com/), som är tillgängligt på Windows, macOS och Linux-plattformar.

Koden för de här självstudierna är tillgänglig för nedladdning på [GitHub-lagringsplatsen för AzureSignalR-exempel](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom). Du kan även skapa Azure-resurser används i den här snabbstarten genom att följa [skapa ett skript med SignalR Service](scripts/signalr-cli-create-service.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Nödvändiga komponenter

* Installera den [.NET Core SDK](https://www.microsoft.com/net/download/windows).
* Ladda ned eller klona GitHub-lagringsplatsen [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples). 

## <a name="create-an-azure-signalr-resource"></a>Skapa en Azure SignalR-resurs

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>Skapa en ASP.NET Core-webbapp

I det här avsnittet ska du använda den [.NET Core-kommandoradsgränssnittet (CLI)](https://docs.microsoft.com/dotnet/core/tools/) att skapa en ASP.NET Core MVC-webbapprojektet. Fördelen med att använda .NET Core CLI via Visual Studio är att den är tillgänglig i Windows, macOS och Linux-plattformar. 

1. Skapa en mapp för ditt projekt. Den här snabbstarten används de *E:\Testing\chattest* mapp.

2. Kör följande kommando för att skapa projektet i den nya mappen:

        dotnet new mvc


## <a name="add-secret-manager-to-the-project"></a>Lägg till Secret Manager i projektet

I det här avsnittet lägger du till den [Secret Manager verktyget](https://docs.microsoft.com/aspnet/core/security/app-secrets) i projektet. Verktyget Secret Manager lagrar känsliga data för utvecklingsarbete utanför projektträdet. Den här metoden hjälper till att förhindra oavsiktlig delning av apphemligheter i källkoden.

1. Öppna *.csproj*-filen. Lägg till ett `DotNetCliToolReference`-element att inkludera *Microsoft.Extensions.SecretManager.Tools*. Också lägga till en `UserSecretsId` element som visas i följande kod för *chattest.csproj*, och spara filen.

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

1. Lägg till en referens till den `Microsoft.Azure.SignalR` NuGet-paketet genom att köra följande kommando:

        dotnet add package Microsoft.Azure.SignalR

2. Kör följande kommando för att återställa paketen för ditt projekt:

        dotnet restore

3. Lägg till en hemlighet med namnet *Azure: SignalR:ConnectionString* till Secret Manager. 

    Den här hemligheten innehåller anslutningssträngen för åtkomst till din SignalR Service-resurs. *Azure: SignalR:ConnectionString* är standard konfigurationsnyckeln som SignalR ser ut för att upprätta en anslutning. Ersätt värdet i följande kommando med anslutningssträngen för din SignalR Service-resurs.

    Du måste köra det här kommandot i samma katalog som den *.csproj* fil.

    ```
    dotnet user-secrets set Azure:SignalR:ConnectionString "<Your connection string>"    
    ```

    SECRET Manager används endast för att testa webbappen medan det finns lokalt. I en senare självstudien får distribuera du chatt-webbapp till Azure. När webbappen har distribuerats till Azure ska du använda en programinställning istället för att lagra anslutningssträngen med Secret Manager.

    Den här hemligheten nås med konfigurations-API. Ett kolon (:) fungerar i Konfigurationsnamnet med konfigurations-API på alla plattformar som stöds. Se [konfigurationen av miljön](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0). 


4. Öppna *Startup.cs* och uppdatera `ConfigureServices`-metoden för att använda Azure SignalR Service genom att anropa metoden `services.AddSignalR().AddAzureSignalR()`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        services.AddSignalR().AddAzureSignalR();
    }
    ```

    Genom att inte skicka en parameter till `AddAzureSignalR()`, den här koden använder standard-konfigurationsnyckeln för anslutningssträngen för SignalR Service-resurs. Standard är *Azure: SignalR:ConnectionString*.

5. Uppdatera även `Configure`-metoden i *Startup.cs* genom att ersätta anropet till `app.UseStaticFiles()` med följande kod och spara filen.

    ```csharp
    app.UseFileServer();
    app.UseAzureSignalR(routes =>
    {
        routes.MapHub<Chat>("/chat");
    });
    ```            

### <a name="add-a-hub-class"></a>Lägg till en hubbklass

I SignalR är en hub en central del som Exponerar en uppsättning metoder som kan anropas från klienten. I det här avsnittet får du lära dig att definiera en hubbklass med en av två metoder: 

* `Broadcast`: Den här metoden sänder ett meddelande till alla klienter.
* `Echo`: Den här metoden skickar ett meddelande tillbaka till anroparen.

Båda metoderna använder den `Clients` gränssnitt som ASP.NET Core SignalR SDK innehåller. Det här gränssnittet ger dig tillgång till alla anslutna klienter, så att du kan skicka innehåll till dina klienter.

1. Lägg till en ny mapp med namnet *Hubb* i projektkatalogen. Lägg till en ny hubbkodfil med namnet *Chat.cs* i den nya mappen.

2. Lägg till följande kod i *Chat.cs* att definiera hub klassen och spara filen. 

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

### <a name="add-the-client-interface-for-the-web-app"></a>Lägg till användargränssnittet för webbappen

Klientens användargränssnitt för den här appen i chattrum består av HTML och JavaScript i en fil med namnet *index.html* i den *wwwroot* directory.

Kopiera den *index.html* filen, den *css* mappen och *skript* mappen från den *wwwroot* mappen för den [exempel databasen](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot). Klistra in dem i ditt projekt *wwwroot* mapp.

Här är huvudsakliga koden för *index.html*: 

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

Koden i *index.html* anrop `HubConnectionBuilder.build()` att göra en HTTP-anslutning till Azure SignalR-resurs.

Om anslutningen lyckas skickas anslutningen till `bindConnectionMessage` som lägger till händelsehanterare för inkommande innehåll som skickas till klienten. 

`HubConnection.start()` startar kommunikationen med hubben. Sedan `onConnected()` lägger till knapphändelsehanterare. Dessa hanterare använder anslutningen för att klientens ska kunna skicka innehållsuppdateringar till alla anslutna klienter.

## <a name="add-a-development-runtime-profile"></a>Lägg till en körningsprofil för utveckling

I det här avsnittet lägger du till en runtime-utvecklingsmiljö för ASP.NET Core. Mer information finns i [arbeta med flera miljöer i ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/environments).

1. Skapa en mapp med namnet *egenskaper* i projektet.

2. Lägg till en ny fil med namnet *launchSettings.json* till mappen med följande innehåll och spara filen.

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

1. Om du vill skapa appen med hjälp av .NET Core CLI kör du följande kommando i Kommandotolken:

        dotnet build

2. När bygget har slutförts kör du följande kommando för att köra webbappen lokalt.

        dotnet run

    Appen körs lokalt på port 5000, som konfigurerats i vår utveckling runtime-profil:

        E:\Testing\chattest>dotnet run
        Hosting environment: Development
        Content root path: E:\Testing\chattest
        Now listening on: http://localhost:5000
        Application started. Press Ctrl+C to shut down.    

3. Öppna två webbläsarfönster. I varje webbläsare går du till `http://localhost:5000`. Du uppmanas att ange ditt namn. Ange ett klientnamn för både klienter och testa push-överföra meddelandeinnehåll mellan båda klienter med hjälp av den **skicka** knappen.

    ![Exempel på en chatt för Azure SignalR-grupp](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)



## <a name="clean-up-resources"></a>Rensa resurser

Om du fortsätter till nästa självstudie, kan du hålla resurserna som du skapade i den här snabbstarten och återanvända.

Om du är klar med snabbstarten exempelprogram kan du ta bort Azure-resurser skapas i denna Snabbstart för att undvika avgifter. 

> [!IMPORTANT]
> Ta bort en resursgrupp kan inte ångras och innehåller alla resurser i gruppen. Var noga så att du inte tar bort fel resursgrupp eller resurser av misstag. Om du har skapat resurserna som värd för det här exemplet i en befintlig resursgrupp som innehåller resurser som du vill behålla, kan du ta bort varje resurs separat från tillhörande blad i stället för att ta bort resursgruppen.
> 
> 

Logga in på [Azure-portalen](https://portal.azure.com) och välj **Resursgrupper**.

I den **filtrera efter namn** text skriver du namnet på resursgruppen. Anvisningarna för den här snabbstarten använde en resursgrupp med namnet *SignalRTestResources*. På din resursgrupp i resultatlistan väljer du de tre punkterna (**...** ) > **Ta bort resursgrupp**.

   
![Val för att ta bort en resursgrupp](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)


Du blir ombedd att bekräfta borttagningen av resursgruppen. Ange namnet på din resursgrupp för att bekräfta och välj **ta bort**.
   
Efter en liten stund tas resursgruppen och de resurser som finns i den bort.



## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en ny Azure SignalR Service-resurs. Du sedan använder den med en ASP.NET Core-webbapp för att skicka uppdateringar av innehållet i realtid till flera anslutna klienter. Om du vill veta mer om hur du använder Azure SignalR Service kan du fortsätta till självstudien som visar autentisering.

> [!div class="nextstepaction"]
> [Azure SignalR Service-autentisering](./signalr-concept-authenticate-oauth.md)


