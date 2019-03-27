---
title: Skriptexempel för Azure PowerShell – lägga till en regel för nätverkssäkerhetsgrupp | Microsoft Docs
description: Azure PowerShell-skriptexempel – lägger till en nätverkssäkerhetsgrupp som tillåter inkommande trafik på en viss port.
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
ms.date: 11/28/2017
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: fead6aa19775eab6cc3a1014e9f52b30cfa2cefa
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58498323"
---
# <a name="add-an-inbound-network-security-group-rule"></a>Lägg till en regel för inkommande nätverkssäkerhetsgrupper

Det här exempelskriptet skapar en regel för nätverkssäkerhetsgrupp som tillåter inkommande trafik på port 8081.  Skriptet hämtar den `Microsoft.Network/networkSecurityGroups` resurs som klustret finns, skapar en ny konfiguration för nätverkssäkerhetsregeln och uppdaterar nätverkssäkerhetsgruppen. Anpassa parametrarna efter behov.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Installera Azure PowerShell med hjälp av anvisningarna i [Azure PowerShell-guiden](/powershell/azure/overview) om det behövs. 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Hämtar `Microsoft.Network/networkSecurityGroups`-resursen. |
|[Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)| Hämtar nätverkssäkerhetsgruppen med namnet.|
|[Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Lägger till en regel för nätverkssäkerhetskonfigurationen en nätverkssäkerhetsgrupp. |
|[Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)| Konfigurerar målstatusen för en grupp.|

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).
