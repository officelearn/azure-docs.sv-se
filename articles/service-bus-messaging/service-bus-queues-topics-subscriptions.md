---
title: Azure Service Bus-meddelanden - köer, ämnen och prenumerationer
description: Den här artikeln innehåller en översikt över Azure Service Bus-meddelandeentiteter (kö, ämnen och prenumerationer).
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/16/2020
ms.author: aschhab
ms.openlocfilehash: 3dc78a22e0e596d812d90fec63475a0b21e9164f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259518"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Service Bus-köer, -ämnen och -prenumerationer

Microsoft Azure Service Bus stöder en uppsättning molnbaserade, meddelandeorienterade mellanprogram, inklusive tillförlitlig meddelandekö och varaktiga publicerings-/prenumerationsmeddelanden. Dessa "förmedlade" meddelandefunktioner kan ses som frikopplade meddelandefunktioner som stöder publicerings-prenumerera, tidsmässig frikoppling och belastningsutjämning scenarier med hjälp av Service Bus meddelandearbetsbelastning. Frikopplad kommunikation har många fördelar: klienter och servrar kan till exempel ansluta efter behov och utföra åtgärder på ett asynkront sätt.

Meddelandeentiteterna som utgör kärnan i meddelandefunktionerna i Service Bus är köer, ämnen och prenumerationer och regler/åtgärder.

## <a name="queues"></a>Köer

Köer erbjuder fifo-meddelandeleverans *för första in, först ut (First Out)* till en eller flera konkurrerande konsumenter. Det innebär att mottagare vanligtvis tar emot och bearbetar meddelanden i den ordning de lades till i kön, och endast ett meddelande som konsumenten tar emot och bearbetar varje meddelande. En viktig fördel med att använda köer är att uppnå "tidsmässig frikoppling" av applikationskomponenter. Med andra ord behöver producenterna (avsändare) och konsumenter (mottagare) inte skicka och ta emot meddelanden samtidigt, eftersom meddelanden lagras på ett sätt i kön. Dessutom behöver tillverkaren inte vänta på ett svar från konsumenten för att kunna fortsätta att behandla och skicka meddelanden.

En relaterad fördel är "belastningsutjämning", som gör det möjligt för producenter och konsumenter att skicka och ta emot meddelanden i olika takt. I många program varierar systembelastningen över tiden. Den bearbetningstid som krävs för varje arbetsenhet är dock vanligtvis konstant. Att förmedla meddelandeproducenter och konsumenter med kö innebär att det tidskrävande programmet bara behöver etableras för att kunna hantera genomsnittlig belastning i stället för toppbelastning. Köns djup växer och dras samman allt eftersom den inkommande belastningen varierar. Den här funktionen sparar direkt pengar när det gäller mängden infrastruktur som krävs för att serva programbelastningen. När belastningen ökar kan fler arbetsprocesser läggas till för att läsa från kön. Varje meddelande bearbetas bara av en av arbetsprocesserna. Dessutom möjliggör denna pull-baserade belastningsutjämning optimal användning av arbetsdatorerna även om arbetsdatorerna skiljer sig åt när det gäller processorkraft, eftersom de hämtar meddelanden med sin egen högsta hastighet. Detta mönster kallas ofta "konkurrerande konsument" mönster.

Att använda köer till mellanliggande mellan meddelandeproducenter och konsumenter ger en inneboende lös koppling mellan komponenterna. Eftersom producenter och konsumenter inte känner till varandra kan en konsument uppgraderas utan att påverka tillverkaren.

### <a name="create-queues"></a>Skapa köer

Du skapar köer med [azure-portalen,](service-bus-quickstart-portal.md) [PowerShell,](service-bus-quickstart-powershell.md) [CLI](service-bus-quickstart-cli.md)eller [Resource Manager.](service-bus-resource-manager-namespace-queue.md) Du skickar och ta emot meddelanden med ett [QueueClient-objekt.](/dotnet/api/microsoft.azure.servicebus.queueclient)

