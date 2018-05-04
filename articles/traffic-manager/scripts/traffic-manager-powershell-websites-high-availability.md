---
title: Skriptexempel Azure PowerShell - vägtrafik för hög tillgänglighet för program | Microsoft Docs
description: Skriptexempel Azure PowerShell - vägtrafik för hög tillgänglighet för program
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-infrastructure
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 04/26/2018
ms.author: kumud
ms.openlocfilehash: 13c24c31606d99f27ed2607fec71b381160624dd
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2018
---
# <a name="route-traffic-for-high-availability-of-applications-using-azure-powershell"></a>Vidarebefordra trafik för hög tillgänglighet för program med hjälp av Azure PowerShell

Det här skriptet skapar en resursgrupp, två apptjänstplaner, två webbappar, en traffic manager-profil och två traffic manager-slutpunkter. Traffic Manager dirigerar trafik till program i en region som den primära regionen och till den sekundära regionen när programmet i den primära regionen är inte tillgänglig. Innan du kör skriptet måste du ändra värdena MyWebApp, MyWebAppL1 och MyWebAppL2 till unika värden i Azure. Du kan komma åt appen i den primära regionen med URL-mywebapp.trafficmanager.net när skriptet har körts.

Om det behövs installerar du Azure PowerShell med hjälp av instruktionerna i [Azure PowerShell-guiden](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) och kör sedan `Connect-AzureRmAccount` för att skapa en anslutning till Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.ps1 "Route traffic for high availability")]


Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup1
Remove-AzureRmResourceGroup -Name myResourceGroup2
```


## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, en webbapp, en Traffic Manager-profil och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)  | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | Skapar en App Service-plan. Detta påminner om en servergrupp för din Azure webbapp. |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Skapar ett Azure-webbapp i App Service-plan. |
| [Set-AzureRmResource](/powershell/module/azurerm.resources/new-azurermresource) | Skapar ett Azure-webbapp i App Service-plan. |
| [Ny AzureRmTrafficManagerProfile](/powershell/module/azurerm.trafficmanager/new-azurermtrafficmanagerprofile) | Skapar en Azure Traffic Manager-profil. |
| [Ny AzureRmTrafficManagerEndpoint](/powershell/module/azurerm.trafficmanager/new-azurermtrafficmanagerendpoint) | Lägger till en slutpunkt för en Azure Traffic Manager-profilen. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/azure/overview).

Ytterligare nätverk PowerShell-skript-exempel finns i den [dokumentation för Azure-nätverk – översikt](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
