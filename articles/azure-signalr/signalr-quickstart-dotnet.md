---
title: Utveckla med ASP.NET – Azure SignalR-tjänsten
description: En snabb start för att använda Azure SignalR-tjänsten för att skapa ett chattrum med ASP.NET Framework.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/20/2019
ms.author: zhshang
ms.openlocfilehash: ec5b7a75bced4b7cd81a120925558b8c1be57818
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "74158174"
---
# <a name="quickstart-create-a-chat-room-with-aspnet-and-signalr-service"></a>Snabb start: skapa ett chattrum med ASP.NET och SignalR-tjänsten

Azure SignalR service baseras på [SignalR för ASP.NET Core 2,0](https://docs.microsoft.com/aspnet/core/signalr/introduction), som **inte** är 100% kompatibel med ASP.net-signalerare. Azure SignalR service har återimplementerat ASP.NET SignalR data Protocol baserat på de senaste ASP.NET Core teknikerna. När du använder Azure SignalR-tjänsten för ASP.NET-signaler, stöds inte längre vissa ASP.NET-signaler-funktioner, till exempel kan inte Azure-signaler spela upp meddelanden igen när klienten återansluter. Dessutom stöds inte alltid-och JSONP för alltid-ramar. Vissa kod ändringar och rätt version av beroende bibliotek krävs för att göra ASP.NET SignalR-program fungera med SignalR-tjänsten. 

Se [versions skillnaders dokumentet](https://docs.microsoft.com/aspnet/core/signalr/version-differences?view=aspnetcore-2.2) för en fullständig lista över funktions jämförelser mellan ASP.net-signalerare och ASP.net Core signalerare.

I den här snabb starten får du lära dig hur du kommer igång med ASP.NET och Azure SignalR-tjänsten för ett liknande [chattrum-program](./signalr-quickstart-dotnet-core.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
## <a name="prerequisites"></a>Krav

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
* [.NET 4.6.1](https://www.microsoft.com/net/download/windows)
* [ASP.NET-SignalR 2.4.1](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure Portal](https://portal.azure.com/) med ditt Azure-konto.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Läge utan *Server* stöds inte för ASP.net SignalR-program. Använd alltid *standard* eller *klassisk* för Azure SignalR service-instansen.

Du kan också skapa Azure-resurser som används i den här snabb starten med [skapa ett skript för signalering](scripts/signalr-cli-create-service.md).

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Medan tjänsten distribueras tar vi och arbetar med koden. Klona [exempelappen från GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom), ange SignalR Service-anslutningssträngen och kör programmet lokalt.

1. Öppna ett git-terminalfönster. Byt till en mapp där du vill klona exempelprojektet.

1. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

## <a name="configure-and-run-chat-room-web-app"></a>Konfigurera och kör chattrum-webbappen

1. Starta Visual Studio och öppna lösningen i mappen *ASPNET-samples/ChatRoom/* på den klonade lagrings platsen.

1. I webbläsaren där Azure Portal öppnas söker du efter och väljer den instans som du skapade.

1. Välj **Nycklar** för att visa anslutningssträngarna för SignalR Service-instansen.

1. Markera och kopiera den primära anslutningssträngen.

1. Ange nu anslutnings strängen i filen Web. config.

    ```xml
    <configuration>
    <connectionStrings>
        <add name="Azure:SignalR:ConnectionString" connectionString="<Replace By Your Connection String>"/>
    </connectionStrings>
    ...
    </configuration>
    ```

1. I *startup.cs*, i stället för `MapSignalR()`att anropa, måste du `MapAzureSignalR({your_applicationName})` anropa och skicka anslutnings strängen för att programmet ska kunna ansluta till tjänsten i stället för att vara värd för SignalR. Ersätt `{YourApplicationName}` till namnet på ditt program. Det här namnet är ett unikt namn som särskiljer det här programmet från andra program. Du kan använda `this.GetType().FullName` som värde.

    ```cs
    public void Configuration(IAppBuilder app)
    {
        // Any connection or hub wire up and configuration should go here
        app.MapAzureSignalR(this.GetType().FullName);
    }
    ```

    Du måste också referera till service SDK innan du använder dessa API: er. Öppna **verktygen | NuGet-paket hanterare | Package Manager-konsolen** och kör kommando:

    ```powershell
    Install-Package Microsoft.Azure.SignalR.AspNet
    ```

    Förutom dessa ändringar är allt annat detsamma, men du kan fortfarande använda hubb gränssnittet som du redan är bekant med för att skriva affärs logik.

    > [!NOTE]
    > I implementeringen visas en `/signalr/negotiate` slut punkt för förhandling av Azure SignalR service SDK. Det returnerar ett särskilt förhandlings svar när klienter försöker ansluta och omdirigera klienter till tjänstens slut punkt som definierats i anslutnings strängen.

1. Tryck på **F5** för att köra projektet i fel söknings läge. Du kan se att programmet körs lokalt. I stället för att vara värd för en signal körning av själva programmet ansluter den nu till Azure SignalR-tjänsten.

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

I den här snabb starten skapade du en ny Azure SignalR service-resurs och använde den med en ASP.NET-webbapp. Härnäst lär du dig hur du utvecklar real tids program med hjälp av Azure SignalR-tjänsten med ASP.NET Core.

> [!div class="nextstepaction"]
> [Azure SignalR service med ASP.NET Core](./signalr-quickstart-dotnet-core.md)
