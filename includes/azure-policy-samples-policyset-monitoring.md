---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/03/2020
ms.author: dacoulte
ms.openlocfilehash: 074d28f8144245247944f9c3409507d331c81166
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758807"
---
|Namn |Beskrivning |Principer |Version |
|---|---|---|---|
|[Aktivera Azure-övervakare för skalningsuppsättningar för virtuella datorer](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Aktivera Azure Monitor för skalningsuppsättningarna för virtuella datorer i det angivna scopet (hanteringsgrupp, prenumeration eller resursgrupp). Tar Log Analytics-arbetsytan som parameter. Om din scale set upgradePolicy är inställd på Manuell måste du använda tillägget på alla virtuella datorer i uppsättningen genom att anropa uppgradering på dem. I CLI detta skulle vara az vmss uppdatering-instanser. |6 |1.0.1 |
|[Aktivera Azure Monitor för virtuella datorer](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Aktivera Azure Monitor för virtuella datorer (virtuella datorer) i det angivna omfånget (hanteringsgrupp, prenumeration eller resursgrupp). Tar Log Analytics-arbetsytan som parameter. |6 |1.0.1 |
