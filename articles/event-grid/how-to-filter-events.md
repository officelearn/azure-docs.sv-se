---
title: Så här filtrerar du händelser för Azure Event Grid
description: Den här artikeln visar hur du filtrerar händelser (efter händelsetyp, efter ämne, efter operatorer och data osv.) när du skapar en Event Grid-prenumeration.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 63a5cdbff79af52d9f96cf410a820c6cfc530066
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454031"
---
# <a name="filter-events-for-event-grid"></a>Filtrera händelser för händelserutnät

Den här artikeln visar hur du filtrerar händelser när du skapar en Event Grid-prenumeration. Mer information om alternativen för händelsefiltrering finns i [Förstå händelsefiltrering för eventrutnätsprenumerationer](event-filtering.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="filter-by-event-type"></a>Filtrera efter händelsetyp

När du skapar en Event Grid-prenumeration kan du ange vilka [händelsetyper](event-schema.md) som ska skickas till slutpunkten. Exemplen i det här avsnittet skapar händelseprenumerationer för `Microsoft.Resources.ResourceWriteFailure` `Microsoft.Resources.ResourceWriteSuccess`en resursgrupp men begränsar de händelser som skickas till och . Om du behöver mer flexibilitet när du filtrerar händelser efter händelsetyper läser du Filtrera efter avancerade operatorer och datafält.

Använd parametern `-IncludedEventType` när du skapar prenumerationen för PowerShell.

```powershell
$includedEventTypes = "Microsoft.Resources.ResourceWriteFailure", "Microsoft.Resources.ResourceWriteSuccess"

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Endpoint <endpoint-URL> `
  -IncludedEventType $includedEventTypes
```

Använd parametern för `--included-event-types` Azure CLI. I följande exempel används Azure CLI i ett Bash-skal:

```azurecli
includedEventTypes="Microsoft.Resources.ResourceWriteFailure Microsoft.Resources.ResourceWriteSuccess"

az eventgrid event-subscription create \
  --name demoSubToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --included-event-types $includedEventTypes
```

Använd egenskapen för `includedEventTypes` en Resource Manager-mall.

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

Du kan filtrera händelser efter ämne i händelsedata. Du kan ange ett värde som ska matchas för början eller slutet av ämnet. Om du behöver mer flexibilitet när du filtrerar händelser efter ämne läser du Filtrera efter avancerade operatorer och datafält.

I följande PowerShell-exempel skapar du en händelseprenumeration som filtrerar i början av ämnet. Du kan `-SubjectBeginsWith` använda parametern för att begränsa händelser till händelser för en viss resurs. Du skickar resurs-ID för en nätverkssäkerhetsgrupp.

```powershell
$resourceId = (Get-AzResource -ResourceName demoSecurityGroup -ResourceGroupName myResourceGroup).ResourceId

New-AzEventGridSubscription `
  -Endpoint <endpoint-URL> `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -SubjectBeginsWith $resourceId
```

I nästa PowerShell-exempel skapas en prenumeration för en blob-lagring. Det begränsar händelser till händelser med `.jpg`ett ämne som slutar i .

```powershell
$storageId = (Get-AzStorageAccount -ResourceGroupName myResourceGroup -AccountName $storageName).Id

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToStorage `
  -Endpoint <endpoint-URL> `
  -ResourceId $storageId `
  -SubjectEndsWith ".jpg"
```

I följande Azure CLI-exempel skapar du en händelseprenumeration som filtrerar i början av ämnet. Du kan `--subject-begins-with` använda parametern för att begränsa händelser till händelser för en viss resurs. Du skickar resurs-ID för en nätverkssäkerhetsgrupp.

```azurecli
resourceId=$(az resource show --name demoSecurityGroup --resource-group myResourceGroup --resource-type Microsoft.Network/networkSecurityGroups --query id --output tsv)

az eventgrid event-subscription create \
  --name demoSubscriptionToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --subject-begins-with $resourceId
```

Nästa Azure CLI-exempel skapar en prenumeration för en blob-lagring. Det begränsar händelser till händelser med `.jpg`ett ämne som slutar i .

```azurecli
storageid=$(az storage account show --name $storageName --resource-group myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name demoSubToStorage \
  --endpoint <endpoint-URL> \
  --subject-ends-with ".jpg"
```

I följande exempel på Resource Manager-mall skapar du en händelseprenumeration som filtrerar i början av ämnet. Du kan `subjectBeginsWith` använda egenskapen för att begränsa händelser till händelser för en viss resurs. Du skickar resurs-ID för en nätverkssäkerhetsgrupp.

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

I nästa Resource Manager-mallexempel skapas en prenumeration för en blob-lagring. Det begränsar händelser till händelser med `.jpg`ett ämne som slutar i .

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

Om du vill ha större flexibilitet i filtrering kan du använda operatorer och dataegenskaper för att filtrera händelser.

### <a name="subscribe-with-advanced-filters"></a>Prenumerera med avancerade filter

Mer information om de operatorer och nycklar som du kan använda för avancerad filtrering finns i [Avancerad filtrering](event-filtering.md#advanced-filtering).

De här exemplen skapar ett anpassat ämne. De prenumererar på det anpassade ämnet och filtrerar efter ett värde i dataobjektet. Händelser som har färgegenskapen inställd på blå, röd eller grön skickas till prenumerationen.

Om du använder Azure CLI använder du:

```azurecli
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
  --expiration-date "<yyyy-mm-dd>"
```

Observera att ett [förfallodatum](concepts.md#event-subscription-expiration) har angetts för prenumerationen.

Om du använder PowerShell använder du:

```powershell
$topicName = <your-topic-name>
$endpointURL = <endpoint-URL>

New-AzResourceGroup -Name gridResourceGroup -Location eastus2
New-AzEventGridTopic -ResourceGroupName gridResourceGroup -Location eastus2 -Name $topicName

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Id

$expDate = '<mm/dd/yyyy hh:mm:ss>' | Get-Date
$AdvFilter1=@{operator="StringIn"; key="Data.color"; Values=@('blue', 'red', 'green')}

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpointURL `
  -ExpirationDate $expDate `
  -AdvancedFilter @($AdvFilter1)
```

### <a name="test-filter"></a>Testfilter

Om du vill testa filtret skickar du en händelse med färgfältet inställt på grönt. Eftersom grönt är ett av värdena i filtret levereras händelsen till slutpunkten.

Om du använder Azure CLI använder du:

```azurecli
topicEndpoint=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicName -g gridResourceGroup --query "key1" --output tsv)

event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "green"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

Om du använder PowerShell använder du:

```powershell
$endpoint = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Endpoint
$keys = Get-AzEventGridTopicKey -ResourceGroupName gridResourceGroup -Name $topicName

$eventID = Get-Random 99999
$eventDate = Get-Date -Format s

$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="green"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

Om du vill testa ett scenario där händelsen inte skickas skickar du en händelse med färgfältet inställt på gult. Gult är inte ett av de värden som anges i prenumerationen, så händelsen levereras inte till din prenumeration.

Om du använder Azure CLI använder du:

```azurecli
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "yellow"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```
Om du använder PowerShell använder du:

```powershell
$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="yellow"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

## <a name="next-steps"></a>Nästa steg

* Information om hur du övervakar händelseleveranser finns i [Övervaka leverans av händelserutnätsmeddelanden](monitor-event-delivery.md).
* Mer information om autentiseringsnyckeln finns i [säkerhet och autentisering av Händelserutnät](security-authentication.md).
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Prenumerationsschema för Event Grid](subscription-creation-schema.md).
