---
title: 'PowerShell: Bildskärm med webbserverloggar'
description: Lär dig hur du använder Azure PowerShell för att automatisera distribution och hantering av App Service. Det här exemplet visar hur du övervakar en app med webbserverloggar.
tags: azure-service-management
ms.assetid: 5805d7cd-9e56-4eba-bd85-75b013690ff5
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: c9b3ab6f8115f68ead478c0de21e4661d3ca032c
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437935"
---
# <a name="monitor-a-web-appwith-web-server-logs"></a>Övervaka en webbapp med webbserverloggar

I det här scenariet skapar du en resursgrupp, en App Service-plan och en webbapp och konfigurerar webbappen för att aktivera webbserverloggar. Sedan hämtar du loggfilerna för granskning.

Om det behövs installerar du Azure PowerShell med hjälp av instruktionen som finns i [Azure PowerShell-guiden](/powershell/azure/overview)och kör `Connect-AzAccount` sedan för att skapa en anslutning med Azure.

## <a name="sample-script"></a>Exempelskript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/monitor-with-logs/monitor-with-logs.ps1 "Monitor a web app with web server logs")]

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
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Ändrar konfigurationen för en webbapp. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Fler Azure Powershell-exempel för Azure App Service Web Apps finns i [Azure PowerShell-exempel](../samples-powershell.md).
