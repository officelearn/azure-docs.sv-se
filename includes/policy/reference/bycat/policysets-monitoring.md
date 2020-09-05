---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/04/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 916c0631cd62f70cfde8e3a6f8a5843c59d865a4
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2020
ms.locfileid: "89487084"
---
|Name |Beskrivning |Principer |Version |
|---|---|---|---|
|[Aktivera Azure Monitor för Virtual Machine Scale Sets](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Aktivera Azure Monitor för Virtual Machine Scale Sets i det angivna omfånget (hanterings grupp, prenumeration eller resurs grupp). Tar Log Analytics-arbetsyta som parameter. Obs! Om din skalnings uppsättnings upgradePolicy är inställd på manuell, måste du tillämpa tillägget på alla virtuella datorer i uppsättningen genom att anropa uppgraderingen på dem. I CLI är detta AZ VMSS Update-instances. |6 |1.0.1 |
|[Aktivera Azure Monitor for VMs](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Aktivera Azure Monitor för de virtuella datorerna (VM) i det angivna omfånget (hanterings grupp, prenumeration eller resurs grupp). Tar Log Analytics-arbetsyta som parameter. |10 |2.0.0 |
