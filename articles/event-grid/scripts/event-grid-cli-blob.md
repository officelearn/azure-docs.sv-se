---
title: Skriptexempel för Azure CLI – Prenumerera på ett Blob Storage-konto| Microsoft Docs
description: Skriptexempel för Azure CLI – Prenumerera på ett Blob Storage-konto
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
ms.openlocfilehash: 3ecf5cd0906e04625f44584d0b668f764665075e
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2018
ms.locfileid: "51037875"
---
# <a name="subscribe-to-events-for-a-blob-storage-account-with-azure-cli"></a>Prenumerera på händelser för ett Blob Storage-konto med Azure CLI

Det här skriptet skapar en Event Grid-prenumeration på händelser för ett Blob Storage-konto. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Förhandsversionen av exempelskriptet kräver Event Grid-tillägget. Installera genom att köra `az extension add --name eventgrid`.

## <a name="sample-script---stable"></a>Exempelskript – stabilt

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-blob-storage/subscribe-to-blob-storage.sh "Subscribe to Blob storage")]

## <a name="sample-script---preview-extension"></a>Exempelskript – tillägg för förhandsversion

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-blob-storage-preview/subscribe-to-blob-storage-preview.sh "Subscribe to Blob storage")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa händelseprenumerationen. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az eventgrid event-subscription create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create) | Skapa en Event Grid-prenumeration. |
| [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) – version av tillägg | Skapa en Event Grid-prenumeration. |

## <a name="next-steps"></a>Nästa steg

* Information om att köra frågor mot prenumerationer finns i [Query Event Grid subscriptions](../query-event-subscriptions.md) (Köra frågor mot Event Grid-prenumerationer).
* Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).
