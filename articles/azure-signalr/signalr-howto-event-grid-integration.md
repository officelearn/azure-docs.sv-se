---
title: Så här skickar du Azure SignalR-tjänsthändelser till Event Grid
description: En guide som visar hur du aktiverar händelserutnätshändelser för din SignalR-tjänst och skickar sedan klientanslutningsanslutna/frånkopplade händelser till ett exempelprogram.
services: signalr
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: chenyl
ms.openlocfilehash: a76c9aaabf984723e2b60a7cd42425c9b29c916a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76710827"
---
# <a name="how-to-send-events-from-azure-signalr-service-to-event-grid"></a>Skicka händelser från Azure SignalR Service till Event Grid

Azure Event Grid är en fullständigt hanterad händelseroutningstjänst som tillhandahåller enhetlig händelseförbrukning med hjälp av en pub-undermodell. I den här guiden använder du Azure CLI för att skapa en Azure SignalR-tjänst, prenumerera på anslutningshändelser och distribuera sedan ett exempelwebbprogram för att ta emot händelserna. Slutligen kan du ansluta och koppla från och se händelsenyttolasten i exempelprogrammet.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt][azure-account] konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure CLI-kommandona i den här artikeln är formaterade för **Bash-skalet.** Om du använder ett annat skal som PowerShell eller Kommandotolk kan du behöva justera radförekommande tecken eller variabla tilldelningsrader därefter. I den här artikeln används variabler för att minimera mängden kommandoredigering som krävs.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk behållare där du distribuerar och hanterar dina Azure-resurser. Följande kommando [för az-grupp skapa][az-group-create] skapar en resursgrupp med namnet *myResourceGroup* i *regionen eastus.* Om du vill använda ett annat namn `RESOURCE_GROUP_NAME` för resursgruppen anger du ett annat värde.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-signalr-service"></a>Skapa en Azure SignalR Service

Distribuera sedan en Azure Signalr-tjänst till resursgruppen med följande kommandon.
```azurecli-interactive
SIGNALR_NAME=SignalRTestSvc

az signalr create --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --sku Free_F1
```

När SignalR-tjänsten har skapats returnerar Azure CLI utdata som liknar följande:

```json
{
  "externalIp": "13.76.156.152",
  "hostName": "clitest.servicedev.signalr.net",
  "hostNamePrefix": "clitest",
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/clitest1/providers/Microsoft.SignalRService/SignalR/clitest",
  "location": "southeastasia",
  "name": "clitest",
  "provisioningState": "Succeeded",
  "publicPort": 443,
  "resourceGroup": "clitest1",
  "serverPort": 443,
  "sku": {
    "capacity": 1,
    "family": null,
    "name": "Free_F1",
    "size": "F1",
    "tier": "Free"
  },
  "tags": null,
  "type": "Microsoft.SignalRService/SignalR",
  "version": "1.0"
}

```

## <a name="create-an-event-endpoint"></a>Skapa en slutpunkt för händelser

I det här avsnittet använder du en Resource Manager-mall som finns i en GitHub-databas för att distribuera ett förbyggt exempelwebbprogram till Azure App Service. Senare prenumererar du på registrets Event Grid-händelser och anger den här appen som den slutpunkt som händelserna skickas till.

Om du vill distribuera `SITE_NAME` exempelappen anger du ett unikt namn för webbappen och kör följande kommandon. Webbplatsnamnet måste vara unikt i Azure eftersom det utgör en del av det fullständigt kvalificerade domännamnet (FQDN) för webbappen. I ett senare avsnitt navigerar du till appens FQDN i en webbläsare för att visa registrets händelser.

```azurecli-interactive
SITE_NAME=<your-site-name>

az group deployment create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

När distributionen lyckas (det kan ta några minuter) öppnar du en webbläsare och navigerar till din webbapp för att se till att den körs:

`http://<your-site-name>.azurewebsites.net`

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Prenumerera på registerhändelser

I Event Grid prenumererar du på ett *ämne* för att berätta vilka händelser du vill spåra och var du ska skicka dem. Följande kommando för [att skapa domänhändelser][az-eventgrid-event-subscription-create] prenumererar på Azure SignalR-tjänsten som du skapade och anger webbappens URL som slutpunkt som den ska skicka händelser till. De miljövariabler som du har fyllt i i tidigare avsnitt återanvänds här, så inga ändringar krävs.

```azurecli-interactive
SIGNALR_SERVICE_ID=$(az signalr show --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-signalr \
    --source-resource-id $SIGNALR_SERVICE_ID \
    --endpoint $APP_ENDPOINT
```

När prenumerationen är klar bör du se utdata som liknar följande:

```JSON
{
  "deadLetterDestination": null,
  "destination": {
    "endpointBaseUrl": "https://$SITE_NAME.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "Microsoft.SignalRService.ClientConnectionConnected",
      "Microsoft.SignalRService.ClientConnectionDisconnected"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/Microsoft.SignalRService/SignalR/SignalRTestSvc/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-signalr",
  "labels": null,
  "name": "event-sub-signalr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "retryPolicy": {
    "eventTimeToLiveInMinutes": 1440,
    "maxDeliveryAttempts": 30
  },
  "topic": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/microsoft.signalrservice/signalr/SignalRTestSvc",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>Utlösa registerhändelser

Växla till serviceläget `Serverless Mode` till och konfigurera en klientanslutning till SignalR-tjänsten. Du kan ta [Serverless Sample](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Serverless) som referens.

```bash
git clone git@github.com:aspnet/AzureSignalR-samples.git

cd samples/Management

# Start the negotiation server
# Negotiation server is responsible for generating access token for clients
cd NegotitationServer
dotnet user-secrets set Azure:SignalR:ConnectionString "<Connection String>"
dotnet run

# Use a separate command line
# Start a client
cd SignalRClient
dotnet run
```

## <a name="view-registry-events"></a>Visa registerhändelser

Du har nu anslutit en klient till SignalR-tjänsten. Navigera till webbappen Loggboken och du `ClientConnectionConnected` bör se en händelse. Om du avslutar klienten visas `ClientConnectionDisconnected` även en händelse.

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
