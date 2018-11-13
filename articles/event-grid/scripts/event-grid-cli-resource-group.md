---
title: Azure CLI-skriptexempel – Prenumerera på resursgrupp | Microsoft Docs
description: Azure CLI-skriptexempel – Prenumerera på resursgrupp
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
ms.openlocfilehash: 7bc07ec294e341c7f96c60fd2c9916b0c6b9f215
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2018
ms.locfileid: "51035853"
---
# <a name="subscribe-to-events-for-a-resource-group-with-azure-cli"></a>Prenumerera på händelser för en resursgrupp med Azure CLI

Det här skriptet skapar en Event Grid-prenumeration på händelser för en resursgrupp. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Förhandsversionen av exempelskript kräver Event Grid-tillägget. För att installera kör du `az extension add --name eventgrid`.

## <a name="sample-script---stable"></a>Exempelskript – stable

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-resource-group/subscribe-to-resource-group.sh "Subscribe to resource group")]

## <a name="sample-script---preview-extension"></a>Exempelskript – förhandsversion av tillägg

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-resource-group-preview/subscribe-to-resource-group-preview.sh "Subscribe to resource group")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa händelseprenumerationen. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az eventgrid event-subscription create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create) | Skapa en Event Grid-prenumeration. |
| [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) - extension version | Skapa en Event Grid-prenumeration. |

## <a name="next-steps"></a>Nästa steg

* Information om att köra frågor mot prenumerationer finns i [Query Event Grid subscriptions](../query-event-subscriptions.md) (Köra frågor mot Event Grid-prenumerationer).
* Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).
