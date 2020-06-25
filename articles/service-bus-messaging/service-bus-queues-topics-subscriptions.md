---
title: Azure Service Bus Messaging – köer, ämnen och prenumerationer
description: Den här artikeln innehåller en översikt över Azure Service Bus meddelande enheter (kö, ämnen och prenumerationer).
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 7d6dbbd0a8cf5699c2cdca34ebeca6773c1fbf6e
ms.sourcegitcommit: 61d92af1d24510c0cc80afb1aebdc46180997c69
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2020
ms.locfileid: "85337213"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Service Bus-köer, -ämnen och -prenumerationer

Microsoft Azure Service Bus har stöd för en uppsättning molnbaserade, objektorienterade mellanprogram teknologier, inklusive Reliable Message Queuing och varaktiga meddelanden för publicering/prenumeration. Dessa "Brokered"-meddelande funktioner kan ses som fristående meddelande funktioner som stöder publicering – prenumerera, tids fri koppling och belastnings Utjämnings scenarier med hjälp av Service Bus meddelande arbets belastning. Frikopplad kommunikation har många fördelar: klienter och servrar kan till exempel ansluta efter behov och utföra åtgärder på ett asynkront sätt.

Meddelande enheterna som utgör kärnan till meddelande funktionerna i Service Bus är köer, ämnen och prenumerationer och regler/åtgärder.

## <a name="queues"></a>Köer

Köer ger en eller flera konkurrerande konsumenter *först in-, First-Out* (FIFO). Det innebär att mottagare vanligt vis tar emot och bearbetar meddelanden i den ordning som de har lagts till i kön, och endast en meddelande mottagare får och bearbetar varje meddelande. En viktig fördel med att använda köer är att uppnå program komponenternas "temporala koppling" av program komponenter. Med andra ord behöver producenterna (avsändare) och konsumenter (mottagare) inte skicka och ta emot meddelanden samtidigt, eftersom meddelanden lagras varaktigt i kön. Dessutom behöver producenten inte vänta på ett svar från konsumenten för att fortsätta att bearbeta och skicka meddelanden.

En relaterad förmån är "belastnings utjämning", som gör det möjligt för producenter och konsumenter att skicka och ta emot meddelanden med olika priser. I många program varierar system belastningen över tid. bearbetnings tiden som krävs för varje arbets enhet är dock normalt konstant. Genom att använda en kö för att kunna hantera den genomsnittliga belastningen i stället för den högsta belastningen, är det bara att använda en kö som kan användas för att kunna hantera den genomsnittliga belastningen. Köns djup växer och dras samman allt eftersom den inkommande belastningen varierar. Den här funktionen sparar pengar direkt med avseende på den mängd infrastruktur som krävs för att hantera program belastningen. När belastningen ökar kan fler arbets processer läggas till för att läsa från kön. Varje meddelande bearbetas bara av en av arbetsprocesserna. Dessutom tillåter den här hämtningen av den här hämtningen av arbets datorer en optimal användning av arbets datorerna även om arbets datorerna skiljer sig åt från bearbetnings kraft, när de hämtar meddelanden till sin egen maximala taxa. Det här mönstret kallas ofta för "konkurrerande konsument"-mönstret.

Att använda köer på mellan meddelande producenter och konsumenter är en lös koppling mellan komponenterna. Eftersom producenter och konsumenter inte är medvetna om varandra kan en konsument uppgraderas utan att det påverkar producenten.

### <a name="create-queues"></a>Skapa köer

Du skapar köer med hjälp av mallarna [Azure Portal](service-bus-quickstart-portal.md), [PowerShell](service-bus-quickstart-powershell.md), [CLI](service-bus-quickstart-cli.md)eller [Resource Manager](service-bus-resource-manager-namespace-queue.md). Sedan kan du skicka och ta emot meddelanden med hjälp av ett [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) -objekt.

Om du snabbt vill lära dig hur du skapar en kö och sedan skickar och tar emot meddelanden till och från kön, se [snabb starter](service-bus-quickstart-portal.md) för varje metod. En mer djupgående självstudie om hur du använder köer finns i [komma igång med Service Bus köer](service-bus-dotnet-get-started-with-queues.md).

Ett arbets exempel finns i BasicSendReceiveUsingQueueClient- [exemplet](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient) på GitHub.

### <a name="receive-modes"></a>Mottagnings lägen

Du kan ange två olika lägen för Service Bus tar emot meddelanden: *ReceiveAndDelete* eller *PeekLock*. I [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) -läge är mottagnings åtgärden en enskild bild. det vill säga när Service Bus tar emot begäran från konsumenten, markerar det meddelandet som förbrukat och returnerar det till klient programmet. **ReceiveAndDelete** -läget är den enklaste modellen och fungerar bäst för scenarier där programmet kan tolerera att inte bearbeta ett meddelande om ett fel uppstår. För att förstå det här scenariot bör du överväga ett scenario där klienten utfärdar Receive-begäran och sedan kraschar innan den bearbetas. Eftersom Service Bus markerar meddelandet som förbrukat, när programmet startas om och börjar förbruka meddelanden igen, kommer det att ha missat meddelandet som förbrukades innan kraschen.

I [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) -läge blir Receive-åtgärden två steg, vilket gör det möjligt att stödja program som inte kan tolerera meddelanden som saknas. När Service Bus tar emot begäran hittar den nästa meddelande som ska förbrukas, låser det för att hindra andra användare från att ta emot det och returnerar det sedan till programmet. När programmet har slutfört bearbetningen av meddelandet (eller lagrar det tillförlitligt för framtida bearbetning) slutförs det andra steget i Receive-processen genom att anropa [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) på det mottagna meddelandet. När Service Bus ser **CompleteAsync** -anropet markerar det meddelandet som förbrukat.

