---
title: Utveckla med ASP.NET - Azure SignalR-tjänsten
description: En snabbstart för att använda Azure SignalR-tjänsten för att skapa ett chattrum med ASP.NET ramverk.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/20/2019
ms.author: zhshang
ms.openlocfilehash: ec5b7a75bced4b7cd81a120925558b8c1be57818
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "74158174"
---
# <a name="quickstart-create-a-chat-room-with-aspnet-and-signalr-service"></a>Snabbstart: Skapa ett chattrum med ASP.NET- och SignalR-tjänsten

Azure SignalR-tjänsten baseras på [SignalR för ASP.NET Core 2.0](https://docs.microsoft.com/aspnet/core/signalr/introduction), vilket **inte** är 100 % kompatibelt med ASP.NET SignalR. Azure SignalR Service implementerade om ASP.NET SignalR-dataprotokoll baserat på de senaste ASP.NET Core-teknikerna. När du använder Azure SignalR-tjänsten för ASP.NET SignalR stöds inte längre vissa ASP.NET SignalR-funktioner, till exempel azure signalr inte spela upp meddelanden när klienten återansluter. Dessutom stöds inte Forever Frame-transporten och JSONP. Vissa kodändringar och korrekt version av beroende bibliotek behövs för att få ASP.NET SignalR-program att fungera med SignalR-tjänsten. 

Se [version skillnader doc](https://docs.microsoft.com/aspnet/core/signalr/version-differences?view=aspnetcore-2.2) för en komplett lista över funktioner jämförelse mellan ASP.NET SignalR och ASP.NET Core SignalR.

I den här snabbstarten får du lära dig hur du kommer igång med ASP.NET och Azure SignalR-tjänsten för ett liknande [chatrumsprogram](./signalr-quickstart-dotnet-core.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
## <a name="prerequisites"></a>Krav

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
* [.NET 4.6.1](https://www.microsoft.com/net/download/windows)
* [ASP.NET SignalR 2.4.1](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com/) med ditt Azure-konto.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

*Serverlöst* läge stöds inte för ASP.NET SignalR-program. Använd alltid *Standard* eller *Klassisk* för Azure SignalR Service-instansen.

Du kan också skapa Azure-resurser som används i den här snabbstarten med [Skapa ett SignalR-tjänstskript](scripts/signalr-cli-create-service.md).

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Medan tjänsten distribueras tar vi och arbetar med koden. Klona [exempelappen från GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom), ange SignalR Service-anslutningssträngen och kör programmet lokalt.

1. Öppna ett git-terminalfönster. Byt till en mapp där du vill klona exempelprojektet.

1. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

## <a name="configure-and-run-chat-room-web-app"></a>Konfigurera och köra webbapp för chattrum

1. Starta Visual Studio och öppna lösningen i mappen *aspnet-samples/ChatRoom/i* den klonade databasen.

1. Leta reda på och välj den instans du skapade i webbläsaren där Azure-portalen öppnas.

1. Välj **Nycklar** för att visa anslutningssträngarna för SignalR Service-instansen.

1. Markera och kopiera den primära anslutningssträngen.

1. Ange nu anslutningssträngen i filen web.config.

    ```xml
    <configuration>
    <connectionStrings>
        <add name="Azure:SignalR:ConnectionString" connectionString="<Replace By Your Connection String>"/>
    </connectionStrings>
    ...
    </configuration>
    ```

1. I *Startup.cs*, istället `MapSignalR()`för att ringa `MapAzureSignalR({your_applicationName})` , måste du ringa och passera i anslutning sträng för att göra programmet ansluta till tjänsten istället för att vara värd SignalR av sig själv. Ersätt `{YourApplicationName}` till namnet på ditt program. Det här namnet är ett unikt namn för att skilja det här programmet från dina andra program. Du kan `this.GetType().FullName` använda som värde.

    ```cs
    public void Configuration(IAppBuilder app)
    {
        // Any connection or hub wire up and configuration should go here
        app.MapAzureSignalR(this.GetType().FullName);
    }
    ```

    Du måste också referera till tjänsten SDK innan du använder dessa API:er. Öppna **verktygen | NuGet pakethanterare | Kommandot Package Manager Console** och kör:

    ```powershell
    Install-Package Microsoft.Azure.SignalR.AspNet
    ```

    Andra än dessa ändringar, allt annat förblir detsamma, kan du fortfarande använda navgränssnittet som du redan är bekant med för att skriva affärslogik.

    > [!NOTE]
    > I implementeringen exponeras en slutpunkt `/signalr/negotiate` för förhandling av Azure SignalR Service SDK. Det returnerar ett särskilt förhandlingssvar när klienter försöker ansluta och omdirigera klienter till tjänstslutpunkten som definierats i anslutningssträngen.

1. Tryck på **F5** för att köra projektet i felsökningsläge. Du kan se att programmet körs lokalt. I stället för att vara värd för en SignalR-körning av själva programmet ansluter den nu till Azure SignalR-tjänsten.

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]



> [!IMPORTANT]
> Det går inte att ångra borttagningen av en resursgrupp och att resursgruppen och alla resurser i den tas bort permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser. Om du har skapat resurserna som värd för det här exemplet i en befintlig resursgrupp som innehåller resurser som du vill behålla, kan du ta bort varje resurs separat från deras respektive blad istället för att ta bort resursgruppen.
> 
> 

Logga in på [Azure Portal](https://portal.azure.com) och klicka på **Resursgrupper**.

Skriv namnet på din resursgrupp i textrutan **Filter by name...** (Filtrera efter namn...). Anvisningarna för den här snabbstarten använde en resursgrupp med namnet *SignalRTestResources*. På din resursgrupp i resultatlistan klickar du på **...** och därefter **Ta bort resursgrupp**.

   
![Ta bort](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

Efter en liten stund tas resursgruppen och resurser som finns i den bort.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en ny Azure SignalR-tjänstresurs och använde den med en ASP.NET webbapp. Lär dig sedan hur du utvecklar realtidsprogram med Azure SignalR-tjänsten med ASP.NET Core.

> [!div class="nextstepaction"]
> [Azure SignalR-tjänst med ASP.NET Kärna](./signalr-quickstart-dotnet-core.md)
