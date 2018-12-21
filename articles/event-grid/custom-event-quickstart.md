---
title: Skicka anpassade händelser till en webbslutpunkt – Event Grid, Azure CLI | Microsoft Docs
description: Använd Azure Event Grid och Azure CLI för att publicera ett enpassat ämne och prenumerera på händelser för det ämnet. Händelserna hanteras av ett webbprogram.
services: event-grid
keywords: ''
author: tfitzmac
ms.author: tomfitz
ms.date: 12/07/2018
ms.topic: quickstart
ms.service: event-grid
ms.custom: seodec18
ms.openlocfilehash: 12fbc23e372042b54a987015273c004b32a9584a
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53104432"
---
# <a name="quickstart-route-custom-events-to-web-endpoint-with-azure-cli-and-event-grid"></a>Snabbstart: Dirigera anpassade händelser till en webbslutpunkt med Azure CLI och Event Grid

Azure Event Grid är en händelsetjänst för molnet. I den här artikeln använder du Azure CLI för att skapa ett anpassat ämne, prenumerera på det anpassade ämnet och utlösa händelsen för att visa resultatet. Normalt kan du skicka händelser till en slutpunkt som bearbetar informationen om händelsen och utför åtgärder. Men för att enkelt beskriva den här artikeln kan skicka du händelser till en webbapp som samlar in och visar meddelanden.

När du är klar kan se du att händelsedata som har skickats till webbappen.

![Visa resultat](./media/custom-event-quickstart/view-result.png)

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI:t lokalt måste du köra den senaste versionen av Azure CLI (2.0.24 eller senare). Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

Om du inte använder Cloud Shell måste du först logga in med `az login`.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Event Grid-ämnen är Azure-resurser och måste placeras i en Azure-resursgrupp. Resursgruppen är en logisk samling där Azure-resurser distribueras och hanteras.

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). 

I följande exempel skapas en resursgrupp med namnet *gridResourceGroup* på platsen *westus2*.

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="create-a-custom-topic"></a>Skapa en anpassat ämne

Ett event grid-ämne tillhandahåller en användardefinierad slutpunkt där du publicerar dina händelser. I följande exempel skapas det anpassade ämnet i din resursgrupp. Ersätt `<your-topic-name>` med ett unikt namn för ditt ämne. Namnet på det anpassade ämnet måste vara unikt eftersom det är en del av DNS-posten. Dessutom måste det bestå av mellan 3 och 50 tecken och bara innehålla a–z, A–Z, 0–9 och "-"

```azurecli-interactive
topicname=<your-topic-name>

az eventgrid topic create --name $topicname -l westus2 -g gridResourceGroup
```

## <a name="create-a-message-endpoint"></a>Skapa en slutpunkt för meddelanden

Innan du prenumererar på det anpassade ämnet ska vi ska slutpunkten för händelsemeddelandet. Slutpunkten utför vanligtvis åtgärder baserat på informationen om händelsen. För att förenkla den här snabbstarten kan du distribuera en [förskapad webbapp](https://github.com/Azure-Samples/azure-event-grid-viewer) som visar meddelanden om händelser. Den distribuerade lösningen innehåller en App Service-plan,en webbapp för App Service och källkod från GitHub.

Ersätt `<your-site-name>` med ett unikt namn för din webbapp. Webbappnamnet måste vara unikt eftersom det är en del av DNS-posten.

```azurecli-interactive
sitename=<your-site-name>

az group deployment create \
  --resource-group gridResourceGroup \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=$sitename hostingPlanName=viewerhost
```

Det kan ta några minuter att slutföra distributionen. Efter distributionen har slutförts kan du visa webbappen för att kontrollera att den körs. I en webbläsare navigerar du till: `https://<your-site-name>.azurewebsites.net`

Webbplatsen bör visas utan några meddelanden.

## <a name="subscribe-to-a-custom-topic"></a>Prenumerera på ett anpassat ämne

Du prenumererar på ett Event Grid-ämne därför att du vill ange för Event Grid vilka händelser du vill följa och vart du vill skicka händelserna. I följande exempel prenumererar vi på det anpassade ämne du just skapat, och URL från din webbapp skickas som slutpunkt för händelseavisering.

Slutpunkten för ditt webbprogram måste innehålla suffixet `/api/updates/`.

```azurecli-interactive
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name $topicname \
  --name demoViewerSub \
  --endpoint $endpoint
```

Visa ditt webbprogram igen och observera att en händelse för verifieringen av prenumerationen har skickats till den. Välj ögonikonen för att utöka informationen om händelsen. Händelserutnätet skickar valideringshändelsen så att slutpunkten kan bekräfta att den vill ta emot händelsedata. Webbappen inkluderar kod för att verifiera prenumerationen.

![Visa prenumerationshändelse](./media/custom-event-quickstart/view-subscription-event.png)

## <a name="send-an-event-to-your-custom-topic"></a>Skicka en händelse till det anpassade ämnet

Nu ska vi utlösa en händelse och se hur Event Grid distribuerar meddelandet till slutpunkten. Först måste vi ta fram URL och nyckel för det anpassade ämnet.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name $topicname -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicname -g gridResourceGroup --query "key1" --output tsv)
```

För att förenkla den här artikeln skickar du exempelhändelsedata till det anpassade ämnet. Ett program eller en Azure-tjänst skulle vanligtvis skicka sådana händelsedata. I följande exempel skapas exempelhändelsedata:

```azurecli-interactive
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
```

Elementet `data` av JSON är händelsens nyttolast. All välformulerad JSON kan stå i det här fältet. Du kan också använda ämnesfältet för avancerad omdirigering och filtrering.

CURL är ett verktyg som skickar HTTP-förfrågningar. I den här artikeln använder vi CURL till att skicka händelsen till ämnet. 

```azurecli-interactive
curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
```

Du har utlöst händelsen och Event Grid skickade meddelandet till den slutpunkt som du konfigurerade när du prenumererade. Visa din webbapp om du vill se händelsen som du har skickat.

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/topics/{topic}"
}]
```

## <a name="clean-up-resources"></a>Rensa resurser
Om du planerar att fortsätta arbeta med den här händelsen eller händelsevisningsappen ska du inte rensa upp bland de resurser som skapades i den här artikeln. I annat fall kan du använda kommandona nedan för att ta bort alla resurser som har skapats i den här artikeln.

```azurecli-interactive
az group delete --name gridResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du skapar ämnen och prenumerationer på händelser kan du läsa mer om vad Event Grid kan hjälpa dig med:

- [Om Event Grid](overview.md)
- [Dirigera Blob Storage-händelser till en anpassad webbslutpunkt](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Övervaka ändringar på virtuella maskiner med Azure Event Grid och Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Strömma stordata till ett datalager](event-grid-event-hubs-integration.md)
