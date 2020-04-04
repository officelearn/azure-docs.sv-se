---
title: Installera Visual Studio 2019
description: Installera Visual Studio och SQL Server Development Tools (SSDT) för Synapse SQL
services: synapse-analytics
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b8e37a15f473e5777fe5412b785a0916bd78f095
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655902"
---
# <a name="getting-started-with-visual-studio-2019"></a>Komma igång med Visual Studio 2019

Visual Studio **2019** SQL Server Data Tools (SSDT) är ett enda verktyg som gör att du kan göra följande:

- Ansluta, fråga och utveckla program
- Utnyttja en objektutforskare för att visuellt utforska alla objekt i din datamodell, inklusive tabeller, vyer, lagrade procedurer och etc.
- Generera DDL-skript (T-SQL Data Definition Language) för dina objekt
- Utveckla ditt informationslager med hjälp av en tillståndsbaserad metod med SSDT-databasprojekt
- Integrera databasprojektet med källkontrollsystem som Git med Azure Repos
- Konfigurera pipelines för kontinuerlig integrering och distribution med automatiseringsservrar som Azure DevOps

## <a name="install-visual-studio-2019"></a>Installera Visual Studio 2019

Se [Ladda ner Visual Studio 2019][] för att ladda ner och installera Visual Studio **16.3 och senare**. Under installationen väljer du datalagring och bearbetningsarbetsbelastning. Fristående SSDT-installation krävs inte längre i Visual Studio 2019.

## <a name="unsupported-features-in-ssdt"></a>Funktioner som inte stöds i SSDT

Det finns tillfällen då funktionsutgåvor för Synapse SQL kanske inte innehåller stöd för SSDT. Följande funktioner stöds för närvarande inte:

- [Materialiserade vyer](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Ordnade grupperade Columnstore-index](/sql/t-sql/statements/create-columnstore-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#examples--and-)
- [KOPIERA utdrag](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Arbetsbelastningshantering](sql-data-warehouse-workload-management.md) - arbetsbelastningsgrupper och klassificerare
- [Säkerhet på radnivå](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
  - Skicka in en supportbiljett eller rösta [här](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) för att få funktionen stöds.
- [Dynamisk datamaskning](/sql/relational-databases/security/dynamic-data-masking?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#defining-a-dynamic-data-mask)
  - Skicka in en supportbiljett eller rösta [här](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) för att få funktionen stöds.
- [Tabeller med begränsningar](sql-data-warehouse-table-constraints.md#table-constraints) stöds inte. För dessa tabellobjekt anger du byggåtgärden till "Ingen".

## <a name="next-steps"></a>Nästa steg

Nu när du har den senaste versionen av SSDT är du redo att [ansluta](sql-data-warehouse-query-visual-studio.md) till din SQL-pool.

<!--Other-->

[Ladda ner Visual Studio 2019]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
