---
title: 'Snabbstart: Skicka Blob-lagringshändelser till webbslutpunkten - portal'
description: 'Snabbstart: Använd Azure Event Grid och Azure-portalen för att skapa Blob-lagringskonto och prenumerera på dess händelser. Skicka händelserna till en Webhook.'
services: event-grid
keywords: ''
author: spelluru
ms.author: spelluru
ms.date: 04/16/2020
ms.topic: quickstart
ms.service: event-grid
ms.custom: seodec18
ms.openlocfilehash: ada451b6bb3578a2903e9bd832b98981d7029d1d
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605609"
---
# <a name="quickstart-route-blob-storage-events-to-web-endpoint-with-the-azure-portal"></a>Snabbstart: Route Blob storage events to web endpoint with the Azure portal Snabbstart: Route Blob storage events to web endpoint with the Azure portal Snabbstart: Route Blob storage events to web endpoint with the Azure portal Snabbstart

Azure Event Grid är en händelsetjänst för molnet. I den här artikeln använder du Azure Portal för att skapa ett Blob Storage-konto, prenumerera på händelser för blobblagringen och utlösa en händelse för att visa resultatet. Normalt kan du skicka händelser till en slutpunkt som bearbetar informationen om händelsen och utför åtgärder. Men för att enkelt beskriva den här artikeln kan skicka du händelser till en webbapp som samlar in och visar meddelanden.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

När du är klar kan se du att händelsedata som har skickats till webbappen.

![Visa resultat](./media/blob-event-quickstart-portal/view-results.png)

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. Om du vill skapa ett Blob Storage-konto väljer du **Skapa en resurs**. 

1. Välj **Storage** (Lagring) för att filtrera de tillgängliga alternativen och välj **Storage-konto – blob, fil, tabell, kö**.

   ![Välj Storage](./media/blob-event-quickstart-portal/create-storage.png)

   Skapa ett v2-lagringskonto för allmän användning eller ett Blob Storage-konto om du vill prenumerera på händelser.
   
1. Gör följande på sidan **Skapa lagringskonto:**
    1. Välj din Azure-prenumeration. 
    2. Skapa en ny resursgrupp för **resursgruppen**eller välj en befintlig resursgrupp. 
    3. Ange namnet på lagringskontot. 
    4. Välj **Granska + skapa**. 

       ![Startsteg](./media/blob-event-quickstart-portal/provide-blob-values.png)    
    5. På sidan **Granska + skapa** granskar du inställningarna och väljer **Skapa**. 

        >[!NOTE]
        > Endast lagringskonton av typen **StorageV2 (general purpose v2)** och **BlobStorage** stöder händelseintegrering. **Lagring (genral syfte v1)** stöder *inte* integrering med Event Grid.

## <a name="create-a-message-endpoint"></a>Skapa en slutpunkt för meddelanden

