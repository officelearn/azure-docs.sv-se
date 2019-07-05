---
title: Hantera Azure Analysis Services med PowerShell | Microsoft Docs
description: Azure Analysis Services-hantering med PowerShell.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 07/01/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a958c33e173c881a3ad09a49fe9f71ddb0c9df56
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508940"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Hantera Azure Analysis Services med PowerShell

Den här artikeln beskriver PowerShell-cmdletar som används för att utföra Azure Analysis Services-servern och databashanteringsuppgifter. 

Hanteringsaktiviteter för serverresurser som att skapa eller ta bort en server, pausa eller återuppta serveråtgärder eller ändra servicenivån (nivå) använda cmdlets för Azure Analysis Services. Andra uppgifter för hantering av databaser som att lägga till eller ta bort rollmedlemmar, behandla eller partitionering används cmdletar som ingår i samma SqlServer-modulen som SQL Server Analysis Services.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Behörigheter

De flesta PowerShell uppgifter kräver att du har Admin-behörighet på Analysis Services-servern som du hanterar. Schemalagda aktiviteter för PowerShell är obevakad åtgärder. Kontot eller tjänstens huvudnamn som kör scheduler måste ha administratörsrättigheter på Analysis Services-servern. 

För åtgärder med hjälp av Azure PowerShell-cmdletar, ditt konto eller det konto som kör scheduler måste tillhöra rollen ägare för resursen i [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md). 

## <a name="resource-and-server-operations"></a>Resurs-och server 

Installera modulen - [Az.AnalysisServices](https://www.powershellgallery.com/packages/Az.AnalysisServices)   
Dokumentation – [Az.AnalysisServices referens](/powershell/module/az.analysisservices)

## <a name="database-operations"></a>Databasoperationer

Azure Analysis Services-databasåtgärder använda samma SqlServer-modulen som SQL Server Analysis Services. Alla cmdlets har dock stöd för Azure Analysis Services. 

SqlServer-modulen innehåller uppgiftsspecifika database management-cmdletar samt den allmänna Invoke-ASCmd-cmdlet som accepterar en fråga Tabular Model Tabellmodellskriptspråket (TMSL) eller ett skript. Följande cmdletar i SqlServer-modulen har stöd för Azure Analysis Services.

Installera modulen - [SqlServer](https://www.powershellgallery.com/packages/SqlServer)   
Dokumentation – [SqlServer-referens](/powershell/module/sqlserver)

### <a name="supported-cmdlets"></a>Cmdlet: ar som stöds

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

* [SQL Server PowerShell](https://docs.microsoft.com/sql/powershell/sql-server-powershell)      
* [Hämta PowerShell-modulen för SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Ladda ned SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [SqlServer-modulen i PowerShell-galleriet](https://www.powershellgallery.com/packages/SqlServer)    
* [Tabular Model programmeringsspråk för kompatibilitet på 1200 och högre](/sql/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)
