---
title: "Azure PowerShell skriptexempel - ändra portintervall RDP | Microsoft Docs"
description: "Azure PowerShell skriptexempel - ändras RDP portintervall distribuerade klustret."
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
ms.date: 11/28/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 9089d071c69830323a94140a5ebb4e9e865499c6
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2017
---
# <a name="update-the-rdp-port-range-values"></a>Uppdatera intervallvärden för RDP-port

Det här exempelskriptet ändrar intervallvärden för RDP-port på klusternoden virtuella datorer när klustret har distribuerats.  Azure PowerShell används så att de underliggande virtuella datorerna inte växla.  Skriptet hämtar den `Microsoft.Network/loadBalancers` resurs i klustrets resursgrupp och uppdaterar den `inboundNatPools.frontendPortRangeStart` och `inboundNatPools.frontendPortRangeEnd` värden. Anpassa parametrarna efter behov.

Om det behövs installerar du Azure PowerShell med hjälp av anvisningarna i den [Azure PowerShell guiden](/powershell/azure/overview). 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-port-range/change-rdp-port-range.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Hämtar den `Microsoft.Network/loadBalancers` resurs. |
|[Ange AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource)|Uppdateringar av `Microsoft.Network/loadBalancers` resurs.|

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns [Azure PowerShell dokumentationen](/powershell/azure/overview).

Ytterligare Azure Powershell-exempel för Azure Service Fabric kan hittas i den [Azure PowerShell-exempel](../service-fabric-powershell-samples.md).
