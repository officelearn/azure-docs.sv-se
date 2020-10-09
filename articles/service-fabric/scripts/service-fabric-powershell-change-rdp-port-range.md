---
title: Skriptexempel för Azure PowerShell – Ändra RDP-portintervall | Microsoft Docs
description: Skriptexempel för Azure PowerShell – Ändra RDP-portintervall för ett distribuerat kluster.
services: service-fabric
tags: azure-service-management
author: athinanthny
ms.author: atsenthi
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 03/19/2018
ms.custom: sfrev
ms.openlocfilehash: 5cc72423d7e901f82cdf7e40f5de72bd54e9cec1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87076173"
---
# <a name="update-the-rdp-port-range-values"></a>Uppdatera RDP-portintervallvärden

Det här exempelskriptet ändrar RDP-portintervallvärdena på klusternodens virtuella datorer när klustret har distribuerats.  Azure PowerShell används så att de underliggande virtuella datorerna inte växlar.  Skriptet hämtar `Microsoft.Network/loadBalancers` resursen i klustrets resurs grupp och uppdaterar `inboundNatPools.frontendPortRangeStart` `inboundNatPools.frontendPortRangeEnd` värdena och. Anpassa parametrarna efter behov.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Installera Azure PowerShell, om så behövs, med hjälp av anvisningarna i [Azure PowerShell-guiden](/powershell/azure/).

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-port-range/change-rdp-port-range.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Obs! |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Hämtar `Microsoft.Network/loadBalancers`-resursen. |
|[Set-AzResource](/powershell/module/az.resources/set-azresource)|Uppdaterar `Microsoft.Network/loadBalancers`-resursen.|

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/).

Ytterligare Azure Powershell-exempel för Azure Service Fabric finns i [Azure PowerShell-exemplen](../service-fabric-powershell-samples.md).
