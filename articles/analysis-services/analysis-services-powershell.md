---
title: Hantera Azure Analysis Services med PowerShell | Microsoft Docs
description: Azure Analysis Services-hantering med PowerShell.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 12/19/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8f309d628ec4531ca5594ecb25533b1484e8b274
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56732406"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Hantera Azure Analysis Services med PowerShell

Den här artikeln beskriver PowerShell-cmdletar som används för att utföra Azure Analysis Services-servern och databashanteringsuppgifter. 

Server-hanteringsåtgärder som att skapa eller ta bort en server, pausa eller återuppta serveråtgärder eller ändra servicenivån (nivå) använda cmdlets för Azure Resource Manager (resurs) och Analysis Services (server)-cmdletar. Andra uppgifter för hantering av databaser som att lägga till eller ta bort rollmedlemmar, behandla eller partitionering används cmdletar som ingår i samma SqlServer-modulen som SQL Server Analysis Services.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Behörigheter

De flesta PowerShell uppgifter kräver att du har Admin-behörighet på Analysis Services-servern som du hanterar. Schemalagda aktiviteter för PowerShell är obevakad åtgärder. Kontot eller tjänstens huvudnamn som kör scheduler måste ha administratörsrättigheter på Analysis Services-servern. 

För åtgärder med hjälp av Azure PowerShell-cmdletar, ditt konto eller det konto som kör scheduler måste tillhöra rollen ägare för resursen i [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md). 

## <a name="resource-management-operations"></a>Resurs-hanteringsåtgärder 

Module - [Az.AnalysisServices](/powershell/module/az.analysisservices)

|Cmdlet|Beskrivning| 
|------------|-----------------| 
|[Get-AzAnalysisServicesServer](/powershell/module/az.analysisservices/get-azanalysisservicesserver)|Hämtar information om en server-instans.|  
|[New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver)|Skapar en server-instans.|   
|[New-AzAnalysisServicesFirewallConfig](/powershell/module/az.analysisservices/new-azanalysisservicesfirewallconfig)|Skapar en ny Analysis Services-konfiguration för brandväggen.|   
|[New-AzAnalysisServicesFirewallRule](/powershell/module/az.analysisservices/new-azanalysisservicesfirewallrule)|Skapar en ny brandväggsregel för Analysis Services.|   
|[Remove-AzAnalysisServicesServer](/powershell/module/az.analysisservices/remove-azanalysisservicesserver)|Tar bort en server-instans.|  
|[Resume-AzAnalysisServicesServer](/powershell/module/az.analysisservices/resume-azanalysisservicesserver)|Återupptar en server-instans.|  
|[Suspend-AzAnalysisServicesServer](/powershell/module/az.analysisservices/suspend-azanalysisservicesserver)|Pausar en server-instans.| 
|[Set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver)|Ändrar en server-instans.|   
|[Test-AzAnalysisServicesServer](/powershell/module/az.analysisservices/test-azanalysisservicesserver)|Testar om en server-instans.| 

## <a name="server-management-operations"></a>Server-hanteringsåtgärder

Modul - [Azure.AnalysisServices](https://www.powershellgallery.com/packages/Azure.AnalysisServices)

|Cmdlet|Beskrivning| 
|------------|-----------------| 
|[Add-AzAnalysisServicesAccount](/powershell/module/azure.analysisservices/add-AzAnalysisServicesaccount)|Lägger till en autentiserad konto som ska användas för Azure Analysis Services serverbegäranden för cmdlet.| 
|[Export-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/export-AzAnalysisServicesinstancelog)|Exporterar en logg från en instans av Analysis Services-servern i den för tillfället inloggade miljö som anges i kommandot Lägg till AzAnalysisServicesAccount|  
|[Restart-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/restart-AzAnalysisServicesinstance)|Startar om en instans av Analysis Services-servern i inloggade miljön. anges i Lägg till AzAnalysisServicesAccount kommandot.|  
|[Sync-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/restart-AzAnalysisServicesinstance)|Synkroniserar en angiven databas på den angivna instansen av Analysis Services-servern på alla fråga skalbar instanser i den för tillfället inloggade miljö som anges i kommandot Lägg till AzAnalysisServicesAccount|  

## <a name="database-operations"></a>Databasoperationer

Azure Analysis Services-databasåtgärder använder samma [SqlServer-modulen](https://www.powershellgallery.com/packages/SqlServer) som SQL Server Analysis Services. Alla cmdlets har dock stöd för Azure Analysis Services. Mer information finns i [SQL Server PowerShell](https://docs.microsoft.com/sql/powershell/sql-server-powershell).

SqlServer-modulen innehåller uppgiftsspecifika database management-cmdletar samt den allmänna Invoke-ASCmd-cmdlet som accepterar en fråga Tabular Model Tabellmodellskriptspråket (TMSL) eller ett skript. Följande cmdletar i SqlServer-modulen har stöd för Azure Analysis Services.

  
|Cmdlet|Beskrivning|
|------------|-----------------| 
|[Lägg till RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Lägga till en medlem i en databasroll.| 
|[Backup-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase)|Säkerhetskopiera en Analysis Services-databas.|  
|[Remove-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Ta bort medlem från en databasroll.|   
|[Invoke-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|Köra ett TMSL-skript.|
|[Invoke-ProcessASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processasdatabase)|Bearbeta en databas.|  
|[Invoke-ProcessPartition](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processpartition)|Bearbeta en partition.| 
|[Anropa ProcessTable](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processtable)|Bearbeta en tabell.|  
|[Merge-Partition](https://docs.microsoft.com/powershell/module/sqlserver/merge-partition)|Koppla en partition.|  
|[Restore-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase)|Återställa en Analysis Services-databas.| 
  

## <a name="related-information"></a>Relaterad information

* [Hämta PowerShell-modulen för SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Ladda ned SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [SqlServer-modulen i PowerShell-galleriet](https://www.powershellgallery.com/packages/SqlServer)    
* [Tabular Model programmeringsspråk för kompatibilitet på 1200 och högre](https://msdn.microsoft.com/library/mt712541.aspx)
