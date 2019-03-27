---
title: Skriptexempel för Azure PowerShell – Öppna programport i lastbalanseraren | Microsoft Docs
description: Skriptexempel för Azure PowerShell – Öppna en port i Azure-lastbalanseraren för ett Service Fabric-program.
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 05/18/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 72badb5ca2e8973fd64f5339eaff55ceb79a5ce3
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58498272"
---
# <a name="open-an-application-port-in-the-azure-load-balancer"></a>Öppna en programport i Azure-lastbalanseraren

Ett Service Fabric-program som körs i Azure är placerat bakom Azure-lastbalanseraren. Det här exempelskriptet öppnar en port i en Azure-lastbalanserare så att ett Service Fabric-program kan kommunicera med externa klienter. Anpassa parametrarna efter behov. Om klustret är i en nätverkssäkerhetsgrupp så [lägg till en inkommande regel för nätverkssäkerhetsgrupper](service-fabric-powershell-add-nsg-rule.md) som tillåter inkommande trafik.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Installera, om så behövs, Service Fabric PowerShell-modulen installeras med [Service Fabric SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/service-fabric/open-port-in-load-balancer/open-port-in-load-balancer.ps1 "Open a port in the load balancer")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Hämtar en Azure-resurs.  |
| [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) | Hämtar Azure Load Balancer. |
| [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) | Lägger till en avsökningskonfiguration till en lastbalanserare.|
| [Get-AzLoadBalancerProbeConfig](/powershell/module/az.network/get-azloadbalancerprobeconfig) | Hämtar en avsökningskonfiguration för en lastbalanserare. |
| [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) | Lägger till en regelkonfiguration till en lastbalanserare. |
| [Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer) | Konfigurerar målstatusen för en lastbalanserare. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare Powershell-exempel för Azure Service Fabric finns i [Azure PowerShell-exemplen](../service-fabric-powershell-samples.md).
