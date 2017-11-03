---
title: "Azure PowerShell-skript exempel – ansluta en webbapp till en SQL-databas | Microsoft Docs"
description: "Azure PowerShell-skript exempel – ansluta en webbapp till en SQL-databas"
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 055440a9-fff1-49b2-b964-9c95b364e533
ms.service: app-service
ms.devlang: multiple
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 5312bf6b81d1cc48490b71c3f77323cca23e1559
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="connect-a-web-app-to-a-sql-database"></a>Ansluta en webbapp till en SQL-databas

I det här scenariot du lära dig hur du skapar en Azure SQL database och en Azure-webbapp. Du kan länka SQL-databasen till det webbprogram som använder app-inställningar.

Om det behövs installerar du Azure PowerShell med hjälp av anvisningarna i den [Azure PowerShell guiden](/powershell/azure/overview), och kör sedan `Login-AzureRmAccount` att skapa en anslutning med Azure.

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/app-service/connect-to-sql/connect-to-sql.ps1?highlight=13 "Connect a web app to a SQL database")]

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
| [Ny AzureRMSQLServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | Skapar en SQL Database-server. |
| [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) | Skapar en brandväggsregel för en SQL Database-server. |
| [Ny AzureRMSQLDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) | Skapar en databas eller en elastisk databas. |
| [Ange AzureRmWebApp](/powershell/module/azurerm.websites/set-azurermwebapp) | Ändrar någon konfiguration för ett webbprogram. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns [Azure PowerShell dokumentationen](/powershell/azure/overview).

Ytterligare Azure Powershell-exempel för Azure App Service Web Apps finns i den [Azure PowerShell-exempel](../app-service-powershell-samples.md).
