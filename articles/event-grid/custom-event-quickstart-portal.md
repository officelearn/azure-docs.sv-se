---
title: Skicka anpassade händelser till en webbslutpunkt – Event Grid, Azure CLI
description: Använd Azure Event Grid och Azure Portal till att publicera ett anpassat ämne och prenumerera på händelser för det ämnet. Händelserna hanteras av en webbapp.
services: event-grid
keywords: ''
author: spelluru
ms.author: spelluru
ms.date: 03/27/2019
ms.topic: quickstart
ms.service: event-grid
ms.custom: seodec18
ms.openlocfilehash: b0fb6ea527c46ab6a9de95ba5f5ae39b8a5d50b1
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576990"
---
# <a name="quickstart-route-custom-events-to-web-endpoint-with-the-azure-portal-and-event-grid"></a>Snabbstart: Dirigera anpassade händelser till en webbslutpunkt med Azure Portal och Event Grid

Azure Event Grid är en händelsetjänst för molnet. I den här artikeln använder du Azure-portalen för att skapa ett anpassat ämne, prenumerera på det anpassade ämnet och utlösa händelsen för att visa resultatet. Normalt kan du skicka händelser till en slutpunkt som bearbetar informationen om händelsen och utför åtgärder. Men för att enkelt beskriva den här artikeln kan skicka du händelser till en webbapp som samlar in och visar meddelanden.

När du är klar kan se du att händelsedata som har skickats till webbappen.

