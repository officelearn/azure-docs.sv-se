---
title: 'Snabbstart: Skicka anpassade händelser till Azure-funktionen - Event Grid'
description: 'Snabbstart: Använd Azure Event Grid och Azure CLI eller portal för att publicera ett ämne och prenumerera på den händelsen. En Azure-funktion används för slutpunkten.'
services: event-grid
keywords: ''
author: banisadr
ms.author: babanisa
ms.date: 11/15/2019
ms.topic: quickstart
ms.service: event-grid
ms.openlocfilehash: 5e38571cf84537fd722093b96cd277743e8ce80c
ms.sourcegitcommit: fab450a18a600d72b583ecfbe6c5e53afd43408c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80292158"
---
# <a name="quickstart-route-custom-events-to-an-azure-function-with-event-grid"></a>Snabbstart: Dirigera anpassade händelser till en Azure-funktion med händelserutnät

Azure Event Grid är en händelsetjänst för molnet. Azure Functions är en av de händelsehanterare som stöds. I den här artikeln använder du Azure-portalen för att skapa ett anpassat ämne, prenumerera på det anpassade ämnet och utlösa händelsen för att visa resultatet. Du skickar händelserna till en Azure-funktion.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-azure-function"></a>Skapa Azure-funktion

Innan du prenumererar på det anpassade ämnet ska vi skapa en funktion för att hantera händelserna. I Azure-portalen klickar du på "Skapa en resurs" och skriver "funktion" och väljer sedan "Funktionsapp" och klickar på Skapa. Välj Skapa nytt under resursgruppen och ge den ett namn. Du kommer att använda detta för resten av handledningen. Ge funktionsappen ett namn, lämna växlingsknappen "Publicera" på "Kod", välj valfri körning och region och tryck sedan på skapa.

När din funktionsapp är klar navigerar du till den och klickar på +Ny funktion. Välj "In-portal" för utvecklingsmiljö och träffen fortsätter. Under Skapa en funktion väljer du Fler mallar för att visa fler mallar och sedan söka efter Azure Event Grid-utlösare och välj den. Om det är första gången du använder den här utlösaren kan du behöva klicka på "Installera" för att installera tillägget.

![Utlösare av funktionshändelserutnätet](./media/custom-event-to-function/grid-trigger.png)

När du har installerat tillägget klickar du på Fortsätt, ger din funktion ett namn och trycker sedan på skapa.

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="create-a-custom-topic"></a>Skapa en anpassat ämne

Ett event grid-ämne tillhandahåller en användardefinierad slutpunkt där du publicerar dina händelser. 

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Välj **Alla tjänster** på menyn till vänster, sök efter **Händelserutnät**och välj **Ämnen för händelserutnät**. 

    ![Välj avsnitt om händelserutnät](./media/custom-event-to-function/select-event-grid-topics.png)
3. På sidan **Ämnen i händelserutnätet** väljer du **+ Lägg till** i verktygsfältet. 

    ![Knappen Lägg till ämne i händelserutnät](./media/custom-event-to-function/add-event-grid-topic-button.png)

4. Gör så här på sidan **Skapa ämne:**

    1. Ange ett unikt **namn** för det anpassade ämnet. Ämnesnamnet måste vara unikt eftersom det representeras av en DNS-post. Använd inte det namn som visas på bilden. I stället skapar du ett eget namn som måste bestå av 3–50 tecken och enbart får innehålla a-z, A-Z, 0-9 och ”-”.
    2. Välj din **Azure-prenumeration**.
    3. Markera samma resursgrupp i föregående steg.
    4. Välj en **plats** för händelserutnätets ämne.
    5. Behåll standardvärdet **Event Grid Schema** för fältet **Händelseschema.** 

       ![Sidan Skapa ämne](./media/custom-event-to-function/create-custom-topic.png)
    6. Välj **Skapa**. 

5. När du har skapat det anpassade ämnet visas ett meddelande om detta. Välj **Gå till resursgrupp**. 

   ![Se meddelandet](./media/custom-event-to-function/success-notification.png)

6. Välj avsnittet för händelserutnät på sidan **Resursgrupp.** 

   ![Välj ämnesresurs för händelserutnätet](./media/custom-event-to-function/select-event-grid-topic.png)

7. Du ser sidan Ämnessida för **händelserutnät** för händelserutnätet. Håll den här sidan öppen. Du använder den senare i snabbstarten. 

    ![Startsida för händelserutnätsämne](./media/custom-event-to-function/event-grid-topic-home-page.png)

## <a name="subscribe-to-custom-topic"></a>Prenumerera på anpassat ämne

Du prenumererar på ett Event Grid-ämne därför att du vill ange för Event Grid vilka händelser du vill följa och vart du vill skicka händelserna.

1. På sidan **Ämnesavsnitt för händelserutnät** för ditt anpassade ämne väljer du + **Händelseprenumeration** i verktygsfältet.

   ![Lägga till händelseprenumeration](./media/custom-event-to-function/new-event-subscription.png)

2. Gör så här på sidan **Skapa händelseprenumeration:**
    1. Ange ett **namn** på händelseprenumerationen.
    3. Välj **Azure-funktion** för **slutpunktstypen**. 
    4. Välj **Välj en slutpunkt**. 

       ![Ange värden för händelseprenumerationen](./media/custom-event-to-function/provide-subscription-values.png)

    5. För funktionsslutpunkten väljer du den Azure-prenumeration och resursgrupp som din funktionsapp finns i och väljer sedan funktionsappen och funktionen som du skapade tidigare. Välj **Bekräfta val**.

       ![Ange slutpunktens webbadress](./media/custom-event-to-function/provide-endpoint.png)

    6. Tillbaka på sidan **Skapa händelseprenumeration** väljer du **Skapa**.

