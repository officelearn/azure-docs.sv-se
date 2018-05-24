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
ms.date: 05/18/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 0549f5f2b5b0f8fdfc18b8c091c1065d6137b8c6
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34366179"
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
