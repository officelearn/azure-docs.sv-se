---
title: Installera Visual Studio 2019
description: Installera Visual Studio och SQL Server Development Tools (SSDT) för SQL Analytics
services: sql-data-warehouse
ms.custom: azure-synapse
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b9ad0f4aedc5a095bfa70dec1b67e52cd4d152e0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195347"
---
# <a name="getting-started-with-visual-studio-2019"></a>Komma igång med Visual Studio 2019
Visual Studio **2019** SQL Server Data Tools (SSDT) är ett enda verktyg som gör att du kan göra följande:

- Ansluta, fråga och utveckla program
- Använd en objekt Utforskare för att visuellt utforska alla objekt i din data modell, inklusive tabeller, vyer, lagrade procedurer och så vidare.
- Generera T-SQL Data Definition Language-skript (DDL) för dina objekt
- Utveckla ditt informations lager med hjälp av en tillstånds-baserad metod med SSDT databas projekt
- Integrera ditt databas projekt med käll kontroll system som git med Azure databaser
- Konfigurera kontinuerliga integrerings-och distributions pipeliner med Automation-servrar som Azure DevOps

## <a name="install-visual-studio-2019"></a>Installera Visual Studio 2019
Se [Hämta Visual Studio 2019][] för att ladda ned och installera visual Studio **16,3 och senare**. Under installationen väljer du arbets belastningen lagring och bearbetning av data. Installation av fristående SSDT krävs inte längre i Visual Studio 2019.

## <a name="unsupported-features-in-ssdt"></a>Funktioner som inte stöds i SSDT

Det finns tillfällen när funktions versioner för SQL Analytics inte kan inkludera stöd för SSDT. Följande funktioner stöds för närvarande inte:

- [Materialiserade vyer](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) (pågår)
- [Beställda grupperade columnstore-index](https://docs.microsoft.com/sql/t-sql/statements/create-columnstore-index-transact-sql?view=azure-sqldw-latest#examples--and-) (pågår)
- [Kopierings instruktion](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (pågår)
- [Arbets](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-management) belastnings hantering-arbets belastnings grupper och klassificerare (pågår)
- [Säkerhet på radnivå](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?view=sql-server-ver15)
  - Skicka in ett support ärende eller rösta [här](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) för att få den funktion som stöds.
- [Dynamisk datamaskning](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc.json&view=sql-server-2017#defining-a-dynamic-data-mask)
  - Skicka in ett support ärende eller rösta [här](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) för att få den funktion som stöds. 
- [Förutsägelse](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=sql-server-ver15&viewFallbackFrom=azure-sqldw-latest) funktion 
- [Tabeller med begränsningar](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-table-constraints#table-constraints) stöds inte. För dessa tabell objekt ställer du in åtgärden Skapa på "ingen".

## <a name="next-steps"></a>Nästa steg

Nu när du har den senaste versionen av SSDT är du redo att [ansluta][connect] till din SQL-pool.

<!--Anchors-->

<!--Image references-->

<!--Articles-->

[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->

[Hämta Visual Studio 2019]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
