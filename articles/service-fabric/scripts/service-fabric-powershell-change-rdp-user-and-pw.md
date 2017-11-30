---
title: "Azure PowerShell-skript Sample - uppdatera RDP-användarnamnet och lösenordet | Microsoft Docs"
description: "Azure PowerShell-skript Sample - uppdatera RDP-användarnamnet och lösenordet för alla noder i Service Fabric av en viss nod-typen."
services: service-fabric
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 11/17/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 3b97cee11c9a85cbd60a05bdbdcd010a0f0a106f
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2017
---
# <a name="update-the-admin-username-and-password-of-the-vms-in-a-cluster"></a>Uppdatera admin användarnamn och lösenord för virtuella datorer i ett kluster

Varje nod i ett Service Fabric-kluster är en skaluppsättning för virtuell dator. Det här exempelskriptet uppdaterar admin användarnamn och lösenord för de virtuella datorerna i klustret i en viss nod-typen.  Lägga till filnamnstillägget VMAccessAgent skaluppsättning, eftersom administratörslösenordet inte är en ändringsbar skala egenskapen.  Användarnamn och lösenord ändringarna till alla noder i skaluppsättning. Anpassa parametrarna efter behov.

Om det behövs installerar du Azure PowerShell med hjälp av anvisningarna i den [Azure PowerShell guiden](/powershell/azure/overview). 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-user-and-pw/change-rdp-user-and-pw.ps1 "Updates a RDP username and password for cluster nodes")]

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon: varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) | Hämtar egenskaperna för en kluster-nodtyp (en virtuella datorns skaluppsättning).   |
| [Lägg till AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension)| Lägger till ett tillägg till virtuella datorns skaluppsättning.|
| [Uppdatera AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss)|Uppdaterar tillståndet för en virtuell dator skala tillstånd för ett lokalt VMSS-objekt.|

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns [Azure PowerShell dokumentationen](/powershell/azure/overview).

Ytterligare Azure Powershell-exempel för Azure Service Fabric kan hittas i den [Azure PowerShell-exempel](../service-fabric-powershell-samples.md).
