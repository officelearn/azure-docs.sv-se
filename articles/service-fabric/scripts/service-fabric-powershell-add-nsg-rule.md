---
title: Lägga till en regel för nätverkssäkerhetsgrupp i Powershell
description: Exempel på Azure PowerShell-skript – lägger till en nätverkssäkerhetsgrupp för att tillåta inkommande trafik på en viss port.
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
ms.date: 11/28/2017
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 33ee8028031bf94d69d11b08c347f84db94494a3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75610428"
---
# <a name="add-an-inbound-network-security-group-rule"></a>Lägga till en regel för säkerhetsgrupp för inkommande nätverk

Med det här exempelskriptet skapas en regel för nätverkssäkerhetsgrupp för att tillåta inkommande trafik på port 8081.  Skriptet hämtar nätverkssäkerhetsgruppen, skapar en ny regel för nätverkssäkerhetskonfiguration och uppdaterar nätverkssäkerhetsgruppen. Anpassa parametrarna efter behov.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Installera Azure PowerShell med hjälp av anvisningarna i [Azure PowerShell-guiden](/powershell/azure/overview) om det behövs. 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Hämtar `Microsoft.Network/networkSecurityGroups`-resursen. |
|[Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)| Hämtar nätverkssäkerhetsgruppen efter namn.|
|[Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Lägger till en nätverkssäkerhetsregelkonfiguration i en nätverkssäkerhetsgrupp. |
|[Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)| Anger måltillståndet för en nätverkssäkerhetsgrupp.|

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).
