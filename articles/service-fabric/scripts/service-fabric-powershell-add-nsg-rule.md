---
title: Azure PowerShell-skript exempel – Lägg till en grupp för nätverkssäkerhetsregeln | Microsoft Docs
description: Azure PowerShell skriptexempel - lägger till en nätverkssäkerhetsgrupp som tillåter inkommande trafik på en specifik port.
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
ms.date: 11/28/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: fd3c648ee63c45bef305658832a4d31dfdb213be
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2017
ms.locfileid: "25987386"
---
# <a name="add-an-inbound-network-security-group-rule"></a>Lägg till en grupp för inkommande nätverk säkerhetsregel

Det här exempelskriptet skapar en grupp nätverkssäkerhetsregeln för att tillåta inkommande trafik på port 8081.  Skriptet hämtar den `Microsoft.Network/networkSecurityGroups` resurs som klustret finns, skapas en ny konfiguration för nätverkssäkerhetsregeln och uppdaterar nätverkssäkerhetsgruppen. Anpassa parametrarna efter behov.

Om det behövs installerar du Azure PowerShell med hjälp av anvisningarna i den [Azure PowerShell guiden](/powershell/azure/overview). 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Hämtar den `Microsoft.Network/networkSecurityGroups` resurs. |
|[Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)| Hämtar nätverkssäkerhetsgruppen efter namn.|
|[Lägg till AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig)| Lägger till en regel för nätverkssäkerhetskonfigurationen till en nätverkssäkerhetsgrupp. |
|[Ange AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/set-azurermnetworksecuritygroup)| Anger tillståndet målet för en nätverkssäkerhetsgrupp.|

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns [Azure PowerShell dokumentationen](/powershell/azure/overview).
