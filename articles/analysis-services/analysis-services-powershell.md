---
title: Hantera Azure Analysis Services med PowerShell | Microsoft Docs
description: Azure Analysis Services-hantering med PowerShell.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 06/25/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6c648a9cb6b8d8dbfb60f1a5a6ebc386c57460b0
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42887254"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Hantera Azure Analysis Services med PowerShell

Den här artikeln beskriver PowerShell-cmdletar som används för att utföra Azure Analysis Services-servern och databashanteringsuppgifter. 

Server-hanteringsuppgifter, till exempel skapa eller ta bort en server, pausa eller återuppta serveråtgärder eller ändra servicenivån (nivå) använda cmdlets för Azure Resource Manager (resurs) och Analysis Services (server)-cmdletar. Andra uppgifter för hantering av databaser, till exempel att lägga till eller ta bort rollmedlemmar, behandla eller partitionering använda cmdletar som ingår i samma SqlServer-modulen som SQL Server Analysis Services.

## <a name="permissions"></a>Behörigheter
De flesta PowerShell uppgifter kräver att du har Admin-behörighet på Analysis Services-servern som du hanterar. Schemalagda aktiviteter för PowerShell är obevakad åtgärder. Principen till kontot eller tjänsten som kör scheduler måste ha administratörsrättigheter på Analysis Services-servern. 

För åtgärder med hjälp av AzureRm-cmdletar, ditt konto eller det konto som kör scheduler måste tillhöra rollen ägare för resursen i [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md). 

## <a name="resource-management-operations"></a>Resurs-hanteringsåtgärder 
Modul - [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices)

|Cmdlet|Beskrivning| 
|------------|-----------------| 
|[Get-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/get-azurermanalysisservicesserver)|Hämtar information om en server-instans.|  
|[New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver)|Skapar en server-instans.|   
|[Ny AzureRmAnalysisServicesFirewallConfig](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesfirewallconfig)|Skapar en ny Analysis Services-konfiguration för brandväggen.|   
|[Ny AzureRmAnalysisServicesFirewallRule](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesfirewallrule)|Skapar en ny brandväggsregel för Analysis Services.|   
|[Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/remove-azurermanalysisservicesserver)|Tar bort en server-instans.|  
|[Resume-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/resume-azurermanalysisservicesserver)|Återupptar en server-instans.|  
|[Suspend-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/suspend-azurermanalysisservicesserver)|Pausar en server-instans.| 
|[Set-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver)|Ändrar en server-instans.|   
|[Test-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/test-azurermanalysisservicesserver)|Testar om en server-instans.| 

## <a name="server-management-operations"></a>Server-hanteringsåtgärder

Modul - [Azure.AnalysisServices](https://www.powershellgallery.com/packages/Azure.AnalysisServices)

|Cmdlet|Beskrivning| 
|------------|-----------------| 
|[Add-AzureAnalysisServicesAccount](/powershell/module/azure.analysisservices/add-azureanalysisservicesaccount)|Lägger till en autentiserad konto som ska användas för Azure Analysis Services serverbegäranden för cmdlet.| 
|[Export-AzureAnalysisServicesInstance](/powershell/module/azurerm.analysisservices/export-azureanalysisservicesinstancelog)|Exporterar en logg från en instans av Analysis Services-servern i den för tillfället inloggade miljö som anges i kommandot Add-AzureAnalysisServicesAccount|  
|[Restart-AzureAnalysisServicesInstance](/powershell/module/azurerm.analysisservices/restart-azureanalysisservicesinstance)|Startar om en instans av Analysis Services-servern i inloggade miljön. anges i Add-AzureAnalysisServicesAccount kommandot.|  
|[Synkronisera AzureAnalysisServicesInstance](/powershell/module/azurerm.analysisservices/restart-azureanalysisservicesinstance)|Synkroniserar en angiven databas på den angivna instansen av Analysis Services-servern på alla fråga scaleout-instanser i den för tillfället inloggade miljö som anges i kommandot Add-AzureAnalysisServicesAccount|  

## <a name="database-operations"></a>Databasoperationer

Azure Analysis Services-databasåtgärder använder samma [SqlServer-modulen](https://www.powershellgallery.com/packages/SqlServer) som SQL Server Analysis Services. Alla cmdlets har dock stöd för Azure Analysis Services. Läs mer i [SQL Server PowerShell](https://docs.microsoft.com/sql/powershell/sql-server-powershell).

SqlServer-modulen innehåller uppgiftsspecifika database management-cmdletar samt den allmänna Invoke-ASCmd-cmdlet som accepterar en fråga Tabular Model Tabellmodellskriptspråket (TMSL) eller ett skript. Följande cmdletar i SqlServer-modulen har stöd för Azure Analysis Services.

  
|Cmdlet|Beskrivning|
|------------|-----------------| 
|[Lägg till RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Lägga till en medlem i en databasroll.| 
|[Backup-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase)|Säkerhetskopiera en Analysis Services-databas.|  
|[Ta bort RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Ta bort medlem från en databasroll.|   
|[Invoke-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|Köra ett TMSL-skript.|
|[Invoke-ProcessASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processasdatabase)|Bearbeta en databas.|  
|[Invoke-ProcessPartition](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processpartition)|Bearbeta en partition.| 
|[Anropa ProcessTable](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processtable)|Bearbeta en tabell.|  
|[Merge-Partition](https://docs.microsoft.com/powershell/module/sqlserver/merge-partition)|Koppla en partition.|  
|[Återställ ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase)|Återställa en Analysis Services-databas.| 
  

## <a name="related-information"></a>Relaterad information

* [Hämta PowerShell-modulen för SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Ladda ned SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [SqlServer-modulen i PowerShell-galleriet](https://www.powershellgallery.com/packages/SqlServer)    
* [Tabular Model programmeringsspråk för kompatibilitet på 1200 och högre](https://msdn.microsoft.com/library/mt712541.aspx)
