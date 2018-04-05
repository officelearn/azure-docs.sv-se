---
title: Anpassade händelser för Azure Event Grid med PowerShell| Microsoft Docs
description: Använd Azure Event Grid och PowerShell för att publicera ett ämne och prenumerera på händelsen.
services: event-grid
keywords: ''
author: tfitzmac
ms.author: tomfitz
ms.date: 03/20/2018
ms.topic: hero-article
ms.service: event-grid
ms.openlocfilehash: 183bafad9b38ccb0e7eaae222c5569e45b15ac21
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="create-and-route-custom-events-with-azure-powershell-and-event-grid"></a>Skapa och dirigera anpassade händelser med Azure PowerShell och Event Grid

Azure Event Grid är en händelsetjänst för molnet. I den här artikeln använder du Azure PowerShell för att skapa ett anpassat ämne, prenumerera på ämnet och utlösa händelsen för att visa resultatet. Normalt kan du skicka händelser till en slutpunkt som svarar på händelsen, exempelvis en webhook eller Azure Function. Men för att enkelt beskriva den här artikeln kan skicka du händelser till en URL som endast samlar in meddelanden. Du skapar den här URL:en med ett tredjepartsverktyg från [Hookbin](https://hookbin.com/).

>[!NOTE]
>**Hookbin** är inte avsett för användning med hög genomströmning. Här används verktyget endast i instruktionssyfte. Om du push-överför fler än en händelse i taget kanske du inte ser alla händelser i verktyget.

När du är klar kan se du att händelsedata som har skickats till en slutpunkt.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

Den här artikeln kräver att du kör den senaste versionen av Azure PowerShell. Om du behöver installera eller uppgradera kan du läsa [Installera och konfigurera Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Event Grid-ämnen är Azure-resurser och måste placeras i en Azure-resursgrupp. Resursgruppen är en logisk samling där Azure-resurser distribueras och hanteras.

Skapa en resursgrupp med kommandot [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup).

I följande exempel skapas en resursgrupp med namnet *gridResourceGroup* på platsen *westus2*.

```powershell
New-AzureRmResourceGroup -Name gridResourceGroup -Location westus2
```

## <a name="create-a-custom-topic"></a>Skapa en anpassat ämne

Ett event grid-ämne tillhandahåller en användardefinierad slutpunkt där du publicerar dina händelser. I följande exempel skapas det anpassade ämnet i din resursgrupp. Ersätt `<topic_name>` med ett unikt namn för ditt ämne. Ämnesnamnet måste vara unikt eftersom det representeras av en DNS-post.

```powershell
New-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Location westus2 -Name <topic_name>
```

## <a name="create-a-message-endpoint"></a>Skapa en slutpunkt för meddelanden

Innan du prenumererar på det anpassade ämnet ska vi ska slutpunkten för händelsemeddelandet. I stället för att skriva kod för att svar på händelsen ska vi skapa en slutpunkt som samlar in meddelandena så att du kan visa dem. Hookbin är ett tredjepartsverktyg som låter dig skapa en slutpunkt och visa förfrågningar som skickas till den. Gå till [Hookbin](https://hookbin.com/) och klicka på **Skapa ny slutpunkt**.  Kopiera lagerplatsens URL eftersom du behöver den för att prenumerera på ämnet.

## <a name="subscribe-to-a-topic"></a>Prenumerera på ett ämne

Du prenumererar på ett ämne för att ange för Event Grid vilka händelser du vill följa. I följande exempel prenumererar vi på det anpassade ämne du just skapat, och URL från Hookbin skickas som slutpunkt för händelseavisering. Ersätt `<event_subscription_name>` med ett unikt namn för din prenumeration och `<endpoint_URL>` med värdet från föregående avsnitt. Genom att ange en slutpunkt när du prenumererar kan Event Grid hantera omdirigeringen av händelser till denna slutpunkt. För `<topic_name>` använder du det värde du skapade tidigare.

```powershell
New-AzureRmEventGridSubscription -EventSubscriptionName <event_subscription_name> -Endpoint <endpoint_URL> -ResourceGroupName gridResourceGroup -TopicName <topic_name>
```

## <a name="send-an-event-to-your-topic"></a>Skicka en händelse till ditt ämne

Nu ska vi utlösa en händelse och se hur Event Grid distribuerar meddelandet till slutpunkten. Först måste vi ta fram URL och nyckel för ämnet. Än en gång, använd din ämnesnamn för `<topic_name>`.

```powershell
$endpoint = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Endpoint
$keys = Get-AzureRmEventGridTopicKey -ResourceGroupName gridResourceGroup -Name <topic-name>
```

Låt oss skapa exempelhändelsedata att skicka till det anpassade ämnet för att förenkla den här artikeln. Ett program eller en Azure-tjänst skulle vanligtvis skicka sådana händelsedata. I följande exempel används en Hashtable för att konstruera händelsedata `htbody` som sedan konverteras till korrekt JSON-nyttolastobjekt `$body`:

```powershell
$eventID = Get-Random 99999

#Date format should be SortableDateTimePattern (ISO 8601)
$eventDate = Get-Date -Format s

#Construct body using Hashtable
$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/motorcycles"
    eventTime= $eventDate   
    data= @{
        make="Ducati"
        model="Monster"
    }
    dataVersion="1.0"
}

#Use ConvertTo-Json to convert event body from Hashtable to JSON Object
#Append square brackets to the converted JSON payload since they are expected in the event's JSON payload syntax
$body = "["+(ConvertTo-Json $htbody)+"]"
```

Om `$body` visas så ser du den fullständiga händelsen. Elementet `data` av JSON är händelsens nyttolast. All välformulerad JSON kan stå i det här fältet. Du kan också använda ämnesfältet för avancerad omdirigering och filtrering.

Skicka nu händelsen till ditt ämne.

```powershell
Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

Du har utlöst händelsen och Event Grid skickade meddelandet till den slutpunkt som du konfigurerade när du prenumererade. Bläddra till slutpunktsadress du skapade tidigare. Du kan också klicka på Uppdatera i webbläsaren du har öppen. Du kan se den händelse du just skickade.

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2018-01-25T15:58:13",
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

Om du planerar att fortsätta arbeta med den här händelsen ska du inte rensa upp bland de resurser som skapades i den här artikeln. I annat fall kan du använda kommandona nedan för att ta bort alla resurser som har skapats i den här artikeln.

```powershell
Remove-AzureRmResourceGroup -Name gridResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du skapar ämnen och prenumerationer på händelser kan du läsa mer om vad Event Grid kan hjälpa dig med:

- [Om Event Grid](overview.md)
- [Dirigera Blob Storage-händelser till en anpassad webbslutpunkt](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Övervaka ändringar på virtuella maskiner med Azure Event Grid och Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Strömma stordata till ett datalager](event-grid-event-hubs-integration.md)
