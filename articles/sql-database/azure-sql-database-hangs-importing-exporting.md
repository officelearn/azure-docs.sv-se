---
title: Azure SQL Database import/export-tjänsten tar lång tid att importera eller exportera en databas | Microsoft Docs
description: Azure SQL Database import/export-tjänsten tar lång tid att importera eller exportera en databas
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: ed5f3d1cd505270eb91c9cfbd6fb5c38b908f33d
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2019
ms.locfileid: "71974466"
---
# <a name="azure-sql-database-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Azure SQL Database import/export-tjänsten tar lång tid att importera eller exportera en databas

När du använder Azure SQL Database import/export-tjänsten kanske du märker att det ibland tar lång tid att slutföra processen. Den här artikeln innehåller ytterligare information om möjliga orsaker till Thee fördröjningar och alternativa metoder som du kan använda för att lösa dessa problem.

## <a name="azure-sql-database-importexport-service"></a>Azure SQL Database import/export-tjänsten

Tjänsten Azure SQL Database import/export är en REST-baserad webb tjänst som körs i alla Microsoft Azure Data Center. Det här är den tjänst som anropas när du använder antingen [import databasen](https://docs.microsoft.com/azure/sql-database/sql-database-import#import-from-a-bacpac-file-in-the-azure-portal) eller [export](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) alternativen för att flytta din SQL-databas i Microsoft Azure-portalen. Tjänsten tillhandahåller en kostnads fri tjänst för begär ande köer och en kostnads fri beräknings tjänst för att utföra import och export från en Microsoft Azure SQL-databas till Microsoft Azure binary large object-lagring (BLOB).

Import-och export åtgärderna är inte en traditionell fysisk databas säkerhets kopia, men en logisk säkerhets kopia av databasen som använder ett särskilt BACPAC-format. Med det här logiska BACPAC-formatet kan du undvika att behöva använda ett fysiskt format som kan variera mellan versioner av SQL Server och SQL Database. Därför kan du använda det för att på ett säkert sätt återställa databasen till en SQL-databas och till en SQL Server databas.

## <a name="what-causes-the-process-to-take-a-long-time"></a>Vad gör processen att ta lång tid

Tjänsten Azure SQL Database import/export innehåller ett begränsat antal virtuella datorer per region för att bearbeta import-och export åtgärderna. Den virtuella Compute-datorn är värd för varje region för att se till att import eller export förhindrar fördröjningar och kostnader för bandbredd i flera regioner. Så om för många begär Anden görs samtidigt i samma region uppstår betydande fördröjningar vid bearbetning av åtgärderna. Tiden som krävs för att slutföra begär Anden kan variera från några sekunder till flera timmar.

> [!NOTE]
> Om en begäran inte bearbetas inom fyra dagar avbryts begäran automatiskt av tjänsten.

## <a name="recommended-solutions"></a>Rekommenderade lösningar

Om databas exporten bara används för återställning från oavsiktlig data borttagning, ger alla Azure SQL Server-databas-versioner självbetjänings återställning från systemgenererade säkerhets kopior. Men om du behöver dessa exporter av andra orsaker, och om du behöver snabbare eller mer förutsägbara import-eller export prestanda, bör du överväga följande alternativ:

* [Exportera till en BACPAC-fil med hjälp av SQLPackage-verktyget](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility)
* [Exportera till en BACPAC-fil med hjälp av SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms)
* Kör BACPAC import eller export direkt i din kod med hjälp av Microsoft® SQL Server® DacFx-API (data-Tier Application Framework). För ytterligare informations granskning
  * [Exportera ett data skikts program](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Microsoft. SqlServer. DAC-namnrymd](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [Ladda ned DACFx](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="considerations-when-exporting-or-importing-an-azure-sql-database"></a>Att tänka på när du exporterar eller importerar en Azure SQL-databas

* Alla metoder som beskrivs i den här artikeln använder sig av en DTU-kvot, vilket ger en begränsning av Azure SQLDB-tjänsten. Du kan [Visa DTU-statistik för databasen på Azure Portal](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#monitor-database-performance). Om databasen når resurs gränser [uppgraderar du tjänst nivån](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources) för att lägga till fler resurser.
* Klient program (t. ex. sqlpackage-verktyg eller anpassade DAC-program) bör helst köras från en virtuell dator (VM) i samma region som SQL-databasen eller annars kan du köra prestanda problem på grund av nätverks fördröjning.
* Att exportera stora tabeller utan grupperade index kan vara mycket långsamt eller till och med orsaka fel. Detta beror på att tabellen inte kan delas upp och exporteras parallellt och måste exporteras i en enda transaktion och som orsakar långsamma och potentiella fel under exporten, särskilt för stora tabeller. 


## <a name="related-documents"></a>Relaterade dokument

[Att tänka på när du exporterar en Azure SQL-databas](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
