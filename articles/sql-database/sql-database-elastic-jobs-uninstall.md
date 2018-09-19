---
title: Så här avinstallerar du Verktyg för elastic database-jobb
description: Lär dig mer om att avinstallera elastiska jobb databaskomponenterna med Azure PowerShell-portalen.
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 06/14/2018
ms.author: sstein
ms.openlocfilehash: 395bbf50373d3a6e3848fba9fd3db0d6989023f4
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2018
ms.locfileid: "35649494"
---
# <a name="uninstall-elastic-database-jobs-components"></a>Avinstallera elastiska jobb databaskomponenter


[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]


**Elastic Database-jobb** komponenter kan avinstalleras med hjälp av Azure-portalen eller PowerShell.

## <a name="uninstall-elastic-database-jobs-components-using-the-azure-portal"></a>Avinstallera elastiska jobb databaskomponenter med Azure portal
1. Öppna [Azure-portalen](https://portal.azure.com/).
2. Navigera till den prenumeration som innehåller **elastiska databasjobb** komponenter, nämligen den prenumeration i vilken Elastic Database jobb komponenter har installerats.
3. Klicka på **Bläddra** och klicka på **resursgrupper**.
4. Välj resursgruppen med namnet ”__ElasticDatabaseJob”.
5. Ta bort resursgruppen.

## <a name="uninstall--elastic-database-jobs-components-using-powershell"></a>Avinstallera elastiska jobb databaskomponenter med hjälp av PowerShell
1. Öppna ett Microsoft Azure PowerShell-kommandofönster och gå till verktyg underkatalog under mappen Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x: typen **cd-verktyg**.
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x* > cd-verktyg
2. Köra.\UninstallElasticDatabaseJobs.ps1 PowerShell-skript.
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools > avblockera filen.\UninstallElasticDatabaseJobs.ps1 PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools >. \ UninstallElasticDatabaseJobs.ps1

Eller bara, kör du följande skript, förutsatt att standard värden används på installation av komponenter:

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
Om du vill installera Elastic Database-jobb, se [installera tjänsten för Elastic Database-jobb](sql-database-elastic-jobs-service-installation.md)

En översikt över Elastic Database-jobb finns i [översikt över elastiska databasjobb](sql-database-elastic-jobs-overview.md).

<!--Image references-->


