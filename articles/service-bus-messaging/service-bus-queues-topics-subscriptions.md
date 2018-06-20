---
title: Översikt över Azure Service Bus-meddelanden köer, ämnen och prenumerationer | Microsoft Docs
description: Översikt över Service Bus meddelandeentiteter.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
ms.service: service-bus-messaging
ms.topic: article
ms.date: 06/18/2018
ms.author: sethm
ms.openlocfilehash: 424004a2a39bd0d05bce515dc17685e60f7a0c9b
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231584"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Service Bus-köer, -ämnen och -prenumerationer

Microsoft Azure Service Bus stöder en uppsättning molnbaserade, meddelande indatavärdena mellanprogram teknologier, inklusive tillförlitliga message Queuing- och varaktig Publicera/prenumerera på meddelanden. Dessa ”asynkrona” meddelandefunktioner kan betraktas som frikopplad meddelandefunktioner som har stöd för att publicera och prenumerera temporal Frikoppling och scenarier med hjälp av arbetsbelastningen för Service Bus-meddelanden för belastningsutjämning. Frikopplad kommunikation har många fördelar: klienter och servrar kan till exempel ansluta efter behov och utföra åtgärder på ett asynkront sätt.

Meddelandeentiteter som utgör kärnan i meddelandefunktioner i Service Bus är köer, ämnen och prenumerationer och regler/en åtgärd.

## <a name="queues"></a>Köer

Köer erbjuder *First In, först ut* (FIFO) meddelandeleverans till en eller flera konkurrerande konsumenter. Det vill säga mottagare normalt ta emot och bearbeta meddelanden i den ordning som de har lagts till i kön, och bara en meddelandekonsument tar emot och bearbetar varje meddelande. En stor fördel med köer är att uppnå ”temporal Frikoppling” av programkomponenter. Med andra ord behöver producenter (avsändare) och konsumenter (mottagare) inte skicka och ta emot meddelanden på samma gång, eftersom meddelanden lagras varaktigt i kön. Dessutom behöver producenten inte vänta på svar från konsumenten för att kunna fortsätta bearbetningen och skicka meddelanden.

En relaterad fördel är ”belastningsutjämning”, vilket innebär att producenter och konsumenter att skicka och ta emot meddelanden med olika hastigheter. I många program varierar systembelastningen med tiden. dock är den bearbetningstid som krävs för varje arbetsenhet vanligtvis är konstant. Medlingen mellan meddelandeproducenter och konsumenter med hjälp av en kö innebär att den konsumerande appen endast som ska etableras för att kunna hantera en genomsnittlig belastning i stället för belastning. Köns djup växer och dras samman allt eftersom den inkommande belastningen varierar. Den här funktionen sparar direkt pengar avseende mängden infrastruktur som krävs för att underhålla. När belastningen ökar kan fler arbetsprocesser läggas för att läsa från kön. Varje meddelande bearbetas bara av en av arbetsprocesserna. Dessutom kan den här pull-baserade belastningsutjämning, för optimal användning av worker-datorer även om worker datorer skiljer sig åt vad gäller processorkraft, som de hämtar meddelanden med sina egna högsta hastighet. Det här mönstret kallas ofta ”konkurrerande konsument”-mönster.

Med hjälp av köer till mellanliggande mellan meddelandeproducenter och konsumenter innehåller en inbyggd lösa kopplingar mellan komponenterna. Eftersom producenter och konsumenter inte är medvetna om varandra, kan en konsument uppgraderas utan någon inverkan på producenten.

### <a name="create-queues"></a>Skapa köer

Du kan skapa köer med hjälp av den [Azure-portalen](service-bus-quickstart-portal.md), [PowerShell](service-bus-quickstart-powershell.md), [CLI](service-bus-quickstart-cli.md), eller [Resource Manager-mallar](service-bus-resource-manager-namespace-queue.md). Du sedan skicka och ta emot meddelanden med hjälp av en [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) objekt. 

Information om hur du skapar en kö och sedan skicka och ta emot meddelanden till och från kön snabbt finns i [Snabbstart](service-bus-quickstart-portal.md) för varje metod. En mer detaljerad vägledning om hur du använder köer finns [komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md). 

Ett exempel på en fungerande, finns det [BasicSendReceiveUsingQueueClient exempel](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient) på GitHub.

### <a name="receive-modes"></a>Ta emot lägen

Du kan ange två olika lägen som Service Bus tar emot meddelanden: *ReceiveAndDelete* eller *PeekLock*. I den [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) läge receive-åtgärden är en, det vill säga när Service Bus tar emot begäran, den markerar meddelandet som Förbrukat och skickar tillbaka det till programmet. **ReceiveAndDelete** läge är den enklaste modellen och fungerar bäst för scenarier där programmet kan tolerera icke-bearbetning av ett meddelande om ett fel inträffar. Överväg ett scenario där konsumenten utfärdar en receive-begäran och sedan kraschar innan bearbetningen för att förstå det här scenariot. Eftersom Service Bus markerar meddelandet som Förbrukat när programmet startas om och börjar förbruka meddelanden igen, kommer det ha missat meddelandet som förbrukades innan kraschen.

I [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) läge receive-åtgärden blir två steg, vilket gör det möjligt att stödprogram som inte tolererar att ett meddelande saknas. När Service Bus tar emot begäran, den söker efter nästa meddelande som ska förbrukas, låser det för att förhindra att andra användare tar emot det och skickar sedan tillbaka det till programmet. När programmet har avslutat bearbetningen av meddelandet (eller lagrar det på ett tillförlitligt sätt för framtida bearbetning), Slutför det andra steget i processen genom att anropa [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) för det mottagna meddelandet. När Service Bus ser den **CompleteAsync** anrop, markerar den meddelandet som Förbrukat.

