---
title: Azure PowerShell-skriptexempel – Hantera webbtrafik | Microsoft Docs
description: Azure PowerShell-skriptexempel – Hantera webbtrafik med en programgateway och en VM-skalningsuppsättning.
services: application-gateway
documentationcenter: networking
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: ef6a4171c582e3eb82fbcc73171797e1c806de05
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
ms.locfileid: "32770612"
---
# <a name="manage-web-traffic-with-azure-powershell"></a>Hantera webbtrafik med hjälp av Azure PowerShell

Med det här skriptet skapar du en programgateway som använder en VM-skalningsuppsättning för serverdelen. Programgatewayen kan sedan konfigureras för att hantera webbtrafik. När du har kört skriptet kan du testa programgatewayen med hjälp av dess offentliga IP-adress.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/application-gateway/create-vmss/create-vmss.ps1 "Create application gateway")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, programgatewayen och alla relaterade resurser.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroupAG
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa distributionen. Varje post i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Skapar undernätskonfigurationen. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Skapar det virtuella nätverket med hjälp av undernätskonfigurationen. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Skapar den offentliga IP-adressen för programgatewayen. |
| [New-AzureRmApplicationGatewayIPConfiguration](/powershell/module/azurerm.network/new-azurermapplicationgatewayipconfiguration) | Skapar den konfiguration som associerar ett undernät med programgatewayen. |
| [New-AzureRmApplicationGatewayFrontendIPConfig](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendipconfig) | Skapar den konfiguration som tilldelar en offentlig IP-adress till programgatewayen. |
| [New-AzureRmApplicationGatewayFrontendPort](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendport) | Tilldelar en port som ska användas för åtkomst till programgatewayen. |
| [New-AzureRmApplicationGatewayBackendAddressPool](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendaddresspool) | Skapar en serverdelspool för en programgateway. |
| [New-AzureRmApplicationGatewayBackendHttpSettings](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendhttpsettings) | Konfigurerar inställningar för serverdelspoolen. |
| [New-AzureRmApplicationGatewayHttpListener](/powershell/module/azurerm.network/new-azurermapplicationgatewayhttplistener) | Skapar en lyssnare. |
| [New-AzureRmApplicationGatewayRequestRoutingRule](/powershell/module/azurerm.network/new-azurermapplicationgatewayrequestroutingrule) | Skapar en hanteringsregel. |
| [New-AzureRmApplicationGatewaySku](/powershell/module/azurerm.network/new-azurermapplicationgatewaysku) | Ange nivå och kapacitet för en programgateway. |
| [New-AzureRmApplicationGateway](/powershell/module/azurerm.network/new-azurermapplicationgateway) | Skapar en programgateway. |
| [Set-AzureRmVmssStorageProfile](/powershell/module/azurerm.compute/set-azurermvmssstorageprofile) | Skapar en lagringsprofil för skalningsuppsättningen. |
| [Set-AzureRmVmssOsProfile](/powershell/module/azurerm.compute/set-azurermvmssosprofile) | Definierar operativsystemet för skalningsuppsättningen. |
| [Add-AzureRmVmssNetworkInterfaceConfiguration](/powershell/module/azurerm.compute/add-azurermvmssnetworkinterfaceconfiguration) | Definierar nätverksgränssnittet för skalningsuppsättningen. |
| [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvm) | Skapar en VM-skalningsuppsättning. |
| [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) | Hämtar den offentliga IP-adressen för en programgateway. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Tar bort en resursgrupp och alla resurser som ingår i gruppen. | 

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för programgatewayer finns i [dokumentationen för Azure Application Gateway](../powershell-samples.md).
