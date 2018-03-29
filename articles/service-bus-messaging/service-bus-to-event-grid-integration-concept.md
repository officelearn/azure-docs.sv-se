---
title: Översikt över integration av Azure Service Bus till Event Grid | Microsoft Docs
description: Beskrivning av Service Bus-meddelanden och Event Grid-integration
services: service-bus-messaging
documentationcenter: .net
author: ChristianWolf42
manager: timlt
editor: ''
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 02/15/2018
ms.author: chwolf
ms.openlocfilehash: e0c32510ee49b95bc3606ea1efff7e2a6f72799b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="azure-service-bus-to-event-grid-integration-overview"></a>Översikt över integration av Azure Service Bus till Event Grid

Azure Service Bus har en ny integration till Azure Event Grid. Detta möjliggör scenarier där Service Bus-köer eller prenumerationer med en låg volym av meddelanden inte behöver ha någon mottagare som kontinuerligt söker efter meddelanden. 

Nu kan Service Bus generera händelser till Event Grid när det finns meddelanden i en kö eller prenumeration utan att det finns några mottagare. Du kan skapa Event Grid-prenumerationer till Service Bus-namnområden, lyssna på dessa händelser och sedan reagera på dem genom att starta en mottagare. Med den här funktionen kan du använda Service Bus i reaktiva programmeringsmodeller.

Om du vill aktivera funktionen behöver du följande:

* Ett Service Bus Premium-namnområde med minst en Service Bus-kö eller ett Service Bus-ämne med minst en prenumeration.
* Deltagaråtkomst till Service Bus-namnområdet.
* Dessutom behöver du en prenumeration på Event Grid för Service Bus-namnområdet. Den här prenumerationen får ett meddelande från Event Grid om det finns meddelanden som ska hämtas. Vanliga prenumeranter kan vara Logic Apps-funktionen i Azure App Service, Azure Functions eller en webhook som kontaktar en webbapp. Prenumeranten bearbetar sedan dessa meddelanden. 

![19][]

### <a name="verify-that-you-have-contributor-access"></a>Kontrollera att du har deltagarbehörighet

Gå till ditt Service Bus-namnområde och välj **Åtkomstkontroll (IAM)** enligt nedan:

![1][]

### <a name="events-and-event-schemas"></a>Händelser och händelsescheman

Service Bus skickar händelser för två scenarier:

* [ActiveMessagesWithNoListenersAvailable](#active-messages-available-event)
* [DeadletterMessagesAvailable](#dead-lettered-messages-available-event)

Dessutom använder Service Bus standardsäkerhet för Event Grid och [autentiseringsmekanismer](https://docs.microsoft.com/en-us/azure/event-grid/security-authentication).

Mer information finns i [Azure Event Grid event schemas](https://docs.microsoft.com/en-us/azure/event-grid/event-schema) (Händelsescheman i Azure Event Grid).

#### <a name="active-messages-available-event"></a>Aktiva meddelanden, tillgänglig händelse

Den här händelsen genereras om det finns aktiva meddelanden i en kö eller prenumeration och inga mottagare lyssnar.

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

#### <a name="dead-letter-messages-available-event"></a>Obeställbara meddelanden, tillgänglig händelse

Du kan hämta minst en händelse per kö för obeställbara meddelanden, som innehåller meddelanden utan aktiva mottagare.

Schemat för den här händelsen ser ut så här:

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

### <a name="how-many-events-are-emitted-and-how-often"></a>Hur många händelser genereras, och hur ofta?

Om du har flera köer och ämnen eller prenumerationer i namnområdet, får du minst en händelse per kö och en per prenumeration. Händelserna genereras omedelbart om det inte finns några meddelanden i Service Bus-entiteten och ett nytt meddelande anländer. Eller så genereras händelserna varannan minut, om inte Service Bus identifierar en aktiv mottagare. Bläddring bland meddelanden avbryter inte händelserna.

Som standard genererar Service Bus händelser för alla entiteter i namnområdet. Om du enbart vill hämta händelser för specifika entiteter kan du läsa mer i nästa avsnitt.

### <a name="use-filters-to-limit-where-you-get-events-from"></a>Använda filter för att begränsa var du hämtar händelser från

Om du enbart vill hämta händelser från exempelvis en kö eller en prenumeration inom namnområdet, kan du använda filtren *Börjar med* eller *Slutar med* som finns i Event Grid. I vissa gränssnitt kallas filtren *För* och *Suffix*. Om du vill hämta händelser för flera, men inte alla, köer och prenumerationer, kan du skapa flera Event Grid-prenumerationer och ange ett filter för varje.

## <a name="create-event-grid-subscriptions-for-service-bus-namespaces"></a>Skapa Event Grid-prenumerationer för Service Bus-namnområden

Du kan skapa Event Grid-prenumerationer för Service Bus-namnområden på tre sätt:

* I [Azure Portal](#portal-instructions)
* I [Azure CLI](#azure-cli-instructions)
* I [PowerShell](#powershell-instructions)

## <a name="azure-portal-instructions"></a>Instruktioner för Azure Portal

Så här skapar du en ny Event Grid-prenumeration:
1. Gå till ditt namnområde i Azure Portal.
2. Välj **Event Grid** i rutan till vänster. 
3. Välj **Händelseprenumeration**.  

   Följande bild visar ett namnområde som har några Event Grid-prenumerationer:

   ![20][]

   Följande bild visar hur du prenumererar på en funktion eller en webhook utan någon specifik filtrering:

   ![21][]

## <a name="azure-cli-instructions"></a>Azure CLI-instruktioner

Kontrollera först att du har Azure CLI version 2.0 eller senare installerad. [Ladda ned installationsprogrammet](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest). Välj **Windows + X** och öppna sedan en ny PowerShell-konsol med administratörsbehörighet. Du kan också använda en kommandotolk i Azure Portal.

Kör följande kod:

 ```azurecli-interactive
az login

az account set -s “THE SUBSCRIPTION YOU WANT TO USE”

$namespaceid=(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name “<yourNamespace>“--resource-group “<Your Resource Group Name>” --query id --output tsv)

az eventgrid event-subscription create --resource-id $namespaceid --name “<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>” --endpoint “<your_function_url>” --subject-ends-with “<YOUR SERVICE BUS SUBSCRIPTION NAME>”
```

## <a name="powershell-instructions"></a>PowerShell-instruktioner

Kontrollera att du har Azure PowerShell installerat. [Ladda ned installationsprogrammet](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-5.4.0). Välj **Windows + X** och öppna sedan en ny PowerShell-konsol med administratörsbehörighet. Du kan också använda en kommandotolk i Azure Portal.

```PowerShell-interactive
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

* Hämta Service Bus- och Event Grid-[exempel](service-bus-to-event-grid-integration-example.md).
* Läs mer om [Event Grid](https://docs.microsoft.com/en-us/azure/azure-functions/).
* Läs mer om [Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/).
* Läs mer om [Logic Apps](https://docs.microsoft.com/en-us/azure/logic-apps/).
* Läs mer om [Service Bus](https://docs.microsoft.com/en-us/azure/azure-functions/).

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[19]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
