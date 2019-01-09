---
title: Skriptexempel för Azure PowerShell – Övervaka en webbapp med webbserverloggar | Microsoft Docs
description: Skriptexempel för Azure PowerShell – Övervaka en webbapp med webbserverloggar
services: app-service\web
documentationcenter: ''
author: syntaxc4
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 5805d7cd-9e56-4eba-bd85-75b013690ff5
ms.service: app-service
ms.devlang: multiple
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 898d9affcd529b75a5be551b00ab2369ac03cd98
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53587113"
---
# <a name="monitor-a-web-appwith-web-server-logs"></a>Övervaka en webbapp med webbserverloggar

I det här scenariet skapar du en resursgrupp, en App Service-plan och en webbapp och konfigurerar webbappen för att aktivera webbserverloggar. Sedan hämtar du loggfilerna för granskning.

Om det behövs installerar du Azure PowerShell med hjälp av instruktionerna i [Azure PowerShell-guiden](/powershell/azure/overview) och kör sedan `Connect-AzureRmAccount` för att skapa en anslutning till Azure.

## <a name="sample-script"></a>Exempelskript

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/monitor-with-logs/monitor-with-logs.ps1 "Monitor a web app with web server logs")]

## <a name="clean-up-deployment"></a>Rensa distribution 

När skriptet har körts kan följande kommando användas för att ta bort resursgruppen, webbappen och alla relaterade resurser.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | Skapar en App Service-plan. |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Skapar en webbapp. |
| [Set-AzureRmWebApp](/powershell/module/azurerm.websites/set-azurermwebapp) | Ändrar konfigurationen för en webbapp. |
| [Get-AzureRMWebAppMetrics](/powershell/module/azurerm.websites/get-azurermwebappmetrics) | Hämtar en webbapps mått. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Fler Azure Powershell-exempel för Azure App Service Web Apps finns i [Azure PowerShell-exempel](../samples-powershell.md).