Information om hur du skapar en kö och sedan skickar och tar emot meddelanden till och från kön finns i [snabbstarten](service-bus-quickstart-portal.md) för varje metod. En mer djupgående självstudiekurs om hur du använder köer finns i [Komma igång med servicebussköer](service-bus-dotnet-get-started-with-queues.md).

Ett arbetsexempel finns i [exemplet BasicSendReceiveUsingQueueClient](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient) på GitHub.

### <a name="receive-modes"></a>Ta emot lägen

Du kan ange två olika lägen där Service Bus tar emot meddelanden: *ReceiveAndDelete* eller *PeekLock*. I läget [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) är mottagningsåtgärden enkelbild. Det vill än när Service Bus tar emot begäran från konsumenten, markerar det meddelandet som förbrukat och returnerar det till konsumentprogrammet. **ReceiveAndDelete-läget** är den enklaste modellen och fungerar bäst för scenarier där programmet kan tolerera att inte bearbeta ett meddelande om ett fel inträffar. Om du vill förstå det här scenariot bör du tänka på ett scenario där konsumenten utfärdar mottagningsbegäran och sedan kraschar innan den bearbetas. Eftersom Service Bus markerar meddelandet som förbrukat, när programmet startas om och börjar konsumera meddelanden igen, kommer det att ha missat meddelandet som förbrukades före kraschen.

I [PeekLock-läget](/dotnet/api/microsoft.azure.servicebus.receivemode) blir mottagningsåtgärden tvåstegs, vilket gör det möjligt att stödja program som inte tolererar meddelanden som saknas. När Service Bus tar emot begäran hittar den nästa meddelande som ska förbrukas, låser den för att förhindra att andra konsumenter tar emot den och returnerar den sedan till programmet. När programmet har slutfört bearbetningen av meddelandet (eller lagrar det på ett tillförlitligt sätt för framtida bearbetning) slutförs den andra fasen av mottagningsprocessen genom att anropa [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) på det mottagna meddelandet. När Service Bus ser **CompleteAsync-anropet** markeras meddelandet som förbrukat.

Om programmet inte kan bearbeta meddelandet av någon anledning kan det anropa [Metoden AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) i det mottagna meddelandet (i stället för [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync)). Med den här metoden kan Service Bus låsa upp meddelandet och göra det tillgängligt för att tas emot igen, antingen av samma konsument eller av en annan konkurrerande konsument. För det andra finns det en timeout som är associerad med låset och om programmet inte kan bearbeta meddelandet innan låstidsgränsen upphör att gälla (till exempel om programmet kraschar), låser Service Bus upp meddelandet och gör det tillgängligt för att tas emot igen (i huvudsak utför en [AbandonAsync-åtgärd](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) som standard).

I händelse av att programmet kraschar efter bearbetning av meddelandet, men innan **CompleteAsync-begäran** utfärdas, levereras meddelandet till programmet när det startas om. Denna process kallas ofta *minst en gång* bearbetning; det vill än, bearbetas varje meddelande minst en gång. I vissa situationer kan dock samma meddelande levereras på nytt. Om scenariot inte kan tolerera dubblettbearbetning krävs ytterligare logik i programmet för att identifiera dubbletter, vilket kan uppnås baserat på [messageid-egenskapen Message,](/dotnet/api/microsoft.azure.servicebus.message.messageid) som förblir konstant över leveransförsök. Den här funktionen kallas *exakt en gång* bearbetning.

## <a name="topics-and-subscriptions"></a>Ämnen och prenumerationer

