---
title: Skriptexempel för Azure PowerShell – Öppna programport i belastningsutjämnaren | Microsoft Docs
description: Skriptexempel för Azure PowerShell – Öppna en port i Azure-belastningsutjämnaren för ett Service Fabric-program.
services: service-fabric
documentationcenter: ''
author: rwike77
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 03/19/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 1edaca9c354a7b65b47213c7970e823aee3cbe87
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="open-an-application-port-in-the-azure-load-balancer"></a>Öppna en programport i Azure-belastningsutjämnaren

Ett Service Fabric-program som körs i Azure är placerat bakom Azure-belastningsutjämnaren. Det här exempelskriptet öppnar en port i en Azure-belastningsutjämnare så att ett Service Fabric-program kan kommunicera med externa klienter. Anpassa parametrarna efter behov. Om klustret är i en nätverkssäkerhetsgrupp så [lägg till en inkommande regel för nätverkssäkerhetsgrupper](service-fabric-powershell-add-nsg-rule.md) som tillåter inkommande trafik.

Installera, om så behövs, Service Fabric PowerShell-modulen installeras med [Service Fabric SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/service-fabric/open-port-in-load-balancer/open-port-in-load-balancer.ps1 "Open a port in the load balancer")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Hämtar en Azure-resurs.  |
| [Get-AzureRmLoadBalancer](/powershell/module/azurerm.network/get-azurermloadbalancer) | Hämtar Azure Load Balancer. |
| [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/add-azurermloadbalancerprobeconfig) | Lägger till en avsökningskonfiguration till en belastningsutjämnare.|
| [Get-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/get-azurermloadbalancerprobeconfig) | Hämtar en avsökningskonfiguration för en belastningsutjämnare. |
| [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/add-azurermloadbalancerruleconfig) | Lägger till en regelkonfiguration till en belastningsutjämnare. |
| [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer) | Konfigurerar målstatusen för en belastningsutjämnare. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare Powershell-exempel för Azure Service Fabric finns i [Azure PowerShell-exemplen](../service-fabric-powershell-samples.md).
