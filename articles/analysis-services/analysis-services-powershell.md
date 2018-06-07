---
title: Hantera Azure Analysis Services med PowerShell | Microsoft Docs
description: Azure Analysis Services-hantering med PowerShell.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 05/22/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b4e819bdce971e92e4b2d99e68f51ddbf8a22182
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34597475"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Hantera Azure Analysis Services med PowerShell

Den här artikeln beskriver PowerShell-cmdlets som används för att utföra Azure Analysis Services-servern och databasen hanteringsuppgifter. 

Hanteringsaktiviteter för Server, till exempel att skapa eller ta bort en server, pausa eller återuppta åtgärder eller ändra service-nivån (nivån) använda cmdlets för Azure Resource Manager (resurs) och Analysis Services (server) cmdlets. Andra uppgifter för hantering av databaser, till exempel att lägga till eller ta bort medlemmar i rollen bearbetning eller partitionering använda cmdletar som ingår i samma modul för SqlServer som SQL Server Analysis Services.

## <a name="permissions"></a>Behörigheter
De flesta PowerShell uppgifter kräver att du har administratörsrättigheter på Analysis Services-servern som du hanterar. Schemalagda aktiviteter för PowerShell är obevakad åtgärder. Principen eller körs Schemaläggaren måste ha administratörsrättigheter på Analysis Services-servern. 

För servern med hjälp av AzureRm cmdlets, ditt konto eller det konto som kör Schemaläggaren måste också tillhöra ägarrollen för resursen i [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md). 

## <a name="resource-management-operations"></a>Resurs-hanteringsåtgärder 
Modul - [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices)

|Cmdlet|Beskrivning| 
|------------|-----------------| 
|[Get-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/get-azurermanalysisservicesserver)|Hämtar information om en server-instans.|  
|[New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver)|Skapar en server-instans.|   
|[Ny AzureRmAnalysisServicesFirewallConfig](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesfirewallconfig)|Skapar en ny Analysis Services-brandväggen config.|   
|[Ny AzureRmAnalysisServicesFirewallRule](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesfirewallrule)|Skapar en ny brandväggsregel för Analysis Services.|   
|[Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/remove-azurermanalysisservicesserver)|Tar bort en server-instans.|  
|[Resume-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/resume-azurermanalysisservicesserver)|Återupptar en server-instans.|  
|[Suspend-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/suspend-azurermanalysisservicesserver)|Pausar ett server-instansen.| 
|[Set-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver)|Ändrar en server-instans.|   
|[Test-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/test-azurermanalysisservicesserver)|Testar förekomsten av en server-instans.| 

## <a name="server-management-operations"></a>Server-hanteringsåtgärder

Modul - [Azure.AnalysisServices](https://www.powershellgallery.com/packages/Azure.AnalysisServices)

|Cmdlet|Beskrivning| 
|------------|-----------------| 
|[Add-AzureAnalysisServicesAccount](/powershell/module/azure.analysisservices/add-azureanalysisservicesaccount)|Lägger till en autentiserad konto som används för Azure Analysis Services serverbegäranden cmdlet.| 
|[Export-AzureAnalysisServicesInstance]()|Exporterar en logg från en instans av Analysis Services-servern i den inloggade i miljön som anges i kommandot Lägg till AzureAnalysisServicesAccount|  
|[Restart-AzureAnalysisServicesInstance](/powershell/module/azurerm.analysisservices/restart-azureanalysisservicesinstance)|Startar om en instans av Analysis Services-server i miljön är inloggad för tillfället. angetts i kommandot Lägg till AzureAnalysisServicesAccount.|  
|[Synkronisera AzureAnalysisServicesInstance](/powershell/module/azurerm.analysisservices/restart-azureanalysisservicesinstance)|Synkroniserar en angiven databas på den angivna instansen av Analysis Services-servern till alla frågan scaleout instanser i den inloggade i miljön som anges i kommandot Lägg till AzureAnalysisServicesAccount|  

## <a name="database-operations"></a>Databasåtgärder

Åtgärder i Azure Analysis Services-databasen använder samma [SqlServer](https://www.powershellgallery.com/packages/SqlServer) modulen som SQL Server Analysis Services. Dock stöds inte alla cmdlets för Azure Analysis Services. 

SqlServer-modulen innehåller cmdlet: ar uppgiftsspecifika databasen samt allmänna Invoke-ASCmd cmdlet som accepterar en tabellvy modellen Scripting språk (TMSL) fråga eller skript. Följande cmdlets i modulen SqlServer har stöd för Azure Analysis Services.

  
|Cmdlet|Beskrivning|
|------------|-----------------| 
|[Lägg till RoleMember](https://msdn.microsoft.com/library/hh510167.aspx)|Lägga till en medlem i en databasroll.| 
|[Säkerhetskopiering ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet)|Säkerhetskopiera databas för Analysis Services.|  
|[Ta bort RoleMember](https://msdn.microsoft.com/library/hh510173.aspx)|Ta bort en medlem från en databasroll.|   
|[Invoke-ASCmd](https://msdn.microsoft.com/library/hh479579.aspx)|Köra ett skript för TMSL.|
|[Invoke-ProcessASDatabase](https://msdn.microsoft.com/library/mt651773.aspx)|Bearbeta en databas.|  
|[Invoke-ProcessPartition](https://msdn.microsoft.com/library/hh510164.aspx)|Bearbeta en partition.| 
|[Anropa ProcessTable](https://msdn.microsoft.com/library/mt651774.aspx)|Bearbeta en tabell.|  
|[Merge-Partition](https://msdn.microsoft.com/library/hh479576.aspx)|Koppla en partition.|  
|[Återställ ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet)|Återställa en Analysis Services-databas.| 
  

## <a name="related-information"></a>Relaterad information

* [Hämta SQL Server PowerShell-modulen](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Hämta SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [SqlServer-modul i PowerShell-galleriet](https://www.powershellgallery.com/packages/SqlServer)    
* [Tabell modellen Programming för kompatibilitet nivå 1200 och högre](https://msdn.microsoft.com/library/mt712541.aspx)
