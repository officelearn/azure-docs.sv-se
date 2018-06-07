---
title: Så här avinstallerar du Verktyg för elastisk databas jobb
description: Lär dig mer om att avinstallera elastiska jobb databaskomponenterna med Azure PowerShell-portalen.
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: e1089db848b30945e5e61765c762262f5478450e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34645896"
---
# <a name="uninstall-elastic-database-jobs-components"></a>Avinstallera jobb komponenter för elastisk databas
**Den elastiska databasen jobb** komponenter kan avinstalleras med Azure-portalen eller PowerShell.

## <a name="uninstall-elastic-database-jobs-components-using-the-azure-portal"></a>Avinstallera elastisk databas jobb komponenter med hjälp av Azure portal
1. Öppna [Azure-portalen](https://portal.azure.com/).
2. Navigera till den prenumeration som innehåller **elastisk databas jobb** komponenter, nämligen prenumerationen i vilka elastisk databas jobb-komponenter installerades.
3. Klicka på **Bläddra** och på **resursgrupper**.
4. Välj den resursgrupp med namnet ”__ElasticDatabaseJob”.
5. Ta bort resursgruppen.

## <a name="uninstall--elastic-database-jobs-components-using-powershell"></a>Avinstallera elastisk databas jobb komponenter med hjälp av PowerShell
1. Starta Microsoft Azure PowerShell-Kommandotolken och navigera till verktyg underkatalog under mappen Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x: typen **cd verktyg**.
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x* > CD-verktyg
2. Köra.\UninstallElasticDatabaseJobs.ps1 PowerShell-skript.
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools > avblockera filen.\UninstallElasticDatabaseJobs.ps1 PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools >.\UninstallElasticDatabaseJobs.ps1

Eller helt enkelt, kör du följande skript, förutsatt att standard värden används på installation av komponenter:

        $ResourceGroupName = "__ElasticDatabaseJob"
        Switch-AzureMode AzureResourceManager

        $resourceGroup = Get-AzureResourceGroup -Name $ResourceGroupName
        if(!$resourceGroup)
        {
            Write-Host "The Azure Resource Group: $ResourceGroupName has already been deleted.  Elastic database job components are uninstalled."
            return
        }

        Write-Host "Removing the Azure Resource Group: $ResourceGroupName.  This may take a few minutes.”
        Remove-AzureResourceGroup -Name $ResourceGroupName -Force
        Write-Host "Completed removing the Azure Resource Group: $ResourceGroupName.  Elastic database job compoennts are now uninstalled."

## <a name="next-steps"></a>Nästa steg
Om du vill installera elastisk databas jobb, se [installera tjänsten jobbet elastisk databas](sql-database-elastic-jobs-service-installation.md)

En översikt över elastisk databas jobb finns [elastisk databas översikt över](sql-database-elastic-jobs-overview.md).

<!--Image references-->


