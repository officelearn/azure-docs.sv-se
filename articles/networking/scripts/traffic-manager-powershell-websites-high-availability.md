---
title: "Azure PowerShell skriptexempel - vägtrafik för hög tillgänglighet för program | Microsoft Docs"
description: "Azure PowerShell skriptexempel - vägtrafik för hög tillgänglighet för program"
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: georgewallace
tags: azure-infrastructure
ms.assetid: 
ms.service: traffic-manager
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 05/16/2017
ms.author: gwallace
ms.openlocfilehash: 2f0ac4fd1779661aab04bafb217e64af5d619a2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="route-traffic-for-high-availability-of-applications"></a>Vidarebefordra trafik för hög tillgänglighet för program

Det här skriptet skapar en resursgrupp, två apptjänstplaner, två webbappar, en traffic manager-profil och två traffic manager-slutpunkter. Traffic Manager dirigerar trafik till program i en region som den primära regionen och till den sekundära regionen när programmet i den primära regionen är inte tillgänglig. Innan du kör skriptet måste du ändra värdena MyWebApp, MyWebAppL1 och MyWebAppL2 till unika värden i Azure. Du kan komma åt appen i den primära regionen med URL-mywebapp.trafficmanager.net när skriptet har körts.

Om det behövs installerar du Azure PowerShell med hjälp av anvisningarna i den [Azure PowerShell guiden](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/), och kör sedan `Login-AzureRmAccount` att skapa en anslutning med Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.ps1 "Route traffic for high availability")]


Kör följande kommando för att ta bort resursgruppen, virtuell dator och alla relaterade resurser.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup1
Remove-AzureRmResourceGroup -Name myResourceGroup2
```


## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att skapa en resursgrupp, webbprogram, trafikhanterarprofilen och alla relaterade resurser. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Ny AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)  | Skapar en resursgrupp som är lagrade i alla resurser. |
| [Ny AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | Skapar en App Service-plan. Detta påminner om en servergrupp för din Azure webbapp. |
| [Ny AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Skapar ett Azure-webbapp i App Service-plan. |
| [Ange AzureRmResource](/powershell/module/azurerm.resources/new-azurermresource) | Skapar ett Azure-webbapp i App Service-plan. |
| [Ny AzureRmTrafficManagerProfile](/powershell/module/azurerm.trafficmanager/new-azurermtrafficmanagerprofile) | Skapar en Azure Traffic Manager-profil. |
| [Ny AzureRmTrafficManagerEndpoint](/powershell/module/azurerm.trafficmanager/new-azurermtrafficmanagerendpoint) | Lägger till en slutpunkt för en Azure Traffic Manager-profilen. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns [Azure PowerShell dokumentationen](https://docs.microsoft.com/powershell/azure/overview).

Ytterligare nätverk PowerShell-skript-exempel finns i den [dokumentation för Azure-nätverk – översikt](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).