## <a name="send-an-event-to-your-topic"></a>Skicka en händelse till ditt ämne

Nu ska vi utlösa en händelse och se hur Event Grid distribuerar meddelandet till slutpunkten. Du kan använda antingen Azure CLI eller PowerShell och skicka en testhändelse till det anpassade ämnet. Ett program eller en Azure-tjänst skulle vanligtvis skicka sådana händelsedata.

I det första exemplet används Azure CLI. URL och nyckel för det anpassade ämnet hämtas, och exempeldata för händelsen. Använd ditt anpassade ämnesnamn för `<topic name>`. Exempelhändelsedata skapas. Elementet `data` av JSON är händelsens nyttolast. All välformulerad JSON kan stå i det här fältet. Du kan också använda ämnesfältet för avancerad omdirigering och filtrering. CURL är ett verktyg som skickar HTTP-förfrågningar.


### <a name="azure-cli"></a>Azure CLI
1. Välj **Cloud Shell**i Azure-portalen . Välj **Bash** i det övre vänstra hörnet i Cloud Shell-fönstret. 

    ![Moln Skal - Bash](./media/custom-event-quickstart-portal/cloud-shell-bash.png)
1. Kör följande kommando för att hämta **slutpunkten** för ämnet: När du har kopierat och klistrat in kommandot uppdaterar du **ämnesnamnet** och **resursgruppsnamnet** innan du kör kommandot. 

    ```azurecli
    endpoint=$(az eventgrid topic show --name <topic name> -g <resource group name> --query "endpoint" --output tsv)
    ```
2. Kör följande kommando för att hämta **nyckeln** för det anpassade avsnittet: När du har kopierat och klistrat in kommandot uppdaterar du **ämnesnamnet** och **resursgruppsnamnet** innan du kör kommandot. 

    ```azurecli
    key=$(az eventgrid topic key list --name <topic name> -g <resource group name> --query "key1" --output tsv)
    ```
3. Kopiera följande uttryck med händelsedefinitionen och tryck på **RETUR**. 

    ```json
    event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
    ```
4. Kör följande **Curl-kommando** för att publicera händelsen:

    ```
    curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
    ```

### <a name="azure-powershell"></a>Azure PowerShell
I det andra exemplet används PowerShell för att utföra liknande steg.

1. I Azure-portalen väljer du **Cloud** `https://shell.azure.com/`Shell (alternativt gå till ). Välj **PowerShell** i det övre vänstra hörnet i Cloud Shell-fönstret. Se exempel på **Cloud Shell-fönsteravbildning** i avsnittet Azure CLI.
2. Ange följande variabler. När du har kopierat och klistrat in varje kommando uppdaterar du **ämnesnamnet** och **resursgruppsnamnet** innan du kör kommandot:

    ```powershell
    $resourceGroupName = <resource group name>
    $topicName = <topic name>
    ```
3. Kör följande kommandon för att hämta **slutpunkten** och **nycklarna** för ämnet:

    ```powershell
    $endpoint = (Get-AzEventGridTopic -ResourceGroupName $resourceGroupName -Name $topicName).Endpoint
    $keys = Get-AzEventGridTopicKey -ResourceGroupName $resourceGroupName -Name $topicName
    ```
4. Förbered händelsen. Kopiera och kör satserna i fönstret Cloud Shell. 

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
5. Använd **cmdleten Invoke-WebRequest** för att skicka händelsen. 

    ```powershell
    Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
    ```

### <a name="verify-in-the-event-grid-viewer"></a>Verifiera i loggboken för loggboken
Du har utlöst händelsen och Event Grid skickade meddelandet till den slutpunkt som du konfigurerade när du prenumererade. Navigera till funktionen Utlöst händelserutnät och öppna loggarna. Du bör se en kopia av datanyttolasten för händelsen i loggarna. Om du inte ser till att du öppnar loggfönstret först, eller trycker på återanslut och sedan försöker skicka en testhändelse igen.

![Logg för lyckad funktionsutlösare](./media/custom-event-to-function/successful-function.png)

## <a name="clean-up-resources"></a>Rensa resurser
Om du planerar att fortsätta arbeta med den här händelsen ska du inte rensa upp bland de resurser som skapades i den här artikeln. I annat fall tar du bort alla resurser som du har skapat i den här artikeln.

1. Välj **Resursgrupper** på den vänstra menyn. Om du inte ser det på den vänstra menyn väljer du **Alla tjänster** på den vänstra menyn och väljer **Resursgrupper**. 
2. Välj den resursgrupp som du vill starta sidan **Resursgrupp.** 
3. Välj **Ta bort resursgrupp** i verktygsfältet. 
4. Bekräfta borttagning genom att ange namnet på resursgruppen och välj **Ta bort**. 

    ![Resursgrupper](./media/custom-event-to-function/delete-resource-groups.png)

    Den andra resursgruppen som visas i avbildningen skapades och användes av cloud shell-fönstret. Ta bort den om du inte planerar att använda Cloud Shell-fönstret senare. 

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du skapar ämnen och prenumerationer på händelser kan du läsa mer om vad Event Grid kan hjälpa dig med:

- [Om Event Grid](overview.md)
- [Dirigera Blob Storage-händelser till en anpassad webbslutpunkt](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Övervaka ändringar på virtuella maskiner med Azure Event Grid och Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Strömma stordata till ett datalager](event-grid-event-hubs-integration.md)
