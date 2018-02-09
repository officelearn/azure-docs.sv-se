---
title: "Anpassade händelser för Azure Event Grid med Azure Portal | Microsoft Docs"
description: "Använd Azure Event Grid och PowerShell för att publicera ett ämne och prenumerera på händelsen."
services: event-grid
keywords: 
author: tfitzmac
ms.author: tomfitz
ms.date: 01/30/2018
ms.topic: hero-article
ms.service: event-grid
ms.openlocfilehash: 01472ffc7a98cd2c99793c8675efe2cefffe5558
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="create-and-route-custom-events-with-the-azure-portal-and-event-grid"></a>Skapa och dirigera anpassade händelser med Azure Portal och Event Grid

Azure Event Grid är en händelsetjänst för molnet. I den här artikeln använder du Azure Portal för att skapa ett anpassat ämne, prenumerera på ämnet och utlösa händelsen för att visa resultatet. Normalt kan du skicka händelser till en slutpunkt som svarar på händelsen, exempelvis en webhook eller Azure Function. Men för att enkelt beskriva den här artikeln kan skicka du händelser till en URL som endast samlar in meddelanden. Du skapar den här URL:en med hjälp av ett tredjepartsverktyg från antingen [RequestBin](https://requestb.in/) eller [Hookbin](https://hookbin.com/).

>[!NOTE]
>**RequestBin** och **Hookbin** är inte avsedda för användning med stora dataflöden. Här används verktygen endast i demonstrativt syfte. Om du push-överför fler än en händelse i taget kanske du inte ser alla händelser i verktyget.

När du är klar kan se du att händelsedata som har skickats till en slutpunkt.

![Händelsedata](./media/custom-event-quickstart-portal/request-result.png)

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Event Grid-ämnen är Azure-resurser och måste placeras i en Azure-resursgrupp. Resursgruppen är en logisk samling där Azure-resurser distribueras och hanteras.

1. Välj **Resursgrupper** i det vänstra navigeringsfönstret. Välj sedan **Lägg till**.

   ![Skapa resursgrupp](./media/custom-event-quickstart-portal/create-resource-group.png)

1. Ange resursgruppnamnet som *gridResourceGroup* och platsen som *westus2*. Välj **Skapa**.

   ![Ange resursgruppvärden](./media/custom-event-quickstart-portal/provide-resource-group-values.png)

## <a name="create-a-custom-topic"></a>Skapa en anpassat ämne

Ett ämne ger en användardefinierad slutpunkt där du publicerar dina händelser. 

1. Välj **Fler tjänster** och sök efter *event grid* för att skapa ett ämne i resursgruppen. Välj **Event Grid-avsnitt** från de tillgängliga alternativen.

   ![Skapa ett event grid-ämne](./media/custom-event-quickstart-portal/create-event-grid-topic.png)

1. Välj **Lägg till**.

   ![Lägg till ett event grid-ämne](./media/custom-event-quickstart-portal/add-topic.png)

1. Tillhandahåll ett namn för ämnet. Ämnesnamnet måste vara unikt eftersom det representeras av en DNS-post. Välj en av de [regioner som stöds](overview.md). Välj den resursgrupp som du skapade tidigare. Välj **Skapa**.

   ![Ange värden för event grid-ämnet](./media/custom-event-quickstart-portal/provide-topic-values.png)

1. När avsnittet har skapats väljer du **Uppdatera** för att visa avsnittet.

   ![Visa ett event grid-ämne](./media/custom-event-quickstart-portal/see-topic.png)

## <a name="create-a-message-endpoint"></a>Skapa en slutpunkt för meddelanden

Innan du prenumererar på ämnet ska vi ska slutpunkten för händelsemeddelandet. I stället för att skriva kod för att svar på händelsen ska vi skapa en slutpunkt som samlar in meddelandena så att du kan visa dem. RequestBin och Hookbin är tredjepartsverktyg med öppen källkod som låter dig skapa en slutpunkt och visa förfrågningar som skickas till den. Gå till [RequestBin](https://requestb.in/) och klicka på **Skapa en RequestBin**, eller gå till [Hookbin](https://hookbin.com/) och klicka på **Skapa ny slutpunkt**.  Kopiera lagerplatsens URL eftersom du behöver den för att prenumerera på ämnet.

## <a name="subscribe-to-a-topic"></a>Prenumerera på ett ämne

Du prenumererar på ett ämne för att ange för Event Grid vilka händelser du vill följa. 

1. Om du vill skapa en Event Grid-prenumeration väljer du återigen **Fler tjänster** och söker efter *event grid*. Välj **Event Grid-prenumeration** bland de tillgängliga alternativen.

   ![Skapa event grid-prenumeration](./media/custom-event-quickstart-portal/create-subscription.png)

1. Välj **+ Händelseprenumeration**.

   ![Lägg till event grid-prenumeration](./media/custom-event-quickstart-portal/add-subscription.png)

1. Ange ett unikt namn för din händelseprenumeration. Välj **Event Grid-ämnen** som ämnestyp. Välj det anpassade ämne som du skapade för instansen. Ange URL:en från RequestBin eller Hookbin som slutpunkt för händelseaviseringen. När du är klar med att ange värden väljer du **Skapa**.

   ![Ange värde för event grid-prenumeration](./media/custom-event-quickstart-portal/provide-subscription-values.png)

Nu ska vi utlösa en händelse och se hur Event Grid distribuerar meddelandet till slutpunkten. För att förenkla den här artikeln kan du använda Cloud Shell för att skicka exempelhändelsedata till ämnet. Ett program eller en Azure-tjänst skulle vanligtvis skicka sådana händelsedata.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="send-an-event-to-your-topic"></a>Skicka en händelse till ditt ämne

Först måste vi ta fram URL och nyckel för ämnet. Använd ditt ämnesnamn för `<topic_name>`.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

I följande exempel används exempelhändelsedata:

```azurecli-interactive
body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/customevent.json)'")
```

Om du `echo "$body"` kan du se den fullständiga händelsen. Elementet `data` av JSON är händelsens nyttolast. All välformulerad JSON kan stå i det här fältet. Du kan också använda ämnesfältet för avancerad omdirigering och filtrering.

CURL är ett verktyg som utför HTTP-begäran. I den här artikeln använder vi CURL till att skicka en händelse till ämnet. 

```azurecli-interactive
curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

Du har utlöst händelsen och Event Grid skickade meddelandet till den slutpunkt du konfigurerade när du startade prenumerationen. Bläddra till slutpunktsadress du skapade tidigare. Du kan också klicka på Uppdatera i webbläsaren du har öppen. Du kan se den händelse du just skickade.

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

Om du planerar att fortsätta arbeta med den här händelsen ska du inte rensa upp bland de resurser som skapades i den här artikeln. Om du inte planerar att fortsätta kan du ta bort alla resurser som har skapats i den här artikeln.

Välj resursgruppen och sedan **Ta bort resursgrupp**.

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du skapar ämnen och prenumerationer på händelser kan du läsa mer om vad Event Grid kan hjälpa dig med:

- [Om Event Grid](overview.md)
- [Dirigera Blob Storage-händelser till en anpassad webbslutpunkt](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Övervaka ändringar på virtuella maskiner med Azure Event Grid och Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Strömma stordata till ett datalager](event-grid-event-hubs-integration.md)
