---
title: Azure PowerShell skript exempel – Lägg till en regel för nätverks säkerhets grupp | Microsoft Docs
description: Azure PowerShell skript exempel – lägger till en nätverks säkerhets grupp som tillåter inkommande trafik på en speciell port.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 11/28/2017
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 1418b85bfa2579ed2dee5211a79a75adf14f1547
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592289"
---
# <a name="add-an-inbound-network-security-group-rule"></a>Lägg till en regel för inkommande nätverks säkerhets grupp

Det här exempel skriptet skapar en regel för nätverks säkerhets grupp som tillåter inkommande trafik på port 8081.  Skriptet hämtar den `Microsoft.Network/networkSecurityGroups` resurs som klustret finns i, skapar en ny nätverks säkerhets konfigurations regel och uppdaterar nätverks säkerhets gruppen. Anpassa parametrarna efter behov.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Installera Azure PowerShell med hjälp av anvisningarna i [Azure PowerShell-guiden](/powershell/azure/overview) om det behövs. 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Hämtar `Microsoft.Network/networkSecurityGroups`-resursen. |
|[Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)| Hämtar nätverks säkerhets gruppen efter namn.|
|[Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Lägger till en nätverks säkerhets regel konfiguration i en nätverks säkerhets grupp. |
|[Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)| Anger mål tillstånd för en nätverks säkerhets grupp.|

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).
