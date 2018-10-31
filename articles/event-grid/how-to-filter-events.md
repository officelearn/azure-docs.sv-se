---
title: Så här filtrerar du händelser för Azure Event Grid
description: Visar hur du skapar Azure Event Grid-prenumerationer som Filtrera händelser.
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: tomfitz
ms.openlocfilehash: 8bf7ac9daf928c35a3d6efcac528d3372fa87c8a
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50252147"
---
# <a name="filter-events-for-event-grid"></a>Filtrera händelser för Event Grid

Den här artikeln visar hur du filtrerar händelser när du skapar en Event Grid-prenumeration. Läs mer om alternativen för händelsefiltrering, i [förstå händelse filtrering för Event Grid-prenumerationer](event-filtering.md).

## <a name="filter-by-event-type"></a>Filtrera efter händelsetyp

När du skapar en Event Grid-prenumeration kan du ange vilka [händelsetyper](event-schema.md) att skicka till slutpunkten. Exemplen i det här avsnittet Skapa prenumerationer på händelser för en resursgrupp, men begränsar de händelser som skickas till `Microsoft.Resources.ResourceWriteFailure` och `Microsoft.Resources.ResourceWriteSuccess`. Om du behöver mer flexibilitet när du filtrerar händelser efter händelsetyper, se [filtrera efter avancerade operatorer och fält](#filter-by-advanced-operators-and-data-fields).

Du använder PowerShell använder den `-IncludedEventType` parameter när du skapar prenumerationen.

```powershell
$includedEventTypes = "Microsoft.Resources.ResourceWriteFailure", "Microsoft.Resources.ResourceWriteSuccess"

New-AzureRmEventGridSubscription `
  -EventSubscriptionName demoSubToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Endpoint <endpoint-URL> `
  -IncludedEventType $includedEventTypes
```

Azure CLI, använder de `--included-event-types` parametern. I följande exempel används Azure CLI i Bash-gränssnittet:

```azurecli
includedEventTypes="Microsoft.Resources.ResourceWriteFailure Microsoft.Resources.ResourceWriteSuccess"

az eventgrid event-subscription create \
  --name demoSubToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --included-event-types $includedEventTypes
```

En Resource Manager-mall, använda den `includedEventTypes` egenskapen.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [
          "Microsoft.Resources.ResourceWriteFailure",
          "Microsoft.Resources.ResourceWriteSuccess"
        ]
      }
    }
  }
]
```

## <a name="filter-by-subject"></a>Filtrera efter ämne

Du kan filtrera händelserna efter ämne i informationen om händelsen. Du kan ange ett värde att matcha för början eller slutet av ämne. Om du behöver mer flexibilitet när du filtrerar händelser efter ämne, se [filtrera efter avancerade operatorer och fält](#filter-by-advanced-operators-and-data-fields).

I följande PowerShell-exempel skapar du en händelseprenumeration som filtrerar genom att i början av ämne. Du använder den `-SubjectBeginsWith` parametern för att begränsa händelser till de som för en specifik resurs. Du skickar resurs-ID för en grupp.

```powershell
$resourceId = (Get-AzureRmResource -ResourceName demoSecurityGroup -ResourceGroupName myResourceGroup).ResourceId

New-AzureRmEventGridSubscription `
  -Endpoint <endpoint-URL> `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -SubjectBeginsWith $resourceId
```

I nästa PowerShell-exempel skapas en prenumeration för ett blob storage. Det begränsar händelser till sådana med ett ämne som slutar med `.jpg`.

```powershell
$storageId = (Get-AzureRmStorageAccount -ResourceGroupName myResourceGroup -AccountName $storageName).Id

New-AzureRmEventGridSubscription `
  -EventSubscriptionName demoSubToStorage `
  -Endpoint <endpoint-URL> `
  -ResourceId $storageId `
  -SubjectEndsWith ".jpg"
```

I följande exempel för Azure CLI skapar du en händelseprenumeration som filtrerar genom att i början av ämne. Du använder den `--subject-begins-with` parametern för att begränsa händelser till de som för en specifik resurs. Du skickar resurs-ID för en grupp.

```azurecli
resourceId=$(az resource show --name demoSecurityGroup --resource-group myResourceGroup --resource-type Microsoft.Network/networkSecurityGroups --query id --output tsv)

