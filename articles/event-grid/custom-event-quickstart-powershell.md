---
title: "Anpassade händelser för Azure Event Grid med PowerShell| Microsoft Docs"
description: "Använd Azure Event Grid och PowerShell för att publicera ett ämne och prenumerera på händelsen."
services: event-grid
keywords: 
author: djrosanova
ms.author: darosa
ms.date: 10/11/2017
ms.topic: hero-article
ms.service: event-grid
ms.openlocfilehash: 89c71194c2ef3c34b3356040c2e252fc09ba09c3
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2017
---
# <a name="create-and-route-custom-events-with-azure-powershell-and-event-grid"></a>Skapa och dirigera anpassade händelser med Azure PowerShell och Event Grid

Azure Event Grid är en händelsetjänst för molnet. I den här artikeln använder du Azure PowerShell för att skapa ett anpassat ämne, prenumerera på ämnet och utlösa händelsen för att visa resultatet. Normalt kan du skicka händelser till en slutpunkt som svarar på händelsen, exempelvis en webhook eller Azure Function. Men för att enkelt beskriva den här artikeln kan skicka du händelser till en URL som endast samlar in meddelanden. Du skapar denna URL med hjälp av en öppen källkod, ett tredjepartsverktyg som kallas [RequestBin](https://requestb.in/).

>[!NOTE]
>**RequestBin** är ett verktyg med öppen källkod som inte är avsett för användning med högt dataflöde. Här används verktyget endast i demonstrativt syfte. Om du push-överför fler än en händelse i taget kanske du inte ser alla händelser i verktyget.

När du är klar kan se du att händelsedata som har skickats till en slutpunkt.

![Händelsedata](./media/custom-event-quickstart-powershell/request-result.png)

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

Ett ämne ger en användardefinierad slutpunkt där du publicerar dina händelser. I följande exempel skapas ämnet i din resursgrupp. Ersätt `<topic_name>` med ett unikt namn för ditt ämne. Ämnesnamnet måste vara unikt eftersom det representeras av en DNS-post. I förhandsversionen har Event Grid stöd för platserna **westus2** och **westcentralus**.

```powershell
New-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Location westus2 -Name <topic_name>
```

## <a name="create-a-message-endpoint"></a>Skapa en slutpunkt för meddelanden

Innan du prenumererar på ämnet ska vi ska slutpunkten för händelsemeddelandet. I stället för att skriva kod för att svar på händelsen ska vi skapa en slutpunkt som samlar in meddelandena så att du kan visa dem. RequestBin är en öppen källkod, ett tredjepartsverktyg som låter dig skapa en slutpunkt och visa begäran som skickas till den. Gå till [RequestBin](https://requestb.in/) och klicka på **Create a RequestBin** (skapa en lagerplats).  Kopiera lagerplatsens URL eftersom du behöver den för att prenumerera på ämnet.

## <a name="subscribe-to-a-topic"></a>Prenumerera på ett ämne

Du prenumererar på ett ämne för att ange för Event Grid vilka händelser du vill följa. I följande exempel prenumererar vi på det ämne du just skapat, och URL från RequestBin skickas som slutpunkt för händelseavisering. Ersätt `<event_subscription_name>` med ett unikt namn för din prenumeration och `<URL_from_RequestBin>` med värdet från föregående avsnitt. Genom att ange en slutpunkt när du prenumererar kan Event Grid hantera omdirigeringen av händelser till denna slutpunkt. För `<topic_name>` använder du det värde du skapade tidigare.

```powershell
New-AzureRmEventGridSubscription -EventSubscriptionName <event_subscription_name> -Endpoint <URL_from_RequestBin> -ResourceGroupName gridResourceGroup -TopicName <topic_name>
```

## <a name="send-an-event-to-your-topic"></a>Skicka en händelse till ditt ämne

Nu ska vi utlösa en händelse och se hur Event Grid distribuerar meddelandet till slutpunkten. Först måste vi ta fram URL och nyckel för ämnet. Än en gång, använd din ämnesnamn för `<topic_name>`.

```powershell
$endpoint = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Endpoint
$keys = Get-AzureRmEventGridTopicKey -ResourceGroupName gridResourceGroup -Name <topic-name>
```

Skapa exempelhändelsedata att skicka till ämnet för att förenkla den här artikeln. Ett program eller en Azure-tjänst skulle vanligtvis skicka sådana händelsedata. I följande exempel hämtas händelsedata:

```powershell
$eventID = Get-Random 99999
$eventDate = Get-Date -Format s

$body = "[{`"id`": `"$eventID`",`"eventType`": `"recordInserted`",`"subject`": `"myapp/vehicles/motorcycles`",`"eventTime`": `"$eventDate`",`"data`":{`"make`": `"Ducati`",`"model`": `"Monster`"}}]"
```

Om `$body` visas så ser du den fullständiga händelsen. Elementet `data` av JSON är händelsens nyttolast. All välformulerad JSON kan stå i det här fältet. Du kan också använda ämnesfältet för avancerad omdirigering och filtrering.

Skicka nu händelsen till ditt ämne.

```powershell
Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

Du har utlöst händelsen och Event Grid skickade meddelandet till den slutpunkt du konfigurerade när du startade prenumerationen. Bläddra till den RequestBin-URL du skapade tidigare. Eller klicka på Uppdatera i den RequestBin-webbläsaren du har öppen. Du kan se den händelse du just skickade.

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
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/topics/{topic}"
}]
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta arbeta med den här händelsen ska du inte rensa upp bland de resurser som skapades i den här artikeln. Om du inte planerar att fortsätta kan du använda kommandona nedan för att ta bort alla resurser som har skapats i den här artikeln.

```powershell
Remove-AzureRmResourceGroup -Name gridResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du skapar ämnen och prenumerationer på händelser kan du läsa mer om vad Event Grid kan hjälpa dig med:

- [Om Event Grid](overview.md)
- [Dirigera Blob Storage-händelser till en anpassad webbslutpunkt](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Övervaka ändringar på virtuella maskiner med Azure Event Grid och Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Strömma stordata till ett datalager](event-grid-event-hubs-integration.md)
