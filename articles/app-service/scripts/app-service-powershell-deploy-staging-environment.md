---
title: "Azure PowerShell skriptexempel - skapa en webbapp och distribuera kod till en mellanlagringsmiljön | Microsoft Docs"
description: "Azure PowerShell skriptexempel - skapa en webbapp och distribuera kod till en mellanlagringsmiljön"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 27cf0680-c3a9-4a58-9f71-6dec09f6b874
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 55adc13350eb0f4711efa3c901f6e4e7755dfb27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-web-app-and-deploy-code-to-a-staging-environment"></a>Skapa en webbapp och distribuera kod till en mellanlagringsmiljön

Det här exempelskriptet skapar en webbapp i App Service med en ytterligare distributionsplatsen som kallas ”mellanlagring” och sedan distribuerar en exempelapp till ”” mellanlagringsplatsen.

Om det behövs installerar du Azure PowerShell med hjälp av anvisningarna i den [Azure PowerShell guiden](/powershell/azure/overview), och kör sedan `Login-AzureRmAccount` att skapa en anslutning med Azure.

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.ps1?highlight=1 "Create a web app and deploy code to a staging environment")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Följande kommando kan användas för att ta bort resursgruppen, webbprogram och alla relaterade resurser efter skriptexempel har körts.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Ny AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [Ny AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | Skapar en App Service-plan. |
| [Ny AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Skapar ett webbprogram. |
| [Ange AzureRmAppServicePlan](/powershell/module/azurerm.websites/set-azurermappserviceplan) | Ändrar en App Service-plan för att ändra dess prisnivå. |
| [Ny AzureRmWebAppSlot](/powershell/module/azurerm.websites/new-azurermwebappslot) | Skapar en distributionsplats för ett webbprogram. |
| [Ange AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) | Ändrar en resurs i en resursgrupp. |
| [Växlingen AzureRmWebAppSlot](/powershell/module/azurerm.websites/swap-azurermwebappslot) | Byter ut en webbapp distributionsplatsen till produktionen. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns [Azure PowerShell dokumentationen](/powershell/azure/overview).

Ytterligare Azure Powershell-exempel för Azure App Service Web Apps finns i den [Azure PowerShell-exempel](../app-service-powershell-samples.md).
