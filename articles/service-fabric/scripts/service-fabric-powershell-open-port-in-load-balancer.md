---
title: "Azure PowerShell skriptexempel - öppna programmet port i belastningsutjämnaren | Microsoft Docs"
description: "Azure PowerShell skriptexempel - öppna en port i Azure belastningsutjämnare för ett Service Fabric-program."
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
ms.date: 12/08/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: c643fc9e575a8e836a361893d78348bbd627a425
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2017
---
# <a name="open-an-application-port-in-the-azure-load-balancer"></a>Öppna en port för program i Azure belastningsutjämnare

Ett Service Fabric-program som körs i Azure är placerad bakom Azure belastningsutjämnare. Det här exempelskriptet öppnar en port i en Azure belastningsutjämnare så att ett Service Fabric-program kan kommunicera med externa klienter. Anpassa parametrarna efter behov. Om klustret är i en nätverkssäkerhetsgrupp också [Lägg till en grupp för inkommande nätverk säkerhetsregel](service-fabric-powershell-add-nsg-rule.md) som tillåter inkommande trafik.

Om det behövs installerar du Service Fabric PowerShell-modulen med den [Service Fabric SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/service-fabric/open-port-in-load-balancer/open-port-in-load-balancer.ps1 "Open a port in the load balancer")]

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommando-specifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Hämtar en Azure-resurs.  |
| [Get-AzureRmLoadBalancer](/powershell/module/azurerm.network/get-azurermloadbalancer) | Hämtar Azure belastningsutjämnare. |
| [Lägg till AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/add-azurermloadbalancerprobeconfig) | Lägger till en avsökning konfiguration till en belastningsutjämnare.|
| [Get-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/get-azurermloadbalancerprobeconfig) | Hämtar en avsökning konfiguration för en belastningsutjämnare. |
| [Lägg till AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/add-azurermloadbalancerruleconfig) | Lägger till en Regelkonfiguration till en belastningsutjämnare. |
| [Ange AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer) | Anger tillståndet målet för en belastningsutjämnare. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns [Azure PowerShell dokumentationen](/powershell/azure/overview).

Ytterligare Powershell-exempel för Azure Service Fabric kan hittas i den [Azure PowerShell-exempel](../service-fabric-powershell-samples.md).
