---
title: Så här avinstallerar du Verktyg för elastic database-jobb
description: Lär dig mer om att avinstallera elastiska jobb databaskomponenterna med Azure PowerShell-portalen.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 12f923724616378a6ea6c83a947bd5362840a697
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57768872"
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
1. Öppna ett Microsoft Azure PowerShell-kommandofönster och gå till verktyg underkatalog under mappen Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x: Typ **cd-verktyg**.
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x* > cd-verktyg
2. Köra.\UninstallElasticDatabaseJobs.ps1 PowerShell-skript.
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools > avblockera filen.\UninstallElasticDatabaseJobs.ps1 PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools >. \ UninstallElasticDatabaseJobs.ps1

Eller bara, kör du följande skript, förutsatt att standard värden används på installation av komponenter:

```powershell
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
Write-Host "Completed removing the Azure Resource Group: $ResourceGroupName.  Elastic database job components are now uninstalled."
```

## <a name="next-steps"></a>Nästa steg
Om du vill installera Elastic Database-jobb, se [installera tjänsten för Elastic Database-jobb](sql-database-elastic-jobs-service-installation.md)

En översikt över Elastic Database-jobb finns i [översikt över elastiska databasjobb](sql-database-elastic-jobs-overview.md).
