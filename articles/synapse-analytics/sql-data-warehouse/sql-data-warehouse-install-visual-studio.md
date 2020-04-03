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
ms.openlocfilehash: aa2f903611f52f8f7a8ede0040b592a7dddd0e89
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584447"
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

- [Materialiserade vyer](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) (pågår)
- [Ordnade grupperade Columnstore-index](https://docs.microsoft.com/sql/t-sql/statements/create-columnstore-index-transact-sql?view=azure-sqldw-latest#examples--and-) (pågår)
- [COPY-sats](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (pågår)
- [Arbetsbelastningshantering](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-management) - arbetsbelastningsgrupper och klassificerare (pågår)
- [Säkerhet på radnivå](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?view=sql-server-ver15)
  - Skicka in en supportbiljett eller rösta [här](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) för att få funktionen stöds.
- [Dynamisk datamaskning](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc.json&view=sql-server-2017#defining-a-dynamic-data-mask)
  - Skicka in en supportbiljett eller rösta [här](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) för att få funktionen stöds. 
- [FUNKTIONEN PREDICT](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=sql-server-ver15&viewFallbackFrom=azure-sqldw-latest) 
- [Tabeller med begränsningar](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-table-constraints#table-constraints) stöds inte. För dessa tabellobjekt anger du byggåtgärden till "Ingen".

## <a name="next-steps"></a>Nästa steg

Nu när du har den senaste versionen av SSDT är du redo att [ansluta](sql-data-warehouse-query-visual-studio.md) till din SQL-pool.




<!--Other-->

[Ladda ner Visual Studio 2019]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
