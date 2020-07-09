---
title: Azure CLI-skriptexempel – Skapa anpassat ämne | Microsoft Docs
description: Den här artikeln innehåller ett exempel på ett Azure CLI-skript som visar hur du skapar ett Azure Event Grid anpassat ämne.
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/08/2020
ms.openlocfilehash: 951008a2bf3919f69429ca15382334a9618b912f
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171320"
---
# <a name="create-event-grid-custom-topic-with-azure-cli"></a>Skapa anpassat Event Grid-ämne med Azure CLI

Det här skriptet skapar ett anpassat Event Grid-ämne.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/event-grid/create-custom-topic/create-custom-topic.sh "Create custom topic")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa det anpassade ämnet. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az eventgrid topic create](https://docs.microsoft.com/cli/azure/eventgrid/topic#az-eventgrid-topic-create) | Skapa ett Event Grid-anpassat ämne. |


## <a name="next-steps"></a>Nästa steg

* Information om att köra frågor mot prenumerationer finns i [Query Event Grid subscriptions](../query-event-subscriptions.md) (Köra frågor mot Event Grid-prenumerationer).
* Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).