Om programmet inte kan bearbeta meddelandet av någon anledning, kan det anropa den [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) metod för det mottagna meddelandet (i stället för [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync)). Den här metoden kan Service Bus låser upp meddelandet och gör det tillgängligt att tas emot igen, antingen genom samma konsumenten eller av en annan konkurrerande konsument. Det finns en tidsgräns som är kopplade till låset för det andra och om programmet misslyckas med att bearbeta meddelandet innan timeout för lås upphör att gälla (till exempel om programmet kraschar), kommer Service Bus låser upp meddelandet och gör det tillgängligt och kan tas emot igen ( i praktiken utför en [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) åtgärden som standard).

I händelse av att programmet kraschar efter att meddelandet har bearbetats men innan det **CompleteAsync** begäran har utfärdats, meddelandet är levereras på nytt till programmet när den startas om. Den här processen kallas ofta *minst när* bearbetning, det vill säga varje meddelande bearbetas minst en gång. I vissa situationer kan dock samma meddelande levereras. Om scenariot inte tolererar duplicerad bearbetning, och sedan ytterligare logik som krävs i programmet för att identifiera dubbletter, vilket kan ske utifrån den [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) för meddelandet, som förblir konstant över leveransförsök. Den här funktionen kallas *exakt en gång* bearbetning.

## <a name="topics-and-subscriptions"></a>Ämnen och prenumerationer

Till skillnad från köer, där varje meddelande bearbetas av en enskild konsument *avsnitt* och *prenumerationer* tillhandahålla en en-till-många-kommunikation i en *förPublicera/prenumerera* mönster. Användbar för att skala till ett stort antal mottagare, varje publicerat meddelande görs tillgänglig för varje prenumeration som har registrerats med ämnet. Meddelanden skickas till ett ämne och levereras till en eller flera associerade prenumerationer, beroende på filterregler som kan ställas in på grundval av per prenumeration. Prenumerationerna kan använda ytterligare filter för att begränsa de meddelanden som de vill ha. Meddelanden skickas till ett ämne på samma sätt som de skickas till en kö, men meddelanden tas inte emot i artikeln direkt. I stället tas de emot från prenumerationer. En prenumeration på artikeln liknar en virtuell kö som tar emot kopior av meddelanden som skickas till ämnet. Meddelanden tas emot från en prenumeration identiskt sätt som de tas emot från en kö.

Som jämförelse sändning av meddelande-funktionerna i en kö mappar direkt till ett ämne och dess funktioner för message-mottagning mappar till en prenumeration. Bland annat den här funktionen innebär att prenumerationer stöder samma mönster som beskrivits tidigare i det här avsnittet avseende köer: konkurrerande konsument, temporal Frikoppling, utjämna belastningen och belastningsutjämning.

### <a name="create-topics-and-subscriptions"></a>Skapa ämnen och prenumerationer

Skapa ett ämne liknar hur du skapar en kö, enligt beskrivningen i föregående avsnitt. Du sedan skickar meddelanden med hjälp av den [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient) klass. Om du vill ta emot meddelanden, kan du skapa en eller flera prenumerationer till ämnet. Liknande köer, meddelanden tas emot från en prenumeration med hjälp av en [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) objekt i stället för en [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) objekt. Skapa prenumeration klienten, skickar namnet på avsnittet, namnet på prenumerationen och (valfritt) receive-läge som parametrar. 

För en fullständig fungerar exempelvis finns på [BasicSendReceiveUsingTopicSubscriptionClient exempel](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingTopicSubscriptionClient) på Github.

### <a name="rules-and-actions"></a>Regler och åtgärder

I många fall är måste meddelanden som har specifika egenskaper bearbetas på olika sätt. Om du vill aktivera den här bearbetning, kan du konfigurera prenumerationer för att söka efter meddelanden som har önskade egenskaper och sedan göra vissa ändringar av dessa egenskaper. Du kan bara kopiera en delmängd av dessa meddelanden till prenumerationskön virtuella när Service Bus prenumerationer visas alla meddelanden som skickas till ämnet. Den här filtreringen åstadkoms med hjälp av prenumerationsfilter. Sådana ändringar kallas *filtrera åtgärder*. När en prenumeration har skapats kan du ange ett filteruttryck som fungerar i egenskaperna för meddelandet, både systemegenskaperna (till exempel **etikett**) och anpassade egenskaper (till exempel  **StoreName**.) Filteruttrycket SQL är valfri i det här fallet; utan en SQL-filteruttrycket utförs något filter som definieras för en prenumeration på alla meddelanden för den prenumerationen.

För en fullständig fungerar exempelvis finns på [TopicSubscriptionWithRuleOperationsSample exempel](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample) på GitHub.

Mer information om möjliga filtret värden finns i dokumentationen för den [SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) och [SqlRuleAction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction) klasser. 

## <a name="next-steps"></a>Nästa steg

Mer information och exempel på hur du använder Service Bus-meddelanden finns i följande avancerade avsnitt:

* [Översikt över Service Bus-meddelandetjänster](service-bus-messaging-overview.md)
* [Snabbstart: Skicka och ta emot meddelanden med Azure-portalen och .NET](service-bus-quickstart-portal.md)
* [Självstudier: Uppdatera lagret med hjälp av Azure-portalen och avsnitt-prenumerationer](service-bus-tutorial-topics-subscriptions-portal.md)


