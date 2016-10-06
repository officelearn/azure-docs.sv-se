<properties
    pageTitle="Kom igång med händelsehubbar i C# | Microsoft Azure"
    description="Följ de här självstudierna för att komma igång med händelsehubbar i Azure; skicka händelser i C# och ta emot dem i Java med EventProcessorHost."
    services="event-hubs"
    documentationCenter=""
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/27/2016"
    ms.author="jotaub;sethm"/>


# Kom igång med händelsehubbar

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## Introduktion

Händelsehubbar är en tjänst som bearbetar stora mängder händelsedata (telemetri) från anslutna enheter och program. När du har samlat in data i händelsehubbar kan du lagra dem med ett lagringskluster eller omvandla dem med hjälp av en leverantör av realtidsanalys. Den här storskaliga händelseinsamlingen och bearbetningsfunktionen är en viktig komponent inom moderna programarkitekturer som t.ex. sakernas internet.

I den här kursen får du lära dig att skapa en händelsehubb i den klassiska Azure-portalen. Du får också lära dig att samla in meddelanden i en händelsehubb med hjälp av ett konsolprogram som skrivits i C# och att parallellt hämta dem med hjälp av biblioteket Värd för händelsebearbetning i Java.

För att kunna genomföra den här kursen behöver du följande:

+ [Microsoft Visual Studio](http://visualstudio.com)

+ Ett aktivt Azure-konto. <br/>Om du inte har något konto kan du skapa ett utan kostnad på ett par minuter. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [här](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F target="_blank").

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephjava](../../includes/service-bus-event-hubs-get-started-receive-ephjava.md)]

## Kör programmen

Du är nu redo att köra programmen.

1.  Kör projektet för **Mottagare**.

    ![][21]

2.  Kör projektet för **Avsändare**.

    ![][22]

## Nästa steg

Nu när du har skapat ett fungerande program som skapar en händelsehubb och skickar och tar emot data kan du gå vidare till följande scenarier:

- Ett komplett [exempelprogram som använder händelsehubbar][].
- Exemplet [Skala ut händelsebearbetning med händelsehubbar][].
- En [lösning för köade meddelanden][] som använder Service Bus-köer.
- [Översikt av händelsehubbar][]

<!-- Images. -->
[21]: ./media/event-hubs-csharp-ephjava-getstarted/ephjava.png
[22]: ./media/event-hubs-csharp-ephjava-getstarted/cs-send.png

<!-- Links -->
[klassiska Azure-portalen]: https://manage.windowsazure.com/
[Översikt av händelsehubbar]: event-hubs-overview.md
[exempelprogram som använder händelsehubbar]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Skala ut händelsebearbetning med händelsehubbar]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[lösning för köade meddelanden]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 



<!--HONumber=Sep16_HO4-->


