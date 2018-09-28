---
title: Översikt över Azure Service Bus-meddelanden för köer, ämnen och prenumerationer | Microsoft Docs
description: Översikt över Service Bus-meddelandeentiteter.
services: service-bus-messaging
documentationcenter: na
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/18/2018
ms.author: spelluru
ms.openlocfilehash: 047c4c37090db77f7a7a692604dd63c5effff9fa
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47409769"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Service Bus-köer, -ämnen och -prenumerationer

Microsoft Azure Service Bus stöder en uppsättning molnbaserade, meddelande inriktad mellanprogram teknik, som tillförlitlig message Queuing- och varaktiga Publicera/prenumerera på meddelanden. Dessa ”hanterade” meddelandefunktioner kan betraktas som frikopplade meddelandefunktioner som har stöd för Publicera / prenumerera, temporal Frikoppling och scenarier med hjälp av Service Bus messaging arbetsbelastningen för belastningsutjämning. Frikopplad kommunikation har många fördelar: klienter och servrar kan till exempel ansluta efter behov och utföra åtgärder på ett asynkront sätt.

Meddelandeentiteter som utgör kärnan i meddelandefunktionerna i Service Bus är köer, ämnen och prenumerationer och regler/åtgärder.

## <a name="queues"></a>Köer

Köer erbjuder *First In, först ut* (FIFO) meddelandeleverans till en eller flera konkurrerande konsumenter. Det vill säga mottagare vanligtvis tar emot och bearbetar meddelanden i den ordning som de har lagts till i kön, och endast en meddelandekonsument tar emot och bearbetar varje meddelande. En stor fördel med köer är att uppnå ”temporal Frikoppling” av programkomponenter. Med andra ord behöver producenter (avsändare) och konsumenter (mottagare) inte skicka och ta emot meddelanden på samma gång, eftersom meddelanden lagras varaktigt i kön. Dessutom behöver producenten inte vänta tills ett svar från konsumenten för att kunna fortsätta bearbetningen och skicka meddelanden.

En relaterad fördel är ”belastningsutjämning”, vilket gör att producenter och konsumenter kan skicka och ta emot meddelanden med olika hastigheter. I många program varierar systembelastningen med tiden. men är den bearbetningstid som krävs för varje arbetsenhet vanligtvis konstant. Medlingen meddelandeproducenter och konsumenter med hjälp av en kö innebär att den konsumerande appen endast har konfigureras för att kunna hantera genomsnittlig belastning i stället för hög belastning. Köns djup växer och dras samman allt eftersom den inkommande belastningen varierar. Den här funktionen sparar direkt pengar avseende mängden infrastruktur som krävs för att underhålla programbelastningen. När belastningen ökar kan fler arbetsprocesser läggas till att läsa från kön. Varje meddelande bearbetas bara av en av arbetsprocesserna. Dessutom kan den här pull-baserade belastningsutjämning, för optimal användning av worker-datorer även om worker-datorer skiljer sig åt vad gäller processorkraft, eftersom de hämta meddelanden med den högsta överföringshastighet. Det här mönstret kallas ofta för ett ”konkurrerande konsument”-mönster.

Med hjälp av köer till mellanliggande mellan meddelandeproducenter och -konsumenter innehåller en inbyggd lösa kopplingar mellan komponenterna. Eftersom producenter och konsumenter inte är medvetna om varandra, kan en konsument uppgraderas utan någon effekt på producenten.

### <a name="create-queues"></a>Skapa köer

Du skapar köer med hjälp av den [Azure-portalen](service-bus-quickstart-portal.md), [PowerShell](service-bus-quickstart-powershell.md), [CLI](service-bus-quickstart-cli.md), eller [Resource Manager-mallar](service-bus-resource-manager-namespace-queue.md). Du sedan skicka och ta emot meddelanden med en [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) objekt. 

Läs snabbt hur du skapar en kö, och sedan skicka och ta emot meddelanden till och från kön i den [snabbstarter](service-bus-quickstart-portal.md) för varje metod. En mer detaljerad genomgång om hur du använder köer kan se [Kom igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md). 

Ett fungerande exempel finns i [BasicSendReceiveUsingQueueClient exempel](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient) på GitHub.

### <a name="receive-modes"></a>Ta emot lägen

Du kan ange två olika lägen där Service Bus tar emot meddelanden: *ReceiveAndDelete* eller *PeekLock*. I den [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) läge, Mottagningsåtgärden är inleveransen, det vill säga när Service Bus tar emot begäran, den markerar meddelandet som Förbrukat och tillbaka till programmet. **ReceiveAndDelete** läge är den enklaste modellen och fungerar bäst för scenarier där programmet kan tolerera icke-bearbetning av ett meddelande om ett fel inträffar. Tänk dig ett scenario där konsumenten utfärdar en receive-begäran och sedan kraschar innan bearbetningen för att förstå det här scenariot. Eftersom Service Bus markerar meddelandet som Förbrukat när programmet startas om och börjar förbruka meddelanden igen, kommer den ha missat meddelandet som förbrukades innan kraschen.

I [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) läge, Mottagningsåtgärden blir två steg, vilket gör det möjligt att stödprogram som inte tolererar att saknas. När Service Bus tar emot begäran, det upp nästa meddelande som ska förbrukas, låser det för att förhindra att andra användare från att ta emot den och tillbaka det till programmet. När programmet har slutfört behandlingen av meddelandet (eller lagrar den på ett tillförlitligt sätt för framtida bearbetning), den är klar det andra steget i processen genom att anropa [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) för det mottagna meddelandet. När Service Bus ser den **CompleteAsync** anrop, markerar den meddelandet som Förbrukat.

