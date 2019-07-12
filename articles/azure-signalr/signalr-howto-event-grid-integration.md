---
title: Hur du skickar Azure SignalR Service-händelser till Event Grid
description: En guide till hur du kan aktivera Event Grid-händelser för din SignalR Service sedan skicka klientanslutning anslutet/frånkopplat händelser till ett exempelprogram.
services: azure-signalr
author: chenyl
ms.service: azure-signalr
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: chenyl
ms.openlocfilehash: 2d782306938136ce6d21a331185f591316f58a29
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789181"
---
# <a name="how-to-send-events-from-azure-signalr-service-to-event-grid"></a>Hur man skickar händelser från Azure SignalR Service till Event Grid

Azure Event Grid är en fullständigt hanterad tjänst för händelsedirigering som ger enhetlig förbrukning med en pub-sub-modell. I den här guiden använder du Azure CLI för att skapa en Azure SignalR Service, prenumerera på anslutningshändelser och sedan distribuera en exempelwebbapp för att ta emot händelser. Slutligen kan du ansluta och koppla bort och visa händelsenyttolast i exempelprogrammet.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto][azure-account] innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure CLI-kommandon i den här artikeln har formaterats för den **Bash** shell. Om du använder ett annat gränssnitt som PowerShell eller Kommandotolken, kan du behöva justeras rad fortsättning tecken eller variabeltilldelning rader. Den här artikeln används variabler för att minimera mängden kommandot redigering krävs.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk behållare där du kan distribuera och hantera Azure-resurser. Följande [az gruppen skapa][az-group-create] kommandot skapar en resursgrupp med namnet *myResourceGroup* i den *eastus* region. Om du vill använda ett annat namn för resursgruppen anger `RESOURCE_GROUP_NAME` på ett annat värde.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-signalr-service"></a>Skapa en Azure SignalR Service

Distribuera en Azure Signalr Service i resursgruppen med följande kommandon.
```azurecli-interactive
SIGNALR_NAME=SignalRTestSvc

az signalr create --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --sku Free_F1
```

När du har skapat SignalR Service, returnerar Azure CLI utdata som liknar följande:

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

## <a name="create-an-event-endpoint"></a>Skapa en händelse-slutpunkt

I det här avsnittet ska använda du Resource Manager-mall finns i en GitHub-lagringsplats för att distribuera ett färdiga exempelwebbprogram till Azure App Service. Senare kan du prenumerera på ditt register Event Grid-händelser och ange den här appen som slutpunkt som händelserna ska skickas.

Om du vill distribuera exempelappen ange `SITE_NAME` till ett unikt namn för din webbapp och kör följande kommandon. Platsnamnet måste vara unikt i Azure eftersom den utgör en del av det fullständigt kvalificerade domännamnet (FQDN) för webbappen. I ett senare avsnitt gå till appens FQDN i en webbläsare för att visa händelser för ditt register.

```azurecli-interactive
SITE_NAME=<your-site-name>

az group deployment create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

När distributionen är klar (det kan ta några minuter), öppna en webbläsare och navigera till webbappen och kontrollera att det körs:

`http://<your-site-name>.azurewebsites.net`

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Prenumerera på händelser för registret

I Event Grid kan du prenumerera på en *avsnittet* att tala om vilka händelser som du vill spåra och var du vill skicka dem. Följande [az eventgrid-händelseprenumeration skapa][az-eventgrid-event-subscription-create] kommandot prenumererar på Azure SignalR Service du har skapat och anger URL: en för din webbapp som slutpunkt som den ska skicka händelser. De miljövariabler som du har fyllt i i tidigare avsnitt återanvänds här, så inga ändringar krävs.

```azurecli-interactive
SIGNALR_SERVICE_ID=$(az signalr show --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-signalr \
    --source-resource-id $SIGNALR_SERVICE_ID \
    --endpoint $APP_ENDPOINT
```

När prenumerationen har slutförts bör du se utdata som liknar följande:

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

## <a name="trigger-registry-events"></a>Utlösarhändelser för registret

Växla till läget för tjänsten att `Serverless Mode` och konfigurera en klientanslutningen till SignalR Service. Du kan vidta [utan Server exemplet](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Serverless) som referens.

```bash
git clone git@github.com:aspnet/AzureSignalR-samples.git

cd samples/Management

# Start the negotiation server
# Negotiation server is responsible for generating access token for clients
cd NegotitationServer
dotnet user-secrets set Azure:SignalR:ConnectionString "<Connection String>"
dotnet run

# Use a seperate command line
# Start a client
cd SignalRClient
dotnet run
```

## <a name="view-registry-events"></a>Visa händelser för registret

Du har nu anslutit en klient till SignalR Service. Navigera till webbappen Grid Loggboken och du bör se en `ClientConnectionConnected` händelse. Om du säger upp klienten dessutom visas en `ClientConnectionDisconnected` händelse.

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
