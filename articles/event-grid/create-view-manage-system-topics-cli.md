---
title: Skapa, Visa och hantera Azure Event Grid system ämnen med CLI
description: Den här artikeln visar hur du använder Azure CLI för att skapa, Visa och ta bort system ämnen.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: spelluru
ms.openlocfilehash: 19a22a0a3b528a9a72fdd51c589e42bf2fba5ce7
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/10/2020
ms.locfileid: "84669944"
---
# <a name="create-view-and-manage-event-grid-system-topics-using-azure-cli"></a>Skapa, Visa och hantera Event Grid system ämnen med hjälp av Azure CLI
Den här artikeln visar hur du skapar och hanterar system ämnen med hjälp av Azure CLI. En översikt över system ämnen finns i [system avsnitt](system-topics.md).

## <a name="install-extension-for-azure-cli"></a>Installera tillägg för Azure CLI
För Azure CLI behöver du Event Grid- [tillägget](/cli/azure/azure-cli-extensions-list).

I CloudShell:

- Om du har installerat tillägget tidigare uppdaterar du det:`az extension update -n eventgrid`
- Om du inte har installerat tillägget tidigare installerar du det:`az extension add -n eventgrid`

För en lokal installation:

1. [Installera Azure CLI](/cli/azure/install-azure-cli). Kontrol lera att du har den senaste versionen genom att kontrol lera med AZ--version.
2. Avinstallera tidigare versioner av tillägget:`az extension remove -n eventgrid`
3. Installera eventgrid-tillägget med`az extension add -n eventgrid`

## <a name="create-a-system-topic"></a>Avsnittet Skapa ett system

- Om du vill skapa ett system ämne i en Azure-källa först och sedan skapa en händelse prenumeration för ämnet, se följande referens avsnitt:
    - [AZ eventgrid system-topic Create](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-create)

        ```azurecli
        az eventgrid system-topic create \
            -g myResourceGroup \
            --name systemtopic1 \
            --location westus2 \
            --topic-type microsoft.storage.storageaccounts \
            --source /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/storagegaccountname
        ```
    - [AZ eventgrid system-topic Event-Subscription Create](/cli/azure/ext/eventgrid/eventgrid/system-topic/event-subscription?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-event-subscription-create)

        ```azurecli
        az eventgrid system-topic event-subscription create --name es1 \
            -g rg1 --system-topic-name systemtopic1 \
            --endpoint https://contoso.azurewebsites.net/api/f1?code=code         
          ```
- To create a system topic (implicitly) when creating an event subscription for an Azure source, use the [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription?view=azure-cli-latest#ext-eventgrid-az-eventgrid-event-subscription-create) method. Here's an example:
    
    ```azurecli
    storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)
    endpoint=https://$sitename.azurewebsites.net/api/updates

    az eventgrid event-subscription create \
      --source-resource-id $storageid \
      --name <event_subscription_name> \
      --endpoint $endpoint
    ```
    En själv studie kurs med stegvisa instruktioner finns i [Prenumerera på lagrings konto](../storage/blobs/storage-blob-event-quickstart.md?toc=%2Fazure%2Fevent-grid%2Ftoc.json#subscribe-to-your-storage-account).

## <a name="view-all-system-topics"></a>Visa alla system ämnen
Om du vill visa alla system ämnen och information om ett valt system, använder du följande kommandon:

- [AZ eventgrid system-topic List](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-list)

    ```azurecli
    az eventgrid system-topic list   
     ```
- [AZ eventgrid system-topic show](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-show)

    ```azurecli
    az eventgrid system-topic show -g rg1 -n systemtopic1    
     ```

## <a name="delete-a-system-topic"></a>Ta bort ett system ämne
Om du vill ta bort ett system avsnitt använder du följande kommando: 

- [AZ eventgrid system-topic Delete](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-delete)

    ```azurecli
    az eventgrid system-topic delete -g myResourceGroup --name systemtopic1  
     ```

## <a name="next-steps"></a>Nästa steg
Mer information om system ämnen och ämnes typer som stöds av Azure Event Grid finns i avsnittet [system ämnen i Azure Event Grid](system-topics.md) . 
