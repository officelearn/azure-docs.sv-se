---
title: Uppdatera pris nivån för ett Azure Event Grid ämne eller en domän
description: Den här artikeln beskriver hur du uppdaterar pris nivån för ett Azure Event Grid ämne eller en domän (Basic till Premium, Premium till Basic) med Azure Portal, Azure CLI och Azure PowerShell.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: spelluru
ms.openlocfilehash: caea8d515964510fce432eb0497e3af19ecc1369
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82101645"
---
# <a name="update-pricing-tier"></a>Uppdatera prisnivå 
Den här artikeln visar hur du uppdaterar pris nivån för ett Azure Event Grid ämne eller en domän med hjälp av Azure Portal, Azure CLI och Azure PowerShell. 

## <a name="use-azure-portal"></a>Använda Azure-portalen
I det här avsnittet visas hur du ändrar pris nivån för ett ämne eller en domän i Azure Portal. 

### <a name="overview-page"></a>Översikts sida
Du kan ändra pris nivån för ett ämne eller en domän på sidan **Översikt** . I följande exempel visas hur du uppgraderar ett ämne från Basic-nivån till Premium-nivån. Stegen för att nedgradera från Premium-nivån till Basic-nivån liknar varandra.

1. Gå till sidan ämne eller domän i [Azure Portal](https://portal.azure.com). 
2. På sidan **Översikt** väljer du den aktuella pris nivån (i följande exempel är det **Basic**.)
    
    ![Välj aktuell pris nivå](./media/update-tier/select-tier.png)
3. På sidan **pris nivå** ändrar du nivån och väljer **OK**. 

    ![Uppdatera pris nivån](./media/update-tier/change-tier.png)
4. Kontrol lera status för åtgärden på sidan **meddelanden** .

    ![Uppdaterings status](./media/update-tier/status.png)    
5. Bekräfta att du ser den uppdaterade nivån på **översikts** sidan. 

    ![Uppdaterings status](./media/update-tier/changed-tier.png)

### <a name="networking-page"></a>Sidan nätverk
Du kan **Uppgradera** från Basic-nivån till Premium nivån på sidan **nätverk** . Du kan inte nedgradera från Premium-nivån till Basic-nivån på den här sidan. 

1. Gå till sidan ämne eller domän i [Azure Portal](https://portal.azure.com). 
2. På sidan **nätverk** växlar du till fliken **privat slut punkt anslutningar (förhands granskning)** . 
3. Om den aktuella pris nivån är **grundläggande**visas följande meddelande. Välj den. 

    ![Uppdatera nivån på sidan anslutningar för privata slut punkter](./media/update-tier/private-endpoint-connections-page.png)
4. På sidan **Uppdatera till Premium pris nivå väljer du** **Ja**. 
    
    ![Bekräfta uppgradering](./media/update-tier/message-private-endpoint-connection.png)
5. Kontrol lera status för åtgärden på sidan **meddelanden** .

    ![Uppdaterings status](./media/update-tier/status.png)



## <a name="use-azure-cli"></a>Använda Azure CLI
Det här avsnittet visar hur du använder Azure CLI-kommandon för att ändra pris nivån för ett ämne. Om du vill uppdatera pris nivån för en domän använder `az eventgrid domain update` du kommandot på liknande sätt.

### <a name="prerequisites"></a>Krav
Uppdatera Azure Event Grid-tillägget för CLI genom att köra följande kommando: 

```azurecli-interactive
az extension update -n eventgrid
```

Om tillägget inte är installerat kör du följande kommando för att installera det: 

```azurecli-interactive
az extension add -n eventgrid
```

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Uppgradera ett ämne från Basic till Premium

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --sku "Premium" 
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Nedgradera ett ämne från Premium till Basic

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --sku "Basic" 
```


## <a name="use-azure-powershell"></a>Använda Azure PowerShell
Det här avsnittet visar hur du använder PowerShell-kommandon för att ändra pris nivån för ett ämne eller en domän. 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Förbered token och rubriker för REST API-anrop 
Kör följande krav kommandon för att hämta en autentiseringstoken som ska användas med REST API-anrop, samt information om auktorisering och annan huvud information. 

```azurepowershell-interactive
# replace <CLIENT ID> and <CLIENT SECRET>
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get the authentication token. Replace <TENANT ID>
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token `
    -Body $body `
    -ContentType 'application/x-www-form-urlencoded'

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
$Headers.Add("Content-Type","application/json")
```

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Uppgradera ett ämne från Basic till Premium

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Nedgradera ett ämne från Premium till Basic

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>Uppgradera en domän från Basic till Premium

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>Nedgradera en domän från Premium till Basic

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

## <a name="next-steps"></a>Nästa steg
För ämnen och domäner i Premium-nivå kan du konfigurera privata slut punkter för att begränsa åtkomsten från endast valda virtuella nätverk. Stegvisa instruktioner finns i [Konfigurera privata slut punkter](configure-private-endpoints.md).
