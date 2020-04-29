---
title: 'PowerShell: Anslut till SQL Database'
description: Lär dig hur du använder Azure PowerShell för att automatisera distribution och hantering av App Service. Det här exemplet visar hur du ansluter en app till en SQL Database.
tags: azure-service-management
ms.assetid: 055440a9-fff1-49b2-b964-9c95b364e533
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: bb5fc8bcc99a4439276f53325c029635143c86c5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "74685390"
---
# <a name="connect-an-app-service-app-to-a-sql-database"></a>Ansluta en App Service-app till en SQL-databas

I det här scenariot får du lära dig hur du skapar en Azure SQL-databas och en App Service-app. Sedan länkar du SQL-databasen till appen med hjälp av appinställningar.

Om det behövs installerar du Azure PowerShell med hjälp av den instruktion som finns i [Azure PowerShell-guiden](/powershell/azure/overview)och `Connect-AzAccount` kör sedan för att skapa en anslutning till Azure.

## <a name="sample-script"></a>Exempelskript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/connect-to-sql/connect-to-sql.ps1?highlight=13 "Connect an app to a SQL database")]

## <a name="clean-up-deployment"></a>Rensa distribution 

När skriptet har körts kan följande kommando användas för att ta bort resursgruppen, App Service-appen och alla relaterade resurser.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Obs! |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Skapar en App Service-plan. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Skapar en App Service-app. |
| [New-AzSQLServer](/powershell/module/az.sql/new-azsqlserver) | Skapar en SQL Database-server. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Skapar en brandväggsregel för SQL Database-server. |
| [New-AzSQLDatabase](/powershell/module/az.sql/new-azsqldatabase) | Skapar en databas eller en elastisk databas. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Ändrar konfigurationen för en App Service-app. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Fler Azure Powershell-exempel för Azure App Service finns i [Azure PowerShell-exempel](../samples-powershell.md).
