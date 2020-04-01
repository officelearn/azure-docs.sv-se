---
title: Azure PowerShell – prenumerera på Blob-lagringskonto
description: Den här artikeln innehåller ett exempel på Azure PowerShell-skript som visar hur du prenumererar på Event Grid-händelser för ett Blob Storage-konto.
services: event-grid
documentationcenter: na
author: spelluru
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: a8a0982ca118663cbf0f7e4d72412ce8feda3c4b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76721446"
---
# <a name="subscribe-to-events-for-a-blob-storage-account-with-powershell"></a>Prenumerera på händelser för ett Blob Storage-konto med PowerShell

Det här skriptet skapar en Event Grid-prenumeration på händelser för ett Blob Storage-konto.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script---stable"></a>Exempelskript – stabilt

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-blob-storage/subscribe-to-blob-storage.ps1 "Subscribe to Blob storage")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa händelseprenumerationen. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Ny-AzEventGridAbonnemang](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) | Skapa en Event Grid-prenumeration. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till hanterade program finns i [Azure Managed Application overview](../overview.md) (Översikt över Azure Managed Application).
* Mer information om PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/azure/get-started-azureps).
