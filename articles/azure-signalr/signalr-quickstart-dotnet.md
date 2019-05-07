---
title: Snabbstart för att lära dig hur du använder Azure SignalR Service med ASP.NET
description: En Snabbstart för Azure SignalR Service för att skapa en chattrum med ASP.NET framework.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/20/2019
ms.author: zhshang
ms.openlocfilehash: cda1eddc5fa40e97038274cf9b4c3c2cb9552871
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65154504"
---
# <a name="quickstart-create-a-chat-room-with-aspnet-and-signalr-service"></a>Snabbstart: Skapa en chattrum med ASP.NET och SignalR Service

Azure SignalR Service baseras på [SignalR för ASP.NET Core 2.0](https://docs.microsoft.com/aspnet/core/signalr/introduction), vilket är **inte** 100% kompatibel med ASP.NET SignalR. Azure SignalR Service implementerats nytt ASP.NET SignalR data protocol baserat på senaste ASP.NET Core-technoledges. När du använder Azure SignalR Service för ASP.NET SignalR kan vissa ASP.NET SignalR-funktioner stöds inte längre, till exempel Azure SignalR titta inte meddelanden när klienten återansluter. Alltid ramens transport- och JSONP är dessutom inte support. Vissa ändringar i koden och rätt version av beroende bibliotek krävs för att göra ASP.NET SignalR program fungerar med SignalR Service. 

Referera till den [version skillnader doc](https://docs.microsoft.com/aspnet/core/signalr/version-differences?view=aspnetcore-2.2) för en fullständig lista över funktionsjämförelse mellan ASP.NET SignalR och ASP.NET Core SignalR.

I den här snabbstarten får du lära dig hur du kommer igång med ASP.NET och Azure SignalR Service för en liknande [chattrum programmet](./signalr-quickstart-dotnet-core.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
## <a name="prerequisites"></a>Nödvändiga komponenter

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
* [.NET 4.6.1](https://www.microsoft.com/net/download/windows)
* [ASP.NET SignalR 2.4.1](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure Portal](https://portal.azure.com/) med ditt Azure-konto.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

*Serverlös* läge stöds inte för ASP.NET SignalR-program. Använd alltid *standard* eller *klassiska* för Azure SignalR Service-instansen.

Du kan också skapa Azure-resurser som används i den här snabbstarten med [skapa ett skript med SignalR Service](scripts/signalr-cli-create-service.md).

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Medan tjänsten distribueras tar vi och arbetar med koden. Klona [exempelappen från GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom), ange SignalR Service-anslutningssträngen och kör programmet lokalt.

1. Öppna ett git-terminalfönster. Byt till en mapp där du vill klona exempelprojektet.

1. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

## <a name="configure-and-run-chat-room-web-app"></a>Konfigurera och köra webbprogram i chattrum

1. Starta Visual Studio och öppna lösningen i den *aspnet-samples/ChatRoom/* mapp i den klonade lagringsplatsen.

1. Hitta och väljer du den instans som du skapade i webbläsaren där Azure-portalen öppnas.

1. Välj **Nycklar** för att visa anslutningssträngarna för SignalR Service-instansen.

1. Markera och kopiera den primära anslutningssträngen.

1. Ställa in anslutningssträngen i filen web.config.

    ```xml
    <configuration>
    <connectionStrings>
        <add name="Azure:SignalR:ConnectionString" connectionString="<Replace By Your Connection String>"/>
    </connectionStrings>
    ...
    </configuration>
    ```

1. I *Startup.cs*, i stället för att anropa `MapSignalR()`, måste du anropa `MapAzureSignalR({your_applicationName})` och ange anslutningssträngen som gör programmet ansluta till tjänsten i stället för som är värd för SignalR ensamt. Ersätt `{YourApplicationName}` till namnet på ditt program. Det här namnet är ett unikt namn för att skilja det här programmet från dina andra program. Du kan använda `this.GetType().FullName` som värde.

    ```cs
    public void Configuration(IAppBuilder app)
    {
        // Any connection or hub wire up and configuration should go here
        app.MapAzureSignalR(this.GetType().FullName);
    }
    ```

    Du måste också referera till tjänst-SDK innan du använder dessa API: er. Öppna den **verktyg | NuGet-Pakethanteraren | Pakethanterarkonsolen** och kör kommandot:

    ```powershell
    Install-Package Microsoft.Azure.SignalR.AspNet
    ```

    Allt annat detsamma än de här ändringarna kan du fortfarande använda gränssnittet hub som du redan är bekant med att skriva affärslogik.

    > [!NOTE]
    > För att genomföra en slutpunkt `/signalr/negotiate` exponeras för förhandling av Azure SignalR Service SDK. Ett särskilt förhandling svar returneras när klienter försöker ansluta och omdirigera klienter till tjänsteslutpunkt som definierats i anslutningssträngen.

1. Tryck på **F5** att köra projektet i felsökningsläge. Du kan se programmet körs lokalt. I stället för som är värd för ett SignalR för körning av själva programmet, nu ansluter den till Azure SignalR Service.

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]



> [!IMPORTANT]
> Det går inte att ångra borttagningen av en resursgrupp och resursgruppen och alla resurser i den tas bort permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser. Om du har skapat resurserna som värd för det här exemplet i en befintlig resursgrupp som innehåller resurser som du vill behålla, kan du ta bort varje resurs separat från deras respektive blad istället för att ta bort resursgruppen.
> 
> 

Logga in på [Azure Portal](https://portal.azure.com) och klicka på **Resursgrupper**.

Skriv namnet på din resursgrupp i textrutan **Filtrera efter namn...**. Anvisningarna för den här snabbstarten använde en resursgrupp med namnet *SignalRTestResources*. På din resursgrupp i resultatlistan klickar du på **...** och därefter **Ta bort resursgrupp**.

   
![Ta bort](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

Efter en liten stund tas resursgruppen och resurser som finns i den bort.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapade en ny Azure SignalR Service-resurs och använder den med en ASP.NET webbapp. Därefter lär du dig hur du utvecklar program i realtid med Azure SignalR Service med ASP.NET Core.

> [!div class="nextstepaction"]
> [Azure SignalR Service med ASP.NET Core](./signalr-quickstart-dotnet-core.md)
