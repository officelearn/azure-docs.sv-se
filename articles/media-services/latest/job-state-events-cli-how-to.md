---
title: Övervaka Azure Media Services-händelser med Event Grid med CLI | Microsoft Docs
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
ms.openlocfilehash: 16f964c6f881777e0217979a329610902b29a87b
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2018
ms.locfileid: "51612636"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-cli"></a>Skapa och övervaka Media Services-händelser med Event Grid med Azure CLI

Azure Event Grid är en händelsetjänst för molnet. I den här artikeln använder du Azure CLI för att prenumerera på händelser för Azure Media Services-kontot. Sedan kan utlösa du händelser för att visa resultatet. Normalt kan du skicka händelser till en slutpunkt som bearbetar informationen om händelsen och utför åtgärder. I den här artikeln får skicka du händelser till en webbapp som samlar in och visar meddelandena.

## <a name="prerequisites"></a>Förutsättningar

- En aktiv Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
- Installera och använda CLI lokalt kan du ha Azure CLI version 2.0 eller senare. Kör `az --version` för att se vilken version du har. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). 

    För närvarande inte alla [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) kommandona fungerar i Azure Cloud Shell. Det rekommenderas att använda CLI lokalt.

- [Skapa ett Media Services-konto](create-account-cli-how-to.md).

    Se till att komma ihåg de värden som du använde för resursgruppens namn och namnet på Media Services-konto.

## <a name="create-a-message-endpoint"></a>Skapa en slutpunkt för meddelanden

Innan du prenumererar på händelser för Media Services-kontot ska vi ska slutpunkten för händelsemeddelandet. Slutpunkten utför vanligtvis åtgärder baserat på informationen om händelsen. I den här artikeln får du distribuera en [färdiga webbapp](https://github.com/Azure-Samples/azure-event-grid-viewer) som visar meddelandena som händelsen. Den distribuerade lösningen innehåller en App Service-plan,en webbapp för App Service och källkod från GitHub.

1. Välj **Deploy to Azure** (Distribuera till Azure) för att distribuera lösningen till din prenumeration. Ange parametervärdena i Azure Portal.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. Det kan ta några minuter att slutföra distributionen. Efter distributionen har slutförts kan du visa webbappen för att kontrollera att den körs. I en webbläsare navigerar du till: `https://<your-site-name>.azurewebsites.net`

Om du byter till webbplatsen ”Azure Grid Loggboken” kan se du den har inga händelser ännu.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="set-the-azure-subscription"></a>Ange Azure-prenumeration

Med följande kommando anger du ID för den Azure-prenumeration som du vill använda för Media Services-kontot. Du kan se en lista över prenumerationer som du har åtkomst till under [Prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli
az account set --subscription mySubscriptionId
```

## <a name="subscribe-to-media-services-events"></a>Prenumerera på händelser för Media Services

Du prenumererar på en artikel som talar om Event Grid vilka händelser som du vill spåra. I följande exempel prenumererar vi på Media Services-konto du just skapat, och URL: en från webbplatsen som du har skapat som slutpunkt för händelseavisering. 

Ersätt `<event_subscription_name>` med ett unikt namn för din händelseprenumeration. För `<resource_group_name>` och `<ams_account_name>`, använder du värden som du använde när du skapar Media Services-kontot. För den `<endpoint_URL>`anger URL: en för webbappen och lägga till `api/updates` till den URL-Adressen. Genom att ange slutpunkten när du prenumererar kan hantera Event Grid omdirigeringen av händelser till denna slutpunkt. 

1. Hämta resurs-id

    ```azurecli
    amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)
    ```

    Exempel:

    ```
    amsResourceId=$(az ams account show --name amsaccount --resource-group amsResourceGroup --query id --output tsv)
    ```

2. Prenumerera på händelser

    ```azurecli
    az eventgrid event-subscription create \
    --resource-id $amsResourceId \
    --name <event_subscription_name> \
    --endpoint <endpoint_URL>
    ```

    Exempel:

    ```
    az eventgrid event-subscription create --resource-id $amsResourceId --name amsTestEventSubscription --endpoint https://amstesteventgrid.azurewebsites.net/api/updates/
    ```    

    > [!TIP]
    > Du kan få verifieringsvarning handskakning. Ge det ett par minuter och handskakningen bör verifiera.

Nu ska vi utlösa händelser för att se hur Event Grid distribuerar meddelandet till slutpunkten.

## <a name="send-an-event-to-your-endpoint"></a>Skicka en händelse till din slutpunkt

Du kan utlösa händelser för Media Services-kontot genom att köra ett kodningsjobb. Du kan följa [snabbstarten](stream-files-dotnet-quickstart.md) att koda en fil och börja skicka händelser. 

Visa ditt webbprogram igen och observera att en händelse för verifieringen av prenumerationen har skickats till den. Händelserutnätet skickar valideringshändelsen så att slutpunkten kan bekräfta att den vill ta emot händelsedata. Slutpunkten har att ställa in `validationResponse` till `validationCode`. Mer information finns i [Event Grid säkerhet och autentisering](../../event-grid/security-authentication.md). Du kan visa koden för webbappen om du vill se hur den validerar prenumerationen.

> [!TIP]
> Välj ögonikonen för att utöka informationen om händelsen. Uppdatera inte sidan, om du vill visa alla händelser.

![Visa prenumerationshändelse](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Nästa steg

[Ladda upp, koda och strömma](stream-files-tutorial-with-api.md)

