<properties
    pageTitle="Kom igång med händelsehubbar i C# | Microsoft Azure"
    description="Följ den här kursen och kom igång med händelsehubbar i Azure med C# och EventProcessorHost."
    services="event-hubs"
    documentationCenter=""
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="05/13/2016"
    ms.author="sethm"/>

# Kom igång med händelsehubbar

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## Introduktion

Händelsehubbar är en tjänst som bearbetar stora mängder händelsedata (telemetri) från anslutna enheter och program. När du har samlat in data i händelsehubbar kan du lagra dem med ett lagringskluster eller omvandla dem med hjälp av en leverantör av realtidsanalys. Den här storskaliga händelseinsamlingen och bearbetningsfunktionen är en viktig komponent inom moderna programarkitekturer som t.ex. sakernas internet.

I den här kursen får du lära dig att skapa en händelsehubb i den klassiska Azure-portalen. Du får också lära dig att samla in meddelanden i en händelsehubb med hjälp av ett konsolprogram som skrivits i C# och att parallellt hämta dem med hjälp av biblioteket [Värd för händelsebearbetning][] i C#.

För att kunna genomföra den här kursen behöver du följande:

+ Microsoft Visual Studio 2013 eller senare, eller Microsoft Visual Studio Express för Windows. I exemplen i den här artikeln används Visual Studio 2015.

+ Ett aktivt Azure-konto. <br/>Om du inte har något konto kan du skapa ett utan kostnad på ett par minuter. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [här](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F target="_blank").

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## Kör programmen

Du är nu redo att köra programmen.

1. Gå till Visual Studio och öppna **mottagar**-projektet som du skapade tidigare.

2. Högerklicka på **mottagar**-lösningen, klicka sedan på **Lägg till**, och klicka sedan på **befintligt projekt**.
 
3. Leta upp den befintliga Sender.csproj-filen och dubbelklicka på den för att lägga till den i lösningen.
 
4. Högerklicka en gång till på **mottagar**-lösningen och klicka sedan på **Egenskaper**. Sidan **Mottagar**-egenskaper visas.

5. Klicka på **Startprojekt**, klicka sedan på knappen**Flera startprojekt**. Ställ in rutan **Åtgärder** för både **mottagar-** och **avsändar-** projekten för att **starta**.

    ![][19]

6. Klicka på **Projektberoenden**. I rutan **Projekt** klickar du på **Avsändare**. Se till att **Mottagare** i rutan **Är beroende av** är markerad.

    ![][20]

7. Klicka på **OK** för att stänga dialogrutan **Egenskaper**.

1.  Tryck på F5 för att köra **mottagar**-projektet i Visual Studio och vänta sedan tills det har startat mottagarna i alla partitionerna.

    ![][21]

2.  **Avsändar**-projektet körs automatiskt. Tryck på **Retur** i konsolfönstret och se händelserna som visas i mottagarfönstret.

    ![][22]

Tryck på **Ctrl + C** i **avsändar**-fönstret för att avsluta avsändarprogrammet, och tryck sedan på **Retur** i mottagarfönstret för att stänga det programmet.

## Nästa steg

Nu när du har skapat ett fungerande program som skapar en händelsehubb och skickar och tar emot data kan du gå vidare till följande scenarier:

- Ett komplett [exempelprogram som använder händelsehubbar][].
- Exemplet [Skala ut händelsebearbetning med händelsehubbar][].
- En [lösning för köade meddelanden][] som använder Service Bus-köer.
- [Översikt av händelsehubbar][]

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Klassisk Azure-portal]: https://manage.windowsazure.com/
[Värd för händelsebearbetning]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Översikt av händelsehubbar]: event-hubs-overview.md
[exempelprogram som använder händelsehubbar]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Skala ut händelsebearbetning med händelsehubbar]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[lösning för köade meddelanden]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 



<!--HONumber=Jun16_HO2-->


