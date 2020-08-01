---
title: Azure CLI – prenumerera på resurs grupp & filtrera efter resurs
description: Den här artikeln innehåller ett exempel på ett Azure CLI-skript som visar hur du prenumererar på Event Grid händelser för en resurs och ett filter för en resurs.
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/08/2020
ms.openlocfilehash: 0d8287100eb4a16572fd5fbd764549b62a284c93
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87460872"
---
# <a name="subscribe-to-events-for-a-resource-group-and-filter-for-a-resource-with-azure-cli"></a>Prenumerera på händelser för en resursgrupp och filtrera efter en resurs med Azure CLI

Det här skriptet skapar en Event Grid-prenumeration på händelser för en resursgrupp. Det använder ett filter för att endast hämta händelser för en specifik resurs i resursgruppen.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Förhandsversionen av exempelskriptet kräver Event Grid-tillägget. Installera genom att köra `az extension add --name eventgrid`.

## <a name="sample-script---stable"></a>Exempelskript – stabilt

[!code-azurecli[main](../../../cli_scripts/event-grid/filter-events/filter-events.sh "Subscribe to Azure subscription")]

## <a name="sample-script---preview-extension"></a>Exempelskript – tillägg för förhandsversion

[!code-azurecli[main](../../../cli_scripts/event-grid/filter-events-preview/filter-events-preview.sh "Subscribe to Azure subscription")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa händelseprenumerationen. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create) | Skapa en Event Grid-prenumeration. |
| [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) – version av tillägg | Skapa en Event Grid-prenumeration. |

## <a name="next-steps"></a>Nästa steg

* Information om att köra frågor mot prenumerationer finns i [Query Event Grid subscriptions](../query-event-subscriptions.md) (Köra frågor mot Event Grid-prenumerationer).
* Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).
