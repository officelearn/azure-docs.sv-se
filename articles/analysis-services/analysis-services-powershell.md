---
title: Hantera Azure Analysis Services med PowerShell | Microsoft Docs
description: Azure Analysis Services hantering med PowerShell.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 10/28/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 329ea51895c45c8cdf50a323328fac3c580ffc16
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73146218"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Hantera Azure Analysis Services med PowerShell

I den här artikeln beskrivs PowerShell-cmdletar som används för att utföra Azure Analysis Services server-och databas hanterings uppgifter. 

Hanterings uppgifter för server resurser som att skapa eller ta bort en server, pausa eller återuppta Server åtgärder, eller ändra service nivån (nivån) använder Azure Analysis Services-cmdletar. Andra aktiviteter för att hantera databaser som att lägga till eller ta bort roll medlemmar, bearbetning eller partitionering använder cmdletar som ingår i samma SqlServer-modul som SQL Server Analysis Services.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Behörigheter

De flesta PowerShell-aktiviteter kräver att du har administratörs behörighet på den Analysis Services server som du hanterar. Schemalagda PowerShell-aktiviteter är obevakade åtgärder. Kontot eller tjänstens huvud namn som kör Scheduler måste ha administratörs behörighet på Analysis Servicess servern. 

För Server åtgärder med hjälp av Azure PowerShell-cmdletar måste ditt konto eller kontot som kör Scheduler också tillhöra ägar rollen för resursen i [Azure Role-Based Access Control (RBAC)](../role-based-access-control/overview.md). 

## <a name="resource-and-server-operations"></a>Resurs-och Server åtgärder 

Installera modul- [AZ. AnalysisServices](https://www.powershellgallery.com/packages/Az.AnalysisServices)   
Dokumentation – [referens för AZ. AnalysisServices](/powershell/module/az.analysisservices)

## <a name="database-operations"></a>Databas åtgärder

Azure Analysis Services databas åtgärder använder samma SqlServer-modul som SQL Server Analysis Services. Men alla cmdletar stöds inte för Azure Analysis Services. 

SqlServer-modulen innehåller verksamhetsspecifika databas hanterings-cmdletar samt den allmänna cmdleten Invoke-ASCmd som accepterar en fråga eller ett skript för tabell modell skript språk (TMSL). Följande cmdletar i SqlServer-modulen stöds för Azure Analysis Services.

Installera modul – [SqlServer](https://www.powershellgallery.com/packages/SqlServer)   
Dokumentation – [SQLServer-referens](/powershell/module/sqlserver)

### <a name="supported-cmdlets"></a>Cmdlets som stöds

|Cmdlet|Beskrivning|
|------------|-----------------| 
|[Add-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Lägg till en medlem i en databas roll.| 
|[Säkerhets kopierings databas](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase)|Säkerhetskopiera en Analysis Services databas.|  
|[Remove-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Ta bort en medlem från en databas roll.|   
|[Invoke-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|Kör ett TMSL-skript.|
|[Invoke-ProcessASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processasdatabase)|Bearbeta en databas.|  
|[Invoke-ProcessPartition](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processpartition)|Bearbeta en partition.| 
|[Invoke-ProcessTable](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processtable)|Bearbetar en tabell.|  
|[Sammanslagning-partition](https://docs.microsoft.com/powershell/module/sqlserver/merge-partition)|Sammanfoga en partition.|  
|[Restore-Database](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase)|Återställa en Analysis Services databas.| 
  

## <a name="related-information"></a>Relaterad information

* [SQL Server PowerShell](https://docs.microsoft.com/sql/powershell/sql-server-powershell)      
* [Ladda ned SQL Server PowerShell-modul](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Ladda ned SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [SqlServer-modul i PowerShell-galleriet](https://www.powershellgallery.com/packages/SqlServer)    
* [Tabell modell programmering för kompatibilitetsnivå 1200 och högre](https://docs.microsoft.com/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)