Innan du prenumererar på händelserna för Blob Storage-kontot ska vi skapa slutpunkten för händelsemeddelandet. Slutpunkten utför vanligtvis åtgärder baserat på informationen om händelsen. För att förenkla den här snabbstarten kan du distribuera en [förskapad webbapp](https://github.com/Azure-Samples/azure-event-grid-viewer) som visar meddelanden om händelser. Den distribuerade lösningen innehåller en App Service-plan,en webbapp för App Service och källkod från GitHub.

1. Välj **Deploy to Azure** (Distribuera till Azure) för att distribuera lösningen till din prenumeration. 

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
2. Gör följande på sidan **Anpassad distribution:** 
    1. För **resursgrupp**väljer du den resursgrupp som du skapade när du skapade lagringskontot. Det blir lättare för dig att rensa när du är klar med självstudien genom att ta bort resursgruppen.  
    2. För **Webbplatsnamn**anger du ett namn på webbappen.
    3. För **värdplannamn**anger du ett namn på apptjänstplanen som ska användas för att vara värd för webbappen.
    4. Markera kryssrutan för **jag godkänner de villkor som anges ovan**. 
    5. Välj **Köp**. 

       ![Distributionsparametrar](./media/blob-event-quickstart-portal/template-deploy-parameters.png)
1. Det kan ta några minuter att slutföra distributionen. Välj Aviseringar (klockikon) i portalen och välj sedan **Gå till resursgrupp**. 

    ![Avisering - navigera till resursgrupp](./media/blob-event-quickstart-portal/navigate-resource-group.png)
4. Välj **Resource group** den webbapp som du skapade i listan över resurser i listan över resurser. Du ser också App Service-planen och lagringskontot i den här listan. 

    ![Välj webbplats](./media/blob-event-quickstart-portal/resource-group-resources.png)
5. På sidan **App service** för din webbapp väljer du url:en för att navigera till webbplatsen. Webbadressen ska vara i `https://<your-site-name>.azurewebsites.net`det här formatet: .
    
    ![Navigera till webbplatsen](./media/blob-event-quickstart-portal/web-site.png)

6. Bekräfta att du ser webbplatsen men inga händelser har lagts upp på den ännu.

   ![Visa ny webbplats](./media/blob-event-quickstart-portal/view-site.png)

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-the-blob-storage"></a>Prenumerera på Blob Storage

Du prenumererar på ett ämne därför att du vill ange för Event Grid vilka händelser du vill följa och vart du vill skicka händelserna.

1. I portalen navigerar du till ditt Azure Storage-konto som du skapade tidigare. På den vänstra menyn väljer du **Alla resurser** och väljer ditt lagringskonto. 
2. På sidan **Lagringskonto** väljer du **Händelser** på den vänstra menyn.
1. Välj **Fler alternativ** och **Webhook**. Du skickar händelser till din tittarapp med hjälp av en webbkrok för slutpunkten. 

   ![Välj webhook](./media/blob-event-quickstart-portal/select-web-hook.png)
3. Gör följande på sidan **Skapa händelseprenumeration:** 
    1. Ange ett **namn** på händelseprenumerationen.
    2. Välj **Webbkrok** för **slutpunktstyp**. 

       ![Välj slutpunktstyp för webbkrok](./media/blob-event-quickstart-portal/select-web-hook-end-point-type.png)
4. För **Slutpunkt**klickar du på **Välj en slutpunkt**och `api/updates` anger webbadressen till webbappen och lägger till webbadressen till startsidan (till exempel: `https://spegridsite.azurewebsites.net/api/updates`) och väljer sedan Bekräfta **markering**.

   ![Bekräfta markering av slutpunkt](./media/blob-event-quickstart-portal/confirm-endpoint-selection.png)
5. På sidan **Skapa händelseprenumeration** **väljer** du Skapa för att skapa händelseprenumerationen. 

   ![Välj loggar](./media/blob-event-quickstart-portal/create-subscription.png)

1. Visa ditt webbprogram igen och observera att en händelse för verifieringen av prenumerationen har skickats till den. Välj ögonikonen för att utöka informationen om händelsen. Händelserutnätet skickar valideringshändelsen så att slutpunkten kan bekräfta att den vill ta emot händelsedata. Webbappen inkluderar kod för att verifiera prenumerationen.

   ![Visa prenumerationshändelse](./media/blob-event-quickstart-portal/view-subscription-event.png)

Nu ska vi utlösa en händelse och se hur Event Grid distribuerar meddelandet till slutpunkten.

## <a name="send-an-event-to-your-endpoint"></a>Skicka en händelse till din slutpunkt

Du kan utlösa en händelse för Blob Storage-kontot genom att ladda upp en fil. Filen behöver inte innehålla något specifikt. Artiklarna förutsätter att du har en fil med namnet testfile.txt, men du kan använda vilken fil som helst.

1. I Azure-portalen navigerar du till ditt Blob-lagringskonto och väljer **Behållare** på **sidan Översikt.**

   ![Välj blobbar](./media/blob-event-quickstart-portal/select-blobs.png)

1. Välj **+ Container**. Ge dig en behållare ett namn och använd valfri åtkomstnivå och välj **Skapa**. 

   ![Lägga till containern](./media/blob-event-quickstart-portal/add-container.png)

1. Välj din nya container.

   ![Välj container](./media/blob-event-quickstart-portal/select-container.png)

1. Välj **Ladda upp** för att ladda upp en fil. På sidan **Ladda upp blob** bläddrar du och väljer en fil som du vill ladda upp för testning och väljer sedan **Ladda upp** på den sidan. 

   ![Välj Överför](./media/blob-event-quickstart-portal/upload-file.png)

1. Bläddra till testfilen och ladda upp den.

1. Du har utlöst händelsen och Event Grid skickade meddelandet till den slutpunkt som du konfigurerade när du prenumererade. Meddelandet är i JSON-format och det innehåller en matris med en eller flera händelser. I följande exempel innehåller JSON-meddelandet en matris med en händelse. Visa din webbapp och lägg märke till att en **blob skapad** händelse togs emot. 

   ![Blob skapad händelse](./media/blob-event-quickstart-portal/blob-created-event.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta arbeta med den här händelsen ska du inte rensa upp bland de resurser som skapades i den här artikeln. I annat fall tar du bort alla resurser som du har skapat i den här artikeln.

Välj resursgruppen och sedan **Ta bort resursgrupp**.

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du skapar anpassade ämnen och prenumerationer på händelser kan du läsa mer om vad Event Grid kan hjälpa dig med:

- [Om Event Grid](overview.md)
- [Dirigera Blob Storage-händelser till en anpassad webbslutpunkt](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Övervaka ändringar på virtuella maskiner med Azure Event Grid och Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Strömma stordata till ett datalager](event-grid-event-hubs-integration.md)
