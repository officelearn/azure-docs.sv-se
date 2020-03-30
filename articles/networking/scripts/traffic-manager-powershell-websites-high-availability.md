---
title: Dirigera trafik för HA-program - Azure PowerShell - Traffic Manager
description: Exempel på Azure PowerShell-skript – Dirigera trafik för hög tillgänglighet av program
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
manager: KumudD
ms.service: traffic-manager
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 05/16/2017
ms.author: allensu
ms.openlocfilehash: 183599fccfad1806faae3cb90de225d388b77da8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74049235"
---
# <a name="route-traffic-for-high-availability-of-applications---azure-powershell"></a>Dirigera trafik för hög tillgänglighet av program - Azure PowerShell

Det här skriptet skapar en resursgrupp, två apptjänstplaner, två webbappar, en traffic manager-profil och två slutpunkter för Traffic Manager. Traffic Manager dirigerar trafik till programmet i en region som den primära regionen och till den sekundära regionen när programmet i den primära regionen inte är tillgänglig. Innan du kör skriptet måste du ändra värdena MyWebApp, MyWebAppL1 och MyWebAppL2 till unika värden i Azure. När du har kört skriptet kan du komma åt appen i den primära regionen med URL-mywebapp.trafficmanager.net.

Om det behövs installerar du Azure PowerShell med hjälp av instruktionen som finns i [Azure PowerShell-guiden](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)och kör `Connect-AzAccount` sedan för att skapa en anslutning med Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.ps1 "Route traffic for high availability")]


Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```powershell
Remove-AzResourceGroup -Name myResourceGroup1
Remove-AzResourceGroup -Name myResourceGroup2
```


## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, en webbapp, en Traffic Manager-profil och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)  | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Skapar en App Service-plan. Det här är som en servergrupp för din Azure-webbapp. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Skapar en Azure-webbapp i App Service-planen. |
| [Set-AzResource](/powershell/module/az.resources/new-azresource) | Skapar en Azure-webbapp i App Service-planen. |
| [New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) | Skapar en Azure Traffic Manager-profil. |
| [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) | Lägger till en slutpunkt i en Azure Traffic Manager-profil. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/azure/overview).

Ytterligare exempel på PowerShell-nätverksskript finns i [dokumentation för översikt över Azure-nätverk](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
