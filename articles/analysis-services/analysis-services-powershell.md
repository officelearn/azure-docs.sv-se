---
title: Hantera Azure Analysis Services med PowerShell | Microsoft-dokument
description: Beskriver Azure Analysis Services PowerShell-cmdlets för vanliga administrativa uppgifter som att skapa servrar, pausa åtgärder eller ändra tjänstnivå.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 10/28/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2c8f4c0541d97a189087af692658cfe794eaaf7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572699"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Hantera Azure Analysis Services med PowerShell

I den här artikeln beskrivs PowerShell-cmdletar som används för att utföra Azure Analysis Services-server- och databashanteringsuppgifter. 

Serverresurshanteringsuppgifter som att skapa eller ta bort en server, pausa eller återuppta serveråtgärder eller ändra tjänstnivån (nivå) använder Azure Analysis Services-cmdlets. Andra uppgifter för att hantera databaser som att lägga till eller ta bort rollmedlemmar, bearbeta eller partitionera använda cmdlets som ingår i samma SqlServer-modul som SQL Server Analysis Services.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Behörigheter

De flesta PowerShell-uppgifter kräver att du har administratörsbehörighet på den Analysis Services-server som du hanterar. Schemalagda PowerShell-aktiviteter är obevakade åtgärder. Kontot eller tjänstens huvudnamn som kör schemaläggaren måste ha administratörsbehörighet på Analysis Services-servern. 

För serveråtgärder som använder Azure PowerShell-cmdletar måste ditt konto eller kontot som kör schemaläggaren också tillhöra ägarrollen för resursen i [Azure Role-Based Access Control (RBAC)](../role-based-access-control/overview.md). 

## <a name="resource-and-server-operations"></a>Resurs- och serveråtgärder 

Installera modul - [Az.AnalysisServices](https://www.powershellgallery.com/packages/Az.AnalysisServices)   
Dokumentation - [Az.AnalysisServices referens](/powershell/module/az.analysisservices)

## <a name="database-operations"></a>Databasåtgärder

Azure Analysis Services-databasåtgärder använder samma SqlServer-modul som SQL Server Analysis Services. Alla cmdlets stöds dock inte för Azure Analysis Services. 

SqlServer-modulen tillhandahåller uppgiftsspecifika databashanterings-cmdlets samt den allmänna invoke-ASCmd-cmdleten som accepterar en TMSL-fråga (Tabular Model Scripting Language) eller skript. Följande cmdlets i SqlServer-modulen stöds för Azure Analysis Services.

Installera modul - [SqlServer](https://www.powershellgallery.com/packages/SqlServer)   
Dokumentation - [SqlServer-referens](/powershell/module/sqlserver)

### <a name="supported-cmdlets"></a>Cmdlets som stöds

|Cmdlet|Beskrivning|
|------------|-----------------| 
|[Tilläggsrollmedlem](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Lägg till en medlem i en databasroll.| 
|[Backup-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase)|Säkerhetskopiera en Analysis Services-databas.|  
|[Ta bort rollmedlem](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Ta bort en medlem från en databasroll.|   
|[Anropa-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|Kör ett TMSL-skript.|
|[Anropa-ProcessASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processasdatabase)|Bearbeta en databas.|  
|[Anropa-ProcessPartition](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processpartition)|Bearbeta en partition.| 
|[Anropa-ProcessTable](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processtable)|Bearbeta en tabell.|  
|[Kopplingspartition](https://docs.microsoft.com/powershell/module/sqlserver/merge-partition)|Slå samman en partition.|  
|[Återställ-ASDatabas](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase)|Återställa en Analysis Services-databas.| 
  

## <a name="related-information"></a>Relaterad information

* [SQL Server PowerShell](https://docs.microsoft.com/sql/powershell/sql-server-powershell)      
* [Ladda ned SQL Server PowerShell-modul](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Ladda ner SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [SqlServer-modul i PowerShell-galleriet](https://www.powershellgallery.com/packages/SqlServer)    
* [Tabellmodellprogrammering för kompatibilitetsnivå 1200 och högre](https://docs.microsoft.com/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)
