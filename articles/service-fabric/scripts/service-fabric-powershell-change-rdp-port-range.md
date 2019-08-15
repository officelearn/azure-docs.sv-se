---
title: Skriptexempel för Azure PowerShell – Ändra RDP-portintervall | Microsoft Docs
description: Skriptexempel för Azure PowerShell – Ändra RDP-portintervall för ett distribuerat kluster.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 03/19/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 4ca7b4085a3978dc3218480c5c8a42679d80de23
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035662"
---
# <a name="update-the-rdp-port-range-values"></a>Uppdatera RDP-portintervallvärden

Det här exempelskriptet ändrar RDP-portintervallvärdena på klusternodens virtuella datorer när klustret har distribuerats.  Azure PowerShell används så att de underliggande virtuella datorerna inte växlar.  Skriptet hämtar `Microsoft.Network/loadBalancers`-resursen i klustrets resursgrupp och uppdaterar `inboundNatPools.frontendPortRangeStart`- och `inboundNatPools.frontendPortRangeEnd`-värdena. Anpassa parametrarna efter behov.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Installera Azure PowerShell, om så behövs, med hjälp av anvisningarna i [Azure PowerShell-guiden](/powershell/azure/overview). 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-port-range/change-rdp-port-range.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Hämtar `Microsoft.Network/loadBalancers`-resursen. |
|[Set-AzResource](/powershell/module/az.resources/set-azresource)|Uppdaterar `Microsoft.Network/loadBalancers`-resursen.|

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare Azure Powershell-exempel för Azure Service Fabric finns i [Azure PowerShell-exemplen](../service-fabric-powershell-samples.md).
