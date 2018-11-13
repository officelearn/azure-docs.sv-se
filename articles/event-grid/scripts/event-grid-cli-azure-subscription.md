---
title: Skriptexempel för Azure CLI – Prenumerera på en Azure-prenumeration| Microsoft Docs
description: Skriptexempel för Azure CLI – Prenumerera på en Azure-prenumeration
services: event-grid
documentationcenter: na
author: tfitzmac
ms.service: event-grid
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2018
ms.author: tomfitz
ms.openlocfilehash: 9d1aa53ede323c2bb536c74eeaaba9fd28b01712
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2018
ms.locfileid: "51037540"
---
# <a name="subscribe-to-events-for-an-azure-subscription-with-azure-cli"></a>Prenumerera på händelser för en Azure-prenumeration med Azure CLI

Det här skriptet skapar en Event Grid-prenumeration på händelser för en Azure-prenumeration.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Förhandsversionen av exempelskript kräver Event Grid-tillägget. För att installera kör du `az extension add --name eventgrid`.

## <a name="sample-script---stable"></a>Exempelskript – stable

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-azure-subscription/subscribe-to-azure-subscription.sh "Subscribe to Azure subscription")]

## <a name="sample-script---preview-extension"></a>Exempelskript – förhandsversion av tillägg

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-azure-subscription-preview/subscribe-to-azure-subscription-preview.sh "Subscribe to Azure subscription")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa händelseprenumerationen. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create) | Skapa en Event Grid-prenumeration. |
| [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) - extension version | Skapa en Event Grid-prenumeration. |

## <a name="next-steps"></a>Nästa steg

* Information om att köra frågor mot prenumerationer finns i [Query Event Grid subscriptions](../query-event-subscriptions.md) (Köra frågor mot Event Grid-prenumerationer).
* Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).
