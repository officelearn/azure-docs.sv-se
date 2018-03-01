---
title: "Översikt över integration av Azure Service Bus till Event Grid | Microsoft Docs"
description: Beskrivning av Service Bus-meddelanden och Event Grid-integration
services: service-bus-messaging
documentationcenter: .net
author: ChristianWolf42
manager: timlt
editor: 
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 02/15/2018
ms.author: chwolf
ms.openlocfilehash: bf771428505081cb60ca4417f87a4f6c2afbd25d
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="azure-service-bus-to-azure-event-grid-integration-overview"></a>Översikt över integration av Azure Service Bus till Azure Event Grid

Azure Service Bus startar en ny integration till Azure Event Grid. Det viktiga scenario som funktionen aktiverar är att Service Bus-köer eller prenumerationer som har en låg volym av meddelanden, inte behöver ha någon mottagare som söker efter meddelanden vid alla tidpunkter. Service Bus kan nu generera händelser till Azure Event Grid när det finns meddelanden i en kö eller prenumeration utan att det finns några mottagare. Du kan skapa Azure Event Grid-prenumerationer till Service Bus-namnområden, lyssna på dessa händelser och reagera på dem genom att starta en mottagare. Med den här funktionen kan Service Bus användas i reaktiva programmeringsmodeller.

Om du vill aktivera funktionen behöver du följande:

* Ett Azure Service Bus Premium-namnområde med minst en Service Bus-kö eller ett Service Bus-ämne med minst en prenumeration.
* Deltagaråtkomst till Azure Service Bus-namnområdet.
* Dessutom behöver du en prenumeration på Azure Event Grid för Service Bus-namnområdet. Den här prenumerationen får meddelandet från Azure Event Grid om det finns meddelanden som ska hämtas. Vanliga prenumeranter kan vara Logic Apps, Azure Functions eller en webhook som kontaktar en webbapp, som i sin tur bearbetar meddelanden. 

![19][]

### <a name="verify-that-you-have-contributor-access"></a>Kontrollera att du har deltagarbehörighet

Gå till ditt Service Bus-namnområde och välj ”Åtkomstkontroll (IAM)” enligt nedan:

![1][]

### <a name="events-and-event-schemas"></a>Händelser och händelsescheman

Azure Service Bus skickar händelser för två scenarier i dag.

