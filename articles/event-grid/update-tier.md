---
title: Uppdatera prisnivån för ett Azure Event Grid-ämne eller -domän
description: I den här artikeln beskrivs hur du uppdaterar prisnivån för ett Azure Event Grid-ämne eller -domän (grundläggande för premium, premium till grundläggande) med Azure-portalen, Azure CLI och Azure PowerShell.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: 1e92a9f0b9ba007b289c634995b04f4f1575310a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79300146"
---
# <a name="update-pricing-tier"></a>Uppdatera prisnivå 
Den här artikeln visar hur du uppdaterar prisnivån för ett Azure Event Grid-ämne eller domän med hjälp av Azure Portal, Azure CLI och Azure PowerShell. 

## <a name="use-azure-portal"></a>Använda Azure-portalen
Det här avsnittet visar hur du ändrar prisnivån för ett ämne eller en domän i Azure-portalen. 

### <a name="overview-page"></a>Översiktssida
Du kan ändra prisnivån för ett ämne eller en domän på sidan **Översikt.** I följande exempel visas hur du uppgraderar ett ämne från den grundläggande nivån till premiumnivån. Stegen för att nedgradera från premiumnivån till grundläggande nivå är likartade.

1. Navigera till ämnet eller domänsidan i [Azure-portalen.](https://portal.azure.com) 
2. På sidan **Översikt** väljer du den aktuella prisnivån (i följande exempel är det **grundläggande**.)
    
    ![Välj den aktuella prisnivån](./media/update-tier/select-tier.png)
3. På sidan **Prisnivå** ändrar du nivån och väljer **OK**. 

    ![Uppdatera prisnivån](./media/update-tier/change-tier.png)
4. Kontrollera status för åtgärden på sidan **Meddelanden.**

    ![Uppdatera status](./media/update-tier/status.png)    
5. Bekräfta att du ser den uppdaterade nivån på sidan **Översikt.** 

    ![Uppdatera status](./media/update-tier/changed-tier.png)

### <a name="networking-page"></a>Sidan Nätverk
Du kan **uppgradera** från den grundläggande nivån till premiumnivån på sidan **Nätverk.** Du kan inte nedgradera från premiumnivån till den grundläggande nivån på den här sidan. 

1. Navigera till ämnet eller domänsidan i [Azure-portalen.](https://portal.azure.com) 
2. På sidan **Nätverk** växlar du till fliken **Privata slutpunktsanslutningar (förhandsgranskning).** 
3. Om den aktuella prisnivån är **grundläggande**visas följande meddelande. Välj den. 

    ![Uppdatera nivå på sidan privata slutpunktsanslutningar](./media/update-tier/private-endpoint-connections-page.png)
4. På sidan **Uppdatera till premiumprisnivå** väljer du **Ja**. 
    
    ![Bekräfta uppgradering](./media/update-tier/message-private-endpoint-connection.png)
5. Kontrollera status för åtgärden på sidan **Meddelanden.**

    ![Uppdatera status](./media/update-tier/status.png)



## <a name="use-azure-cli"></a>Använda Azure CLI
I det här avsnittet visas hur du använder Azure CLI-kommandon för att ändra prisnivån för ett ämne eller en domän. 

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Uppgradera ett ämne från grundläggande till premium

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""premium\""}}"
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Nedgradera ett ämne från premium till grundläggande

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""basic\""}}"
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>Uppgradera en domän från grundläggande till premium

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""premium\""}}"
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>Nedgradera en domän från premium till grundläggande

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""basic\""}}"
```



## <a name="use-azure-powershell"></a>Använda Azure PowerShell
I det här avsnittet visas hur du använder PowerShell-kommandon för att ändra prisnivån för ett ämne eller en domän. 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Förbereda token och rubriker för REST API-anrop 
Kör följande nödvändiga kommandon för att hämta en autentiseringstoken som ska användas med REST API-anrop och auktorisering och annan rubrikinformation. 

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

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Uppgradera ett ämne från grundläggande till premium

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Nedgradera ett ämne från premium till grundläggande

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>Uppgradera en domän från grundläggande till premium

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>Nedgradera en domän från premium till grundläggande

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

## <a name="next-steps"></a>Nästa steg
För avsnitt och domäner på premiumnivå kan du konfigurera privata slutpunkter för att begränsa åtkomsten från endast valda virtuella nätverk. Steg-för-steg-instruktioner finns i [Konfigurera privata slutpunkter](configure-private-endpoints.md).