I motsats till köer, där varje meddelande bearbetas av en enda konsument, *ämnen* och *prenumerationer* ger en en-till-många form av kommunikation, i en *publicera / prenumerera* mönster. Användbart för skalning till ett stort antal mottagare, varje publicerat meddelande görs tillgängligt för varje prenumeration som registrerats med ämnet. Meddelanden skickas till ett ämne och levereras till en eller flera associerade prenumerationer, beroende på filterregler som kan ställas in per prenumeration. Prenumerationerna kan använda ytterligare filter för att begränsa de meddelanden som de vill ta emot. Meddelanden skickas till ett ämne på samma sätt som de skickas till en kö, men meddelanden tas inte emot direkt från ämnet. I stället tas de emot från prenumerationer. En ämnesprenumeration liknar en virtuell kö som tar emot kopior av de meddelanden som skickas till ämnet. Meddelanden tas emot från en prenumeration på samma sätt som de tas emot från en kö.

Som jämförelse mappas funktionen för meddelandesändning av en kö direkt till ett ämne och dess meddelandemottagande funktioner mappas till en prenumeration. Den här funktionen innebär bland annat att prenumerationer stöder samma mönster som beskrivits tidigare i det här avsnittet när det gäller köer: konkurrerande konsumenter, tidsmässig frikoppling, belastningsutjämning och belastningsutjämning.

### <a name="create-topics-and-subscriptions"></a>Skapa ämnen och prenumerationer

Att skapa ett ämne påminner om att skapa en kö, enligt beskrivningen i föregående avsnitt. Du skickar sedan meddelanden med klassen [TopicClient.](/dotnet/api/microsoft.azure.servicebus.topicclient) Om du vill ta emot meddelanden skapar du en eller flera prenumerationer på ämnet. På samma sätt som köer tas meddelanden emot från en prenumeration med ett [SubscriptionClient-objekt](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) i stället för ett [QueueClient-objekt.](/dotnet/api/microsoft.azure.servicebus.queueclient) Skapa prenumerationsklienten, skicka namnet på ämnet, namnet på prenumerationen och (eventuellt) mottagningsläget som parametrar.

Ett fullständigt arbetsexempel finns i [exemplet BasicSendReceiveUsingTopicSubscriptionClient](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingTopicSubscriptionClient) på GitHub.

### <a name="rules-and-actions"></a>Regler och åtgärder

I många fall måste meddelanden som har specifika egenskaper bearbetas på olika sätt. Om du vill aktivera den här bearbetningen kan du konfigurera prenumerationer för att hitta meddelanden som har önskade egenskaper och sedan utföra vissa ändringar av dessa egenskaper. Service Bus-prenumerationer ser alla meddelanden som skickas till ämnet, men du kan bara kopiera en delmängd av dessa meddelanden till den virtuella prenumerationskön. Den här filtrningen utförs med hjälp av prenumerationsfilter. Sådana ändringar kallas *filteråtgärder*. När en prenumeration skapas kan du ange ett filteruttryck som fungerar på egenskaperna för meddelandet, både systemegenskaperna (till exempel **Etikett)** och anpassade programegenskaper (till exempel **StoreName**.) SQL-filteruttrycket är valfritt i det här fallet. Utan ett SQL-filteruttryck utförs alla filteråtgärder som definierats på en prenumeration på alla meddelanden för den prenumerationen.

Ett fullständigt arbetsexempel finns i [exemplet TopicSubscriptionWithRuleOperationsSample](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample) på GitHub.

Mer information om möjliga filtervärden finns i dokumentationen för [klasserna SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) och [SqlRuleAction.](/dotnet/api/microsoft.azure.servicebus.sqlruleaction)

## <a name="next-steps"></a>Nästa steg

Mer information och exempel på hur du använder Service Bus-meddelanden finns i följande avancerade avsnitt:

* [Översikt över servicebussmeddelanden](service-bus-messaging-overview.md)
* [Snabbstart: Skicka och ta emot meddelanden med Azure-portalen och .NET](service-bus-quickstart-portal.md)
* [Självstudie: Uppdatera lagret med Azure-portalen och ämnen/prenumerationer](service-bus-tutorial-topics-subscriptions-portal.md)


