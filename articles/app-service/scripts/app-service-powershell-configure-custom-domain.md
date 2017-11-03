---
title: "Azure PowerShell skriptexempel - tilldela en anpassad domän till en webbapp | Microsoft Docs"
description: "Azure PowerShell skriptexempel - tilldela en anpassad domän till en webbapp"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 356f5af9-f62e-411c-8b24-deba05214103
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 6d25fe8098848fc69470c77e3200bee554c1f875
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="assign-a-custom-domain-to-a-web-app"></a>Tilldela en anpassad domän till en webbapp

Det här exempelskriptet skapar en webbapp i App Service med dess relaterade resurser och sedan mappa `www.<yourdomain>` till den. 

Om det behövs installerar du Azure PowerShell med hjälp av anvisningarna i den [Azure PowerShell guiden](/powershell/azure/overview), och kör sedan `Login-AzureRmAccount` att skapa en anslutning med Azure. Dessutom måste ha åtkomst till din domänregistrator DNS-konfigurationssidan.

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/app-service/map-custom-domain/map-custom-domain.ps1?highlight=1 "Assign a custom domain to a web app")]

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
| [Ange AzureRmWebApp](/powershell/module/azurerm.websites/set-azurermwebapp) | Ändrar någon konfiguration för ett webbprogram. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns [Azure PowerShell dokumentationen](/powershell/azure/overview).

Ytterligare Azure Powershell-exempel för Azure App Service Web Apps finns i den [Azure PowerShell-exempel](../app-service-powershell-samples.md).
