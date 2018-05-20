---
title: Fråga händelse rutnät för Azure-prenumerationer
description: Beskriver hur du listar Azure händelse rutnätet prenumerationer.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: tomfitz
ms.openlocfilehash: 2b46cde4a352e647ee97669f116a6c1926879fa0
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="query-event-grid-subscriptions"></a>Fråga händelse rutnätet prenumerationer 

Den här artikeln beskriver hur du listar händelse rutnätet prenumerationer i din Azure-prenumeration. När du hämtar dina befintliga prenumerationer på händelsen rutnät, är det viktigt att förstå de olika typerna av prenumerationer. Du kan ange olika parametrar som är baserat på vilken typ av prenumeration som du vill hämta.

## <a name="resource-groups-and-azure-subscriptions"></a>Resursgrupper och Azure-prenumerationer

Azure-prenumerationer och resursgrupper inte Azure-resurser. Därför har händelse rutnätet prenumerationer till resursgrupper eller Azure-prenumerationer inte samma egenskaper som händelsen rutnätet prenumerationer till Azure-resurser. Händelsen rutnätet prenumerationer till resursgrupper eller Azure-prenumerationer betraktas som global.

För att få prenumerationer på händelser rutnät för en Azure-prenumeration och dess resursgrupper, behöver du inte ange några parametrar. Kontrollera att du har valt Azure-prenumeration du vill fråga. I följande exempel hämta inte händelsen rutnätet prenumerationer för anpassade avsnitt eller Azure-resurser.

Om du använder Azure CLI använder du:

```azurecli-interactive
az account set -s "My Azure Subscription"
az eventgrid event-subscription list
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
Set-AzureRmContext -Subscription "My Azure Subscription"
Get-AzureRmEventGridSubscription
```

För att få händelse rutnätet prenumerationer för en Azure-prenumeration, ange ämnestyp **Microsoft.Resources.Subscriptions**.

Om du använder Azure CLI använder du:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.Subscriptions"
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicTypeName "Microsoft.Resources.Subscriptions"
```

För att få prenumerationer på händelser rutnät för alla resursgrupper i en Azure-prenumeration, ange ämnestyp **Microsoft.Resources.ResourceGroups**.

Om du använder Azure CLI använder du:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.ResourceGroups"
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicTypeName "Microsoft.Resources.ResourceGroups"
```

Ange namnet på resursgruppen som en parameter för att få prenumerationer på händelser rutnätet för en viss resursgrupp.

Om du använder Azure CLI använder du:

```azurecli-interactive
az eventgrid event-subscription list --resource-group myResourceGroup
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -ResourceGroupName myResourceGroup
```

## <a name="custom-topics-and-azure-resources"></a>Anpassade ämnen och Azure-resurser

Händelsen rutnätet anpassade avsnitt är Azure-resurser. Därför kan fråga du händelse rutnätet prenumerationer för anpassade ämnen och andra resurser, t.ex. Blob storage-konto på samma sätt. För att få händelse rutnätet prenumerationer för anpassade avsnitt, måste du ange parametrar som identifiera resursen eller identifierar platsen för resursen. Det går inte att brett fråga händelse rutnätet prenumerationer för resurser i din Azure-prenumeration.

Om du vill hämta händelsen rutnätet prenumerationer för anpassad information och andra resurser på en plats, ange namnet på platsen.

Om du använder Azure CLI använder du:

```azurecli-interactive
az eventgrid event-subscription list --location westus2
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -Location westus2
```

För att få prenumerationer till anpassade avsnitt för en plats, ange platsen och ämnestyp **Microsoft.EventGrid.Topics**.

Om du använder Azure CLI använder du:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.EventGrid.Topics" --location "westus2"
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicTypeName "Microsoft.EventGrid.Topics" -Location westus2
```

För att få prenumerationer till lagringskonton för en plats, ange platsen och ämnestyp **Microsoft.Storage.StorageAccounts**.

Om du använder Azure CLI använder du:

```azurecli-interactive
az eventgrid event-subscription list --topic-type "Microsoft.Storage.StorageAccounts" --location westus2
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicTypeName "Microsoft.Storage.StorageAccounts" -Location westus2
```

Ange namnet på det anpassa ämnet och namnet på resursgruppen för att få händelse rutnätet prenumerationer för ett anpassat ämne.

Om du använder Azure CLI använder du:

```azurecli-interactive
az eventgrid event-subscription list --topic-name myCustomTopic --resource-group myResourceGroup
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicName myCustomTopic -ResourceGroupName myResourceGroup
```

För att få prenumerationer på händelser rutnätet för en viss resurs, ange resurs-ID.

Om du använder Azure CLI använder du:

```azurecli-interactive
resourceid=$(az resource show -n mystorage -g myResourceGroup --resource-type "Microsoft.Storage/storageaccounts" --query id --output tsv)
az eventgrid event-subscription list --resource-id $resourceid
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
$resourceid = (Get-AzureRmResource -Name mystorage -ResourceGroupName myResourceGroup).ResourceId
Get-AzureRmEventGridSubscription -ResourceId $resourceid
```

## <a name="next-steps"></a>Nästa steg

* Information om händelsen leverans och omförsök, [händelse rutnätet meddelandeleverans och försök igen](delivery-and-retry.md).
* En introduktion till händelse rutnätet finns [om händelsen rutnätet](overview.md).
* Om du vill komma igång snabbt med hjälp av händelse rutnätet kan se [skapa och flöde anpassade händelser med Azure händelse rutnätet](custom-event-quickstart.md).