![Visa resultat](./media/custom-event-quickstart-portal/view-result.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="create-a-custom-topic"></a>Skapa en anpassat ämne

Ett event grid-ämne tillhandahåller en användardefinierad slutpunkt där du publicerar dina händelser. 

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **alla tjänster** på menyn till vänster navigering, söka efter **Event Grid**, och välj **Event Grid-ämnen**. 

    ![Välj Event Grid-ämnen](./media/custom-event-quickstart-portal/select-event-grid-topics.png)
3. På den **Event Grid-ämnen** väljer **+ Lägg till** i verktygsfältet. 

    ![Lägg till Event Grid-ämne knapp](./media/custom-event-quickstart-portal/add-event-grid-topic-button.png)
4. På den **Skapa avsnittet** utför de här stegen:
    1. Ange ett unikt **namn** för det anpassade ämnet. Ämnesnamnet måste vara unikt eftersom det representeras av en DNS-post. Använd inte det namn som visas på bilden. I stället skapar du ett eget namn som måste bestå av 3–50 tecken och enbart får innehålla a-z, A-Z, 0-9 och ”-”.
    2. Välj din Azure-**prenumeration**.
    3. Välj en befintlig resursgrupp eller välj **Skapa nytt**, och ange en **namn** för den **resursgrupp**.
    4. Välj en **plats** för event grid-ämne.
    5. Behåll standardvärdet **Grid Händelseschema** för den **Händelseschema** fält. 

       ![Skapa ämne sida](./media/custom-event-quickstart-portal/create-custom-topic.png)
    6. Välj **Skapa**. 
5. När du har skapat det anpassade ämnet visas ett meddelande om detta. Välj **går du till resursgruppen**. 

   ![Se meddelandet](./media/custom-event-quickstart-portal/success-notification.png)
6. På den **resursgrupp** väljer event grid-ämne. 

   ![Välj event grid avsnittet resurs](./media/custom-event-quickstart-portal/select-event-grid-topic.png)
7. Du ser den **Event Grid-ämne** sidan för event grid. Behåll den här sidan öppen. Du kan använda den senare i snabbstarten. 

    ![Startsida för Event Grid-ämne](./media/custom-event-quickstart-portal/event-grid-topic-home-page.png)

## <a name="create-a-message-endpoint"></a>Skapa en slutpunkt för meddelanden
Innan du skapar en prenumeration för det anpassade ämnet måste du skapa en slutpunkt för händelsemeddelandet. Slutpunkten utför vanligtvis åtgärder baserat på informationen om händelsen. För att förenkla den här snabbstarten kan du distribuera en [förskapad webbapp](https://github.com/Azure-Samples/azure-event-grid-viewer) som visar meddelanden om händelser. Den distribuerade lösningen innehåller en App Service-plan,en webbapp för App Service och källkod från GitHub.

1. Artikeln på sidan Välj **distribuera till Azure** att distribuera lösningen till din prenumeration. Ange parametervärdena i Azure Portal.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
1. Det kan ta några minuter att slutföra distributionen. Efter distributionen har slutförts kan du visa webbappen för att kontrollera att den körs. I en webbläsare navigerar du till: `https://<your-site-name>.azurewebsites.net`
1. Du ser webbplatsen men det har inte publicerats händelser till den än.

   ![Visa ny webbplats](./media/custom-event-quickstart-portal/view-site.png)

## <a name="subscribe-to-custom-topic"></a>Prenumerera på anpassat ämne

Du prenumererar på ett Event Grid-ämne därför att du vill ange för Event Grid vilka händelser du vill följa och vart du vill skicka händelserna.

1. Nu på den **Event Grid-ämne** sidan för ditt anpassade ämne väljer **+ händelseprenumeration** i verktygsfältet.

   ![Lägga till händelseprenumeration](./media/custom-event-quickstart-portal/new-event-subscription.png)
2. På den **skapa händelseprenumeration** utför de här stegen:
    1. Händelse en **namn** för händelseprenumerationen.
    3. Välj **Webhook** för den **slutpunktstyp**. 
    4. Välj **väljer du en slutpunkt**. 

       ![Ange värden för händelseprenumerationen](./media/custom-event-quickstart-portal/provide-subscription-values.png)
    5. För webhookens slutpunkt anger du webbappens webbadress och lägger till `api/updates` till startsidans webbadress. Välj **Bekräfta val**.

       ![Ange slutpunktens webbadress](./media/custom-event-quickstart-portal/provide-endpoint.png)
    6. Gå tillbaka till den **skapa händelseprenumeration** väljer **skapa**.

3. Visa ditt webbprogram igen och observera att en händelse för verifieringen av prenumerationen har skickats till den. Välj ögonikonen för att utöka informationen om händelsen. Händelserutnätet skickar valideringshändelsen så att slutpunkten kan bekräfta att den vill ta emot händelsedata. Webbappen inkluderar kod för att verifiera prenumerationen.

    ![Visa prenumerationshändelse](./media/custom-event-quickstart-portal/view-subscription-event.png)

## <a name="send-an-event-to-your-topic"></a>Skicka en händelse till ditt ämne

Nu ska vi utlösa en händelse och se hur Event Grid distribuerar meddelandet till slutpunkten. Du kan använda antingen Azure CLI eller PowerShell och skicka en testhändelse till det anpassade ämnet. Ett program eller en Azure-tjänst skulle vanligtvis skicka sådana händelsedata.

I det första exemplet används Azure CLI. URL och nyckel för det anpassade ämnet hämtas, och exempeldata för händelsen. Använd ditt anpassade ämnesnamn för `<topic name>`. Exempelhändelsedata skapas. Elementet `data` av JSON är händelsens nyttolast. All välformulerad JSON kan stå i det här fältet. Du kan också använda ämnesfältet för avancerad omdirigering och filtrering. CURL är ett verktyg som skickar HTTP-förfrågningar.


### <a name="azure-cli"></a>Azure CLI
1. I Azure-portalen väljer du **Cloud Shell**. Välj **Bash** i det övre vänstra hörnet i Cloud Shell-fönstret. 

    ![Cloudshell - Bash](./media/custom-event-quickstart-portal/cloud-shell-bash.png)
1. Kör följande kommando för att hämta den **endpoint** för ämnet: När du kopierar och klistrar in det, uppdaterar den **ämnesnamn** och **resursgruppens namn** innan du kör kommandot. 

    ```azurecli
    endpoint=$(az eventgrid topic show --name <topic name> -g <resource group name> --query "endpoint" --output tsv)
    ```
2. Kör följande kommando för att hämta den **nyckel** för det anpassade ämnet: När du kopierar och klistrar in det, uppdaterar den **ämnesnamn** och **resursgrupp** namn innan du kör kommandot. 

    ```azurecli
    key=$(az eventgrid topic key list --name <topic name> -g <resource group name> --query "key1" --output tsv)
    ```
3. Kopiera följande instruktion med händelsen definition och tryck på **RETUR**. 

    ```json
    event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
    ```
4. Kör följande **Curl** kommando för att publicera händelsen:

    ```
    curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
    ```

### <a name="azure-powershell"></a>Azure PowerShell
I det andra exemplet används PowerShell för att utföra liknande steg.

1. I Azure-portalen väljer du **Cloud Shell**. Välj **PowerShell** i det övre vänstra hörnet i Cloud Shell-fönstret. Se exemplet **Cloud Shell** fönstret avbildning i Azure CLI-avsnitt. 
2. Kör följande kommando för att hämta den **endpoint** för ämnet: När du kopierar och klistrar in det, uppdaterar den **ämnesnamn** och **resursgruppens namn** innan du kör kommandot. 

    ```powershell
    $endpoint = (Get-AzEventGridTopic -ResourceGroupName <resource group name> -Name <topic name>).Endpoint
    ```
3. Kör följande kommando för att hämta den **nyckel** för det anpassade ämnet: När du kopierar och klistrar in det, uppdaterar den **ämnesnamn** och **resursgrupp** namn innan du kör kommandot.

    ```powershell
    $keys = Get-AzEventGridTopicKey -ResourceGroupName gridResourceGroup -Name <topic name>
    ```
4. Förbered händelsen. Kopiera och kör uttryck i Cloud Shell-fönstret. 

    ```azurepowershell
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
5. Använd den **Invoke-WebRequest** cmdlet för att skicka händelsen. 

    ```powershell
    Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
    ```

### <a name="verify-in-the-event-grid-viewer"></a>Kontrollera i Loggboken i rutnätet
Du har utlöst händelsen och Event Grid skickade meddelandet till den slutpunkt som du konfigurerade när du prenumererade. Visa din webbapp om du vill se händelsen som du har skickat.

```json
{
  "id": "974",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2019-03-28T01:11:59+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/myegridrg/providers/Microsoft.EventGrid/topics/myegridtopic"
}
```

## <a name="clean-up-resources"></a>Rensa resurser
Om du planerar att fortsätta arbeta med den här händelsen ska du inte rensa upp bland de resurser som skapades i den här artikeln. I annat fall tar du bort alla resurser som du har skapat i den här artikeln.

1. Välj **resursgrupper** på den vänstra menyn. Om du inte ser det på den vänstra menyn, Välj **alla tjänster** på den vänstra menyn och välj **resursgrupper**. 
2. Välj resursgrupp att starta den **resursgrupp** sidan. 
3. Välj **ta bort resursgrupp** i verktygsfältet. 
4. Bekräfta borttagningen genom att ange namnet på resursgruppen och välj **ta bort**. 

    ![Resursgrupper](./media/custom-event-quickstart-portal/delete-resource-groups.png)

    Den resursgrupp som du ser i bilden skapades och används av Cloud Shell-fönstret. Ta bort den om du inte planerar att använda Cloud Shell-fönstret senare. 

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du skapar anpassade ämnen och prenumerationer på händelser kan du läsa mer om vad Event Grid kan hjälpa dig med:

- [Om Event Grid](overview.md)
- [Dirigera Blob Storage-händelser till en anpassad webbslutpunkt](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Övervaka ändringar på virtuella maskiner med Azure Event Grid och Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Strömma stordata till ett datalager](event-grid-event-hubs-integration.md)
