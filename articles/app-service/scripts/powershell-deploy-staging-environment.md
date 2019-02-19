---
title: Skriptexempel för Azure PowerShell – Skapa en app och distribuera kod till en mellanlagringsplats | Microsoft Docs
description: Skriptexempel för Azure PowerShell – Skapa en webbapp och distribuera kod till en mellanlagringsmiljö
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 27cf0680-c3a9-4a58-9f71-6dec09f6b874
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: ec0a6fa516c11b443964380b0cfa35769d04b843
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56107420"
---
# <a name="create-a-web-app-and-deploy-code-to-a-staging-environment"></a>Skapa en webbapp och distribuera kod till en mellanlagringsmiljö

Det här exempelskriptet skapar en webbapp i App Service med ytterligare ett distributionsfack som kallas för ”mellanlagring”, och distribuerar sedan en exempelapp till ”mellanlagringsplatsen”.

Om det behövs installerar du Azure PowerShell med hjälp av instruktionerna i [Azure PowerShell-guiden](/powershell/azure/overview) och kör sedan `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="sample-script"></a>Exempelskript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.ps1?highlight=1 "Create a web app and deploy code to a staging environment")]

## <a name="clean-up-deployment"></a>Rensa distribution 

När skriptet har körts kan följande kommando användas för att ta bort resursgruppen, webbappen och alla relaterade resurser.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Skapar en App Service-plan. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Skapar en webbapp. |
| [Set-AzAppServicePlan](/powershell/module/az.websites/set-azappserviceplan) | Ändrar en App Service-plan för att ändra prisnivån. |
| [New-AzWebAppSlot](/powershell/module/az.websites/new-azwebappslot) | Skapar ett distributionsfack för en webbapp. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Ändrar en resurs i en resursgrupp. |
| [Switch-AzWebAppSlot](/powershell/module/az.websites/switch-azwebappslot) | Byter ut en webbapps distributionsfack till produktion. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Fler Azure Powershell-exempel för Azure App Service Web Apps finns i [Azure PowerShell-exempel](../samples-powershell.md).