Om programmet inte kan bearbeta meddelandet av någon anledning, kan det anropa den [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) metod för det mottagna meddelandet (i stället för [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync)). Den här metoden gör det möjligt för Service Bus låser upp meddelandet och gör det tillgängligt att tas emot igen, antingen av samma konsument eller av en annan konkurrerande konsument. Det finns en tidsgräns som är associerade med låset och om programmet inte kan bearbeta meddelandet innan timeout för lås går ut (till exempel om programmet kraschar), kommer Service Bus låser upp meddelandet och gör det tillgängligt och kan tas emot igen () för det andra i stort sett utför en [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) åtgärden som standard).

I händelse av att programmet kraschar efter behandlingen av meddelandet, men innan de **CompleteAsync** begäran har utfärdats, meddelandet once till programmet när den startas om. Den här processen kallas ofta *minst när* bearbetning, det vill säga varje meddelande bearbetas minst en gång. I vissa situationer kan dock samma meddelande levereras. Om scenariot inte tolererar duplicerad bearbetning, och sedan ytterligare logik krävs i programmet för att upptäcka dubbletter, som kan uppnås utifrån den [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) egenskapen för meddelandet, vilket är konstant över leveransförsök. Den här funktionen kallas *exakt en gång* bearbetning.

## <a name="topics-and-subscriptions"></a>Ämnen och prenumerationer

Till skillnad från köer, där varje meddelande bearbetas av en enskild konsument *ämnen* och *prenumerationer* tillhandahålla en en-till-många-kommunikation i en *förpublicering/prenumeration* mönster. Användbart för att skala till flera olika mottagare, varje publicerat meddelande görs tillgänglig för varje prenumeration som har registrerats med ämnet. Meddelanden skickas till ett ämne och levereras till en eller flera associerade prenumerationer, beroende på filterregler som kan ställas in på basis av per prenumeration. Prenumerationer kan använda ytterligare filter för att begränsa de meddelanden som de vill ha. Meddelanden skickas till ett ämne på samma sätt som de skickas till en kö, men meddelanden tas inte emot från ämnet direkt. I stället tas de emot från prenumerationer. En ämnesprenumeration liknar en virtuell kö som tar emot kopior av meddelanden som skickas till ämnet. Meddelanden tas emot från en prenumeration identiskt sätt som de tas emot från en kö.

Skicka meddelande funktionerna i en kö mappas direkt till ett ämne genom jämförelse och dess funktioner för meddelande-tar emot mappar till en prenumeration. Bland annat kan den här funktionen innebär att prenumerationer stöder samma mönster som beskrivs i det här avsnittet avseende köer: konkurrerande konsument, temporal Frikoppling, belastningsutjämning och belastningsutjämning.

### <a name="create-topics-and-subscriptions"></a>Skapa ämnen och prenumerationer

Skapa ett ämne liknar skapar en kö, enligt beskrivningen i föregående avsnitt. Du sedan skickar meddelanden med den [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient) klass. Om du vill ta emot meddelanden, skapar du en eller flera prenumerationer till ämnet. Liknar köer, meddelanden tas emot från en prenumeration med hjälp av en [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) objekt i stället för en [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) objekt. Skapa prenumerationsklient, skicka namnet på ämnet, namnet på prenumerationen och (frivilligt) receive-läge som parametrar. 

För en fullständig fungerar exemplet finns i den [BasicSendReceiveUsingTopicSubscriptionClient exempel](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingTopicSubscriptionClient) på Github.

### <a name="rules-and-actions"></a>Regler och åtgärder

Meddelanden som har specifika egenskaper måste bearbetas på olika sätt i många scenarier. Om du vill aktivera den här bearbetningen kan du konfigurera prenumerationer för att hitta meddelanden som har önskade egenskaper och utför vissa ändringar av dessa egenskaper. Du kan bara kopiera en delmängd av de här meddelandena till virtuella prenumerationskön när Service Bus-prenumerationer visas alla meddelanden som skickas till ämnet. Den här filtreringen åstadkoms med hjälp av prenumerationsfilter. Sådana ändringar kallas *filteråtgärder*. När en prenumeration har skapats kan du ange ett filteruttryck som körs med egenskaperna för meddelandet, både systemegenskaperna (till exempel **etikett**) och egenskaper för anpassade program (till exempel  **StoreName**.) Filteruttrycket SQL är valfritt i det här fallet; utan ett SQL-filteruttryck utförs något filter som definieras för en prenumeration på alla meddelanden för den aktuella prenumerationen.

För en fullständig fungerar exemplet finns i den [TopicSubscriptionWithRuleOperationsSample exempel](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample) på GitHub.

Mer information om möjliga filtervärden finns i dokumentationen för den [SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) och [SqlRuleAction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction) klasser. 

## <a name="next-steps"></a>Nästa steg

Mer information och exempel på hur du använder Service Bus-meddelanden finns i följande avancerade ämnen:

* [Översikt över Service Bus-meddelandetjänster](service-bus-messaging-overview.md)
* [Snabbstart: Skicka och ta emot meddelanden med Azure-portalen och .NET](service-bus-quickstart-portal.md)
* [Självstudier: Uppdatera lagret med hjälp av Azure-portalen och ämnen/prenumerationer](service-bus-tutorial-topics-subscriptions-portal.md)