az eventgrid event-subscription create \
  --name demoSubscriptionToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --subject-begins-with $resourceId
```

I nästa Azure CLI-exempel skapas en prenumeration för ett blob storage. Det begränsar händelser till sådana med ett ämne som slutar med `.jpg`.

```azurecli
storageid=$(az storage account show --name $storageName --resource-group myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name demoSubToStorage \
  --endpoint <endpoint-URL> \
  --subject-ends-with ".jpg"
```

I följande exempelmall för Resource Manager kan du skapa en händelseprenumeration som filtrerar genom att i början av ämne. Du använder den `subjectBeginsWith` egenskapen att begränsa händelser till de som för en specifik resurs. Du skickar resurs-ID för en grupp.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "[resourceId('Microsoft.Network/networkSecurityGroups','demoSecurityGroup')]",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

I nästa exempel för Resource Manager-mallen skapar en prenumeration för ett blob storage. Det begränsar händelser till sådana med ett ämne som slutar med `.jpg`.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
    "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/', parameters('eventSubName'))]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectEndsWith": ".jpg",
        "subjectBeginsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

## <a name="filter-by-operators-and-data"></a>Filtrera efter operatorer och data

Om du vill använda avancerade filter, måste du installera ett tillägg för förhandsversion för Azure CLI. Du kan använda [CloudShell](/azure/cloud-shell/quickstart) eller installera Azure CLI lokalt.

### <a name="install-extension"></a>Installera tillägget

I CloudShell:

* Om du har installerat tillägget tidigare kan du uppdatera den `az extension update -n eventgrid`
* Om du inte tidigare har installerat tillägget, installerar du det `az extension add -n eventgrid`

För en lokal installation:

1. Avinstallera Azure CLI lokalt.
1. Installera den [senaste versionen](/cli/azure/install-azure-cli) av Azure CLI.
1. Starta Kommandotolken.
1. Avinstallera tidigare versioner av tillägget `az extension remove -n eventgrid`
1. Installera tillägget `az extension add -n eventgrid`

Du är nu redo att använda avancerade filter.

### <a name="subscribe-with-advanced-filters"></a>Prenumerera med avancerade filter

Läs om operatörer och nycklar som du kan använda för avancerade filter i [avancerad filtrering](event-filtering.md#advanced-filtering).

I följande exempel skapas ett anpassat ämne. Den prenumererar på det anpassade ämnet och filtrerar efter ett värde i dataobjektet. Händelser som har egenskapen färg har angetts till blått, rött eller grönt skickas till prenumerationen.

```azurecli-interactive
topicName=<your-topic-name>
endpointURL=<endpoint-URL>

az group create -n gridResourceGroup -l eastus2
az eventgrid topic create --name $topicName -l eastus2 -g gridResourceGroup

topicid=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  -n demoAdvancedSub \
  --advanced-filter data.color stringin blue red green \
  --endpoint $endpointURL \
  --expiration-date "2018-11-30"
```

Observera att ett förfallodatum har angetts för prenumerationen. Händelseprenumerationen har upphört att gälla automatiskt efter det datumet. Ange en giltighetstid för prenumerationer på händelser som krävs endast under en begränsad tid.

### <a name="test-filter"></a>Test-filter

Testa filtret genom att skicka en händelse med färgfältet inställd grönt.

```azurecli-interactive
topicEndpoint=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicName -g gridResourceGroup --query "key1" --output tsv)

event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "green"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

Händelsen skickas till din slutpunkt.

Testa ett scenario där händelsen inte skickas genom att skicka en händelse med färgfältet inställd gult.

```azurecli-interactive
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "yellow"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

Gul inte något av värdena som anges i prenumerationen, så att händelsen inte levereras till din prenumeration.

## <a name="next-steps"></a>Nästa steg

* Information om hur du övervakar händelse leveranser finns i [övervaka Event Grid meddelandeleverans](monitor-event-delivery.md).
* Läs mer om autentiseringsnyckeln [Event Grid säkerhet och autentisering](security-authentication.md).
* Läs mer om hur du skapar en Azure Event Grid-prenumeration, [Event Grid prenumerationsschema](subscription-creation-schema.md).