Om programmet inte kan bearbeta meddelandet av någon anledning kan det anropa metoden [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) i det mottagna meddelandet (i stället för [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync)). Med den här metoden kan Service Bus låsa upp meddelandet och göra det tillgängligt att tas emot igen, antingen av samma konsument eller av en annan konkurrerande konsument. För det andra är det en tids gräns som är kopplad till låset och om programmet inte kan bearbeta meddelandet innan tids gränsen för låsning går ut (till exempel om programmet kraschar) låser Service Bus upp meddelandet och gör det tillgängligt för att tas emot igen (huvudsakligen utföra en [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) -åtgärd som standard).

I händelse av att programmet kraschar efter bearbetning av meddelandet, men innan **CompleteAsync** -begäran utfärdas omlevereras meddelandet till programmet när det startas om. Den här processen anropas ofta *minst en gång* . det vill säga varje meddelande bearbetas minst en gång. Men i vissa situationer kan samma meddelande levereras igen. Om scenariot inte kan tolerera dubbla bearbetningar krävs ytterligare logik i programmet för att identifiera dubbletter, som kan uppnås baserat på meddelandets [messageid](/dotnet/api/microsoft.azure.servicebus.message.messageid) -egenskap, som är konstant över alla leverans försök. Den här funktionen kallas *exakt* för bearbetning.

## <a name="topics-and-subscriptions"></a>Ämnen och prenumerationer

I motsats till köer, där varje meddelande bearbetas av en enskild konsument, tillhandahåller *ämnen* och *prenumerationer* en en-till-många-form av kommunikation i ett mönster för *publicering/prenumeration* . Det är användbart för att skala till ett stort antal mottagare, varje publicerat meddelande görs tillgängligt för varje prenumeration som registrerats i ämnet. Meddelanden skickas till ett ämne och skickas till en eller flera associerade prenumerationer, beroende på vilka filter regler som kan ställas in per prenumeration. Prenumerationerna kan använda ytterligare filter för att begränsa vilka meddelanden som ska tas emot. Meddelanden skickas till ett ämne på samma sätt som de skickas till en kö, men meddelanden tas inte emot från avsnittet direkt. De tas i stället från prenumerationer. En ämnes prenumeration liknar en virtuell kö som tar emot kopior av meddelanden som skickas till ämnet. Meddelanden tas emot från en prenumeration på samma sätt som de tas emot från en kö.

Funktionen för att skicka meddelanden i en kö mappar direkt till ett ämne och dess funktioner för meddelande mottagning mappar till en prenumeration. Detta innebär bland annat att prenumerationer har stöd för samma mönster som beskrivs tidigare i det här avsnittet med avseende på köer: konkurrerande konsument, temporala, belastnings utjämning och belastnings utjämning.

### <a name="create-topics-and-subscriptions"></a>Skapa ämnen och prenumerationer

Att skapa ett ämne liknar att skapa en kö, enligt beskrivningen i föregående avsnitt. Sedan skickar du meddelanden med klassen [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient) . Om du vill ta emot meddelanden skapar du en eller flera prenumerationer för ämnet. Precis som i köer tas meddelanden emot från en prenumeration med hjälp av ett [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) -objekt i stället för ett [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) -objekt. Skapa prenumerations klienten, skicka namnet på ämnet, namnet på prenumerationen och (valfritt) Receive-läget som parametrar.

Ett fullständigt arbets exempel finns i BasicSendReceiveUsingTopicSubscriptionClient- [exemplet](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingTopicSubscriptionClient) på GitHub.

### <a name="rules-and-actions"></a>Regler och åtgärder

I många fall måste meddelanden med särskilda egenskaper bearbetas på olika sätt. Om du vill aktivera den här bearbetningen kan du konfigurera prenumerationer för att hitta meddelanden som har önskade egenskaper och sedan utföra vissa ändringar av dessa egenskaper. Medan Service Bus prenumerationer visar alla meddelanden som skickas till ämnet, kan du bara kopiera en delmängd av dessa meddelanden till kön för virtuella prenumerationer. Filtreringen utförs med hjälp av prenumerations filter. Sådana ändringar kallas *filter åtgärder*. När en prenumeration skapas kan du ange ett filter uttryck som fungerar på meddelandets egenskaper, både system egenskaper (till exempel **etikett**) och anpassade program egenskaper (till exempel **StoreName**.) SQL filter-uttrycket är valfritt i det här fallet. utan ett SQL-filter-uttryck utförs eventuella filter åtgärder som definierats för en prenumeration på alla meddelanden för den prenumerationen.

Ett fullständigt arbets exempel finns i TopicSubscriptionWithRuleOperationsSample- [exemplet](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample) på GitHub.

Mer information om möjliga filter värden finns i dokumentationen för klassen [SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) och [SqlRuleAction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction) .

## <a name="next-steps"></a>Nästa steg

Mer information och exempel på hur du använder Service Bus meddelanden finns i följande avancerade avsnitt:

* [Översikt över Service Bus meddelande hantering](service-bus-messaging-overview.md)
* [Snabbstart: Skicka och ta emot meddelanden med Azure-portalen och .NET](service-bus-quickstart-portal.md)
* [Självstudie: Uppdatera lagret med Azure-portalen och ämnen/prenumerationer](service-bus-tutorial-topics-subscriptions-portal.md)


