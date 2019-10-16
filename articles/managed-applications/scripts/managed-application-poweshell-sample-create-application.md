---
title: Azure PowerShell-skriptexempel – Distribuera ett hanterat program | Microsoft Docs
description: Innehåller Azure PowerShell exempel skript som distribuerar en definition för ett hanterat program till prenumerationen.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: d450c2292f5d2982b7c8b266cc6780a72d426321
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332558"
---
# <a name="deploy-a-managed-application-for-a-service-catalog-with-powershell"></a>Distribuera ett hanterat program för en tjänstkatalog med PowerShell

Det här skriptet distribuerar en definition för hanterade program från tjänstkatalogen.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/managed-applications/create-application/create-application.ps1 "Create application")]


## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommando för att distribuera det hanterade programmet. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzManagedApplication](https://docs.microsoft.com/powershell/module/az.resources/new-azmanagedapplication) | Skapa ett hanterat program. Ange definitions-ID och parametrar för mallen. |


## <a name="next-steps"></a>Nästa steg

* En introduktion till hanterade program finns i [översikten över Azure Managed Application](../overview.md).
* Mer information om PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/azure/get-started-azureps).