* [ActiveMessagesWithNoListenersAvailable](#active-messages-available-event)
* [DeadletterMessagesAvailable](#dead-lettered-messages-available-event)

Dessutom använder den standardsäkerhet för Azure Event Grid och [autentiseringsmekanismer](https://docs.microsoft.com/en-us/azure/event-grid/security-authentication).

Om du vill ha mer information om händelsescheman i Event Grid kan du följa [denna](https://docs.microsoft.com/en-us/azure/event-grid/event-schema) länk.

#### <a name="active-messages-available-event"></a>Aktiva meddelanden, tillgänglig händelse

Denna händelse genereras om det finns aktiva meddelanden i en kö eller prenumeration och inga mottagare lyssnar.

Schemat för den här händelsen är följande:

```JSON
{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}
```

#### <a name="dead-lettered-messages-available-event"></a>Obeställbara meddelanden, tillgänglig händelse

Du kan hämta minst en händelse per kö för obeställbara meddelanden, som innehåller meddelanden utan aktiva mottagare.

Schemat för den här händelsen är följande:

```JSON
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="how-often-and-how-many-events-are-emitted"></a>Hur ofta och hur många händelser genereras?

Om du har flera köer och ämnen/prenumerationer i namnområdet, får du minst en händelse per kö och en per prenumeration. Händelserna genereras omedelbart om det inte finns några meddelanden i Service Bus-entiteten och ett nytt meddelande anländer, eller varannan minut såvida inte Azure Service Bus upptäcker en aktiv mottagare. Bläddring bland meddelanden avbryter inte händelserna.

Som standard genererar Azure Service Bus händelser för alla entiteter i namnområdet. Om du enbart vill hämta händelser för specifika entiteter kan du läsa mer i följande filtreringsavsnitt.

### <a name="filtering-limiting-from-where-you-get-events"></a>Filtrering, begränsa var du kan hämta händelser ifrån

Om du enbart vill hämta händelser till exempelvis en kö eller en prenumeration inom namnområdet, kan du använda filtren ”Börja med” eller ”Slutar med” som finns i Azure Event Grid. I vissa gränssnitt kallas filtren ”För” och ”Suffix”. Om du vill hämta händelser för flera men inte alla köer och prenumerationer, kan du skapa flera olika Azure Event Grid-prenumerationer och ange ett filter för varje.

## <a name="how-to-create-azure-event-grid-subscriptions-for-service-bus-namespaces"></a>Skapa Azure Event Grid-prenumerationer för Service Bus-namnområden

Det finns tre olika metoder att skapa Event Grid-prenumerationer för Service Bus-namnområden.

* [Azure Portal](#portal-instructions)
* [Azure CLI](#azure-cli-instructions)
* [PowerShell](#powershell-instructions)

## <a name="portal-instructions"></a>Portalinstruktioner

Gå till namnområdet i Azure-portalen för att skapa en ny Azure Event Grid-prenumeration och välj bladet Event Grid. Klicka på ”+ Händelseprenumeration” nedan för att visa ett namnområde som redan innehåller några Event Grid-prenumerationer.

![20][]

Följande skärmbild visar ett exempel på hur du prenumererar på en Azure-funktion eller en webhook utan någon specifik filtrering:

![21][]

## <a name="azure-cli-instructions"></a>Azure CLI-instruktioner

Kontrollera först att du har minst Azure CLI version 2.0 installerat. Du kan hämta installationsprogrammet här. Tryck sedan på ”Windows + X” och öppna en ny PowerShell-konsol med administratörsbehörighet. Du kan också använda en kommandotolk i Azure-portalen.

Kör följande kod:

```PowerShell
Az login

Aa account set -s “THE SUBSCRIPTION YOU WANT TO USE”

$namespaceid=(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name “<yourNamespace>“--resource-group “<Your Resource Group Name>” --query id --output tsv)

az eventgrid event-subscription create --resource-id $namespaceid --name “<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>” --endpoint “<your_function_url>” --subject-ends-with “<YOUR SERVICE BUS SUBSCRIPTION NAME>”
```

## <a name="powershell-instructions"></a>PowerShell-instruktioner

Kontrollera att du har Azure PowerShell installerat. Du hittar den här. Tryck sedan på ”Windows + X” och öppna en ny PowerShell-konsol med administratörsbehörighet. Du kan också använda en kommandotolk i Azure-portalen.

```PowerShell
Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionName "<YOUR SUBSCRIPTION NAME>"

# This might be installed already
Install-Module AzureRM.ServiceBus

$NSID = (Get-AzureRmServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -Na
mespaceName "<YOUR NAMESPACE NAME>").Id 

New-AzureRmEVentGridSubscription -EventSubscriptionName “<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>” -ResourceId $NSID -Endpoint "<YOUR FUNCTION URL>” -SubjectEndsWith “<YOUR SERVICE BUS SUBSCRIPTION NAME>”
```

Härifrån kan du utforska andra installationsalternativ eller [testa händelser som flödar in](#test-that-events-are-flowing).

## <a name="next-steps"></a>Nästa steg

* Service Bus- och Event Grid-[exempel](service-bus-to-event-grid-integration-example.md).
* Läs mer om [Azure Event Grid](https://docs.microsoft.com/en-us/azure/azure-functions/).
* Läs mer om [Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/).
* Läs mer om [Azure Logic Apps](https://docs.microsoft.com/en-us/azure/logic-apps/).
* Läs mer om [Azure Service Bus](https://docs.microsoft.com/en-us/azure/azure-functions/).

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[19]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png