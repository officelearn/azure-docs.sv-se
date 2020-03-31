---
title: Fråga Azure Event Grid-prenumerationer
description: I den här artikeln beskrivs hur du listar Event Grid-prenumerationer i din Azure-prenumeration. Du anger olika parametrar baserat på typen av prenumeration.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 2da4ba5946f16092c1d0918aec8dc3109b5fd4a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721553"
---
# <a name="query-event-grid-subscriptions"></a>Frågehändelserutnätsprenumerationer 

I den här artikeln beskrivs hur du listar Event Grid-prenumerationerna i din Azure-prenumeration. När du frågar dina befintliga Event Grid-prenumerationer är det viktigt att förstå de olika typerna av prenumerationer. Du anger olika parametrar baserat på vilken typ av prenumeration du vill få.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-groups-and-azure-subscriptions"></a>Resursgrupper och Azure-prenumerationer

Azure-prenumerationer och resursgrupper är inte Azure-resurser. Därför har händelserutnätsprenumerationer till resursgrupper eller Azure-prenumerationer inte samma egenskaper som event grid-prenumerationer till Azure-resurser. Event grid-prenumerationer på resursgrupper eller Azure-prenumerationer betraktas som globala.

För att få prenumerationer på händelserutnät för en Azure-prenumeration och dess resursgrupper behöver du inte ange några parametrar. Kontrollera att du har valt den Azure-prenumeration som du vill fråga. Följande exempel får inte prenumerationer på händelsenät för anpassade ämnen eller Azure-resurser.

Om du använder Azure CLI använder du:

```azurecli-interactive
az account set -s "My Azure Subscription"
az eventgrid event-subscription list
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
Set-AzContext -Subscription "My Azure Subscription"
Get-AzEventGridSubscription
```

Om du vill hämta prenumerationer på eventrutnät för en Azure-prenumeration anger du ämnestypen **för Microsoft.Resources.Subscriptions**.

Om du använder Azure CLI använder du:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.Subscriptions" --location global
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.Subscriptions"
```

Om du vill hämta prenumerationer på händelserutnät för alla resursgrupper i en Azure-prenumeration anger du ämnestypen **för Microsoft.Resources.ResourceGroups**.

Om du använder Azure CLI använder du:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.ResourceGroups" --location global
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.ResourceGroups"
```

Om du vill hämta prenumerationer på händelserutnät för en angiven resursgrupp anger du namnet på resursgruppen som en parameter.

Om du använder Azure CLI använder du:

```azurecli-interactive
az eventgrid event-subscription list --resource-group myResourceGroup --location global
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
Get-AzEventGridSubscription -ResourceGroupName myResourceGroup
```

## <a name="custom-topics-and-azure-resources"></a>Anpassade ämnen och Azure-resurser

Anpassade ämnen för händelserutnät är Azure-resurser. Därför frågar du händelserutnätsprenumerationer för anpassade ämnen och andra resurser, till exempel Blob storage-konto, på samma sätt. Om du vill hämta prenumerationer på händelserutnät för anpassade avsnitt måste du ange parametrar som identifierar resursen eller identifierar resursens plats. Det går inte att i stort sett fråga om prenumerationer på händelsenät för resurser i din Azure-prenumeration.

Om du vill hämta prenumerationer på händelserutnät för anpassade ämnen och andra resurser på en plats anger du namnet på platsen.

Om du använder Azure CLI använder du:

```azurecli-interactive
az eventgrid event-subscription list --location westus2
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
Get-AzEventGridSubscription -Location westus2
```

Om du vill få prenumerationer på anpassade ämnen för en plats anger du platsen och ämnestypen för **Microsoft.EventGrid.Topics**.

Om du använder Azure CLI använder du:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.EventGrid.Topics" --location "westus2"
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.EventGrid.Topics" -Location westus2
```

Om du vill hämta prenumerationer på lagringskonton för en plats anger du platsen och ämnestypen **för Microsoft.Storage.StorageAccounts**.

Om du använder Azure CLI använder du:

```azurecli-interactive
az eventgrid event-subscription list --topic-type "Microsoft.Storage.StorageAccounts" --location westus2
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Storage.StorageAccounts" -Location westus2
```

Om du vill hämta prenumerationer på händelserutnät för ett anpassat ämne anger du namnet på det anpassade avsnittet och namnet på dess resursgrupp.

Om du använder Azure CLI använder du:

```azurecli-interactive
az eventgrid event-subscription list --topic-name myCustomTopic --resource-group myResourceGroup
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicName myCustomTopic -ResourceGroupName myResourceGroup
```

Om du vill hämta prenumerationer på händelserutnät för en viss resurs anger du resurs-ID:et.

Om du använder Azure CLI använder du:

```azurecli-interactive
resourceid=$(az resource show -n mystorage -g myResourceGroup --resource-type "Microsoft.Storage/storageaccounts" --query id --output tsv)
az eventgrid event-subscription list --resource-id $resourceid
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
$resourceid = (Get-AzResource -Name mystorage -ResourceGroupName myResourceGroup).ResourceId
Get-AzEventGridSubscription -ResourceId $resourceid
```

## <a name="next-steps"></a>Nästa steg

* För information om händelseleverans och återförsök, [Leverans av eventrutnätsmeddelanden och återförsök](delivery-and-retry.md).
* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Information om hur du snabbt kommer igång med Event Grid finns i [Skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).
