---
title: Tjänsten Importera/exportera tar lång tid
description: Det tar lång tid att importera eller exportera en databas i Azure SQL Database
ms.custom: seo-lt-2019
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
manager: dcscontentpm
ms.openlocfilehash: cf2d9b218fe63414af2446b8562d3ba187b2d395
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535773"
---
# <a name="azure-sql-database-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Det tar lång tid att importera eller exportera en databas i Azure SQL Database

När du använder azure SQL Database Import/Export-tjänsten kan processen ta längre tid än förväntat. I den här artikeln beskrivs de potentiella orsakerna till den här fördröjningen och alternativa lösningsmetoder.

## <a name="azure-sql-database-importexport-service"></a>Tjänsten Import/export av Azure SQL-databas

Azure SQL Database Import/Export-tjänsten är en REST-baserad webbtjänst som körs i alla Azure-datacenter. Den här tjänsten anropas när du använder alternativet [Importera databas](sql-database-import.md#using-azure-portal) eller [Exportera](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) för att flytta din SQL-databas i Azure-portalen. Tjänsten tillhandahåller kostnadsfria kö- och beräkningstjänster för begäran för att utföra import och export mellan en Azure SQL-databas och Azure Blob-lagring.

Import- och exportåtgärder representerar inte en traditionell fysisk databassäkerhetskopiering utan en logisk säkerhetskopia av databasen som använder ett speciellt BACPAC-format. Bacpac-formatet gör att du kan undvika att behöva använda ett fysiskt format som kan variera mellan versioner av Microsoft SQL Server och Azure SQL Database. Därför kan du använda den för att återställa databasen till en SQL Server-databas på ett säkert sätt och till en SQL-databas.

## <a name="what-causes-delays-in-the-process"></a>Vad orsakar förseningar i processen?

Azure SQL Database Import/Export-tjänsten tillhandahåller ett begränsat antal virtuella beräkningsdatorer (VIRTUELLA datorer) per region för att bearbeta import- och exportåtgärder. De virtuella beräknings-datorerna är värd per region för att säkerställa att importen eller exporten undviker bandbreddsfördröjningar och avgifter mellan regioner. Om för många begäranden görs samtidigt i samma region kan betydande förseningar uppstå vid bearbetning av åtgärderna. Den tid som krävs för att slutföra begäranden kan variera från några sekunder till många timmar.

> [!NOTE]
> Om en begäran inte behandlas inom fyra dagar avbryts tjänsten automatiskt begäran.

## <a name="recommended-solutions"></a>Rekommenderade lösningar

Om databasexporten endast används för återställning från oavsiktlig dataradering, tillhandahåller alla Azure SQL Database-versioner återställningsfunktioner för självbetjäning från systemgenererade säkerhetskopior. Men om du behöver dessa exporter av andra skäl, och om du behöver konsekvent snabbare eller mer förutsägbara import / export prestanda, överväga följande alternativ:

* [Exportera till en BACPAC-fil med hjälp av SQLPackage-verktyget](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility).
* [Exportera till en BACPAC-fil med hjälp av SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms).
* Kör BACPAC-importen eller exportera direkt i koden med hjälp av DacFx-API:et (Microsoft SQL Server Data-Tier Application Framework). Mer information finns i:
  * [Exportera ett datanivåprogram](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Namnområde för Microsoft.SqlServer.Dac](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [Ladda ner DACFx](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="things-to-consider-when-you-export-or-import-an-azure-sql-database"></a>Saker att tänka på när du exporterar eller importerar en Azure SQL-databas

* Alla metoder som beskrivs i den här artikeln använder upp DTU-kvoten (Database Transaction Unit), vilket orsakar begränsning av Azure SQL Database-tjänsten. Du kan [visa DTU-statistiken för databasen på Azure-portalen](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#sql-database-resource-monitoring). Om databasen har nått sina resursgränser [uppgraderar du tjänstnivån](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources) för att lägga till fler resurser.
* Helst bör du köra klientprogram (som sqlpackage-verktyget eller ditt anpassade DAC-program) från en virtuell dator i samma region som SQL-databasen. Annars kan prestandaproblem relaterade till nätverksfördröjning.
* Exportera stora tabeller utan klustrade index kan vara mycket långsam eller till och med orsaka fel. Detta beror på att tabellen inte kan delas upp och exporteras parallellt. I stället måste den exporteras i en enda transaktion och det orsakar långsamma prestanda och potentiella fel under export, särskilt för stora tabeller.


## <a name="related-documents"></a>Relaterade dokument

[Överväganden vid export av en Azure SQL-databas](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
