---
title: "Kom igång med händelsehubbar i C# | Microsoft Docs"
description: "Följ de här självstudierna för att komma igång med händelsehubbar i Azure; skicka händelser i C# och ta emot dem i Java med EventProcessorHost."
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 059fb733-a397-400e-8e43-0c7ea5930b8b
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/27/2016
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: c9aa7cebab12eafe71476ee45ef578d3201e69e5
ms.openlocfilehash: e7c38aa5a778d5e8a653e753e5794d7c7da31dc1


---
# <a name="get-started-with-event-hubs"></a>Kom igång med händelsehubbar
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduktion
Händelsehubbar är en tjänst som bearbetar stora mängder händelsedata (telemetri) från anslutna enheter och program. När du har samlat in data i händelsehubbar kan du lagra dem med ett lagringskluster eller omvandla dem med hjälp av en leverantör av realtidsanalys. Den här storskaliga händelseinsamlingen och bearbetningsfunktionen är en viktig komponent inom moderna programarkitekturer som t.ex. sakernas internet.

I den här kursen får du lära dig att skapa en händelsehubb i Azure Portal. Du får också lära dig att samla in meddelanden i en händelsehubb med hjälp av ett konsolprogram som skrivits i C# och att parallellt hämta dem med hjälp av biblioteket Värd för händelsebearbetning i Java.

För att kunna genomföra den här kursen behöver du följande:

* [Microsoft Visual Studio](http://visualstudio.com)
* Ett aktivt Azure-konto. <br/>Om du inte har något konto kan du skapa ett utan kostnad på ett par minuter. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [här](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F target="_blank").

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-ephjava](../../includes/service-bus-event-hubs-get-started-receive-ephjava.md)]

## <a name="run-the-applications"></a>Kör programmen
Du är nu redo att köra programmen.

1. Kör projektet för **Mottagare**.
   
   ![][21]
2. Kör projektet för **Avsändare**.
   
   ![][22]

## <a name="next-steps"></a>Nästa steg
Nu när du har skapat ett fungerande program som skapar en händelsehubb och skickar och tar emot data kan du gå vidare till följande scenarier:

* Ett komplett [exempelprogram som använder händelsehubbar][sample application that uses Event Hubs].
* Exemplet [Skala ut händelsebearbetning med händelsehubbar][Scale out Event Processing with Event Hubs].
* [Översikt av händelsehubbar][Event Hubs overview]

<!-- Images. -->
[21]: ./media/event-hubs-csharp-ephjava-getstarted/ephjava.png
[22]: ./media/event-hubs-csharp-ephjava-getstarted/cs-send.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Event Hubs overview]: event-hubs-overview.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Dec16_HO1-->


