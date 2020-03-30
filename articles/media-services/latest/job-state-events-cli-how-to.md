---
title: Övervaka Azure Media Services-händelser med Event Grid med CLI | Microsoft-dokument
description: Den här artikeln visar hur du prenumererar på Event Grid för att övervaka Azure Media Services-händelser.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 11/09/2018
ms.author: juliako
ms.openlocfilehash: 619d40ab56715b4444d8e5649c7fb3401b3f57ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71937291"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-cli"></a>Skapa och övervaka Media Services-händelser med Event Grid med Hjälp av Azure CLI

Azure Event Grid är en händelsetjänst för molnet. Den här tjänsten använder [händelseprenumerationer](../../event-grid/concepts.md#event-subscriptions) för att dirigera händelsemeddelanden till prenumeranter. Media Services-händelser innehåller all information du behöver för att svara på ändringar i dina data. Du kan identifiera en Media Services-händelse eftersom eventType-egenskapen börjar med "Microsoft.Media". Mer information finns i [Media Services-händelsescheman](media-services-event-schemas.md).

I den här artikeln använder du Azure CLI för att prenumerera på händelser för ditt Azure Media Services-konto. Sedan utlöser du händelser för att visa resultatet. Normalt kan du skicka händelser till en slutpunkt som bearbetar informationen om händelsen och utför åtgärder. I den här artikeln skickar du händelserna till en webbapp som samlar in och visar meddelandena.

## <a name="prerequisites"></a>Krav

- En aktiv Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) konto innan du börjar.
- Installera och använd CLI lokalt – du måste ha Azure CLI version 2.0 eller senare. Kör `az --version` för att se vilken version du har. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). 

    För närvarande fungerar inte alla [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref)-kommandon i Azure Cloud Shell. Vi rekommenderar att du använder CLI lokalt.

- [Skapa ett Media Services-konto](create-account-cli-how-to.md).

    Se till att komma ihåg de värden som du använde för resursgruppens namn och namnet på Media Services-kontot.

## <a name="create-a-message-endpoint"></a>Skapa en slutpunkt för meddelanden

Innan du prenumererar på händelserna för Media Services-kontot ska vi skapa slutpunkten för händelsemeddelandet. Slutpunkten utför vanligtvis åtgärder baserat på informationen om händelsen. I den här artikeln distribuerar du en [färdig webbapp](https://github.com/Azure-Samples/azure-event-grid-viewer) som visar händelsemeddelandena. Den distribuerade lösningen innehåller en App Service-plan,en webbapp för App Service och källkod från GitHub.

1. Välj **Deploy to Azure** (Distribuera till Azure) för att distribuera lösningen till din prenumeration. Ange parametervärdena i Azure Portal.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. Det kan ta några minuter att slutföra distributionen. Efter distributionen har slutförts kan du visa webbappen för att kontrollera att den körs. I en webbläsare navigerar du till: `https://<your-site-name>.azurewebsites.net`

Om du växlar till webbplatsen "Azure Event Grid Viewer" ser du att den inte har några händelser ännu.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="set-the-azure-subscription"></a>Ange Azure-prenumeration

Med följande kommando anger du ID för den Azure-prenumeration som du vill använda för Media Services-kontot. Du kan se en lista över prenumerationer som du har åtkomst till under [Prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli
az account set --subscription mySubscriptionId
```

## <a name="subscribe-to-media-services-events"></a>Prenumerera på Media Services-evenemang

Du prenumererar på en artikel för att berätta för Event Grid vilka händelser du vill spåra. I följande exempel prenumererar du på det Media Services-konto som du skapade och skickar webbadressen från webbplatsen som du skapade som slutpunkt för händelseavisering. 

Ersätt `<event_subscription_name>` med ett unikt namn för din händelseprenumeration. För `<resource_group_name>` `<ams_account_name>`och använder du de värden som du använde när du skapade Media Services-kontot. För `<endpoint_URL>`anger du webbadressen till webbappen och lägger `api/updates` till webbadressen till startsidan. Genom att ange slutpunkten när du prenumererar hanterar Event Grid routningen av händelser till den slutpunkten. 

1. Hämta resurs-ID

    ```azurecli
    amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)
    ```

    Ett exempel:

    ```
    amsResourceId=$(az ams account show --name amsaccount --resource-group amsResourceGroup --query id --output tsv)
    ```

2. Prenumerera på evenemangen

    ```azurecli
    az eventgrid event-subscription create \
    --source-resource-id $amsResourceId \
    --name <event_subscription_name> \
    --endpoint <endpoint_URL>
    ```

    Ett exempel:

    ```
    az eventgrid event-subscription create --source-resource-id $amsResourceId --name amsTestEventSubscription --endpoint https://amstesteventgrid.azurewebsites.net/api/updates/
    ```    

    > [!TIP]
    > Du kan få varning för handskakning för validering. Ge det några minuter och handskakningen bör validera.

Nu ska vi utlösa händelser för att se hur Event Grid distribuerar meddelandet till slutpunkten.

## <a name="send-an-event-to-your-endpoint"></a>Skicka en händelse till din slutpunkt

Du kan utlösa händelser för Media Services-kontot genom att köra ett kodningsjobb. Du kan följa [den här snabbstarten](stream-files-dotnet-quickstart.md) för att koda en fil och börja skicka händelser. 

Visa ditt webbprogram igen och observera att en händelse för verifieringen av prenumerationen har skickats till den. Händelserutnätet skickar valideringshändelsen så att slutpunkten kan bekräfta att den vill ta emot händelsedata. Slutpunkten måste ställas `validationResponse` `validationCode`in på . Mer information finns i [Säkerhet och autentisering av Händelserutnät](../../event-grid/security-authentication.md). Du kan visa webbappkoden för att se hur den validerar prenumerationen.

> [!TIP]
> Välj ögonikonen för att utöka informationen om händelsen. Uppdatera inte sidan om du vill visa alla händelser.

![Visa prenumerationshändelse](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Nästa steg

[Ladda upp, koda och strömma](stream-files-tutorial-with-api.md)

